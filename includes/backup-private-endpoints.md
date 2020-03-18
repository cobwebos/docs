---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: a3bae0ce3e6ebcf64936d0ca4af4fb702e5ea404
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2020
ms.locfileid: "79137018"
---
现在可以使用[专用终结点](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)将数据从虚拟网络内的服务器安全地备份到恢复服务保管库。 专用终结点为保管库使用 VNET 地址空间中的 IP。 虚拟网络中的资源与保管库之间的网络流量将通过虚拟网络和 Microsoft 主干网络上的专用链接传输。 这样就不会从公共 Internet 泄露信息。 专用终结点可用于备份和还原在 Azure VM 中运行的 SQL 数据库和 SAP HANA 数据库。 它还可以通过 MARS 代理用于本地服务器。

Azure VM 备份不需要 Internet 连接，因此不需要专用终结点允许网络隔离。

>[!NOTE]
> 此功能目前面市不久。 如果你有兴趣将专用终结点用于 Azure 备份，请填写[此调查](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u)并向 azbackupnetsec@microsoft.com 发送电子邮件。 若要使用此功能，需要从 Azure 备份服务得到批准。
