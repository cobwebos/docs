---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: 024f70a21b36bffa662ba6f568adc8efbd0a909d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81539441"
---
你现在可以使用[专用终结点](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)将数据从虚拟网络内的服务器安全地备份到恢复服务保管库中。 专用终结点使用保管库的 VNET 地址空间中的 IP。 虚拟网络和保管库中资源之间的网络流量将通过虚拟网络，并在 Microsoft 主干网络上传输专用链接。 这消除了公共 internet 的泄露。 专用终结点可用于备份和还原在 Azure Vm 中运行的 SQL 和 SAP HANA 数据库。 它还可用于使用 MARS 代理的本地服务器。

Azure VM 备份不需要 internet 连接，因此不需要专用终结点就能实现网络隔离。

>[!NOTE]
> 此功能目前处于有限的可用性，并在美国西部、美国中南部、美国西部2和美国东部提供（其他 Azure 区域中的可用性）。 如果你有兴趣使用 Azure 备份的专用终结点，请填写[此调查](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u)并向我们发送电子邮件。 azbackupnetsec@microsoft.com 使用此功能的功能可从 Azure 备份服务进行审批。
