---
title: 用于 IoT Linux 安全代理故障排除指南的 Azure 安全中心。微软文档
description: 使用 Azure 安全中心为 Linux 的 IoT 安全代理进行故障排除。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "68600561"
---
# <a name="security-agent-troubleshoot-guide-linux"></a>安全代理故障排除指南 (Linux)

本文介绍了如何解决安全代理启动过程中的潜在问题。

安装后立即启动 IoT 代理的 Azure 安全中心。 代理启动过程包括读取本地配置、连接到 Azure IoT 中心以及检索远程孪生配置。 这些步骤中的任何一个失败都可能导致安全代理失败。

在此故障排除指南中，您将了解如何：
> [!div class="checklist"]
> * 验证安全代理是否正在运行
> * 获取安全代理错误
> * 了解并修复安全代理错误 

## <a name="validate-if-the-security-agent-is-running"></a>验证安全代理是否正在运行

1. 要验证安全代理是否正在运行，请在安装代理后等待几分钟并运行以下命令。 
     <br>

    **C 代理**
    ```bash
    grep "ASC for IoT Agent initialized" /var/log/syslog
    ```
    **C# 代理**
    ```bash
    grep "Agent is initialized!" /var/log/syslog
    ```
2. 如果命令返回空行，则安全代理无法成功启动。    

## <a name="force-stop-the-security-agent"></a>强制停止安全代理 
如果安全代理无法启动，请使用以下命令停止代理，然后继续到下面的错误表：

```bash
systemctl stop ASCIoTAgent.service
```
## <a name="get-security-agent-errors"></a>获取安全代理错误
1. 通过运行以下命令检索安全代理错误：
    ```bash
    grep ASCIoTAgent /var/log/syslog
    ```
2. 获取安全代理错误命令检索由 IoT 代理的 Azure 安全中心创建的所有日志。 使用下表了解错误并采取正确的方法进行补救。 

> [!Note]
> 错误日志按时间顺序显示。 请务必记下每个错误的时间戳，以帮助进行补救。 

## <a name="restart-the-agent"></a>重新启动代理

1. 查找和修复安全代理错误后，请尝试通过运行以下命令重新启动代理。 
    ```bash
    systemctl restart ASCIoTAgent.service
    ```
1. 如果代理继续失败启动过程，请重复上一个进程以检索停止并检索错误。 

## <a name="understand-security-agent-errors"></a>了解安全代理错误

大多数安全代理错误以以下格式显示： 
```
Azure Security Center for IoT agent encountered an error! Error in: {Error Code}, reason: {Error sub code}, extra details: {error specific details}
```
| 错误代码 | 错误子代码 | 错误详细信息 | 修复 C | 修复 C# |
|:-----------|:---------------|:--------|:------------|:------------|
| 本地配置 | 缺少配置 | 本地配置文件中缺少配置。 错误消息应说明缺少哪个密钥。 | 将缺少的密钥添加到 /var/Local配置.json 文件，有关详细信息，请参阅[cs 本地配置引用](azure-iot-security-local-configuration-c.md)。| 将缺少的键添加到 General.config 文件，有关详细信息，请参阅[c#本地配置引用](azure-iot-security-local-configuration-csharp.md)。 |
| 本地配置 | 无法解析配置 | 无法分析配置值。 错误消息应说明无法解析的键。 无法分析配置值，因为该值不在预期类型中，或者该值不在范围范围内。 | 修复了 /var/Local配置.json 文件中密钥的值，以便它与本地配置架构匹配，有关详细信息，请参阅[c#本地配置引用](azure-iot-security-local-configuration-csharp.md)。 |  修复了 General.config 文件中的键的值，以便它与架构匹配，有关详细信息，请参阅[cs-localconfig 引用](azure-iot-security-local-configuration-c.md)。|
| 本地配置 | 文件格式 | 无法分析配置文件。 | 配置文件已损坏，下载代理并重新安装。 | |
| 远程配置 | 超时 | 代理无法在超时期间获取 Azureiotasecurity 模块孪生。 | 请确保身份验证配置正确，然后重试。 | 代理无法在超时期间获取 Azureiotasecurity 模块孪生。 | 请确保身份验证配置正确，然后重试。 |
| 身份验证 | 文件不存在 | 给定路径中的文件不存在。 | 请确保该文件存在于给定的路径中，或转到**Local配置.json**文件并更改**FilePath**配置。 | 请确保该文件存在于给定的路径中，或转到**身份验证.config**文件并更改**文件Path**配置。|
| 身份验证 | 文件权限 | 代理没有足够的权限来打开该文件。 | 为**asciotagent**用户授予给定路径中文件的读取权限。 | 确保文件可访问。 |
| 身份验证 | 文件格式 | 给定的文件格式不正确。 | 确保文件格式正确。 支持的文件类型是 .pfx 和 .pem。 | 确保该文件是有效的证书文件。 |
| 身份验证 | 未授权 | 代理无法使用给定的凭据对 IoT 中心进行身份验证。 | 验证本地配置文件中的身份验证配置，通过身份验证配置并确保所有详细信息都正确，验证文件中的机密是否与经过身份验证的标识匹配。 | 验证身份验证配置在身份验证.config 中，通过身份验证配置并确保所有详细信息都正确，然后验证文件中的机密是否与经过身份验证的标识匹配。
| 身份验证 | 未找到 | 找到设备/模块。 | 验证身份验证配置 - 确保主机名正确，设备存在于 IoT 中心中，并且具有 Azureiot 安全孪模块。 |  验证身份验证配置 - 确保主机名正确，设备存在于 IoT 中心中，并且具有 Azureiot 安全孪模块。 |
| 身份验证 | 缺少配置 | *身份验证.config 文件中*缺少配置。 错误消息应说明缺少哪个密钥。 | 将丢失的密钥添加到*本地配置.json*文件。| 将缺少的密钥添加到*身份验证.config*文件，有关详细信息，请参阅[c#本地配置引用](azure-iot-security-local-configuration-csharp.md)。 |
| 身份验证 | 无法解析配置 | 无法分析配置值。 错误消息应说明无法解析的键。 无法分析配置值，因为该值不是预期类型，或者该值不在范围范围内。 |修复**本地配置.json**文件中密钥的值。 |修复了**身份验证.config**文件中的密钥的值以匹配架构，有关详细信息，请参阅[cs-localconfig 引用](azure-iot-security-local-configuration-c.md)。|
|

## <a name="restart-the-agent"></a>重新启动代理
1. 查找和修复安全代理错误后，通过运行以下命令重新启动代理：

    ```bash
    systemctl restart ASCIoTAgent.service
    ```
2. 如果需要，请重复前面的进程以强制停止代理，并在代理继续失败的启动过程时检索错误。 

## <a name="next-steps"></a>后续步骤
- 阅读 Azure 安全中心，了解 IoT 服务[概述](overview.md)
- 了解有关 IoT[体系结构](architecture.md)的 Azure 安全中心
- 启用适用于 IoT[服务的](quickstart-onboard-iot-hub.md)Azure 安全中心
- 阅读 Azure 安全中心，了解 IoT 服务[常见问题解答](resources-frequently-asked-questions.md)
- 了解如何访问[原始安全数据](how-to-security-data-access.md)
- 了解[建议](concept-recommendations.md)
- 了解安全[警报](concept-security-alerts.md)
