---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 60707a8fbba4eb5ff7dbcf784fe29f9eb6b7d7d3
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2018
ms.locfileid: "50227222"
---
除了自动将客户端连接到主副本外，侦听器还可用于将只读工作负荷重定向到辅助副本。 这样使用可以提高整个解决方案的性能和可伸缩性。 有关详细信息，请参阅 [Use ReadIntent Routing with Azure Always On availability group listener](http://go.microsoft.com/fwlink/?LinkId=522515)（将 ReadIntent 路由与 Azure AlwaysOn 可用性组侦听器配合使用）。

> [!NOTE]
> 有关 Azure 侦听器的故障排除提示，请参阅 AlwaysOn 支持团队[博客](http://blogs.msdn.com/b/alwaysonpro/)中的 [Troubleshooting availability group listener in Azure](https://blogs.msdn.microsoft.com/alwaysonpro/2017/02/22/troubleshooting-internal-load-balancer-listener-connectivity-in-azure)（排查 Azure 中的可用性组侦听器问题）。
> 
> 

有关在 Azure 中使用 SQL Server 的详细信息，请参阅 [Azure 虚拟机上的 SQL Server](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)。

