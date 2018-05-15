---
title: 使用 Azure IoT SDK 针对移动设备进行开发 | Microsoft Docs
description: 开发人员指南 - 了解如何使用 Azure IoT 中心 SDK 针对移动设备进行开发。
services: iot-hub
documentationcenter: ''
author: yzhong94
manager: timlt
editor: ''
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/16/2018
ms.author: yizhon
ms.openlocfilehash: 824d577a4709a388bd858bdda6ef5f11f53dde58
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2018
---
# <a name="develop-for-mobile-devices-using-azure-iot-sdks"></a>使用 Azure IoT SDK 针对移动设备进行开发

物联网 (Internet of Things) 中的物 (Things) 指的是具有不同功能的各种设备：传感器、微控制器、智能设备、工业网关甚至移动设备。  移动设备可以是 IoT 设备，它发送设备到云遥测数据并且由云管理。  它还可以是运行后端服务应用程序的设备，它管理其他 IoT 设备。  在这两种情况下，都可以使用 [Azure IoT 中心 SDK][lnk-sdk-overview] 来开发适用于移动设备的应用程序。  

## <a name="develop-for-native-ios-platform"></a>针对原生 iOS 平台进行开发

Azure IoT 中心 SDK 通过 Azure IoT 中心 C SDK 提供了原生 iOS 平台支持。  可以将其视为可以在 Swift 或 Objective C XCode 项目中包含的 iOS SDK。  可通过两种方式在 iOS 上使用 C SDK：
- 直接在 XCode 项目中使用 CocoaPod 库。  
- 下载 C SDK 的源代码并按照适用于 MacOS 的[生成说明][lnk-c-devbox]针对 iOS 平台进行生成。  

Azure IoT 中心 C SDK 是以 C99 编写的，针对各种平台提供了最大的可移植性。  移植过程涉及为平台特定组件编写一个精简采用层，对于 [iOS][lnk-ios-pal]，可以在此处找到该采用层。  可以在 iOS 平台上利用 C SDK 中的功能，包括 Azure IoT 中心基元支持的功能和 SDK 特定功能，例如网络可靠性的重试策略。  iOS SDK 的接口也类似于 Azure IoT 中心 C SDK 的接口。  

这些文档演练了如何在 iOS 设备上开发设备应用程序或服务应用程序：
- [快速入门：将遥测数据从设备发送到 IoT 中心][lnk-device-ios-quickstart]  
- [使用 IoT 中心将消息从云发送到设备][lnk-service-ios-quickstart]  

### <a name="develop-with-azure-iot-hub-cocoapod-libraries"></a>使用 Azure IoT 中心 CocoaPod 库进行开发

Azure IoT 中心 SDK 发布了一组用于 iOS 开发的 Objective-C CocoaPod 库。  若要查看 CocoaPod 库的最新列表，请参阅[用于 Microsoft Azure IoT 的 CocoaPod][lnk-cocoapod-list]。  将相关库包含到 XCode 项目中之后，可以采用两种方式来编写 IoT 中心相关代码：
- Objective 函数：如果项目是以 Objective-C 编写的，则可以直接从 Azure IoT 中心 C SDK 调用 API。  如果项目是以 Swift 编写的，则可以在创建函数之前调用 ``@objc func``，然后继续使用 C 或 Objective-C 代码编写与 Azure IoT 中心相关的所有逻辑。  可以在[示例存储库][lnk-ios-samples-repo]中找到演示了上述两种方式的一组示例。  
- 包含 C 示例：如果已编写了一个 C 设备应用程序，则可以直接在 XCode 项目中引用该应用程序：
    - 通过 XCode 将 sample.c 文件添加到 XCode 项目中。  
    - 将头文件添加到依赖项。  [示例存储库][lnk-ios-samples-repo]中提供了一个头文件示例。  有关详细信息，请访问 Apple 提供的关于 [Objective-C](https://developer.apple.com/documentation/objectivec) 的文档页。

## <a name="next-steps"></a>后续步骤
* [IoT 中心 REST API 参考][lnk-rest-ref]
* [Azure IoT C SDK 源代码][lnk-c-sdk]

[lnk-sdk-overview]: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks
[lnk-c-devbox]: https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md
[lnk-device-ios-quickstart]:https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-ios
[lnk-service-ios-quickstart]: https://docs.microsoft.com/azure/iot-hub/iot-hub-ios-swift-c2d
[lnk-cocoapod-list]: https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/ios/CocoaPods.md
[lnk-ios-samples-repo]: https://github.com/Azure-Samples/azure-iot-samples-ios
[lnk-ios-pal]: https://github.com/Azure/azure-c-shared-utility/tree/master/pal/ios-osx
[lnk-c-sdk]: https://github.com/Azure/azure-iot-sdk-c
[lnk-rest-ref]: https://docs.microsoft.com/rest/api/iothub/
