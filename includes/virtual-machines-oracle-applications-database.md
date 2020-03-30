---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 8106b1a77f11874b1b13f41bfc9e62ea800afc98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "68361526"
---
### <a name="database-tier"></a>数据库层

数据库层包含应用程序的数据库实例。 该数据库可以是 Oracle 数据库、Oracle RAC 或 Oracle Exadata 数据库系统。 

如果选择使用 Oracle DB，则数据库实例可以通过 Azure 应用商店上可用的 Oracle 数据库映像部署在 Azure 上。 或者，您可以使用 Azure 和 OCI 之间的互连在 OCI 上的 PaaS 模型中部署 Oracle DB。

对于 Oracle RAC，您可以在 IaaS 模型中的 Azure 云简单部署 Oracle RAC，或在 PaaS 模型中的 OCI 中部署 Oracle RAC。 建议您使用双节点 RAC 系统。 

最后，对于 Exadata 系统，请使用 OCI 互连并在 OCI 中部署 Exadata 系统。 上面的体系结构图显示了在 OCI 中跨两个子网部署的 Exadata 系统。

对于生产方案，跨两个可用性区域（如果在 Azure 中部署）或两个可用性域（在 OCI 中）部署数据库的多个实例。 使用 Oracle 活动数据防护同步主数据库和备用数据库。

数据库层仅接收来自中间层的请求。 建议您设置一个网络安全组（如果在 OCI 中部署数据库时的安全列表），以仅允许出于管理原因允许来自堡垒服务器的端口 1521 和端口 22 的请求。

对于在 OCI 中部署的数据库，必须使用连接到 FastConnect 电路的动态路由网关 （DRG） 设置单独的虚拟云网络。