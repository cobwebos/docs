---
title: Azure 堆栈上的应用服务更新 2 发行说明 |Microsoft 文档
description: 了解什么是在更新中两个用于 Azure 堆栈上的 App Service、 已知的问题和下载更新的位置。
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: anwestg
ms.reviewer: brenduns
ms.openlocfilehash: 8e1790b7d0b3a210a9142fc8580ff8ed4d64311c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
ms.locfileid: "34360413"
---
# <a name="app-service-on-azure-stack-update-2-release-notes"></a>App Service 上 Azure 堆栈 update 2 发行说明

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

这些发行说明描述的改进和在 Azure App Service 中的修补程序，Azure 堆栈 Update 2 和任何已知的问题。 已知问题分为与部署、更新过程直接相关的问题，以及内部版本（安装后）的问题。

> [!IMPORTANT]
> 将 1804年更新应用于你的 Azure 堆栈集成系统，或在部署 Azure 应用程序服务 1.2 之前部署的最新的 Azure 堆栈开发工具包。
>
>

## <a name="build-reference"></a>内部版本参考

Azure 堆栈 Update 2 生成号上的应用程序服务已**72.0.13698.10**

### <a name="prerequisites"></a>必备组件

> [!IMPORTANT]
> 基于 Azure Stack 的 Azure 应用服务的新部署现在要求提供[三使用者通配型证书](azure-stack-app-service-before-you-get-started.md#get-certificates)，因为目前在 Azure 应用服务中处理适用于 Kudu 的 SSO 的方式已改进。 新的使用者是 **\*。 sso.appservice。\<区域\>。\<domainname\>。\<扩展\>**
>
>

在开始部署之前，请参阅[准备工作文档](azure-stack-app-service-before-you-get-started.md)。

### <a name="new-features-and-fixes"></a>新功能和修复

Azure 堆栈 Update 2 上的 azure 应用程序服务包括以下改进和修补程序：

- 针对**应用服务租户、管理员、函数门户和 Kudu 工具**的更新。 与 Azure Stack 门户 SDK 版本一致。

- 更新到核心服务，以提高可靠性和错误消息启用更容易诊断常见的问题。

- **针对以下应用程序框架和工具的更新**：
  - 添加的.Net Framework 4.7.1
  - 增加了 **Node.JS** 版本：
    - NodeJS 6.12.3
    - NodeJS 8.9.4
    - NodeJS 8.10.0
    - NodeJS 8.11.1
  - 增加了 **NPM** 版本：
    - 5.6.0
  - 更新.NET Core 组件可以使用在公共云中的 Azure App Service 一致。
  - 更新的 Kudu

- 部署的自动交换槽功能已启用-[配置自动交换](https://docs.microsoft.com/azure/app-service/web-sites-staged-publishing#configure-auto-swap)

- 在生产功能已启用-中的测试[在生产中测试的简介](https://azure.microsoft.com/resources/videos/introduction-to-azure-websites-testing-in-production-with-galin-iliev/)

- 已启用-azure 函数代理[工作与 Azure 函数代理](https://docs.microsoft.com/en-us/azure/azure-functions/functions-proxies)

- App Service 管理扩展 UX 支持添加：
  - 密钥轮换
  - 证书旋转
  - 系统凭据旋转
  - 连接字符串旋转

### <a name="known-issues-post-installation"></a>已知问题（安装后）

- 工作人员都无法访问文件服务器时在现有的虚拟网络中部署应用程序服务和文件服务器才私有网络上可用。

如果你选择要部署到现有的虚拟网络与内部 IP 地址连接到你的文件服务器，必须添加出站安全规则，启用辅助子网和文件服务器之间的 SMB 流量。 若要执行此操作，请转到在管理门户中 WorkersNsg 并添加出站安全规则具有以下属性：
 * 源： 任何
 * 源端口范围: *
 * 目标： IP 地址
 * 目标 IP 地址范围： Ip 范围为文件服务器
 * 目标端口范围： 445
 * 协议： TCP
 * 操作： 允许
 * 优先级： 700
 * 名称： Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>云管理员在操作基于 Azure Stack 的 Azure 应用服务时的已知问题

中的文档，请参阅[Azure 堆栈 1804年发行说明](azure-stack-update-1804.md)

## <a name="next-steps"></a>后续步骤

- 有关 Azure 应用服务的概述，请参阅[基于 Azure Stack 的 Azure 应用服务概述](azure-stack-app-service-overview.md)。
- 若要详细了解如何完成基于 Azure Stack 的应用服务的部署准备，请参阅[基于 Azure Stack 的应用服务的准备工作](azure-stack-app-service-before-you-get-started.md)。
