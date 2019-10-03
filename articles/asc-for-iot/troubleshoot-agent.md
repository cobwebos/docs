---
title: Azure 安全中心 IoT Linux 安全代理故障排除指南 |Microsoft Docs
description: 排查使用适用于 Linux 的 IoT security agent 的 Azure 安全中心的问题。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 55c8d3b6-3126-4246-8d07-ef88fe5ea84f
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2019
ms.author: mlottner
ms.openlocfilehash: 7f3bd4be3ef927f73643146a457bc551ef86a450
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68600561"
---
# <a name="security-agent-troubleshoot-guide-linux"></a>安全代理疑难解答指南 (Linux)

本文介绍如何解决安全代理启动过程中的潜在问题。

用于 IoT 代理的 Azure 安全中心-安装后立即启动。 代理启动过程包括读取本地配置、连接到 Azure IoT 中心, 以及检索远程克隆配置。 在这些步骤中的任何一个步骤中, 故障都可能导致安全代理失败。

在此故障排除指南中, 你将了解如何执行以下操作:
> [!div class="checklist"]
> * 验证安全代理是否正在运行
> * 获取安全代理错误
> * 了解并修正安全代理错误 

## <a name="validate-if-the-security-agent-is-running"></a>验证安全代理是否正在运行

1. 若要验证安全代理是否正在运行, 请在安装代理之后等待几分钟, 并运行以下命令。 
     <br>

    **C 代理**
    ```bash
    grep "ASC for IoT Agent initialized" /var/log/syslog
    ```
    **C#代理商**
    ```bash
    grep "Agent is initialized!" /var/log/syslog
    ```
2. 如果命令返回空行, 则无法成功启动安全代理。    

## <a name="force-stop-the-security-agent"></a>强制停止安全代理 
如果安全代理无法启动, 请通过以下命令停止代理, 然后继续以下错误表:

```bash
systemctl stop ASCIoTAgent.service
```
## <a name="get-security-agent-errors"></a>获取安全代理错误
1. 通过运行以下命令来检索安全代理错误:
    ```bash
    grep ASCIoTAgent /var/log/syslog
    ```
2. 获取安全代理错误命令将检索 Azure 安全中心为 IoT 代理创建的所有日志。 使用下表来了解错误并采取纠正措施。 

> [!Note]
> 错误日志按时间顺序显示。 请确保记下每个错误的时间戳, 以帮助进行修正。 

## <a name="restart-the-agent"></a>重新启动代理

1. 查找并修复安全代理错误后, 请运行以下命令, 尝试重新启动代理。 
    ```bash
    systemctl restart ASCIoTAgent.service
    ```
1. 如果代理在启动过程中继续失败, 请重复前面的过程来检索 stop 并检索错误。 

## <a name="understand-security-agent-errors"></a>了解安全代理错误

大多数安全代理错误都按以下格式显示: 
```
Azure Security Center for IoT agent encountered an error! Error in: {Error Code}, reason: {Error sub code}, extra details: {error specific details}
```
| 错误代码 | 错误子代码 | 错误详细信息 | 修正 C | 补救C# |
|:-----------|:---------------|:--------|:------------|:------------|
| 本地配置 | 缺少配置 | 本地配置文件中缺少配置。 错误消息应声明缺少哪个密钥。 | 向/var/LocalConfiguration.json 文件添加缺少的密钥, 请参阅[cs-localconfig](azure-iot-security-local-configuration-c.md)获取详细信息。| 将缺少的密钥添加到常规 .config 文件中, 有关详细信息, 请参阅[c # localconfig](azure-iot-security-local-configuration-csharp.md) 。 |
| 本地配置 | 无法分析配置 | 无法分析配置值。 错误消息应说明无法分析哪一项。 由于值不在预期的类型中, 或者值超出了范围, 因此无法分析配置值。 | 请在/var/LocalConfiguration.json 文件中修复密钥的值, 以便它与 LocalConfiguration 架构匹配, 有关详细信息, 请参阅[c # localconfig](azure-iot-security-local-configuration-csharp.md) 。 |  修复常规 .config 文件中密钥的值, 使其与架构匹配, 有关详细信息, 请参阅[cs-localconfig](azure-iot-security-local-configuration-c.md) 。|
| 本地配置 | 文件格式 | 未能分析配置文件。 | 配置文件已损坏, 请下载并重新安装代理。 | |
| 远程配置 | 超时 | 代理无法在超时期限内提取 azureiotsecurity 模块。 | 请确保身份验证配置正确, 然后重试。 | 代理无法在超时期限内提取 azureiotsecurity 模块。 | 请确保身份验证配置正确, 然后重试。 |
| 身份验证 | 文件不存在 | 给定路径中的文件不存在。 | 请确保该文件存在于给定路径中或转到**LocalConfiguration**文件, 然后更改**FilePath**配置。 | 请确保该文件存在于给定的路径中, 或转到**Authentication .config**文件并更改**filePath**配置。|
| 身份验证 | 文件权限 | 代理没有足够的权限打开该文件。 | 向**asciotagent**用户授予对给定路径中的文件的读取权限。 | 请确保该文件可访问。 |
| 身份验证 | 文件格式 | 给定文件的格式不正确。 | 请确保文件的格式正确。 支持的文件类型为 .pfx 和 pem。 | 请确保该文件是有效的证书文件。 |
| 身份验证 | 未经授权 | 代理无法针对具有给定凭据的 IoT 中心进行身份验证。 | 验证 LocalConfiguration 文件中的身份验证配置, 浏览身份验证配置并确保所有详细信息都正确, 验证文件中的密码是否与已验证身份匹配。 | 在 Authentication 中验证身份验证配置, 浏览身份验证配置, 确保所有详细信息都正确, 然后验证文件中的密码是否与已验证的标识匹配。
| 身份验证 | 未找到 | 已找到设备/模块。 | 验证身份验证配置-确保主机名正确, 设备在 IoT 中心存在并且具有 azureiotsecurity 的克隆模块。 |  验证身份验证配置-确保主机名正确, 设备在 IoT 中心存在并且具有 azureiotsecurity 的克隆模块。 |
| 身份验证 | 缺少配置 | *身份验证*文件中缺少配置。 错误消息应声明缺少哪个密钥。 | 将缺少的密钥添加到*LocalConfiguration*文件。| 将缺少的密钥添加到*Authentication*文件中。有关详细信息, 请参阅[c # localconfig](azure-iot-security-local-configuration-csharp.md) 。 |
| 身份验证 | 无法分析配置 | 无法分析配置值。 错误消息应说明无法分析哪一项。 无法分析配置值, 因为该值不是预期的类型, 或者值超出了范围。 |修复**LocalConfiguration**文件中的项的值。 |在**Authentication .config**文件中修复密钥的值以匹配架构, 有关详细信息, 请参阅[cs-localconfig](azure-iot-security-local-configuration-c.md) 。|
|

## <a name="restart-the-agent"></a>重新启动代理
1. 查找并修复安全代理错误后, 请运行以下命令重新启动代理:

    ```bash
    systemctl restart ASCIoTAgent.service
    ```
2. 如果需要, 请重复前面的过程, 以强制停止代理并检索错误 (如果代理继续失败启动过程)。 

## <a name="next-steps"></a>后续步骤
- 阅读 Azure 安全中心以获取 IoT 服务[概述](overview.md)
- 详细了解用于 IoT[体系结构](architecture.md)的 Azure 安全中心
- 为 IoT[服务](quickstart-onboard-iot-hub.md)启用 Azure 安全中心
- 阅读 Azure 安全中心以获取 IoT 服务[常见问题解答](resources-frequently-asked-questions.md)
- 了解如何访问[原始安全数据](how-to-security-data-access.md)
- 了解[建议](concept-recommendations.md)
- 了解安全[警报](concept-security-alerts.md)
