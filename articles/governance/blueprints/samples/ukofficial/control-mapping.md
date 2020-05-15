---
title: UK OFFICIAL 和 UK NHS 蓝图示例控件
description: UK OFFICIAL 和 UK NHS 蓝图示例的控件映射。 每个控制都映射到一个或多个协助评估的 Azure 策略。
ms.date: 05/08/2020
ms.topic: sample
ms.openlocfilehash: 88f9606df5c3dcbca6ade05be918e3500a6ba64c
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2020
ms.locfileid: "83005618"
---
# <a name="control-mapping-of-the-uk-official-and-uk-nhs-blueprint-samples"></a>UK OFFICIAL 和 UK NHS 蓝图示例的控件映射

以下文章详细介绍 UK OFFICIAL 和 UK NHS 蓝图示例如何映射到 UK OFFICIAL 和 UK NHS 控件。 有关控件的详细信息，请参阅 [UK OFFICIAL](https://www.gov.uk/government/publications/government-security-classifications)。

以下映射对应于“UK OFFICIAL”和“UK NHS”控件   。 使用右侧的导航栏可直接跳转到特定的控制映射。 许多的映射控制措施都是使用 [Azure Policy](../../../policy/overview.md) 计划实施的。 若要查看完整计划，请在 Azure 门户中打开“策略”，并选择“定义”页。   然后，找到并选择“\[预览\] 审核 UK OFFICIAL 和 UK NHS 控件并部署特定 VM 扩展以支持审核要求”内置策略计划。 

> [!IMPORTANT]
> 下面的每个控件都与一个或多个 [Azure Policy](../../../policy/overview.md) 定义关联。 这些策略可以帮助你[评估控件的符合性](../../../policy/how-to/get-compliance-data.md)；但是，控件与一个或多个策略之间通常不是 1:1 或完全匹配。 因此，Azure Policy 中的符合性  仅引用策略本身；这不确保你完全符合控件的所有要求。 此外，符合性标准包含目前未由任何 Azure Policy 定义处理的控件。 因此，Azure Policy 中的符合性只是整体符合性状态的部分视图。 此符合性蓝图示例的控件和 Azure Policy 定义之间的关联可能会随着时间的推移而发生变化。 若要查看更改历史记录，请参阅 [GitHub 提交历史记录](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/ukofficial/control-mapping.md)。

## <a name="1-data-in-transit-protection"></a>1 传输中的数据保护

该蓝图通过分配 [Azure Policy](../../../policy/overview.md) 定义用于审核与存储帐户和 Redis 缓存建立的不安全连接，来帮助你确保与 Azure 服务之间安全传输信息。

- 应该启用只能通过安全方式连接到 Redis 缓存
- 应该启用安全传输到存储帐户
- 显示未使用安全通信协议的 Windows Web 服务器中的审核结果
- 部署先决条件，以便审核未使用安全通信协议的 Windows Web 服务器

## <a name="23-data-at-rest-protection"></a>2.3 静态数据保护

此蓝图通过分配 [Azure Policy](../../../policy/overview.md) 定义用于强制实施特定的加密控件并审核弱加密设置的使用，帮助你针对加密控件的使用强制实施自己的策略。
了解 Azure 资源中的哪些位置采用欠佳的加密配置有助于采取纠正措施，以确保根据信息安全策略配置资源。 具体而言，此蓝图分配的策略要求对 Data Lake Storage 帐户加密；要求对 SQL 数据库实施透明数据加密；审核存储帐户、SQL 数据库、虚拟机磁盘和自动化帐户变量是否缺少加密；审核是否与存储帐户和 Redis 缓存建立了不安全的连接；审核虚拟机弱密码加密；审核未加密的 Service Fabric 通信。

- 应在虚拟机上启用磁盘加密
- 自动化帐户变量应进行加密
- Service Fabric 群集应将 ClusterProtectionLevel 属性设置为 EncryptAndSign
- 应在 SQL 数据库上启用透明数据加密
- 部署 SQL DB 透明数据加密
- 要求对 Data Lake Store 帐户加密
- 允许的位置（已硬编码为“英国南部”和“英国西部”）
- 允许的资源组位置（已硬编码为“英国南部”和“英国西部”）

## <a name="52-vulnerability-management"></a>5.2 漏洞管理

此蓝图通过分配 [Azure Policy](../../../policy/overview.md) 定义用于监视缺少的终结点保护、缺少的系统更新、操作系统漏洞、SQL 漏洞和虚拟机漏洞，来帮助你管理信息系统漏洞。 这些见解提供有关已部署资源的安全状态的实时信息，可帮助你指定补救措施的优先级。

- 监视 Azure 安全中心 Endpoint Protection 的缺失情况
- 应在计算机上安装系统更新
- 应在虚拟机规模集上安装系统更新
- 应该修复计算机上安全配置中的漏洞
- 应该修复 SQL 数据库中的漏洞
- 应该通过漏洞评估解决方案修复漏洞
- 应对 SQL Server 启用漏洞评估
- 应对 SQL 托管实例启用漏洞评估
- 应该修复虚拟机规模集上安全配置中的漏洞
- 应在 SQL 托管实例上启用高级数据安全性
- 应在 SQL 服务器上启用高级数据安全性

## <a name="53-protective-monitoring"></a>5.3 保护性监视

此蓝图通过分配针对无限制访问、允许列表活动和威胁提供保护性监视的 [Azure Policy](../../../policy/overview.md) 定义，帮助保护信息系统资产。

- 审核对存储帐户的不受限的网络访问
- 应在虚拟机上启用自适应应用程序控制
- 审核没有配置灾难恢复的虚拟机
- 应启用 DDoS 防护标准版
- 应在 SQL 托管实例的“高级数据安全性”设置中将“高级威胁保护类型”设置为“所有”
- 应在 SQL 服务器的“高级数据安全性”设置中将“高级威胁保护类型”设置为“所有”
- 在 SQL 服务器上部署威胁检测
- 为 Windows Server 部署默认 Microsoft IaaSAntimalware 扩展

## <a name="9-secure-user-management"></a>9 安全用户管理 

Azure 实施基于角色的访问控制 (RBAC)，以帮助管理谁有权访问 Azure 中的资源。 使用 Azure 门户可以评审有权访问 Azure 资源的用户及其权限。 此蓝图通过分配 [Azure Policy](../../../policy/overview.md) 定义用于审核拥有所有者和/或读/写权限的外部帐户，以及拥有所有者、读取和/或写入权限、但未启用多重身份验证的帐户，来帮助你限制和控制访问权限。

- 应在对订阅拥有所有者权限的帐户上启用 MFA
- 应对订阅中拥有写入权限的帐户启用 MFA
- 应在对订阅拥有读取权限的帐户上启用 MFA
- 应从订阅中删除拥有所有者权限的外部帐户
- 应从订阅中删除具有写入权限的外部帐户
- 应从订阅中删除拥有读取权限的外部帐户

## <a name="10-identity-and-authentication"></a>10 标识和身份验证

此蓝图通过分配 [Azure Policy](../../../policy/overview.md) 定义用于审核拥有所有者和/或读/写权限的外部帐户，以及拥有所有者、读取和/或写入权限、但未启用多重身份验证的帐户，来帮助你限制和控制访问权限。

- 应在对订阅拥有所有者权限的帐户上启用 MFA
- 应对订阅中拥有写入权限的帐户启用 MFA
- 应在对订阅拥有读取权限的帐户上启用 MFA
- 应从订阅中删除拥有所有者权限的外部帐户
- 应从订阅中删除具有写入权限的外部帐户
- 应从订阅中删除拥有读取权限的外部帐户

此蓝图分配 Azure Policy 定义，用于审核 Azure Active Directory 身份验证在 SQL 服务器和 Service Fabric 中的使用。 使用 Azure Active Directory 身份验证可以简化权限管理，以及集中化数据库用户和其他 Microsoft 服务的标识管理。

- 应该为 SQL 服务器预配 Azure Active Directory 管理员
- Service Fabric 群集只应使用 Azure Active Directory 进行客户端身份验证

此蓝图还分配 Azure Policy 定义用于审核应该优先评审的帐户，包括已淘汰的帐户以及外部帐户。 如果需要，可以阻止帐户登录（或将其删除），这会立即删除其 Azure 资源访问权限。 此蓝图分配两个 Azure Policy 定义用于审核应该考虑删除的已淘汰帐户。

- 应从订阅中删除弃用的帐户
- 应从订阅中删除拥有所有者权限的已弃用帐户
- 应从订阅中删除拥有所有者权限的外部帐户
- 应从订阅中删除具有写入权限的外部帐户

此蓝图还将分配一个 Azure Policy 定义，用于审核 Linux VM 密码文件权限，以便在这些权限设置不正确时发出警报。 使用这种设计可以采取纠正措施，以确保验证器不会泄密。

- \[预览\]：显示未将密码文件权限设为 0644 的 Linux VM 中的审核结果

此蓝图通过分配 Azure Policy 定义用于审核不强制实施最低强度和其他密码要求的 Windows VM，来帮助你强制实施强密码。 识别违反密码强度策略的 VM 有助于采取纠正措施，以确保所有 VM 用户帐户的密码符合策略。

- \[预览\]：部署先决条件，以便审核未启用密码复杂性设置的 Windows VM
- \[预览\]：部署先决条件，以便审核未将最长密码期限设为 70 天的 Windows VM
- \[预览\]：部署先决条件，以便审核未将最短密码期限设为 1 天的 Windows VM
- \[预览\]：部署先决条件，以便审核未将最短密码长度限制为 14 个字符的 Windows VM
- \[预览\]：部署先决条件，以便审核允许重用之前的 24 个密码的 Windows VM
- \[预览\]：显示未启用密码复杂性设置的 Windows VM 中的审核结果
- \[预览\]：显示未将最长密码期限设为 70 天的 Windows VM 中的审核结果
- \[预览\]：显示未将最短密码期限设为 1 天的 Windows VM 中的审核结果
- \[预览\]：显示未将最短密码长度限制为 14 个字符的 Windows VM 中的审核结果
- \[预览\]：显示允许重用之前的 24 个密码的 Windows VM 中的审核结果

此蓝图还可帮助你通过分配 Azure Policy 定义来控制对 Azure 资源的访问。 这些策略将审核可能允许更高资源访问权限的资源类型和配置的使用。 了解违反这些策略的资源有助于采取纠正措施来确保仅限已授权的用户访问 Azure 资源。

- \[预览\]：部署要求以审核具有不使用密码的帐户的 Linux VM
- \[预览\]：部署要求以审核允许通过没有密码的帐户进行远程连接的 Linux VM
- \[预览\]：显示具有不使用密码的帐户的 Linux VM 中的审核结果
- \[预览\]：显示允许通过没有密码的帐户进行远程连接的 Linux VM 中的审核结果
- 应将存储帐户迁移到新 Azure 资源管理器资源
- 应将虚拟机迁移到新的 Azure 资源管理器资源
- 审核不使用托管磁盘的 VM

## <a name="11-external-interface-protection"></a>11 外部接口保护

除了使用超过 25 个策略进行适当的安全用户管理之外，此蓝图还通过分配一个用于监视无限制存储帐户的 [Azure Policy](../../../policy/overview.md) 定义，帮助防止对服务接口进行未经授权的访问。 具有无限制访问权限的存储帐户可能会允许意外访问信息系统中包含的信息。 此蓝图还会分配一个策略，在虚拟机上启用自适应应用程序控制。

- 审核对存储帐户的不受限的网络访问
- 应在虚拟机上启用自适应应用程序控制
- 应该限制通过面向 Internet 的终结点进行访问
- 应在面向 Internet 的虚拟机上应用自适应网络强化建议
- 应在虚拟机规模集上安装 Endpoint Protection 解决方案
- 应在虚拟机上应用实时网络访问控制
- 应对函数应用禁用远程调试
- 应禁用 Web 应用程序的远程调试
- 应为 API 应用禁用远程调试
- 只能通过 HTTPS 访问 Web 应用程序
- 应该只能通过 HTTPS 访问函数应用
- 只能通过 HTTPS 访问 API 应用

## <a name="12-secure-service-administration"></a>12 安全服务管理

Azure 实施基于角色的访问控制 (RBAC)，以帮助管理谁有权访问 Azure 中的资源。 使用 Azure 门户可以评审有权访问 Azure 资源的用户及其权限。 此蓝图通过分配五个 [Azure Policy](../../../policy/overview.md) 定义以审核拥有所有者和/或写入权限的外部帐户，以及拥有所有者和/或写入权限、但未启用多重身份验证的帐户，从而帮助你限制和控制特权访问权限。

用于云服务管理的系统将具有高特权来访问该服务。 其泄露将产生重大影响，包括绕过安全控制、窃取或操纵大量数据的手段。 对于服务提供商管理员用于管理操作服务的方法，应设计为降低任何可能破坏服务安全性的利用风险。 如果未实施此原则，则攻击者可能有办法绕过安全控制、窃取或操纵大量数据。

- 应在对订阅拥有所有者权限的帐户上启用 MFA
- 应对订阅中拥有写入权限的帐户启用 MFA
- 应从订阅中删除拥有所有者权限的外部帐户
- 应从订阅中删除具有写入权限的外部帐户

此蓝图分配 Azure Policy 定义，用于审核 Azure Active Directory 身份验证在 SQL 服务器和 Service Fabric 中的使用。 使用 Azure Active Directory 身份验证可以简化权限管理，以及集中化数据库用户和其他 Microsoft 服务的标识管理。

- 应该为 SQL 服务器预配 Azure Active Directory 管理员
- Service Fabric 群集只应使用 Azure Active Directory 进行客户端身份验证

此蓝图还分配 Azure Policy 定义以审核应该优先评审的帐户（包括已淘汰的帐户），以及具有提升权限的外部帐户。 如果需要，可以阻止帐户登录（或将其删除），这会立即删除其 Azure 资源访问权限。 此蓝图分配两个 Azure Policy 定义用于审核应该考虑删除的已淘汰帐户。

- 应从订阅中删除弃用的帐户
- 应从订阅中删除拥有所有者权限的已弃用帐户
- 应从订阅中删除拥有所有者权限的外部帐户
- 应从订阅中删除具有写入权限的外部帐户

此蓝图还将分配一个 Azure Policy 定义，用于审核 Linux VM 密码文件权限，以便在这些权限设置不正确时发出警报。 使用这种设计可以采取纠正措施，以确保验证器不会泄密。

- \[预览\]：显示未将密码文件权限设为 0644 的 Linux VM 中的审核结果

## <a name="13-audit-information-for-users"></a>13 用户的审核信息

此蓝图通过分配 [Azure Policy](../../../policy/overview.md) 定义来帮助确保记录系统事件，这些定义用于审核在 Azure 资源上的日志设置。 分配的策略还会审核虚拟机是否不向指定的 Log Analytics 工作区发送日志。

- 应在 SQL 服务器上启用高级数据安全性
- 审核诊断设置
- \[预览\]：为 Linux VM 部署 Log Analytics 代理
- \[预览\]：为 Windows VM 部署 Log Analytics 代理
- 创建虚拟网络时部署网络观察程序

## <a name="next-steps"></a>后续步骤

了解 UK OFFICIAL 和 UK NHS 蓝图的控件映射后，请访问以下文章，了解概述以及如何部署此示例：

> [!div class="nextstepaction"]
> [UK OFFICIAL 和 UK NHS 蓝图 - 概述](./index.md)
> [UK OFFICIAL 和 UK NHS 蓝图 - 部署步骤](./deploy.md)

有关蓝图及其使用方式的更多文章：

- 了解[蓝图生命周期](../../concepts/lifecycle.md)。
- 了解如何使用[静态和动态参数](../../concepts/parameters.md)。
- 了解如何自定义[蓝图排序顺序](../../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../../concepts/resource-locking.md)。
- 了解如何[更新现有分配](../../how-to/update-existing-assignments.md)。
