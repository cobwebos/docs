---
title: 了解 IoT security agent 体系结构的 Azure 安全中心 |Microsoft Docs
description: 了解用于 IoT 服务的 Azure 安全中心中所使用的代理的安全代理体系结构。
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
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596907"
---
# <a name="security-agent-reference-architecture"></a>安全代理参考体系结构

用于 IoT 的 Azure 安全中心为通过 IoT 中心记录、处理、聚合和发送安全数据的安全代理提供了参考体系结构。

安全代理设计为在受约束的 IoT 环境中工作, 并且与它们所使用的资源相比, 它们可在其提供的值方面实现高度自定义。

安全代理支持以下功能:

- 收集基础操作系统 (Linux、Windows) 中的原始安全事件。 若要了解有关可用安全数据收集器的详细信息, 请参阅[用于 IoT 代理配置的 Azure 安全中心](how-to-agent-configuration.md)。

- 将原始安全事件聚合到通过 IoT 中心发送的消息中。

- 使用现有的设备标识或专用模块标识进行身份验证。 有关详细信息, 请参阅[安全代理身份验证方法](concept-security-agent-authentication-methods.md)。

- 通过使用**azureiotsecurity**模块克隆进行远程配置。 若要了解详细信息, 请参阅[为 IoT 代理配置 Azure 安全中心](how-to-agent-configuration.md)。

用于 IoT 安全代理的 Azure 安全中心作为开放源代码项目开发, 可从 GitHub 获得: 

- [基于 IoT C 的代理的 Azure 安全中心](https://github.com/Azure/Azure-IoT-Security-Agent-C) 
- [基于 IoT C#的代理的 Azure 安全中心](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>代理支持的平台

用于 IoT 的 Azure 安全中心为32位和64位 Windows 提供不同的安装程序代理, 适用于32位和64位 Linux。 请确保按照下表为每个设备设置正确的代理安装程序:

| 体系结构 | Linux | Windows |    详细信息|
|----------|----------------------------------------------|-------------|-------------------------------------------|
| 32位  | C  | C#  ||
| 64  | C#或 C           | C#      | 建议为设备使用具有更大限制或最小设备资源的 C 代理。|
|

## <a name="next-steps"></a>后续步骤

本文介绍了适用于 IoT 安全代理体系结构和可用安装程序的 Azure 安全中心。

若要继续开始使用 Azure 安全中心进行 IoT 部署, 请使用以下文章:

- 了解[安全代理身份验证方法](concept-security-agent-authentication-methods.md)
- 选择并部署[安全代理](how-to-deploy-agent.md)
- 查看 Azure 安全中心以了解 IoT[服务先决条件](service-prerequisites.md)
- 了解如何[在 Iot 中心为 iot 服务启用 Azure 安全中心](quickstart-onboard-iot-hub.md)
- 详细了解[Azure 安全中心提供的 IOT 常见问题解答](resources-frequently-asked-questions.md)
