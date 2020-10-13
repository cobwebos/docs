---
title: 安全代理体系结构
description: 了解 Azure Defender for IoT 服务中使用的代理的安全代理体系结构。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: 64d90b4d9dc3efbe877230bbc20780b1c4f2d213
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934561"
---
# <a name="security-agent-reference-architecture"></a>安全代理参考体系结构

Azure Defender for IoT 为通过 IoT 中心记录、处理、聚合和发送安全数据的安全代理提供了参考体系结构。

安全代理设计为在受约束的 IoT 环境中工作，并且与它们所使用的资源相比，它们可在其提供的值方面实现高度自定义。

安全代理支持以下功能：

- 从基础操作系统 (Linux，Windows) 收集原始安全事件。 若要了解有关可用安全数据收集器的详细信息，请参阅 [用于 IoT 代理配置的 Defender](how-to-agent-configuration.md)。

- 将原始安全事件聚合到通过 IoT 中心发送的消息中。

- 使用现有的设备标识或专用模块标识进行身份验证。 有关详细信息，请参阅 [安全代理身份验证方法](concept-security-agent-authentication-methods.md) 。

- 通过使用 **azureiotsecurity** 模块克隆进行远程配置。 若要了解详细信息，请参阅 [配置用于 IoT 代理的 Defender](how-to-agent-configuration.md)。

Defender for IoT Security agent 作为开放源代码项目开发，可从 GitHub 获得：

- [基于 IoT C 的代理的 Defender](https://github.com/Azure/Azure-IoT-Security-Agent-C)
- [基于 IoT c # 的代理的 Defender](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>代理支持的平台

Defender for IoT 为32位和64位 Windows 提供不同的安装程序代理，适用于32位和64位 Linux。 请确保按照下表为每个设备设置正确的代理安装程序：

| 体系结构 | Linux | Windows |    详细信息|
|----------|----------------------------------------------|-------------|-------------------------------------------|
| 32位  | C  | C#  ||
| 64 位  | C # 或 C           | C#      | 建议为设备使用具有更大限制或最小设备资源的 C 代理。|
|

## <a name="next-steps"></a>后续步骤

本文介绍了有关 IoT 安全代理体系结构和可用安装程序的 Defender。

若要继续使用适用于 IoT 的 Defender 部署，请使用以下文章：

- 了解 [安全代理身份验证方法](concept-security-agent-authentication-methods.md)
- 选择并部署 [安全代理](how-to-deploy-agent.md)
- 查看用于 IoT[服务先决条件](service-prerequisites.md)的 Defender
- 了解如何 [在 Iot 中心为 iot 服务启用 Defender](quickstart-onboard-iot-hub.md)
- 从[用于 IoT 的 DEFENDER 常见问题](resources-frequently-asked-questions.md)中了解有关该服务的详细信息
