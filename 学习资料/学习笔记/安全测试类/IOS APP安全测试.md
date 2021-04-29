## IOS APP安全测试

###### ipa安装包简介

IPA是Apple程序应用文件iPhoneApplication的缩写; ipa文件实质是一个zip压缩包(不是rar或7z包)，包含3个组件: payload目录个的.app目录，这个是软件的
主程序; iTunesArtwork，实质是一个无后缀名的png 图片，用来在iTunes中显示图标；iTunesMetadata.plist，记录购买者信息、售价等数据。

ipa安装包获取途径

- 从app store中下载，利用爱思助手等连接工具导出ipa安装包
- 客户提供ipa安装包
- 直接访问链接下载ipa安装包
- 扫描二维码下载ipa安装包
- 需要在网页源代码中的js文件中找到ipa安装包链接下载
- 注意：不是从客户手中得到的安装包，要和客户确认获取的安装包是否是客户要求测试的相应版本的app

IOS应用运行原理

应用在磁盘中是加密状态，由于CPU运行不会识别加密文件，因此在启动应用前需要在内核中对加密应用文件进行解密，提取可执行文件才可放入内存中运行

APPstore上下载下来的app都默认被苹果加了一层壳，也就是经过加密的，有壳的保护，无法使用hopper等反编译软件反编译应用，也不乏使用工具导出头文件进行深入研究，需要对其进行砸壳

IOS APP测试工具

- Clutch工具（砸壳）
- dumpdecrypted.dylib工具（砸壳）
- class-dump工具（导出app的头文件）
- Cycript工具（动态调试运行程序）

1. 使用Clutch可以对app进行砸壳，得到解密后的app
2. 使用dumpdecrypted.dylib工具对app进行砸壳，砸壳成功后，可以得到破以后的.decrypted文件
3. 使用class-dump工具可以通过砸壳后得的.decrypted破译文件，导出app的头文件
4. 使用cycript工具对运行的app进行动态调试，可以修改运行中的程序等