---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 06/01/2020
ms.author: danlep
ms.openlocfilehash: b9e275cbdaf264df3d0a40f032693fd93553e520
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84317707"
---
### <a name="database-tier"></a>数据库层

数据库层包含应用程序的数据库实例。 数据库可以是 Oracle DB、Oracle RAC 或 Oracle Exadata 数据库系统。 

如果选择使用 Oracle DB，则可以通过 Azure Marketplace 中提供的 Oracle DB 映像将数据库实例部署在 Azure 上。 或者，你可以使用 Azure 与 OCI 之间的互连在 OCI 上部署 PaaS 模型中的 Oracle DB。

对于 Oracle RAC，可以使用 PaaS 模型中的 OCI。 建议使用双节点 RAC 系统。 尽管可以在 IaaS 模型中部署 Azure 上的 Oracle RAC CloudSimple，但它不是 Oracle 支持的配置。 请参阅[适用于授权云环境的 Oracle 程序](http://www.oracle.com/us/corporate/pricing/authorized-cloud-environments-3493562.pdf)。

最后，对于 Exadata 系统，请使用 OCI 互连并在 OCI 中部署 Exadata 系统。 上述体系结构图示显示了在两个子网的 OCI 中部署的 Exadata 系统。

对于生产方案，请跨两个可用性区域（如果在 Azure 中部署）或两个可用性域（在 OCI 中）部署多个数据库实例。 使用 Oracle Active Data Guard 同步主数据库和备用数据库。

数据库层只接收来自中间层的请求。 建议设置网络安全组（如果在 OCI 中部署数据库，则为安全列表），以便仅允许来自中间层的端口1521上的请求和来自堡垒服务器的端口22（出于管理原因）。

对于部署在 OCI 中的数据库，必须使用连接到 FastConnect 线路的动态路由网关（.DRG）设置单独的虚拟云网络。