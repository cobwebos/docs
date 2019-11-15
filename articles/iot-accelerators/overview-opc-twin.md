---
title: 什么是 OPC 孪生 - Azure | Microsoft Docs
description: 本文提供了 OPC 孪生的概述。 OPC 孪生通过 REST API 提供发现、注册和远程控制工业设备的功能。
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 91448f55f0ebb88ba6c685b960ece9d91cb98e25
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826225"
---
# <a name="what-is-opc-twin"></a>什么是 OPC 孪生？

OPC 孪生由使用 Azure IoT Edge 和 IoT 中心连接云与工厂网络的微服务构成。 OPC 孪生通过 REST API 提供发现、注册和远程控制工业设备的功能。 OPC 孪生不需要 OPC 统一体系结构 (OPC UA) SDK，对编程语言不可知，可包含在无服务器工作流中。 本文将介绍 OPC 孪生的几个用例。

## <a name="discovery-and-control"></a>发现和控制
可以使用 OPC 孪生完成简单的发现和注册。

### <a name="simple-discovery-and-registration"></a>简单发现和注册
工厂操作员可以使用 OPC 孪生来扫描工厂网络，以便可以发现和注册 OPC UA 服务器。 作为替代方法，工厂操作员也可以使用已知的发现 URL 来手动注册 OPC UA 设备。 例如，若要在工厂车间中安装包含 OPC 孪生模块的 IoT Edge 网关后连接到所有 OPC UA 设备，工厂操作员可以远程触发网络扫描，并直观地查看所有 OPC UA 服务器。 

### <a name="simple-control"></a>简单控制
工厂操作员可以使用 OPC 孪生对事件做出反应，并在云中自动或者手动即时重新配置其工厂车间中的机器。 OPC 孪生提供 REST API 用于调用 OPC UA 服务器上的服务、浏览服务器的地址空间，以及读取/写入变量和执行方法。 例如，锅炉工可以使用温度 KPI 来控制生产线。 温度传感器使用 OPC 发布程序来发布数据更改。 当温度达到阈值时，工厂操作员会收到警报。 生产线可通过 OPC 孪生自动冷却设备。 冷却后，工厂操作员会收到通知。

## <a name="authentication"></a>Authentication
使用 OPC 孪生可以进行简单的身份验证以及获得简单的开发人员体验。

### <a name="simple-authentication"></a>简单身份验证 
OPC 孪生使用基于 Azure Active Directory (AAD) 的身份验证和端到端的审核。 例如，使用 OPC 孪生可在其顶层生成应用程序，以操作员在机器上执行了哪些操作。 在机器端，可通过 OPC UA 审核实现此目的。 在云端，可通过存储一个不可变的客户端审核日志并使用 REST API 执行 AAD 身份验证来实现此目的。

### <a name="simple-developer-experience"></a>简单开发人员体验 
OPC 孪生可与通过 REST API 以任何编程语言编写的应用程序配合使用。 由于开发人员可将 OPC UA 客户端集成到解决方案中，因此不需要拥有 OPC UA SDK 方面的知识。 OPC 孪生可以无缝集成到无状态的无服务器体系结构。 例如，开发警报和事件仪表板应用程序的完整堆栈 Web 开发人员可以使用 OPC 孪生以 JavaScript 或 TypeScript 语言编写逻辑来响应事件，而无需拥有 C、C# 或整个 OPC UA 堆栈实现方面的知识。 

## <a name="next-steps"></a>后续步骤

了解 OPC 孪生及其用途后，建议接下来完成以下步骤：

> [!div class="nextstepaction"]
> [什么是 OPC 保管库](overview-opc-vault.md)
