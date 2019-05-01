---
title: 处理 Azure 应用程序评审反馈 - Azure 市场 | Microsoft Docs
description: 介绍如何使用 Azure DevOps 来处理 Azure 市场中 Azure 应用程序套餐的评审反馈。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 02/05/2019
ms.author: pbutlerm
ms.openlocfilehash: e5fe076cfe733b4fa0151570603c090af98de0e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60745493"
---
# <a name="handling-review-feedback"></a>处理评审反馈

本文介绍如何访问 Microsoft Azure 市场评审团队使用的 Azure DevOps 环境。  如果 **Microsoft 评审**步骤发现你的 Azure 应用程序套餐存在严重问题，你可以登录到此系统来查看有关这些问题（评审反馈）的详细信息。  解决这些问题后，必须重新提交套餐才能继续将其发布到 Azure 市场中。  下图演示了此反馈过程与发布过程之间的关系。

![提供 VSTS 反馈的发布步骤](./media/pub-flow-vsts-access.png)

通常，评审问题引用为拉取请求 (PR)。  每个 PR 链接到一个联机 [Azure DevOps](https://azure.microsoft.com/services/devops/)（以前称为 Visual Studio Team Services (VSTS)）项，该项包含有关问题的详细信息。  下图显示了评审 PR 引用的示例。  对于更复杂的情况，评审和支持团队还可能会向你发送电子邮件。 

![显示评审反馈的“状态”选项卡](./media/status-tab-ms-review.png)


## <a name="vsts-access"></a>VSTS 访问

若要查看评审反馈中引用的 PR 项，必须先为发布者授予适当的授权。  否则，新发布者在尝试查看 PR 时会收到 `401 - Not Authorized` 响应页。  若要请求访问此 Azure DevOps 存储库，请执行以下步骤：

1. 收集以下信息：
    - 发布者名称和 ID
    - 套餐类型（Azure 应用程序）、套餐名称和 SKU ID
    - 拉取请求链接，例如：`https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>` 可以从通知消息或 401 响应页的地址检索此 URL。
    - 发布组织中要向其授予访问权限的个人的电子邮件地址。  此列表应包括在云合作伙伴门户上注册为发布者时提供的所有者地址。
2. 创建支持事件。  在云合作伙伴门户的标题栏中选择“帮助”按钮，然后从菜单中选择“支持”。  默认的 Web 浏览器应会启动，并导航到 Microsoft 的新建支持事件页。  （可能需要先登录。）
3. 指定“市场登记”作为“问题类型”，指定“访问问题”作为“类别”，然后选择“开始请求”。

    ![支持票证类别](./media/support-incident1.png)

4. 在“步骤 1/2”页上，提供联系信息并选择“继续”。
5. 在“步骤 2/2”页上，指定事件标题（例如 `Request VSTS access`），并提供在上述第一个步骤中收集的信息。  阅读并接受协议，然后选择“提交”。

如果事件创建成功，会显示确认页。  保存此页上的确认信息供将来参考。  Microsoft 支持团队会在几个工作日内回复你的访问请求。


## <a name="reviewing-the-pull-request"></a>查看拉取请求 

使用以下过程查看拉取请求中所述的问题。

1. 在“发布步骤”窗体中的“Microsoft 评审”部分，单击某个 PR 链接以启动浏览器，并导航到此 PR 的“概述”页（主页）。  下图描绘了 Contoso 示例应用套餐的严重问题主页示例。  此页包含有关在 Azure 应用中找到的评审问题的有用摘要信息。  

    [![拉取请求主页](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> 单击图像展开内容。
    
2. （可选）在窗口右侧的“策略”部分，单击问题消息（在此示例为“策略验证失败”）以调查该问题的低级别详细信息，包括关联的日志文件。  错误通常显示在日志文件的底部。

3. 在主页左侧的菜单中，选择“文件”以显示构成此套餐的技术资产的列表文件。  Microsoft 评审者应已添加了备注来描述所发现的严重问题。  在以下示例中发现了两个问题。 

    [![拉取请求主页](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> 单击图像展开内容。

4. 单击左侧树中的每个备注节点导航到周围代码上下文中的备注。  在团队项目中修复源代码，以更正备注中所述的问题。

> [!Note]
> 无法在评审团队的 Azure DevOps 环境中编辑套餐的技术资产。  对于发布者而言，在此环境中只能阅读包含的源代码。  但是，可以回复备注，以方便 Microsoft 评审团队进行调查。

   在以下示例中，发布者已查看、更正并回复了第一个问题。

   ![解决第一个问题并回复备注](./media/first-comment-reply.png)


## <a name="next-steps"></a>后续步骤

更正评审反馈 (PR) 中所述的严重问题后，必须[重新发布 Azure 应用套餐](./cpp-publish-offer.md)。
