---
title: Azure 生产操作和管理
description: 本文提供有关 Microsoft Azure 生产网络管理和操作的一般说明。
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
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: dc389f5f5c155555deb860f041b15b0ea49ee416
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37101393"
---
# <a name="azure-production-operations-and-management"></a>Azure 生产操作和管理    
Azure 生产网络的管理和操作需要在 Azure 运营团队与 Azure SQL 数据库之间做出协调。 该环境中使用了多个系统和应用程序性能监视工具。 使用相应的工具来监视网络设备、服务器、服务和应用程序进程。

实施多种级别的监视、日志记录和报告，以确保安全执行 Microsoft Azure 环境中运行的服务，包括以下操作：

- 首先，Microsoft Azure 监视代理 (MA) 从多个位置（包括 FC 和根 OS）收集监视和诊断日志信息，并将其写入日志文件中。 它最终会将一部分摘要信息推送到预配置的 Azure 存储帐户中。 此外，监视和诊断服务 (MDS) 是一个独立的服务，它会读取各种监视和诊断日志数据，并汇总信息。 MDS 将信息写入集成式日志。 Azure 使用定制的 Azure 安全监视 (ASM)，这是 Azure 监视系统的一个扩展。 ASM 中的组件可以从平台中的各个位置观察、分析和报告安全相关的事件。
- Microsoft Azure SQL 数据库 WinFabric 平台为 Microsoft Azure SQL 数据库提供管理、部署、开发和操作监督服务。 它提供分布式的多步骤部署服务、运行状况监视、自动修复和服务版本合规功能。 它提供以下服务：

   - 服务建模功能和高保真开发环境（数据中心群集的成本高昂且能力不足）。
   - 一键式部署和升级工作流，用于执行服务启动和维护。
   - 运行状况报告和自动化修复工作流，可实现自我修复。
   - 跨分布式系统节点的实时监视、警报和调试工具。
   - 集中收集操作数据和指标，以提供分散式的根本原因分析和服务见解。
   - 用于部署、变更管理和监视的操作工具。
   - Microsoft Azure SQL 数据库 WinFabric 平台和监视器脚本持续实时运行并提供监视。

如果出现任何异常，将会激活事件响应过程，Azure 事件会审团队需遵循此过程。 相应的 Azure 支持人员会收到响应事件的通知。 在集中式票证系统中阐述和管理问题跟踪与解决方法。 根据保密协议 (NDA) 和客户请求提供系统正常运行时间指标。

## <a name="corporate-network-and-multi-factor-access-to-production"></a>通过企业网络和多重身份验证访问生产网络
企业网络用户群包括 Microsoft Azure 支持人员。 企业网络支持内部企业职能，并提供 Azure 客户支持人员所用的内部应用程序的访问权限。 企业网络在物理上和逻辑上与 Azure 生产网络分离。 Microsoft Azure 人员使用 Microsoft Azure 工作站和便携式计算机访问企业网络。 所有用户必须有一个 Active Directory (AD) 帐户（包括用户名和密码）才能访问企业网络资源。 使用 AD 帐户访问企业网络，这是帐户颁发给所有 Microsoft 员工、合同工与供应商，由 MSIT 管理。 唯一的用户标识符根据用户在 Microsoft 的雇佣关系状态来区分用户。

对内部 Azure 应用程序的访问通过身份验证和 Active Directory 联合身份验证服务 (ADFS) 进行控制。 ADFS 是 MSIT 托管的一个服务，它通过应用安全令牌和用户声明来提供企业网络用户的身份验证。 ADFS 使内部 Microsoft Azure 应用程序能够对 Microsoft 企业 AD 域中的用户进行身份验证。 若要从企业网络环境访问生产网络，用户必须完成多重身份验证。

## <a name="next-steps"></a>后续步骤
若要详细了解 Microsoft 如何保护 Azure 基础结构，请参阅：

- [Azure 设施、场地和物理安全性](azure-physical-security.md)
- [Azure 基础结构可用性](azure-infrastructure-availability.md)
- [Azure 信息系统的组件和边界](azure-infrastructure-components.md)
- [Azure 网络体系结构](azure-infrastructure-network.md)
- [Azure 生产网络](azure-production-network.md)
- [Microsoft Azure SQL 数据库安全功能](azure-infrastructure-sql.md)
- [监视 Azure 基础结构](azure-infrastructure-monitoring.md)
- [Azure 基础结构的完整性](azure-infrastructure-integrity.md)
- [保护 Azure 中的客户数据](azure-protection-of-customer-data.md)
