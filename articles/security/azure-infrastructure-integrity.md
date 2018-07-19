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
ms.openlocfilehash: 867bc66a68bec662153d8336e649cf46df02f101
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2018
ms.locfileid: "37901310"
---
# <a name="azure-infrastructure-integrity"></a>Azure 基础结构完整性

## <a name="software-installation"></a>软件安装
安装在 Azure 环境中的软件堆栈中的所有组件都是按照 Microsoft 的安全开发生命周期 (SDL) 流程自定义生成的。 所有软件组件（包括 OS 映像和 SQL 数据库）都在更改和发布管理过程中进行部署。 在所有节点上运行的 OS 是 Windows Server 2008 或 Windows Server 2012 的自定义版本。 FC 根据其为 OS 设定的角色来选择确切的版本。 此外，主机 OS 不允许安装任何未经授权的软件组件。

某些 Microsoft Azure 组件（例如，RDFE、开发人员门户等等）作为 Azure 客户部署在来宾 OS 上运行的来宾 VM 上。

## <a name="virus-scans-on-builds"></a>生成时的病毒扫描
Azure 软件组件（包括 OS）生成必须使用 Microsoft Endpoint Protection (MEP) 防病毒工具进行病毒扫描。 每次病毒扫描都会在关联的生成目录中创建一个日志，详细说明扫描的内容和扫描结果。 病毒扫描是 Azure 中每个组件的生成源代码的一部分。 如果未对代码进行干净且成功的病毒扫描，就不会将其移至生产环境中。 一旦发现任何问题，就会将生成冻结，并提交给 Microsoft Security 的安全团队，以确定在生成的哪一处混入了“未授权”代码。

## <a name="closedlocked-environment"></a>封闭/锁定的环境
默认情况下，Azure 基础结构节点和来宾 VM 上不会创建任何用户帐户。 此外，默认的 Windows 管理员帐户也处于禁用状态。 Microsoft Azure Live Support (WALS) 的管理员经过适当的身份验证后，可以登录这些计算机并管理 Azure 生产网络以进行紧急维修。

## <a name="microsoft-azure-sql-database-authentication"></a>Microsoft Azure SQL 数据库身份验证
与 SQL Server 的任何实现一样，必须严格控制用户帐户管理。 Microsoft Azure SQL 数据库仅支持 SQL Server 身份验证。 此外，还应使用具有强密码并配置有特定权限的用户帐户来补充客户的数据安全模型。

## <a name="firewallacls-between-msft-corpnet-and-microsoft-azure-cluster"></a>MSFT CorpNet 与 Microsoft Azure 群集之间的防火墙/ACL
服务平台与 MS 企业网络之间的 ACL/防火墙可防止未经授权的内部人员访问 Microsoft Azure SQL 数据库。 此外，只有来自 Microsoft CorpNet 的 IP 地址范围的用户才能访问 WinFabric 平台管理终结点。

## <a name="firewallacls-between-nodes-in-an-azure-sql-db-cluster"></a>Azure SQL DB 群集节点之间的防火墙/ACL
作为额外保护以及深入防御策略的一部分，已在 Microsoft Azure SQL DB 群集中的节点之间实施 ACL/防火墙。 WinFabric 平台群集内的所有通信以及所有正在运行的代码都是可信的。

## <a name="custom-mas-watchdogs"></a>自定义 MA（监视器）
Microsoft Azure SQL 数据库使用称为监视器的自定义 MA 来监视 Microsoft Azure SQL DB 群集的运行状况。

## <a name="web-protocols"></a>Web 协议

### <a name="role-instance-monitoring-and-restart"></a>角色实例监视和重启
Azure 确保部署的所有正在运行的角色（面向 Internet 的 Web 角色或后端处理辅助角色）都受到持续的运行状况监视，以确保这些角色有效且高效地提供已预配它们的服务。 如果某个角色由于托管应用程序中的严重故障或角色实例本身的基础配置问题而变得不正常，Microsoft Azure FC 将检测角色实例中的问题并启动纠正状态。

### <a name="compute-connectivity"></a>计算连接
Azure 确保可通过基于 Web 的标准协议来访问部署的应用程序/服务。 面向 Internet 的 Web 角色虚拟实例将具有外部 Internet 连接，并且可供 Web 用户直接访问。 后端处理辅助角色虚拟实例具有外部 Internet 连接，但不能由外部 Web 用户直接访问，目的是保护辅助角色代表可公开访问的 Web 角色虚拟实例执行的操作的敏感性和完整性。

## <a name="next-steps"></a>后续步骤
若要详细了解 Microsoft 如何保护 Azure 基础结构，请参阅：

- [Azure 设施、场地和物理安全性](azure-physical-security.md)
- [Azure 基础结构可用性](azure-infrastructure-availability.md)
- [Azure 信息系统的组件和边界](azure-infrastructure-components.md)
- [Azure 网络体系结构](azure-infrastructure-network.md)
- [Azure 生产网络](azure-production-network.md)
- [Microsoft Azure SQL 数据库安全功能](azure-infrastructure-sql.md)
- [Azure 生产操作和管理](azure-infrastructure-operations.md)
- [监视 Azure 基础结构](azure-infrastructure-monitoring.md)
- [保护 Azure 中的客户数据](azure-protection-of-customer-data.md)
