---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 27b09ea9cc7ce3cf2559304a26d0c04f3ea30b54
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2018
ms.locfileid: "51262908"
---
除了自动将客户端连接到主副本外，侦听器还可用于将只读工作负荷重定向到辅助副本。 这样使用可以提高整个解决方案的性能和可伸缩性。 有关详细信息，请参阅 [Use ReadIntent Routing with Azure Always On availability group listener](https://go.microsoft.com/fwlink/?LinkId=522515)（将 ReadIntent 路由与 Azure AlwaysOn 可用性组侦听器配合使用）。

> [!NOTE]
> 有关 Azure 侦听器的故障排除提示，请参阅 AlwaysOn 支持团队[博客](https://blogs.msdn.com/b/alwaysonpro/)中的 [Troubleshooting availability group listener in Azure](https://blogs.msdn.microsoft.com/alwaysonpro/2017/02/22/troubleshooting-internal-load-balancer-listener-connectivity-in-azure)（排查 Azure 中的可用性组侦听器问题）。
> 
> 

有关在 Azure 中使用 SQL Server 的详细信息，请参阅 [Azure 虚拟机上的 SQL Server](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)。

