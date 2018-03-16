---
title: "在 Azure 堆栈更新一个 app Service |Microsoft 文档"
description: "了解什么是 Azure 堆栈上的 App Service 的其中一个更新中的已知的问题，以及在何处下载更新。"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2018
ms.author: anwestg
ms.reviewer: brenduns
ms.openlocfilehash: 0c33c8fdefbb27ba8414e58bed1b42ee7aaba88a
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
---
# <a name="app-service-on-azure-stack-update-one-release-notes"></a>Azure 堆栈上的应用服务更新一个发行说明

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

这些发行说明描述的改进和在 Azure App Service 中的修补程序，Azure 堆栈 Update 1 和任何已知的问题。 已知的问题划分为与部署、 更新过程和生成 （安装后） 的问题直接相关的问题。

> [!IMPORTANT]
> 将 1802年更新应用于你的 Azure 堆栈集成系统，或在部署 Azure App Service 之前部署的最新的 Azure 堆栈开发工具包。
>
>

## <a name="build-reference"></a>生成参考

Azure 堆栈 Update 1 生成号上的应用程序服务已**69.0.13698.9**

### <a name="prerequisites"></a>必备组件

> [!IMPORTANT]
> Azure 堆栈上的 azure App Service 表现在要求[三个使用者通配证书](azure-stack-app-service-before-you-get-started.md#get-certificates)由于处理的方式在其中 Kudu 的 SSO 现在在 Azure App Service 中的改进。  新的使用者是 * * *。 sso.appservice。<region>。<domainname>.<extension>**
>
>

请参阅[之前要开始文档](azure-stack-app-service-before-you-get-started.md)在开始部署之前。

### <a name="new-features-and-fixes"></a>新功能和修复

Azure 堆栈 Update 1 上的 azure 应用程序服务包括以下改进和修补程序：

- **高可用性的各种 Azure App Service** -跨部署 Azure 堆栈 1802年启用的更新的工作负荷故障域。  因此 App Service 基础结构是能够具有容错能力为跨容错域将已部署的。  默认情况下的 Azure App Service 的所有新部署将拥有此功能但对于在 Azure 堆栈 1802年之前完成的部署更新正在应用，请参阅到[应用服务容错域文档](azure-stack-app-service-fault-domain-update.md)

- **在现有虚拟网络中部署**-客户现在可在现有的虚拟网络中部署 Azure 堆栈上的 App Service。  在现有虚拟网络中部署使客户能够连接到 SQL Server 和文件服务器，所需的 Azure App Service，通过私有端口。  在部署过程客户可以选择以将部署在现有的虚拟网络中，但是[必须创建子网以供 App Service](azure-stack-app-service-before-you-get-started.md#virtual-network)部署之前。

- 更新到**应用服务租户的管理员，函数门户和 Kudu 工具**。  与 Azure 堆栈门户 SDK 版本一致。

- **以下应用程序框架和工具的更新**:
    - 添加**.Net 核心 2.0**支持
    - 添加**Node.JS**版本：
        - 6.11.2
        - 6.11.5
        - 7.10.1
        - 8.0.0
        - 8.1.4
        - 8.4.0
        - 8.5.0
        - 8.7.0
        - 8.8.1
        - 8.9.0
    - 添加**NPM**版本：
        - 3.10.10
        - 4.2.0
        - 5.0.0
        - 5.0.3
        - 5.3.0
        - 5.4.2
        - 5.5.1
    - 添加**PHP**更新：
        - 5.6.32
        - 7.0.26 （x86 和 x64）
        - 7.1.12 （x86 和 x64）
    - 更新**Git for Windows**到 v 2.14.1
    - 更新**Mercurial**到 v4.5.0

  - 增加了对支持**仅 HTTPS**内应用程序服务租户门户中的自定义域功能的功能。 

  - 添加的验证的自定义存储选取器中，Azure 函数的存储连接 

#### <a name="fixes"></a>修复项

- 在创建时的脱机的部署包，客户不会再收到拒绝访问错误消息时从应用程序服务安装程序中打开文件夹

- 在应用程序服务租户门户中的自定义域功能中工作时，请解决问题。

- 阻止客户在安装过程中使用保留的管理员名称

- 启用了与 App Service 部署**加入域**文件服务器

- 改进的检索 Azure 堆栈根证书在脚本和现在验证在 App Service 安装程序中的根证书。

- 固定不正确的状态时订阅是返回到 Azure 资源管理器中删除该 Microsoft.Web 命名空间中的包含的资源。

### <a name="known-issues-with-the-deployment-process"></a>在部署过程的已知的问题

- 没有部署的 Azure App Service 上 Azure 堆栈 Update 1 已知的问题。

### <a name="known-issues-with-the-update-process"></a>在更新过程的已知的问题

- 没有进行更新的 Azure App Service 上 Azure 堆栈 Update 1 已知的问题。

### <a name="known-issues-post-installation"></a>已知的问题 （安装后）

- 没有安装的 Azure App Service 上 Azure 堆栈 Update 1 已知的问题。

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>运行 Azure 堆栈上的 Azure App Service 的云管理员的已知的问题

中的文档，请参阅[Azure 堆栈 1802年发行说明](azure-stack-update-1802.md)

## <a name="see-also"></a>另请参阅

- 有关 Azure App Service 的概述，请参阅[Azure 堆栈概述 Azure App Service](azure-stack-app-service-overview.md)。
- 有关如何准备部署 Azure 堆栈上的应用程序服务的详细信息，请参阅[开始使用 Azure 堆栈上的 App Service 之前](azure-stack-app-service-before-you-get-started.md)。
