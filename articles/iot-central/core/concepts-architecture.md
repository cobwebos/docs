---
title: Azure IoT Central 中的体系结构概念 | Microsoft Docs
description: 本文介绍与 Azure IoT Central 的体系结构相关的重要概念
author: dominicbetts
ms.author: dobett
ms.date: 11/27/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 12ad231d81b6c134ebb8d4902b3f95c978e9622d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271637"
---
# <a name="azure-iot-central-architecture"></a>Azure IoT Central 体系结构



本文概述 Microsoft Azure IoT Central 体系结构。

![顶层体系结构](media/concepts-architecture/architecture.png)

## <a name="devices"></a>设备

设备与 Azure IoT Central 应用程序交换数据。 设备可以：

- 发送度量，例如遥测。
- 与应用程序同步设置。

在 Azure IoT Central 中，设备可以与应用程序交换的数据在设备模板中指定。 有关设备模板的详细信息，请参阅[元数据管理](#metadata-management)。

若要详细了解设备如何连接到 Azure IoT Central 应用程序，请参阅[设备连接](concepts-get-connected.md)。

## <a name="azure-iot-edge-devices"></a>Azure IoT Edge 设备

除了使用 [Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) 创建的设备之外，还可以将 [Azure IoT Edge 设备](../../iot-edge/about-iot-edge.md)连接到 IoT Central 应用程序。 使用 IoT Edge 可以直接在 IoT Central 管理的 IoT 设备上运行云智能和自定义逻辑。 IoT Edge 运行时使你能够：

- 在设备上安装和更新工作负荷。
- 维护设备上的 IoT Edge 安全标准。
- 确保 IoT Edge 模块始终处于运行状态。
- 将模块运行状况报告给云以进行远程监视。
- 管理下游叶设备与 IoT Edge 设备之间、IoT Edge 设备上的模块之间以及 IoT Edge 设备与云之间的通信。

![包含 Azure IoT Edge 的 Azure IoT Central](./media/concepts-architecture/iotedge.png)

IoT Central 为 IoT Edge 设备启用以下功能：

- 设备模板，用于描述 IoT Edge 设备的功能，例如：
  - 部署清单上传功能，可帮助管理设备群的清单。
  - 在 IoT Edge 设备上运行的模块。
  - 每个模块发送的遥测数据。
  - 每个模块报告的属性。
  - 每个模块响应的命令。
  - Azure IoT Edge 网关设备功能模型与下游设备功能模型之间的关系。
  - 不存储在 IoT Edge 设备上的云属性。
  - 属于 IoT Central 应用程序的自定义、仪表板和窗体。

  有关详细信息，请参阅[将 Azure IoT 边缘设备连接到 Azure IoT 中心应用程序](./concepts-iot-edge.md)一文。

- 使用 Azure IoT 设备预配服务大规模预配 Azure IoT Edge 设备的功能
- 规则和操作。
- 自定义仪表板和分析。
- 从 Azure IoT Edge 设备连续导出遥测数据。

### <a name="iot-edge-device-types"></a>IoT Edge 设备类型

IoT Central 将 IoT Edge 设备类型分类为：

- 叶设备。 IoT Edge 设备可以包含下游叶设备，但这些设备不会在 IoT Central 中预配。
- 包含下游设备的网关设备。 网关设备和下游设备均在 IoT Central 中进行预配

![包含 IoT Edge 的 IoT Central 概述](./media/concepts-architecture/gatewayedge.png)

### <a name="iot-edge-patterns"></a>IoT Edge 模式

IoT Central 支持以下 IoT Edge 设备模式：

#### <a name="iot-edge-as-leaf-device"></a>用作叶设备的 IoT Edge

![用作叶设备的 IoT Edge](./media/concepts-architecture/edgeasleafdevice.png)

IoT Edge 设备在 IoT Central 中预配，任何下游设备及其遥测均表示为来自 IoT Edge 设备。 已连接到 IoT Edge 设备的下游设备不会在 IoT Central 中预配。

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity"></a>已连接到有标识下游设备的 IoT Edge 网关设备

![有下游设备标识的 IoT Edge](./media/concepts-architecture/edgewithdownstreamdeviceidentity.png)

IoT Edge 设备连同已连接到该设备的下游设备一起在 IoT Central 中预配。 用于通过网关预配下游设备的运行时支持当前不受支持。

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity-provided-by-the-iot-edge-gateway"></a>已连接到具有 IoT Edge 网关所提供标识的下游设备的 IoT Edge 网关设备

![包含无标识下游设备的 IoT Edge](./media/concepts-architecture/edgewithoutdownstreamdeviceidentity.png)

IoT Edge 设备连同已连接到该设备的下游设备一起在 IoT Central 中预配。 用于为下游设备提供标识和预配下游设备的网关运行时支持当前不受支持。 如果你引入自己的标识转换模块，则 IoT Central 可以支持此模式。

## <a name="cloud-gateway"></a>云网关

Azure IoT Central 使用 Azure IoT 中心作为启用设备连接的云网关。 IoT 中心允许：

- 在云中进行大规模数据引入。
- 设备管理。
- 安全的设备连接。

若要详细了解 IoT 中心，请参阅 [Azure IoT 中心](https://docs.microsoft.com/azure/iot-hub/)。

若要详细了解 Azure IoT Central 中的设备连接，请参阅[设备连接](concepts-get-connected.md)。

## <a name="data-stores"></a>数据存储

Azure IoT Central 在云中存储应用程序数据。 存储的应用程序数据包括：

- 设备模板。
- 设备标识。
- 设备元数据。
- 用户和角色数据。

Azure IoT Central 将时序存储用于从设备发送的度量数据。 设备提供的时序数据供分析服务使用。

## <a name="analytics"></a>Analytics

分析服务负责生成应用程序显示的自定义报告数据。 操作员可以[自定义在应用程序中显示的分析](howto-create-analytics.md)。 分析服务在 [Azure 时序见解](https://azure.microsoft.com/services/time-series-insights/)基础上构建，可以处理从设备发送的度量数据。

## <a name="rules-and-actions"></a>规则和操作

[规则和操作](tutorial-create-telemetry-rules.md)可以紧密地配合使用，以便自动完成应用程序中的任务。 开发者可以根据设备遥测（例如温度超过定义的阈值）来定义规则。 Azure IoT Central 使用流处理器来确定何时满足规则条件。 规则条件在得到满足的情况下，会触发开发者定义的操作。 例如，可以通过某个操作向工程师发送通知电子邮件，告知对方设备中的温度过高。

## <a name="metadata-management"></a>元数据管理

在 Azure IoT Central 应用程序中，设备模板用于定义设备类型的行为和功能。 例如，致冷器设备模板指定致冷器发送给应用程序的遥测。

![模板体系结构](media/concepts-architecture/template-architecture.png)

在 IoT 中央应用程序设备模板中包含：

- **设备功能模型**指定设备的功能，例如它发送的遥测数据、用于定义设备状态的属性，以及设备响应的命令。 设备功能已组织到一个或多个界面中。 有关设备功能模型的详细信息，请参阅[IoT 即插即用（预览）](../../iot-pnp/overview-iot-plug-and-play.md)文档。
- **云属性**指定 IoT Central 为设备存储的属性。 这些属性只存储在 IoT Central 中，而不会发送到设备。
- **视图**指定生成器创建的、让操作员监视和管理设备的仪表板和窗体。
- **自定义项**让生成器替代设备功能模型中的某些定义，使之与 IoT Central 应用程序更相关。

一个应用程序可以有一个或多个基于每个设备模板的模拟设备和真实设备。

## <a name="data-export"></a>数据导出

在 Azure IoT Central 应用程序中，可以[连续将数据导出](howto-export-data.md)到自己的 Azure 事件中心和 Azure 服务总线实例。 还可以定期将数据导出到 Azure Blob 存储帐户。 IoT Central 可以导出度量、设备和设备模板。

## <a name="batch-device-updates"></a>设备批量更新

在 Azure IoT Central 应用程序中，可以[创建并运行作业](howto-run-a-job.md)来管理连接的设备。 使用这些作业可对设备属性或设置进行批量更新，或运行命令。 例如，可以创建一个作业来提高多个冷冻食品自动售货机的风扇速度。

## <a name="role-based-access-control-rbac"></a>基于角色的访问控制 (RBAC)

管理员可以使用预定义的角色之一或创建自定义角色为 Azure IoT 中心应用程序[定义访问规则](howto-manage-users-roles.md)。 角色确定用户有权访问的应用程序区域以及他们可以执行哪些操作。

## <a name="security"></a>安全性

Azure IoT Central 中的安全功能包括：

- 对传输中的和静止时的数据加密。
- 通过 Azure Active Directory 或 Microsoft 帐户提供身份验证。 支持双重身份验证。
- 完全的租户隔离。
- 设备级别安全性。

## <a name="ui-shell"></a>UI Shell

UI Shell 是一个现代的基于 HTML5 浏览器的应用程序，响应速度快。
管理员可以通过应用自定义主题，并将帮助链接修改为指向你自己的自定义帮助资源，来自定义应用程序的 UI。 若要详细了解 UI 自定义，请参阅[自定义 Azure IoT Central UI](howto-customize-ui.md) 一文。

操作员可以创建个性化的应用程序仪表板。 可以创建多个仪表板用于显示不同的数据，并可以切换这些仪表板。

## <a name="next-steps"></a>后续步骤

了解 Azure IoT Central 的体系结构后，建议接下来了解 Azure IoT Central 中的[设备连接](concepts-get-connected.md)。
