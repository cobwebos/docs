---
title: Azure IoT Central 中的体系结构概念 | Microsoft Docs
description: 本文介绍与 Azure IoT Central 的体系结构相关的重要概念
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 44408e7b6ad1a068f265bf7b78d973e6aae3001b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628753"
---
# <a name="azure-iot-central-architecture"></a>Azure IoT Central 体系结构

本文概述 Microsoft Azure IoT Central 体系结构。

![顶层体系结构](media/concepts-architecture/architecture.png)

## <a name="devices"></a>设备

设备与 Azure IoT Central 应用程序交换数据。 设备可以：

- 发送度量，例如遥测。
- 与应用程序同步设置。

在 Azure IoT Central 中，设备可以与应用程序交换的数据在设备模板中指定。 有关设备模板的详细信息，请参阅[元数据管理](#metadata-management)。

若要详细了解设备如何连接到 Azure IoT Central 应用程序，请参阅[设备连接](concepts-connectivity.md)。

## <a name="cloud-gateway"></a>云网关

Azure IoT Central 使用 Azure IoT 中心作为启用设备连接的云网关。 IoT 中心允许：

- 在云中进行大规模数据引入。
- 设备管理。
- 安全的设备连接。

若要详细了解 IoT 中心，请参阅 [Azure IoT 中心](https://docs.microsoft.com/azure/iot-hub/)。

若要详细了解 Azure IoT Central 中的设备连接，请参阅[设备连接](concepts-connectivity.md)。

## <a name="data-stores"></a>数据存储

Azure IoT Central 在云中存储应用程序数据。 存储的应用程序数据包括：

- 设备模板。
- 设备标识。
- 设备元数据。
- 用户和角色数据。

Azure IoT Central 将时序存储用于从设备发送的度量数据。 设备提供的时序数据供分析服务使用。

## <a name="analytics"></a>分析

分析服务负责生成应用程序显示的自定义报告数据。 操作员可以[自定义在应用程序中显示的分析](howto-create-analytics.md)。 分析服务在 [Azure 时序见解](https://azure.microsoft.com/services/time-series-insights/)基础上构建，可以处理从设备发送的度量数据。

## <a name="rules-and-actions"></a>规则和操作

[规则和操作](howto-create-telemetry-rules.md)可以紧密地配合使用，以便自动完成应用程序中的任务。 开发者可以根据设备遥测（例如温度超过定义的阈值）来定义规则。 Azure IoT Central 使用流处理器来确定何时满足规则条件。 规则条件在得到满足的情况下，会触发开发者定义的操作。 例如，可以通过某个操作向工程师发送通知电子邮件，告知对方设备中的温度过高。

## <a name="metadata-management"></a>元数据管理

在 Azure IoT Central 应用程序中，设备模板用于定义设备类型的行为和功能。 例如，致冷器设备模板指定致冷器发送给应用程序的遥测。

![模板体系结构](media/concepts-architecture/template_architecture.png)

在设备模板中：

- **度量**指定设备发送给应用程序的遥测。
- **设置**指定操作员可以设置的配置。
- **属性**指定操作员可以设置的元数据。
- **规则**根据从设备发送的数据自动设置应用程序中的行为。
- **仪表板**是可以在应用程序中自定义的设备视图。

一个应用程序可以有一个或多个基于每个设备模板的模拟设备和真实设备。

## <a name="rbac"></a>RBAC

[管理员可以使用预定义的角色定义适用于 Azure IoT Central 应用程序的访问规则](howto-administer.md)。 管理员可以通过为用户分配角色来决定用户可以访问的应用程序的具体区域。

## <a name="security"></a>“安全”

Azure IoT Central 中的安全功能包括：

- 对传输中的和静止时的数据加密。
- 通过 Azure Active Directory 或 Microsoft 帐户提供身份验证。 支持双重身份验证。
- 完全的租户隔离。
- 设备级别安全性。

## <a name="ui-shell"></a>UI Shell

UI Shell 是一个现代的基于 HTML5 浏览器的应用程序，响应速度快。

## <a name="next-steps"></a>后续步骤

了解 Azure IoT Central 的体系结构以后，建议接下来继续学习 Azure IoT Central 中的[设备连接](concepts-connectivity.md)。