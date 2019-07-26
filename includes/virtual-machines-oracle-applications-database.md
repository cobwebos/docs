---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 8106b1a77f11874b1b13f41bfc9e62ea800afc98
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2019
ms.locfileid: "68361526"
---
### <a name="database-tier"></a>数据库层

数据库层包含应用程序的数据库实例。 数据库可以是 Oracle DB、Oracle RAC 或 Oracle Exadata 数据库系统。 

如果选择使用 Oracle DB, 则可以通过 Azure Marketplace 中提供的 Oracle DB 映像将数据库实例部署在 Azure 上。 或者, 你可以使用 Azure 与 OCI 之间的互连在 OCI 上部署 PaaS 模型中的 Oracle DB。

对于 Oracle RAC, 可以在 Azure 上的 CloudSimple 中部署 Oracle RAC, 或在 PaaS 模型中部署 OCI。 建议使用双节点 RAC 系统。 

最后, 对于 Exadata 系统, 请使用 OCI 互连并在 OCI 中部署 Exadata 系统。 上述体系结构图示显示了在两个子网的 OCI 中部署的 Exadata 系统。

对于生产方案, 请跨两个可用性区域 (如果在 Azure 中部署) 或两个可用性域 (在 OCI 中) 部署多个数据库实例。 使用 Oracle Active Data Guard 同步主数据库和备用数据库。

数据库层只接收来自中间层的请求。 建议设置网络安全组 (如果在 OCI 中部署数据库, 则为安全列表), 以便仅允许来自中间层的端口1521上的请求和来自堡垒服务器的端口 22 (出于管理原因)。

对于部署在 OCI 中的数据库, 必须使用连接到 FastConnect 线路的动态路由网关 (.DRG) 设置单独的虚拟云网络。