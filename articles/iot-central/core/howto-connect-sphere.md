---
title: 在 Azure IoT Central 中连接 Azure Sphere 设备| Microsoft Docs
description: 了解如何将 Azure Sphere (DevKit) 设备连接到 Azure IoT Central 应用程序。
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.author: sandeepu
author: sandeeppujar
ms.date: 04/30/2020
ms.openlocfilehash: cd583b79d6c73ff8da1c9770bf72b3e6990c3140
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594356"
---
# <a name="connect-an-azure-sphere-device-to-your-azure-iot-central-application"></a>将 Azure Sphere 设备连接到 Azure IoT Central 应用程序

本文适用于设备开发人员。

本文介绍如何将 Azure Sphere (DevKit) 设备连接到 Azure IoT Central 应用程序。

Azure Sphere 是一个安全的高级应用程序平台，具有针对互连设备的内置通信和安全功能。 它包括一个安全的、连接的跨接微控制器单元 (MCU)、一个定制的基于 linux 的高级操作系统 (OS) 和一个基于云的安全服务（提供连续的可续订安全性）。 有关详细信息，请参阅[什么是 Azure Sphere？](https://docs.microsoft.com/azure-sphere/product-overview/what-is-azure-sphere)。

[Azure Sphere 开发工具包](https://azure.microsoft.com/services/azure-sphere/get-started/)提供开始原型制作和开发 Azure Sphere 应用程序所需的一切。 带有 Azure Sphere 的 Azure IoT Central 可使 IoT 解决方案实现端到端堆栈。 Azure Sphere 提供设备支持和 IoT Central，作为一个零代码的托管 IoT 应用程序平台。

通过本操作说明文章，你可以：

- 使用库中的 Azure Sphere DevKit 设备模板在 IoT Central 中创建 Azure Sphere 设备。
- 准备用于 Azure IoT 的 Azure Sphere DevKit 设备。
- 将 Azure Sphere DevKit 连接到 Azure IoT Central。
- 在 IoT Central 中查看设备的遥测。

## <a name="prerequisites"></a>先决条件

若要完成本文中的步骤，需要准备好以下资源：

- Azure IoT Central 应用程序。
- Visual Studio 2019，版本 16.4 或更高版本。
- [Seeed Studios 的 Azure Sphere MT3620 开发工具包](https://docs.microsoft.com/azure-sphere/hardware/mt3620-reference-board-design)。

> [!NOTE]
> 如果没有物理设备，则在第一步之后跳到最后一部分以尝试创建模拟设备。

## <a name="create-the-device-in-iot-central"></a>在 IoT Central 中创建设备

若要在 IoT Central 中创建 Azure Sphere 设备：

1. 在 Azure IoT Central 应用程序中，选择“设备模板”选项卡，并选择“+ 新建”。  在“使用特色设备模板”部分中，选择“Azure Sphere 示例设备” 。

    :::image type="content" source="media/howto-connect-sphere/sphere-create-template.png" alt-text="Azure Sphere DevKit 的设备模板":::

1. 在设备模板中，编辑名为“概述”的视图以显示“温度”和“按钮按下”  。

1. 选择“编辑设备和云数据”视图类型以添加另一个显示读/写属性“状态 LED”的视图。 将“状态 LED”属性拖到窗体右侧的空白虚线矩形中。 选择“保存”。

## <a name="prepare-the-device"></a>准备设备

在将 Azure Sphere DevKit 设备连接到 IoT Central 之前，需要[设置设备和开发环境](https://github.com/Azure/azure-sphere-samples/tree/master/Samples/AzureIoT)。

## <a name="connect-the-device"></a>连接设备

若要使示例连接到 IoT Central，必须[配置 Azure IoT Central 应用程序，然后修改示例的应用程序清单](https://aka.ms/iotcentral-sphere-git-readme)。

## <a name="view-the-telemetry-from-the-device"></a>查看设备的遥测

将设备连接到 IoT Central 时，可以在仪表板上看到遥测。

:::image type="content" source="media/howto-connect-sphere/sphere-view.png" alt-text="Azure Sphere DevKit 的仪表板":::

## <a name="create-a-simulated-device"></a>创建模拟设备

如果没有物理 Azure Sphere DevKit 设备，可以创建模拟设备以试用 Azure IoT Central 应用程序。

要创建模拟设备：

- 选择“设备 > Azure IoT Sphere”
- 选择“+ 新建”。
- 输入唯一设备 ID 和一个易记的设备名称。
- 启用“模拟”设置。
- 选择“创建”。

## <a name="next-steps"></a>后续步骤

如果你是设备开发人员，则建议执行以下后续步骤：

- 阅读有关 [Azure IoT Central 中的设备连接](./concepts-get-connected.md)的信息
- 了解如何[使用 Azure CLI 监视设备连接](./howto-monitor-devices-azure-cli.md)
