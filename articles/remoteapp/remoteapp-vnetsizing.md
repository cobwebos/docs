---
title: "Azure RemoteApp 中的 VNET 的大小调整信息 | Microsoft Docs"
description: "了解与 VNET 一起运行的 Azure RemoteApp 的 IP 地址要求"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: b6e1c4ba-0236-42b2-bced-69353bf211be
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 9375981db64ec4a1ae523e958423b5f5787cec33
ms.lasthandoff: 03/31/2017


---
# <a name="sizing-information-for-a-vnet-in-azure-remoteapp"></a>Azure RemoteApp 中的 VNET 的大小调整信息
> [!IMPORTANT]
> Azure RemoteApp 将于 2017 年 8 月 31 日停用。 阅读 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 了解详细信息。
> 
> 

当你将 Azure RemoteApp 与虚拟网络 (VNET) 一起使用时，RemoteApp 会使用子网内的 IP 地址。 根据你的 RemoteApp 服务规模，你需要确保你的子网具有足够可供 RemoteApp 虚拟机使用的 IP 地址。 虽然此大小调整指南并不能完美指定 RemoteApp 如何动态地向上和向下调整集合内的虚拟机，但是它可以帮助你估计子网范围。 这是相当重要的，因为一旦将某个 RemoteApp 服务置于 VNET 中，你就无法在不移除 RemoteApp 的情况下增加子网大小。

对于每个你希望以最大容量运行的 RemoteApp 集合，你应该有 100 个可用的 IP 地址。 例如，如果你在标准计划中有一个 RemoteApp 集合并且想要有最多 500 个用户，那么你应该为该集合提供 100 个 IP 地址。 同样，对于在有 800 个用户的基本计划中的 RemoteApp 集合，你需要提供 100 个 IP 地址。 如果你打算让用户数量少一些（小于最大数量），则可以减少每个集合所需的 IP 地址数。 最小的子网大小要求是 30 个 IP 地址 (/27)。

请检查以下信息以确保你的 VNET 配置正确且正常工作︰

* [从个人 VNET 迁移到 Azure VNET](remoteapp-migratevnet.md)
* [验证 Azure RemoteApp 是否与 Azure VNET 一起使用](remoteapp-vnet.md)


