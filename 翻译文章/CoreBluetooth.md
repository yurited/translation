作为 iOS 开发，我们十分清楚人们都喜欢互通性。很明显，我们喜欢通过无线设备与其他人进行沟通。最近，我们开始希望能够与那些曾经被认为是独立的普通设备进行通信。我们开始喜欢，甚至是期望，部分无线设备可以收集并且分析我们自己的数据 (通常称为"可穿戴设备")。太多的设备已经成为我们生活里的一部分，并且专门创建了一个通用的短语，"Internet of Things" 或者 "IoT" (物联网)。现在地球上有数十亿的无线通讯设备。在这篇教程中，我们将聚焦 IoT 其中的一部分：蓝牙。  

我将说明蓝牙技术背后的基本概念，以及：  

 1. 向你展示如何精通蓝牙方向的软件开发从而为你提供巨大的职业机遇  
 2. 提醒你，你在发布一个使用蓝牙技术的应用时必须确定是否需要通过"资格审查"  
 3. 给你提供苹果 [Core Bluetooth](https://developer.apple.com/documentation/corebluetooth) 框架的概述 ([请参阅](https://developer.apple.com/library/archive/documentation/NetworkingInternetWeb/Conceptual/CoreBluetooth_concepts/AboutCoreBluetooth/Introduction.html#//apple_ref/doc/uid/TP40013257-CH1-SW1))  
 4. 最后，带领你使用 Swift 4 并通过 `Core Bluetooth` 和一个蓝牙设备来开发一款 iOS 应用程序用于监控心率

```
提示：注意跟随阅读文章中包含的超链接。对于开发者这是重要的资料，它确保你完全理解蓝牙的工作方式以及苹果公司针对蓝牙如何提供支持。
```

## 蓝牙 - 一项迅速发展的技术
在一篇文章中不可能说清楚如何为整个物联网开发软件，但是运行所有这些无线设备的数据是有启发性的 -- 实际上是很不可思议的。连接着的东西无处不在并且可以预测这个小东西的增长速度将是惊人的。如果你观察一下我们今天讨论的内容，在"短程段"中，使用如蓝牙和无线网的技术，然后添加上使用如电话的技术 (比如: CDMA) 的"广域类别"，你将看到 2014 年的 125亿设备将迅速增加到 2022 年预计的 300亿 [(参考)](https://www.ericsson.com/en/mobility-report/internet-of-things-forecast)。  

蓝牙是一种短距离无线通讯技术的标准化规范。[Bluetooth SIG (蓝牙技术联盟)](https://www.bluetooth.com/zh-cn) 管理和保护这种短程无线技术背后的研发、发展还有知识产权。SIG 确保关于蓝牙的制造商，开发者和销售者他们的硬件和软件都是基于标准化规范。  

根据 Bluetooth SIG，["今年将近40亿台设备使用蓝牙进行连接。连接手机，平板电脑，个人电脑，或者彼此。"](https://www.bluetooth.com/bluetooth-technology) Ellisys，一家对短程通讯技术进行深度投资的公司，对此表示认同，并 ["预估 2018 年将有近 40 亿台新的蓝牙设备上市"](https://globenewswire.com/news-release/2018/02/22/1379920/0/en/Ellisys-Increases-Support-for-Bluetooth-Mesh-Networking-on-Protocol-Solutions.html)。请记住，那是 40亿 新设备的上市并且仅是今年。  

根据这个趋势，Statista，一家收集 "市场和消费数据" 的公司，主张全球的蓝牙设备[将从 2012 年的 35 亿增长到 2018 年预估的 100 亿。](https://www.statista.com/statistics/283638/installed-base-forecast-bluetooth-enabled-devices-2012-2018/)  



























