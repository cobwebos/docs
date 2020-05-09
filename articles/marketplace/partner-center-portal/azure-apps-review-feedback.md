---
title: 为 Microsoft 商业应用商店中的 Azure 应用服务提供处理评审反馈
description: 如何使用 Microsoft 合作伙伴中心的商业应用商店门户来处理 Azure 应用提供的查看反馈，以便在 Azure Marketplace、AppSource 或云解决方案提供商（CSP）计划中列出或销售。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: 464f4d5434c8c0b8615e24eb1a323ae66a52273c
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82744869"
---
# <a name="handling-review-feedback-for-azure-apps-offer-before-publishing-in-the-azure-marketplace"></a>在 Azure Marketplace 中发布之前，处理 Azure 应用提供的反馈

本文介绍如何访问 Microsoft Azure 市场评审团队使用的 Azure DevOps 环境。 如果 **Microsoft 评审**步骤发现你的 Azure 应用程序套餐存在严重问题，你可以登录到此系统来查看有关这些问题（评审反馈）的详细信息。 解决这些问题后，必须重新提交套餐才能继续将其发布到 Azure 市场中。 下图演示了此反馈过程与发布过程之间的关系。

![查看反馈流程](./media/review-feedback-process.png)

通常，评审问题引用为拉取请求 (PR)。 每个 PR 链接到一个联机 [Azure DevOps](https://azure.microsoft.com/services/devops/)（以前称为 Visual Studio Team Services (VSTS)）项，该项包含有关问题的详细信息。 下图显示了在评审过程中发现问题时合作伙伴中心体验的示例。 

![发布状态](./media/publishing-status.png)

包含有关提交的特定详细信息的 PR 将在 "查看认证报告" 链接中提到。 对于更复杂的情况，评审和支持团队还可能会向你发送电子邮件。

## <a name="azure-devops-access"></a>Azure DevOps 访问

有权访问合作伙伴中心的 "开发人员" 角色的所有用户都有权查看查看反馈中引用的 PR 项。

<!---
To view the PR items referenced in review feedback, publishers must first be granted proper authorization. Otherwise, new publishers receive a 401 - Not Authorized response page when trying to view PRs. To request access to this Azure DevOps repository, perform the following steps:

1. Collect the following information:
    * Your publisher name and ID
    * Offer type (Azure app), offer name, and SKU ID
    * The pull request link, for example: `https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>`. This URL can be retrieved from the notification message or the address of the 401 response page.
    * The email address(es) of the individuals from your publishing organization that you want access granted to. This list should include the owner address(es) you provided when registering as a publisher on the Cloud Partner Portal (or Partner Center??).
1. Create a support incident. In the title bar of the Cloud Partner Portal (??), select the **Help** button, then choose **Support** from the menu. Your default web browser should launch and navigate to the Microsoft new support incident page. (You may have to sign in first.)
1. Specify the **Problem type** as **marketplace onboarding** and **Category** as **Access problem**, then select **Start request**.

    ![Create an incident](./media/create-an-incident.png)

1. In **Step 1 of 2** page, supply your contact information and select **Continue**.
1. In **Step 2 of 2** page, specify an incident title (for example Request Azure DevOps access) and supply the information you collected in the first step (above). Read and accept the agreement, then select **Submit**.

If the incident creation was successful, a confirmation page is displayed. Save the confirmation information on this page for your reference. The Microsoft Support Team should reply to your access request within a few business days. --->

## <a name="reviewing-the-pull-request"></a>查看拉取请求

使用以下过程查看拉取请求中所述的问题。

1. 在**Microsoft 查看**发布步骤窗体部分中，单击 "pr" 链接以启动浏览器并导航到此 PR 的 "**概述**" （主页）页面。 下图描绘了 Contoso 示例应用程序产品/服务的关键问题主页的示例。 此页包含有关在 Azure 应用中找到的评审问题的有用摘要信息。

    [![拉取请求主页](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> 单击图像展开内容。**

1. 可有可无在窗口右侧的 "**策略**" 部分中，单击 "问题" 消息（在本示例中为 "**策略验证失败**"）以调查问题的低级详细信息，包括关联的日志文件。 错误通常显示在日志文件的底部。
1. 在主页左侧的菜单中，选择“文件”以显示构成此套餐的技术资产的列表文件。**** Microsoft 评审者应已添加了备注来描述所发现的严重问题。 在以下示例中发现了两个问题。

    [![拉取请求主页](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> 单击图像展开内容。**

1. 单击左侧树中的每个备注节点导航到周围代码上下文中的备注。 在团队项目中修复源代码，以更正备注中所述的问题。

>[!Note]
>无法在评审团队的 Azure DevOps 环境中编辑套餐的技术资产。 对于发布者而言，在此环境中只能阅读包含的源代码。 但是，可以回复备注，以方便 Microsoft 评审团队进行调查。

   在以下示例中，发布者已查看、更正并回复了第一个问题。

   ![解决第一个问题并回复备注](./media/first-comment-reply.png)

## <a name="next-steps"></a>后续步骤

更正评审反馈 (PR) 中所述的严重问题后，必须[重新发布 Azure 应用套餐](./create-new-azure-apps-offer.md#publish)。
