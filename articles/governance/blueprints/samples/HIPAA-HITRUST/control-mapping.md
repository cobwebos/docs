---
title: HIPAA HITRUST 蓝图示例控制
description: HIPAA HITRUST 蓝图示例的控制映射。 每个控制都映射到一个或多个协助评估的 Azure 策略。
ms.date: 11/26/2019
ms.topic: sample
ms.openlocfilehash: c6680055cfe41a2f893d46cba797783913d880ce
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75470742"
---
# <a name="control-mapping-of-the-hipaa-hitrust-blueprint-sample"></a>HIPAA HITRUST 蓝图示例的控制映射

下文详细说明了 Azure 蓝图 HIPAA HITRUST 蓝图示例如何映射到 HIPAA HITRUST 控制。 有关控制的详细信息，请参阅 [HIPAA HITRUST](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html)。

以下映射适用于 HIPAA HITRUST 控制  。 使用右侧的导航栏可直接跳转到特定的控制映射。 许多的映射控制措施都是使用 [Azure Policy](../../../policy/overview.md) 计划实施的。 若要查看完整计划，请在 Azure 门户中打开“策略”，并选择“定义”页。   然后，找到并选择“ **\[预览\]：审核 HIPAA HITRUST 控制的内置策略计划**。

> [!IMPORTANT]
> 下面的每个控件都与一个或多个 [Azure Policy](../../../policy/overview.md) 定义关联。 这些策略可以帮助你[评估控件的符合性](../../../policy/how-to/get-compliance-data.md)；但是，控件与一个或多个策略之间通常不是 1:1 或完全匹配。 因此，Azure Policy 中的符合性  仅引用策略本身；这不确保你完全符合控件的所有要求。 此外，符合性标准包含目前未由任何 Azure Policy 定义处理的控件。 因此，Azure Policy 中的符合性只是整体符合性状态的部分视图。 此符合性蓝图示例的控件和 Azure Policy 定义之间的关联可能会随着时间的推移而发生变化。 若要查看更改历史记录，请参阅 [GitHub 提交历史记录](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/HIPAA-HITRUST/control-mapping.md)。

## <a name="control-against-malicious-code"></a>恶意代码控制

此蓝图分配 [Azure Policy](../../../policy/overview.md) 定义用于监视 Azure 安全中心中虚拟机上缺失的终结点防护并在 Windows 虚拟机上强制执行 Microsoft 反恶意软件解决方案，从而帮助管理终结点防护，包括恶意代码防护。

- 为 Windows Server 部署默认 Microsoft IaaS Antimalware 扩展
- 应启用 Batch 帐户中的诊断日志
- 应在计算机上安装系统更新


## <a name="management-of-removable-media"></a>可移动媒体管理

组织根据数据分类级别，在使用之前注册媒体（包括便携式计算机），对此类媒体的使用方式施加合理的限制，并为包含相应信息的媒体提供适当级别的物理和逻辑保护，直至妥善销毁或清理。

- 要求对 Data Lake Store 帐户加密
- 应使用自己的密钥加密 SQL 托管实例的 TDE 保护器
- 应在虚拟机上启用磁盘加密
- 应在 SQL 数据库上启用透明数据加密


## <a name="information-exchange-policies-and-procedures"></a>信息交换策略和过程

云服务提供商使用行业认可的虚拟化平台和标准虚拟化格式（例如开放虚拟化格式 (OVF)）来帮助确保互操作性，并记录对使用的任何虚拟机监控程序进行的自定义更改，以及特定于解决方案且供客户审阅的所有虚拟化挂钩。

- 部署先决条件，以审核未安装指定应用程序的 Windows VM

## <a name="control-of-operational-software"></a>操作软件控制 

组织识别信息系统（包括服务器、工作站和便携式计算机）上的未经授权软件，使用 allow-all、deny-by-exception 策略来禁止在信息系统上执行已知的未经授权软件，并定期（至少每年）审阅和更新未经授权软件的列表。

- \[预览\] 显示“安全选项 - 审核”中 Windows VM 配置的审核结果
- \[预览\] 显示“系统审核策略 - 帐户管理”中 Windows VM 配置的审核结果

## <a name="change-control-procedures"></a>更改控制过程

通过记录对虚拟机映像进行的任何更改并发出警报，同时使业务所有者和/或客户通过电子方法（例如门户或警报）了解更改或移动的结果以及后续对映像完整性进行的验证，始终确保所有虚拟机映像的完整性。

- \[预览\] 显示“系统审核策略 - 详细跟踪”中 Windows VM 配置的审核结果

## <a name="inventory-of-assets"></a>资产清单 

维护资产和服务清单

- 应启用搜索服务中的诊断日志。
- \[预览\] 部署要求以审核“安全选项 - Microsoft 网络服务器”中的 Windows VM 配置
- \[预览\] 部署要求以审核“管理模板 - 网络”中的 Windows VM 配置

## <a name="control-of-technical-vulnerabilities"></a>技术漏洞控制 

所有系统和网络组件都存在强化的配置标准。

- 审核未配置灾难恢复的虚拟机
- 应对 SQL 托管实例启用漏洞评估
- 应通过漏洞评估解决方案修复漏洞

## <a name="segregation-in-networks"></a>网络隔离

组织的安全网关（例如防火墙）强制执行安全策略，并配置为筛选域之间的流量、阻止未经授权的访问。它用于维护内部有线、内部无线和外部网络段（如 Internet，包括 DMZ）之间的隔离，并对每个域强制执行访问控制策略。

- 安全监视代理的自动设置
- 创建虚拟网络时部署网络观察程序

## <a name="input-data-validation"></a>输入数据验证

对于面向公众的任何 Web 应用程序，实现了应用程序级别的防火墙来控制流量。 对于面向公众但不基于 Web 的应用程序，组织实现了特定于应用程序类型且基于网络的防火墙。 如果对面向公众的应用程序的流量进行了加密，则设备要么位于加密之后，要么能够在分析之前解密流量。

- \[预览\] 显示“Windows 防火墙属性”中 Windows VM 配置的审核结果


## <a name="network-connection-control"></a>网络连接控制

通过防火墙以及每个网络访问点或外部电信服务托管接口的其他网络相关限制，根据组织的访问控制策略来控制网络流量。

- 应限制通过面向 Internet 的终结点进行访问
- 应使用自己的密钥加密 SQL 托管实例的 TDE 保护器
- 应为 API 应用禁用远程调试

## <a name="network-controls"></a>网络控制

组织在将物理服务器、应用程序或数据迁移到虚拟化服务器时，使用安全且加密的信道。

- 应在 VM 上应用磁盘加密
- 应使用自己的密钥加密 SQL 服务器的 TDE 保护器
- \[预览\] 显示“安全选项 - 网络访问”中 Windows VM 配置的审核结果
- 审核对存储帐户的不受限的网络访问
- \[预览\] 显示“Windows 防火墙属性”中 Windows VM 配置的审核结果
- 从网络安全组部署诊断设置
- 应限制通过面向 Internet 的终结点进行访问

## <a name="sensitive-system-isolation"></a>敏感系统隔离

共享的系统资源（例如寄存器、主内存、辅助存储器）会发回到系统，防止泄露给其他系统/应用程序/用户，并且用户无法有意或无意地访问残留的信息。

- 应将虚拟机迁移到新的 Azure 资源管理器资源

## <a name="security-of-network-services"></a>网络服务安全

由网络服务提供商/管理员提供的协议服务将受到正式管理和监视，以确保安全地提供这些服务。

- 应将虚拟机迁移到新的 Azure 资源管理器资源

## <a name="network-routing-control"></a>网络路由控制

路由控制是通过在内部和外部网络（如 Internet 和第三方网络）之间使用安全网关（如防火墙）来实现的。

- 应在虚拟机上启用自适应应用程序控制

## <a name="information-exchange-policies-and-procedures"></a>信息交换策略和过程

组织限制了授权人员在外部信息系统上使用由组织控制的便携式存储媒体。

- 审核对存储帐户的不受限的网络访问
- 应禁用 Web 应用程序的远程调试
- 只能通过 HTTPS 访问 API 应用

## <a name="electronic-messaging"></a>电子消息

在使用外部公共服务（包括即时消息或文件共享）之前需要获得审批。

- \[预览\] 显示未将密码文件权限设置为 0644 的 Linux VM 的审核结果

## <a name="on-line-transactions"></a>线上交易

组织要求参与交易的各方使用加密并使用电子签名。组织确保不将交易详情存储在任何可公开访问的环境（例如组织 Intranet 上的存储平台），并且不会将这些信息保留和发布到可从 Internet 直接访问的存储媒体上。如果出于签发和维护数字签名和/或数字证书的目的使用受信任的机构，则在整个端到端证书/签名管理过程中都可确保安全。

- 应在 VM 上应用磁盘加密
- \[预览\] 显示在受信任的根中未包含指定证书的 Windows VM 的审核结果

## <a name="password-management"></a>密码管理

在所有系统组件上传输和存储期间，密码均已加密。

- \[预览\] 显示未启用密码复杂性设置的 Windows VM 的审核结果

## <a name="user-authentication-for-external-connections"></a>外部连接用户身份验证

对于组织网络的所有外部连接，实现了强大的身份验证方法，如多重身份验证、Radius 或 Kerberos（用于特权访问）和 CHAP（用于加密拨号方法的凭据）。

- 应对订阅中拥有写入权限的帐户启用 MFA
- 应在 VM 上应用实时网络访问控制

## <a name="user-identification-and-authentication"></a>用户识别和身份验证

执行特权功能（如系统管理）的用户在执行这些特权功能时使用单独的帐户。根据组织策略（例如远程网络访问策略）使用多重身份验证方法。

- 应对订阅中拥有写入权限的帐户启用 MFA
- 应在 VM 上应用实时网络访问控制

## <a name="privilege-management"></a>特权管理

在托管虚拟化系统的系统中，只有符合最小特权原则的人员才能访问管理功能或管理控制台，并通过技术控制得以实现。

- 应在 VM 上应用实时网络访问控制
- \[预览\] 应在 Kubernetes 服务上使用基于角色的访问控制 (RBAC)

## <a name="review-of-user-access-rights"></a>审阅用户访问权限

组织维护信息资产授权用户的记录清单。

- \[预览\] 显示“安全选项 - 帐户”中 Windows VM 配置的审核结果

## <a name="remote-diagnostic-and-configuration-port-protection"></a>远程诊断和配置端口保护

禁用或删除在计算机或网络系统上安装的端口、服务以及类似的应用程序（这些应用程序不是业务功能所必需的）。

- 应关闭虚拟机上的管理端口
- 应修复虚拟机规模集上的安全配置漏洞

## <a name="audit-logging"></a>审核日志

保留发送和接受的消息日志，包括消息的日期、时间、来源和目标，但不包括消息的内容。当系统处于活动状态时，始终进行审核并跟踪关键事件、成功/失败的数据访问、系统安全配置更改、特权或实用程序的使用情况、引发的任何警报、保护系统的激活和停用（如 A/V 和 IDS）、识别和身份验证机制的激活和停用，以及系统级对象的创建和删除。

- 应启用事件中心内的诊断日志
- 应在虚拟机规模集上安装系统更新

## <a name="monitoring-system-use"></a>监视系统使用情况

在整个组织环境中部署的自动系统用于监视关键事件和异常活动并分析系统日志，这些结果会定期审阅。监视包括特权操作、授权访问或未经授权的访问尝试（包括尝试访问停用的帐户），以及系统警报或故障。

- 应启用虚拟机规模集中的诊断日志

## <a name="segregation-of-duties"></a>职责分离

职责分离用于限制未经授权或无意中修改信息和系统的风险。 未经授权或检测，任何人都不能访问、修改或使用信息系统。 根据每个用户的角色和职责，负责管理和访问控制的人员的访问权限必须限制在最低必需权限，并且这些人员不能访问与这些控制相关的审核功能。

- 应对订阅中拥有写入权限的帐户启用 MFA

## <a name="administrator-and-operator-logs"></a>管理员和操作员日志

组织确保启用正确的日志记录，以便审核管理员活动；并定期审阅系统管理员和操作员日志。

- 应对订阅中拥有写入权限的帐户启用 MFA

## <a name="identification-of-risks-related-to-external-parties"></a>识别与外部各方相关的风险

对组织与外部各方之间的远程访问连接进行加密

- 应在虚拟机上启用磁盘加密

## <a name="business-continuity-and-risk-assessment"></a>业务连续性和风险评估

组织确定其关键业务流程，并将业务连续性的信息安全管理要求与其他方面（如操作、人员配备、材料、运输和设施）的连续性要求相结合。

- \[预览\] 显示“安全选项 - 恢复控制台”中 Windows VM 配置的审核结果

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
