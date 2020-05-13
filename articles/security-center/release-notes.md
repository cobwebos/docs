---
title: Azure 安全中心的发行说明
description: Azure 安全中心新增功能和更改内容的说明。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/11/2020
ms.author: memildin
ms.openlocfilehash: bfe1e5d6a0c4171a262b36387f02be356fb1d72d
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83210891"
---
# <a name="whats-new-in-azure-security-center"></a>Azure 安全中心有哪些新功能？

Azure 安全处于积极开发阶段，并不断地获得改进。 为了及时了解最新的开发工作，此页面提供了以下内容的相关信息：

- 新增功能
- Bug 修复
- 已弃用的功能

此页面会定期更新，因此请经常对其进行访问。 如果你正在寻找超过6个月的项目，你会在存档中找到[Azure 安全中心的新增功能](release-notes-archive.md)。


## <a name="may-2020"></a>2020 年 5 月

### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>对实时（JIT）虚拟机（VM）访问的更改

安全中心包含一项可选功能，用于保护 Vm 的管理端口。 这可针对最常见的强力攻击形式提供防护。

此更新将对此功能进行以下更改：

- 建议你在 VM 上启用 JIT 的建议已重命名。 以前，"实时网络访问控制应应用在虚拟机上" 现在： "虚拟机的管理端口应使用实时网络访问控制进行保护"。

- 建议设置为仅在有打开的管理端口时才触发。

[了解有关 JIT 访问功能的详细信息](security-center-just-in-time.md)。


### <a name="custom-recommendations-have-been-moved-to-a-separate-security-control"></a>自定义建议已转移到单独的安全控制

增强安全分数中引入的一个安全控制是 "实现安全最佳做法"。 为订阅创建的任何自定义建议会自动放置在该控件中。 

为了更轻松地查找自定义建议，我们已将它们移到专用安全控制 "自定义建议" 中。 此控制不会对安全分数产生任何影响。

在[Azure 安全中心的增强安全评分（预览版）](secure-score-security-controls.md)中详细了解安全控制措施。


### <a name="toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list"></a>切换添加到控件中或作为简单列表的视图建议

安全控制是相关安全建议的逻辑组。 它们反映了易受攻击的攻击面。 控件是一组安全建议，其中包含有助于实现这些建议的说明。

若要立即了解你的组织如何保证每个单独的攻击面，请查看每个安全控制的分数。

默认情况下，安全控件中显示了你的建议。 在此更新中，还可以将其显示为列表。 若要将其查看为按受影响资源的运行状况状态排序的简单列表，请使用新的 "切换控件组"。 切换超出了门户中的列表。

安全控制-和此切换是新的安全评分体验的一部分。 请记得将你的反馈发送到门户中。

在[Azure 安全中心的增强安全评分（预览版）](secure-score-security-controls.md)中详细了解安全控制措施。


### <a name="account-security-recommendations-moved-to-security-best-practices-security-control"></a>帐户安全建议已转到 "安全最佳实践" 安全控制

增强安全分数中引入的一项安全控制是 "安全最佳实践"。 如果建议位于此控件中，则不会影响安全分数。 

通过此更新，三个建议已移出它们最初所在的控件，以及此最佳实践控件。 我们已执行此步骤，因为我们已确定这三个建议的风险低于最初的考虑因素。

建议如下：

- 应在对订阅拥有读取权限的帐户上启用 MFA （最初在 "启用 MFA" 控制中）
- 应从订阅中删除具有读取权限的外部帐户（最初在 "管理访问权限和权限" 控件中）
- 应为订阅指定最多3个所有者（最初在 "管理访问权限和权限" 控件中）

在[Azure 安全中心的增强安全评分（预览版）](secure-score-security-controls.md)中详细了解安全控制措施。


### <a name="custom-policies-with-custom-metadata-generally-available"></a>自定义元数据已正式发布的自定义策略

自定义策略现在是安全中心建议体验、安全评分和规章遵从标准仪表板的一部分。 此功能现已公开发布，可让你在安全中心扩展你的组织的安全评估覆盖范围。 

在 Azure 策略中创建自定义计划，向其添加策略并将其加入到 Azure 安全中心，并将其作为建议进行可视化。

现在，我们还添加了编辑自定义建议元数据的选项。 元数据选项包括严重性、修正步骤、威胁信息等。  

详细[了解如何增强你的自定义建议，并提供详细信息](custom-security-policies.md#enhancing-your-custom-recommendations-with-detailed-information)。


## <a name="april-2020"></a>2020 年 4 月

### <a name="dynamic-compliance-packages-now-generally-available"></a>动态符合性包现已正式发布

Azure 安全中心规章相容性仪表板现在包含**动态符合性包**（现已正式发布），以跟踪其他行业和法规标准。

可以通过 "安全中心安全策略" 页将动态符合性包添加到订阅或管理组。 如果已载入标准或基准，则标准将显示在你的符合性仪表板中，并将所有关联的符合性数据映射为评估。 可供下载的任何标准的汇总报告。

现在，你可以添加如下标准：

- **NIST SP 800-53 R4**
- **SWIFT CSP CSCF-v2020**
- **英国官方和英国 NHS**
- **加拿大联邦 PBMM**
- **AZURE cis 1.1.0 （新）** （azure cis 1.1.0 的更完整表示形式）

此外，我们最近还添加了**Azure 安全基准**，其中包括基于公共符合性框架的安全性和符合性最佳做法的 Microsoft 创作的特定于 azure 的准则。 在仪表板可用时，将支持该仪表板中的其他标准。  
 
[详细了解如何自定义合规性仪表板中的标准集](update-regulatory-compliance-packages.md)。


### <a name="identity-recommendations-now-included-in-azure-security-center-free-tier"></a>目前已包含在 Azure 安全中心免费层中的身份建议

Azure 安全中心免费层上的标识和访问安全建议现已正式发布。 这是使云安全状况管理（CSPM）功能可用的一部分工作。 到目前为止，这些建议仅适用于标准定价层。

标识和访问建议的示例包括：

- "应在对订阅拥有所有者权限的帐户上启用多重身份验证。"
- "应为订阅指定最多三个所有者。"
- "应从订阅中删除弃用的帐户。"

如果你的订阅是免费定价层，则其安全分数将受到此更改的影响，因为它们永远不会评估其身份和访问安全性。

[详细了解标识和访问建议](recommendations-reference.md#recs-identity)。
[详细了解如何监视标识和访问](security-center-identity-access.md)。


## <a name="march-2020"></a>2020 年 3 月

### <a name="workflow-automation-is-now-generally-available"></a>工作流自动化现已正式发布

Azure 安全中心的工作流自动化功能现已正式发布。 使用它可以根据安全警报和建议自动触发逻辑应用。 此外，还可以使用手动触发器来获取警报，并提供可使用 "快速修复" 选项的所有建议。

每个安全计划都包含多个事件响应工作流。 这些过程可能包括通知相关利益干系人、启动更改管理流程，以及应用特定的补救步骤。 安全专家建议您尽可能多地自动执行这些过程的步骤。 自动化可确保按照预定义的要求快速、一致地执行过程步骤，从而减少开销并提高安全性。

有关运行工作流的自动和手动安全中心功能的详细信息，请参阅[工作流自动化](workflow-automation.md)。

若要了解如何创建逻辑应用，请参阅[Azure 逻辑应用](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)。


### <a name="integration-of-azure-security-center-with-windows-admin-center"></a>Azure 安全中心与 Windows 管理中心的集成

现在可以将本地 Windows 服务器从 Windows 管理中心直接移到 Azure 安全中心。 然后，安全中心成为你的单个窗格，可查看所有 Windows 管理中心资源（包括本地服务器、虚拟机和其他 PaaS 工作负荷）的安全信息。

将服务器从 Windows 管理中心移动到 Azure 安全中心后，你将能够：

- 在 Windows 管理中心的安全中心扩展中查看安全警报和建议。
- 查看安全状况，并在 Azure 门户（或通过 API）的安全中心中检索 Windows 管理中心托管服务器的其他详细信息。

详细了解如何将[Azure 安全中心与 Windows 管理中心集成](windows-admin-center-integration.md)。


### <a name="protection-for-azure-kubernetes-service"></a>Azure Kubernetes 服务的保护

Azure 安全中心正在扩展其容器安全功能以保护 Azure Kubernetes 服务（AKS）。

常见的开源平台 Kubernetes 已广泛采用，因为它现在是容器业务流程的行业标准。 尽管这是广泛实现的，但仍缺乏了解如何保护 Kubernetes 环境的相关知识。 防御容器化应用程序的攻击面需要专业知识，以确保安全地配置基础结构并持续监视潜在的威胁。

安全中心防御包括：

- **发现和可见性**-持续发现注册到安全中心的订阅中的托管 AKS 实例。
- **安全建议**-可操作的建议，可帮助你满足 AKS 的最佳安全做法。 这些建议包括在安全分数中，以确保它们被视为组织的安全状况。 你可能会看到的 "基于角色的访问控制应用于限制对 Kubernetes 服务群集的访问" AKS 相关建议的示例。
- **威胁防护**-通过连续分析 AKS 部署，安全中心会提醒你在主机和 AKS 群集级别检测到的威胁和恶意活动。

[了解有关 Azure Kubernetes 服务与安全中心的集成的详细信息](azure-kubernetes-service-integration.md)。
[详细了解安全中心中的容器安全功能](container-security.md)。


### <a name="improved-just-in-time-experience"></a>缩短了实时体验

Azure 安全中心的功能、操作和 UI （用于保护管理端口的实时工具）已得到增强，如下所示： 

- **论证字段**-请求通过 Azure 门户的实时页面访问虚拟机（VM）时，可以使用一个新的可选字段来输入请求的理由。 可以在活动日志中跟踪输入到此字段的信息。 
- **自动清除冗余实时（JIT）规则**-每当更新 JIT 策略时，清理工具会自动运行，以检查整个规则集的有效性。 该工具查找策略中的规则与 NSG 中的规则之间的不匹配。 如果清理工具发现不匹配，则会确定原因，并在安全操作时删除不再需要的内置规则。 清除从不删除已创建的规则。 

[了解有关 JIT 访问功能的详细信息](security-center-just-in-time.md)。


### <a name="two-security-recommendations-for-web-applications-deprecated"></a>不推荐使用的 web 应用程序的两个安全建议

不推荐使用与 web 应用程序相关的两个安全建议： 

- 应强化 IaaS Nsg 上的 web 应用程序的规则。
    （相关策略：应强制执行 IaaS 上的 web 应用程序的 Nsg 规则）

- 应限制对应用服务的访问。
    （相关策略：应限制对应用服务的访问 [预览]）

这些建议将不再出现在 "安全中心建议" 列表中。 相关策略将不再包含在名为 "安全中心默认值" 的计划中。

[详细了解安全建议](recommendations-reference.md)。

## <a name="february-2020"></a>2020 年 2 月

### <a name="fileless-attack-detection-for-linux-is-now-in-preview"></a>适用于 Linux 的 Fileless 攻击检测现为预览版

由于攻击者增加了使用 stealthier 方法来避免检测，Azure 安全中心除了 Windows 外，还扩展了 Linux 的 fileless 攻击检测。 Fileless 攻击利用软件漏洞，将恶意负载注入良性系统进程，并隐藏在内存中。 这些技术：

- 最小化或消除磁盘上的恶意软件跟踪
- 大大降低了基于磁盘的恶意软件扫描解决方案检测到的机率

为了应对这种威胁，Azure 安全中心在2018年10月发布了适用于 Windows 的 fileless 攻击检测，现已在 Linux 上扩展了 fileless 攻击检测。 


## <a name="january-2020"></a>2020 年 1 月

### <a name="enhanced-secure-score"></a>增强安全分数

Azure 安全中心的安全评分功能的增强版本现已在预览版中提供。 在此版本中，多个建议分组到安全控制中，以便更好地反映易受攻击的攻击面（例如，限制对管理端口的访问）。

在预览阶段熟悉安全分数更改，并确定可帮助进一步保护环境的其他修正。

有关详细信息，请参阅[Azure 安全中心的增强安全分数（预览）](secure-score-security-controls.md)。