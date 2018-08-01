---
title: Azure 基础结构完整性
description: 本文讨论 Azure 基础结构的完整性。
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: 65fe541f61389a2e52033cdaedcfcec4944faf35
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171453"
---
# <a name="azure-infrastructure-integrity"></a>Azure 基础结构完整性

## <a name="software-installation"></a>软件安装
安装在 Azure 环境中的软件堆栈中的所有组件都是按照 Microsoft 的安全开发生命周期 (SDL) 流程自定义生成的。 所有软件组件（包括操作系统 (OS) 映像和 SQL 数据库）都在变更管理和发布管理过程中进行部署。 在所有节点上运行的 OS 是 Windows Server 2008 或 Windows Server 2012 的自定义版本。 结构控制器 (FC) 根据其为 OS 设定的角色来选择确切的版本。 此外，主机 OS 不允许安装任何未经授权的软件组件。

某些 Azure 组件作为 Azure 客户部署在来宾 OS 上运行的来宾 VM 上。

## <a name="virus-scans-on-builds"></a>生成时的病毒扫描
Azure 软件组件（包括 OS）生成必须使用终结点保护防病毒工具进行病毒扫描。 每次病毒扫描都会在关联的生成目录中创建一个日志，详细说明扫描的内容和扫描结果。 病毒扫描是 Azure 中每个组件的生成源代码的一部分。 如果未对代码进行干净且成功的病毒扫描，就不会将其移至生产环境中。 一旦发现任何问题，就会将生成冻结，并提交给 Microsoft Security 的安全团队，以确定在生成的哪一处混入了“未授权”代码。

## <a name="closed-and-locked-environment"></a>封闭和锁定的环境
默认情况下，Azure 基础结构节点和来宾 VM 上不会创建用户帐户。 此外，默认的 Windows 管理员帐户也处于禁用状态。 Azure Live Support 的管理员经过适当的身份验证后，可以登录这些计算机并管理 Azure 生产网络以进行紧急维修。

## <a name="azure-sql-database-authentication"></a>Azure SQL 数据库身份验证
与 SQL Server 的任何实现一样，必须严格控制用户帐户管理。 Azure SQL 数据库仅支持 SQL Server 身份验证。 若要补充客户的数据安全模型，还应使用具有强密码并配置有特定权限的用户帐户。

## <a name="acls-and-firewalls-between-the-microsoft-corporate-network-and-an-azure-cluster"></a>Microsoft 企业网络与 Azure 群集之间的 ACL 和防火墙
服务平台与 Microsoft 企业网络之间的访问控制列表 (ACL) 和防火墙可防止未经授权的内部人员访问 SQL 数据库实例。 此外，只有来自 Microsoft 企业网络的 IP 地址范围的用户才能访问 Windows Fabric 平台管理终结点。

## <a name="acls-and-firewalls-between-nodes-in-a-sql-database-cluster"></a>SQL 数据库群集节点之间的 ACL 和防火墙
作为额外保护以及深入防御策略的一部分，已在 SQL 数据库群集中的节点之间实施 ACL 和防火墙。 Windows Fabric 平台群集内的所有通信以及所有正在运行的代码都是可信的。

## <a name="custom-monitoring-agents"></a>自定义监视代理
SQL 数据库使用称为监视器的自定义监视代理 (MA) 来监视 SQL 数据库群集的运行状况。

## <a name="web-protocols"></a>Web 协议

### <a name="role-instance-monitoring-and-restart"></a>角色实例监视和重启
Azure 确保部署的所有正在运行的角色（面向 Internet 的 Web 角色或后端处理辅助角色）都受到持续的运行状况监视，以确保这些角色有效且高效地提供已预配它们的服务。 如果某个角色由于托管应用程序中的严重故障或角色实例本身的基础配置问题而变得不正常，FC 将检测角色实例中的问题并启动纠正状态。

### <a name="compute-connectivity"></a>计算连接
Azure 确保可通过基于 Web 的标准协议来访问部署的应用程序或服务。 面向 Internet 的 Web 角色的虚拟实例具有外部 Internet 连接，并且可供 Web 用户直接访问。 为了保护辅助角色代表可公开访问的 Web 角色虚拟实例执行的操作的敏感性和完整性，后端处理辅助角色的虚拟实例具有外部 Internet 连接，但不能由外部 Web 用户直接访问。

## <a name="next-steps"></a>后续步骤
若要详细了解 Microsoft 如何保护 Azure 基础结构，请参阅：

- [Azure 设施、场地和物理安全性](azure-physical-security.md)
- [Azure 基础结构可用性](azure-infrastructure-availability.md)
- [Azure 信息系统的组件和边界](azure-infrastructure-components.md)
- [Azure 网络体系结构](azure-infrastructure-network.md)
- [Azure 生产网络](azure-production-network.md)
- [Azure SQL 数据库安全功能](azure-infrastructure-sql.md)
- [Azure 生产运营和管理](azure-infrastructure-operations.md)
- [Azure 基础结构监视](azure-infrastructure-monitoring.md)
- [Azure 客户数据保护](azure-protection-of-customer-data.md)
