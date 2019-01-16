---
title: 让虚拟机产品/服务在 Azure 市场推出
description: 让虚拟机产品/服务在 Azure 市场推出
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: qianw211
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
ms.openlocfilehash: ad22f1944d3fe9a088b66da4cf4df7136db497f7
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2019
ms.locfileid: "54075324"
---
<a name="make-your-virtual-machine-offer-live-on-azure-marketplace"></a>让虚拟机产品/服务在 Azure 市场推出
=========================================================

填充产品/服务的所有详细信息以后，即可将产品/服务发布并让其在 Azure 市场中推出。 产品/服务需要经过几个阶段。 确保市场营销内容和 VM 映像都满足获得 Azure 认证和在网站上推出所需的质量要求。

![产品/服务推出步骤 0](./media/cloud-partner-portal-offer-go-live-azure-marketplace/makeanofferlive.png)

让我们更详细地介绍此过程，以便更好地了解每个步骤中发生的情况。 在此过程中，你需要采取行动以确保产品/服务继续取得进展。

<a name="publishing-process"></a>发布过程
------------------

单击“编辑器”选项卡下的“发布”以启动“发布”过程。

![产品/服务推出步骤 1 - 发布](./media/cloud-partner-portal-offer-go-live-azure-marketplace/publish.png)

“状态”选项卡下会显示“发布步骤”以及产品/服务的进度。

![产品/服务推出步骤 2 - 状态](./media/cloud-partner-portal-offer-go-live-azure-marketplace/status.png)

在发布过程中，还可以随时登录，单击“所有产品/服务”选项卡即可查看任何产品/服务的最新状态。 可以直接单击产品/服务的状态，查看有关产品/服务发布进度的详细信息。

![产品/服务推出步骤 3 - 所有产品/服务状态](./media/cloud-partner-portal-offer-go-live-azure-marketplace/alloffersstatus.png)

让我们演练一下每个发布步骤，讨论每个步骤发生的情况，而你则应估算一下每一步所需的时间。

**验证先决条件（\<1 天）**

单击“发布”时，系统会进行自动检查，确保已填充了产品/服务的所有必填字段。 如果有字段未填充，则会在该字段旁边显示警告。需要准确填充该字段，并再次单击“发布”。

正确完成此步骤以后，会显示一个弹出窗口，提示输入电子邮件地址。 该地址是在接下来的发布过程中用于接收发布状态通知的电子邮件地址。 提交电子邮件地址以后，此步骤即告完成。

![产品/服务推出步骤 4 - 发布产品/服务](./media/cloud-partner-portal-offer-go-live-azure-marketplace/publishyouroffer.png)

**认证（\<5 天）**

我们会在此步骤运行多项测试，确保 VM 映像满足 Azure 认证要求。 [此处](../cloud-partner-portal/virtual-machine/cpp-prerequisites.md)提供了需要的所有指南，确保满足认证要求。

由于此步骤可能需要数天的时间，你可以注销云合作伙伴门户。 如果存在需要你解决的错误，我们会向你发送电子邮件通知。 如果一切成功通过，会自动进入“预配”步骤。

**预配（\<1 天）**

在此阶段，我们会将映像复制到全球所有 Azure 数据中心，可能需要长达一天才能完成。

**打包以及潜在顾客开发注册（\<1 小时）**

在此阶段，我们会将技术内容和市场营销内容组合到一起，做成网站的产品页。

另外，如果配置了“潜在顾客开发”功能，我们会向 CRM 发送一位测试性潜在顾客，验证 CRM 集成是否正常工作。 此步骤完成以后，会看到一个记录，在 CRM 或 Azure 表中填充了假数据。 关于“潜在顾客开发”的所有文档都在[这里](./cloud-partner-portal-get-customer-leads.md)。

**产品/服务以预览版形式提供**

会收到通知电子邮件，告知你产品/服务已成功完成必需步骤，目前允许以预览方式访问该产品/服务。 应该在这一步预览产品/服务，确保一切正常，且 VM 已正常部署在过渡环境中。

只有已列入允许列表的订阅能够执行此验证。

**发布者签收**

在预览版中验证一切正确并可正常运行以后，即可推出产品/服务。 单击“状态”选项卡下的“推出”，我们就会启动相关步骤，让产品/服务以产品形式发布，在网站上推出。 从你单击“推出”到产品/服务在网站上推出，通常需要数小时。 产品/服务在网站上正式推出以后，我们会向你发送电子邮件通知。

![产品/服务推出步骤 5 - 推出](./media/cloud-partner-portal-offer-go-live-azure-marketplace/golive.png)

**推出**

产品/服务现已在 Azure 市场和 Azure 门户推出，客户可以在其 Azure 订阅中查看和部署虚拟机。 可以随时单击“所有产品/服务”选项卡，查看在右侧栏中列出的所有产品/服务的状态。 单击状态即可详细查看产品/服务的发布流状态。

<a name="error-handling"></a>错误处理
--------------

在发布过程中，可能会遇到错误。 如果遇到错误，会收到通知电子邮件，告知你出现错误，并提供后续步骤的指导。 在此过程中，也可以随时单击“状态”选项卡来查看错误。可以查看错误发生在此过程的什么时候，并且可以通过错误消息大致了解需要解决什么问题。

![产品/服务推出步骤 6 - 错误消息](./media/cloud-partner-portal-offer-go-live-azure-marketplace/errormessage.png)

如果在发布过程中遇到错误，则需解决这些问题，然后单击“发布”，重新启动发布过程。 纠正错误以后，如果需要重新发布，必须从头开始发布步骤，即从“验证先决条件”开始。

如果解决不了错误，则应提出支持请求，从我们的支持工程师处获取帮助。

![产品/服务推出步骤 7 - 获取支持](./media/cloud-partner-portal-offer-go-live-azure-marketplace/getsupport.png)

<a name="canceling-the-publishing-request"></a>取消发布请求
--------------------------------

在开始发布过程以后，可能需要取消发布请求。 只有在发布请求到达“发布者签收”这一步后，才能取消发布请求。 若要取消，请单击“取消发布”。 发布状态将重置为“步骤 1”。要再次发布，应单击“发布”并执行状态中显示的步骤。

![产品/服务推出步骤 8 - 状态](./media/cloud-partner-portal-offer-go-live-azure-marketplace/status5.png)

