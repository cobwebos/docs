---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: a3bae0ce3e6ebcf64936d0ca4af4fb702e5ea404
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137018"
---
现在，您可以使用[专用终结点](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)安全地将数据从虚拟网络中的服务器备份到恢复服务保管库。 专用终结点使用来自 VNET 地址空间的 IP 来用于保管库。 虚拟网络中的资源和保管库之间的网络流量通过虚拟网络和 Microsoft 骨干网络上的专用链路传输。 这消除了公共互联网的曝光。 专用终结点可用于备份和还原在 Azure VM 中运行的 SQL 和 SAP HANA 数据库。 它还可用于使用 MARS 代理的本地服务器。

Azure VM 备份不需要互联网连接，因此不需要专用终结点来允许网络隔离。

>[!NOTE]
> 此功能目前正在早期使用。 如果您有兴趣使用专用终结点进行 Azure 备份，请填写[此调查](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u)并发送电子邮件至我们这里。 azbackupnetsec@microsoft.com 使用此功能的功能须经 Azure 备份服务批准。
