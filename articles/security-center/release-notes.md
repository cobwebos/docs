---
title: Azure 安全中心的发行说明
description: 介绍 Azure 安全中心的新增功能和已更改的功能。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: 3cb35cdf217d497b612dee12aedb869b0583e464
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90986699"
---
# <a name="whats-new-in-azure-security-center"></a>Azure 安全中心的新增功能

Azure 安全中心正在积极开发中，并不断得到改进。 为及时了解最新开发成果，可在本页面查看下列相关信息：

- 新增功能
- Bug 修复
- 已弃用的功能

本页面会定期更新，请经常回来查看。 如果要查找 6 个月之前的项目，可查看 [Azure 安全中心的新增功能存档](release-notes-archive.md)。


## <a name="september-2020"></a>2020 年 9 月

9月的更新包括：
- [安全中心获得新的外观！](#security-center-gets-a-new-look)
- [Azure Defender 已发布](#azure-defender-released)
- [Azure Defender for Key Vault 现已正式发布](#azure-defender-for-key-vault-is-generally-available)
- [用于文件和 ADLS Gen2 的存储保护的 Azure Defender 已公开发布](#azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available)
- [资产清单工具现已正式发布](#asset-inventory-tools-are-now-generally-available)
- [禁用查找容器注册表和虚拟机扫描的特定漏洞](#disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines)
- [从建议中免除资源](#exempt-a-resource-from-a-recommendation)
- [安全中心的 AWS 和 GCP 连接器带来了多云体验](#aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience)
- [Kubernetes 工作负荷保护建议捆绑](#kubernetes-workload-protection-recommendation-bundle)
- [Azure Defender 中 iot 威胁防护的增强功能](#iot-threat-protection-enhancements-in-azure-defender-for-iot)
- [漏洞评估发现现在提供连续导出](#vulnerability-assessment-findings-are-now-available-in-continuous-export)
- [在创建新资源时强制建议来防止安全错误配置](#prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources)
- [网络安全组建议改进](#network-security-group-recommendations-improved)
- [弃用的预览版 AKS 建议 "Pod 安全策略应在 Kubernetes Services 上定义"](#deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services)
- [已改进 Azure 安全中心发送的电子邮件通知](#email-notifications-from-azure-security-center-improved)
- [安全分数不包括预览建议](#secure-score-doesnt-include-preview-recommendations)
- [建议现在包含严重性指示器和新鲜度时间间隔](#recommendations-now-include-a-severity-indicator-and-the-freshness-interval)


### <a name="security-center-gets-a-new-look"></a>安全中心获得新的外观！

我们已发布安全中心门户页面的刷新 UI。 新页包括新的 "概述" 页以及用于安全分数、资产清单和 Azure Defender 的仪表板。

重新设计的 "概述" 页现在包含一个磁贴，用于访问安全分数、资产清单和 Azure Defender 仪表板。 它还会有一个链接到合规性仪表板的磁贴。

了解有关 " [概述" 页](overview-page.md)的详细信息。


### <a name="azure-defender-released"></a>Azure Defender 已发布

**Azure Defender** 是 (CWPP) 集成在安全中心内的云工作负荷保护平台，用于对 Azure 和混合工作负荷进行高级、智能、保护。 它取代了安全中心的标准定价层选项。 

当你从 Azure 安全中心的 " **定价和设置** " 区域启用 azure Defender 时，将同时启用以下 Defender 计划，并为环境中的计算、数据和服务层提供综合防御：

- [适用于服务器的 Azure Defender](defender-for-servers-introduction.md)
- [适用于应用服务的 Azure Defender](defender-for-app-service-introduction.md)
- [适用于存储的 Azure Defender](defender-for-storage-introduction.md)
- [Azure Defender for SQL](defender-for-sql-introduction.md)
- [适用于 IoT 的 Azure Defender](defender-for-iot-introduction.md)
- [适用于 Key Vault 的 Azure Defender](defender-for-key-vault-introduction.md)
- [适用于 Kubernetes 的 Azure Defender](defender-for-kubernetes-introduction.md)
- [适用于容器注册表的 Azure Defender](defender-for-container-registries-introduction.md)

其中每个计划分别在安全中心文档中进行了介绍。

通过其专用仪表板，Azure Defender 为虚拟机、SQL 数据库、容器、web 应用程序和网络等提供安全警报和高级威胁防护。

[了解有关 Azure Defender 的详细信息](azure-defender.md)

### <a name="azure-defender-for-key-vault-is-generally-available"></a>Azure Defender for Key Vault 现已正式发布

Azure 密钥保管库是一种云服务，用于保护加密密钥和机密（例如证书、连接字符串和密码）。 

**Azure Defender for Key Vault** 为 Azure Key Vault 提供 azure 本机的高级威胁防护，同时提供额外的安全智能层。 通过扩展，Azure Defender for Key Vault 因此可保护依赖于 Key Vault 帐户的多个资源。

可选计划现已正式发布。 此功能作为 "高级威胁防护 Azure Key Vault" 提供。

此外，Azure 门户中的 Key Vault 页面现在包含一个专用 **安全** 页面，用于 **安全中心** 建议和警报。

在 [Azure Defender 中了解 Key Vault 的](defender-for-key-vault-introduction.md)详细信息。


### <a name="azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available"></a>用于文件和 ADLS Gen2 的存储保护的 Azure Defender 已公开发布 

**用于存储的 Azure Defender** 检测到你的 azure 存储帐户有潜在有害的活动。 无论数据存储为 blob 容器、文件共享还是数据 lake，都可以对其进行保护。

[Azure 文件](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)和[Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)的支持现已正式发布。

从 10 2020 月1日起，我们将开始充电以保护这些服务的资源。

在 [Azure Defender 中了解有关存储的](defender-for-storage-introduction.md)详细信息。


### <a name="asset-inventory-tools-are-now-generally-available"></a>资产清单工具现已正式发布

Azure 安全中心的 "资产清单" 页提供了一个页面，用于查看已连接到安全中心的资源的安全状况。

安全中心会定期分析 Azure 资源的安全状态，以识别潜在的安全漏洞。 然后，提供有关如何修正这些漏洞的建议。

当任何资源具有未完成的建议时，它们将显示在清单中。

有关详细信息，请参阅 [利用资产清单和管理工具浏览和管理资源](asset-inventory.md)。



### <a name="disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines"></a>禁用查找容器注册表和虚拟机扫描的特定漏洞

Azure Defender 包含用于扫描 Azure 容器注册表和虚拟机中的映像的漏洞扫描程序。

如果组织需要忽略查找而不是对其进行修正，则可以选择禁用它。 禁用的结果不会影响安全分数或产生不需要的噪音。

当查找与在禁用规则中定义的条件相匹配时，它不会显示在结果列表中。

此选项在的 "建议详细信息" 页中提供：

- **应修正 Azure 容器注册表映像中的漏洞**
- **应修正虚拟机中的漏洞**

详细了解如何 [禁用容器映像的特定发现](defender-for-container-registries-usage.md#disable-specific-findings-preview) 并 [禁用虚拟机的特定发现](remediate-vulnerability-findings-vm.md#disable-specific-findings-preview)。


### <a name="exempt-a-resource-from-a-recommendation"></a>从建议中免除资源

有时，资源会在特定建议 (中列为不正常，因此降低安全分数) ，即使您认为不应如此。 它可能已被安全中心未跟踪的进程修正。 或者，您的组织可能决定接受该特定资源的风险。 

在这种情况下，你可以创建例外规则，并确保将来不会在不正常的资源中列出资源。 这些规则可以包括如下所述的记录的理由。

详细了解如何 [从建议和安全分数中排除资源](exempt-resource.md)。


### <a name="aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience"></a>安全中心的 AWS 和 GCP 连接器带来了多云体验

云工作负荷通常跨多个云平台，云安全服务必须执行相同的操作。

Azure 安全中心现在保护 Azure 中的工作负荷，Amazon Web Services (AWS) ，并 Google Cloud Platform (GCP) 。

将 AWS 和 GCP 帐户加入安全中心，将 AWS 安全中心、GCP Security 命令和 Azure 安全中心集成在一起。 

有关详细信息，请参阅 [将 AWS 帐户连接到 Azure 安全中心](quickstart-onboard-aws.md) 和 [将 GCP 帐户连接到 azure 安全中心](quickstart-onboard-gcp.md)。


### <a name="kubernetes-workload-protection-recommendation-bundle"></a>Kubernetes 工作负荷保护建议捆绑

为了确保 Kubernetes 工作负荷在默认情况下是安全的，安全中心添加了 Kubernetes 级别强化建议，其中包括具有 Kubernetes 许可控制的强制选项。

在 AKS 群集上安装了适用于 Kubernetes 的 Azure 策略外接程序后，将针对 Kubernetes API 服务器的每个请求进行监视，然后再将其保存到群集。 然后，你可以将配置为强制实施最佳实践，并为未来的工作负荷进行强制。

例如，你可以规定不应创建特权容器，以后的任何请求都将被阻止。

有关详细信息，请参阅 [使用 Kubernetes 许可控制的工作负荷保护最佳实践](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)。


### <a name="iot-threat-protection-enhancements-in-azure-defender-for-iot"></a>Azure Defender 中 iot 威胁防护的增强功能

由于包含 CyberX 无代理技术，Azure Defender for IoT 提供了更多威胁防护功能。 这为) 环境（例如制造、构建管理系统 (BMS) 、生命科学、能源和水设施、石油 & 气体和后勤） (的操作技术中使用的非托管要重建 mqtt 设备提供了安全保护。

了解 [Azure Defender For IoT 简介](defender-for-iot-introduction.md)中的详细信息。


### <a name="vulnerability-assessment-findings-are-now-available-in-continuous-export"></a>漏洞评估发现现在提供连续导出

使用连续导出将警报和建议实时流式传输到 Azure 事件中心、Log Analytics 工作区或 Azure Monitor。 在该处，你可以将此数据与 Siem (（例如 Azure Sentinel、Power BI、Azure 数据资源管理器等）集成。

安全中心的集成漏洞评估工具将有关资源的结果作为 "父" 建议中的可操作建议返回，如 "应修正虚拟机中的漏洞"。 

当你选择建议并启用 " **包括安全发现** " 选项时，现在可以通过连续导出导出安全结果。

:::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="在连续导出配置中包括安全发现切换" :::

相关页面：

- [Azure 虚拟机安全中心的集成漏洞评估解决方案](deploy-vulnerability-assessment-vm.md)
- [Azure 容器注册表映像的安全中心集成漏洞评估解决方案](defender-for-container-registries-usage.md)
- [连续导出](continuous-export.md)

### <a name="prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources"></a>在创建新资源时强制建议来防止安全错误配置

安全错误配置是安全事件的主要原因。 安全中心现在能够帮助 *防止* 新资源的配置错误，这与特定建议相关。 

此功能可帮助保护工作负荷的安全并使安全分数稳定。

根据特定建议强制实施安全配置，以两种模式提供：

- 使用 Azure 策略的 **拒绝** 效果，可阻止创建不正常的资源

- 使用 **强制** 选项，你可以利用 Azure 策略的 **DeployIfNotExist** 效果，并在创建时自动修正不合规的资源
 
这适用于所选的安全建议，可在资源详细信息页的顶部找到。

有关详细信息 [，请参阅防止强制/拒绝建议](prevent-misconfigurations.md)的错误配置。

###  <a name="network-security-group-recommendations-improved"></a>网络安全组建议改进

以下与网络安全组相关的安全建议已进行了改进，以减少误报的一些实例。

- 应在与 VM 关联的 NSG 上限制所有网络端口
- 应关闭虚拟机上的管理端口
- 面向 Internet 的虚拟机应使用网络安全组进行保护
- 子网应与网络安全组关联


### <a name="deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services"></a>弃用的预览版 AKS 建议 "Pod 安全策略应在 Kubernetes Services 上定义"

建议使用 [Azure Kubernetes 服务](https://docs.microsoft.com/azure/aks/use-pod-security-policies) 文档中所述的 "预览版建议，应在 Kubernetes Services 上定义安全策略"。

Pod 安全策略 (预览版) 功能，已设置为弃用，在2020年10月15日之后将不再提供，以支持 AKS 的 Azure 策略。

弃用 Pod 安全策略（预览版）之后，必须在使用已弃用功能的任何现有群集上禁用该功能，以执行将来的群集升级并保留在 Azure 支持范围内。


### <a name="email-notifications-from-azure-security-center-improved"></a>已改进 Azure 安全中心发送的电子邮件通知

有关安全警报的电子邮件的以下方面得到了改进： 

- 添加了针对所有严重级别的警报发送电子邮件通知的功能
- 添加了在订阅上通知具有不同 RBAC 角色的用户的功能
- 默认情况下，我们会主动通知订阅所有者 (高严重性的警报，这会造成严重的破坏) 
- 我们已从电子邮件通知配置页中删除了 "电话号码" 字段

有关详细信息，请参阅 [设置安全警报的电子邮件通知](security-center-provide-security-contact-details.md)。


### <a name="secure-score-doesnt-include-preview-recommendations"></a>安全分数不包括预览建议 

安全中心会持续评估资源、订阅和组织的安全问题。 然后，它将所有调查结果汇总成一个分数，让你可以一目了然地了解当前的安全状况：分数越高，识别出的风险级别就越低。

发现新的威胁后，安全中心会提供新的安全建议。 为了避免意外更改你的安全分数，并提供一个宽限期，在此期间你可以在影响你的评分之前探索新建议，已标记为 **预览版** 的建议将不再包含在安全分数的计算中。 它们应尽可能进行修正，以便在预览期结束时，它们会给你评分。

此外， **预览** 建议不会呈现资源 "不正常"。

预览建议的示例如下：

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="带有预览标志的建议":::

[详细了解安全分数](secure-score-security-controls.md)。


### <a name="recommendations-now-include-a-severity-indicator-and-the-freshness-interval"></a>建议现在包含严重性指示器和新鲜度时间间隔

建议的详细信息页现在包含 "新鲜度间隔" 指标 (在相关) 时，以及建议的严重性清晰显示。

:::image type="content" source="./media/release-notes/recommendations-severity-freshness-indicators.png" alt-text="显示新鲜度和严重性的建议页":::



## <a name="august-2020"></a>2020 年 8 月

8月的更新包括：

- [资产清单-资产安全状况的强大新视图](#asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets)
- [添加了对多重身份验证 (Azure Active Directory 安全默认值的支持) ](#added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication)
- [添加了服务主体建议](#service-principals-recommendation-added)
- [Vm 上的漏洞评估-合并建议和策略](#vulnerability-assessment-on-vms---recommendations-and-policies-consolidated)
- [新的 AKS 安全策略已添加到 ASC_default 计划-仅供个人预览版客户使用](#new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only)


### <a name="asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets"></a>资产清单-资产安全状况的强大新视图

安全中心的资产清单 (当前为预览版) 提供一种方法来查看已连接到安全中心的资源的安全状况。

安全中心会定期分析 Azure 资源的安全状态，以识别潜在的安全漏洞。 然后，提供有关如何修正这些漏洞的建议。 当任何资源具有未完成的建议时，它们将显示在清单中。

您可以使用视图及其筛选器来浏览您的安全状况数据，并根据您的发现采取更多操作。

了解有关 [资产清单](asset-inventory.md)的详细信息。


### <a name="added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication"></a>添加了对多重身份验证 (Azure Active Directory 安全默认值的支持) 

安全中心已为 [安全默认设置](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults)（Microsoft 的免费标识安全保护）提供完全支持。

安全默认值提供预先配置的标识安全设置，以使你的组织免受与常见标识相关的攻击。 安全默认值已保护超过5000000个租户;50000租户还受安全中心保护。

安全中心在标识 Azure 订阅时，无论是否启用了安全默认设置，现在都提供安全建议。 到目前为止，安全中心建议使用条件性访问启用多重身份验证，这是 Azure Active Directory (AD) premium 许可证的一部分。 对于使用 Azure AD 免费版的客户，我们现在建议启用安全默认设置。 

我们的目标是鼓励更多客户通过 MFA 保护其云环境，并缓解最高的风险，这也是最有影响力的 [安全分数](https://docs.microsoft.com/azure/security-center/secure-score-security-controls)。

详细了解 [安全默认值](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults)。


### <a name="service-principals-recommendation-added"></a>添加了服务主体建议

添加了新的建议，建议安全中心客户使用管理证书管理其订阅，并切换到服务主体。

建议使用 **服务主体来保护订阅，而不是管理证书** ，建议使用服务主体或 Azure 资源管理器更安全地管理你的订阅。 

详细了解 [Azure Active Directory 中的应用程序和服务主体对象](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object)。


### <a name="vulnerability-assessment-on-vms---recommendations-and-policies-consolidated"></a>Vm 上的漏洞评估-合并建议和策略

安全中心会检查你的 Vm 是否正在运行漏洞评估解决方案。 如果未找到漏洞评估解决方案，安全中心将提供建议以简化部署。

如果发现漏洞，安全中心将提供一个建议，用于汇总调查结果，以便在必要时进行调查和修正。

为了确保所有用户都能获得一致的体验，无论他们使用哪种扫描仪类型，都可以将四个建议合并为以下两个：

|统一建议|更改说明|
|----|:----|
|**应在虚拟机上启用漏洞评估解决方案**|替换以下两个建议：<br> **•** 在虚拟机上启用内置漏洞评估解决方案 (由 "标准" 层附带的 "Qualys (支持的)  (") <br> **•** 漏洞评估解决方案应安装在虚拟机上 (现已弃用)  (标准和免费层) |
|**应修正虚拟机中的漏洞**|替换以下两个建议：<br>**•** 更正虚拟机上发现的 (由 Qualys) 支持的漏洞， (现已弃用) <br>**•** 漏洞评估解决方案应修正的漏洞 (现已弃用) |
|||

现在，你将使用相同的建议部署安全中心的漏洞评估扩展或私下许可的解决方案 ( "BYOL" ) 来自合作伙伴（例如 Qualys 或 Rapid7）。

此外，如果发现安全中心并向其报告了漏洞，则单个建议会提醒你发现发现，而不考虑发现它们的漏洞评估解决方案。

#### <a name="updating-dependencies"></a>正在更新依赖项

如果有脚本、查询或自动化引用前面的建议或策略密钥/名称，请使用以下表格更新引用：

##### <a name="before-august-2020"></a>2020年8月之前

|建议|范围|
|----|:----|
|**启用 (通过 Qualys) 支持的虚拟机上的内置漏洞评估解决方案 **<br>密钥：550e890b-e652-4d22-8274-60b3bdb24c63|内置|
|修正虚拟机上发现的漏洞（由 Qualys 提供支持）<br>密钥：1195afff-c881-495e-9bc5-1486211ae03f|内置|
|应在虚拟机上安装漏洞评估解决方案<br>密钥：01b1ed4c-b733-4fee-b145-f23236e70cf3|BYOL|
|**应通过漏洞评估解决方案修复漏洞**<br>密钥：71992a2a-d168-42e0-b10e-6b45fa2ecddb|BYOL|
||||


|策略|范围|
|----|:----|
|**应对虚拟机启用漏洞评估**<br>策略 ID：501541f7-f7e7-4cd6-868c-4190fdad3ac9|内置|
|**漏洞评估解决方案应修正的漏洞**<br>策略 ID：760a85ff-6162-42b3-8d70-698e268f648c|BYOL|
||||


##### <a name="from-august-2020"></a>自8月2020

|建议|范围|
|----|:----|
|**应在虚拟机上启用漏洞评估解决方案**<br>密钥： ffff0522-1e88-47fc-8382-2a80ba848f5d|内置 + BYOL|
|**应修正虚拟机中的漏洞**<br>密钥：1195afff-c881-495e-9bc5-1486211ae03f|内置 + BYOL|
||||

|策略|范围|
|----|:----|
|[**应在虚拟机上启用漏洞评估**](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f501541f7-f7e7-4cd6-868c-4190fdad3ac9)<br>策略 ID：501541f7-f7e7-4cd6-868c-4190fdad3ac9 |内置 + BYOL|
||||


### <a name="new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only"></a>新的 AKS 安全策略已添加到 ASC_default 计划-仅供个人预览版客户使用

为了确保 Kubernetes 工作负荷在默认情况下是安全的，安全中心添加 Kubernetes 级别策略和强化建议，包括 Kubernetes 许可控制的强制选项。

此项目的早期阶段包括个人预览版，并在默认情况下禁用新 () 策略添加到 ASC_default 计划。

你可以放心地忽略这些策略，而不会对环境造成任何影响。 如果你想要启用它们，请在中注册预览， https://aka.ms/SecurityPrP 并从以下选项中进行选择：

1. **单一预览** –只加入此个人预览版。 明确提及 "ASC 连续扫描" 作为要加入的预览。
1. **正在进行的程序** -将添加到此和将来的专用预览。 你将需要完成个人资料和隐私协议。


## <a name="july-2020"></a>2020 年 7 月

7月的更新包括：
- [虚拟机的漏洞评估现在适用于非 marketplace 映像](#vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images)
- [针对 Azure 存储的威胁防护已扩展为包括 Azure 文件和 Azure Data Lake Storage Gen2 (预览版) ](#threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview)
- [启用威胁防护功能的八个新建议](#eight-new-recommendations-to-enable-threat-protection-features)
- [容器安全改进-更快速的注册表扫描和刷新文档](#container-security-improvements---faster-registry-scanning-and-refreshed-documentation)
- [使用新建议更新的自适应应用程序控件，并支持路径规则中的通配符](#adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules)
- [已弃用六个 SQL 高级数据安全策略](#six-policies-for-sql-advanced-data-security-deprecated)




### <a name="vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images"></a>虚拟机的漏洞评估现在适用于非 marketplace 映像

部署漏洞评估解决方案时，安全中心以前在部署之前执行过验证检查。 检查是确认目标虚拟机的 marketplace SKU。 

从此更新中，已删除该检查，你现在可以将漏洞评估工具部署到 "自定义" Windows 和 Linux 计算机。 自定义映像是你从 marketplace 默认值修改的映像。

尽管你现在可以在多台计算机上部署 (由 Qualys 提供支持的集成漏洞评估扩展) ，但仅当你使用的操作系统[部署到标准层 vm](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-vulnerability-scanner-to-your-vms)时，才提供支持。

详细了解 [虚拟机的集成漏洞扫描器 (需要 Azure Defender) ](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)。

若要详细了解如何在 [部署合作伙伴漏洞扫描解决方案](deploy-vulnerability-assessment-vm.md)中使用 Qualys 或 Rapid7 中的你自己的私下许可漏洞评估解决方案。


### <a name="threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview"></a>针对 Azure 存储的威胁防护已扩展为包括 Azure 文件和 Azure Data Lake Storage Gen2 (预览版) 

Azure 存储的威胁防护会在 Azure 存储帐户上检测潜在的有害活动。 当安全中心检测到访问或利用存储帐户的尝试时，会显示警报。 

无论数据存储为 blob 容器、文件共享还是数据 lake，都可以对其进行保护。




### <a name="eight-new-recommendations-to-enable-threat-protection-features"></a>启用威胁防护功能的八个新建议

添加了八个新建议以提供一种简单的方法来为以下资源类型启用 Azure 安全中心的威胁防护功能：虚拟机、应用服务计划、Azure SQL 数据库服务器、计算机上的 SQL server、Azure 存储帐户、Azure Kubernetes 服务群集、Azure 容器注册表注册表和 Azure Key Vault 保管库。

新建议如下：

- **应在 Azure SQL 数据库服务器上启用高级数据安全**
- **应在计算机的 SQL 服务器上启用高级数据安全**
- **应在 Azure 应用服务计划上启用高级威胁防护**
- **应对 Azure 容器注册表的注册表启用高级威胁防护**
- **应对 Azure Key Vault 的保管库启用高级威胁防护**
- **应对 Azure Kubernetes 服务的群集启用高级威胁防护**
- **应对 Azure 存储帐户启用高级威胁防护**
- **应在虚拟机上启用高级威胁防护**

这些新建议属于 " **启用高级威胁防护** " 安全控制。

建议还包括快速修复功能。 

> [!IMPORTANT]
> 修正其中的任何建议将导致保护相关资源的费用。 如果当前订阅中有相关资源，则会立即开始使用这些费用。 如果以后再添加，则为。
> 
> 例如，如果订阅中没有任何 Azure Kubernetes Service 群集并且启用了威胁防护，则不会产生任何费用。 如果将来在同一订阅中添加群集，它将自动受到保护，并将在该时间开始收费。

有关详细信息，请 [参阅安全建议参考页](recommendations-reference.md)。

详细了解 [Azure 安全中心的威胁防护](https://docs.microsoft.com/azure/security-center/threat-protection)。




### <a name="container-security-improvements---faster-registry-scanning-and-refreshed-documentation"></a>容器安全改进-更快速的注册表扫描和刷新文档

作为容器安全域中的连续投资的一部分，我们非常乐意在安全中心对 Azure 容器注册表中存储的容器映像进行动态扫描，大大提高性能。 现在，扫描通常在大约两分钟内完成。 在某些情况下，可能需要最多15分钟。

为了改进有关 Azure 安全中心容器安全功能的清晰度和指南，我们还刷新了容器安全文档页。 

若要详细了解安全中心的容器安全性，请参阅以下文章：

- [安全中心容器安全功能概述](https://docs.microsoft.com/azure/security-center/container-security)
- [与 Azure 容器注册表集成的详细信息](https://docs.microsoft.com/azure/security-center/azure-container-registry-integration)
- [与 Azure Kubernetes 服务集成的详细信息](https://docs.microsoft.com/azure/security-center/azure-kubernetes-service-integration)
- [如何扫描注册表并强化 Docker 主机](https://docs.microsoft.com/azure/security-center/monitor-container-security)
- [来自 Azure Kubernetes 服务群集的威胁防护功能的安全警报](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)
- [来自 Azure Kubernetes 服务主机的威胁防护功能的安全警报](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-containerhost)
- [容器的安全性建议](https://docs.microsoft.com/azure/security-center/recommendations-reference#recs-containers)



### <a name="adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules"></a>使用新建议更新的自适应应用程序控件，并支持路径规则中的通配符

自适应应用程序控件功能已收到两个重要更新：

* 新的建议确定以前未允许的潜在合法行为。 **应更新自适应应用程序控制策略中**的新建议允许列表规则，提示你向现有策略添加新规则，以减少自适应应用程序控制违规警报中的误报数。

* 路径规则现在支持通配符。 在此更新中，可以使用通配符配置允许的路径规则。 支持以下两种方案：

    * 在路径末尾使用通配符以允许此文件夹和子文件夹中的所有可执行文件

    * 使用路径中间的通配符启用具有更改的文件夹名称的已知可执行文件名称 (例如，使用已知可执行文件、自动生成的文件夹名称等个人用户文件夹，) 。


[详细了解自适应应用程序控制](security-center-adaptive-application.md)。



### <a name="six-policies-for-sql-advanced-data-security-deprecated"></a>已弃用六个 SQL 高级数据安全策略

即将弃用六个与 SQL 计算机高级数据安全相关的策略：

- 高级威胁防护类型应在 SQL 托管实例高级数据安全设置中设置为 "All"
- 高级威胁防护类型应在 SQL server 高级数据安全设置中设置为 "All"
- SQL 托管实例的“高级数据安全性”设置应包含用于接收安全警报的电子邮件地址
- SQL 服务器的“高级数据安全性”设置应包含用于接收安全警报的电子邮件地址
- 应在 SQL 托管实例高级数据安全设置中启用“向管理员和订阅所有者发送电子邮件通知”
- 应在 SQL 服务器高级数据安全设置中为管理员和订阅所有者启用电子邮件通知

了解有关 [内置策略](security-center-policy-definitions.md)的详细信息。





## <a name="june-2020"></a>2020 年 6 月

6月的更新包括：
- [安全评分 API (预览) ](#secure-score-api-preview)
- [适用于 SQL 计算机 (Azure、其他云和本地)  (预览版的高级数据安全) ](#advanced-data-security-for-sql-machines-azure-other-clouds-and-on-prem-preview)
- [ (预览版将 Log Analytics 代理部署到 Azure Arc 计算机的两个新建议) ](#two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview)
- [用于大规模创建连续导出和工作流自动化配置的新策略](#new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale)
- [使用 Nsg 保护非面向 internet 的虚拟机的新建议](#new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines)
- [用于启用威胁防护和高级数据安全性的新策略](#new-policies-for-enabling-threat-protection-and-advanced-data-security)



### <a name="secure-score-api-preview"></a>安全评分 API (预览) 

你现在可以通过 [安全分数 API](https://docs.microsoft.com/rest/api/securitycenter/securescores/) (当前在预览版) 中来访问你的评分。 利用 API 方法可以灵活地查询数据，并在一段时间内构建你自己的安全评分报表机制。 例如，可以使用 **安全评分** API 来获取特定订阅的分数。 此外，你可以使用 **安全分数控制** API 列出你的订阅的安全控制和当前分数。

有关安全分数 API 可以实现外部工具的示例，请参阅 [GitHub 社区的 secure 评分区](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)。

详细了解 [Azure 安全中心中的安全分数和安全控制](secure-score-security-controls.md)。



### <a name="advanced-data-security-for-sql-machines-azure-other-clouds-and-on-prem-preview"></a>适用于 SQL 计算机 (Azure、其他云和本地)  (预览版的高级数据安全) 

Azure 安全中心的 SQL 计算机高级数据安全现在保护托管在 Azure 中、其他云环境甚至本地计算机上的 SQL Server。 这会扩展 Azure 本机 SQL Server 的保护，以完全支持混合环境。

高级数据安全为 SQL 计算机提供漏洞评估和高级威胁防护，无论它们位于何处。

设置涉及两个步骤：

1. 将 Log Analytics 代理部署到 SQL Server 的主机计算机，以提供与 Azure 帐户的连接。

1. 在安全中心的 "定价和设置" 页中启用可选捆绑。

详细了解 [SQL 虚拟机的高级数据安全性](defender-for-sql-usage.md)。



### <a name="two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview"></a> (预览版将 Log Analytics 代理部署到 Azure Arc 计算机的两个新建议) 

添加了两个新建议来帮助你将 [Log Analytics 代理](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent) 部署到 azure Arc 计算机，并确保它们受 Azure 安全中心保护：

- **Log Analytics 代理应在基于 Windows 的 Azure Arc 计算机上安装 (预览版) **
- **Log Analytics 代理应安装在基于 Linux 的 Azure Arc 计算机上 (预览版) **

这些新建议将与与现有 (相关的) 建议相同的四个安全控件中出现，并且 **应在计算机上安装监视代理**：修正安全配置、应用自适应应用程序控制、应用系统更新和启用 endpoint protection。

建议还包括快速修复功能，可帮助加速部署过程。 

在 [计算和应用建议](recommendations-reference.md#recs-computeapp) 表中了解有关这两个新建议的详细信息。

若要详细了解 Azure 安全中心如何使用代理，请参阅 [什么是 Log Analytics 代理？](https://docs.microsoft.com/azure/security-center/faq-data-collection-agents#what-is-the-log-analytics-agent)。

了解有关 [Azure Arc 计算机扩展的](https://docs.microsoft.com/azure/azure-arc/servers/manage-vm-extensions#enable-extensions-from-the-portal)详细信息。



### <a name="new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale"></a>用于大规模创建连续导出和工作流自动化配置的新策略

自动执行组织的监视和事件响应过程可以极大地提高调查和缓解安全事件所花的时间。

若要在整个组织中部署自动化配置，请使用这些内置的 "DeployIfdNotExist" Azure 策略来创建和配置 [连续导出](continuous-export.md) 和 [工作流自动化](workflow-automation.md) 过程：

可在 Azure 策略中找到策略：


|目标  |策略  |策略 ID  |
|---------|---------|---------|
|连续导出到事件中心|[为 Azure 安全中心警报和建议部署“导出到事件中心”配置](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
|连续导出到 Log Analytics 工作区|[为 Azure 安全中心警报和建议配置“导出到 Log Analytics 工作区”配置](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
|安全警报的工作流自动化|[为 Azure 安全中心警报部署工作流自动化](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|安全建议的工作流自动化|[为 Azure 安全中心建议部署工作流自动化](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
||||

[工作流自动化模板](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation)入门。

详细了解如何 [通过策略持续导出 Azure 安全中心警报和建议](https://techcommunity.microsoft.com/t5/azure-security-center/continuously-export-azure-security-center-alerts-and/ba-p/1440745)中的两个导出策略。


### <a name="new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines"></a>使用 Nsg 保护非面向 internet 的虚拟机的新建议

"实施安全最佳做法" 安全控制现在包括以下新建议：

- **应使用网络安全组来保护非面向 Internet 的虚拟机**

现有建议、 **面向 internet 的虚拟机应使用网络安全组进行保护**，而不区分面向 internet 的虚拟机和面向 Internet 的 vm。 对于这两种情况，如果未将 VM 分配到网络安全组，则会生成高严重性建议。 此新建议将非面向 internet 的计算机隔开，以减少误报并避免不必要的高严重性警报。

在 [网络建议](recommendations-reference.md#recs-network) 表中了解详细信息。




### <a name="new-policies-for-enabling-threat-protection-and-advanced-data-security"></a>用于启用威胁防护和高级数据安全性的新策略

下面的新策略已添加到 ASC 默认计划中，旨在帮助为相关资源类型启用威胁防护或高级数据安全性。

可在 Azure 策略中找到策略：


| 策略                                                                                                                                                                                                                                                                | 策略 ID                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------|
| [应在 Azure SQL 数据库服务器上启用高级数据安全](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f7fe3b40f-802b-4cdd-8bd4-fd799c948cc2)     | 7fe3b40f-802b-4cdd-8bd4-fd799c948cc2 |
| [应在计算机的 SQL 服务器上启用高级数据安全](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f6581d072-105e-4418-827f-bd446d56421b) | 6581d072-105e-4418-827f-bd446d56421b |
| [应对 Azure 存储帐户启用高级威胁防护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f308fbb08-4ab8-4e67-9b29-592e93fb94fa)           | 308fbb08-4ab8-4e67-9b29-592e93fb94fa |
| [应对 Azure Key Vault 的保管库启用高级威胁防护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f0e6763cc-5078-4e64-889d-ff4d9a839047)           | 0e6763cc-5078-4e64-889d-ff4d9a839047 |
| [应在 Azure 应用服务计划上启用高级威胁防护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f2913021d-f2fd-4f3d-b958-22354e2bdbcb)                | 2913021d-f2fd-4f3d-b958-22354e2bdbcb |
| [应对 Azure 容器注册表的注册表启用高级威胁防护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4)   | c25d9a16-bc35-4e15-a7e5-9db606bf9ed4 |
| [应对 Azure Kubernetes 服务的群集启用高级威胁防护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f523b5cd1-3e23-492f-a539-13118b6d1e3a)   | 523b5cd1-3e23-492f-a539-13118b6d1e3a |
| [应在虚拟机上启用高级威胁防护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4da35fc9-c9e7-4960-aec9-797fe7d9051d)           | 4da35fc9-c9e7-4960-aec9-797fe7d9051d |
|                                                                                                                                                                                                                                                                       |                                      |

详细了解 [Azure 安全中心的威胁防护](https://docs.microsoft.com/azure/security-center/threat-protection)。





## <a name="may-2020"></a>2020 年 5 月

中的更新可能包括：
- [警报抑制规则（预览版）](#alert-suppression-rules-preview)
- [虚拟机漏洞评估现已正式发布](#virtual-machine-vulnerability-assessment-is-now-generally-available)
- [对实时 (JIT) 虚拟机 (VM) 访问权限的更改](#changes-to-just-in-time-jit-virtual-machine-vm-access)
- [自定义建议已移至单独的安全控件](#custom-recommendations-have-been-moved-to-a-separate-security-control)
- [已添加开关，可在控件中显示建议或以简单列表的形式显示](#toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list)
- [扩展了“实现安全最佳做法”这一安全控件](#expanded-security-control-implement-security-best-practices)
- [具有自定义元数据的自定义策略现已正式发布](#custom-policies-with-custom-metadata-are-now-generally-available)
- [故障转储分析功能正在迁至无文件攻击检测中](#crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection)


### <a name="alert-suppression-rules-preview"></a>警报抑制规则（预览版）

这项新功能目前为预览版，它可帮助缓解警报疲劳。 可使用规则来自动隐藏已知无害或已知与你组织中的正常活动相关的警报。 这可让你专注于最相关的威胁。 

仍将生成与你启用的抑制规则相匹配的警报，但它们的状态将设置为“已取消”。 你可在 Azure 门户中查看状态，也可在安全中心查看安全警报。

抑制规则定义了自动取消警报所应遵循的条件。 通常，使用抑制规则来：

- 取消已标识为“误报”的警报

- 取消限制过于频繁地触发而失去作用的警报

详细了解如何[取消来自 Azure 安全中心威胁防护服务的警报](alerts-suppression-rules.md)。


### <a name="virtual-machine-vulnerability-assessment-is-now-generally-available"></a>虚拟机漏洞评估现已正式发布

安全中心的标准层现包含集成的虚拟机漏洞评估，该扩展免费提供。 该扩展由 Qualys 提供支持，但将检测结果直接报告回安全中心。 你无需具备 Qualys 许可证，甚至还不需要 Qualys 帐户 - 所有操作都在安全中心内无缝执行。

这一新的解决方案可持续扫描你的虚拟机来找出漏洞，并在安全中心显示检测结果。 

若要部署该解决方案，请使用新的安全建议：

“在虚拟机上启用内置漏洞评估解决方案（由 Qualys 提供支持）”

详细了解[安全中心集成的虚拟机漏洞评估](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)。



### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>对实时 (JIT) 虚拟机 (VM) 访问权限的更改

安全中心包含一项可选功能，可保护 VM 的管理端口。 这可抵御最常见形式的暴力攻击。

本次更新就此功能进行了以下更改：

- 重命名了推荐你在 VM 上启用 JIT 的建议。 之前称为“应在虚拟机上应用实时网络访问控制”，而现在叫做“应通过即时网络访问控制来保护虚拟机的管理端口”。

- 仅当存在打开的管理端口时，才会触发此建议。

详细了解 [JIT 访问功能](security-center-just-in-time.md)。


### <a name="custom-recommendations-have-been-moved-to-a-separate-security-control"></a>自定义建议已移至单独的安全控件

增强安全分数引入的一个安全控制是 "实现安全最佳做法"。 为订阅创建的所有自定义建议已自动放入该控件中。 

为便于查找自定义建议，我们已将这些建议移到一个名为“自定义建议”的专用安全控件中。 此控件不会影响你的安全功能分数。

要详细了解安全控件，请参阅 [Azure 安全中心的安全功能分数增强版（预览版）](secure-score-security-controls.md)。


### <a name="toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list"></a>已添加开关，可在控件中显示建议或以简单列表的形式显示

安全控件是相关安全建议的逻辑组。 它们反映了易受攻击的攻击面。 控件是一组安全建议，附有帮助你实施这些建议的说明。

若要立即查看组织对每个攻击面的保护情况，请查看每个安全控件的分数。

默认情况下，你的建议显示在安全控件中。 在此更新中，还可以将其显示为列表。 若要以简单列表的形式查看它们，且列表按受影响的资源的运行状况排序，请使用新的“按控件分组”开关。 开关位于门户中列表的上面。

安全控件及其开关是新的安全功能分数体验的一部分。 请记得在门户中提供反馈。

要详细了解安全控件，请参阅 [Azure 安全中心的安全功能分数增强版（预览版）](secure-score-security-controls.md)。

:::image type="content" source="./media/secure-score-security-controls/recommendations-group-by-toggle.gif" alt-text=""分组依据" 控件切换建议":::

### <a name="expanded-security-control-implement-security-best-practices"></a>扩展了“实现安全最佳做法”这一安全控件 

增强安全分数引入的一个安全控制是 "实现安全最佳做法"。 如果建议在此控件中显示，则不影响安全功能分数。 

通过本次更新，已将三项建议从它们原先所在的控件移动到这个最佳做法控件中。 我们采取此步骤的原因是我们判定这三项建议的风险比最初设想的要低。

此外，还引入了两项新建议，它们也添加到了此控件中。

移动的三项建议如下：

- **应在对订阅拥有读取权限的帐户上启用 MFA**（原先位于“启用 MFA”控件中）
- **应从订阅中删除具有读取权限的外部帐户**（原先位于“管理访问和权限”控件中）
- **只多只为订阅指定 3 个所有者**（原先位于“管理访问和权限”控件中）

添加到控件中的两项新建议如下：

- **应在 windows 虚拟机上安装来宾配置扩展 (预览) ** -使用 [Azure 策略来宾配置](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration) 可在虚拟机内部向服务器和应用程序设置提供 (仅限 Windows 的) 中的可见性。

- **应在计算机上启用 Windows Defender 攻击防护 (预览) ** -Windows Defender exploit Guard 利用 Azure 策略来宾配置代理。 攻击防护服务具有 4 个组件，旨在锁定设备来阻隔各种攻击途径，并阻止恶意软件攻击中常用的行为，同时让企业能够平衡其安全风险和生产力要求（仅限 Windows）。

要详细了解 Windows Defender 攻击防护，可参阅[创建和部署攻击防护策略](https://docs.microsoft.com/mem/configmgr/protect/deploy-use/create-deploy-exploit-guard-policy)。

了解更多有关安全控制的详细信息，请 [ (预览) ](secure-score-security-controls.md)。



### <a name="custom-policies-with-custom-metadata-are-now-generally-available"></a>具有自定义元数据的自定义策略现已正式发布

自定义策略现显示在安全中心的建议体验、安全功能分数和法规符合性标准仪表板中。 此功能现已正式发布，可用于在安全中心扩大你组织的安全评估范围。 

在 Azure 策略中创建自定义计划，向该计划添加策略并将它加入 Azure 安全中心，然后将它作为建议直观呈现。

现在，我们还添加了可编辑自定义建议元数据的选项。 元数据选项中有严重级别、修正步骤和威胁信息等。  

详细了解[利用详细信息增强自定义建议](custom-security-policies.md#enhance-your-custom-recommendations-with-detailed-information)。



### <a name="crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection"></a>故障转储分析功能正在迁至无文件攻击检测中 

我们正在将 Windows 故障转储分析 (CDA) 检测功能集成到[无文件攻击检测](https://docs.microsoft.com/azure/security-center/threat-protection#windows-fileless)中。 无文件攻击检测分析改进了 Windows 计算机的以下安全警报：“发现代码注入”、“检测到伪装 Windows 模块”、“发现 Shellcode”和“检测到可疑的代码段”。

该转换的一些优势如下：

- **主动和及时的恶意软件检测** -CDA 方法涉及到等待崩溃发生，然后运行分析以查找恶意项目。 使用无文件攻击检测后，可在内存中威胁正在运行时主动识别它们。 

- **警报信息更丰富** - 来自无文件攻击检测的安全警报包含 CDA 中不提供的丰富信息，例如有效网络连接信息。 

- **警报聚合** - CDA 在一个故障转储中检测到多个攻击模式时，会触发多个安全警报。 而无文件攻击检测将从同一进程中确定的所有攻击模式组合到一个警报中，免去了关联多个警报的必要性。

- **降低了对 Log Analytics 工作区的要求** - 包含潜在敏感数据的故障转储将无法上传到 Log Analytics 工作区。



## <a name="april-2020"></a>2020 年 4 月

4月的更新包括：
- [动态符合性包现已正式发布](#dynamic-compliance-packages-are-now-generally-available)
- [标识建议现包含在 Azure 安全中心的免费层中](#identity-recommendations-now-included-in-azure-security-center-free-tier)


### <a name="dynamic-compliance-packages-are-now-generally-available"></a>动态符合性包现已正式发布

Azure 安全中心的法规符合性仪表板现包含动态符合性包（现已正式发布），可跟踪更多行业和法规标准。

可通过安全中心的安全策略页面将动态符合性包添加到订阅或管理组中。 加入标准或基准后，该标准会出现在法规符合性仪表板中，所有关联的符合性数据都映射为评估。 还将提供已加入的所有标准的摘要报表供下载。

现在，你可添加如下标准：

- **NIST SP 800-53 R4**
- **SWIFT CSP CSCF-v2020**
- **UK Official 和 UK NHS**
- **加拿大联邦 PBMM**
- **Azure CIS 1.1.0（新版）** ，它是 Azure CIS 1.1.0 的完整表示形式

此外，我们还添加了 Azure 安全基准，它是 Microsoft 创作的特定于 Azure 的准则，适合基于常见符合性框架的安全与合规最佳做法。 其他标准一经提供就将在仪表板中受到支持。  
 
详细了解如何[在法规符合性仪表板中自定义一组标准](update-regulatory-compliance-packages.md)。


### <a name="identity-recommendations-now-included-in-azure-security-center-free-tier"></a>标识建议现包含在 Azure 安全中心的免费层中

Azure 安全中心免费层中针对标识和访问的安全建议现已正式发布。 这是我们努力使云安全状态管理 (CSPM) 功能免费而取得的成果之一。 截至目前，这些建议仅在标准定价层中提供。

标识和访问建议的示例包括：

- “应在对订阅拥有所有者权限的帐户上启用多重身份验证。”
- “最多只能为订阅指定 3 个所有者。”
- “应从订阅中删除弃用的帐户。”

如果你有订阅在免费定价层，则此更改将影响它们的安全功能分数，因为它们之前从未接受过标识和访问安全性评估。

详细了解[标识和访问建议](recommendations-reference.md#recs-identity)。

详细了解[监视标识和访问](security-center-identity-access.md)。