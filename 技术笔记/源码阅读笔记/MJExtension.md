开发过程中遇到如何通过*runtime* 获取到成员属性类型，在翻阅源码时找到了李明杰老师的处理方法，在这里记录下来：

#### 获取成员属性类型

##### MJProperty 

作者通过面向对象的思想，封装到了两个自定义类中：`MJProperty` 和 `MJPropertyType`  ，`MJPropertyType`  中的 **typeClass** 描述的就是成员属性类型， 其中也涉及到 `runtime ` 的应用 ：

```objective-c
MJProperty.h

/** 成员属性的类型 */
@property (nonatomic, readonly) MJPropertyType *type;

/** 成员属性 */
@property (nonatomic, assign) objc_property_t property;

/**
 *  初始化
 */
+ (instancetype)cachedPropertyWithProperty:(objc_property_t)property;

...
```



MJProperty 通过类方法`+cachedPropertyWithProperty:` 创建实例，初始化属性`property` ，`type`  :

```objective-c
MJProperty.m

- (void)setProperty:(objc_property_t)property
{
    _property = property;
    
    MJExtensionAssertParamNotNil(property);
    
    // 1.属性名
    _name = @(property_getName(property));
    
    // 2.成员类型
    NSString *attrs = @(property_getAttributes(property));
    NSUInteger dotLoc = [attrs rangeOfString:@","].location;
    NSString *code = nil;
    NSUInteger loc = 1;
    // 通过硬编码获取到最初的code值
    if (dotLoc == NSNotFound) { // 没有,
        code = [attrs substringFromIndex:loc];
    } else {
        code = [attrs substringWithRange:NSMakeRange(loc, dotLoc - loc)];
    }
    _type = [MJPropertyType cachedTypeWithCode:code];
}
```



`runtime`  函数 `property_getAttributes()` 返回属性字符串，大概像这样：

```objective-c
@interface SHTestModel : NSObject
@property (nonatomic, copy) NSArray *array;
@property (nonatomic, strong) NSMutableArray *changeArray;
@property (nonatomic, copy) NSString *name;
@property (nonatomic, assign) int age;
@property (nonatomic, assign) BOOL gender;
@property (nonatomic, strong) SHTestModel *testModel;
@end
```



比如这个类 `SHTestModel` , 获取到的 字符串是这样：

```objective-c
Dush:) attrs: T@"NSArray",C,N,V_array
Dush:) attrs: T@"NSMutableArray",&,N,V_changeArray
Dush:) attrs: T@"NSString",C,N,V_name
Dush:) attrs: Ti,N,V_age
Dush:) attrs: TB,N,V_gender
Dush:) attrs: T@"SHTestModel",&,N,V_testModel
```

关于属性字符串的含义，可以阅读 [Declared Properties](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtPropertyIntrospection.html#//apple_ref/doc/uid/TP40008048-CH101)。



##### MJPropertyType

其中可以看到，通过传入硬编码得到的 *code* ，构造一个 `MJPropertype` 实例，其中有一个关键属性 `typeClass`  :

```
MJPropertyType.h

/**
 *  包装一种类型
 */
@interface MJPropertyType : NSObject
/** 类型标识符 */
@property (nonatomic, copy) NSString *code;

/** 是否为id类型 */
@property (nonatomic, readonly, getter=isIdType) BOOL idType;

/** 是否为基本数字类型：int、float等 */
@property (nonatomic, readonly, getter=isNumberType) BOOL numberType;

/** 是否为BOOL类型 */
@property (nonatomic, readonly, getter=isBoolType) BOOL boolType;

/** 对象类型（如果是基本数据类型，此值为nil） */
@property (nonatomic, readonly) Class typeClass;

/** 类型是否来自于Foundation框架，比如NSString、NSArray */
@property (nonatomic, readonly, getter = isFromFoundation) BOOL fromFoundation;
/** 类型是否不支持KVC */
@property (nonatomic, readonly, getter = isKVCDisabled) BOOL KVCDisabled;

/**
 *  获得缓存的类型对象
 */
+ (instancetype)cachedTypeWithCode:(NSString *)code;

...
```

看一下 `typeClass` 是如何赋值的：

```objective-c
- (void)setCode:(NSString *)code
{
    _code = code;
    
    MJExtensionAssertParamNotNil(code);
    
    if ([code isEqualToString:MJPropertyTypeId]) {
        _idType = YES;
    } else if (code.length == 0) {
        _KVCDisabled = YES;
    } else if (code.length > 3 && [code hasPrefix:@"@\""]) {
        // 去掉@"和"，截取中间的类型名称
        _code = [code substringWithRange:NSMakeRange(2, code.length - 3)];
        _typeClass = NSClassFromString(_code);
        _fromFoundation = [MJFoundation isClassFromFoundation:_typeClass];
        _numberType = [_typeClass isSubclassOfClass:[NSNumber class]];
        
    } else if ([code isEqualToString:MJPropertyTypeSEL] ||
               [code isEqualToString:MJPropertyTypeIvar] ||
               [code isEqualToString:MJPropertyTypeMethod]) {
        _KVCDisabled = YES;
    }
    
    // 是否为数字类型
    NSString *lowerCode = _code.lowercaseString;
    NSArray *numberTypes = @[MJPropertyTypeInt, MJPropertyTypeShort, MJPropertyTypeBOOL1, MJPropertyTypeBOOL2, MJPropertyTypeFloat, MJPropertyTypeDouble, MJPropertyTypeLong, MJPropertyTypeLongLong, MJPropertyTypeChar];
    if ([numberTypes containsObject:lowerCode]) {
        _numberType = YES;
        
        if ([lowerCode isEqualToString:MJPropertyTypeBOOL1]
            || [lowerCode isEqualToString:MJPropertyTypeBOOL2]) {
            _boolType = YES;
        }
    }
}
```

作者将逻辑都放在 *setter* 方法里面处理，搬砖时可以借鉴。



明杰老师加上了中文注释，理解起来更加容易，对于对象类型的处理是这样：

也是通过硬编码截取到`code` 里面的类型子串，再通过 `NSClassFromString()` 转换成 类对象。

到这里，就获取到了成员的属性类型。



其中不光处理了类对象，也处理了基本数据类型，id 类型等，考虑的很全面。





#### 总结

多看看优秀的开源项目，了解一下大佬的思维方式，有时候还挺有趣。



#### 参考

1、[Declared Properties](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtPropertyIntrospection.html#//apple_ref/doc/uid/TP40008048-CH101)