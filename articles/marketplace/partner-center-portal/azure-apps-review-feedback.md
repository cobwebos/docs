---
title: 关于 Azure 应用套餐的评审反馈 - Microsoft 商业市场
description: 如何处理 Microsoft Azure 市场评审团队关于你的 Azure 应用程序套餐的反馈。 你可使用合作伙伴中心凭据在 Azure DevOps 中查看反馈。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: e8d90f5238ea312602d2142d25b9ae226aa34c84
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119015"
---
# <a name="handling-review-feedback-for-azure-application-offers"></a>处理关于 Azure 应用程序套餐的评审反馈

本文介绍如何在 [Azure DevOps](https://azure.microsoft.com/services/devops/) 中查看来自 Microsoft Azure 市场评审团队的反馈。 如果 **Microsoft 评审**步骤发现你的 Azure 应用程序套餐存在严重问题，你可以登录到此系统来查看有关这些问题（评审反馈）的详细信息。 解决全部问题后，必须重新提交套餐才能继续在 Azure 市场中发布。 下图演示了此反馈过程与发布过程之间的关系。

![评审反馈过程](./media/review-feedback-process.png)

通常，评审问题被称为拉取请求 (PR)。 每个 PR 都与一个联机 Azure DevOps 项目关联，后者包含有关问题的详细信息。 下图的例子显示了在审批期间发生问题的情况下合作伙伴中心的体验。 

![发布状态](./media/publishing-status.png)

“查看认证报告”链接将提及 PR，其中有所提交内容的具体详细信息。 对于更复杂的情况，评审和支持团队还可能会向你发送电子邮件。

## <a name="azure-devops-access"></a>Azure DevOps 访问

有权在合作伙伴中心访问“开发人员”角色的用户都有权查看评审反馈中引用的 PR 项目。

## <a name="reviewing-the-pull-request"></a>查看拉取请求

使用以下过程查看拉取请求中所述的问题。

1. 在“发布步骤”表单的“Microsoft 评审”部分，单击一个 PR 链接以启动浏览器，然后导航到此 PR 的“概述”页面（主页） 。 下图的例子显示了 Contoso 示例应用套餐的“严重问题”主页。 此页包含有关在 Azure 应用中找到的评审问题的有用摘要信息。

    [![拉取请求主页](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> 单击此图像展开内容。

1. （可选）在窗口右侧的“策略”部分，选择问题消息（在本例中为“策略验证失败”）以调查该问题的低级别详细信息，包括关联的日志文件。 错误通常显示在日志文件的底部。

1. 在主页左侧的菜单中，选择“文件”以显示构成此套餐的技术资产的列表文件。 Microsoft 评审者应已添加了备注来描述所发现的严重问题。 在以下示例中发现了两个问题。

    [![拉取请求主页](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> 单击此图像展开内容。

1. 选择左侧树中的每个注释节点，导航到周围代码上下文中的注释。 在团队项目中修复源代码，以更正备注中所述的问题。

>[!Note]
>无法在评审团队的 Azure DevOps 环境中编辑套餐的技术资产。 对于发布者而言，在此环境中只能阅读包含的源代码。 但是，可以回复备注，以方便 Microsoft 评审团队进行调查。

   在以下示例中，发布者已查看、更正并回复了第一个问题。

   ![解决第一个问题并回复备注](./media/first-comment-reply.png)

## <a name="next-steps"></a>后续步骤

更正评审反馈 (PR) 中所述的严重问题后，必须[重新发布 Azure 应用套餐](./create-new-azure-apps-offer.md#publish)。
