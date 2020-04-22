---
title: 术语表 - IoT 即插即用预览 |微软文档
description: 概念 - 与 IoT 即插即用预览相关的常用术语的词汇表。
author: Philmea
ms.author: philmea
ms.date: 12/23/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: f0c21626c664f2d72b534ebae7f0a257620be07d
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767074"
---
# <a name="glossary-of-terms-for-iot-plug-and-play-preview"></a>IoT 即插即用预览的术语表

IoT 即插即用文章中使用的常见术语的定义。

## <a name="azure-certified-for-iot-portal"></a>为 IoT 门户认证的 Azure

您可以使用 Azure[认证的 IoT 门户网站](https://aka.ms/ACFI)：

- 完成[IoT 即插即用设备的](#iot-plug-and-play-device)[认证过程](#device-certification)。
- 查找[设备功能模型](#device-capability-model)。
- 将设备功能模型发布到[公共模型存储库](#public-model-repository)。

## <a name="azure-cli"></a>Azure CLI

Azure CLI 是一种跨平台的命令行工具，用于管理 Azure 资源。 Azure CLI 的 Azure IoT 扩展是一个命令行工具，用于与[IoT 即插即用设备](#iot-plug-and-play-device)进行交互和测试。 您可以使用扩展：

- 连接到 IoT 即插即用设备。
- 查看设备发送的[遥测](#telemetry)数据。
- 使用设备[属性](#properties)。
- 调用设备[命令](#commands)。
- 管理[模型存储库](#model-repository)、[接口](#interface)[和设备功能模型](#device-capability-model)。

## <a name="azure-iot-central"></a>Azure IoT Central

Azure IoT 中心是一个完全托管的软件即服务解决方案，可轻松连接、监视和管理[IoT 即插即用设备](#iot-plug-and-play-device)。 您可以使用[设备功能模型](#device-capability-model)自动配置 IoT 中心应用程序来监视和管理您的设备。

## <a name="azure-iot-certification-service"></a>Azure IoT 认证服务

当您通过[IoT 门户](#azure-certified-for-iot-portal)提交[IoT 即插即用设备](#iot-plug-and-play-device)进行认证时，Azure IoT 认证服务将运行一组认证测试。 在将设备添加到[IoT 认证设备目录中](#certified-for-iot-device-catalog)之前，设备必须经过认证。

## <a name="azure-iot-tools-extension"></a>Azure IoT 工具扩展

Azure IoT 工具是 Visual Studio[代码](#visual-studio-code)中的扩展的集合，可帮助您与 IoT 中心进行交互并开发 IoT 设备。 对于 IoT 即插即用设备开发，它可以帮助您：

- 编写[设备功能模型](#device-capability-model)和[接口](#interface)。
- 发布到[模型存储库](#model-repository)。
- 生成骨架代码以实现设备应用程序。

## <a name="azure-iot-explorer-tool"></a>Azure IoT 资源管理器工具

Azure IoT 资源管理器是一种图形工具，可用于与[IoT 即插即用设备](#iot-plug-and-play-device)进行交互和测试。 在本地计算机上安装该工具后，可以使用它：

- 查看连接到[IoT 中心的设备](#azure-iot-hub)。
- 连接到 IoT 即插即用设备。
- 查看设备发送的[遥测](#telemetry)数据。
- 使用设备[属性](#properties)。
- 调用设备[命令](#commands)。

## <a name="azure-iot-hub"></a>Azure IoT 中心

IoT 中心托管服务在云中进行托管，充当中央消息中心，用于 IoT 应用程序与其管理的设备之间的双向通信。 [IoT 即插即用设备](#iot-plug-and-play-device)可以连接到 IoT 中心。 IoT 解决方案使用 IoT 中心启用：

- 将遥测发送到基于云的解决方案的设备。
- 基于云的解决方案，用于管理连接的设备。

## <a name="azure-iot-device-sdk"></a>Azure IoT 设备 SDK

有多种语言的设备 SDK 可用于构建 IoT 即插即用设备客户端应用程序。 [设备认证](#device-certification)的要求之一是设备客户端代码使用 Azure IoT 设备 SDK 之一。

## <a name="certified-for-iot-device-catalog"></a>IoT 设备目录认证

[IoT 认证设备目录](https://catalog.azureiotsolutions.com/)列出了已通过[设备认证](#device-certification)测试的[IoT 即插即用设备](#iot-plug-and-play-device)。 目录中的 IoT 即插即用设备[的设备功能模型](#device-capability-model)，并在公共模型存储库中发布。

## <a name="commands"></a>命令

在[接口](#interface)中定义的命令表示可以在[数字孪生](#digital-twin)上执行的方法。 例如，重新启动设备的命令。

## <a name="common-interface"></a>通用接口

所有[物联网即插即用设备](#iot-plug-and-play-device)都有望实现一些通用[接口](#interface)。 例如，设备信息接口定义有关设备的硬件和操作系统信息。 [设备认证](#device-certification)要求您的设备实现多个通用接口。 可以从公共模型存储库检索通用接口定义。

## <a name="company-model-repository"></a>公司模型存储库

组织可以使用公司[模型存储库](#model-repository)作为[设备功能模型](#device-capability-model)和[接口](#interface)的专用存储。

## <a name="connection-string"></a>连接字符串

连接字符串封装连接到终结点所需的信息。 连接字符串通常包含终结点的地址和安全信息，但连接字符串的格式因服务而异。 与 IoT 中心服务关联的连接字符串有两种：

- 设备连接字符串使[IoT 即插即用设备](#iot-plug-and-play-device)能够连接到 IoT 中心上面向设备的终结点。 设备上的客户端代码使用连接字符串与 IoT 中心建立安全连接。
- IoT 中心连接字符串支持后端解决方案和工具，以安全地连接到 IoT 中心上面向服务的端点。 这些解决方案和工具管理 IoT 中心及其连接的设备。
- 公司模型存储库连接字符串支持后端解决方案和工具安全地连接到[公司模型存储库](#company-model-repository)。 这些解决方案和工具使用或管理存储库中的[设备功能模型](#device-capability-model)和[接口](#interface)。

## <a name="device-capability-model"></a>设备功能模型

设备功能模型描述[IoT 即插即用设备](#iot-plug-and-play-device)，并定义设备实现的[接口](#interface)集。 设备功能模型通常对应于物理设备、产品或 SKU。 您可以使用[数字双定义语言](#digital-twin-definition-language)定义设备功能模型。

## <a name="device-certification"></a>设备认证

设备认证是验证[IoT 即插即用设备](#iot-plug-and-play-device)的过程，以便将其添加到[IoT 设备认证目录](#certified-for-iot-device-catalog)及其[设备功能模型](#device-capability-model)和添加到[公共模型存储库](#public-model-repository)中的[接口](#interface)。

## <a name="device-developer"></a>设备开发人员

设备开发人员使用[设备功能模型](#device-capability-model)、[接口](#interface)和 Azure [IoT 设备 SDK](#azure-iot-device-sdk)来实现在[IoT 即插即用设备上](#iot-plug-and-play-device)运行的代码。

## <a name="device-modeling"></a>设备建模

[设备开发人员](#device-developer)使用[数字双定义语言](#digital-twin-definition-language)对[IoT 即插即用设备的](#iot-plug-and-play-device)功能进行建模。 可以使用模型存储库共享模型。 设备开发人员可以从模型生成骨架设备代码。 [解决方案开发人员](#solution-developer)可以从模型中配置 IoT 解决方案。

## <a name="device-provisioning-service"></a>设备预配服务

[Azure IoT 中心](#azure-iot-central)使用设备预配服务来管理所有设备注册和连接。 有关详细信息，请参阅[Azure IoT 中心 中的设备连接](../iot-central/core/concepts-get-connected.md)。 您还可以使用设备配置服务来管理设备注册和连接到基于 IoT 集线器的 IoT 解决方案。 有关详细信息，请参阅使用[Azure IoT 中心设备预配服务预配设备](../iot-dps/about-iot-dps.md)。

## <a name="device-registration"></a>设备注册

在[IoT 即插即用设备](#iot-plug-and-play-device)连接到 IoT 解决方案之前，必须将其注册到解决方案。 [Azure IoT 中心](#azure-iot-central)使用[设备预配服务](#device-provisioning-service)来管理设备注册。 在自定义 IoT 解决方案中，可以在 Azure 门户中或以编程方式向 IoT 中心注册设备。

## <a name="device-first"></a>设备优先

[Azure IoT 中心](#azure-iot-central)支持设备优先注册和连接方案。 在这种情况下[，IoT 即插即用设备](#iot-plug-and-play-device)可以连接到 IoT 中央应用程序，而无需提前注册。 当设备首次连接到应用程序时，将自动进行注册。

## <a name="digital-twin"></a>数字孪生

数字孪生是[物联网即插即用设备的](#iot-plug-and-play-device)模型。 数字孪生使用[数字双定义语言](#digital-twin-definition-language)进行建模。 您可以使用 Azure [IoT 设备 SDK](#azure-iot-device-sdk)在运行时与数字孪生进行交互。 例如，您可以在设备上的数字孪生中设置属性值，SDK 会将此更改传达给云中的 IoT 解决方案。

## <a name="digital-twin-change-events"></a>数字双更改事件

当[IoT 即插即用设备](#iot-plug-and-play-device)连接到[IoT 中心](#azure-iot-hub)时，集线器可以使用其路由功能发送数字孪生更改通知。 例如，每当[属性](#properties)值在设备上发生更改时，IoT 中心都可以向终结点（如服务总线队列）发送通知。

## <a name="digital-twin-definition-language"></a>数字双定义语言

一种用于描述[IoT 即插即用设备的模型和接口的语言](#iot-plug-and-play-device)。 使用[数字双定义语言](https://aka.ms/DTDL)描述[数字孪生的功能](#digital-twin)，并使 IoT 平台和 IoT 解决方案能够利用实体的语义。

## <a name="digital-twin-route"></a>数字双线

在[IoT 中心](#azure-iot-hub)中设置的路线，用于向和终结点（如服务总线队列）提供[数字孪生更改事件](#digital-twin-change-events)。

## <a name="interface"></a>接口

接口描述由[IoT 即插即用设备](#iot-plug-and-play-device)或[数字孪生](#digital-twin)实现的相关功能。 您可以在不同的[设备功能模型中](#device-capability-model)重用接口。

## <a name="iot-hub-query-language"></a>IoT 中心查询语言

IoT 中心查询语言用于多种用途。 例如，可以使用该语言搜索在 IoT 中心[注册的设备](#device-registration)或优化[数字双路由](#digital-twin-route)行为。

## <a name="iot-plug-and-play-device"></a>物联网即插即用设备

IoT 即插即用设备通常是一种小规模的独立计算设备，用于收集数据或控制其他设备，并运行实现[设备功能模型](#device-capability-model)的软件或固件。  例如，IoT 即插即用设备可能是环境监测设备，也可以是智能农业灌溉系统的控制器。 您可以编写云托管 IoT 解决方案来命令、控制和接收来自 IoT 即插即用设备的数据。 [适用于 IoT 设备的 Azure 认证目录](#certified-for-iot-device-catalog)列表列出了可用的 IoT 即插即用设备。 目录中的每个 IoT 即插即用设备都已过验证，并且具有[设备功能模型](#device-capability-model)。

## <a name="microsoft-partner-center"></a>Microsoft 合作伙伴中心

[Microsoft 合作伙伴中心](https://docs.microsoft.com/partner-center/)是您的组织管理其与 Microsoft 的端到端关系的地方。 您需要一个 Microsoft 合作伙伴中心帐户，然后才能在[Azure 认证 IoT 门户](#azure-certified-for-iot-portal)中验证[IoT 即插即用设备](#iot-plug-and-play-device)。

## <a name="model-discovery"></a>模型发现

当[IoT 即插即用设备](#iot-plug-and-play-device)连接到 IoT 解决方案时，该解决方案可以通过查找[设备功能模型来发现设备的功能](#device-capability-model)。 设备可以将其功能模型发送到解决方案，或者解决方案可以在[模型存储库](#model-repository)中找到设备功能模型。

## <a name="model-repository"></a>模型存储库

模型存储库存储[设备功能模型](#device-capability-model)和[接口](#interface)。 有一个[公共模型存储库](#public-model-repository)。 组织可以创建自己的组织模型存储库。

## <a name="model-repository-rest-api"></a>模型存储库 REST API

用于管理和与模型存储库交互的 API。 例如，可以使用 API 添加[设备功能模型](#device-capability-model)并搜索功能模型。

## <a name="properties"></a>属性

属性是在表示数字孪生的某些状态的[接口](#interface)中定义的数据字段。 您可以将属性声明为只读或可写。 只读属性（如序列号）由在[IoT 即插即用设备上](#iot-plug-and-play-device)运行的代码设置。  可写属性（如报警阈值）通常从基于云的 IoT 解决方案设置。

## <a name="public-model-repository"></a>公共模型存储库

有一个公共模型存储库，用于存储[设备功能模型](#device-capability-model)和[认证设备的](#device-certification)[接口](#interface)。 公共模型存储库还存储[通用接口](#common-interface)定义。

## <a name="registration-id"></a>注册 ID

注册 ID 唯一标识[设备预配服务](#device-provisioning-service)中的设备。 此 ID 与[IoT 中心](#azure-iot-hub)中设备的唯一标识符的设备 ID 不同。

## <a name="scope-id"></a>作用域 ID

范围 ID 范围唯一标识[设备预配服务](#device-provisioning-service)实例。

## <a name="shared-access-signature"></a>共享访问签名

共享访问签名是基于 SHA-256 安全哈希或 URI 的身份验证机制。 共享访问签名身份验证有两个组件：共享访问策略和共享访问签名（通常称为令牌）。 [IoT 即插即用设备](#iot-plug-and-play-device)使用共享访问签名使用[IoT 中心](#azure-iot-hub)进行身份验证。

## <a name="solution-developer"></a>解决方案开发人员

解决方案开发人员创建解决方案后端。 解决方案开发人员通常使用 Azure 资源（如[IoT 中心](#azure-iot-hub)和[模型存储库](#model-repository)）或使用[IoT 中心](#azure-iot-central)。

## <a name="telemetry"></a>遥测

接口中定义的遥测[字段表示度量](#interface)值。 这些测量通常是[IoT 即插即用设备](#iot-plug-and-play-device)作为数据流发送的传感器读数等值。

## <a name="visual-studio-code"></a>视觉工作室代码

Visual Studio 代码是一种现代代码编辑器，可用于多个平台。 扩展（如[Azure IoT 工具](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)包中的扩展）使您能够自定义编辑器以支持各种开发方案。
