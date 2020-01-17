---
title: Azure 安全中心的增强安全评分（预览版）
description: Azure 安全中心的增强安全评分（预览）和安全控制的说明
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetd: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/04/2019
ms.author: memildin
ms.openlocfilehash: 8f0b7b1225d78c7626c5be3e68c3dcd2dd557aa3
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2020
ms.locfileid: "76158151"
---
# <a name="the-enhanced-secure-score-preview"></a>增强的安全评分（预览） 

本文介绍了增强的安全评分（目前以预览版提供）、随附的安全控制措施及其带来的优势。

## <a name="introduction-to-secure-score"></a>安全分数简介

Azure 安全中心有两个主要目标：帮助你了解当前的安全情况，以及帮助你有效有效地提高安全性。 安全中心的核心特性使你能够实现这些目标，这是安全分数。

安全中心会持续评估你的资源、订阅和组织的安全问题。 然后，它将所有发现聚合为一个分数，以便您可以一目了然地了解当前的安全情况：分数越高，确定的风险级别越低。 使用评分跟踪组织中的安全工作和项目。 

*增强*的安全评分（目前为预览版）是以**攻击为中心的受攻击面**，有三个好处：

- 安全控制-安全建议现在分组为逻辑集，可以更好地反映容易受到攻击的攻击面。 有关详细信息，请参阅下面[的如何计算安全分数](secure-score-security-controls.md#how-the-secure-score-is-calculated)。

- 整体分数更好地反映了在建议级别奖励的总体观点。 利用这种增强功能，您的分数仅在修正控件内单个资源的*所有*建议时才会提高。 这意味着，分数仅在资源的安全性提高时才会提高。 

- 单个攻击面的安全状态更直观-通过显示 "每个安全控制的分数"，"安全分数" 页将成为你可以详细了解组织如何保证每个攻击面的安全的位置。

增强安全分数显示为百分比，如以下屏幕截图所示：

[![增强安全分数（预览版）现在包括百分比](media/secure-score-security-controls/secure-score-with-percentage.png)](media/secure-score-security-controls/secure-score-with-percentage.png#lightbox)


## <a name="locating-your-secure-score"></a>查找安全分数

安全中心显示你的分数：概述页中显示的第一件事。 如果单击到专用安全分数页，将看到按订阅细分的分数。 单击单个订阅可查看优先推荐建议的详细列表，以及修正这些建议将对订阅分数产生的潜在影响。

## <a name="how-the-secure-score-is-calculated"></a>如何计算安全分数 

在此预览版之前，安全中心会逐个考虑每个建议，并根据其严重性向其分配一个值。 用于提高安全状况的安全团队必须根据发现的完整列表，确定安全中心建议响应的优先级。 每次修正单个资源的建议后，就会提高安全分数。

作为安全分数的增强的一部分，现已将建议分组到**安全控件**中。 控件是一组安全建议，以及有助于实现这些建议的说明。 控件是相关建议的逻辑分组。 不再为建议级别授予点。 相反，您的分数仅在修正控件内单个资源的*所有*建议时才会提高。

建议页面上清楚地显示了每个安全控件对整体安全分数的贡献。

[![增强安全评分（预览版）会引入安全控制](media/secure-score-security-controls/security-controls.png)](media/secure-score-security-controls/security-controls.png#lightbox)

若要获取安全控制的所有可能的点，所有资源都必须符合安全控制中的所有安全建议。 例如，安全中心提供有关如何保护管理端口的多个建议。 过去，您可以修正其中一些相关和相互依赖的建议，同时让其他人未解决，并提高安全分数。 在客观上，您可以很容易地认为您的安全性尚未得到改善。 现在，你必须对它们进行修正，以区分安全分数。

例如，名为 "应用系统更新" 的安全控件的最大分数为六个点，你可以在工具提示中查看该控件的潜在增加值：

[![安全控件 "应用系统更新"](media/secure-score-security-controls/apply-system-updates-control.png)](media/secure-score-security-controls/apply-system-updates-control.png#lightbox)

上面屏幕截图中的安全控件 "应用系统更新" 的值显示了 "2% （1点）"。 这意味着，如果修正此控制中的所有建议，分数将增加2% （在本例中为1）。 为简单起见，建议列表的 "潜在增加" 列中的值舍入到整数。 工具提示将显示精确值：

* **潜在增加**-控件内可供您使用的剩余点。 若要将这些点数添加到安全分数，请修正控件的所有建议。 在上面的示例中，为控件显示的一个点实际上为0.96 点。
* **当前分数**-此控件的当前分数。 每个控件都提供总体分数。 在此示例中，控件的分数为5.04。 
* **最大分数**-前两个值的和。

### <a name="calculations---understanding-your-score"></a>计算-了解成绩

|度量值|公式和示例|
|-|-|
|**安全控件的当前评分**|<br>用于计算安全控件当前分数的 ![公式](media/secure-score-security-controls/security-control-scoring-equation.png)<br><br>每个单独的安全控件都提供安全分数。 受控件中的建议影响的每个资源都有助于控件的当前分数。 每个控件的当前分数是控件*中*资源状态的度量值。<br>![工具提示，显示计算安全控件的当前分数时使用的值](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>在此示例中，最大分数6将除以78，因为这是正常和不正常资源的总和。<br>6/78 = 0.0769<br>按照正常资源（74）的数量乘以当前分数：<br>0.0769 * 74 = **5.69**<br><br>|
|**安全功能分数**<br>一个订阅|<br>![用于计算当前安全分数的公式](media/secure-score-security-controls/secure-score-equation.png)<br><br>![已启用所有控制的单一订阅安全分数](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>在此示例中，有一个订阅，其中包含所有可用的安全控件（60点的可能最大分数）。 评分显示了可能的60的28个点，其余32点反映在安全控制的 "潜在分数增加" 图形中。<br>![控件列表和潜在评分增加](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**安全功能分数**<br>多个订阅|<br>添加所有订阅中所有资源的当前分数，然后计算与单个订阅相同<br><br>查看多个订阅时，安全分数会评估所有启用的策略中的所有资源，并对每个安全控件的最大分数的组合影响进行分组。<br>为启用了所有控件的多个订阅 ![安全分数](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>组合分数**不**是平均值;相反，它是所有订阅中所有资源的状态的计算状态。<br>这种情况下，如果你跳到 "建议" 页面并增加可用的可用点数，你会发现这是当前评分（24）和可用分数（60）之间的差异。|
||||

## <a name="improving-your-secure-score"></a>提高安全分数

若要提高安全分数，请从建议列表中修正安全建议。 可以为每个资源手动更正每个建议，也可以使用**快速修复！** 选项（如果有），可以快速将建议应用于一组资源。 有关详细信息，请参阅[修正建议](security-center-remediate-recommendations.md)。

仅内置建议会影响安全分数。

## <a name="security-controls-and-their-recommendations"></a>安全控制及其建议

下表列出了 Azure 安全中心的安全控制。 对于每个控件，如果修正了控件中列出的*所有* *资源的建议*，则可以查看可以添加到安全分数的最大点数。 

> [!TIP]
> 如果要以不同的方式对此列表进行筛选或排序，请将其复制并粘贴到 Excel 中。

|安全控制|最大安全分数分数|建议|
|----------------|:-------------------:|---------------|
|**启用 MFA**|10|-应在对订阅拥有所有者权限的帐户上启用 MFA<br>-应在对订阅拥有读取权限的帐户上启用 MFA<br>-应为启用了你的订阅的写入权限的帐户启用 MFA|
|**安全管理端口**|8|-应在虚拟机上应用实时网络访问控制<br>-虚拟机应与网络安全组相关联<br>-应在虚拟机上关闭管理端口|
|**应用系统更新**|6|-应在计算机上解决监视代理运行状况问题<br>-监视代理应安装在虚拟机规模集上<br>-应在计算机上安装监视代理<br>-应为云服务角色更新 OS 版本<br>-应安装虚拟机规模集的系统更新<br>-应在计算机上安装系统更新<br>-应重启计算机以应用系统更新<br>-Kubernetes 服务应升级到不容易受到攻击的 Kubernetes 版本<br>-监视代理应安装在虚拟机上|
|**修正漏洞**|6|-应在 SQL server 上启用高级数据安全性<br>-应修正 Azure 容器注册表映像中的漏洞（预览版）<br>-应修正 SQL 数据库上的漏洞<br>-漏洞评估解决方案应修正漏洞<br>-应对 SQL 托管实例启用漏洞评估<br>-应在 SQL server 上启用漏洞评估<br>-应在虚拟机上安装漏洞评估解决方案|
|**启用静态加密**|4|-应在虚拟机上应用磁盘加密<br>-应启用 SQL 数据库上的透明数据加密<br>-应加密 Automation 帐户变量<br>-Service Fabric 群集应将 ClusterProtectionLevel 属性设置为 EncryptAndSign<br>-应将 SQL server TDE 保护程序加密为自己的密钥|
|**加密传输中的数据**|4|-API 应用只能通过 HTTPS 访问<br>-Function App 只能通过 HTTPS 访问<br>-仅应启用到 Redis 缓存的安全连接<br>-应启用到存储帐户的安全传输<br>-只能通过 HTTPS 访问 Web 应用程序|
|**管理访问权限和权限**|4|-应为订阅指定最多3个所有者<br>-应从订阅中删除不推荐使用的帐户（预览）<br>-应从订阅中删除不推荐使用的帐户（预览）<br>-应从订阅中删除具有所有者权限的外部帐户（预览）<br>-应从订阅中删除具有读取权限的外部帐户<br>-应从订阅中删除具有写入权限的外部帐户（预览）<br>-应该有多个所有者分配给你的订阅<br>-基于角色的访问控制（RBAC）应在 Kubernetes Services （预览版）上使用<br>-Service Fabric 群集只应使用 Azure Active Directory 进行客户端身份验证|
|**修正安全配置**|4|-Pod 安全策略应在 Kubernetes Services （预览版）上定义<br>-应修正容器安全配置中的漏洞<br>-应修正计算机上安全配置中的漏洞<br>-应修正虚拟机规模集上的安全配置漏洞<br>-监视代理应安装在虚拟机上<br>-应在计算机上安装监视代理<br>-监视代理应安装在虚拟机规模集上<br>-应在计算机上解决监视代理运行状况问题|
|**限制未经授权的网络访问**|4|-应禁用虚拟机上的 IP 转发<br>-应在 Kubernetes Services （预览版）上定义授权的 IP 范围<br>-应限制对应用服务的访问（预览）<br>-应强制执行 IaaS Nsg 上的 web 应用程序的规则<br>-虚拟机应与网络安全组相关联<br>-CORS 不应允许每个资源访问 API 应用<br>-CORS 不应允许每个资源访问你的 Function App<br>-CORS 不应允许每个资源访问你的 Web 应用程序<br>-应对 API 应用关闭远程调试<br>-应对 Function App 关闭远程调试<br>-应关闭 Web 应用程序的远程调试<br>-应为具有面向 Internet 的 Vm 的许可网络安全组限制访问<br>-应加强面向 Internet 的虚拟机的网络安全组规则|
|**应用自适应应用程序控制**|3|-应在虚拟机上启用自适应应用程序控件<br>-监视代理应安装在虚拟机上<br>-应在计算机上安装监视代理<br>-应在计算机上解决监视代理运行状况问题|
|**应用数据分类**|2|-应对 SQL 数据库中的敏感数据进行分类（预览）|
|**保护应用程序免受 DDoS 攻击**|2|-应启用 DDoS 保护标准|
|**启用 endpoint protection**|2|-应在虚拟机规模集上修正 Endpoint protection 运行状况故障<br>-应在你的计算机上解决 Endpoint protection 运行状况问题<br>-Endpoint protection 解决方案应安装在虚拟机规模集上<br>-在虚拟机上安装 endpoint protection 解决方案<br>-应在计算机上解决监视代理运行状况问题<br>-监视代理应安装在虚拟机规模集上<br>-应在计算机上安装监视代理<br>-监视代理应安装在虚拟机上<br>-在计算机上安装 endpoint protection 解决方案|
|**启用审核和日志记录**|第|-应启用 SQL server 审核<br>-应在应用服务中启用诊断日志<br>-应启用 Azure Data Lake Store 中的诊断日志<br>-应启用 Azure 流分析中的诊断日志<br>-应启用 Batch 帐户中的诊断日志<br>-应启用 Data Lake Analytics 中的诊断日志<br>-应启用事件中心的诊断日志<br>-应启用 IoT 中心的诊断日志<br>-应启用 Key Vault 中的诊断日志<br>-应在逻辑应用中启用诊断日志<br>-应启用搜索服务中的诊断日志<br>-应启用 Service Bus 中的诊断日志<br>-应启用虚拟机规模集中的诊断日志<br>-应在批处理帐户上配置指标警报规则<br>-SQL 审核设置应将操作组配置为捕获关键活动<br>-应将 SQL server 配置为超过90天的审核保留天数。|
|**实现安全最佳做法**|0|-应限制对具有防火墙和虚拟网络配置的存储帐户的访问权限<br>-应从事件中心命名空间中删除除 RootManageSharedAccessKey 之外的所有授权规则<br>-应为 SQL server 设置 Azure Active Directory 管理员<br>-应定义事件中心实例的授权规则<br>-应该将存储帐户迁移到新的 Azure 资源管理器资源<br>-虚拟机应迁移到新的 Azure 资源管理器资源<br>-SQL server 的高级数据安全设置应包含用于接收安全警报的电子邮件地址<br>-应在托管实例上启用高级数据安全性<br>-所有高级威胁防护类型都应在 SQL 托管实例高级数据安全设置中启用<br>-应在 SQL server 高级数据安全设置中启用针对管理员和订阅所有者的电子邮件通知<br>-高级威胁防护类型应在 SQL server 高级数据安全设置中设置为 "All"<br>-子网应与网络安全组相关联<br>-所有高级威胁防护类型都应在 SQL server 高级数据安全设置中启用|
||||

## <a name="secure-score-faq"></a>安全评分常见问题

### <a name="why-has-my-secure-score-gone-down"></a>为什么安全分数会下降？
随着此增强安全分数中引入的更改，你必须解决资源的所有建议才能接收点。 评分还变化为0-10 的小数位数。

### <a name="if-i-address-only-three-out-of-four-recommendations-in-a-security-control-will-my-secure-score-change"></a>如果我仅在安全控制中处理四个建议中的三个，则安全分数是否会变化？
不直到您修正单个资源的所有建议时，它才会更改。 若要获取控件的最大分数，必须为所有资源修正所有建议。

### <a name="will-this-enhanced-secure-score-replace-the-existing-secure-score"></a>这一增强的安全分数是否会替代现有的安全分数？ 
是的，但在一段时间内，它们会并行运行以简化转换。

### <a name="if-a-recommendation-isnt-applicable-to-me-and-i-disable-it-in-the-policy-will-my-security-control-be-fulfilled-and-my-secure-score-updated"></a>如果建议不适用于我，而在策略中禁用它，我的安全控制是否已完成，安全分数是否更新？
可以。 建议在不适用环境中时禁用建议。 有关如何禁用特定建议的说明，请参阅[禁用安全策略](https://docs.microsoft.com/azure/security-center/tutorial-security-policy#disable-security-policies)。

### <a name="if-a-security-control-offers-me-zero-points-towards-my-secure-score-should-i-ignore-it"></a>如果安全控制向我的安全评分提供了零点，应忽略它吗？
在某些情况下，你会看到一个大于零的控件最大分数，但影响为零。 如果用于修复资源的增量分数可忽略不计，则舍入为零。 请勿忽略这些建议，因为它们仍会带来安全改进。 唯一的例外是 "附加最佳做法" 控件。 修正这些建议不会提高你的得分，但会提高你的整体安全性。

## <a name="next-steps"></a>后续步骤

本文介绍了增强的安全分数以及它引入的新安全控件。 有关相关材料，请参阅以下文章：

- [了解建议的不同元素](security-center-recommendations.md)
- [了解如何修正建议](security-center-remediate-recommendations.md)