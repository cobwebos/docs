---
title: Azure IoT 设备 SDK 平台支持 | Microsoft Docs
description: 开源设备 SDK 在 GitHub 上以 C、.NET(C#)、Java、Node.js 和 Python 提供，用于将设备连接到 Azure IoT 中心和设备预配服务 (DPS)。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: robinsh
ms.openlocfilehash: 496b890cc49b6b6b9f15213a48472447f801b1c9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "76045118"
---
# <a name="azure-iot-device-sdks-platform-support"></a>Azure IoT 设备 SDK 平台支持

Microsoft 致力于持续扩展支持 Azure IoT 中心的设备的应用领域。 Microsoft 在 GitHub 上发布开源设备 SDK，以帮助将设备连接到 Azure IoT 中心和设备预配服务。 设备 SDK 适用于 C、.NET (C#)、Java、Node.js 和 Python。 Microsoft 测试每个 SDK，确保它可以在 [Microsoft SDK 和设备平台支持](#microsoft-sdks-and-device-platform-support)部分中详述的受支持配置中运行。

除了设备 SDK 以外，Microsoft 还提供其他多种途径来使客户和开发人员能够将其设备连接到 Azure IoT：

* Microsoft 与多家合作伙伴公司协作，以帮助他们基于 Azure IoT C SDK 发布适用于其硬件平台的开发工具包。

* Microsoft 与其信任的合作伙伴合作，以提供不断扩充的已通过 Azure IoT 测试和认证的设备。 有关这些设备的最新列表，请参阅 [Azure IoT 认证设备目录](https://catalog.azureiotsolutions.com/)。

* Microsoft 在 Azure IoT 中心设备 C SDK 中提供了平台抽象层 (PAL)，帮助开发人员轻松地将 SDK 移植到其平台。 有关详细信息，请参阅 [C SDK 移植指南](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)。

本主题提供有关 Microsoft SDK 及其支持的平台配置的信息，以及上面列出的其他每个选项。

## <a name="microsoft-sdks-and-device-platform-support"></a>Microsoft SDK 和设备平台支持

Microsoft 在 GitHub 上发布适用于以下语言的开源 SDK：C、.NET (C#)、Node.js、Java 和 Python。 本部分列出了 SDK 及其依赖项。 满足这些依赖项的任何设备平台都支持这些 SDK。

对于列出的每个 SDK，Microsoft 将会：

* 在多个流行平台上，针对 GitHub 中的相关 SDK 的主分支持续构建并运行端到端测试。  为了提供覆盖不同编译器版本的测试，我们通常会针对最新的 LTS 版本和最常用的版本进行测试。

* 在适用的情况下提供安装指南或安装包。

* 通过开源代码、客户贡献途径，以及产品团队参与 GitHub 问题的解决，来为 GitHub 上的 SDK 提供全面的支持。

### <a name="c-sdk"></a>C SDK

[Azure IoT 中心 C 设备 SDK](https://github.com/Azure/azure-iot-sdk-c) 已使用以下配置进行测试，并支持这些配置。

| 操作系统                  | TLS 库                  | 其他要求                                                                     |
|---------------------|------------------------------|---------------------------------------------------------------------------------------------|
| Linux               | OpenSSL、WolfSSL 或 BearSSL | Berkeley 套接字</br></br>可移植操作系统接口 (POSIX)                       |
| iOS 12.2            | OpenSSL                      | 在 OSX 10.13.4 中仿真的 XCode                                                               |
| Windows 10 系列   | SChannel                     |                                                                                             |
| Mbed OS 5.4         | Mbed TLS 2                   | [MXChip IoT 开发工具包](https://microsoft.github.io/azure-iot-developer-kit/)                  |
| Azure Sphere OS     | WolfSSL                      | [Azure Sphere MT3620](https://azure.microsoft.com/services/azure-sphere/get-started/) |
| Arduino             | BearSSL                      | [ESP32 或 ESP8266](https://github.com/Azure/azure-iot-arduino#simple-sample-instructions) 

### <a name="python-sdk"></a>Python SDK

[Azure IoT 中心 Python 设备 SDK](https://github.com/Azure/azure-iot-sdk-python) 已使用以下配置进行测试，并支持这些配置。

| 操作系统                  | 编译器                          |
|---------------------|-----------------------------------|
| Linux               | Python 2.7.*、3.5 或更高版本 |
| MacOS High Sierra   | Python 2.7.*、3.5 或更高版本 |
| Windows 10 系列   | Python 2.7.*、3.5 或更高版本 |

只有 Python 3.5.3 或更高版本支持异步 API，我们建议使用 3.7 版或更高版本。

### <a name="net-sdk"></a>.NET SDK

[Azure IoT 中心 .NET (C#) 设备 SDK](https://github.com/Azure/azure-iot-sdk-csharp) 已使用以下配置进行测试，并支持这些配置。

| 操作系统                                   | 标准                                                   |
|--------------------------------------|------------------------------------------------------------|
| Linux                                | .NET Core 2.1                                              |
| Windows 10 桌面版和 Server SKU   | .NET Core 2.1、.NET Framework 4.5.1 或 .NET Framework 4.7 |

.NET SDK 还可与包含 [Azure 设备代理](https://github.com/ms-iot/azure-client-tools/blob/master/docs/device-agent/device-agent.md)或包含[可使用 RPC 来与 UWP 应用程序通信的自定义 NTService](https://docs.microsoft.com/samples/microsoft/windows-iotcore-samples/ntservice-rpc/) 的 Windows IoT Core 配合使用。

### <a name="nodejs-sdk"></a>Node.js SDK

[Azure IoT 中心 Node.js 设备 SDK](https://github.com/Azure/azure-iot-sdk-node) 已使用以下配置进行测试，并支持这些配置。

| 操作系统                  | Node 版本    |
|---------------------|-----------------|
| Linux               | LTS 和最新版 |
| Windows 10 系列   | LTS 和最新版 |

### <a name="java-sdk"></a>Java SDK

[Azure IoT 中心 Java 设备 SDK](https://github.com/Azure/azure-iot-sdk-java) 已使用以下配置进行测试，并支持这些配置。

| 操作系统                     | Java 版本 |
|------------------------|--------------|
| Android API 28         | Java 8       |
| Linux x64             | Java 8       |
| Windows 10 系列 x64  | Java 8       |

## <a name="partner-supported-development-kits"></a>合作伙伴支持的开发工具包

Microsoft 与各行业的合作伙伴合作，为多种微处理器体系结构提供开发工具包。 这些合作伙伴已将 Azure IoT C SDK 移植到其平台。 合作伙伴创建并维护 SDK 的平台抽象层 (PAL)。 Microsoft 与以下合作伙伴合作提供扩展支持。

| 合作伙伴             | 设备                            | 链接                     | 支持 |
|---------------------|------------------------------------|--------------------------|---------|
| Espressif           | ESP32 <br/> ESP8266                              | [Esp-azure](https://github.com/espressif/esp-azure)                | [GitHub](https://github.com/espressif/esp-azure)  
| Qualcomm            | Qualcomm MDM9206 LTE IoT 调制解调器     | [Qualcomm LTE for IoT SDK](https://developer.qualcomm.com/software/lte-iot-sdk)（用于 IoT SDK 的 Qualcomm LTE） | [论坛](https://developer.qualcomm.com/forums/software/lte-iot-sdk)   |
| ST Microelectronics | STM32L4 系列 <br/> STM32F4 系列 <br/>  STM32F7 系列 <br/>  用于 IoT 节点的 STM32L4 发现包    | [X-CUBE-AZURE](https://www.st.com/en/embedded-software/x-cube-azure.html) <br/>  <br/> [P-NUCLEO-AZURE](https://www.st.com/content/st_com/en/products/evaluation-tools/solution-evaluation-tools/communication-and-connectivity-solution-eval-boards/p-nucleo-azure1.html) <br/> [FP-CLD-AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32-ode-function-pack-sw/fp-cld-azure1.html)            | [支持](https://www.st.com/content/st_com/en/support/support-home.html)
| Texas Instruments   | CC3220SF LaunchPad </br> CC3220S LaunchPad </br> CC3235SF LaunchPad </br> CC3235S LaunchPad </br> MSP432E4 LaunchPad | [适用于 SimpleLink 的 Azure IoT 插件](https://github.com/TexasInstruments/azure-iot-pal-simplelink) | [TI E2E 论坛](https://e2e.ti.com) <br/> [CC3220 的 TI E2E 论坛](https://e2e.ti.com/support/wireless_connectivity/simplelink_wifi_cc31xx_cc32xx/) <br/> [MSP432E4 的 TI E2E 论坛](https://e2e.ti.com/support/microcontrollers/msp430/) |

## <a name="porting-the-microsoft-azure-iot-c-sdk"></a>移植 Microsoft Azure IoT C SDK

如果前面的部分未提到你所用的设备平台，你可以考虑移植 Azure IoT C SDK。 移植 C SDK 主要涉及到实现 SDK 的平台抽象层 (PAL)。 PAL 定义基元，基元用于衔接设备与 SDK 中的较高级功能。 有关详细信息，请参阅[移植指南](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)。

## <a name="microsoft-partners-and-certified-azure-iot-devices"></a>Microsoft 合作伙伴和认证的 Azure IoT 设备

Microsoft 与许多合作伙伴合作，通过 Azure IoT 测试和认证的设备持续扩展 Azure IoT 的应用领域。

* 若要浏览 Azure IoT 认证的设备，请参阅 [Microsoft Azure IoT 认证设备目录](https://catalog.azureiotsolutions.com/)。

* 若要了解有关 Azure IoT 认证生态系统的详细信息，请参阅[加入 IoT 认证生态系统](https://catalog.azureiotsolutions.com/register)。

## <a name="connecting-to-iot-hub-without-an-sdk"></a>在不使用 SDK 的情况下连接到 IoT 中心

如果你无法使用某个 IoT 中心设备 SDK，可以在能够发送和接收 HTTPS 请求与响应的任何应用程序中使用 [IoT 中心 REST API](https://docs.microsoft.com/rest/api/iothub/) 直接连接到 IoT 中心。

## <a name="support-and-other-resources"></a>支持和其他资源

如果在使用 Azure IoT 设备 SDK 时遇到问题，可以通过多种方式来寻求支持。 可尝试使用以下渠道之一：

**报告 bug** – 可在相关 GitHub 项目的“问题”页上报告设备 SDK 中的 Bug。 修复很快就会从项目实施到产品更新中。

* [Azure IoT 中心 C SDK 问题](https://github.com/Azure/azure-iot-sdk-c/issues)

* [Azure IoT 中心 .NET (C#) SDK 问题](https://github.com/Azure/azure-iot-sdk-csharp/issues)

* [Azure IoT 中心 Java SDK 问题](https://github.com/Azure/azure-iot-sdk-java/issues)

* [Azure IoT 中心 Node.js SDK 问题](https://github.com/Azure/azure-iot-sdk-node/issues)

* [Azure IoT 中心 Python SDK 问题](https://github.com/Azure/azure-iot-sdk-python/issues)

**Microsoft 客户支持团队** – 拥有[支持计划](https://azure.microsoft.com/support/plans/)的用户可以通过直接从 [Azure 门户](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)创建新的支持请求来与 Microsoft 客户支持团队进行沟通。

**功能请求** – 我们会通过产品的 [User Voice 页](https://feedback.azure.com/forums/321918-azure-iot)跟踪 Azure IoT 功能请求。

## <a name="next-steps"></a>后续步骤

* [设备和服务 SDK](iot-hub-devguide-sdks.md)
* [移植指南](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
