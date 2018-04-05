---
title: Cortana Intelligence AppSource 发布指南 | Microsoft Docs
description: 作为 Microsoft 合作伙伴，以下是将 Cortana Intelligence 解决方案发布到 AppSource 需要执行的所有步骤。
services: machine-learning
documentationcenter: ''
author: AnupamMicrosoft
manager: jhubbard
editor: cgronlun
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: anupams
ms.openlocfilehash: 1a5671eda967c1bba585d31b001bb465526709f3
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="cortana-intelligence-appsource-publishing-guide"></a>Cortana Intelligence AppSource 发布指南

## <a name="overview"></a>概述
AppSource 是业务决策制定者 (BDM) 的单个目标平台，用户可在其中发现并无缝试用由合作伙伴生成并由 Microsoft 评估的业务解决方案/应用。 观看[此视频](https://youtu.be/hpq_Y9LuIB8)了解 AppSource 的工作原理。 

作为 Microsoft 合作伙伴，如果满足以下条件，那么在 AppSource 上进行发布可使你受益良多：
- 使用 [Cortana Intelligence 套件](https://azure.microsoft.com/en-us/suites/cortana-intelligence-suite/?cdn=disable)生成了智能解决方案/应用。
- 你的解决方案或应用可解决具体业务问题。
- 你的客户能够以可预测的方式相对快速地重用你所构建的模块或智力资产。

查看已在 AppSource 上发布的 [Cortana Intelligence 解决方案](https://appsource.microsoft.com/en-us/marketplace/apps?product=cortana-intelligence&page=1)。 

本文逐步介绍如何获取发布到 AppSource 的合作伙伴的 Cortana Intelligence 解决方案的相关步骤

## <a name="getting-started"></a>入门
1. 在[合作伙伴社区权益指南](https://www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/Partner%20Community%20Benefits%20Guide%20-%20Cloud%20and%20Enterprise.pdf) (PDF) 中，请参阅第 9 页以列为高级分析合作伙伴。
1. 完成[提交你的应用](https://appsource.microsoft.com/en-us/partners/list-an-app)表单。

    对于“选择你的应用所构建的产品”问题，选中“其他”复选框，并在编辑控件中列出“Cortana Intelligence”。
1. 在可以将 Cortana Intelligence 应用上架到 AppSource 之前，它必须获取 Cortana Intelligence 合作伙伴解决方案技术团队的认证。 要开始此过程，请在[面向 AppSource 发布的 Cortana Intelligence 解决方案评估](https://aka.ms/cisappsrceval)处填写调查表单，以共享有关你的应用的详细信息。 此站点受密码保护，并且站点提供了有关如何获取密码的说明。

## <a name="solution-evaluation-criteria"></a>解决方案评估标准
以下是应用需要满足的标准列表
1. 应用需要以可重复的方式在给定功能区域内解决特定业务用例问题，以最低配置实现预定义的价值主张且可在短时间内实现。
1. 解决方案应至少使用以下组件之一：

    - HDInsight
    - 机器学习
    - 数据湖分析
    - 流分析
    - 认知服务
    - Bot 框架
    - Analysis Services
    - Microsoft R Server 独立版
    - SQL 2016 或 HDInsight Premium 上的 R Services
1. 解决方案应让使用 DPOR/CSP 的每个客户每月至少获得 $1000。
1. 解决方案应使用 Azure Active Directory 联合单一登录（AAD 联合 SSO），同意启用用户身份验证和资源访问控制。 在可以将你的应用上架到 AppSource 之前，需要向评估团队证明你的解决方案支持 AAD 联合 SSO。

     若要查看支持 AAD 联合 SSO 具体意味着什么，请定位到 [AppSource 试用体验演练](https://aka.ms/trialexperienceforwebapps)视频中的 02:35 位置。 如果你的应用尚不支持 AAD 联合 SSO，以下提供了一些相关文档
   1. [一键登录](https://identity.microsoft.com/Landing?ru=https://identity.microsoft.com/)。
   1. [将应用程序与 Azure Active Directory 集成](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#adding-an-application)。
     
1. 在你的解决方案中使用 Power BI：此项可选，但强烈建议采用，因为经证明这会生成较大数量的潜在客户。

## <a name="devcenter-account-setup"></a>DevCenter 帐户设置
此过程通过 Microsoft 将你的公司注册为发布者。 如果你已使用现有 DevCenter 帐户注册为发布者，请共享与 DevCenter 帐户关联的电子邮件 ID。 在你的应用程序获得批准可发布后，你便能够访问关于[云门户管理发布者个人资料](https://cloudpartner.azure.com/#documentation/manage-publisher-profile)的完整文档

如果你没有帐户，请执行以下设置 DevCenter 帐户的关键步骤。
1. 在[此处](https://signup.live.com/signup.aspx)创建 Microsoft 帐户。
1. 转到 Microsoft DevCenter [注册页](http://go.microsoft.com/fwlink/?LinkId=615100)，然后单击“注册”。
1. 当系统提示付款时，使用我们向你提供的代码。 如果你没有代码，请联系 [appsourcecissupport@microsoft.com](mailto:appsourcecissupport@microsoft.com?subject=Request%20for%20promotion-code%20for%20DevCenter%20account%20setup)。
1. 选择你所在的[国家/地区](https://docs.microsoft.com/windows/uwp/publish/account-types-locations-and-fees)，或你的业务所在地。 稍后将无法更改此内容。
1. 选择你的[开发人员帐户类型](https://docs.microsoft.com/windows/uwp/publish/account-types-locations-and-fees) 对于 AppSource，选择“公司”。 对于公司帐户，请务必查看这些[准则](https://docs.microsoft.com/windows/uwp/publish/opening-a-developer-account)。
1. 输入要用于你的开发人员帐户的联系人信息。
有关如何设置 DevCenter 帐户的详细分步说明，请参阅[此处](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-accounts-creation-registration)的说明。

## <a name="provide-info-for-microsoft-sellers"></a>提供 Microsoft 卖家信息
对于合作伙伴而言，AppSource 的关键价值主张之一是能够与 Microsoft 卖方协作，面向潜在客户定位合作伙伴应用。

填写 [Microsoft 卖方的合作伙伴解决方案信息](https://aka.ms/aapartnerappinfo)并将其发送到 [appsourcecissupport@microsoft.com](mailto:appsourcecissupport@microsoft.com?subject=Request%20publisher%20account%20creation%20for%20%3cPartner%20Name%3e%20and%20whitelist%20owner/contributer%20AAD/MSA%20email%20IDs)。需要执行此步骤才能使 Cortana Intelligence 应用获得批准进行发布。

## <a name="build-a-compelling-customer-walkthrough-on-appsource"></a>在 AppSource 上构建具有吸引力的客户演练
首先，在 AppSource 上查看 [Neal Analytics 库存优化](https://appsource.microsoft.com/en-us/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview&tag=CISHome)。 除了试用体验的入口点之外，AppSource 中的每个应用条目还具有标题、摘要（不超过 100 个字符）、说明（不超过 1300 个字符）、图像、视频（可选）和 pdf 文档。 合作伙伴应利用所有这些信息构建具有吸引力的客户体验。

合作伙伴应将他们在 AppSource 上提供的内容视为端到端销售业务流程。 客户阅读标题和说明以了解价值主张，然后通过图像和视频了解解决方案的相关内容。 案例研究可帮助潜在客户了解现有客户如何获取价值。 

所有这些内容应使客户产生兴趣并想要了解详细信息。 将这些内容视为基于融资演讲稿的演示文稿，优秀的技术销售人员将使用它向新客户进行演示。 建议使用的说明格式是根据价值主张将文本分解为多个子部分，每个子部分都有一个突出显示的子标题。 

访问者通常会浏览“产品/服务摘要”字段和子标题，快速简要地了解应用所解决的问题以及为什么应考虑使用此应用。 因此，务必要吸引用户的注意力，让他们有理由继续阅读以获取具体信息。

查看这些合作伙伴所完成的事项。
- [Neal Analytics 库存优化](https://appsource.microsoft.com/en-us/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview)
- [Plexure 零售个性化](https://appsource.microsoft.com/en-us/product/web-apps/plexure.c82dc2fc-817b-487e-ae83-1658c1bc8ff2?tab=Overview)
- [AvePoint 公民服务](https://appsource.microsoft.com/en-us/product/web-apps/avepoint.7738ac97-fd40-4ed3-aaab-327c3e0fe0b3?tab=Overview)

销售的最终行为是呈现应用/解决方案的演示，介绍如何实现价值主张。 这正是 AppSource 上所提供的客户试用体验的意义所在。 出色的演示可以：
- 简短地重新概要介绍应用的价值主张，并列出客户公司中将与解决方案进行交互的角色
- 讲述案例并设置有关客户处理特定问题的示例的上下文
- 说明（之前）这种情况通常会如何演变并给客户带来影响，并与使用解决方案的情况进行对比。 可以使用 PowerBI 仪表板等工具进行展示。
- 概要介绍解决方案如何通过使用任何特定的机器学习算法等方式实现。

添加到 AppSource 中的内容应具有较高质量，并且足够全面，以达到以下效果：
- 合作伙伴的技术销售人员会将它用于销售业务流程。 如果你的销售团队使用它，这会是一个好的迹象，这样就可以预计 MS 销售人员也会使用它。 这将使客户接触点能够一致地将相同案例转述给团队同事和更高层领导，获取预算和审批后完成购买交易。
- 进行了系统了解的客户在访问站点时可以自行浏览所有内容，并积极回复合作伙伴通信以执行后续步骤。

因此，合作伙伴应将他们在 AppSource 上提供的内容视为端到端销售业务流程。 根据试用体验中展示的情节和功能，可以确定产品/服务类型。

## <a name="publish-your-app-on-the-publishing-portal"></a>在发布门户上发布应用
我们为你的应用程序评估上述步骤后，你将获得对发布门户的访问权限，并可以查看[如何通过云合作伙伴门户发布 Cortana Intelligence 产品/服务](https://cloudpartner.azure.com/#documentation/cloud-partner-portal-publish-cortana-intelligence-app)，了解有关后续步骤的详细说明。

如需了解有关任何字段的信息，请发送电子邮件至 <appsourcecissupport@microsoft.com>。
## <a name="my-app-is-published-on-appsource---now-what"></a>我的应用已在 AppSource 上发布 - 现在要做什么？
首先，祝贺你成功发布应用。
通过在 AppSource 上发布应用可获得的回报水平主要取决于如何影响目标受众。 请参阅[在 AppSource 上黑客式营销 Cortana Intelligence 应用](http://aka.ms/aagrowthhackguide)，详细了解如何使回报最大化。

如果有任何问题或建议，请通过以下电子邮件地址联系我们：<appsourcecissupport@microsoft.com>。

