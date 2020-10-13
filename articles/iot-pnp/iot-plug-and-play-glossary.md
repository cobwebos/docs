---
title: 术语词汇表-IoT 即插即用 |Microsoft Docs
description: 概念-与 IoT 即插即用相关的常见术语的词汇表。
author: dominicbetts
ms.author: dobett
ms.date: 07/22/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: d44866e2d04ab1bab5d2eca01374350a7d73a0ea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91577333"
---
# <a name="glossary-of-terms-for-iot-plug-and-play"></a>IoT 即插即用术语词汇表

IoT 即插即用文章中常用术语的定义。

## <a name="azure-iot-explorer-tool"></a>Azure IoT 资源管理器工具

Azure IoT 资源管理器是一种图形工具，可用于与 [IoT 即插即用设备](#iot-plug-and-play-device)进行交互并对其进行测试。 在本地计算机上安装该工具后，可以用它执行以下操作：

- 查看连接到 [IoT 中心](#azure-iot-hub)的设备。
- 连接到 IoT 即插即用设备。
- 查看设备 [组件](#component)。
- 查看设备发送的[遥测数据](#telemetry)。
- 使用设备[属性](#properties)。
- 调用设备[命令](#commands)。

## <a name="azure-iot-hub"></a>Azure IoT 中心

IoT 中心托管服务在云中进行托管，充当中央消息中心，用于 IoT 应用程序与其管理的设备之间的双向通信。 [IoT 即插即用设备](#iot-plug-and-play-device)可以连接到 IoT 中心。 IoT 解决方案使用 IoT 中心来实现：

- 用于将遥测数据发送到基于云的解决方案的设备。
- 用于管理已连接的设备的基于云的解决方案。

## <a name="azure-iot-device-sdk"></a>Azure IoT 设备 SDK

可以使用多种语言的设备 SDK 来生成 IoT 即插即用设备客户端应用程序。 将 **DeviceClient** 用于设备，并将 **ModuleClient** 用于模块和 IoT Edge 模块。

## <a name="commands"></a>命令

在[接口](#interface)中定义的命令表示可以在[数字孪生体](#digital-twin)上执行的方法。 例如，用于重新启动设备的命令。

## <a name="component"></a>组件

组件允许您将模型 [接口](#interface) 生成为其他接口的程序集。 [设备模型](#device-model)可以将多个接口组合为多个组件。 例如，模型可能包含 switch 组件和恒温器组件。 模型中的多个组件也可以使用相同的接口类型。 例如，模型可能包括两个恒温器组件。

## <a name="connection-string"></a>连接字符串

连接字符串封装了连接到终结点所需的信息。 连接字符串通常包含终结点的地址和安全信息，但连接字符串的格式因服务而异。 与 IoT 中心服务关联的连接字符串有两种：

- 设备连接字符串使 [IoT 即插即用设备](#iot-plug-and-play-device)能够连接到 IoT 中心上面向设备的终结点。 设备上的客户端代码使用连接字符串与 IoT 中心建立安全连接。
- IoT 中心连接字符串使后端解决方案和工具能够安全地连接到 IoT 中心上面向服务的终结点。 这些解决方案和工具可管理 IoT 中心以及与之连接的设备。

## <a name="default-component"></a>默认组件

所有 [设备模型](#device-model) 都具有默认组件。 简单的设备模型只有一个默认组件（此类模型也称为 "无组件" 设备）。 更复杂的模型在默认组件下嵌套了多个组件。

## <a name="device-certification"></a>设备认证

IoT 即插即用认证计划验证设备是否符合 IoT 即插即用认证要求。 可将已验证的设备添加到公共 [Azure IoT 认证设备目录](https://aka.ms/devicecatalog)。

## <a name="device-model"></a>设备型号

设备型号描述了 [IoT 即插即用设备](#iot-plug-and-play-device) ，并定义组成设备的 [组件](#component) 。 简单的设备模型没有单独的组件，并包含单个接口的定义。 Azure IoT 浏览器工具显示一个简单模型，其中包含一个 [默认组件](#default-component)。

更复杂的设备模型包含多个组件。 设备型号通常对应于物理设备、产品或 SKU。 使用 [数字孪生定义语言版本 2](#digital-twins-definition-language) 来定义设备型号。

## <a name="device-builder"></a>设备生成器

设备生成器在实现在[IoT 即插即用设备](#iot-plug-and-play-device)上运行的代码时使用[设备模型](#device-model)和[接口](#interface)。 设备构建者通常使用 [Azure IoT 设备 sdk](#azure-iot-device-sdk) 之一来实现设备客户端，但这不是必需的。

## <a name="device-modeling"></a>设备建模

[设备生成器](#device-builder)或[模块生成器](#module-builder)使用[数字孪生定义语言](#digital-twins-definition-language)来模拟[IoT 即插即用设备](#iot-plug-and-play-device)的功能。 [解决方案生成器](#solution-builder)可以从模型配置 IoT 解决方案。

## <a name="digital-twin"></a>数字孪生体

数字孪生体是一种 [IoT 即插即用设备](#iot-plug-and-play-device)模型。 数字克隆使用 [数字孪生定义语言](#digital-twins-definition-language)进行建模。 你可以使用 [Azure IoT 设备 SDK](#azure-iot-device-sdk) 在运行时与数字孪生体交互。 例如，你可以在设备的数字孪生体中设置属性值，SDK 会将此更改传递到云中的 IoT 解决方案。

## <a name="digital-twin-change-events"></a>数字孪生体更改事件

将 [IoT 即插即用设备](#iot-plug-and-play-device)连接到 [IoT 中心](#azure-iot-hub)时，IoT 中心可以使用其路由功能发送数字孪生体更改通知。 例如，每当设备上的 [属性](#properties) 值发生更改时，IoT 中心均可将通知发送到终结点，如事件中心。

## <a name="digital-twins-definition-language"></a>数字孪生定义语言

用于描述 [IoT 即插即用设备](#iot-plug-and-play-device)的模型和接口的语言。 使用 [数字孪生定义语言版本 2](https://github.com/Azure/opendigitaltwins-dtdl) 来描述数字克隆 [的](#digital-twin) 功能，并使 iot 平台和 iot 解决方案能够利用实体的语义。

## <a name="digital-twin-route"></a>数字孪生体路由

在 [IoT 中心](#azure-iot-hub) 内设置的一种路由，用于向和终结点（例如事件中心）传递 [数字克隆更改事件](#digital-twin-change-events) 。

## <a name="interface"></a>接口

接口描述由 [IoT 即插即用设备](#iot-plug-and-play-device)或[数字孪生体](#digital-twin)实现的相关功能。 可以跨不同的 [设备模型](#device-model)重复使用接口。 当在设备模型中使用接口时，它将定义设备的一个 [组件](#component) 。 简单设备只包含一个默认接口。

## <a name="iot-hub-query-language"></a>IoT 中心查询语言

IoT 中心查询语言用于多种目的。 例如，可以使用该语言来搜索已向 IoT 中心注册的设备或优化[数字孪生体路由](#digital-twin-route)行为。

## <a name="iot-plug-and-play-bridge"></a>IoT 即插即用桥接

IoT 即插即用桥是一个开源应用程序，它使连接到 Windows 或 Linux 网关上的现有传感器和外围设备能够作为 [IoT 即插即用设备](#iot-plug-and-play-device)连接。

## <a name="iot-plug-and-play-device"></a>IoT 即插即用设备

IoT 即插即用设备通常是一种小型的独立计算设备，用于收集数据或控制其他设备，并运行实现 [设备型号](#device-model)的软件或固件。  例如，IoT 即插即用设备可以是环境监视设备，也可以是智能农业灌溉系统的控制器。 IoT 即插即用设备可能会直接实现或作为 IoT Edge 模块。 你可以编写云托管的 IoT 解决方案来命令、控制和接收 IoT 即插即用设备中的数据。

## <a name="iot-plug-and-play-conventions"></a>IoT 即插即用约定

IoT 即插即用 [设备](#iot-plug-and-play-device) 在与解决方案交换数据时，应遵循一组 [约定](concepts-convention.md) 。

## <a name="model-id"></a>模型 ID

当 IoT 即插即用设备连接到 IoT 中心时，它会发送其实现的[DTDL](#digital-twins-definition-language)模型的**模型 ID** 。 这使解决方案能够查找设备型号。

## <a name="model-repository"></a>模型存储库

[模型存储库](concepts-model-repository.md)存储[设备模型](#device-model)和[接口](#interface)。

## <a name="model-repository-rest-api"></a>模型存储库 REST API

用于管理模型存储库和与之交互的 API。 例如，你可以使用 API 来添加和搜索 [设备型号](#device-model)。

## <a name="module-builder"></a>模块生成器

当实现在[IoT 即插即用设备](#iot-plug-and-play-device)上运行的代码时，模块生成器使用[设备模型](#device-model)和[接口](#interface)。 模块构建者将代码作为模块或 IoT Edge 模块实现，以便部署到设备上的 IoT Edge 运行时。

## <a name="properties"></a>属性

属性是在[接口](#interface)中定义的数据字段，表示数字孪生体的某种状态。 你可以将属性声明为只读或可写。 只读属性（例如序列号）由 [IoT 即插即用设备](#iot-plug-and-play-device)上运行的代码设置。  可写属性（例如警报阈值）通常从基于云的 IoT 解决方案中设置。

## <a name="shared-access-signature"></a>共享访问签名

共享访问签名是基于 SHA-256 安全哈希或 URI 的身份验证机制。 共享访问签名身份验证有两个组件：共享访问策略和共享访问签名（通常称为令牌）。 [IoT 即插即用设备](#iot-plug-and-play-device)使用共享访问签名向 [IoT 中心](#azure-iot-hub)进行身份验证。

## <a name="solution-builder"></a>解决方案生成器

解决方案生成器将创建解决方案后端。 解决方案生成器通常适用于 Azure 资源，如 [IoT 中心](#azure-iot-hub) 和 [模型存储库](#model-repository)。

## <a name="telemetry"></a>遥测

在[接口](#interface)中定义的遥测字段表示度量值。 这些度量值通常是诸如 [IoT 即插即用设备](#iot-plug-and-play-device)作为数据流发送的传感器读数之类的值。
