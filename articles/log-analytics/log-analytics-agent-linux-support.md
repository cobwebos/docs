---
title: Azure Log Analytics Linux 代理故障排除 | Microsoft Docs
description: 针对 Log Analytics Linux 代理的最常见问题，描述症状、原因和解决方法。
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2018
ms.author: magoedte
ms.openlocfilehash: 80d7e39b284554ebfa8cac4488e1663b3e3648e8
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
ms.locfileid: "29936469"
---
# <a name="how-to-troubleshoot-issues-with-the-linux-agent-for-log-analytics"></a>如何排查 Log Analytics Linux 代理问题

本文介绍如何解决可能遇到的 Log Analytics Linux 代理的相关错误，并提供可能的解决方案建议。

## <a name="issue-unable-to-connect-through-proxy-to-log-analytics"></a>问题：无法通过代理连接到 Log Analytics

### <a name="probable-causes"></a>可能的原因
* 在载入期间指定的代理不正确
* Log Analytics 和 Azure 自动化服务终结点不在数据中心的允许列表中 

### <a name="resolutions"></a>解决方法
1. 使用以下命令（启用了 `-v` 选项）通过适用于 Linux 的 OMS 代理重新载入到 Log Analytics 服务中。 这允许通过代理服务器连接到 OMS 服务的代理能够进行详细输出。 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <OMS Workspace ID> -s <OMS Workspace Key> -p <Proxy Conf> -v`

2. 请查看[更新代理设置](log-analytics-agent-manage.md#update-proxy-settings)部分，验证是否已将代理正确配置为通过代理服务器进行通信。    
* 仔细检查下列 Log Analytics 服务终结点是否在允许列表中：

    |代理资源| 端口 | 方向 |
    |------|---------|----------|  
    |*.ods.opinsights.azure.com | 端口 443| 入站和出站 |  
    |*.oms.opinsights.azure.com | 端口 443| 入站和出站 |  
    |* .blob.core.windows.net | 端口 443| 入站和出站 |  
    |* .azure-automation.net | 端口 443| 入站和出站 | 

## <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>问题：尝试载入时收到 403 错误

### <a name="probable-causes"></a>可能的原因
* Linux 服务器上的日期和时间不正确 
* 使用的工作区 ID 和工作区密钥不正确

### <a name="resolution"></a>解决方法

1. 使用 date 命令检查 Linux 服务器上的时间。 如果时间比当前时间快/慢 15 分钟，则载入失败。 若要纠正此问题，请更新 Linux 服务器的日期和/或时区。 
2. 验证你已安装了最新版本的 OMS Agent for Linux。  如果时间偏差导致了载入故障，最新版本现在会发出通知。
3. 请按照本主题前文所述的安装说明使用正确的工作区 ID 和工作区密钥重新载入。

## <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>问题：载入后，日志文件中立即显示 500 和 404 错误
这是第一次将 Linux 数据上传到 Log Analytics 工作区时发生的已知问题。 这不会影响发送的数据或服务体验。

## <a name="issue-you-are-not-seeing-any-data-in-the-azure-portal"></a>问题：Azure 门户中未显示任何数据

### <a name="probable-causes"></a>可能的原因

- 载入到 Log Analytics 服务失败
- 与 Log Analytics 服务的连接受阻
- 备份了 OMS Agent for Linux 数据

### <a name="resolutions"></a>解决方法
1. 通过检查是否存在以下文件，来检查是否已成功载入 Log Analytics 服务：`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`
2. 使用 `omsadmin.sh` 命令行指令重新载入
3. 如果使用代理，请参阅之前提供的代理解决方法步骤。
4. 在某些情况下，当适用于 Linux 的 OMS 代理无法与此服务通信时，代理上的数据会在整个缓冲区（大小 50 MB）中排队。 OMS Agent for Linux 应通过运行以下命令重新启动：`/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]`。 

    >[!NOTE]
    >此问题已在代理版本 1.1.0-28 及更高版本中解决。

