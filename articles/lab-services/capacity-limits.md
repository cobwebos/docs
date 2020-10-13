---
title: Azure 实验室服务中的容量限制
description: 了解 Azure 实验室服务)  (虚拟机限制的容量限制。
ms.topic: conceptual
ms.date: 06/26/2020
ms.openlocfilehash: 92bdc714d70b3d73ca2cbc76b1f5dc5366582cbd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85444091"
---
# <a name="capacity-limits-in-azure-lab-services"></a>Azure 实验室服务中的容量限制
Azure 实验室服务对 Azure 订阅具有默认容量限制，以遵守 Azure 计算配额限制并减轻欺诈行为。 所有 Azure 订阅都具有初始容量限制，该限制可能因订阅类型、标准计算核心数和 Azure 实验室服务内可用的 GPU 内核而有所不同。 它限制你可以在实验室内创建多少个虚拟机，然后才需要请求增加限制。  

如果你接近或达到了订阅的虚拟机核心限制，则当你尝试执行创建其他虚拟机的操作时，你将看到来自 Azure 实验室服务的消息。 例如： 

- 创建实验室
- 发布实验室
- 调整实验室容量以便将更多的虚拟机添加到现有实验室

如果已达到内核限制，则这些操作也可能会被禁用。 

![核心限制-警告消息](./media/capacity-limits/warning-message.png)

## <a name="subscriptions-with-default-limit-of-zero-cores"></a>默认限制为零核心的订阅
某些极少使用的常见订阅类型的默认限制为0个标准核心，0个 GPU 核心。 如果使用其中一种订阅类型，则创建实验室帐户的管理员需要先请求限制增加，然后才能使用 Azure 实验室服务。 

管理员可以按照以下步骤请求限制增加：  

1.  在订阅中 [创建一个实验室帐户](tutorial-setup-lab-account.md)。
2.  在实验室帐户的 " **概述** " 页上，单击顶部的 " **请求限制增加** " 按钮。 
3.  按照窗体中的步骤提交支持请求以增加限制。

## <a name="request-a-limit-increase"></a>请求增加限制
如果达到内核限制，可以请求限制增加以继续使用 Azure 实验室服务。 请求过程是一个检查点，可确保订阅不涉及欺诈行为或意外的大规模部署的情况。

有关 Azure 实验室服务门户中的虚拟机内核限制的消息包括用于请求限制增加的链接。 该链接将打开新的浏览器选项卡，你可以在其中创建新的支持请求。 系统将自动填写问题类型、订阅和配额类型信息，如下图所示： 

![新建支持请求](./media/capacity-limits/new-support-request.png)


然后，系统将提示您提供有关限制增加的详细信息。 在 " **描述** " 字段中，提供以下详细信息：

- 你正在尝试执行的操作 (例如，创建实验室来讲授计算机科学类，运行 hackathon 等。 ) 
- 此实验室使用的虚拟机大小
- 需要的虚拟机数

提交支持请求后，我们将查看请求。 如果需要，我们将与你联系以获取更多详细信息。 

## <a name="next-steps"></a>后续步骤
请参阅以下文章：
- [常见问题解答](classroom-labs-faq.md)。