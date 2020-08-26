---
title: HIPAA HITRUST 蓝图示例控制
description: HIPAA HITRUST 蓝图示例的控制映射。 每个控制都映射到一个或多个协助评估的 Azure 策略。
ms.date: 08/03/2020
ms.topic: sample
ms.openlocfilehash: 10b771e3cfb18a28bd720332a26e13bb1d1f6022
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2020
ms.locfileid: "88209414"
---
# <a name="control-mapping-of-the-hipaa-hitrust-blueprint-sample"></a>HIPAA HITRUST 蓝图示例的控制映射

下文详细说明了 Azure 蓝图 HIPAA HITRUST 蓝图示例如何映射到 HIPAA HITRUST 控制。 有关控制的详细信息，请参阅 [HIPAA HITRUST](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html)。

以下映射适用于 HIPAA HITRUST 控制****。 使用右侧的导航栏可直接跳转到特定的控制映射。 许多的映射控制措施都是使用 [Azure Policy](../../../policy/overview.md) 计划实施的。 若要查看完整计划，请在 Azure 门户中打开“策略”，并选择“定义”页。 然后，找到并选择“ **\[预览\]：审核 HIPAA HITRUST 控制的内置策略计划**。

> [!IMPORTANT]
> 下面的每个控件都与一个或多个 [Azure Policy](../../../policy/overview.md) 定义关联。 这些策略可以帮助你[评估控件的符合性](../../../policy/how-to/get-compliance-data.md)；但是，控件与一个或多个策略之间通常不是 1:1 或完全匹配。 因此，Azure Policy 中的符合性仅引用策略本身；这不确保你完全符合控件的所有要求。 此外，符合性标准包含目前未由任何 Azure Policy 定义处理的控件。 因此，Azure Policy 中的符合性只是整体符合性状态的部分视图。 此符合性蓝图示例的控件和 Azure Policy 定义之间的关联可能会随着时间的推移而发生变化。 若要查看更改历史记录，请参阅 [GitHub 提交历史记录](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/HIPAA-HITRUST/control-mapping.md)。

## <a name="control-against-malicious-code"></a>恶意代码控制

此蓝图分配 [Azure Policy](../../../policy/overview.md) 定义用于监视 Azure 安全中心中虚拟机上缺失的终结点防护并在 Windows 虚拟机上强制执行 Microsoft 反恶意软件解决方案，从而帮助管理终结点防护，包括恶意代码防护。

- Microsoft Antimalware for Azure 应配置为自动更新保护签名
- 监视 Azure 安全中心 Endpoint Protection 的缺失情况
- 应在虚拟机规模集上安装 Endpoint Protection 解决方案
- 应在虚拟机上启用自适应应用程序控制


## <a name="management-of-removable-media"></a>可移动媒体管理

组织根据数据分类级别，在使用之前注册媒体（包括便携式计算机），对此类媒体的使用方式施加合理的限制，并为包含相应信息的媒体提供适当级别的物理和逻辑保护，直至妥善销毁或清理。

- 应在 SQL 数据库上启用透明数据加密
- 应在虚拟机上启用磁盘加密
- 应加密未附加的磁盘
- 要求对 Data Lake Store 帐户加密
- 应使用自己的密钥加密 SQL 服务器的 TDE 保护器
- 应使用自己的密钥加密 SQL 托管实例的 TDE 保护器

## <a name="control-of-operational-software"></a>操作软件控制 

组织识别信息系统（包括服务器、工作站和便携式计算机）上的未经授权软件，使用 allow-all、deny-by-exception 策略来禁止在信息系统上执行已知的未经授权软件，并定期（至少每年）审阅和更新未经授权软件的列表。

- 应该修复计算机上安全配置中的漏洞
- 应该修复容器安全配置中的漏洞
- 应该修复虚拟机规模集上安全配置中的漏洞
- 应在虚拟机上启用自适应应用程序控制

## <a name="change-control-procedures"></a>更改控制过程

通过记录对虚拟机映像进行的任何更改并发出警报，同时使业务所有者和/或客户通过电子方法（例如门户或警报）了解更改或移动的结果以及后续对映像完整性进行的验证，始终确保所有虚拟机映像的完整性。

- \[预览\] 显示“系统审核策略 - 详细跟踪”中 Windows VM 配置的审核结果
- \[预览\]：显示“系统审核策略 - 详细跟踪”中 Windows VM 配置的审核结果

## <a name="control-of-technical-vulnerabilities"></a>技术漏洞控制 

所有系统和网络组件都存在强化的配置标准。

- 应对 SQL Server 启用漏洞评估
- 应对 SQL 托管实例启用漏洞评估
- \[预览版\] 应在虚拟机上启用漏洞评估
- 应该通过漏洞评估解决方案修复漏洞
- 应该修复计算机上安全配置中的漏洞
- 应该修复虚拟机规模集上安全配置中的漏洞
- 应该修复容器安全配置中的漏洞
- 应该修复 SQL 数据库中的漏洞
- \[预览\]：应在 Kubernetes 服务上定义 Pod 安全策略

## <a name="segregation-in-networks"></a>网络隔离

组织的安全网关（例如防火墙）强制执行安全策略，并配置为筛选域之间的流量、阻止未经授权的访问。它用于维护内部有线、内部无线和外部网络段（如 Internet，包括 DMZ）之间的隔离，并对每个域强制执行访问控制策略。

- 子网应与网络安全组关联
- 虚拟机应连接到已批准的虚拟网络
- 虚拟机应与网络安全组关联
- 服务总线应使用虚拟网络服务终结点
- 应用服务应使用虚拟网络服务终结点
- SQL Server 应使用虚拟网络服务终结点
- 事件中心应使用虚拟网络服务终结点
- Cosmos DB 应使用虚拟网络服务终结点
- Key Vault 应使用虚拟网络服务终结点
- 不应在网关子网中配置网络安全组
- 存储帐户应使用虚拟网络服务终结点
- \[预览\]：容器注册表应使用虚拟网络服务终结点
- 应在面向内部的虚拟机上应用自适应网络强化建议

## <a name="network-connection-control"></a>网络连接控制

通过防火墙以及每个网络访问点或外部电信服务托管接口的其他网络相关限制，根据组织的访问控制策略来控制网络流量。

- 应该启用安全传输到存储帐户
- 应在 API 应用中使用最新的 TLS 版本
- 应在 Web 应用中使用最新的 TLS 版本
- 应在函数应用中使用最新的 TLS 版本
- 应该只能通过 HTTPS 访问函数应用
- 只能通过 HTTPS 访问 Web 应用程序
- 只能通过 HTTPS 访问 API 应用
- 应为 MySQL 数据库服务器启用“强制 SSL 连接”
- 应为 PostgreSQL 数据库服务器启用“强制 SSL 连接”
- 应该启用只能通过安全方式连接到 Redis 缓存
- 子网应与网络安全组关联
- 应该强化 IaaS 上 Web 应用程序的 NSG 规则
- 应该强化面向 Internet 的虚拟机的网络安全组规则
- 虚拟机应连接到已批准的虚拟网络
- 虚拟机应与网络安全组关联

## <a name="network-controls"></a>网络控制

组织在将物理服务器、应用程序或数据迁移到虚拟化服务器时，使用安全且加密的信道。

- 应在虚拟机上应用实时网络访问控制
- 应在面向内部的虚拟机上应用自适应网络强化建议
- 服务总线应使用虚拟网络服务终结点
- 应用服务应使用虚拟网络服务终结点
- SQL Server 应使用虚拟网络服务终结点
- 事件中心应使用虚拟网络服务终结点
- Cosmos DB 应使用虚拟网络服务终结点
- Key Vault 应使用虚拟网络服务终结点
- 审核对存储帐户的不受限的网络访问
- 存储帐户应使用虚拟网络服务终结点
- \[预览\]：容器注册表应使用虚拟网络服务终结点

## <a name="security-of-network-services"></a>网络服务安全

由网络服务提供商/管理员提供的协议服务将受到正式管理和监视，以确保安全地提供这些服务。

- \[预览\]：应在 Windows 虚拟机上安装网络流量数据收集代理
- \[预览\]：应在 Linux 虚拟机上安装网络流量数据收集代理 应启用网络观察程序

## <a name="information-exchange-policies-and-procedures"></a>信息交换策略和过程

组织限制了授权人员在外部信息系统上使用由组织控制的便携式存储媒体。

- 确保 WEB 应用的“客户端证书(传入客户端证书)”设置为“打开”
- CORS 不应允许所有资源访问 Web 应用程序
- CORS 不应允许所有资源访问函数应用
- CORS 不应允许所有资源访问 API 应用
- 应当为 Web 应用程序禁用远程调试
- 应当为函数应用禁用远程调试
- 应当为 API 应用禁用远程调试

## <a name="on-line-transactions"></a>线上交易

组织要求参与交易的各方使用加密并使用电子签名。组织确保不将交易详情存储在任何可公开访问的环境（例如组织 Intranet 上的存储平台），并且不会将这些信息保留和发布到可从 Internet 直接访问的存储媒体上。如果出于签发和维护数字签名和/或数字证书的目的使用受信任的机构，则在整个端到端证书/签名管理过程中都可确保安全。

- 应该启用安全传输到存储帐户
- 应在 API 应用中使用最新的 TLS 版本
- 应在 Web 应用中使用最新的 TLS 版本
- 应在函数应用中使用最新的 TLS 版本
- 应该只能通过 HTTPS 访问函数应用
- 只能通过 HTTPS 访问 Web 应用程序
- 只能通过 HTTPS 访问 API 应用
- 应为 MySQL 数据库服务器启用“强制 SSL 连接”
- 应为 PostgreSQL 数据库服务器启用“强制 SSL 连接”
- 应该启用只能通过安全方式连接到 Redis 缓存
- \[预览\]：部署必备组件用于审核在受信任的根中不包含指定证书的 Windows VM
- \[预览\]：显示在受信任的根中不包含指定证书的 Windows VM 的审核结果

## <a name="user-password-management"></a>用户密码管理

在所有系统组件上传输和存储期间，密码均已加密。

- \[预览\]审核带有不安全密码安全设置的 VM

## <a name="user-authentication-for-external-connections"></a>外部连接用户身份验证

对于组织网络的所有外部连接，实现了强大的身份验证方法，如多重身份验证、Radius 或 Kerberos（用于特权访问）和 CHAP（用于加密拨号方法的凭据）。

- 应在对订阅拥有所有者权限的帐户上启用 MFA
- 应在对订阅拥有写入权限的帐户上启用 MFA
- 应在对订阅拥有读取权限的帐户上启用 MFA
- 应在虚拟机上应用实时网络访问控制

## <a name="user-identification-and-authentication"></a>用户识别和身份验证

执行特权功能（如系统管理）的用户在执行这些特权功能时使用单独的帐户。根据组织策略（例如远程网络访问策略）使用多重身份验证方法。

- 应在对订阅拥有所有者权限的帐户上启用 MFA
- 应在对订阅拥有写入权限的帐户上启用 MFA
- 应在对订阅拥有读取权限的帐户上启用 MFA
- 只多只为订阅指定 3 个所有者
- 应该为你的订阅分配了多个所有者
- 部署先决条件来审核管理员组包含任意指定成员的 Windows VM
- 显示其中的“管理员”组包含任意指定成员的 Windows VM 的审核结果
- 部署先决条件，以便审核在其管理员组中不包含所有指定成员的 Windows VM
- 显示来自 Windows VM 的审核结果，其中的管理员组不包含所有指定成员
- 部署必备组件以审核其中的“管理员”组不只包含指定成员的 Windows VM
- 显示其中的“管理员”组不只包含指定成员的 Windows VM 的审核结果

## <a name="privilege-management"></a>特权管理

在托管虚拟化系统的系统中，只有符合最小特权原则的人员才能访问管理功能或管理控制台，并通过技术控制得以实现。

- 应在虚拟机上应用实时网络访问控制
- 应关闭虚拟机上的管理端口
- 只多只为订阅指定 3 个所有者
- 应该为你的订阅分配了多个所有者
- 应从订阅中删除拥有所有者权限的外部帐户
- 应从订阅中删除拥有所有者权限的已弃用帐户
- 审核自定义 RBAC 规则的使用
- 应在 Kubernetes 服务中使用基于角色的访问控制 (RBAC)

## <a name="review-of-user-access-rights"></a>审阅用户访问权限

组织维护信息资产授权用户的记录清单。

- 审核自定义 RBAC 规则的使用

## <a name="remote-diagnostic-and-configuration-port-protection"></a>远程诊断和配置端口保护

禁用或删除在计算机或网络系统上安装的端口、服务以及类似的应用程序（这些应用程序不是业务功能所必需的）。

- 应在虚拟机上应用实时网络访问控制
- 应关闭虚拟机上的管理端口
- 应当为 Web 应用程序禁用远程调试
- 应当为函数应用禁用远程调试
- 应当为 API 应用禁用远程调试
- 应在虚拟机上启用自适应应用程序控制

## <a name="audit-logging"></a>审核日志

保留发送和接受的消息日志，包括消息的日期、时间、来源和目标，但不包括消息的内容。当系统处于活动状态时，始终进行审核并跟踪关键事件、成功/失败的数据访问、系统安全配置更改、特权或实用程序的使用情况、引发的任何警报、保护系统的激活和停用（如 A/V 和 IDS）、识别和身份验证机制的激活和停用，以及系统级对象的创建和删除。

- 应启用 Azure Data Lake Store 中的诊断日志
- 应启用逻辑应用的诊断日志 
- 应启用 IoT 中心的诊断日志 
- 应启用 Batch 帐户中的诊断日志 
- 应启用虚拟机规模集中的诊断日志 
- 应启用事件中心内的诊断日志 
- 应启用搜索服务的诊断日志 
- 应启用应用服务中的诊断日志 
- 应启用 Data Lake Analytics 中的诊断日志 
- 应启用 Key Vault 中的诊断日志 
- 应启用服务总线中的诊断日志
- 应该在 Azure 流分析中启用诊断日志
- 应启用 SQL 服务器上的审核
- 审核诊断设置
- Azure Monitor 应从所有区域收集活动日志

## <a name="monitoring-system-use"></a>监视系统使用情况

在整个组织环境中部署的自动系统用于监视关键事件和异常活动并分析系统日志，这些结果会定期审阅。监视包括特权操作、授权访问或未经授权的访问尝试（包括尝试访问停用的帐户），以及系统警报或故障。

- Azure Monitor 应从所有区域收集活动日志
- 应在虚拟机上安装 Log Analytics 代理
- 应在虚拟机规模集上安装 Log Analytics 代理
- \[预览\]：部署必备组件以审核其上的 Log Analytics 代理未按预期连接的 Windows VM
- \[预览\]：显示其上的 Log Analytics 代理未按预期连接的 Windows VM 的审核结果
- Azure Monitor 日志配置文件应收集“写入”、“删除”和“操作”类别的日志
- 应该对订阅启用 Log Analytics 监视代理的自动预配

## <a name="segregation-of-duties"></a>职责分离

职责分离用于限制未经授权或无意中修改信息和系统的风险。 未经授权或检测，任何人都不能访问、修改或使用信息系统。 根据每个用户的角色和职责，负责管理和访问控制的人员的访问权限必须限制在最低必需权限，并且这些人员不能访问与这些控制相关的审核功能。

- 应在 Kubernetes 服务中使用基于角色的访问控制 (RBAC)
- 审核自定义 RBAC 规则的使用
- \[预览\]：部署必备组件用于审核“用户权限分配”中的 Windows VM 配置
- \[预览\]：显示“用户权限分配”中 Windows VM 配置的审核结果
- \[预览\]：部署必备组件用于审核“安全选项 - 用户帐户控制”中的 Windows VM 配置
- \[预览\]：显示“安全选项 - 用户帐户控制”中 Windows VM 配置的审核结果
- 不应存在自定义订阅所有者角色

## <a name="administrator-and-operator-logs"></a>管理员和操作员日志

组织确保启用正确的日志记录，以便审核管理员活动；并定期审阅系统管理员和操作员日志。

- 特定管理操作应有活动日志警报

## <a name="identification-of-risks-related-to-external-parties"></a>识别与外部各方相关的风险

对组织与外部各方之间的远程访问连接进行加密

- 应该启用安全传输到存储帐户
- 应该只能通过 HTTPS 访问函数应用
- 只能通过 HTTPS 访问 Web 应用程序
- 只能通过 HTTPS 访问 API 应用
- 应为 MySQL 数据库服务器启用“强制 SSL 连接”
- 应为 PostgreSQL 数据库服务器启用“强制 SSL 连接”
- 应该启用只能通过安全方式连接到 Redis 缓存

## <a name="business-continuity-and-risk-assessment"></a>业务连续性和风险评估

组织确定其关键业务流程，并将业务连续性的信息安全管理要求与其他方面（如操作、人员配备、材料、运输和设施）的连续性要求相结合。

- 审核没有配置灾难恢复的虚拟机
- 应可恢复 Key Vault 对象
- \[预览\]：部署必备组件用于审核“安全选项 - 恢复控制台”中的 Windows VM 配置
- \[预览\] 显示“安全选项 - 恢复控制台”中 Windows VM 配置的审核结果

## <a name="back-up"></a>备份

此蓝图以电子方式将对组织的系统备份信息进行审核的 Azure 策略定义分配给备用存储站点。 对于存储元数据的物理装运，请考虑使用 Azure Data Box。

- 应为 Azure SQL 数据库启用长期异地冗余备份
- 应为 Azure Database for MySQL 启用异地冗余备份
- 应为 Azure Database for PostgreSQL 启用异地冗余备份
- 应为 Azure Database for MariaDB 启用异地冗余备份
- 应为虚拟机启用 Azure 备份

> [!NOTE]
> 特定 Azure Policy 定义的可用性在 Azure 政府和其他国家云中可能会有所不同。 

## <a name="next-steps"></a>后续步骤

你已查看了 HIPAA HITRUST 蓝图示例的控制映射。 接下来，请访问以下文章，了解概述以及如何部署此示例：

> [!div class="next step action"]
> [HIPAA HITRUST 蓝图 - 概述](./control-mapping.md)
> [HIPAA HITRUST 蓝图 - 部署步骤](./deploy.md)

有关蓝图及其使用方式的更多文章：

- 了解[蓝图生命周期](../../concepts/lifecycle.md)。
- 了解如何使用[静态和动态参数](../../concepts/parameters.md)。
- 了解如何自定义[蓝图排序顺序](../../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../../concepts/resource-locking.md)。
- 了解如何[更新现有分配](../../how-to/update-existing-assignments.md)。
