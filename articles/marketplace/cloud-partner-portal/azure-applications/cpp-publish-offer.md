---
title: 发布 Azure 应用程序套餐 - Azure 市场 | Microsoft Docs
description: 介绍在 Azure 市场上发布 Azure 应用程序产品/服务的过程和步骤。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: pbutlerm
ms.openlocfilehash: deab8298d09972a4e60be5c72d6d7e43dc2683e6
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2019
ms.locfileid: "54329984"
---
# <a name="publish-azure-application-offer"></a>发布 Azure 应用程序产品/服务

通过在“新建套餐”页上提供信息创建套餐后，可以发布该套餐。 选择“发布”启动发布过程。

下图显示了发布套餐以使其“上线”的过程的主要步骤。

![产品/服务发布步骤](./media/offer-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>发布步骤的详细说明

下表列出并描述了每个发布步骤，并提供了完成该步骤预计所需的时间。  预计时间以“天”为单位，以工作日定义，不包括周末和节假日。

|  **发布步骤**           | **时间**    | **说明**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| 验证先决条件         | 小于 15 分钟    | 验证套餐信息和套餐设置。                        |
| 验证影响的收入设置 | 小于 15 分钟  |      |
| 认证                  | 小于 1 天     | Azure 认证团队对套餐进行分析。 针对病毒、恶意软件、安全合规性和安全问题对产品/服务进行扫描。 检查产品/服务，以了解它是否满足所有资格条件。 有关详细信息，请参阅[先决条件](./cpp-prerequisites.md)。 如果发现了问题，则提供反馈。 |
| 体验版验证          | 小于 2 小时   | （可选）如果存在体验版，Microsoft 将验证是否可以部署和复制该体验版。  |
| 打包以及潜在顾客开发注册 | 小于 1 小时  | 打包套餐的技术资产供客户使用，配置并部署潜在顾客系统。 |
|  发布者签核             |  manual    | 最终发布者在套餐上线之前进行复查和确认。 套餐现在可供预览。  可以在所选订阅（在套餐信息步骤中）中部署套餐，以验证它是否符合所有要求。  验证套餐后，选择“上线”，使套餐可以转移到下一步骤。 |
| Microsoft 评审                | 7 - 14 天 | Microsoft 对 Azure 应用程序进行整体评审，如果发现问题，将通过电子邮件告诉你。  此步骤所需的时间取决于应用程序的复杂性、发现的问题，以及你对这些问题的响应速度。  |
| 实时                           | 小于 1 天 | 发布套餐，将其复制到指定的区域，并使其可供公众使用。 |
|   |&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|   |
 
可以在云合作伙伴门户上套餐的“状态”选项卡中监视发布过程。

![Azure 应用套餐的“状态”选项卡](./media/offer-status-tab.png)

完成发布过程后，套餐将在 [Microsoft Azure 市场应用程序类别](https://azuremarketplace.microsoft.com/marketplace/apps/)中列出。


## <a name="errors-and-review-feedback"></a>错误和评审反馈

除了显示套餐的发布状态以外，“状态”选项卡还显示“Microsoft 评审”步骤返回的错误消息和反馈。  通常，评审问题引用为拉取请求 (PR)。  每个拉取请求链接到某个联机 Visual Studio Team Services（VSTS，已重命名为 [Azure DevOps](https://azure.microsoft.com/services/devops/)）项，该项包含有关问题的详细信息。  下图显示了评审 PR 引用的示例。  对于更复杂的情况，评审和支持团队可能会向你发送电子邮件。 

![显示评审反馈的“状态”选项卡](./media/status-tab-ms-review.png)

必须先解决所报告的每个问题，然后，套餐才能转到发布过程的下一步骤。  下图演示了此反馈过程与发布过程之间的关系。

![提供 VSTS 反馈的发布步骤](./media/pub-flow-vsts-access.png)


### <a name="vsts-access"></a>VSTS 访问

若要查看评审反馈中引用的 VSTS 项，必须为发布者授予适当的授权。  否则，新发布者会收到 `401 - Not Authorized` 响应页。  若要请求访问套餐评审 VSTS 系统，请执行以下步骤：

1. 收集以下信息：
    - 发布者名称和 ID
    - 套餐类型（Azure 应用程序）、套餐名称和 SKU ID
    - 拉取请求链接，例如：`https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>` 可以从通知消息或 401 响应页的地址检索此 URL。
    - 发布组织中要向其授予访问权限的个人的电子邮件地址。  这些地址应包括在云合作伙伴门户上注册为发布者时提供的所有者地址。
2. 创建支持事件。  在云合作伙伴门户的标题栏中选择“帮助”按钮，然后从菜单中选择“支持”。  默认的 Web 浏览器应会启动，并导航到 Microsoft 的新建支持事件页。  （可能需要先登录。）
3. 指定“市场登记”作为“问题类型”，指定“访问问题”作为“类别”，然后选择“开始请求”。

    ![支持票证类别](./media/support-incident1.png)

4. 在“步骤 1/2”页上，提供联系信息并选择“继续”。
5. 在“步骤 2/2”页上，指定事件标题（例如 `Request VSTS access`），并提供在上述第一个步骤中收集的信息。  阅读并接受协议，然后选择“提交”。

如果事件创建成功，会显示确认页。  保存确认信息供将来参考。  Microsoft 支持部门会在几个工作日内回复你的访问请求。


## <a name="next-steps"></a>后续步骤

发布 Azure 应用后，可以[更新现有套餐](./cpp-update-existing-offer.md)以反映业务或技术要求的变化。 
