---
title: PCI-DSS v2.0 版蓝图示例-控件映射
description: 控制支付卡行业数据安全标准 v2.0 蓝图示例到 Azure 策略和 RBAC 的映射。
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: conceptual
ms.service: blueprints
ms.openlocfilehash: c1e04cb2bfd5ae532b556ed53d585aae90c312e6
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73163061"
---
# <a name="control-mapping-of-the-pci-dss-v321-blueprint-sample"></a>控制 PCI DSS v 3.2.1 蓝图示例的映射

下面的文章详细介绍了如何将 Azure 蓝图-DSS v2.0 蓝图示例映射到 PCI-DSS 伏特版控制。 有关控件的详细信息，请参阅[PCI-DSS](https://www.pcisecuritystandards.org/documents/PCI_DSS_v3-2-1.pdf)v2.0。

以下映射适用于**PCI-DSS v 3.2.1： 2018**控件。 使用右侧的导航栏可直接跳转到特定的控制映射。 许多的映射控制措施都是使用 [Azure Policy](../../../policy/overview.md) 计划实施的。 若要查看完整计划，请在 Azure 门户中打开“策略”，并选择“定义”页。 然后，查找并选择 " **\[预览"\] AUDIT PCI v2.0 3.2.1：2018控件并部署特定的 VM 扩展以支持审核要求**内置策略计划。

> [!IMPORTANT]
> 下面的每个控件都与一个或多个 [Azure Policy](../../../policy/overview.md) 定义关联。 这些策略可以帮助你[评估控件的符合性](../../../policy/how-to/get-compliance-data.md)；但是，控件与一个或多个策略之间通常不是 1:1 或完全匹配。 因此，Azure Policy 中的符合性仅引用策略本身；这不确保你完全符合控件的所有要求。 此外，符合性标准包含目前未由任何 Azure Policy 定义处理的控件。 因此，Azure Policy 中的符合性只是整体符合性状态的部分视图。 此符合性蓝图示例的控件和 Azure Policy 定义之间的关联可能会随着时间的推移而发生变化。 若要查看更改历史记录，请参阅 [GitHub 提交历史记录](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/pci-dss-3.2.1/control-mapping.md)。

## <a name="132-and-134-boundary-protection"></a>1.3.2 和1.3.4 边界保护

此蓝图通过分配用于监视具有可许可规则的网络安全组的[Azure 策略](../../../policy/overview.md)定义，帮助你管理和控制网络。 过于宽松的规则可能会允许意外的网络访问，应该对其进行评审。 此蓝图分配一个 Azure 策略定义，用于监视未受保护的终结点、应用程序和存储帐户。 不受防火墙保护的终结点和应用程序，以及具有无限制访问权限的存储帐户，可能会允许意外访问信息系统中包含的信息。

- 审核对存储帐户的不受限的网络访问
- 应该限制通过面向 Internet 的终结点进行访问

## <a name="34a-41-41g-41h-and-653-cryptographic-protection"></a>3.4. a、4.1、4.1. g、4.1 和6.5.3 加密保护

此蓝图通过分配[Azure 策略](../../../policy/overview.md)定义（强制执行特定的如何控件并审核弱加密设置的使用），帮助你通过使用如何控件来强制实施策略。 了解 Azure 资源中的哪些位置采用欠佳的加密配置有助于采取纠正措施，以确保根据信息安全策略配置资源。 具体而言，此蓝图分配的策略需要对 SQL 数据库进行透明数据加密;审核存储帐户和自动化帐户变量上缺少加密。 此外，还提供了用于处理与存储帐户、Function Apps、WebApp、API 应用和 Redis 缓存的不安全连接的策略，以及审核未加密 Service Fabric 通信。

- 应该只能通过 HTTPS 访问函数应用
- 只能通过 HTTPS 访问 Web 应用程序
- 只能通过 HTTPS 访问 API 应用
- 应在 SQL 数据库上启用透明数据加密
- 应在虚拟机上启用磁盘加密
- 自动化帐户变量应加密
- 应该启用只能通过安全方式连接到 Redis 缓存
- 应该启用安全传输到存储帐户
- Service Fabric 群集应将 ClusterProtectionLevel 属性设置为 EncryptAndSign
- 应在 SQL 数据库上启用透明数据加密
- 部署 SQL DB 透明数据加密

## <a name="51-62-66-and-1121-vulnerability-scanning-and-system-updates"></a>5.1、6.2、6.6 和11.2.1 漏洞扫描和系统更新

此蓝图通过分配用于监视 Azure 中缺少系统更新、操作系统漏洞、SQL 漏洞和虚拟机漏洞的[Azure 策略](../../../policy/overview.md)定义，帮助你管理信息系统漏洞安全中心。 Azure 安全中心提供报告功能，使你能够实时洞察已部署的 Azure 资源的安全状态。

- 监视 Azure 安全中心 Endpoint Protection 的缺失情况
- 为 Windows Server 部署默认 Microsoft IaaSAntimalware 扩展
- 在 SQL Server 上部署威胁检测
- 应在计算机上安装系统更新
- 应该修复计算机上安全配置中的漏洞
- 应该修复 SQL 数据库中的漏洞
- 应该通过漏洞评估解决方案修复漏洞

## <a name="711-712-and-713-separation-of-duties"></a>7.1.1. 7.1.2 和7.1.3 的职责分离

仅分配一个 Azure 订阅所有者并不能实现管理冗余。 相反，分配过多的 Azure 订阅所有者会增大违规的可能性，因为会有更多的所有者帐户可能会泄密。 此蓝图可帮助你维护合适数量的 Azure 订阅所有者，方法是分配[Azure 策略](../../../policy/overview.md)定义，用于审核 azure 订阅的所有者数量。 管理订阅所有者权限有助于实现适当的职责分离。

- 应该为你的订阅分配了多个所有者
- 只多只为订阅指定 3 个所有者 

## <a name="32-721-831a-and-831b-management-of-privileged-access-rights"></a>3.2、7.2.1、8.3.1 和8.3.1 管理特权访问权限

此蓝图通过以下方式来帮助你限制和控制特权访问权限：将[Azure 策略](../../../policy/overview.md)定义分配给审核外部帐户，其中所有者、写入和/或读取权限以及具有不具有的所有者和/或写入权限的员工帐户已启用多重身份验证。 Azure 实施基于角色的访问控制 (RBAC) 来管理谁有权访问 Azure 资源。 了解实施自定义 RBAC 规则的位置有助于验证需求以及实施是否适当，因为自定义 RBAC 规则容易出错。 此蓝图还会分配[Azure 策略](../../../policy/overview.md)定义，以审核对 SQL server Azure Active Directory 身份验证的使用。 使用 Azure Active Directory 身份验证可简化权限管理，并集中管理数据库用户和其他 Microsoft  
服务器.
 
- 应从订阅中删除拥有所有者权限的外部帐户
- 应从订阅中删除具有写入权限的外部帐户
- 应从订阅中删除拥有读取权限的外部帐户
- 应在对订阅拥有所有者权限的帐户上启用 MFA
- 应为 MFA 启用对订阅具有写入权限的帐户
- 应在对订阅拥有读取权限的帐户上启用 MFA
- 应该为 SQL 服务器预配 Azure Active Directory 管理员
- 审核自定义 RBAC 规则的使用

## <a name="812-and-815-least-privilege-and-review-of-user-access-rights"></a>8.1.2 和8.1.5 最小权限和用户访问权限检查

Azure 实施基于角色的访问控制（RBAC），以帮助你管理哪些用户有权访问 Azure 中的资源。 使用 Azure 门户可以评审有权访问 Azure 资源的用户及其权限。 此蓝图将[Azure 策略](../../../policy/overview.md)定义分配到审核帐户，这些帐户应优先排序，包括具有提升权限的帐户和外部帐户。

- 应从订阅中删除弃用的帐户
- 应从订阅中删除拥有所有者权限的已弃用帐户
- 应从订阅中删除拥有所有者权限的外部帐户
- 应从订阅中删除具有写入权限的外部帐户
- 应从订阅中删除拥有读取权限的外部帐户

## <a name="813-removal-or-adjustment-of-access-rights"></a>8.1.3 删除或调整访问权限

Azure 实施基于角色的访问控制（RBAC），以帮助你管理哪些用户有权访问 Azure 中的资源。 使用 Azure Active Directory 和 RBAC，可以更新用户角色以反映组织更改。 如果需要，可以阻止帐户登录（或将其删除），这会立即删除其 Azure 资源访问权限。 此蓝图将[Azure 策略](../../../policy/overview.md)定义分配给审核折旧帐户，以便删除。

- 应从订阅中删除弃用的帐户
- 应从订阅中删除拥有所有者权限的已弃用帐户

## <a name="823ab-824ab-and-825-password-based-authentication"></a>8.2.3，b，8.2.4，b 和8.2.5 基于密码的身份验证

此蓝图通过分配[Azure 策略](../../../policy/overview.md)定义来帮助你强制实施强密码，这些策略定义用于审核不强制实施最低强度的 Windows vm 和其他密码要求。 识别违反密码强度策略的 VM 有助于采取纠正措施，以确保所有 VM 用户帐户的密码符合策略。

- \[预览\]：审核的 Windows Vm 的最长密码期限为70天
- \[预览\]：部署要求以审核不具有最长密码期限70天的 Windows Vm
- \[预览\]：审核不将最短密码长度限制为14个字符的 Windows Vm
- \[预览\]：部署要求以审核不将最短密码长度限制为14个字符的 Windows Vm
- \[预览\]：审核允许重复使用以前24密码的 Windows Vm
- \[预览\]：部署要求以审核允许重复使用以前24密码的 Windows Vm

## <a name="103-and-1054-audit-generation"></a>10.3 和10.5.4 审核生成

此蓝图通过分配 [Azure Policy](../../../policy/overview.md) 定义来帮助确保记录系统事件，这些定义用于审核在 Azure 资源上的日志设置。
诊断日志针对 Azure 资源中执行的操作提供见解。 Azure 日志依赖于同步的内部时钟创建各个资源中事件的时间相关记录。

- 应在 SQL Server 的高级数据安全设置上启用审核
- 审核诊断设置
- 审核 SQL 服务器级别审核设置
- 对 SQL 服务器部署审核
- 应将存储帐户迁移到新的 Azure 资源管理器资源
- 应将虚拟机迁移到新的 Azure 资源管理器资源

## <a name="1236-and-1237-information-security"></a>12.3.6 和12.3.7 信息安全性

此蓝图可帮助你管理和控制网络，方法是分配审核可接受网络位置和环境允许的已批准公司产品的[Azure 策略](../../../policy/overview.md)定义。 每个公司通过其中每个策略中的策略参数可自定义这些设置。

- 允许的位置
- 允许的资源组位置

## <a name="next-steps"></a>后续步骤

现在，你已查看了 PCI-DSS v1.0 蓝图的控件映射，请访问以下文章了解概述以及如何部署此示例：

> [!div class="nextstepaction"]
> [Pci-dss v2.0 3.2.1 蓝图-概述](./index.md)
> [PCI-dss V1.0 蓝图-部署步骤](./deploy.md)

有关蓝图以及如何使用它们的其他文章：

- 了解[蓝图生命周期](../../concepts/lifecycle.md)。
- 了解如何使用[静态和动态参数](../../concepts/parameters.md)。
- 了解如何自定义[蓝图排序顺序](../../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../../concepts/resource-locking.md)。
- 了解如何[更新现有分配](../../how-to/update-existing-assignments.md)。