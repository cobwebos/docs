---
title: Azure IoT 设备 SDK 平台支持 | Microsoft Docs
description: 概念 - Azure IoT 设备 SDK 支持的平台的列表
author: yzhong94
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: yizhon
ms.openlocfilehash: cf3c80424c4626b62317bda537f9491cafc8198c
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2018
ms.locfileid: "40043288"
---
# <a name="azure-iot-sdks-platform-support"></a>Azure IoT SDK 平台支持

[Azure IoT SDK](iot-hub-devguide-sdks.md) 是一组可以与 IoT 中心和设备预配服务交互的库，具有广泛的语言和平台支持。  该 SDK 在最常用平台上运行，开发人员可以按照[移植指南](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)将 C SDK 移植到特定的平台。 

Microsoft 支持各种操作系统/平台/框架，后者可以通过 Azure IoT C SDK 进行扩展。  某些是团队正式支持的，可以分成多个层，这些层表示用户可以预期的支持级别。  为平台提供全面的支持是指 Microsoft 会：
    - 持续生成主版本和 LTS 支持的版本并对其进行端到端测试
    - 根据需要提供安装指南或包
    - GitHub 上的全面支持

另外，许多合作伙伴已将 C SDK 移植到多个平台，他们负责维护平台抽象层 (PAL)。  [Azure IoT 认证设备目录](https://catalog.azureiotsolutions.com/)还提供了一系列已在其上测试了各种 SDK 的 OS 平台。  另外，这些 SDK 还会定期在这些平台上进行构建，但进行的测试和提供的支持有限：
- MBED2
- Arduino
- Windows CE 2013（于 2018 年 10 月弃用）
- .NET Standard 1.3（包含 .NET Core 2.1 和 .NET Framework 4.7）
- Xamarin iOS、Android、UWP
- Android（包含 Java）

## <a name="supported-platforms"></a>支持的平台
### <a name="c-sdk"></a>C SDK
| 操作系统                  | Arch | 编译器             | TLS 库       |
|---------------------|------|----------------------|-------------------|
| Ubuntu 16.04 LTS    | X64  | gcc-5.4.0            | openssl  - 1.0.2g |
| Ubuntu 18.04 LTS    | X64  | gcc-7.3              | WolfSSL – 1.13    |
| Ubuntu 18.04 LTS    | X64  | Clang 6.0.X          | Openssl – 1.1.0g  |
| OSX 10.13.4         | x64  | XCode 9.4.1          | 本机 OSX        |
| Windows Server 2016 | x64  | Visual Studio 14.0.X | SChannel          |
| Windows Server 2016 | x86  | Visual Studio 14.0.X | SChannel          |
| Debian 9 Stretch    | x64  | gcc-7.3              | Openssl – 1.1.0f  |

### <a name="python-sdk"></a>Python SDK
| 操作系统                  | Arch | 编译器   | TLS 库 |
|---------------------|------|------------|-------------|
| Windows Server 2016 | x86  | Python 2.7 | openssl     |
| Windows Server 2016 | x64  | Python 2.7 | openssl     |
| Windows Server 2016 | x86  | Python 3.5 | openssl     |
| Windows Server 2016 | x64  | Python 3.5 | openssl     |
| Ubuntu 18.04 LTS    | x86  | Python 2.7 | openssl     |
| Ubuntu 18.04 LTS    | x86  | Python 3.4 | openssl     |
| MacOS High Sierra   | x64  | Python 2.7 | openssl     |

### <a name="net-sdk"></a>.NET SDK
| 操作系统                  | Arch | 框架            | 标准          |
|---------------------|------|----------------------|-------------------|
| Ubuntu 16.04 LTS    | X64  | .NET Core 2.1        | .NET Standard 2.0 |
| Windows Server 2016 | X64  | .NET Core 2.1        | .NET Standard 2.0 |
| Windows Server 2016 | X64  | .NET Framework 4.7   | .NET Standard 2.0 |
| Windows Server 2016 | X64  | .NET Framework 4.5.1 | 不适用               |

### <a name="nodejs-sdk"></a>Node.js SDK
| 操作系统                                           | Arch | Node 版本 |
|----------------------------------------------|------|--------------|
| Ubuntu 16.04 LTS（使用 node 6 docker 映像） | X64  | Node 6       |
| Windows Server 2016                          | X64  | Node 6       |

### <a name="java-sdk"></a>Java SDK
| 操作系统                  | Arch | Java 版本 |
|---------------------|------|--------------|
| Ubuntu 16.04 LTS    | X64  | Java 8       |
| Windows Server 2016 | X64  | Java 8       |

## <a name="partner-supported-platforms"></a>合作伙伴支持的平台
| 合作伙伴             | 设备                            | 链接                     | 支持 |
|---------------------|------------------------------------|--------------------------|---------|
| Qualcomm            | Qualcomm MDM9206 LTE IoT 调制解调器     | [Qualcomm LTE for IoT SDK](https://developer.qualcomm.com/software/lte-iot-sdk)（用于 IoT SDK 的 Qualcomm LTE） | [论坛](https://developer.qualcomm.com/forums/software/lte-iot-sdk)   |
| ST Microelectronics | STM32L4 系列、STM32F4 系列      | [X-CUBE-CLOUD](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32cube-expansion-packages/x-cube-cloud.html)             | [支持](https://www.st.com/content/st_com/en/support/support-home.html) |
|                     | STM32F7 系列                     | [X-CUBE-AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32cube-expansion-packages/x-cube-azure.html)             |         |
|                     | 用于 IoT 节点的 STM32L4 发现包 | [P-NUCLEO-AZURE](https://www.st.com/content/st_com/en/products/evaluation-tools/solution-evaluation-tools/communication-and-connectivity-solution-eval-boards/p-nucleo-azure1.html)          |         |
|                     |                                    | [FP-CLD-AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32-ode-function-pack-sw/fp-cld-azure1.html)            |         |
| Espressif           | ESP32                              | [Esp-azure](https://github.com/espressif/esp-azure)                | [GitHub](https://github.com/espressif/esp-azure)  |

## <a name="next-steps"></a>后续步骤
- [设备和服务 SDK](iot-hub-devguide-sdks.md)
- [移植指南](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)