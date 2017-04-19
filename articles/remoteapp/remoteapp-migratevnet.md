---
title: "如何从 RemoteApp VNET 迁移到 Azure VNET | Microsoft Docs"
description: "了解如何从 RemoteApp VNET 迁移到 Azure VNET"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: baea5d29-353b-48f8-b47f-806f2163e067
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 10b5f4844a38fe97852dee8634e8cf54f1a23a1e
ms.lasthandoff: 03/31/2017


---
# <a name="how-to-migrate-a-hybrid-collection-from-a-remoteapp-vnet-to-an-azure-vnet"></a>如何将混合集合从 RemoteApp VNET 迁移到 Azure VNET
> [!IMPORTANT]
> Azure RemoteApp 将于 2017 年 8 月 31 日停用。 阅读 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 了解详细信息。
> 
> 

好消息！ 我们已允许直接将混合 RemoteApp 集合部署到现有的 Azure 虚拟网络 (VNET)，而不用创建特定于 RemoteApp 的 VNET。 这样就可使用最新的 VNET 功能（例如 ExpressRoute）并授予混合集合对部署到该 VNET 的其他 Azure 服务和虚拟机的直接网络访问权限。  （这样可以使你获取更佳性能，设置比 VNET-到-VNET 的配置更为简单。）

假设你已使用名为 *RemoteAppVNET* 的 RemoteApp VNET 创建名为 *OriginalCollection* 的混合 RemoteApp 集合。 以下是将其迁移到称为 *AzureVNET* 的新 Azure VNET 的步骤。

1. 在 [管理门户](http://manage.windowsazure.com/) 中的“**网络**”选项卡上，使用用于 *RemoteAppVNET* 的相同位置、DNS 配置和地址空间（至少用于 *AzureVNET* 子集之一）的创建名为 *AzureVNET* 的 VNET。
2. 将 *AzureVNET* 配置到任意主机，或将网络连接到已加入 *OriginalCollection* 域的 Active Directory 部署。
3. 在“ **RemoteApps**”选项卡中创建名为“*新集合*”的 RemoteApp 集合。 （使用“**使用 VNet 创建**”，而非“**快速创建**”选项。）
4. 将 *NewCollection* 部署为配置到 *AzureVNET* 的子集。
5. 将 *NewCollection* 配置为使用用于 *OriginalCollection* 的相同映像和域加入信息。
6. 几个小时后，*NewCollection* 将显示在自己的集合列表中，且处于“活动”状态。

现在，如果无需将任何用户信息从原始集合迁移到新集合中，则在下一步中执行以下步骤:

1. 删除 *OriginalCollection*。
2. 删除 *RemoteAppVNET*。

大功告成！

或者，如果需要将用户信息从原始集合迁移到新集合中，则在下一步中执行以下步骤:

1. 向 [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com?subject=Azure%20RemoteApp%20user%20information%20migration) 发送电子邮件，其中包含你的 Azure 订阅 ID、原始集合的名称和新集合的名称，并要求他们迁移你的用户信息。
2. 在 2 个工作日内，RemoteApp 团队会将用户访问列表和所有的用户文档及用户设置从原始集合迁移到新集合。
3. 删除 *OriginalCollection*。
4. 删除 *RemoteAppVNET*。

大功告成！

如果存在任何问题，或需要特别帮助，请发送电子邮件至 [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com?subject=Azure%20RemoteApp%20VNET%20migration%20help)。


