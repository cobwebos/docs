---
title: "如何让我的产品/服务在 Azure 应用商店推出？  | Microsoft Docs"
description: "产品/服务需要经过数个阶段，才能确保你的市场营销内容和 VM 映像满足获得 Azure 认证和在网站推出的质量要求。"
services: marketplace-publishing
documentationcenter: 
author: rupeshazure
manager: hamidm
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/29/2017
ms.author: rupesk@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: 233d62b176303ca384aae9453c2e125c0c70fad2
ms.openlocfilehash: 4acd94e4b83164d52d2ce8db414bb99de109aca1
ms.lasthandoff: 04/12/2017


---


# <a name="make-your-virtual-machine-offer-live-on-azure-marketplace"></a>让你的虚拟机产品/服务在 Azure 应用商店推出

填充产品/服务的所有详细信息以后，即可将产品/服务发布并让其在 Azure 应用商店推出。  产品/服务需要经过数个阶段，才能确保你的市场营销内容和 VM 映像满足获得 Azure 认证和在网站推出的质量要求。


![产品/服务推出步骤 0](./media/cloud-partner-portal-offer-go-live-azure-marketplace/makeanofferlive.png)


让我们更详细地讨论一下此过程，以便更好地了解每一步的具体内容，以及何时需要采取措施来确保你的产品/服务在这方面持续获得进展。 


## <a name="publishing-process"></a>发布过程

若要启动“发布”过程，请在“编辑器”选项卡下单击“发布”。 

![产品/服务推出步骤 1](./media/cloud-partner-portal-offer-go-live-azure-marketplace/publish.png)


“状态”选项卡下会显示“发布步骤”以及产品/服务的进度。 


![产品/服务推出步骤 2](./media/cloud-partner-portal-offer-go-live-azure-marketplace/status.png)

在发布过程中，你还可以随时 登录，单击“所有产品/服务”选项卡即可查看任意产品/服务的最新状态。 可以直接单击产品/服务的状态，查看有关产品/服务发布进度的详细信息。

![产品/服务推出步骤 3](./media/cloud-partner-portal-offer-go-live-azure-marketplace/alloffersstatus.png)

让我们演练一下每个发布步骤，讨论每个步骤发生的情况，而你则应估算一下每一步的时间。 

**验证先决条件（< 1 天）**

单击“发布”时，系统会进行自动检查，确保你已填充了产品/服务的所有必填字段。 如果有字段未填充，则会在该字段旁边显示警告。你需要准确填充该字段，然后再次单击“发布”。 


正确完成此步骤以后，将会显示一个弹出窗口，要求你提供电子邮件地址。 该地址是在接下来的发布过程中用于接收发布状态通知的电子邮件地址。 提交电子邮件地址以后，此步骤即告完成。

![产品/服务推出步骤 4](./media/cloud-partner-portal-offer-go-live-azure-marketplace/publishyouroffer.png)

**认证（< 5 天）**

我们会在此步骤运行多项测试，确保你的 VM 映像满足 Azure 认证要求。 [此处](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-prerequisites)提供了你需要的所有指南，确保你满足认证要求。

由于此步骤可能需要数天的时间，你可以注销云合作伙伴门户。 如果存在需要你解决的错误，我们会向你发送电子邮件通知。 如果一切成功通过，将自动进入“预配”步骤。 

**预配（< 1 天）**

在此阶段，我们会将你的映像复制到全球所有 Azure 数据中心，可能需要长达一天才能完成。

**打包以及潜在顾客开发注册（< 1 小时）**

在此阶段，我们会将技术内容和市场营销内容组合到一起，做成网站的产品页。 

另外，如果你配置了“潜在顾客开发”功能，我们会向你的 CRM 发送一位测试性潜在顾客，验证你的 CRM 集成是否正常工作。 此步骤完成以后，你会看到一个记录，在 CRM 或 Azure 表中填充了假数据。 关于“潜在顾客开发”的所有文档都在这里。

**产品/服务以预览版形式提供**

你会收到通知电子邮件，告知你你的产品/服务已成功完成必需步骤，目前允许以预览方式访问该产品/服务。 你应该在这一步预览你的产品/服务，确保一切正常，且 VM 已正常部署在过渡环境中。 

**只有已列入允许列表的订阅能够执行此验证。***

**发布者签收**

在预览版中验证一切正确并可正常运行以后，即可推出你的产品/服务。 为此，请单击“状态”选项卡下的“推出”，我们就会启动相关步骤，让你的产品/服务以产品形式发布，在网站上推出。 从你单击“推出”到产品/服务在网站上推出，通常需数小时。 你的产品/服务在网站上正式推出以后，我们会向你发送电子邮件通知。

![产品/服务推出步骤 5](./media/cloud-partner-portal-offer-go-live-azure-marketplace/golive.png)

**推出**    

你的产品/服务现已在 Azure 应用商店和 Azure 门户推出，客户可以在其 Azure 订阅中查看和部署你的虚拟机。
你可以随时单击“所有产品/服务”选项卡，查看在右侧栏中列出的所有产品/服务的状态。 单击状态即可详细查看产品/服务的发布流状态。

## <a name="error-handling"></a>错误处理

在发布过程中，可能会遇到错误。 如果遇到错误，你会收到通知电子邮件，告知你出现错误，并为你提供后续步骤的指导。 在此过程中，你也可以随时单击“状态”选项卡来查看错误。 你可以查看错误发生在此过程的什么时候，并且可以通过错误消息大致了解需要解决什么问题。 

![产品/服务推出步骤 6](./media/cloud-partner-portal-offer-go-live-azure-marketplace/errormessage.png)


如果在发布过程中遇到错误，则需纠正这些错误，然后单击“发布”，重新启动发布过程。 纠正错误以后，如果需要重新发布，必须从头开始发布步骤，即从“验证先决条件”开始。

如果解决不了错误，则应提出支持请求，从我们的支持工程师处获取帮助。


![产品/服务推出步骤 7](./media/cloud-partner-portal-offer-go-live-azure-marketplace/getsupport.png)



## <a name="canceling-the-publishing-request"></a>取消发布请求

在开始发布过程以后，你可能需要取消发布请求。 只有在发布请求到达“发布者签收”这一步后，才能取消发布请求。 若要取消，请单击“取消发布”。 发布状态将重置为“步骤 1”。若要再次发布，应单击“发布”并执行状态中显示的步骤。

![产品/服务推出步骤 8](./media/cloud-partner-portal-offer-go-live-azure-marketplace/status5.png)














