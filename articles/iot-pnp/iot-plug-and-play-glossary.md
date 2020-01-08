---
title: 术语词汇表-IoT 即插即用预览版 |Microsoft Docs
description: 概念-与 IoT 即插即用预览相关的常见术语的词汇表。
author: ChrisGMsft
ms.author: chrisgre
ms.date: 12/23/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: b6516b07f24c40fcb95d47e05b22dd7672c10f2a
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/28/2019
ms.locfileid: "75531195"
---
# <a name="glossary-of-terms-for-iot-plug-and-play-preview"></a>IoT 即插即用预览版术语表

IoT 即插即用文章中使用的常用术语的定义。

## <a name="azure-certified-for-iot-portal"></a>Azure IoT 认证门户

你可以使用[Azure IoT 认证门户](https://aka.ms/ACFI)网站来执行以下操作：

- 完成[IoT 即插即用设备](#iot-plug-and-play-device)的[认证过程](#device-certification)。
- 查找[设备功能模型](#device-capability-model)。
- 将设备功能模型发布到[公共模型存储库](#public-model-repository)。

## <a name="azure-cli"></a>Azure CLI

Azure CLI 是一种跨平台的命令行工具，用于管理 Azure 资源。 适用于 Azure CLI 的 Azure IoT 扩展是一个命令行工具，用于与测试[iot 即插即用设备](#iot-plug-and-play-device)进行交互。 你可以使用扩展来执行以下操作：

- 连接到 IoT 即插即用设备。
- 查看设备发送的[遥测](#telemetry)数据。
- 使用设备[属性](#properties)。
- 调用设备[命令](#commands)。
- 管理[模型存储库](#model-repository)、[接口](#interface)和[设备功能模型](#device-capability-model)。

## <a name="azure-iot-central"></a>Azure IoT 中心

Azure IoT Central 是一种完全托管的软件即服务解决方案，可让你轻松地连接、监视和管理[IoT 即插即用设备](#iot-plug-and-play-device)。 你可以使用[设备功能模型](#device-capability-model)自动配置 IoT Central 应用程序，以监视和管理你的设备。

## <a name="azure-iot-certification-service"></a>Azure IoT 认证服务

当你通过[Azure IoT 认证门户](#azure-certified-for-iot-portal)提交[IoT 即插即用设备](#iot-plug-and-play-device)进行认证时，Azure IoT 认证服务将运行一组认证测试。 必须先认证设备，然后才能将设备添加到[IoT 认证设备目录](#certified-for-iot-device-catalog)。

## <a name="azure-iot-tools-extension"></a>Azure IoT 工具扩展

Azure IoT 工具是[Visual Studio code](#visual-studio-code)中的扩展的集合，可帮助你与 iot 中心进行交互并开发 iot 设备。 对于 IoT 即插即用设备开发，它可帮助你：

- 创作[设备功能模型](#device-capability-model)和[接口](#interface)。
- 发布到[模型存储库](#model-repository)。
- 生成用于实现设备应用程序的主干代码。

## <a name="azure-iot-explorer-tool"></a>Azure IoT 资源管理器工具

Azure IoT 浏览器是一种图形工具，可用于与[即插即用设备](#iot-plug-and-play-device)交互和测试 IoT。 在本地计算机上安装该工具后，可以使用它来执行以下操作：

- 查看连接到[IoT 中心](#azure-iot-hub)的设备。
- 连接到 IoT 即插即用设备。
- 查看设备发送的[遥测](#telemetry)数据。
- 使用设备[属性](#properties)。
- 调用设备[命令](#commands)。

## <a name="azure-iot-hub"></a>Azure IoT 中心

IoT 中心托管服务在云中进行托管，充当中央消息中心，用于 IoT 应用程序与其管理的设备之间的双向通信。 [Iot 即插即用设备](#iot-plug-and-play-device)可以连接到 iot 中心。 IoT 解决方案使用 IoT 中心来启用：

- 用于将遥测发送到基于云的解决方案的设备。
- 用于管理连接的设备的基于云的解决方案。

## <a name="azure-iot-device-sdk"></a>Azure IoT 设备 SDK

有多种语言的设备 Sdk 可用于构建 IoT 即插即用设备客户端应用程序。 [设备认证](#device-certification)的要求之一是设备客户端代码使用 Azure IoT 设备 sdk 之一。

## <a name="certified-for-iot-device-catalog"></a>IoT 认证设备目录

[IoT 认证设备目录](https://catalog.azureiotsolutions.com/)列出了已通过[设备认证](#device-certification)测试[即插即用设备的 IoT](#iot-plug-and-play-device) 。 IoT 即插即用设备的[设备功能模型](#device-capability-model)，并在公共模型存储库中发布。

## <a name="commands"></a>命令

在[接口](#interface)中定义的命令表示可以在[数字](#digital-twin)克隆上执行的方法。 例如，用于重新启动设备的命令。

## <a name="common-interface"></a>公共接口

所有[IoT 即插即用设备](#iot-plug-and-play-device)都应该实现一些公共[接口](#interface)。 例如，设备信息接口定义有关设备的硬件和操作系统信息。 [设备证书](#device-certification)要求设备实现多个通用接口。 你可以从公共模型存储库检索公共接口定义。

## <a name="company-model-repository"></a>公司模型存储库

组织可以使用公司[模型存储库](#model-repository)作为[设备功能模型](#device-capability-model)和[接口](#interface)的专用存储。

## <a name="connection-string"></a>连接字符串

连接字符串封装连接到终结点所需的信息。 连接字符串通常包含终结点的地址和安全信息，但连接字符串的格式因服务而异。 与 IoT 中心服务关联的连接字符串有两种：

- 设备连接字符串使[iot 即插即用设备](#iot-plug-and-play-device)能够连接到 iot 中心上面向设备的终结点。 设备上的客户端代码使用连接字符串建立与 IoT 中心的安全连接。
- IoT 中心连接字符串使后端解决方案和工具能够安全地连接到 IoT 中心上面向服务的终结点。 这些解决方案和工具管理 IoT 中心和连接到它的设备。
- 公司模型存储库连接字符串使后端解决方案和工具能够安全地连接到[公司模型存储库](#company-model-repository)。 这些解决方案和工具使用或管理存储库中的[设备功能模型](#device-capability-model)和[接口](#interface)。

## <a name="device-capability-model"></a>设备功能模型

设备功能模型描述[IoT 即插即用设备](#iot-plug-and-play-device)，并定义设备实现的一组[接口](#interface)。 设备功能模型通常对应于物理设备、产品或 SKU。 使用数字克隆[定义语言](#digital-twin-definition-language)来定义设备功能模型。

## <a name="device-certification"></a>设备认证

设备证书是验证[IoT 即插即用设备](#iot-plug-and-play-device)的过程，以便可以将其添加到[IoT 认证设备目录](#certified-for-iot-device-catalog)及其[设备功能模型](#device-capability-model)和添加到[公共模型存储库](#public-model-repository)的[接口](#interface)。

## <a name="device-developer"></a>设备开发人员

设备开发人员使用[设备功能模型](#device-capability-model)、[接口](#interface)和[Azure IoT 设备 SDK](#azure-iot-device-sdk)来实现在[IoT 即插即用设备](#iot-plug-and-play-device)上运行的代码。

## <a name="device-modeling"></a>设备建模

[设备开发人员](#device-developer)使用[数字克隆定义语言](#digital-twin-definition-language)来模拟[IoT 即插即用设备](#iot-plug-and-play-device)的功能。 可以使用模型存储库共享模型。 设备开发人员可以从模型中生成主干设备代码。 [解决方案开发人员](#solution-developer)可以从模型配置 IoT 解决方案。

## <a name="device-provisioning-service"></a>设备预配服务

[Azure IoT Central](#azure-iot-central)使用设备预配服务来管理所有设备注册和连接。 有关详细信息，请参阅[Azure IoT Central 中的设备连接](../iot-central/preview/overview-iot-central-get-connected.md)。 你还可以使用设备预配服务管理设备注册并连接到基于 IoT 中心的 IoT 解决方案。 有关详细信息，请参阅[通过 Azure IoT 中心设备预配服务预配设备](../iot-dps/about-iot-dps.md)。

## <a name="device-registration"></a>设备注册

在[iot 即插即用设备](#iot-plug-and-play-device)可以连接到 iot 解决方案之前，必须向解决方案注册该解决方案。 [Azure IoT Central](#azure-iot-central)使用[设备预配服务](#device-provisioning-service)管理设备注册。 在自定义 IoT 解决方案中，你可以在 Azure 门户中或以编程方式向 IoT 中心注册设备。

## <a name="device-first"></a>设备-第一

[Azure IoT Central](#azure-iot-central)支持设备首次注册和连接方案。 在这种情况下， [IoT 即插即用设备](#iot-plug-and-play-device)无需提前注册，即可连接到 IoT Central 应用程序。 设备首次连接到应用程序时，会自动进行注册。

## <a name="digital-twin"></a>数字输出

数字克隆是[IoT 即插即用设备](#iot-plug-and-play-device)的型号。 数字克隆使用[数字克隆定义语言](#digital-twin-definition-language)进行建模。 可以在运行时使用[Azure IoT 设备 sdk](#azure-iot-device-sdk)与数字孪生交互。 例如，可以在设备上的数字克隆中设置属性值，SDK 会将此更改传递到云中的 IoT 解决方案。

## <a name="digital-twin-change-events"></a>数字克隆更改事件

当[iot 即插即用设备](#iot-plug-and-play-device)连接到[iot 中心](#azure-iot-hub)时，中心可以使用其路由功能来发送数字克隆更改的通知。 例如，当设备上的[属性](#properties)值发生更改时，IoT 中心可以将通知发送到终结点，例如服务总线队列。

## <a name="digital-twin-definition-language"></a>数字克隆定义语言

用于描述[IoT 即插即用设备](#iot-plug-and-play-device)的模型和接口的一种语言。 使用[数字克隆定义语言](https://aka.ms/DTDL)描述[数字克隆的](#digital-twin)功能，并使 iot 平台和 iot 解决方案能够利用实体的语义。

## <a name="digital-twin-route"></a>数字克隆路由

在[IoT 中心](#azure-iot-hub)内设置的路由，可将[数字非整数变化事件](#digital-twin-change-events)传递给和终结点，例如服务总线队列。

## <a name="interface"></a>接口

接口描述由[IoT 即插即用设备](#iot-plug-and-play-device)或[数字](#digital-twin)克隆实现的相关功能。 可以跨不同的[设备功能模型](#device-capability-model)重复使用接口。

## <a name="iot-hub-query-language"></a>IoT 中心查询语言

IoT 中心查询语言用于多种目的。 例如，你可以使用语言搜索注册到 IoT 中心的[设备](#device-registration)，或优化[数字克隆路由](#digital-twin-route)行为。

## <a name="iot-plug-and-play-device"></a>IoT 即插即用设备

IoT 即插即用设备通常是一种小型的独立计算设备，用于收集数据或控制其他设备，并运行实现[设备功能模型](#device-capability-model)的软件或固件。  例如，IoT 即插即用设备可以是环境监视设备，也可以是智能设备灌溉灌溉系统的控制器。 可以编写云托管的 IoT 解决方案来命令、控制和接收 IoT 即插即用设备中的数据。 [Azure IoT 认证设备目录](#certified-for-iot-device-catalog)列出可用的 IoT 即插即用设备。 目录中的每个 IoT 即插即用设备均已验证，且具有[设备功能模型](#device-capability-model)。

## <a name="microsoft-partner-center"></a>Microsoft 合作伙伴中心

[Microsoft 合作伙伴中心](https://docs.microsoft.com/partner-center/)是组织管理其与 Microsoft 的端到端关系的地方。 你需要一个 Microsoft 合作伙伴中心帐户，然后才能在[Azure IoT 认证门户](#azure-certified-for-iot-portal)中验证[IoT 即插即用设备](#iot-plug-and-play-device)。

## <a name="model-discovery"></a>模型发现

当[iot 即插即用设备](#iot-plug-and-play-device)连接到 iot 解决方案时，该解决方案可以通过查找[设备功能模型](#device-capability-model)来发现设备的功能。 设备可以将其功能模型发送到解决方案，也可以在[模型存储库](#model-repository)中找到设备功能模型。

## <a name="model-repository"></a>模型存储库

模型存储库存储[设备功能模型](#device-capability-model)和[接口](#interface)。 有一个[公共模型存储库](#public-model-repository)。 组织可以创建自己的组织模型存储库。

## <a name="model-repository-rest-api"></a>模型存储库 REST API

用于管理模型存储库和与之交互的 API。 例如，你可以使用 API 来添加[设备功能模型](#device-capability-model)和搜索功能模型。

## <a name="properties"></a>属性

属性是在[接口](#interface)中定义的数据字段，表示数字克隆的某种状态。 可以将属性声明为只读或可写。 只读属性（如序列号）由[IoT 即插即用设备](#iot-plug-and-play-device)上运行的代码进行设置。  可写属性（如警报阈值）通常是基于云的 IoT 解决方案设置的。

## <a name="public-model-repository"></a>公共模型存储库

存在单个公共模型存储库，用于存储已[认证设备](#device-certification)的[设备功能模型](#device-capability-model)和[接口](#interface)。 公共模型存储库还存储[公共接口](#common-interface)定义。

## <a name="registration-id"></a>注册 ID

注册 ID 在[设备预配服务](#device-provisioning-service)中唯一标识设备。 此 ID 与设备 ID 不同，后者是[IoT 中心](#azure-iot-hub)中设备的唯一标识符。

## <a name="scope-id"></a>作用域 ID

作用域 ID 作用域唯一标识[设备预配服务](#device-provisioning-service)实例。

## <a name="shared-access-signature"></a>共享访问签名

共享访问签名是基于 SHA-256 安全哈希或 URI 的身份验证机制。 共享访问签名身份验证有两个组件：共享访问策略和共享访问签名（通常称为令牌）。 [Iot 即插即用设备](#iot-plug-and-play-device)使用共享访问签名在[iot 中心](#azure-iot-hub)进行身份验证。

## <a name="solution-developer"></a>解决方案开发人员

解决方案开发人员创建解决方案后端。 解决方案开发人员通常适用于 Azure 资源，如[IoT 中心](#azure-iot-hub)和[模型存储库](#model-repository)，或与[IoT Central](#azure-iot-central)配合使用。

## <a name="telemetry"></a>遥测

在[接口](#interface)中定义的遥测字段表示度量值。 这些测量值通常是[IoT 即插即用设备](#iot-plug-and-play-device)发送的传感器读数等值作为数据流。

## <a name="visual-studio-code"></a>Visual Studio code

Visual Studio code 是可用于多个平台的新式代码编辑器。 扩展（如[Azure IoT 工具包](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)中的扩展）使你可以自定义编辑器以支持各种开发方案。
