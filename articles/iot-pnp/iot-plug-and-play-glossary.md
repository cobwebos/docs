---
title: 术语表 - IoT 即插即用预览版 | Microsoft Docs
description: 概念 - 与 IoT 即插即用预览版相关的常用术语表。
author: Philmea
ms.author: philmea
ms.date: 12/23/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: f0c21626c664f2d72b534ebae7f0a257620be07d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81767074"
---
# <a name="glossary-of-terms-for-iot-plug-and-play-preview"></a>IoT 即插即用预览版术语表

IoT 即插即用文章中常用术语的定义。

## <a name="azure-certified-for-iot-portal"></a>Azure IoT 认证门户

可以使用 [Azure IoT 认证门户](https://aka.ms/ACFI)网站执行以下操作：

- 完成 [IoT 即插即用设备](#iot-plug-and-play-device)的[认证过程](#device-certification)。
- 查找[设备功能模型](#device-capability-model)。
- 将设备功能模型发布到[公共模型存储库](#public-model-repository)。

## <a name="azure-cli"></a>Azure CLI

Azure CLI 是用于管理 Azure 资源的跨平台命令行工具。 适用于 Azure CLI 的 Azure IoT 扩展是一个命令行工具，用于与 [IoT 即插即用设备](#iot-plug-and-play-device)进行交互并对其进行测试。 可以使用该扩展执行以下操作：

- 连接到 IoT 即插即用设备。
- 查看设备发送的[遥测数据](#telemetry)。
- 使用设备[属性](#properties)。
- 调用设备[命令](#commands)。
- 管理[模型存储库](#model-repository)、[接口](#interface)和[设备功能模型](#device-capability-model)。

## <a name="azure-iot-central"></a>Azure IoT Central

Azure IoT Central 是一种完全托管的软件即服务解决方案，借助此解决方案可轻松连接、监视以及管理 [IoT 即插即用设备](#iot-plug-and-play-device)。 可以使用[设备功能模型](#device-capability-model)自动配置 IoT Central 应用程序，以监视和管理设备。

## <a name="azure-iot-certification-service"></a>Azure IoT 认证服务

当你通过 [Azure IoT 认证门户](#azure-certified-for-iot-portal)提交 [IoT 即插即用设备](#iot-plug-and-play-device)进行认证时，Azure IoT 认证服务会运行一组认证测试。 必须先对设备进行认证，然后才能将设备添加到 [IoT 认证设备目录](#certified-for-iot-device-catalog)中。

## <a name="azure-iot-tools-extension"></a>Azure IoT Tools 扩展

Azure IoT Tools 是 [Visual Studio Code](#visual-studio-code) 中的扩展集合，可帮助你与 IoT 中心进行交互并开发 IoT 设备。 开发 IoT 即插即用设备时，它可以帮助你：

- 创作[设备功能模型](#device-capability-model)和[接口](#interface)。
- 发布到[模型存储库](#model-repository)。
- 生成用于实现设备应用程序的主干代码。

## <a name="azure-iot-explorer-tool"></a>Azure IoT 资源管理器工具

Azure IoT 资源管理器是一种图形工具，可用于与 [IoT 即插即用设备](#iot-plug-and-play-device)进行交互并对其进行测试。 在本地计算机上安装该工具后，可以用它执行以下操作：

- 查看连接到 [IoT 中心](#azure-iot-hub)的设备。
- 连接到 IoT 即插即用设备。
- 查看设备发送的[遥测数据](#telemetry)。
- 使用设备[属性](#properties)。
- 调用设备[命令](#commands)。

## <a name="azure-iot-hub"></a>Azure IoT 中心

IoT 中心托管服务在云中进行托管，充当中央消息中心，用于 IoT 应用程序与其管理的设备之间的双向通信。 [IoT 即插即用设备](#iot-plug-and-play-device)可以连接到 IoT 中心。 IoT 解决方案使用 IoT 中心来实现：

- 用于将遥测数据发送到基于云的解决方案的设备。
- 用于管理已连接的设备的基于云的解决方案。

## <a name="azure-iot-device-sdk"></a>Azure IoT 设备 SDK

可以使用多种语言的设备 SDK 来生成 IoT 即插即用设备客户端应用程序。 [设备认证](#device-certification)的要求之一是设备客户端代码使用其中一种 Azure IoT 设备 SDK。

## <a name="certified-for-iot-device-catalog"></a>IoT 认证设备目录

[IoT 认证设备目录](https://catalog.azureiotsolutions.com/)列出了已通过[设备认证](#device-certification)测试的 [IoT 即插即用设备](#iot-plug-and-play-device)。 目录中 IoT 即插即用设备的[设备功能模型](#device-capability-model)发布在公共模型存储库中。

## <a name="commands"></a>命令

在[接口](#interface)中定义的命令表示可以在[数字孪生体](#digital-twin)上执行的方法。 例如，用于重新启动设备的命令。

## <a name="common-interface"></a>通用接口

所有 [IoT 即插即用设备](#iot-plug-and-play-device)均应实现一些通用[接口](#interface)。 例如，设备信息接口定义有关设备的硬件和操作系统信息。 [设备认证](#device-certification)要求设备实现几个通用接口。 你可以从公共模型存储库中检索通用接口定义。

## <a name="company-model-repository"></a>公司模型存储库

组织可以将公司[模型存储库](#model-repository)用作[设备功能模型](#device-capability-model)和[接口](#interface)的专用存储。

## <a name="connection-string"></a>连接字符串

连接字符串封装了连接到终结点所需的信息。 连接字符串通常包含终结点的地址和安全信息，但连接字符串的格式因服务而异。 与 IoT 中心服务关联的连接字符串有两种：

- 设备连接字符串使 [IoT 即插即用设备](#iot-plug-and-play-device)能够连接到 IoT 中心上面向设备的终结点。 设备上的客户端代码使用连接字符串与 IoT 中心建立安全连接。
- IoT 中心连接字符串使后端解决方案和工具能够安全地连接到 IoT 中心上面向服务的终结点。 这些解决方案和工具可管理 IoT 中心以及与之连接的设备。
- 公司模型存储库连接字符串使后端解决方案和工具能够安全地连接到[公司模型存储库](#company-model-repository)。 这些解决方案和工具使用或管理存储库中的[设备功能模型](#device-capability-model)和[接口](#interface)。

## <a name="device-capability-model"></a>设备功能模型

设备功能模型描述了 [IoT 即插即用设备](#iot-plug-and-play-device)，并定义了设备实现的[接口](#interface)集。 设备功能模型通常对应于物理设备、产品或 SKU。 可使用[数字孪生体定义语言](#digital-twin-definition-language)定义设备功能模型。

## <a name="device-certification"></a>设备认证

设备认证是对 [IoT 即插即用设备](#iot-plug-and-play-device)进行认证的过程，以便可以将该设备添加到 [IoT 认证设备目录](#certified-for-iot-device-catalog)，将其[设备功能模型](#device-capability-model)和[接口](#interface)添加到[公共模型存储库](#public-model-repository)。

## <a name="device-developer"></a>设备开发人员

设备开发人员使用[设备功能模型](#device-capability-model)、[接口](#interface)和 [Azure IoT 设备 SDK](#azure-iot-device-sdk) 来实现在 [IoT 即插即用设备](#iot-plug-and-play-device)上运行的代码。

## <a name="device-modeling"></a>设备建模

[设备开发人员](#device-developer)使用[数字孪生体定义语言](#digital-twin-definition-language)对 [IoT 即插即用设备](#iot-plug-and-play-device)的功能建模。 可以使用模型存储库共享该模型。 设备开发人员可以根据该模型生成主干设备代码。 [解决方案开发人员](#solution-developer)可以根据该模型配置 IoT 解决方案。

## <a name="device-provisioning-service"></a>设备预配服务

[Azure IoT Central](#azure-iot-central) 使用设备预配服务来管理所有设备注册和连接。 有关详细信息，请参阅 [Azure IoT Central 中的设备连接](../iot-central/core/concepts-get-connected.md)。 你还可以使用设备预配服务来管理基于 IoT 中心的 IoT 解决方案的设备注册和连接。 有关详细信息，请参阅[使用 Azure IoT 中心设备预配服务预配设备](../iot-dps/about-iot-dps.md)。

## <a name="device-registration"></a>设备注册

在 [IoT 即插即用设备](#iot-plug-and-play-device)连接到 IoT 解决方案之前，必须先向该解决方案注册该设备。 [Azure IoT Central](#azure-iot-central) 使用[设备预配服务](#device-provisioning-service)来管理设备注册。 在自定义 IoT 解决方案中，可以在 Azure 门户中或以编程方式向 IoT 中心注册设备。

## <a name="device-first"></a>设备优先

[Azure IoT Central](#azure-iot-central) 支持设备优先的注册和连接方案。 在此方案中，[IoT 即插即用设备](#iot-plug-and-play-device)可以连接到 IoT Central 应用程序，而无需事先注册。 设备首次连接到应用程序时，会自动进行注册。

## <a name="digital-twin"></a>数字孪生体

数字孪生体是一种 [IoT 即插即用设备](#iot-plug-and-play-device)模型。 数字孪生体使用[数字孪生体定义语言](#digital-twin-definition-language)进行建模。 你可以使用 [Azure IoT 设备 SDK](#azure-iot-device-sdk) 在运行时与数字孪生体交互。 例如，你可以在设备的数字孪生体中设置属性值，SDK 会将此更改传递到云中的 IoT 解决方案。

## <a name="digital-twin-change-events"></a>数字孪生体更改事件

将 [IoT 即插即用设备](#iot-plug-and-play-device)连接到 [IoT 中心](#azure-iot-hub)时，IoT 中心可以使用其路由功能发送数字孪生体更改通知。 例如，每当设备上的[属性](#properties)值发生更改时，IoT 中心都可以将通知发送到诸如服务总线队列之类的终结点。

## <a name="digital-twin-definition-language"></a>数字孪生体定义语言

用于描述 [IoT 即插即用设备](#iot-plug-and-play-device)的模型和接口的语言。 可使用[数字孪生体定义语言](https://aka.ms/DTDL)描述[数字孪生体](#digital-twin)的各项功能，并使 IoT 平台和 IoT 解决方案能够利用实体的语义。

## <a name="digital-twin-route"></a>数字孪生体路由

在 [IoT 中心](#azure-iot-hub)设置的路由，用于将[数字孪生体更改事件](#digital-twin-change-events)传递到诸如服务总线队列之类的终结点。

## <a name="interface"></a>接口

接口描述由 [IoT 即插即用设备](#iot-plug-and-play-device)或[数字孪生体](#digital-twin)实现的相关功能。 你可以跨不同的[设备功能模型](#device-capability-model)重复使用接口。

## <a name="iot-hub-query-language"></a>IoT 中心查询语言

IoT 中心查询语言用于多种目的。 例如，可以使用该语言来搜索已向 IoT 中心[注册的设备](#device-registration)或优化[数字孪生体路由](#digital-twin-route)行为。

## <a name="iot-plug-and-play-device"></a>IoT 即插即用设备

IoT 即插即用设备通常是一种小型的独立计算设备，可收集数据或控制其他设备，并运行实现[设备功能模型](#device-capability-model)的软件或固件。  例如，IoT 即插即用设备可以是环境监视设备，也可以是智能农业灌溉系统的控制器。 你可以编写云托管的 IoT 解决方案来命令、控制和接收 IoT 即插即用设备中的数据。 [Azure IoT 认证设备目录](#certified-for-iot-device-catalog)列出了可用的 IoT 即插即用设备。 该目录中的每个 IoT 即插即用设备都已经过验证，并且具有[设备功能模型](#device-capability-model)。

## <a name="microsoft-partner-center"></a>Microsoft 合作伙伴中心

[Microsoft 合作伙伴中心](https://docs.microsoft.com/partner-center/)是组织用来管理与 Microsoft 的端到端关系的地方。 必须有 Microsoft 合作伙伴中心帐户才能在 [Azure IoT 认证门户](#azure-certified-for-iot-portal)中认证 [IoT 即插即用设备](#iot-plug-and-play-device)。

## <a name="model-discovery"></a>模型发现

当 [IoT 即插即用设备](#iot-plug-and-play-device)连接到 IoT 解决方案时，该解决方案可以通过查找[设备功能模型](#device-capability-model)来发现设备的功能。 设备可以将其功能模型发送到解决方案，或者解决方案可以在[模型存储库](#model-repository)中查找设备功能模型。

## <a name="model-repository"></a>模型存储库

模型存储库用于存储[设备功能模型](#device-capability-model)和[接口](#interface)。 [公共模型存储库](#public-model-repository)只有一个。 组织可以创建自己的组织模型存储库。

## <a name="model-repository-rest-api"></a>模型存储库 REST API

用于管理模型存储库并与之交互的 API。 例如，可以使用该 API 来添加[设备功能模型](#device-capability-model)和搜索功能模型。

## <a name="properties"></a>属性

属性是在[接口](#interface)中定义的数据字段，表示数字孪生体的某种状态。 你可以将属性声明为只读或可写。 只读属性（例如序列号）由 [IoT 即插即用设备](#iot-plug-and-play-device)上运行的代码设置。  可写属性（例如警报阈值）通常从基于云的 IoT 解决方案中设置。

## <a name="public-model-repository"></a>公共模型存储库

公共模型存储库只有一个，用于存储[认证设备](#device-certification)的[设备功能模型](#device-capability-model)和[接口](#interface)。 公共模型存储库还存储[通用接口](#common-interface)定义。

## <a name="registration-id"></a>注册 ID

注册 ID 用于唯一标识[设备预配服务](#device-provisioning-service)中的设备。 此 ID 与作为 [IoT 中心](#azure-iot-hub)内设备的唯一标识符的设备 ID 不同。

## <a name="scope-id"></a>范围 ID

范围 ID 用于唯一标识[设备预配服务](#device-provisioning-service)实例。

## <a name="shared-access-signature"></a>共享访问签名

共享访问签名是基于 SHA-256 安全哈希或 URI 的身份验证机制。 共享访问签名身份验证有两个组件：共享访问策略和共享访问签名（通常称为令牌）。 [IoT 即插即用设备](#iot-plug-and-play-device)使用共享访问签名向 [IoT 中心](#azure-iot-hub)进行身份验证。

## <a name="solution-developer"></a>解决方案开发人员

解决方案开发人员创建解决方案后端。 解决方案开发人员通常使用 [IoT 中心](#azure-iot-hub)和[模型存储库](#model-repository)等 Azure 资源，或使用 [IoT Central](#azure-iot-central)。

## <a name="telemetry"></a>遥测

在[接口](#interface)中定义的遥测字段表示度量值。 这些度量值通常是诸如 [IoT 即插即用设备](#iot-plug-and-play-device)作为数据流发送的传感器读数之类的值。

## <a name="visual-studio-code"></a>Visual Studio Code

Visual Studio Code 是可用于多个平台的新式代码编辑器。 可以使用扩展（例如 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 包中的扩展）自定义该编辑器，以支持各种开发方案。
