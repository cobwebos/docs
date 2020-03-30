---
title: Azure 安全中心的安全分数
description: Azure 安全中心安全得分及其安全控制的描述
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
ms.date: 03/10/2020
ms.author: memildin
ms.openlocfilehash: d7eea9cd83e72b6ffeaae319a8e87c065015e6b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415830"
---
# <a name="enhanced-secure-score-preview-in-azure-security-center"></a>Azure 安全中心增强的安全分数（预览）

## <a name="introduction-to-secure-score"></a>安全分数简介

Azure 安全中心有两个主要目标：帮助您了解当前的安全状况，并帮助您高效有效地提高安全性。 安全中心的核心方面是安全得分，使您能够实现这些目标。

安全中心持续评估您的资源、订阅和组织安全问题。 然后，它将所有发现聚合到单个分数中，以便您可以一目了然地判断您当前的安全情况：分数越高，识别的风险级别越低。 使用分数跟踪组织中的安全工作和项目。 

安全中心的安全分数页包括：

- **分数**- 安全分数显示为百分比值，但基础值也很清楚：

    [![安全分数显示为百分比值，基础数字也清晰](media/secure-score-security-controls/secure-score-with-percentage.png)](media/secure-score-security-controls/secure-score-with-percentage.png#lightbox)

- **安全控制**- 每个控件都是一组相关的安全建议，并反映易受攻击的攻击面。 控件是一组安全建议，其中包含可帮助您实现这些建议的说明。 仅当修复控件中单个资源*的所有*建议时，您的分数才会提高。

    要立即查看您的组织保护每个攻击面的能力，请查看每个安全控制的分数。

    有关详细信息，请参阅下面[如何计算安全分数](secure-score-security-controls.md#how-the-secure-score-is-calculated)。 


>[!TIP]
> 早期版本的安全中心在推荐级别授予分数：当您对单个资源进行修正时，您的安全分数会提高。 如今，只有修复控件中单个资源*的所有*建议，您的分数才会提高。 因此，只有在提高资源的安全性时，您的分数才会提高。
> 虽然此增强版本仍处于预览阶段，但早期安全分数体验可从 Azure 门户作为选项提供。 


## <a name="locating-your-secure-score"></a>找到您的安全分数

安全中心显著显示您的分数：这是"概述"页中显示的第一件事。 如果您单击专用安全分数页，您将看到按订阅细分的分数。 单击单个订阅以查看优先建议的详细列表以及修复这些建议对订阅分数的潜在影响。

## <a name="how-the-secure-score-is-calculated"></a>如何计算安全分数 

建议页面上清楚地显示了每个安全控制对总体安全分数的贡献。

[![增强的安全分数引入了安全控制](media/secure-score-security-controls/security-controls.png)](media/secure-score-security-controls/security-controls.png#lightbox)

要获取安全控制的所有可能点，所有资源都必须符合安全控制中的所有安全建议。 例如，安全中心对如何保护管理端口提出了多项建议。 过去，您可以修复其中一些相关和相互依赖的建议，同时使其他建议未解决，您的安全分数将得到改善。 客观地看，很容易说，你的安全没有改善，直到你解决了所有问题。 现在，您必须修复所有，以改变您的安全分数。

例如，称为"应用系统更新"的安全控制的最大分数为 6 分，您可以在工具提示中看到控件的潜在增加值：

[![安全控制"应用系统更新"](media/secure-score-security-controls/apply-system-updates-control.png)](media/secure-score-security-controls/apply-system-updates-control.png#lightbox)

上图中安全控制"应用系统更新"的可能性显示"2%（1 点）"。 这意味着，如果修复此控件中的所有建议，您的分数将增加 2%（在这种情况下，一个点）。 为简单起见，建议列表中的"潜在增加"列中的值四舍五入为整数。 工具提示显示精确值：

* **最大分数**- 通过在控件内完成所有建议，您可以获得的最大分数数。 控件的最大分数指示该控件的相对显著性。 使用最大分数值来分诊要首先处理哪些问题。 
* **潜在增加**- 控件内可供您使用的剩余点。 要将这些分数添加到安全分数中，请修复控件的所有建议。 在上面的示例中，控件显示的一个点实际上是 0.96 点。
* **当前分数**- 此控件的当前分数。 每个控件都会对总分做出贡献。 在此示例中，控件对总计贡献 5.04 点。 

### <a name="calculations---understanding-your-score"></a>计算 - 了解您的分数

|指标|公式和示例|
|-|-|
|**安全控制当前得分**|<br>![计算安全控制当前分数的方程](media/secure-score-security-controls/security-control-scoring-equation.png)<br><br>每个单独的安全控制都有助于提高安全分数。 受控件内建议影响的每个资源都有助于控制当前的得分。 每个控件的当前分数是控件*内*资源状态的度量。<br>![显示计算安全控制当前分数时使用的值的工具提示](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>在此示例中，6 的最大值将除以 78，因为这是健康和不正常资源的总和。<br>6 / 78 = 0.0769<br>乘以健康资源数 （4） 可生成当前分数：<br>0.0769 × 4 × **0.31**<br><br>|
|**安全评分**<br>一个订阅|<br>![用于计算当前安全分数的方程](media/secure-score-security-controls/secure-score-equation.png)<br><br>![启用所有控件的单次订阅安全分数](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>在此示例中，有一个订阅，所有安全控制都可用（潜在最高分数为 60 分）。 得分显示28分的可能60和剩余的32分反映在"潜在分数增加"的数字的安全控制。<br>![控件列表和潜在分数增加](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**安全评分**<br>多个订阅|<br>添加所有订阅中所有资源的当前分数，然后计算与单个订阅相同<br><br>查看多个订阅时，安全分数会评估所有启用的策略中的所有资源，并对其进行组合影响，以影响每个安全控制的最大分数。<br>![启用所有控件后，为多个订阅的安全分数](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>合并分数不是平均值;因此，合并分数**不是**平均值。而是评估所有订阅中所有资源的状态。<br>在这里，如果您转到建议页面并添加可用的潜在分数，您会发现当前分数 （24） 和可用最大分数 （60） 之间的差异。|
||||

## <a name="improving-your-secure-score"></a>提高您的安全分数

要提高安全分数，请从建议列表中修复安全建议。 您可以为每个资源手动修复每个建议，或者使用**快速修复！** 选项（如果可用）可快速将建议的修正应用于一组资源。 有关详细信息，请参阅[修正建议](security-center-remediate-recommendations.md)。

>[!IMPORTANT]
> 只有内置建议才会对安全分数产生影响。

## <a name="security-controls-and-their-recommendations"></a>安全控制及其建议

下表列出了 Azure 安全中心中的安全控件。 对于每个控件，如果修复控件中列出的*所有*资源*的所有*建议，则可以查看可添加到安全分数的最大点数。 

> [!TIP]
> 如果要以不同方式筛选或排序此列表，请将其复制并粘贴到 Excel 中。

|安全控制|最大安全分数|建议|
|----------------|:-------------------:|---------------|
|**启用 MFA**|10|- 应在具有订阅所有者权限的帐户上启用 MFA<br>- 应在订阅上具有读取权限的帐户上启用 MFA<br>- 应启用具有订阅写入权限的 MFA 帐户|
|**安全管理端口**|8|- 应在虚拟机上应用实时网络访问控制<br>- 虚拟机应与网络安全组关联<br>- 应在虚拟机上关闭管理端口|
|**应用系统更新**|6|- 监控代理运行状况问题应在您的计算机上解决<br>- 监视代理应安装在虚拟机规模集中<br>- 监控代理应安装在您的计算机上<br>- 应针对云服务角色更新操作系统版本<br>- 应安装虚拟机规模集的系统更新<br>- 系统更新应安装在您的计算机上<br>- 应重新启动计算机以应用系统更新<br>- 库伯内斯服务应升级到非易受攻击的库伯内斯版本<br>- 监视代理应安装在虚拟机上|
|**修正漏洞**|6|- 应在 SQL 服务器上启用高级数据安全性<br>- 应修复 Azure 容器注册表映像中的漏洞<br>- 应修复 SQL 数据库上的漏洞<br>- 漏洞应通过漏洞评估解决方案进行补救<br>- 应在 SQL 托管实例上启用漏洞评估<br>- 应在 SQL 服务器上启用漏洞评估<br>- 漏洞评估解决方案应安装在虚拟机上|
|**启用静态加密**|4|- 磁盘加密应用于虚拟机<br>- 应启用 SQL 数据库上的透明数据加密<br>- 应加密自动化帐户变量<br>- 服务结构群集应将群集保护级别属性设置为加密和签名<br>- SQL 服务器 TDE 保护器应使用您自己的密钥进行加密|
|**加密传输中的数据**|4|- API 应用程序应仅通过 HTTPS 访问<br>- 功能应用程序应仅通过 HTTPS 访问<br>- 应仅启用与 Redis 缓存的安全连接<br>- 应启用安全转移到存储帐户<br>- Web 应用程序只能通过 HTTPS 访问|
|**管理访问权限和权限**|4|- 应为您的订阅指定最多 3 个所有者<br>- 应从订阅中删除已弃用的帐户（预览版）<br>- 应从订阅中删除具有所有者权限的已弃用帐户（预览版）<br>- 应从订阅中删除具有所有者权限的外部帐户（预览版）<br>- 应从订阅中删除具有读取权限的外部帐户<br>- 应从订阅中删除具有写入权限的外部帐户（预览版）<br>- 应有多个所有者分配给您的订阅<br>- 基于角色的访问控制 （RBAC） 应在库伯奈斯服务（预览版）上使用<br>- 服务结构群集应仅使用 Azure 活动目录进行客户端身份验证|
|**修正安全配置**|4|- 应在库伯内斯服务上定义 Pod 安全策略<br>- 应修复容器安全配置中的漏洞<br>- 应修复计算机安全配置中的漏洞<br>- 应修复虚拟机规模集中的安全配置漏洞<br>- 监视代理应安装在虚拟机上<br>- 监控代理应安装在您的计算机上<br>- 监视代理应安装在虚拟机规模集中<br>- 监控代理运行状况问题应在您的计算机上解决|
|**限制未经授权的网络访问**|4|- 应禁用虚拟机上的 IP 转发<br>- 授权 IP 范围应在库伯奈斯服务（预览）上定义<br>- （取消）应用服务访问应受到限制（预览）<br>- （解密） IaaS NSG 上的 Web 应用程序规则应强化<br>- 虚拟机应与网络安全组关联<br>- CORS 不应允许每个资源访问您的 API 应用<br>- CORS 不应允许每个资源访问您的功能应用<br>- CORS 不应允许每个资源访问您的 Web 应用程序<br>- 应关闭 API 应用的远程调试<br>- 应关闭函数应用的远程调试<br>- 应关闭 Web 应用程序的远程调试<br>- 对于具有面向 Internet 的 VM 的允许性网络安全组，应限制访问<br>- 应强化面向互联网的虚拟机的网络安全组规则|
|**应用自适应应用程序控制**|3|- 应在虚拟机上启用自适应应用程序控制<br>- 监视代理应安装在虚拟机上<br>- 监控代理应安装在您的计算机上<br>- 监控代理运行状况问题应在您的计算机上解决|
|**应用数据分类**|2|- 应对 SQL 数据库中的敏感数据进行分类（预览）|
|**保护应用程序免受 DDoS 攻击**|2|- 应启用 DDoS 保护标准|
|**启用端点保护**|2|- 应在虚拟机规模集中修复端点保护运行状况故障<br>- 应在计算机上解决端点保护运行状况问题<br>- 端点保护解决方案应安装在虚拟机规模集中<br>- 在虚拟机上安装端点保护解决方案<br>- 监控代理运行状况问题应在您的计算机上解决<br>- 监视代理应安装在虚拟机规模集中<br>- 监控代理应安装在您的计算机上<br>- 监视代理应安装在虚拟机上<br>- 在计算机上安装端点保护解决方案|
|**启用审核和日志记录**|1|- 应启用 SQL 服务器上的审核<br>- 应启用应用服务中的诊断日志<br>- 应启用 Azure 数据湖存储中的诊断日志<br>- 应启用 Azure 流分析中的诊断日志<br>- 应启用批处理帐户中的诊断日志<br>- 应启用数据湖分析中的诊断日志<br>- 应启用事件中心中的诊断日志<br>- 应启用 IoT 中心中的诊断日志<br>- 应启用密钥保管库中的诊断日志<br>- 应启用逻辑应用中的诊断日志<br>- 应启用搜索服务中的诊断日志<br>- 应启用服务总线中的诊断日志<br>- 应启用虚拟机规模集中的诊断日志<br>- 应在批处理帐户上配置指标警报规则<br>- SQL 审核设置应配置操作组以捕获关键活动<br>- SQL 服务器应配置审核保留天数大于 90 天。|
|**实施安全最佳实践**|0|- 应限制使用防火墙和虚拟网络配置访问存储帐户<br>- 应从事件中心命名空间中删除除根管理共享访问密钥之外的所有授权规则<br>- 应为 SQL 服务器预配 Azure 活动目录管理员<br>- 应定义事件中心实例上的授权规则<br>- 存储帐户应迁移到新的 Azure 资源管理器资源<br>- 虚拟机应迁移到新的 Azure 资源管理器资源<br>- SQL 服务器的高级数据安全设置应包含电子邮件地址以接收安全警报<br>- 应在托管实例上启用高级数据安全性<br>- 应在 SQL 托管实例高级数据安全设置中启用所有高级威胁保护类型<br>- 应在 SQL 服务器高级数据安全设置中启用发送给管理员和订阅所有者的电子邮件通知<br>- 高级威胁保护类型应在 SQL 服务器高级数据安全设置中设置为"全部"<br>- 子网应与网络安全组关联<br>- 应在 SQL 服务器高级数据安全设置中启用所有高级威胁防护类型|
||||

## <a name="secure-score-faq"></a>安全分数常见问题解答

### <a name="why-has-my-secure-score-gone-down"></a>为什么我的安全分数下降了？
安全中心已切换到增强的安全分数（当前处于预览状态），其中包括分数计算方式的更改。 现在，您必须解决资源接收点的所有建议。 分数也更改为 0-10。

### <a name="if-i-address-only-three-out-of-four-recommendations-in-a-security-control-will-my-secure-score-change"></a>如果我只处理安全控制中四分之三的建议，我的安全分数是否会更改？
不是。 在修复单个资源的所有建议之前，它不会更改。 要获取控件的最大分数，必须修复所有资源的所有建议。

### <a name="is-the-previous-experience-of-the-secure-score-still-available"></a>安全分数的先前体验是否仍然可用？ 
是的。 有一段时间，他们将并排运行，以缓解过渡。 预期以前的模型会及时淘汰。 

### <a name="if-a-recommendation-isnt-applicable-to-me-and-i-disable-it-in-the-policy-will-my-security-control-be-fulfilled-and-my-secure-score-updated"></a>如果建议不适用于我，并且我在策略中禁用了它，我的安全控制是否会得到满足并更新我的安全分数？
是的。 当建议不适用于您的环境时，我们建议禁用这些建议。 有关如何禁用特定建议的说明，请参阅[禁用安全策略](https://docs.microsoft.com/azure/security-center/tutorial-security-policy#disable-security-policies)。

### <a name="if-a-security-control-offers-me-zero-points-towards-my-secure-score-should-i-ignore-it"></a>如果安全控制为我的安全分数提供零点，我应该忽略它吗？
在某些情况下，您将看到控件最大值大于零，但影响为零。 当修复资源的增量分数可以忽略不计时，则四舍五入为零。 不要忽视这些建议，因为它们仍然会带来安全改进。 唯一的例外是"其他最佳实践"控件。 修复这些建议不会提高您的分数，但它会提高您的整体安全性。

## <a name="next-steps"></a>后续步骤

本文介绍了安全分数及其引入的安全控制。 有关相关材料，请参阅以下文章：

- [了解建议的不同元素](security-center-recommendations.md)
- [了解如何修复建议](security-center-remediate-recommendations.md)