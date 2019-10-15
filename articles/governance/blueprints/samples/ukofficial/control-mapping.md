---
title: 英国官方 & 英国 NHS 蓝图示例-控件映射
description: 控制英国官方和英国 NHS 蓝图示例的映射。 每个控件映射到一个或多个帮助评估的 Azure 策略。
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/26/2019
ms.topic: conceptual
ms.service: blueprints
ms.openlocfilehash: 72b7b370158f228d725e9c6970dfcffb09e7986b
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2019
ms.locfileid: "72297116"
---
# <a name="control-mapping-of-the-uk-official-and-uk-nhs-blueprint-samples"></a>控制英国官方和英国 NHS 蓝图示例的映射

以下文章详细介绍了英国官方和英国 NHS 蓝图示例如何映射到英国官方和英国 NHS 控件。 有关控件的详细信息，请参阅[UK 官方](https://www.gov.uk/government/publications/government-security-classifications)。

以下映射适用于**英国官方**和**英国 NHS**控件。 使用右侧的导航栏可直接跳转到特定的控制映射。 许多的映射控制措施都是使用 [Azure Policy](../../../policy/overview.md) 计划实施的。 若要查看完整计划，请在 Azure 门户中打开“策略”，并选择“定义”页。 然后，找到并选择 " **\[Preview @ no__t-2 AUDIT UK 官方 AND UK NHS" 控件，并部署特定的 VM 扩展以支持审核要求**内置策略计划。

> [!IMPORTANT]
> 下面的每个控件都与一个或多个 [Azure Policy](../../../policy/overview.md) 定义关联。 这些策略可以帮助你[评估控件的符合性](../../../policy/how-to/get-compliance-data.md)；但是，控件与一个或多个策略之间通常不是 1:1 或完全匹配。 因此，Azure Policy 中的符合性仅引用策略本身；这不确保你完全符合控件的所有要求。 此外，符合性标准包含目前未由任何 Azure Policy 定义处理的控件。 因此，Azure Policy 中的符合性只是整体符合性状态的部分视图。 此符合性蓝图示例的控件和 Azure Policy 定义之间的关联可能会随着时间的推移而发生变化。 若要查看更改历史记录，请参阅 [GitHub 提交历史记录](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/ukofficial/control-mapping.md)。

## <a name="1-data-in-transit-protection"></a>1传输防护中的数据

该蓝图通过分配用于审核与存储帐户和 Redis 缓存的不安全连接的[Azure 策略](../../../policy/overview.md)定义，帮助确保信息通过 azure 服务进行传输。

- 应仅启用与 Redis 缓存的安全连接
- 应启用安全转移到存储帐户

## <a name="23-data-at-rest-protection"></a>2.3 静态数据保护

此蓝图通过分配[Azure 策略](../../../policy/overview.md)定义（强制执行特定的如何控件并审核弱加密设置的使用），帮助你在使用如何控件时执行策略。
了解 Azure 资源中的哪些位置采用欠佳的加密配置有助于采取纠正措施，以确保根据信息安全策略配置资源。 具体而言，此蓝图分配的策略需要 data lake 存储帐户的加密;需要对 SQL 数据库进行透明数据加密;审核存储帐户、SQL 数据库、虚拟机磁盘和自动化帐户变量上缺少的加密;审核到存储帐户和 Redis 缓存的不安全连接;审核弱虚拟机密码加密;和审核未加密 Service Fabric 通信。

- 应启用 SQL 数据库上的透明数据加密
- 应对虚拟机应用磁盘加密
- 自动化帐户变量应进行加密
- 应启用安全转移到存储帐户
- Service Fabric 群集应将 ClusterProtectionLevel 属性设置为 EncryptAndSign
- 应启用 SQL 数据库上的透明数据加密
- 部署 SQL DB 透明数据加密
- 需要对 Data Lake Store 帐户加密
- 允许的位置（已硬编码为 "英国南部" 和 "英国西部"）
- 允许的资源组位置（已硬编码为 "英国南部" 和 "英国西部"）

## <a name="52-vulnerability-management"></a>5.2 漏洞管理

此蓝图通过分配用于监视缺少的 endpoint protection、缺少系统更新、操作系统漏洞、SQL 漏洞和虚拟的[Azure 策略](../../../policy/overview.md)定义，帮助你管理信息系统漏洞计算机漏洞。 这些见解提供有关已部署资源的安全状态的实时信息，可帮助你指定补救措施的优先级。

- 在 Azure 安全中心监视 Endpoint Protection 的缺失情况
- 应对计算机安装系统更新
- 应修正计算机上的安全配置漏洞
- 应修正 SQL 数据库上的漏洞
- 漏洞应由漏洞评估解决方案修正

## <a name="53-protective-monitoring"></a>5.3 保护性监视

此蓝图通过分配[Azure 策略](../../../policy/overview.md)定义来帮助保护信息系统资产，这些策略定义针对无限制访问、允许列表活动和威胁提供保护性监视。

- 审核对存储帐户的不受限的网络访问
- 应对虚拟机启用自适应应用程序控制
- 在 SQL Server 上部署威胁检测
- 为 Windows Server 部署默认的 Microsoft IaaS 反恶意软件扩展

## <a name="9-secure-user-management--10-identity-and-authentication"></a>9保护用户管理/10 身份验证和身份验证

Azure 实施基于角色的访问控制（RBAC），以帮助你管理哪些用户有权访问 Azure 中的资源。 使用 Azure 门户可以评审有权访问 Azure 资源的用户及其权限。 此蓝图通过以下方法来限制和控制访问权限：向所有者和/或读/写[权限指定所有者](../../../policy/overview.md)和/或读/写权限的外部帐户，并使用具有不具有多重身份的 "读取" 和/或 "写入" 权限的帐户身份验证已启用。

- 应对拥有订阅所有者权限的帐户启用 MFA
- 应对订阅中拥有写入权限的帐户启用 MFA
- 应对拥有订阅读取权限的帐户启用 MFA
- 应从订阅中删除拥有所有者权限的外部帐户
- 应从订阅中删除拥有写入权限的外部帐户
- 应从订阅中删除拥有读取权限的外部帐户

此蓝图分配 Azure 策略定义，以审核对 SQL server 和 Service Fabric 的 Azure Active Directory 身份验证的使用。 使用 Azure Active Directory 身份验证可以简化权限管理，以及集中化数据库用户和其他 Microsoft 服务的标识管理。

- 应为 SQL 服务器预配 Azure Active Directory 管理员
- Service Fabric 群集应仅使用 Azure Active Directory 进行客户端身份验证

此蓝图还会将 Azure 策略定义分配给审核帐户，这些帐户应优先考虑，包括折旧帐户和外部帐户。 如果需要，可以阻止帐户登录（或将其删除），这会立即删除其 Azure 资源访问权限。 此蓝图将两个 Azure 策略定义分配给审核折旧帐户，以便删除。

- 应从订阅中删除弃用帐户
- 应从订阅中删除拥有所有者权限的已弃用帐户
- 应从订阅中删除拥有所有者权限的外部帐户
- 应从订阅中删除拥有写入权限的外部帐户

此蓝图还会分配一个 Azure 策略定义，用于审核 Linux VM 密码文件权限，以在设置不正确时进行警报。 此设计使你可以采取纠正措施以确保验证器不会受到侵害。

- \[预览\]：审核 Linux VM /etc/密码文件权限是否设置为 0644

此蓝图通过分配 Azure 策略定义来帮助你强制实施强密码，这些策略定义用于审核不强制实施最低强度的 Windows Vm 和其他密码要求。 识别违反密码强度策略的 VM 有助于采取纠正措施，以确保所有 VM 用户帐户的密码符合策略。

- \[预览\]：部署要求以审核未启用密码复杂性设置的 Windows VM
- \[预览\]：部署要求以审核未将最长密码期限设为 70 天的 Windows VM
- \[预览\]：部署要求以审核未将最短密码期限设为 1 天的 Windows VM
- \[预览\]：部署要求以审核未将最短密码长度限制为 14 个字符的 Windows VM
- \[预览\]：部署要求以审核允许重用之前的 24 个密码的 Windows VM
- \[预览\]：审核未启用密码复杂性设置的 Windows VM
- \[预览\]：审核未将最长密码期限设为 70 天的 Windows VM
- \[预览\]：审核未将最短密码期限设为 1 天的 Windows VM
- \[预览\]：审核未将最短密码长度限制为 14 个字符的 Windows VM
- \[预览\]：审核允许重用之前的 24 个密码的 Windows VM

此蓝图还有助于通过分配 Azure 策略定义来控制对 Azure 资源的访问。 这些策略将审核可能允许更高资源访问权限的资源类型和配置的使用。 了解违反这些策略的资源有助于采取纠正措施来确保仅限已授权的用户访问 Azure 资源。

- \[预览\]：部署要求以审核具有不使用密码的帐户的 Linux VM
- \[预览\]：部署要求以审核允许通过没有密码的帐户进行远程连接的 Linux VM
- \[预览\]：审核具有不使用密码的帐户的 Linux VM
- \[预览\]：审核允许通过没有密码的帐户进行远程连接的 Linux VM
- 应将存储帐户迁移到新 Azure 资源管理器资源
- 应将虚拟机迁移到新 Azure 资源管理器资源
- 审核未使用托管磁盘的 VM

## <a name="11-external-interface-protection"></a>11外部接口保护

除了为适当的安全用户管理使用超过25个策略之外，此蓝图还有助于通过分配用于监视不受限制的存储帐户的[Azure 策略](../../../policy/overview.md)定义，来保护服务接口免受未经授权的访问。 具有不受限制访问权限的存储帐户可允许对信息系统中包含的信息进行意外访问。 此蓝图还会分配一个策略，该策略启用虚拟机上的自适应应用程序控制。

- 审核对存储帐户的不受限的网络访问
- 应对虚拟机启用自适应应用程序控制

## <a name="12-secure-service-administration"></a>12安全服务管理

Azure 实施基于角色的访问控制（RBAC），以帮助你管理哪些用户有权访问 Azure 中的资源。 使用 Azure 门户可以评审有权访问 Azure 资源的用户及其权限。 此蓝图通过以下方法来帮助你限制和控制特权访问权限：分配5个[Azure 策略](../../../policy/overview.md)定义，以审核具有所有者和/或写入权限的外部帐户，以及拥有所有者和/或写入权限的具有以下权限的帐户：已启用多重身份验证。

用于云服务管理的系统将具有高特权来访问该服务。 其泄露将产生重大影响，包括绕过安全控制、窃取或操纵大量数据的手段。 服务提供商管理员用于管理操作服务的方法应设计为缓解可能破坏服务安全的任何攻击风险。 如果未实现此原则，攻击者可能会绕过安全控制并盗取或处理大量数据。

- 应对拥有订阅所有者权限的帐户启用 MFA
- 应对订阅中拥有写入权限的帐户启用 MFA
- 应从订阅中删除拥有所有者权限的外部帐户
- 应从订阅中删除拥有写入权限的外部帐户

此蓝图分配 Azure 策略定义，以审核对 SQL server 和 Service Fabric 的 Azure Active Directory 身份验证的使用。 使用 Azure Active Directory 身份验证可以简化权限管理，以及集中化数据库用户和其他 Microsoft 服务的标识管理。

- 应为 SQL 服务器预配 Azure Active Directory 管理员
- Service Fabric 群集应仅使用 Azure Active Directory 进行客户端身份验证

此蓝图还会将 Azure 策略定义分配给审核帐户，这些帐户应优先排序，包括具有提升权限的帐户和外部帐户。 如果需要，可以阻止帐户登录（或将其删除），这会立即删除其 Azure 资源访问权限。 此蓝图将两个 Azure 策略定义分配给审核折旧帐户，以便删除。

- 应从订阅中删除弃用帐户
- 应从订阅中删除拥有所有者权限的已弃用帐户
- 应从订阅中删除拥有所有者权限的外部帐户
- 应从订阅中删除拥有写入权限的外部帐户

此蓝图还会分配一个 Azure 策略定义，用于审核 Linux VM 密码文件权限，以在设置不正确时进行警报。 此设计使你可以采取纠正措施以确保验证器不会受到侵害。

- \[预览\]：审核 Linux VM /etc/密码文件权限是否设置为 0644

## <a name="13-audit-information-for-users"></a>13个用户的审核信息

此蓝图通过分配 [Azure Policy](../../../policy/overview.md) 定义来帮助确保记录系统事件，这些定义用于审核在 Azure 资源上的日志设置。 分配的策略还会审核虚拟机是否不向指定的 Log Analytics 工作区发送日志。

- 应在 SQL Server 上的高级数据安全设置上启用审核
- 审核诊断设置
- 审核 SQL 服务器级别审核设置
- \[预览\]：为 Linux VM 部署 Log Analytics 代理
- \[预览\]：为 Windows VM 部署 Log Analytics 代理
- 在创建虚拟网络时部署网络观察程序

## <a name="next-steps"></a>后续步骤

现在，你已经查看了英国官方和英国 NHS 蓝图的控件映射，接下来请访问以下文章来了解概述以及如何部署此示例：

> [!div class="nextstepaction"]
> [英国官方和英国 NHS 蓝图-概述](./index.md)
> [UK 官方和英国 NHS 蓝图-部署步骤](./deploy.md)

有关蓝图和如何使用这些蓝图的更多文章：

- 了解[蓝图生命周期](../../concepts/lifecycle.md)。
- 了解如何使用[静态和动态参数](../../concepts/parameters.md)。
- 了解如何自定义[蓝图排序顺序](../../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../../concepts/resource-locking.md)。
- 了解如何[更新现有分配](../../how-to/update-existing-assignments.md)。