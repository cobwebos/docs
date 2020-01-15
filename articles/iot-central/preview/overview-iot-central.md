---
title: 什么是 Azure IoT Central | Microsoft Docs
description: Azure IoT Central 是一个 IoT 应用程序平台，可简化 IoT 解决方案的创建，并有助于减轻 IoT 管理、运营和开发的负担以及降低相关成本。 本文概述了 Azure IoT Central 的功能。
author: dominicbetts
ms.author: dobett
ms.date: 12/10/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 52504fb8333b286407b3f2df8f962da59b80ac53
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75434856"
---
# <a name="what-is-azure-iot-central-preview-features"></a>Azure IoT Central（预览版功能）是什么？

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

> [!WARNING]
> Azure IoT Central 中的 [IoT 即插即用](../../iot-pnp/overview-iot-plug-and-play.md)功能目前为公共预览版。 请勿将已启用 IoT 即插即用的 IoT Central [应用程序模板](../core/concepts-app-templates.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json)用于生产工作负荷。 对于生产环境，请使用从当前正式发布的[应用程序模板](../core/concepts-app-templates.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json)创建的 IoT Central 应用程序。

IoT Central 是一个 IoT 应用程序平台，可减轻开发、管理和维护企业级 IoT 解决方案的负担和成本。 选择使用 IoT Central 进行构建可将时间、金钱和精力集中于使用 IoT 数据转换业务，而不仅仅是维护和更新复杂且不断发展的 IoT 基础结构。

通过 Web UI 可以监视设备条件、创建规则并在其整个生命周期中管理数百万个设备及其数据。 此外，该平台将 IoT 智能扩展到了业务线应用程序，你可以利用该平台根据设备的见解采取行动。

本文概述了与 IoT Central 相关的以下内容：

- 与项目关联的典型角色。
- 如何创建应用程序。
- 如何将设备连接到应用程序。
- 如何管理应用程序。
- IoT Central 中的 Azure IoT Edge 功能。
- 如何将 Azure IoT Edge 运行时支持的设备连接到应用程序。

## <a name="known-issues"></a>已知问题

> [!Note]
> 这些已知问题仅适用于 IoT Central 预览版应用程序。

- 连续数据导出不支持 Avro 格式（不兼容）。
- 目前不支持 GeoJSON。
- 目前不支持地图图块。
- 作业不支持复杂类型。
- 不支持数组架构类型。
- 仅支持 C 设备 SDK 以及 Node.js 设备和服务 SDK。
- 仅在美国和欧洲地区提供。
- 必须在同一文件中以内联方式定义设备功能模型的所有接口。

## <a name="personas"></a>角色

本 IoT Central 文档提到了四个与 IoT Central 应用程序进行交互的角色：

- “解决方案构建者”  负责定义可以连接到应用程序的设备类型，并为操作员自定义应用程序。
- “操作员”  管理连接到应用程序的设备。
- “管理员”  负责执行管理任务，例如，管理应用程序中的[用户角色和权限](howto-administer.md)。
- “设备开发者”  创建在连接到应用程序的设备或 IoT Edge 模块上运行的代码。

## <a name="create-your-iot-central-application"></a>创建 IoT Central 应用程序

作为解决方案构建者，你可以使用 IoT Central 为组织创建自定义的、托管在云中的 IoT 解决方案。 自定义的 IoT 解决方案通常包括：

- 一个基于云的应用程序，它从设备接收遥测数据并使得你可以管理这些设备。
- 运行自定义代码且连接到基于云的应用程序的多个设备。

可以快速部署新的 IoT Central 应用程序，然后在浏览器中对其进行自定义以满足特定要求。 作为解决方案构建者，你可以使用基于 Web 的工具为连接到应用程序的设备创建“设备模板”  。 设备模板是定义一种设备的特征和行为的蓝图，例如：

- 它发送的遥测。
- 操作员可以修改的业务属性。
- 由设备设置的、在应用程序中处于只读状态的设备属性。
- 由操作员设置的属性，决定了设备的行为。

此设备模板包括：

- 设备功能模型  ，描述设备应该实现的功能，例如发送的遥测数据和报告的属性。
- 不存储在设备上的云属性。
- 属于 IoT Central 应用程序的自定义、仪表板和窗体。

### <a name="create-device-templates"></a>创建设备模板

IoT Central 可以通过 [IoT 即插即用](../../iot-pnp/overview-iot-plug-and-play.md)来集成设备，不需你编写任何嵌入式设备代码。 IoT 即插即用的核心是描述设备功能的设备功能模型架构。 在 IoT Central 预览版应用程序中，设备模板使用这些 IoT 即插即用设备功能模型。

作为解决方案构建者，你在创建设备模板时有多种选择：

- 在 IoT Central 中设计设备模板，然后在设备代码中实现其设备功能模型。
- 从 [Azure IoT 认证设备目录](https://aka.ms/iotdevcat)导入设备功能模型，然后添加 IoT Central 应用程序所需的任何云属性、自定义和仪表板。
- 使用 Visual Studio Code 创建设备功能模型。 基于模型实现设备代码，并将设备连接到 IoT Central 应用程序。 IoT Central 从库中查找设备功能模型，并为你创建简单的设备模板。
- 使用 Visual Studio Code 创建设备功能模型。 基于模型实现设备代码。 将设备功能模型手动导入 IoT Central 应用程序，然后添加 IoT Central 应用程序所需的任何云属性、自定义和仪表板。

作为解决方案构建者，你可以使用 IoT Central 来生成测试设备的代码，以便验证设备模板。

### <a name="customize-the-ui"></a>自定义 UI

作为解决方案构建者，你还可以为负责日常使用应用程序的操作员自定义 IoT Central 应用程序 UI。 解决方案构建者可以进行的自定义包括：

- 在设备模板上定义属性和设置的布局。
- 配置自定义仪表板，以帮助操作员获得见解，并更快地解决问题。
- 配置自定义分析来从已连接的设备探究时序数据。

## <a name="connect-your-devices"></a>连接数据

在构建者定义可以连接到应用程序的设备类型后，设备开发者创建要在设备上运行的代码。 设备开发者可以使用 Microsoft 的开源 [Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) 来创建设备代码。 这些 SDK 提供了广泛的语言、平台和协议支持来满足将设备连接到 IoT Central 应用程序时的需求。 SDK 可帮助实现以下设备功能：

- 创建安全连接。
- 发送遥测数据。
- 报告状态。
- 接收配置更新。

有关详细信息，请参阅博客文章 [Benefits of using the Azure IoT SDKs, and pitfalls to avoid if you don't](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/)（使用 Azure IoT SDK 的好处，以及未使用时要避免的陷阱）。

### <a name="azure-iot-edge-devices"></a>Azure IoT Edge 设备

除了使用 [Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) 创建的设备之外，还可以将 [Azure IoT Edge 设备](../../iot-edge/about-iot-edge.md)连接到 IoT Central 应用程序。 使用 Azure IoT Edge 可以直接在 IoT Central 管理的 IoT 设备上运行云智能和自定义逻辑。 IoT Edge 运行时使你能够：

- 在设备上安装和更新工作负荷。
- 维护设备上的 Azure IoT Edge 安全标准。
- 确保 IoT Edge 模块始终处于运行状态。
- 将模块运行状况报告给云以进行远程监视。
- 管理下游叶设备与 IoT Edge 设备之间、IoT Edge 设备上的模块之间以及 IoT Edge 设备与云之间的通信。

有关详细信息，请参阅 [Azure IoT Edge 设备和 IoT Central](./concepts-architecture.md#azure-iot-edge-devices)。

## <a name="manage-your-application"></a>管理应用程序

IoT Central 应用程序完全由 Microsoft 托管，这降低了管理应用程序时的管理开销。

操作员使用 IoT Central 应用程序管理 IoT Central 解决方案中的设备。 操作员执行以下任务：

- 监视连接到应用程序的设备。
- 排查和修正设备问题。
- 预配新设备。

作为解决方案构建者，你可以定义对连接设备的数据流进行操作的自定义规则和操作。 操作员可以在设备级别启用或禁用这些规则来控制和自动执行应用程序中的任务。

管理员通过[用户角色和权限](howto-administer.md)管理对应用程序的访问权限。

## <a name="quotas"></a>配额

每个 Azure 订阅具有默认的配额，这些配额可能影响 IoT 解决方案的范围。 目前，IoT Central 将可在单个订阅中部署的应用程序数限制为 10 个。 如果需要增加此限制，请联系 [Microsoft 支持](https://azure.microsoft.com/support/options/)。

## <a name="next-steps"></a>后续步骤

现在，你已大致了解了 IoT Central，下面是建议的后续步骤：

- 了解可用的[用于创建 IoT 解决方案的 Azure 技术和服务](../../iot-fundamentals/iot-services-and-technologies.md)。
- 熟悉 [Azure IoT Central UI](overview-iot-central-tour.md)。
- 通过[创建 Azure IoT Central 应用程序](quick-deploy-iot-central.md)开始使用此解决方案。
- 详细了解 [IoT 即插即用](../../iot-pnp/overview-iot-plug-and-play.md)
- 了解如何[创建 Azure IoT Edge 设备模板](./tutorial-define-edge-device-type.md)
