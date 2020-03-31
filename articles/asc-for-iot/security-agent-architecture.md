---
title: 了解 IoT 安全代理体系结构的 Azure 安全中心*微软文档
description: 了解 Azure 安全中心中用于 IoT 服务的代理的安全代理体系结构。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: 998aeab197931a75579fc39b28e3a248b85fc57b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "68596907"
---
# <a name="security-agent-reference-architecture"></a>安全代理引用体系结构

IoT 的 Azure 安全中心为通过 IoT 中心记录、处理、聚合和发送安全数据的安全代理提供了参考体系结构。

安全代理设计为在受约束的 IoT 环境中工作，并且与它们使用的资源相比，它们提供的值是高度可定制的。

安全代理支持以下功能：

- 从基础操作系统（Linux、Windows）收集原始安全事件。 要了解有关可用安全数据收集器的更多信息，请参阅[Azure 安全中心进行 IoT 代理配置](how-to-agent-configuration.md)。

- 将原始安全事件聚合到通过 IoT 中心发送的消息中。

- 使用现有设备标识或专用模块标识进行身份验证。 有关详细信息，请参阅[安全代理身份验证方法](concept-security-agent-authentication-methods.md)。

- 使用**Azureiot 安全**模块孪生进行远程配置。 要了解更多信息，请参阅[为 IoT 代理配置 Azure 安全中心](how-to-agent-configuration.md)。

IoT 安全代理的 Azure 安全中心开发为开源项目，可从 GitHub 获得： 

- [基于 IoT C 的代理的 Azure 安全中心](https://github.com/Azure/Azure-IoT-Security-Agent-C) 
- [基于 IoT C# 的代理的 Azure 安全中心](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>代理支持的平台

IoT 的 Azure 安全中心为 32 位和 64 位 Windows 提供了不同的安装程序代理，32 位和 64 位 Linux 也提供了相同的安装程序代理。 确保根据下表为每个设备具有正确的代理安装程序：

| 体系结构 | Linux | Windows |    详细信息|
|----------|----------------------------------------------|-------------|-------------------------------------------|
| 32 位  | C  | C#  ||
| 64 位  | C# 或 C           | C#      | 我们建议对设备资源受限或最少的设备使用 C 代理。|
|

## <a name="next-steps"></a>后续步骤

在本文中，您了解了适用于 IoT 安全代理体系结构的 Azure 安全中心以及可用的安装程序。

要继续使用用于 IoT 部署的 Azure 安全中心，请使用以下文章：

- 了解[安全代理身份验证方法](concept-security-agent-authentication-methods.md)
- 选择并部署[安全代理](how-to-deploy-agent.md)
- 查看 Azure 安全中心，查看 IoT[服务先决条件](service-prerequisites.md)
- 了解如何在[IoT 中心启用 IoT 服务的 Azure 安全中心](quickstart-onboard-iot-hub.md)
- 从[IoT 常见问题解答的 Azure 安全中心](resources-frequently-asked-questions.md)了解有关该服务
