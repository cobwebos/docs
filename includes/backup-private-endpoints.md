---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: 024f70a21b36bffa662ba6f568adc8efbd0a909d
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81539441"
---
现在，您可以使用[专用终结点](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)安全地将数据从虚拟网络中的服务器备份到恢复服务保管库。 专用终结点使用来自 VNET 地址空间的 IP 来用于保管库。 虚拟网络中的资源和保管库之间的网络流量通过虚拟网络和 Microsoft 骨干网络上的专用链路传输。 这消除了公共互联网的曝光。 专用终结点可用于备份和还原在 Azure VM 中运行的 SQL 和 SAP HANA 数据库。 它还可用于使用 MARS 代理的本地服务器。

Azure VM 备份不需要互联网连接，因此不需要专用终结点来允许网络隔离。

>[!NOTE]
> 此功能目前可用性有限，在美国中西部、美国中南部、美国西部 2 和美国东部（后续其他 Azure 区域的可用性）可用。 如果您有兴趣使用专用终结点进行 Azure 备份，请填写[此调查](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u)并发送电子邮件至我们这里。 azbackupnetsec@microsoft.com 使用此功能的功能须经 Azure 备份服务批准。
