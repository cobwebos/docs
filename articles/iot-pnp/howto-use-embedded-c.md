---
title: 在受限制的设备上使用 IoT 即插即用 |Microsoft Docs
description: 了解如何在受限制的设备上使用 SDK 实现嵌入式 C 或 Azure RTO 来实现 IoT 即插即用。
author: elhorton
ms.author: elhorton
ms.date: 09/23/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: d61ca10612a0935f8483745d164835d7498280c0
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92042807"
---
# <a name="implement-iot-plug-and-play-on-constrained-devices"></a>在受限制的设备上实施 IoT 即插即用

如果要为 *受约束的设备*进行开发，可以将 IoT 即插即用与 azure SDK 配合使用，使用 [适用于嵌入的 C IoT 客户端库](https://aka.ms/embeddedcsdk) 或 [azure rto](/azure/rtos/overview-rtos)。 本文包括这些约束方案的链接和资源。

## <a name="use-the-sdk-for-embedded-c"></a>使用 SDK 进行嵌入式 C

适用于 Embedded C 的 SDK 提供了一个轻型解决方案，用于将受限制的设备连接到 Azure IoT 服务，包括使用 [IoT 即插即用约定](concepts-convention.md)。 以下链接包括真实设备的示例以及教育和调试目的。

### <a name="use-a-real-device"></a>使用真实设备

若要获取完整的端到端教程，使用适用于嵌入的 C 的 SDK、设备预配服务和 IoT 即插即用在实际设备上，请参阅重新 [调整 PIC-IoT Wx 开发板的用途，以通过 IoT 中心设备预配服务连接到 Azure](https://github.com/Azure-Samples/Microchip-PIC-IoT-Wx)。

### <a name="introductory-samples"></a>介绍性示例

SDK for Embedded C 存储库包含 [几个示例](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/samples/iot#iot-hub-plug-and-play-sample) ，演示如何使用 IoT 即插即用：

> [!NOTE]
> 这些示例显示在 Windows 和 Linux 上运行，用于进行教育和调试。 在生产方案中，示例仅适用于受限制的设备。

- [带有 SDK for Embedded C 的恒温器示例](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/paho_iot_hub_pnp_sample.c)

- [适用于嵌入 C 的 SDK 的温度控制器示例](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/paho_iot_hub_pnp_component_sample.c)

## <a name="using-azure-rtos"></a>使用 Azure RTO

Azure RTO 包含一个轻型层，可将本机连接添加到 Azure IoT 云服务。 此层提供一种简单的机制，用于将受限制的设备连接到 Azure IoT，同时使用 Azure RTO 的高级功能。 若要了解详细信息，请参阅 [什么是 MICROSOFT AZURE rto](/azure/rtos/overview-rtos)。

### <a name="toolchains"></a>工具链

Azure RTO 示例随不同种类的 IDE 和工具链组合提供，例如：

- IAR： IAR 的 [嵌入式工作台](https://www.iar.com/iar-embedded-workbench/) IDE
- GCC/CMake：在开源 [CMake](https://cmake.org/) 内部版本系统和 [GNU Arm Embedded 工具链](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm)上构建。
- MCUExpresso： NXP 的 [MCUXPRESSO IDE](https://www.nxp.com/design/software/development-software/mcuxpresso-software-and-tools-/mcuxpresso-integrated-development-environment-ide:MCUXpresso-IDE)
- STM32Cube： STMicroelectronic 的 [STM32CUBE IDE](https://www.st.com/en/development-tools/stm32cubeide.html)
- MPLAB：微芯片的 [MPLAB X IDE](https://www.microchip.com/mplab/mplab-x-ide)

### <a name="samples"></a>示例

若要深入了解如何在不同设备上启动 Azure RTO 和 IoT 即插即用，请参阅下表：

制造商 | 设备 | 示例 |
| --- | --- | --- |
| 微芯片 | [ATSAME54-XPRO](https://www.microchip.com/developmenttools/productdetails/atsame54-xpro) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/Microchip/ATSAME54-XPRO) • [IAR](https://aka.ms/azrtos-sample/e54-iar) • [MPLAB](https://aka.ms/azrtos-sample/e54-mplab)
| MXCHIP | [AZ3166](https://aka.ms/iot-devkit) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/MXChip/AZ3166)
| NXP | [MIMXRT1060-EVK](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/mimxrt1060-evk-i-mx-rt1060-evaluation-kit:MIMXRT1060-EVK) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/NXP/MIMXRT1060-EVK) • [IAR](https://aka.ms/azrtos-sample/rt1060-iar) • [MCUXpresso](https://aka.ms/azrtos-sample/rt1060-mcuxpresso)
| STMicroelectronics | [32F746GDISCOVERY](https://www.st.com/en/evaluation-tools/32f746gdiscovery.html) | [IAR](https://aka.ms/azrtos-sample/f746g-iar) • [STM32Cube](https://aka.ms/azrtos-sample/f746g-cubeide)
| STMicroelectronics | [B-L475E-IOT01](https://www.st.com/en/evaluation-tools/b-l475e-iot01a.html) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) • [IAR](https://aka.ms/azrtos-sample/l4s5-iar) • [STM32Cube](https://aka.ms/azrtos-sample/l4s5-cubeide)
| STMicroelectronics | [B-L4S5I-IOT01](https://www.st.com/en/evaluation-tools/b-l4s5i-iot01a.html) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) • [IAR](https://aka.ms/azrtos-sample/l4s5-iar) • [STM32Cube](https://aka.ms/azrtos-sample/l4s5-cubeide)

## <a name="next-steps"></a>后续步骤

现在，你已了解在受约束的设备上实现 IoT 即插即用的选项，接下来建议的下一步是了解 [IoT 即插即用约定](concepts-convention.md)。