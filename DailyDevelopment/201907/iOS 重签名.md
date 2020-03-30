最近上线版本，遇到了几个关于ipa包重签名的问题，遇以记之

## ipa文件修改版本号

步骤：

1、通过终端加压缩ipa文件后，修改项目plist文件中对应的版本号值，然后重新压缩。

2、通过重签名工具进行ipa重签（[签名工具下载地址](https://github.com/maciekish/iReSign)）。

## 重签名需要的文件entitlement.plist

[官方文档](https://developer.apple.com/library/archive/technotes/tn2415/_index.html)

[xcode问题定位与解决指南](https://www.cnblogs.com/abinzhang/p/5662061.html)

- Entitlement常见字段介绍

- - **beta-reports-active**  是否可以通过TestFilight发布
  - **team-identifier** 开发账号的团队id，由10个数字和字母字符组成。这个值也是默认的App ID前缀。
  - **get-task-allow** Xcode是否可以对该应用进行调试。
  - **application-identifier** 应用的App ID，格式是 <prefix>.<bundle_id>
  - **keychain-access-groups** 钥匙串权限组 （更多可以参考 https://developer.apple.com/library/ios/documentation/Security/Reference/keychainservices/）
  - **push-notification** 开启推送服务后，会有这个字段，它的值表示是生产环境（production）还是开发环境(development)。