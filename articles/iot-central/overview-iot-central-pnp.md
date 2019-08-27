---
title: 什么是 Azure IoT Central | Microsoft Docs
description: Azure IoT Central 是一个可用于构建和管理自定义 IoT 解决方案的端到端 SaaS 解决方案。 本文概述了 Azure IoT Central 的功能。
author: dominicbetts
ms.author: dobett
ms.date: 07/06/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 0675be988e7e9560560dd07338563c9fa22fcb4c
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2019
ms.locfileid: "69881646"
---
# <a name="what-is-azure-iot-central-preview-features"></a>Azure IoT Central（预览版功能）是什么？

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

> [!WARNING]
> Azure IoT Central 中的 [IoT 即插即用](https://aka.ms/iot-pnp-docs)功能目前为公共预览版。 请勿将已启用 IoT 即插即用的 IoT Central 应用程序用于生产工作负荷。 对于生产环境，请使用从当前正式发布的应用程序模板创建的 IoT Central 应用程序。

Azure IoT Central 是一个完全托管的 IoT 软件即服务解决方案，可用来轻松创建对物理和电子世界进行连接的产品。 可以通过以下方式实现有关已连接产品的远景：

- 通过已连接的设备提供新见解，来为客户提供更好的产品和体验。
- 为组织创造新的业务机会。

与典型的 IoT 项目相比，Azure IoT Central：

- 降低了管理负担。
- 降低了运营成本和开销。
- 可以轻松自定义应用程序，同时利用：
  - 行业领先的技术，例如 [Azure IoT 中心](https://azure.microsoft.com/services/iot-hub/)和 [Azure 时序见解](https://azure.microsoft.com/services/time-series-insights/)。
  - 企业级安全功能，例如端到端加密。

下面的视频概述了 Azure IoT Central：

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Microsoft-IoT-Central-intro-walkthrough/Player]

本文概述了与 Azure IoT Central 相关的以下内容：

- 与项目关联的典型角色。
- 如何创建应用程序。
- 如何将设备连接到应用程序。
- 如何管理应用程序。

## <a name="known-issues"></a>已知问题

> [!Note]
> 这些已知问题仅适用于 IoT Central 预览版应用程序。

- 规则并不支持所有操作（仅限电子邮件）。
- 对于复杂的类型 - 规则、分析和设备组不受支持。
- 连续数据导出不支持 Avro 格式（不兼容）。
- 模拟设备不支持所有复杂类型。
- 目前不支持 GeoJSON。
- 目前不支持地图图块。
- 作业不支持复杂类型。
- 不支持数组架构类型。
- 不支持应用程序模板导出和应用程序复制。
- 仅支持 C 设备 SDK 以及 Node.js 设备和服务 SDK。
- 仅在选定区域提供。

## <a name="personas"></a>角色

本 Azure IoT Central 文档提到了四个与 Azure IoT Central 应用程序进行交互的角色：

- “构建者”  负责定义可以连接到应用程序的设备类型，并为操作员自定义应用程序。
- “操作员”  管理连接到应用程序的设备。
- “管理员”  负责执行管理任务，例如，管理应用程序中的用户和角色。
- “设备开发者”  创建在连接到应用程序的设备上运行的代码。

## <a name="create-your-azure-iot-central-application"></a>创建 Azure IoT Central 应用程序

构建者使用 Azure IoT Central 为组织创建自定义的、托管在云中的 IoT 解决方案。 自定义的 IoT 解决方案通常包括：

- 一个基于云的应用程序，它从设备接收遥测数据并使得你可以管理这些设备。
- 运行自定义代码且连接到基于云的应用程序的多个设备。

可以快速部署新的 Azure IoT Central 应用程序，然后在浏览器中对其进行自定义以满足特定要求。 作为构建者，你可以使用基于 Web 的工具为连接到应用程序的设备创建“设备模板”  。 设备模板是定义一种设备的特征和行为的蓝图，例如：

- 它发送的遥测。
- 操作员可以修改的业务属性。
- 由设备设置的、在应用程序中处于只读状态的设备属性。
- 由操作员设置的属性，决定了设备的行为。

此设备模板包括：

- 设备功能模型  ，描述设备应该实现的功能，例如发送的遥测数据和报告的属性。
- 存储在设备上的云属性。
- 属于 IoT Central 应用程序的自定义、仪表板和窗体。

### <a name="create-device-templates"></a>创建设备模板

IoT Central 可以通过 [IoT 即插即用](https://aka.ms/iot-pnp-docs)来集成设备，不需你编写任何嵌入式设备代码。 IoT 即插即用的核心是描述设备功能的设备功能模型架构。 在 IoT Central 预览版应用程序中，设备模板使用这些 IoT 即插即用设备功能模型。

作为构建者，你在创建设备模板时有多种选择：

- 在 IoT Central 中设计设备模板，然后在设备代码中实现其设备功能模型。
- 从 [Azure IoT 认证设备目录](https://aka.ms/iotdevcat)导入设备功能模型，然后添加 IoT Central 应用程序所需的任何云属性、自定义和仪表板。
- 使用 Visual Studio Code 创建设备功能模型。 基于模型实现设备代码，并将设备连接到 IoT Central 应用程序。 IoT Central 从库中查找设备功能模型，并为你创建简单的设备模板。
- 使用 Visual Studio Code 创建设备功能模型。 基于模型实现设备代码。 将设备功能模型手动导入 IoT Central 应用程序，然后添加 IoT Central 应用程序所需的任何云属性、自定义和仪表板。

作为构建者，你可以使用 IoT Central 来生成测试设备的代码，以便验证设备模板。

### <a name="customize-the-ui"></a>自定义 UI

构建者还可以为负责日常使用应用程序的操作员自定义 Azure IoT Central 应用程序 UI。 构建者可以进行的自定义包括：

- 在设备模板上定义属性和设置的布局。
- 配置自定义仪表板，以帮助操作员获得见解，并更快地解决问题。
- 配置自定义分析来从已连接的设备探究时序数据。

## <a name="connect-your-devices"></a>连接数据

在构建者定义可以连接到应用程序的设备类型后，设备开发者创建要在设备上运行的代码。 设备开发者可以使用 Microsoft 的开源 [Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) 来创建设备代码。 这些 SDK 提供了广泛的语言、平台和协议支持来满足将设备连接到 Azure IoT Central 应用程序时的需求。 SDK 可帮助实现以下设备功能：

- 创建安全连接。
- 发送遥测数据。
- 报告状态。
- 接收配置更新。

有关详细信息，请参阅博客文章 [Benefits of using the Azure IoT SDKs, and pitfalls to avoid if you don't](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/)（使用 Azure IoT SDK 的好处，以及未使用时要避免的陷阱）。

## <a name="manage-your-application"></a>管理应用程序

Azure IoT Central 应用程序完全由 Microsoft 托管，这降低了管理应用程序时的管理开销。

操作员使用 Azure IoT Central 应用程序管理 Azure IoT Central 解决方案中的设备。 操作员执行以下任务：

- 监视连接到应用程序的设备。
- 排查和修正设备问题。
- 预配新设备。

作为构建者，你可以定义对连接设备的数据流进行操作的自定义规则和操作。 操作员可以在设备级别启用或禁用这些规则来控制和自动执行应用程序中的任务。

管理员通过[用户角色和权限](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)管理对应用程序的访问权限。

## <a name="next-steps"></a>后续步骤

现在，你已大致了解了 Azure IoT Central，下面是建议的后续步骤：

- 了解 [Azure IoT Central 与 Azure IoT 解决方案加速器](overview-iot-options.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)之间的区别。
- 熟悉 [Azure IoT Central UI](overview-iot-central-tour-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)。
- 通过[创建 Azure IoT Central 应用程序](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)开始使用此解决方案。
- 请按顺序学习以下教程，它们展示了：
  - [构建者如何创建设备模板](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
  - [构建者如何添加规则来自动执行解决方案](tutorial-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
  - [操作员如何监视设备](tutorial-monitor-devices-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
  - [操作员如何向解决方案添加设备](tutorial-add-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
- 详细了解 [IoT 即插即用](https://aka.ms/iot-pnp-docs)
