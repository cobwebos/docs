---
title: "要列入在客户虚拟网络中部署的 Azure RemoteApp 的白名单中的端口和 URL 列表 | Microsoft Docs"
description: "了解你将需要配置哪些端口和 URL 才能通过 Azure RemoteApp 进行通信。"
services: remoteapp
documentationcenter: 
author: mghosh1616
manager: mbaldwin
ms.assetid: 5a001ff7-14c9-47fa-9b39-78fd5a5f0250
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 114ed651f914060818f10efac849e7f85a3da981
ms.openlocfilehash: f41396e4aa0c7b6cc2f1768e0c2ba3b95ee31cd3
ms.lasthandoff: 02/08/2017


---
# <a name="list-of-ports-and-urls-to-permit-access-for-azure-remoteapp-deployed-in-customer-virtual-network"></a>要允许在客户虚拟网络中部署的 Azure RemoteApp 访问的端口和 URL 列表
> [!IMPORTANT]
> 正在中断 Azure RemoteApp。 请参阅 [公告](https://go.microsoft.com/fwlink/?linkid=821148) ，了解详细信息。
> 
> 

如果你正在某个虚拟网络 (VNET) 中部署 Azure RemoteApp 云或混合集合，请查看以下端口信息。 有关虚拟网络的详细信息，请阅读[虚拟网络概述](../virtual-network/virtual-networks-overview.md)。 如果你已创建一个网络安全组 (NSG)，限制到集合中的虚拟网络资源的流量，请确保以下端口可访问，并得到虚拟网络上的安全策略的允许。 有关网络安全组的详细信息，请阅读[什么是网络安全组？(NSG)](../virtual-network/virtual-networks-nsg.md)

## <a name="azure-remoteapp-subnet-needs-access-to-these-endpoints-and-urls"></a>Azure RemoteApp 子网需要访问以下终结点和 URL：
* *.servicebus.windows.net
* *.servicebus.net
* https://*.remoteapp.windowsazure.com  
* https://www.remoteapp.windowsazure.com 
* https://*remoteapp.windowsazure.com  
* https://*.core.windows.net  
* 出站：TCP: TCP: 443、9351、9352、10101-10175 
* Optional – UDP: 10201-10275  

## <a name="azure-remoteapp-clients-need-access-to-these-endpoints-and-urls"></a>Azure RemoteApp 客户端需要访问以下终结点和 URL：
客户端的意思是人们用于连接到在 Azure RemoteApp 集合中部署的应用的桌面、设备等。

* https://telemetry.remoteapp.windowsazure.com  
* https://*.remoteapp.windowsazure.com（可选的 UDP 端口是用于此地址的） 
* https://login.windows.net  
* https://login.microsoftonline.com  
* https://www.remoteapp.windowsazure.com 
* https://*.core.windows.net  
* Outbound: TCP: 443  
* Optional - UDP: 3391 


