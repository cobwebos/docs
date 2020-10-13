---
title: 概念-API 管理
description: '了解 API 管理如何保护在 Azure VMware 解决方案虚拟机上运行的 Api (Vm) '
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 346d0f795c3d19b115ced771991263cce2104217
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91262971"
---
# <a name="api-management-to-publish-and-protect-apis-running-on-azure-vmware-solution-based-vms"></a>用于发布和保护在基于 Azure VMware 解决方案的虚拟机上运行的 Api 的 API 管理

Microsoft Azure [API 管理](https://azure.microsoft.com/services/api-management/) 使开发人员和 DevOps 团队能够安全地将其发布到内部或外部使用者。

尽管在多个 Sku 中提供，但只有开发人员和高级 Sku 允许 Azure 虚拟网络集成，才能发布在 Azure VMware 解决方案工作负荷上运行的 Api。 这两个 Sku 安全地启用了 API 管理服务和后端之间的连接。 开发人员 SKU 适用于开发和测试，而高级 SKU 用于生产部署。

对于在 Azure VMware 解决方案虚拟机上运行的后端服务 (Vm) ，默认情况下，API 管理中的配置与本地后端服务相同。 对于内部部署和外部部署，API 管理会将负载均衡器的虚拟 IP (VIP) 配置为后端终结点，以便在 Azure VMware 解决方案端将后端服务器置于 NSX 负载平衡器之后。

## <a name="external-deployment"></a>外部部署

外部部署使用公用终结点发布外部用户使用的 Api。 开发人员和 DevOps 工程师可以通过 Azure 门户或 PowerShell 以及 API 管理开发人员门户来管理 Api。

外部部署关系图显示了在顶部)  (显示的整个过程和参与方。 执行组件如下：

- **管理员 () ：** 表示管理员或 DevOps 团队，该团队通过 PowerShell 或 Azure DevOps 等 Azure 门户和自动化机制来管理 Azure VMware 解决方案。

- **用户：**  表示公开的 Api 的使用者，同时表示使用 Api 的用户和服务。

流量流通过 API 管理实例，该实例可提取连接到中心虚拟网络的后端服务。 ExpressRoute 网关将流量路由到 ExpressRoute Global Reach 通道，并达到 NSX 负载均衡器将传入流量分发到不同的后端服务实例。

API 管理具有 Azure 公共 API，建议激活 Azure DDOS 防护服务。 

:::image type="content" source="media/api-management/external-deployment.png" alt-text="外部部署-Azure VMware 解决方案的 API 管理":::


## <a name="internal-deployment"></a>内部部署

内部部署可发布内部用户或系统使用的 Api。 DevOps 团队和 API 开发人员使用与外部部署中相同的管理工具和开发人员门户。

内部部署可以 [与 Azure 应用程序关一起使用](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md) ，为 API 创建一个公共且安全的终结点，以便利用其功能并创建支持不同方案的混合部署。  API 利用其功能，并创建支持不同方案的混合部署。

* 使用与内部和外部使用者相同的 API 管理资源。

* 使用单个 API 管理资源，其中包含定义的 Api 子集，并且可供外部使用者使用。

* 提供了一种简单的方法来从公共 internet 打开和关闭对 API 管理的访问。

下面的部署关系图显示了可以是内部或外部的使用者，每个类型访问相同的或不同的 Api。

在内部部署中，Api 公开到相同的 API 管理实例。 在 API 管理之前，应用程序网关与 Azure Web 应用程序防火墙一起部署 (WAF) 功能已激活，并使用一组 HTTP 侦听器 (s) 和规则筛选流量，只公开 Azure VMware 解决方案上运行的后端服务的子集。

* 内部流量通过 ExpressRoute 网关路由到 Azure 防火墙，然后将其路由到 API 管理（如果已建立流量规则或直接通过 API 管理）。  

* 外部流量通过应用程序网关进入 Azure，该应用程序网关使用外部保护层进行 API 管理。


:::image type="content" source="media/api-management/internal-deployment.png" alt-text="外部部署-Azure VMware 解决方案的 API 管理":::