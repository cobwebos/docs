---
title: 使用 Azure Site Recovery 排查将 VMware VM 和物理服务器灾难恢复到 Azure 时的配置服务器问题 | Microsoft Docs
description: 本文提供用于部署配置服务器以便使用 Azure Site Recovery 将 VMware VM 和物理服务器灾难恢复到 Azure 的故障排除信息。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: 4faadc27648b0d944e61a4d390313a35b4ba8bfa
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2018
ms.locfileid: "52837726"
---
# <a name="troubleshoot-configuration-server-issues"></a>排查配置服务器问题

本文可帮助你排查在部署和管理 [Azure Site Recovery](site-recovery-overview.md) 配置服务器时遇到的问题。 为本地 VMware VM 和物理服务器设置使用 Site Recovery 灾难恢复到 Azure 时，将使用配置服务器。 

## <a name="installation-failures"></a>安装失败

| **错误消息** | **建议的操作** |
|--------------------------|------------------------|
|错误...未能加载帐户。 错误：System.IO.IOException:安装和注册 CS 服务器时无法从传输连接读取数据。| 确保在计算机上启用 TLS 1.0。 |

## <a name="registration-failures"></a>注册失败

可以使用 %ProgramData%\ASRLogs 文件夹中的 l<br/ogs 来调试注册失败。

可以通过检查 **%ProgramData%\ASRLogs** 文件夹中的日志来调试注册失败。

| **错误消息** | **建议的操作** |
|--------------------------|------------------------|
|**09:20:06**:InnerException.Type:SrsRestApiClientLib.AcsException,InnerException。<br>消息：ACS50008:SAML 令牌无效。<br>跟踪 ID：1921ea5b-4723-4be7-8087-a75d3f9e1072<br>相关 ID：62fea7e6-2197-4be4-a2c0-71ceb7aa2d97><br>时间戳：**2016-12-12 14:50:08Z<br>** | 确保系统时钟上的时间与本地时间之间的偏差不超过 15 分钟。 重新运行安装程序完成注册。|
|**09:35:27**尝试获取所选证书的所有灾难恢复保管库时出现 DRRegistrationException：引发了 Exception.Type:Microsoft.DisasterRecovery.Registration.DRRegistrationException，Exception.Message:ACS50008:SAML 令牌无效。<br>跟踪 ID: e5ad1af1-2d39-4970-8eef-096e325c9950<br>相关 ID: abe9deb8-3e64-464d-8375-36db9816427a<br>时间戳：**2016-05-19 01:35:39Z**<br> | 确保系统时钟上的时间与本地时间之间的偏差不超过 15 分钟。 重新运行安装程序完成注册。|
|06:28:45: 未能创建证书<br>06:28:45: 安装无法继续。 无法创建用于在 Site Recovery 中进行身份验证的证书。 重新运行安装程序 | 确保以本地管理员的身份运行安装程序。 |
