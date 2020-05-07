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
ms.openlocfilehash: 409ba6cd192f65c73aba6bd6eb11a26ec1f820fa
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82790875"
---
# <a name="what-is-azure-iot-central"></a>什么是 Azure IoT Central？

IoT Central 是一个 IoT 应用程序平台，可减轻开发、管理和维护企业级 IoT 解决方案的负担和成本。 选择使用 IoT Central 进行构建可将时间、金钱和精力集中于使用 IoT 数据转换业务，而不仅仅是维护和更新复杂且不断发展的 IoT 基础结构。

通过 Web UI 可以监视设备条件、创建规则并在其整个生命周期中管理数百万个设备及其数据。 此外，该平台将 IoT 智能扩展到了业务线应用程序，你可以利用该平台根据设备的见解采取行动。

本文概述了与 IoT Central 相关的以下内容：

- 与项目关联的典型角色。
- 如何创建应用程序。
- 如何将设备连接到应用程序。
- 如何管理应用程序。
- IoT Central 中的 Azure IoT Edge 功能。
- 如何将 Azure IoT Edge 运行时支持的设备连接到应用程序。

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

可以快速部署新的 IoT Central 应用程序，然后在浏览器中对其进行自定义以满足特定要求。 一开始可以使用普通的应用程序模板，或使用以[零售](../retail/overview-iot-central-retail.md)、[能源](../energy/overview-iot-central-energy.md)、[政府](../government/overview-iot-central-government.md)或[医疗保健](../healthcare/overview-iot-central-healthcare.md)行业为中心的某个应用程序模板。 

作为解决方案构建者，你可以使用基于 Web 的工具为连接到应用程序的设备创建“设备模板”  。 设备模板是定义一种设备的特征和行为的蓝图，例如：

- 它发送的遥测。
- 操作员可以修改的业务属性。
- 由设备设置的、在应用程序中处于只读状态的设备属性。
- 由操作员设置的属性，决定了设备的行为。

此设备模板包括：

- 设备功能模型  ，描述设备应该实现的功能，例如发送的遥测数据和报告的属性。
- 不存储在设备上的云属性。
- 属于 IoT Central 应用程序的自定义、仪表板和窗体。

### <a name="pricing"></a>定价

可以使用 7 天免费试用版创建 IoT Central 应用程序，也可以使用标准定价计划。

- 使用免费计划创建的应用程序在七天内是免费的，最多支持五台设备  。 在它们过期之前，随时可将其转换为使用标准定价计划。
- 使用标准计划创建的应用程序按设备计费，你可以选择“标准 1”  或“标准 2”  定价计划，前两个设备是免费的  。 在 [Azure IoT Central 定价页](https://azure.microsoft.com/pricing/details/iot-central/)上详细了解免费和标准定价计划。

### <a name="create-device-templates"></a>创建设备模板

IoT Central 可以通过 [IoT 即插即用（预览版）](../../iot-pnp/overview-iot-plug-and-play.md)来集成设备，不需要你编写任何嵌入式设备代码。 IoT 即插即用（预览版）的核心是描述设备功能的设备功能模型架构。 在 IoT Central 应用程序中，设备模板使用这些 IoT 即插即用（预览版）设备功能模型。

作为解决方案构建者，你在创建设备模板时有多种选择：

- 从 [Azure IoT 认证设备目录](https://aka.ms/iotdevcat)导入设备功能模型，然后添加 IoT Central 应用程序所需的任何云属性、自定义和仪表板。
- 在 IoT Central 中设计设备模板，然后在设备代码中实现其设备功能模型。
- 使用 Visual Studio code 创建设备功能模型，并将模型发布到存储库。 基于模型实现设备代码，并将设备连接到 IoT Central 应用程序。 IoT Central 从存储库中查找设备功能模型，并创建简单的设备模板。
- 使用 Visual Studio Code 创建设备功能模型。 基于模型实现设备代码。 将设备功能模型手动导入 IoT Central 应用程序，然后添加 IoT Central 应用程序所需的任何云属性、自定义和仪表板。

作为解决方案构建者，你可以使用 IoT Central 来生成测试设备的代码，以便验证设备模板。

### <a name="customize-the-ui"></a>自定义 UI

作为解决方案构建者，你还可以为负责日常使用应用程序的操作员自定义 IoT Central 应用程序 UI。 解决方案构建者可以进行的自定义包括：

- 在设备模板上定义属性和设置的布局。
- 配置自定义仪表板，以帮助操作员获得见解，并更快地解决问题。
- 配置自定义分析来从已连接的设备探究时序数据。

## <a name="pricing"></a>定价

可以使用 7 天免费试用版创建 IoT Central 应用程序，也可以使用标准定价计划。

- 使用免费计划创建的应用程序在七天内是免费的，最多支持五台设备  。 在它们过期之前，随时可将其转换为使用标准定价计划。
- 使用标准计划创建的应用程序按设备计费，可以选择“标准 1”  或“标准 2”  定价计划，前两个设备是免费的  。 详细了解 [IoT Central 定价](https://aka.ms/iotcentral-pricing)。

## <a name="connect-your-devices"></a>连接数据

Azure IoT Central 使用 [Azure IoT 中心设备预配服务 (DPS)](../../iot-dps/about-iot-dps.md) 来管理所有设备注册和连接。

使用 DPS：

- 可让 IoT Central 支持大规模的设备加入和连接。
- 可让你离线生成设备凭据并配置设备，而无需通过 IoT Central UI 注册设备。
- 可让设备使用共享访问签名建立连接。
- 可让设备使用行业标准的 X.509 证书建立连接。
- 可让你使用自己的设备 ID 在 IoT Central 中注册设备。 使用自己的设备 ID 可以简化与现有后端办公系统的集成。
- 可以通过一致的单一方式将设备连接到 IoT Central。

有关详细信息，请参阅[连接到 Azure IoT Central](./concepts-get-connected.md)。

### <a name="azure-iot-edge-devices"></a>Azure IoT Edge 设备

除了使用 [Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) 创建的设备之外，还可以将 [Azure IoT Edge 设备](../../iot-edge/about-iot-edge.md)连接到 IoT Central 应用程序。 使用 Azure IoT Edge 可以直接在 IoT Central 管理的 IoT 设备上运行云智能和自定义逻辑。 IoT Edge 运行时使你能够：

- 在设备上安装和更新工作负荷。
- 维护设备上的 Azure IoT Edge 安全标准。
- 确保 IoT Edge 模块始终处于运行状态。
- 将模块运行状况报告给云以进行远程监视。
- 管理下游叶设备与 IoT Edge 设备之间、IoT Edge 设备上的模块之间以及 IoT Edge 设备与云之间的通信。

有关详细信息，请参阅 [Azure IoT Edge 设备和 IoT Central](concepts-architecture.md#azure-iot-edge-devices)。

## <a name="stay-connected"></a>保持连接

IoT Central 应用程序完全由 Microsoft 托管，这降低了管理应用程序时的管理开销。

操作员使用 IoT Central 应用程序管理 IoT Central 解决方案中的设备。 操作员执行以下任务：

- 监视连接到应用程序的设备。
- 排查和修正设备问题。
- 预配新设备。

作为解决方案构建者，你可以定义对连接设备的数据流进行操作的自定义规则和操作。 操作员可以在设备级别启用或禁用这些规则来控制和自动执行应用程序中的任务。

管理员通过[用户角色和权限](howto-administer.md)管理对应用程序的访问权限。

对于旨在大规模运行的 IoT 解决方案，以结构化的方式管理设备非常重要。 只是将设备连接到云是不够的，还需要使设备保持连接状态并正常运行。 在整个应用程序生命周期内，操作员可使用以下 IoT Central 功能来管理设备：

### <a name="dashboards"></a>仪表板

内置的[仪表板](./howto-set-up-template.md#generate-default-views)提供可自定义的 UI 用于监视设备运行状况和遥测。 一开始可以使用[应用程序模板](howto-use-app-templates.md)中的预生成仪表板，或创建自己的根据操作员需求定制的仪表板。 可与应用程序中的所有用户共享仪表板，或将其保留为个人专用。

### <a name="rules-and-actions"></a>规则和操作

基于设备状态和遥测生成[自定义规则](tutorial-create-telemetry-rules.md)，以识别需要引以注意的设备。 配置操作来通知适当的人员，并确保及时采取纠正措施。

### <a name="jobs"></a>作业

使用[作业](howto-run-a-job.md)可以通过设置属性或调用命令对设备应用单项更新或批量更新。 

### <a name="user-roles-and-permissions"></a>用户角色和权限

管理员可以通过[角色和权限](howto-manage-users-roles.md)定制每个用户的体验。 管理员使用 Web UI 创建角色和分配权限。

## <a name="transform-your-iot-data"></a>转换 IoT 数据

作为应用程序平台，IoT Central 可让你将 IoT 数据转换为业务见解来促成可行的结果。 例如，可通过[规则](./tutorial-create-telemetry-rules.md)、[数据导出](./howto-export-data.md)和[公共 REST API](https://docs.microsoft.com/learn/modules/manage-iot-central-apps-with-rest-api/) 将 IoT Central 与业务线应用程序相集成：

![IoT Central 如何转换 IoT 数据](media/overview-iot-central/transform.png)

### <a name="monitor-device-health-and-operations-using-rules"></a>使用规则监视设备运行状况和操作

当设备已连接并发送数据时，规则可以识别遇到问题的设备或发送错误消息，使你能够修复这些问题，并尽量减少停机时间。 在 IoT Central 应用程序中生成规则以监视设备发出的遥测数据，并在指标超过阈值或设备发送特定的消息时发出警报。 规则的电子邮件操作和 Webhook 会通知适当的人员和下游系统。

### <a name="run-custom-analytics-and-processing-on-your-exported-data"></a>对导出的数据运行自定义分析和处理

可以生成业务见解（例如，确定机器的效率趋势，或预测车间的未来能耗），方法是生成自定义的分析管道来处理设备发出的遥测数据并存储结果。 在 IoT Central 应用程序中配置数据导出，以将遥测数据、设备属性更改和设备模板更改导出到其他服务，在其中可以使用偏好的工具分析、存储和可视化数据。

### <a name="build-custom-iot-solutions-and-integrations-with-the-rest-apis"></a>使用 REST API 生成自定义 IoT 解决方案和集成

生成 IoT 解决方案，例如：

- 可远程设置和控制设备的移动伴侣应用。
- 可使现有业务线应用程序能够与 IoT 设备和数据交互的自定义集成。
- 用于设备建模、加入、管理和数据访问的设备管理应用程序。

## <a name="quotas"></a>配额

每个 Azure 订阅具有默认的配额，这些配额可能影响 IoT 解决方案的范围。 目前，IoT Central 将可在单个订阅中部署的应用程序数限制为 10 个。 如果需要增加此限制，请联系 [Microsoft 支持](https://azure.microsoft.com/support/options/)。

## <a name="known-issues"></a>已知问题

- 连续数据导出不支持 Avro 格式（不兼容）。
- 目前不支持 GeoJSON。
- 目前不支持地图图块。
- 不支持数组架构类型。
- 仅支持 C 设备 SDK 以及 Node.js 设备和服务 SDK。
- IoT Central 目前可在美国、欧洲、亚太、澳大利亚、英国和日本位置中使用。
- 不能在英国和日本位置使用“自定义应用程序(旧版)”  应用程序模板。
- 必须在同一文件中以内联方式定义设备功能模型的所有接口。
- 对 [IoT 即插即用](../../iot-pnp/overview-iot-plug-and-play.md)的支持处于预览状态，并且仅在选定区域受支持。

## <a name="next-steps"></a>后续步骤

现在，你已大致了解了 IoT Central，下面是一些建议的后续步骤：

- 了解可用的[用于创建 IoT 解决方案的 Azure 技术和服务](../../iot-fundamentals/iot-services-and-technologies.md)。
- 熟悉 [Azure IoT Central UI](overview-iot-central-tour.md)。
- 通过[创建 Azure IoT Central 应用程序](quick-deploy-iot-central.md)开始使用此解决方案。
- 详细了解 [IoT 即插即用（预览版）](../../iot-pnp/overview-iot-plug-and-play.md)。
- 了解如何[连接 Azure IoT Edge 设备](./tutorial-add-edge-as-leaf-device.md)。
- 详细了解 [Azure IoT 技术和服务](../../iot-fundamentals/iot-services-and-technologies.md)。

如果你是设备开发人员并想深入了解某种代码，建议执行的下一步骤是[创建客户端应用程序并将其连接到 Azure IoT Central 应用程序](./tutorial-connect-device-nodejs.md)。
