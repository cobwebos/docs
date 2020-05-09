---
title: ISO 27001 蓝图示例控件
description: ISO 27001 蓝图示例的控制映射。 每个控制都映射到一个或多个协助评估的 Azure 策略。
ms.date: 01/13/2020
ms.topic: sample
ms.openlocfilehash: 501884491dbef85cdf8a29cb5fdcef44a68235a9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "75920576"
---
# <a name="control-mapping-of-the-iso-27001-blueprint-sample"></a>ISO 27001 蓝图示例的控制映射

以下文章详细说明了 Azure 蓝图 ISO 27001 蓝图示例如何映射到 ISO 27001 控制措施。 有关控制措施的详细信息，请参阅 [ISO 27001](https://www.iso.org/isoiec-27001-information-security.html)。

以下映射适用于 **ISO 27001:2013** 控制措施。 使用右侧的导航栏可直接跳转到特定的控制映射。 许多的映射控制措施都是使用 [Azure Policy](../../../policy/overview.md) 计划实施的。 若要查看完整计划，请在 Azure 门户中打开“策略”，并选择“定义”页。   然后，找到并选择“\[预览\] 审核 ISO 27001:2013 控制措施并部署特定 VM 扩展以支持审核要求”内置策略计划。 

> [!IMPORTANT]
> 下面的每个控件都与一个或多个 [Azure Policy](../../../policy/overview.md) 定义关联。 这些策略可以帮助你[评估控件的符合性](../../../policy/how-to/get-compliance-data.md)；但是，控件与一个或多个策略之间通常不是 1:1 或完全匹配。 因此，Azure Policy 中的符合性  仅引用策略本身；这不确保你完全符合控件的所有要求。 此外，符合性标准包含目前未由任何 Azure Policy 定义处理的控件。 因此，Azure Policy 中的符合性只是整体符合性状态的部分视图。 此符合性蓝图示例的控件和 Azure Policy 定义之间的关联可能会随着时间的推移而发生变化。 若要查看更改历史记录，请参阅 [GitHub 提交历史记录](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/iso27001/control-mapping.md)。

## <a name="a612-segregation-of-duties"></a>A.6.1.2 职责分离

仅分配一个 Azure 订阅所有者并不能实现管理冗余。 相反，分配过多的 Azure 订阅所有者会增大违规的可能性，因为会有更多的所有者帐户可能会泄密。 此蓝图可帮助你通过分配两个用于审核 Azure 订阅所有者数目的 [Azure Policy](../../../policy/overview.md) 定义，来保持适当的 Azure 订阅所有者数目。 管理订阅所有者权限有助于实现适当的职责分离。

- \[预览\]：审核最小订阅所有者数
- \[预览\]：审核最大订阅所有者数

## <a name="a821-classification-of-information"></a>A.8.2.1 信息分类

Azure 的 [SQL 漏洞评估服务](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment)可以帮助你发现数据库中存储的敏感数据并提供用于对该数据进行分类的建议。 此蓝图分配了一个 [Azure Policy](../../../policy/overview.md) 定义来审核在 SQL 漏洞评估过程中查明的漏洞是否已更正。

- \[预览\]：监视 Azure 安全中心的 SQL 漏洞评估结果

## <a name="a912-access-to-networks-and-network-services"></a>A.9.1.2 访问网络和网络服务

Azure 实施[基于角色的访问控制](../../../../role-based-access-control/overview.md) (RBAC) 来管理谁有权访问 Azure 资源。 此蓝图可帮助你通过分配七个 [Azure Policy](../../../policy/overview.md) 定义来控制对 Azure 资源的访问。 这些策略将审核可能允许更高资源访问权限的资源类型和配置的使用。
了解违反这些策略的资源有助于采取纠正措施来确保仅限已授权的用户访问 Azure 资源。

- \[预览\]：部署 VM 扩展以审核没有密码的 Linux VM 帐户
- \[预览\]：部署 VM 扩展以审核允许从没有密码的帐户进行远程连接的 Linux VM
- \[预览\]：审核没有密码的 Linux VM 帐户
- \[预览\]：审核允许从没有密码的帐户进行远程连接的 Linux VM
- 审核经典存储帐户的使用
- 审核经典虚拟机的使用
- 审核不使用托管磁盘的 VM

## <a name="a923-management-of-privileged-access-rights"></a>A.9.2.3 管理特权访问权限

此蓝图通过分配四个 [Azure Policy](../../../policy/overview.md) 定义用于审核拥有所有者和/或写入权限的外部帐户，以及拥有所有者和/或写入权限、但未启用多重身份验证的帐户，来帮助你限制和控制特权访问权限。 Azure 实施基于角色的访问控制 (RBAC) 来管理谁有权访问 Azure 资源。 此蓝图还分配了三个 Azure Policy 定义，用于审核 Azure Active Directory 身份验证在 SQL 服务器和 Service Fabric 中的使用。 使用 Azure Active Directory 身份验证可以简化权限管理，以及集中化数据库用户和其他 Microsoft 服务的标识管理。 此蓝图还分配一个 Azure Policy 定义用于审核自定义 RBAC 规则的使用。 了解实施自定义 RBAC 规则的位置有助于验证需求以及实施是否适当，因为自定义 RBAC 规则容易出错。

- \[预览\]：审核具有所有者权限但未启用 MFA 的订阅帐户
- \[预览\]：审核具有写入权限但未启用 MFA 的订阅帐户
- \[预览\]：审核具有所有者权限的外部订阅帐户
- \[预览\]：审核具有写入权限的外部订阅帐户
- 审核确认已为 SQL Server 预配了 Azure Active Directory 管理员
- 审核确认已在 Service Fabric 中使用 Azure Active Directory，用于实施客户端身份验证
- 审核自定义 RBAC 规则的使用

## <a name="a924-management-of-secret-authentication-information-of-users"></a>A.9.2.4 管理用户的机密身份验证信息

此蓝图分配三个 [Azure Policy](../../../policy/overview.md) 定义用于审核未启用多重身份验证的帐户。 即使某个身份验证信息片段已泄密，多重身份验证也有助于保护帐户的安全。 通过监视未启用多重身份验证的帐户，可以识别出更有可能会泄密的帐户。 此蓝图还将分配两个 Azure Policy 定义用于审核 Linux VM 密码文件权限，并在这些权限设置不当时发出警报。 使用这种设置可以采取纠正措施，以确保验证器不会泄密。

- \[预览\]：审核具有所有者权限但未启用 MFA 的订阅帐户
- \[预览\]：审核具有读取权限但未启用 MFA 的订阅帐户
- \[预览\]：审核具有写入权限但未启用 MFA 的订阅帐户
- \[预览\]：部署 VM 扩展以审核 Linux VM 密码文件权限
- \[预览\]：审核 Linux VM /etc/密码文件权限是否设置为 0644

## <a name="a925-review-of-user-access-rights"></a>A.9.2.5 评审用户访问权限

Azure 实施了[基于角色的访问控制](../../../../role-based-access-control/overview.md) (RBAC) 来帮助你管理谁有权访问 Azure 中的资源。 使用 Azure 门户可以评审有权访问 Azure 资源的用户及其权限。 此蓝图分配四个 [Azure Policy](../../../policy/overview.md) 定义用于审核应该优先评审的帐户，包括已淘汰的帐户，以及具有提升权限的外部帐户。

- \[预览\]：审核已弃用的订阅帐户
- \[预览\]：审核具有所有者权限但已被弃用的订阅帐户
- \[预览\]：审核具有所有者权限的外部订阅帐户
- \[预览\]：审核具有写入权限的外部订阅帐户

## <a name="a926-removal-or-adjustment-of-access-rights"></a>A.9.2.6 删除或调整访问权限

Azure 实施了[基于角色的访问控制](../../../../role-based-access-control/overview.md) (RBAC) 来帮助你管理谁有权访问 Azure 中的资源。 使用 [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) 和 RBAC 可以更新用户角色，以反映组织变化。 如果需要，可以阻止帐户登录（或将其删除），这会立即删除其 Azure 资源访问权限。 此蓝图分配两个 [Azure Policy](../../../policy/overview.md) 定义用于审核应该考虑删除的已淘汰帐户。

- \[预览\]：审核已弃用的订阅帐户
- \[预览\]：审核具有所有者权限但已被弃用的订阅帐户

## <a name="a942-secure-log-on-procedures"></a>A.9.4.2 安全登录过程

此蓝图分配了三个 Azure Policy 定义，以用于审核未启用多重身份验证的帐户。 Azure 多重身份验证通过要求使用另一种形式的身份验证提供额外的安全性，从而提供增强式身份验证。 通过监视未启用多重身份验证的帐户，可以识别出更有可能会泄密的帐户。

- \[预览\]：审核具有所有者权限但未启用 MFA 的订阅帐户
- \[预览\]：审核具有读取权限但未启用 MFA 的订阅帐户
- \[预览\]：审核具有写入权限但未启用 MFA 的订阅帐户

## <a name="a943-password-management-system"></a>A.9.4.3 密码管理系统

此蓝图通过分配 10 个 [Azure Policy](../../../policy/overview.md) 定义用于审核不强制实施最低强度和其他密码要求的 Windows VM，来帮助你强制实施强密码。 识别违反密码强度策略的 VM 有助于采取纠正措施，以确保所有 VM 用户帐户的密码符合策略。

- \[预览\]：部署 VM 扩展以审核 Windows VM 是否强制实施密码复杂性要求
- \[预览\]：部署 VM 扩展以审核 Windows VM 最长密码期限是否为 70 天
- \[预览\]：部署 VM 扩展以审核 Windows VM 最短密码期限是否为 1 天
- \[预览\]：部署 VM 扩展以审核 Windows VM 密码必须至少为 14 个字符
- \[预览\]：部署 VM 扩展以审核 Windows VM 不应允许之前的 24 个密码
- \[预览\]：审核 Windows VM 是否强制实施密码复杂性要求
- \[预览\]：审核 Windows VM 最长密码期限是否为 70 天
- \[预览\]：审核 Windows VM 最短密码期限是否为 1 天
- \[预览\]：审核 Windows VM 密码必须至少为 14 个字符
- \[预览\]：审核 Windows VM 不应允许之前的 24 个密码

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>A.10.1.1 有关使用加密控制措施的策略

此蓝图通过分配 13 个 [Azure Policy](../../../policy/overview.md) 定义用于强制实施特定的加密控制措施并审核弱加密设置的使用，来帮助你针对加密控制措施的使用强制实施自己的策略。
了解 Azure 资源中的哪些位置采用欠佳的加密配置有助于采取纠正措施，以确保根据信息安全策略配置资源。 具体而言，此蓝图分配的策略要求对 Blob 存储帐户和 Data Lake Storage 帐户加密；要求对 SQL 数据库实施透明数据加密；审核存储帐户、SQL 数据库、虚拟机磁盘和自动化帐户变量是否缺少加密；审核是否与存储帐户、函数应用、Web 应用、API 应用和 Redis 缓存建立了不安全的连接；审核虚拟机弱密码加密；审核未加密的 Service Fabric 通信。

- \[预览\]：审核函数应用的仅 HTTPS 访问权限
- \[预览\]：审核 Web 应用的仅 HTTPS 访问权限
- \[预览\]：审核 API 应用的仅 HTTPS 访问权限
- \[预览\]：审核存储帐户是否缺少 blob 加密
- \[预览\]：部署 VM 扩展以审核 Windows VM 不应使用可逆加密存储密码
- \[预览\]：审核 Windows VM 不应使用可逆加密存储密码
- \[预览\]：监视 Azure 安全中心内未加密的 VM 磁盘
- 审核确认已启用自动化帐户变量加密功能
- 审核确认仅启用了到 Redis 缓存的安全连接
- 审核确认指向存储帐户的传输的安全性
- 审核确认 Service Fabric 中的 ClusterProtectionLevel 属性设置为 EncryptAndSign
- 审核透明数据加密状态
- 应在 SQL 数据库上启用透明数据加密

## <a name="a1241-event-logging"></a>A.12.4.1 事件日志记录

此蓝图通过分配七个 [Azure Policy](../../../policy/overview.md) 定义用于审核 Azure 资源的日志设置，来帮助你确保记录系统事件。
诊断日志针对 Azure 资源中执行的操作提供见解。

- \[预览\]：审核依赖项代理部署 - VM 映像 (OS) 未列出
- \[预览\]：审核 VMSS 中的依赖项代理部署 - VM 映像 (OS) 未列出
- \[预览\]：审核 Log Analytics 代理部署 - VM 映像 (OS) 未列出
- \[预览\]：审核 VMSS 中的 Log Analytics 代理部署 - VM 映像 (OS) 未列出
- 审核诊断设置
- 审核 SQL 服务器级别审核设置
- 应在 SQL Server 的高级数据安全设置上启用审核

## <a name="a1243-administrator-and-operator-logs"></a>A.12.4.3 管理员和操作员日志

此蓝图分配了七个 Azure Policy 定义，以用于审核 Azure 资源的日志设置，从而帮助你确保系统事件会被记录。 诊断日志针对 Azure 资源中执行的操作提供见解。

- \[预览\]：审核依赖项代理部署 - VM 映像 (OS) 未列出
- \[预览\]：审核 VMSS 中的依赖项代理部署 - VM 映像 (OS) 未列出
- \[预览\]：审核 Log Analytics 代理部署 - VM 映像 (OS) 未列出
- \[预览\]：审核 VMSS 中的 Log Analytics 代理部署 - VM 映像 (OS) 未列出
- 审核诊断设置
- 审核 SQL 服务器级别审核设置
- 应在 SQL Server 的高级数据安全设置上启用审核

## <a name="a1244-clock-synchronization"></a>A.12.4.4 时钟同步

此蓝图分配了七个 Azure Policy 定义，以用于审核 Azure 资源的日志设置，从而帮助你确保系统事件会被记录。 Azure 日志依赖于同步的内部时钟创建各个资源中事件的时间相关记录。

- \[预览\]：审核依赖项代理部署 - VM 映像 (OS) 未列出
- \[预览\]：审核 VMSS 中的依赖项代理部署 - VM 映像 (OS) 未列出
- \[预览\]：审核 Log Analytics 代理部署 - VM 映像 (OS) 未列出
- \[预览\]：审核 VMSS 中的 Log Analytics 代理部署 - VM 映像 (OS) 未列出
- 审核诊断设置
- 审核 SQL 服务器级别审核设置
- 应在 SQL Server 的高级数据安全设置上启用审核

## <a name="a1251-installation-of-software-on-operational-systems"></a>A.12.5.1 在可操作的系统上安装软件

自适应应用程序控制是 Azure 安全中心内的一个解决方案，可帮助你控制哪些应用程序可在 Azure 中的 VM 上运行。 此蓝图分配一个 Azure Policy 定义用于监视对允许的应用程序集的更改。 此功能帮助你控制软件和应用程序在 Azure VM 上的安装。

- \[预览\]：监视 Azure 安全中心内列入允许列表的可能的应用

## <a name="a1261-management-of-technical-vulnerabilities"></a>A.12.6.1 管理技术漏洞

此蓝图分配了五个 [Azure Policy](../../../policy/overview.md) 定义，以用于在 Azure 安全中心内监视缺少的系统更新、操作系统漏洞、SQL 漏洞和虚拟机漏洞，来帮助你管理信息系统漏洞。 Azure 安全中心提供报告功能，使你能够实时洞察已部署的 Azure 资源的安全状态。

- \[预览\]：监视 Azure 安全中心 Endpoint Protection 的缺失情况
- \[预览\]：监视 Azure 安全中心内系统更新的缺失情况
- \[预览\]：监视 Azure 安全中心的 OS 漏洞
- \[预览\]：监视 Azure 安全中心的 SQL 漏洞评估结果
- \[预览\]：监视 Azure 安全中心的 VM 漏洞

## <a name="a1262-restrictions-on-software-installation"></a>A.12.6.2 软件安装的限制

自适应应用程序控制是 Azure 安全中心内的一个解决方案，可帮助你控制哪些应用程序可在 Azure 中的 VM 上运行。 此蓝图分配一个 Azure Policy 定义用于监视对允许的应用程序集的更改。 软件安装限制有助于减少出现软件漏洞的可能性。

- \[预览\]：监视 Azure 安全中心内列入允许列表的可能的应用

## <a name="a1311-network-controls"></a>A.13.1.1 网络控制措施

此蓝图通过分配一个 [Azure Policy](../../../policy/overview.md) 定义用于监视具有宽松规则的网络安全组，来帮助你管理和控制网络。 过于宽松的规则可能会允许意外的网络访问，应该对其进行评审。 此蓝图还分配了三个 Azure Policy 定义，以用于监视不受保护的终结点、应用程序和存储帐户。 不受防火墙保护的终结点和应用程序，以及具有无限制访问权限的存储帐户，可能会允许意外访问信息系统中包含的信息。

- \[预览\]：监视 Azure 安全中心内规则较宽松的网络访问
- \[预览\]：监视 Azure 安全中心内未受保护的网络终结点
- \[预览\]：监视 Azure 安全中心内未受保护的 Web 应用程序
- 审核对存储帐户的不受限的网络访问

## <a name="a1321-information-transfer-policies-and-procedures"></a>A.13.2.1 信息传输策略和过程

该蓝图通过分配两个 [Azure Policy](../../../policy/overview.md) 定义用于审核与存储帐户和 Redis 缓存建立的不安全连接，来帮助你确保与 Azure 服务之间安全传输信息。

- 审核确认仅启用了到 Redis 缓存的安全连接
- 审核确认指向存储帐户的传输的安全性

## <a name="next-steps"></a>后续步骤

了解 ISO 27001 蓝图的控制映射后，请访问以下文章来了解体系结构以及如何部署此示例：

> [!div class="nextstepaction"]
> [ISO 27001 蓝图 - 概述](./index.md)
> [ISO 27001 蓝图 - 部署步骤](./deploy.md)

有关蓝图及其使用方式的更多文章：

- 了解[蓝图生命周期](../../concepts/lifecycle.md)。
- 了解如何使用[静态和动态参数](../../concepts/parameters.md)。
- 了解如何自定义[蓝图排序顺序](../../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../../concepts/resource-locking.md)。
- 了解如何[更新现有分配](../../how-to/update-existing-assignments.md)。