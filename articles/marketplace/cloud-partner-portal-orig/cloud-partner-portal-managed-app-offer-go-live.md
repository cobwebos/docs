---
title: TITLE | Microsoft Docs
description: .
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
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 643b7d674de3d51f98b69d6f0659478c9893e3b1
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2018
ms.locfileid: "48805675"
---
<a name="make-your-azure-application-offer-live-on-azure-marketplace"></a>在 Azure 市场中推出你的 Azure 应用程序产品/服务 
===========================================================

现在，你已填充了产品/服务的所有详细信息，是时候发布你的产品/服务并在 Azure 市场中推出了。\'\' 需要执行许多步骤，在网站上推出之前，请确保你的市场营销内容和你的应用程序满足获得 Azure 认证的质量要求。

![发布流](./media/cloud-partner-portal-publish-managed-app/publish_flow.png)

让我们更详细地讨论一下此过程，以便更好地了解每一步的具体内容，以及在此过程中你需要执行哪些操作项来确保产品/服务在这方面持续获得进展。\'

<a name="publishing-process"></a>发布过程 
------------------

你将在“编辑器”选项卡下单击“发布”来启动发布过程。\"\"

![产品/服务推出步骤 1 - 发布](./media/cloud-partner-portal-publish-managed-app/newOffer_managedApp_publish.png)

在“状态”选项卡下，你将看到“发布步骤”以及你的产品/服务处于哪个步骤。

![产品/服务推出步骤 2 - 工作流](./media/cloud-partner-portal-publish-managed-app/newOffer_managedApp_workflow.png)

在发布过程中，还可以随时登录，单击“所有产品/服务”选项卡即可查看任何产品/服务的最新状态。 可以直接单击产品/服务的状态，查看有关产品/服务发布进度的详细信息。

让我们演练一下每个发布步骤，讨论每个步骤发生的情况，而你则应估算一下每一步所需的时间。\'

### <a name="validate-pre-requisites-1-day"></a>验证先决条件（\< 1 天） 

单击“发布”时，系统会进行自动检查，确保已填充了产品/服务的所有必填字段。\"\"\' 如果有字段未填充，则会在该字段旁边显示警告。需要准确填充该字段，并再次单击“发布”。\'\'

正确完成此步骤以后，会显示一个弹出窗口，要求提供电子邮件地址。 该地址是在接下来的发布过程中用于接收发布状态通知的电子邮件地址。 提交电子邮件地址以后，此步骤即告完成。

![产品/服务推出步骤 1 - 发布 1](./media/cloud-partner-portal-publish-managed-app/newOffer_managedApp_publish1.png)

### <a name="certification-5-days"></a>认证（\<5 天） 

我们会在此步骤中运行多项测试，以确保你的 Azure 应用程序包满足 Azure 认证要求。

由于此步骤可能需要数天的时间，可以注销云合作伙伴门户。 如果存在任何错误，我们会向你发送电子邮件通知。 如果一切成功通过，会自动进入“预配”步骤。

### <a name="packaging-and-lead-generation-registration-1-hour"></a>打包以及潜在顾客开发注册（\< 1 小时） 

在此阶段，我们会将技术内容和市场营销内容组合到一起，做成网站的产品页。

### <a name="offer-available-in-preview"></a>产品/服务以预览版提供 

会收到通知电子邮件，告知你产品/服务已成功完成必需步骤，目前允许以预览方式访问该产品/服务。 在此步骤中，应当预览你的产品/服务并确保一切事项看起来符合预期。 检查是否已正确地在过渡环境中部署了你的 VM。

只有已列入允许列表的订阅能够执行此验证。\*

### <a name="publisher-sign-out"></a>发布者注销 

在预览版中验证一切正确并可正常运行以后，即可推出产品/服务。 单击“状态”选项卡下的“推出”，我们就会启动相关步骤，让产品/服务以产品形式发布，在网站上推出。 从你单击“推出”到产品/服务在网站上推出，通常需要数小时。 产品/服务在网站上正式推出以后，我们会向你发送电子邮件通知。

![产品/服务推出步骤 6 - 推出](./media/cloud-partner-portal-publish-managed-app/newOffer_managedApp_goLive.png)

### <a name="microsoft-review"></a>Microsoft 评审 

在准备好继续前进后，单击“推出”\"\"，这会在 Visual Studio Online 中创建一个工作网站，然后 Microsoft 将对包内容进行代码评审。 代码评审将包括在创作模板时要使用的最佳模式/做法，以及用于优化资源创建的技巧和诀窍。 如果反馈导致发布者对文件进行更改，则必须重新启动发布过程。 当前发布将被取消，你必须在解决反馈意见后重新发布。

### <a name="live"></a>推出

你的产品/服务现已在 Azure 市场和 Azure 门户中推出。 你的客户将能够在其 Azure 订阅中查看并部署你的 Azure 托管应用程序。 可以单击“所有产品/服务”选项卡，查看在右侧栏中列出的所有产品/服务的状态。 单击状态即可详细查看产品/服务的发布流状态。

### <a name="error-handling"></a>错误处理 

如果遇到错误，则会收到通知电子邮件，告知你出现错误，并提供后续步骤的指导。 在此过程中，也可以随时单击“状态”选项卡来查看错误。可以查看错误发生在此过程的什么时候，并且可以通过错误消息大致了解需要解决什么问题。

如果在发布过程中遇到错误，则需纠正这些错误，并单击“发布”，重新启动发布过程。\'\' 纠正错误以后，如果需要重新发布，必须从头开始发布步骤，即从“验证先决条件”开始。

如果解决不了错误，则应提出支持请求，从我们的支持工程师处获取帮助。

### <a name="canceling-the-publishing-request"></a>取消发布请求

在开始发布过程以后，可能需要取消发布请求。 只有在发布请求到达“发布者签收”这一步后，才能取消发布请求。 若要取消，请单击“取消发布”。\'\' 发布状态将重置为“步骤 1”。要再次发布，应单击“发布”并执行状态中显示的步骤。
