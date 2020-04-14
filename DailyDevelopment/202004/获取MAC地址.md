### 参考链接

[iphone ipad 获取wifi信息 BSSID SSID](<https://blog.csdn.net/feiyue0823/article/details/11140415>)

### 问题

通过  **CNCopyCurrentNetworkInfo**  获取mac地址时，会出现缺0的情况：

```objective-c
+ (NSString *)getMACAddress {
    NSArray *ifs = CFBridgingRelease(CNCopySupportedInterfaces());
    id info = nil;
    for (NSString *ifname in ifs) {
        info = (__bridge_transfer id)CNCopyCurrentNetworkInfo((CFStringRef) ifname);
        if (info && [info count]) {
            break;
        }
    }
    NSDictionary *dic = (NSDictionary *)info;
    NSString *bssid = [dic objectForKey:@"BSSID"];
    if (bssid.length == 0) {
        bssid = @"";
    }
    return bssid;
}
```

所以需要进行补零操作：

```objective-c

- (NSString *)standardFormateMAC:(NSString *)MAC {
    NSArray * subStr = [MAC componentsSeparatedByCharactersInSet:[NSCharacterSet characterSetWithCharactersInString:@":-"]];
    NSMutableArray * subStr_M = [[NSMutableArray alloc] initWithCapacity:0];
    for (NSString * str in subStr) {
        if (1 == str.length) {
            NSString * tmpStr = [NSString stringWithFormat:@"0%@", str];
            [subStr_M addObject:tmpStr];
        } else {
            [subStr_M addObject:str];
        }
    }
    
    NSString * formateMAC = [subStr_M componentsJoinedByString:@":"];
    [subStr_M release];
    return [formateMAC uppercaseString];
}

————————————————
版权声明：本文为CSDN博主「feiyue0823」的原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/feiyue0823/article/details/11140415
```

### 小尾巴

其实获取到的是当前WIFI下对应的Mac地址

如何才能获取到手机中 设置-关于本机-无线局域网地址 呢，笔者目前尚未找到对应的方法，应该是系统禁止获取了叭 :P