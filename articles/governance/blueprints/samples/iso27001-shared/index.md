---
title: 示例 - ISO 27001 共享服务蓝图 - 概述
description: ISO 27001 共享服务蓝图示例的概述和体系结构。
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: e723fab12c6ba88ec5698a697ba1ddd774eed7e2
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2019
ms.locfileid: "71000513"
---
# <a name="overview-of-the-iso-27001-shared-services-blueprint-sample"></a>ISO 27001 共享服务蓝图示例的概述

ISO 27001 共享服务蓝图示例提供了一组符合标准的基础结构模式和策略防护机制，以便帮助通过 ISO 27001 认证。 此蓝图帮助客户部署基于云的体系结构，以便为有认证或符合性要求的方案提供解决方案。

[ISO 27001 应用服务环境/SQL 数据库工作负荷](../iso27001-ase-sql-workload/index.md)蓝图示例扩展了此示例。

## <a name="architecture"></a>体系结构

ISO 27001 共享服务蓝图示例在 Azure 中部署一个基础结构，该基础结构可供组织用来基于虚拟数据中心 (VDC) 方法托管多个工作负荷。
VDC 是一套行之有效的参考体系结构、自动化工具和参与模型，由 Microsoft 用于其最大的企业客户。 共享服务蓝图示例基于下面所示的完全原生 Azure VDC 环境。

![ISO 27001 共享服务蓝图示例设计](../../media/sample-iso27001-shared/iso27001-shared-services-blueprint-sample-design.png)

此环境包括多项 Azure 服务，这些服务用于根据 ISO 27001 标准提供安全的、全面受监视的、面向企业的共享服务基础结构。 此环境包括：

- [基于角色的访问控制](../../../../role-based-access-control/overview.md) (RBAC) 角色，用于从控制平面角度分离职责。 三个角色是在部署任何基础结构之前定义的：
  - NetOps 角色有权管理网络环境，包括防火墙设置、NSG 设置、路由和其他网络功能
  - SecOps 角色具有部署和管理 [Azure 安全中心](../../../../security-center/security-center-intro.md)、定义 [Azure 策略](../../../policy/overview.md)的必要权限，以及其他与安全相关的权限
  - SysOps 角色具有在订阅内定义 [Azure 策略](../../../policy/overview.md)、为整个环境管理 [Log Analytics](../../../../azure-monitor/overview.md) 的必要权限，以及其他操作权限
- [Log Analytics](../../../../azure-monitor/overview.md) 作为第一个 Azure 服务进行部署，以便确保从开始安全部署起所有操作和服务都记录到一个中心位置
- 一个虚拟网络，它支持用于连接回本地数据中心的子网、用于 Internet 连接的入口和出口堆叠、使用 NSG 和 ASG 进行完全微分段的共享服务子网，其中包含：
  - 一个用于管理目的的 Jumpbox 或堡垒主机，只能通过入口堆叠子网中部署的 [Azure 防火墙](../../../../firewall/overview.md)访问
  - 两个运行 Active Directory 域服务 (ADDS) 和 DNS 的虚拟机，只能通过 Jumpbox 访问，可以配置为仅通过 VPN 或 [ExpressRoute](../../../../expressroute/expressroute-introduction.md) 连接来复制 AD（不按蓝图部署）
  - 使用 [Azure 网络观察程序](../../../../network-watcher/network-watcher-monitoring-overview.md)和标准 DDoS 保护
- 一个 [Azure Key Vault](../../../../key-vault/key-vault-overview.md) 实例，用于托管对共享服务环境中部署的 VM 使用的机密

所有这些元素遵守 [Azure 体系结构中心 - 参考体系结构](/azure/architecture/reference-architectures/)中发布的行之有效的做法。

> [!NOTE]
> ISO 27001 共享服务基础结构奠定了适用于工作负荷的基础体系结构的基础。
> 你仍需要部署此基础体系结构后面的工作负荷。

有关详细信息，请参阅[虚拟数据中心文档](/azure/architecture/vdc/)。

## <a name="next-steps"></a>后续步骤

你已查看了 ISO 27001 共享服务蓝图示例的概述和体系结构。
接下来，请访问以下文章，了解控制映射以及如何部署此示例：

> [!div class="nextstepaction"]
> [ISO 27001 共享服务蓝图 - 控制映射](./control-mapping.md)
> [ISO 27001 共享服务蓝图 - 部署步骤](./deploy.md)

有关蓝图和如何使用这些蓝图的更多文章：

- 了解[蓝图生命周期](../../concepts/lifecycle.md)。
- 了解如何使用[静态和动态参数](../../concepts/parameters.md)。
- 了解如何自定义[蓝图排序顺序](../../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../../concepts/resource-locking.md)。
- 了解如何[更新现有分配](../../how-to/update-existing-assignments.md)。