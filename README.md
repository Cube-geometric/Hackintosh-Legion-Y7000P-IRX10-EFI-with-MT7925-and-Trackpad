该EFI是由1401自动生成后，在使用的过程中不断打补丁完善而来，适用于macOS 26.7.1以及macOS 26.7.2

请注意，该EFI是抹掉了三码的，使用时请使用工具重新生成

本EFI相较于其它版本，主要亮点是做到了对MT7925网卡的WiFi和蓝牙驱动，以及触控板支持多指的驱动，同时在nullmoth 3.0驱动下实现了睡眠和唤醒

接下来，按驱动的加载顺序说明本EFI的局限所在：

1. 内含通过ALC来驱动声卡的方式，在nullmoth的Nvidia驱动下，会和NMXAudio.kext发生冲突导致被忽略
2. 不加CpuTsc时触发过Non-monotonic time panic，使用TCS\_sync\_margin=0参数修复，而非-cputsclock
3. UTBMap是适配macOS 26的格式，故在macOS 15上应该无法正常使用，同时因为手头没有USB 3.0的Type-C接口设备，没有做它们的映射
4. 选用bafcz大佬基于原版VoodooGPIO魔改后的源码直接编译,同时参考他的实现思路修改了VoodooI2C的plist。不过我也想在这里分享一下大佬的解决思路，以供其它不支持的机型进行参考：为了使VoodooI2C可以驱动触控板，在IOPCIMatch下加入了0x7a7c8086\&0xFFFCFFFF以让VoodooI2C可以识别该I2C控制器，同时在VoodooGPIO中增补了12-14代的GPIO数据
5. WiFi和蓝牙选用了watchthelight-labs大佬编写的驱动，可以在macOS 26.7.2中直接原生地控制，体验非常不错，不过目前还不支持隔空投送和随航（
6. CpuFriend是按照MacBookPro16,2的机型配置的，有其它伪装机型需求的需要自行修改，重新配置dataProvider
7. 睡眠有可能出现莫名唤醒和难以唤醒，唤醒后系统加速追赶时间的情况，目前不知是何原因

部分问题解决时的参考网址：

https://bbs.pcbeta.com/viewthread-2082611-1-1.html?show\_loading=0\&webview\_progress\_bar=1\&push\_animated=1\&theme=light

https://github.com/bafcz/VoodooGPIO

https://github.com/watchthelight-labs/WTLWireless

显卡驱动（nullmoth nvidia-macos-driver）的网址：
https://github.com/nullmoth/nvidia-macos-driver

注意：编译VoodooGPIO需要用到MacKernelSDK，否则会找不到IOKit和mach文件夹里面的头文件，在这里也附上SDK的仓库地址：
https://github.com/acidanthera/MacKernelSDK
