---
title: 我们将虚拟机 （VM） 映像移动到 Azure 应用商店中的托管磁盘存储
description: 为了为新的市场特性和功能提供更快、更可靠的存储和支持，我们将市场 VM 映像迁移到托管磁盘存储。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: 17b418ab82cc88b4368efe247b06c0c8bbb27453
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285106"
---
# <a name="were-moving-virtual-machine-vm-images-on-azure-marketplace-to-managed-disk-storage"></a>我们将 Azure 应用商店上的虚拟机 （VM） 映像移动到托管磁盘存储

为了为新的市场特性和功能提供更快、更可靠的存储和支持，我们将市场 VM 映像迁移到托管磁盘存储。

从 2020 年 1 月 2 日开始，我们将分阶段将 VM 映像移动到托管磁盘存储。 在第一阶段，我们只会在前 90 天内移动没有新部署或运行 VM 的图像。 在移动任何图像之前，我们将发送电子邮件，让发布者知道哪些图像将移动以及何时移动。

发布者或使用者不需要执行任何操作，用户也不会受到影响。 市场优惠将保持可用状态，客户仍能够在移动期间和之后从这些映像部署托管 VM。

如果您有任何问题，[请联系我们](https://support.microsoft.com/supportforbusiness/productselection?sapId=48734891-ee9a-5d77-bf29-82bf8d8111ff)。

## <a name="faqs"></a>常见问题解答

### <a name="would-the-users-of-my-vm-images-experience-an-outage"></a>VM 映像的用户是否会遇到中断？

VM 映像的用户不会遇到中断。 

在第一阶段，我们将仅移动没有正在运行的 VM 的 VM 映像。 由于这些图像没有用户，因此不会产生任何影响。 对于后续阶段，对用户也没有任何影响。

### <a name="how-long-does-it-take-for-the-process-to-complete"></a>完成此过程需要多长时间？

完成迁移可能需要长达 24 小时。

### <a name="do-i-need-to-take-any-action"></a>我需要采取任何行动吗？

不是。 发布者或消费者无需采取任何操作。

### <a name="do-i-have-to-update-my-system-to-call-the-cloud-portal-apis-in-a-different-way-after-they-are-moved-to-managed-disk-storage"></a>在云门户 API 移动到托管磁盘存储后，我是否必须更新系统以不同方式调用云门户 API？

不是。 您现有的 API 调用将继续工作。

### <a name="would-all-my-vm-images-be-moved-to-managed-disk-at-the-same-time"></a>我的所有 VM 映像是否会同时移动到托管磁盘？

我们将在同一天移动您的所有 VM 映像。 一旦他们被移动，我们将通知您。

### <a name="can-i-request-to-schedule-the-move-of-my-vm-images-to-a-later-time"></a>我可以请求将 VM 映像的移动安排到以后吗？

我们建议在计划日期移动图像。 但是，如果您有任何疑问，请联系我们重新安排搬迁时间。

### <a name="can-i-publish-updates-to-my-vm-images-during-the-move"></a>在移动过程中，是否可以将更新发布到 VM 映像？

在移动过程中无法对 VM 映像进行更新。

### <a name="will-the-publishing-process-change-after-my-vm-image-is-moved-to-managed-disk"></a>将 VM 映像移动到托管磁盘后，发布过程是否会更改？

不，发布过程将保持不变。 

### <a name="can-the-publisher-move-their-offers-to-managed-disk"></a>发布者是否可以将其产品/服务移动到托管磁盘？

否，发布者无法将其产品/服务移动到托管磁盘。 他们必须等待，他们的图像将自动移动。 我们将在进行任何更改之前向发布者发送通知。
