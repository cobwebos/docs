---
title: Azure IoT 设备 SDK 平台支持 | Microsoft Docs
description: 概念 - Azure IoT 设备 SDK 支持的平台的列表
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: robinsh
ms.openlocfilehash: ac5817675d3cfc97a8732ee2e10ec7b9246b12a5
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693338"
---
# <a name="azure-iot-device-sdks-platform-support"></a>Azure IoT 设备 Sdk 平台支持

Microsoft 努力不断扩展支持 Azure IoT 中心的设备。 Microsoft 在 GitHub 上发布开源设备 Sdk，有助于将设备连接到 Azure IoT 中心和设备预配服务。 设备 Sdk 可用于 C、.NET （C#）、Java、Node.js 和 Python。 Microsoft 测试每个 SDK，以确保它在[Microsoft sdk 和设备平台支持](#microsoft-sdks-and-device-platform-support)部分中详细介绍的受支持配置。

除了设备 Sdk 以外，Microsoft 还提供了其他几个途径来使客户和开发人员能够将其设备连接到 Azure IoT：

* Microsoft 与多家合作伙伴公司协作，帮助他们根据 Azure IoT C SDK 发布其硬件平台的开发工具包。

* Microsoft 与 Microsoft 受信任的合作伙伴合作，提供一套不断扩充的设备，这些设备已经过 Azure IoT 的测试和认证。 有关这些设备的最新列表，请参阅[Azure IoT 认证设备目录](https://catalog.azureiotsolutions.com/)。

* Microsoft 在 Azure IoT 中心设备 C SDK 中提供了平台抽象层（PAL），可帮助开发人员轻松地将 SDK 移植到其平台。 若要了解详细信息，请参阅[C SDK 移植指南](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)。

本主题提供有关 Microsoft Sdk 和它们支持的平台配置的信息，以及上面列出的每个其他选项。

## <a name="microsoft-sdks-and-device-platform-support"></a>Microsoft Sdk 和设备平台支持

Microsoft 在 GitHub 上发布了以下语言的开源 Sdk： C、.NET （C#）、Node.js、Java 和 Python。 此部分列出了 Sdk 及其依赖项。 任何满足这些依赖关系的设备平台都支持 Sdk。

对于列出的每个 Sdk，Microsoft：

* 在多个常用平台上持续构建并运行针对相关 SDK 的主分支的端到端测试。  为了提供跨不同编译器版本的测试覆盖率，我们通常针对最新的 LTS 版本和最受欢迎的版本进行测试。

* 提供安装指南或安装包（如果适用）。

* 完全支持 GitHub 上的 Sdk 与开源代码、客户发布路径，以及包含 GitHub 问题的产品团队合作。

### <a name="c-sdk"></a>C SDK

[Azure IoT 中心 C 设备 SDK](https://github.com/Azure/azure-iot-sdk-c)已通过进行测试，并支持以下配置。

| OS                  | TLS 库                  | 其他要求                                                                     |
|---------------------|------------------------------|---------------------------------------------------------------------------------------------|
| Linux               | OpenSSL、WolfSSL 或 BearSSL | Berkeley 套接字</br></br>可移植操作系统接口（POSIX）                       |
| iOS 12。2            | OpenSSL 或 Native OSX        | XCode 在 OSX 10.13.4 中模拟                                                               |
| Windows 10 系列   | SChannel                     |                                                                                             |
| Mbed 操作系统5。4         | Mbed TLS 2                   | [MXChip IoT 开发工具包](https://microsoft.github.io/azure-iot-developer-kit/)                  |
| Azure Sphere OS     | WolfSSL                      | [Azure Sphere MT3620](https://azure.microsoft.com/en-us/services/azure-sphere/get-started/) |

### <a name="python-sdk"></a>Python SDK

[Azure IoT 中心 Python 设备 SDK](https://github.com/Azure/azure-iot-sdk-python)已通过进行测试，并支持以下配置。

| OS                  | 编译器                          |
|---------------------|-----------------------------------|
| Linux               | Python 2.7 *、3.4*、3.5 *、3.6、3。7 |
| MacOS High Sierra   | Python 2.7 *、3.4*、3.5 *、3.6、3。7 |
| Windows 10 系列   | Python 2.7 *、3.4*、3.5 *、3.6、3。7 |

\* 仅 Python 版本3.5.3 或更高版本支持异步 Api，我们建议使用3.7 或更高版本。

### <a name="net-sdk"></a>.NET SDK

[Azure IoT 中心 .net （C#）设备 SDK](https://github.com/Azure/azure-iot-sdk-csharp)已通过进行测试，并支持以下配置。

| OS                                   | 标准版                                                   |
|--------------------------------------|------------------------------------------------------------|
| Linux                                | .NET Core 2.1                                              |
| Windows 10 桌面版和服务器 Sku   | .NET Core 2.1、.NET Framework 4.5.1 或 .NET Framework 4。7 |

.NET SDK 还可与 Windows IoT Core 一起用于[Azure 设备代理](https://github.com/ms-iot/azure-client-tools/blob/master/docs/device-agent/device-agent.md)或[自定义的 NTService，可使用 RPC 与 UWP 应用程序进行通信](https://docs.microsoft.com/samples/microsoft/windows-iotcore-samples/ntservice-rpc/)。

### <a name="nodejs-sdk"></a>Node.js SDK

[Azure IoT 中心 node.js 设备 SDK](https://github.com/Azure/azure-iot-sdk-node)已通过进行测试，并支持以下配置。

| OS                  | Node 版本    |
|---------------------|-----------------|
| Linux               | LTS 和当前 |
| Windows 10 系列   | LTS 和当前 |

### <a name="java-sdk"></a>Java SDK

[Azure IoT 中心 Java 设备 SDK](https://github.com/Azure/azure-iot-sdk-java)已通过进行测试，并支持以下配置。

| OS                     | Java 版本 |
|------------------------|--------------|
| Android API 28         | Java 8       |
| Linux x64             | Java 8       |
| Windows 10 家族 x64  | Java 8       |

## <a name="partner-supported-development-kits"></a>合作伙伴支持的开发工具包

Microsoft 与各种合作伙伴合作，为多个微处理器体系结构提供开发工具包。 这些合作伙伴已将 Azure IoT C SDK 移植到其平台。 合作伙伴创建和维护 SDK 的平台抽象层（PAL）。 Microsoft 与这些合作伙伴合作提供扩展支持。

| Partner             | 设备                            | 链接                     | 支持 |
|---------------------|------------------------------------|--------------------------|---------|
| Espressif           | ESP32 <br/> ESP8266                              | [Esp-azure](https://github.com/espressif/esp-azure)                | [GitHub](https://github.com/espressif/esp-azure)  
| Qualcomm            | Qualcomm MDM9206 LTE IoT 调制解调器     | [Qualcomm LTE for IoT SDK](https://developer.qualcomm.com/software/lte-iot-sdk)（用于 IoT SDK 的 Qualcomm LTE） | [论坛](https://developer.qualcomm.com/forums/software/lte-iot-sdk)   |
| ST Microelectronics | STM32L4 系列 <br/> STM32F4 系列 <br/>  STM32F7 系列 <br/>  用于 IoT 节点的 STM32L4 发现包    | [X-CUBE-AZURE](https://www.st.com/en/embedded-software/x-cube-azure.html) <br/>  <br/> [P-NUCLEO-AZURE](https://www.st.com/content/st_com/en/products/evaluation-tools/solution-evaluation-tools/communication-and-connectivity-solution-eval-boards/p-nucleo-azure1.html) <br/> [FP-CLD-AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32-ode-function-pack-sw/fp-cld-azure1.html)            | [支持](https://www.st.com/content/st_com/en/support/support-home.html)
| Texas Instruments   | CC3220SF 快速启动板 </br> CC3220S 快速启动板 </br> CC3235SF 快速启动板 </br> CC3235S 快速启动板 </br> MSP432E4 快速启动板 | [适用于 SimpleLink 的 Azure IoT 插件](https://github.com/TexasInstruments/azure-iot-pal-simplelink) | [TI E2E 论坛](https://e2e.ti.com) <br/> [CC3220 的 TI E2E 论坛](https://e2e.ti.com/support/wireless_connectivity/simplelink_wifi_cc31xx_cc32xx/) <br/> [MSP432E4 的 TI E2E 论坛](https://e2e.ti.com/support/microcontrollers/msp430/) |

## <a name="porting-the-microsoft-azure-iot-c-sdk"></a>移植 Microsoft Azure IoT C SDK

如果前面的部分中未涵盖你的设备平台，则可以考虑迁移 Azure IoT C SDK。 移植 C SDK 主要涉及实现 SDK 的平台抽象层（PAL）。 PAL 定义基元，这些基元提供您的设备和 SDK 中更高级别的函数之间的粘附。 有关详细信息，请参阅[迁移指南](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)。

## <a name="microsoft-partners-and-certified-azure-iot-devices"></a>Microsoft 合作伙伴和认证的 Azure IoT 设备

Microsoft 与许多合作伙伴合作，通过 Azure IoT 测试和认证设备持续扩展 Azure IoT universe。

* 若要浏览 Azure IoT 认证设备，请参阅[Microsoft Azure IoT 认证设备目录](https://catalog.azureiotsolutions.com/)"。

* 若要详细了解 Microsoft 受信任的合作伙伴，或了解如何成为 Microsoft 可信合作伙伴，请参阅[Microsoft Azure 认证物联网受信任的合作伙伴](https://azure.microsoft.com/en-us/marketplace/certified-iot-partners/)。

## <a name="connecting-to-iot-hub-without-an-sdk"></a>无需 SDK 即可连接到 IoT 中心

如果无法使用其中一个 IoT 中心设备 Sdk，则可以使用[Iot 中心 REST api](https://docs.microsoft.com/en-us/rest/api/iothub/)从能够发送和接收 HTTPS 请求和响应的任何应用程序直接连接到 iot 中心。

## <a name="support-and-other-resources"></a>支持和其他资源

如果在使用 Azure IoT 设备 Sdk 时遇到问题，可以通过多种方式寻求支持。 可以尝试以下通道之一：

**报告 bug** –可在相关 GitHub 项目的 "问题" 页上报告设备 sdk 中的 bug。 修复很快就会从项目实施到产品更新中。

* [Azure IoT 中心 C SDK 问题](https://github.com/Azure/azure-iot-sdk-c/issues)

* [Azure IoT 中心 .NET （C#） SDK 问题](https://github.com/Azure/azure-iot-sdk-csharp/issues)

* [Azure IoT 中心 Java SDK 问题](https://github.com/Azure/azure-iot-sdk-java/issues)

* [Azure IoT 中心 node.js SDK 问题](https://github.com/Azure/azure-iot-sdk-node/issues)

* [Azure IoT 中心 Python SDK 问题](https://github.com/Azure/azure-iot-sdk-python/issues)

**Microsoft 客户支持团队**–具有[支持计划](https://azure.microsoft.com/support/plans/)的用户可以通过直接从[Azure 门户](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)创建新的支持请求来与 Microsoft 客户支持团队联系。

**功能请求**–通过产品的[用户语音页面](https://feedback.azure.com/forums/321918-azure-iot)跟踪 Azure IoT 功能请求。

## <a name="next-steps"></a>后续步骤

* [设备和服务 SDK](iot-hub-devguide-sdks.md)
* [移植指南](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
