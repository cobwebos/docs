---
title: "验证 Azure VNET 是否与 Azure RemoteApp 一起使用 | Microsoft Docs"
description: "了解如何确保 Azure VNET 已准备好与 Azure RemoteApp 一起使用"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: b573ba02-4587-4be5-9821-27bd891a73b2
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: e4d94d3f9736378d93e93be6645ed04ade763ca3
ms.openlocfilehash: 30d18bdbdc6293bab5a8876fb1e503e125829e2e


---
# <a name="validate-the-azure-vnet-to-use-with-azure-remoteapp"></a>验证 Azure VNET 是否与 Azure RemoteApp 一起使用
> [!IMPORTANT]
> 正在中断 Azure RemoteApp。 阅读 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 了解详细信息。
> 
> 

在将 Azure VNET 与 Azure RemoteApp 一起使用之前，你可能需要验证 VNET。 这有助于防止连接问题。

若要验证你的 Azure VNET，请执行以下操作︰

1. 在你想要与 Azure RemoteApp 一起使用的 Azure VNET 的子网内，创建 Azure 虚拟机。
2. 通过使用管理门户中的“**连接**”选项，连接到 VM。
3. 将虚拟机加入到你想要与 Azure RemoteApp 一起使用的同一域中。 如果你正在创建连接到你的本地网络的混合集合，请将虚拟机加入到你的本地域。

如果此操作成功，Azure VNET 就准备好与 RemoteApp 一起使用了。

有关端到端混合集合工作流的详细信息，请参阅以下文章︰

* [如何规划 Azure RemoteApp 虚拟网络](remoteapp-planvnet.md)
* [创建混合集合](remoteapp-create-hybrid-deployment.md)
* [将 Azure RemoteApp 集合部署到 Azure 虚拟网络（带有对 ExpressRoute 的支持）](http://blogs.msdn.com/b/rds/archive/2015/04/23/deploy-azure-remoteapp-collection-to-your-azure-virtual-network-with-support-for-expressroute.aspx)




<!--HONumber=Dec16_HO2-->


