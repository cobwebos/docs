---
title: 什么是 Azure IoT Central | Microsoft Docs
description: Azure IoT Central 是一个端到端 SaaS 解决方案，可以用来构建和管理自定义 IoT 解决方案。 本文概述了 Azure IoT Central 的功能。
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: overview
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 41eb9049b45c6835dd866c988af486a4975c4831
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
ms.locfileid: "34201160"
---
<!---
Purpose of an Overview article: 
1. To give a TECHNICAL overview of a service/product: What is it? Why should I use it? It's a "learn" topic that describes key benefits and our competitive advantage. It's not a "do" topic.
2. To help audiences who are new to service but who may be familiar with related concepts. 
3. To compare the service to another service/product that has some similar functionality, ex. SQL Database / SQL Data Warehouse, if appropriate. This info can be in a short list or table. 
-->

# <a name="what-is-azure-iot-central"></a>什么是 Azure IoT Central？

Microsoft Azure IoT Central 是一个完全托管的 IoT 软件即服务解决方案，可用来轻松创建对物理和电子世界进行连接的产品。 可以通过以下方式实现有关已连接产品的远景：

- 通过已连接的设备提供新见解，来为客户提供更好的产品和体验。
- 为组织创造新的业务机会。

与典型的 IoT 项目相比，Azure IoT Central 解决了对 IoT 解决方案进行管理的难题，具体体现在以下方面：

- 降低了管理负担。
- 降低了运营成本和开销。
- 可以轻松自定义应用程序，同时利用：
  - 行业领先的技术，例如 [Azure IoT 中心](https://azure.microsoft.com/services/iot-hub/)和 [Azure 时序见解](https://azure.microsoft.com/services/time-series-insights/)。
  - 企业级安全功能，例如端到端加密。

下面的视频概述了 Azure IoT Central：

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Microsoft-IoT-Central-intro-walkthrough/Player]

本文的剩余部分概述了与 Azure IoT Central 相关的以下内容：

- 与项目关联的典型角色。
- 如何创建应用程序。
- 如何将设备连接到应用程序。
- 如何管理应用程序。

## <a name="personas"></a>角色

本 Azure IoT Central 文档提到了四个与 Azure IoT Central 应用程序进行交互的典型角色：

- “构建者”负责定义可以连接到应用程序的设备类型，并为操作员自定义应用程序。
- “操作员”管理连接到应用程序的设备。
- “管理员”负责执行管理任务，例如，管理应用程序中的用户和角色。
- “设备开发者”创建在连接到应用程序的设备上运行的代码。

## <a name="create-your-azure-iot-central-application"></a>创建 Azure IoT Central 应用程序

构建者使用 Azure IoT Central 为组织创建自定义的、托管在云中的 IoT 解决方案。 自定义的 IoT 解决方案通常包括：

- 一个基于云的应用程序，它从设备接收遥测数据并使得你可以管理这些设备。
- 运行自定义代码且连接到基于云的应用程序的多个设备。

可以快速部署新的 Azure IoT Central 应用程序，然后在浏览器中对其进行自定义以满足特定要求。 Azure IoT Central 构建者可以使用基于 Web 的工具为连接到应用程序的设备创建“设备模板”。 设备模板是基于该设备模板创建的所有设备共享的设备模型的蓝图。 设备模板定义一种设备的特征和行为，例如：

- 它发送的遥测数据。
- 操作员可以修改的业务属性。
- 由设备设置的、在应用程序中处于只读状态的设备属性。
- 应用程序对其做出响应的阈值。
- 决定了设备行为的设置。

可以立即使用 Azure IoT Central 生成的模拟数据测试设备模板和应用程序。

构建者还可以为负责日常使用应用程序的操作员自定义 Azure IoT Central 应用程序 UI。 构建者可以进行的自定义包括：

- 在设备模板上定义属性和设置的布局。
- 配置自定义仪表板，以帮助操作员获得见解，并更快地解决问题。
- 配置自定义分析来从已连接的设备探究时序数据。

## <a name="connect-your-devices"></a>连接设备

在构建者定义可以连接到应用程序的设备类型后，设备开发者创建要在设备上运行的代码。 设备开发者可以使用 Microsoft 的开源 [Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) 来创建设备代码。 这些 SDK 提供了广泛的语言、平台和协议支持来满足将设备连接到 Azure IoT Central 应用程序时的需求。 这些 SDK 可帮助在连接到 Azure IoT Central 的设备上执行以下任务：

- 创建安全连接。
- 发送遥测数据。
- 报告状态。
- 接收配置更新。

有关详细信息，请参阅博客文章 [Benefits of using the Azure IoT SDKs, and pitfalls to avoid if you don't](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/)（使用 Azure IoT SDK 的好处，以及未使用时要避免的陷阱）。

## <a name="manage-your-application"></a>管理应用程序

Azure IoT Central 应用程序完全由 Microsoft 托管，这降低了管理应用程序时的管理开销。

操作员使用 Azure IoT Central 应用程序管理 Azure IoT Central 解决方案中的设备。 操作员可以执行如下所述的任务：

- 监视连接到应用程序的设备。
- 排查和修正设备问题。
- 预配新设备。

构建者可以在设备模板级别定义针对流数据进行操作的自定义规则和操作。 操作员可以在设备级别启用或禁用这些规则来控制和自动执行应用程序中的任务。

管理员可以通过[用户角色和权限](howto-administer.md)管理对应用程序的访问权限。

## <a name="next-steps"></a>后续步骤

现在，你已大致了解了 Azure IoT Central，下面是建议的后续步骤：

- 了解 [Azure IoT Central 与 Azure IoT 套件](overview-iot-options.md)之间的区别。
- 熟悉 [Azure IoT Central UI](overview-iot-central-tour.md)。
- 通过[创建 Azure IoT Central 应用程序](quick-deploy-iot-central.md)开始使用此解决方案。
- 请按顺序学习以下教程，它们展示了：
  - [构建者如何创建设备模板](tutorial-define-device-type.md)
  - [构建者如何添加规则来自动执行解决方案](tutorial-configure-rules.md)
  - [构建者如何为操作员自定义应用程序](tutorial-customize-operator.md)
  - [操作员如何监视设备](tutorial-monitor-devices.md)
  - [操作员如何向解决方案中添加真实设备](tutorial-add-device.md)
  - [设备开发者如何为设备创建代码](tutorial-add-device.md#prepare-the-client-code)
