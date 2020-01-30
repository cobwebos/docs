---
title: 我们正在将虚拟机（VM）映像迁移到 Azure Marketplace 中的托管磁盘存储
description: 若要为新的 marketplace 特性和功能提供更快、更可靠的存储和支持，我们要将 marketplace VM 映像迁移到托管磁盘存储。
author: MaggiePucciEvans
manager: evansma
ms.author: evansma
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: d85f6ba9d50b7d30930322ca34cb15edb30e1472
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2020
ms.locfileid: "76774998"
---
# <a name="were-moving-virtual-machine-vm-images-on-azure-marketplace-to-managed-disk-storage"></a>我们正在将 Azure Marketplace 中的虚拟机（VM）映像迁移到托管磁盘存储

若要为新的 marketplace 特性和功能提供更快、更可靠的存储和支持，我们要将 marketplace VM 映像迁移到托管磁盘存储。

从2020年1月2日开始，我们将 VM 映像迁移到托管磁盘存储。 在第一阶段，我们将仅在以前的90天内移动没有新部署或正在运行的 Vm 的映像。 在移动任何图像之前，我们将发送一封电子邮件，让发布者知道将移动哪些图像以及何时移动图像。

发布者或使用者不需要执行任何操作，用户也不会受到影响。 Marketplace 产品/服务将保持可用，并且在移动过程中和之后，客户仍将能够从这些映像部署托管 Vm。

如果你有任何疑问，请[联系我们](https://support.microsoft.com/supportforbusiness/productselection?sapId=48734891-ee9a-5d77-bf29-82bf8d8111ff)。

## <a name="faqs"></a>常见问题

### <a name="would-the-users-of-my-vm-images-experience-an-outage"></a>我的 VM 映像的用户是否会遇到中断？

VM 映像的用户将不会遇到中断。 

在第一个阶段中，只会移动没有运行的 Vm 的 VM 映像。 由于这些映像没有用户，因此不会有任何影响。 对于后续阶段，用户不会有任何影响。

### <a name="how-long-does-it-take-for-the-process-to-complete"></a>完成此过程需要多长时间？

完成迁移可能需要长达24小时。

### <a name="do-i-need-to-take-any-action"></a>是否需要执行任何操作？

不。 发布者或使用者不需要执行任何操作。

### <a name="do-i-have-to-update-my-system-to-call-the-cloud-portal-apis-in-a-different-way-after-they-are-moved-to-managed-disk-storage"></a>是否必须将系统更新为以不同方式调用云门户 Api，然后再将其移动到托管磁盘存储？

不。 你的现有 API 调用将继续工作。

### <a name="would-all-my-vm-images-be-moved-to-managed-disk-at-the-same-time"></a>是否所有 VM 映像都同时移动到托管磁盘？

我们将在同一天移动所有 VM 映像。 移动这些用户后，我们会通知你。

### <a name="can-i-request-to-schedule-the-move-of-my-vm-images-to-a-later-time"></a>我能否请求将我的 VM 映像迁移到以后一段时间？

建议在计划日期移动图像。 但是，如果你有疑问，请联系我们来重新安排搬迁。

### <a name="can-i-publish-updates-to-my-vm-images-during-the-move"></a>能否在移动过程中将更新发布到我的 VM 映像？

移动期间无法对 VM 映像进行更新。

### <a name="will-the-publishing-process-change-after-my-vm-image-is-moved-to-managed-disk"></a>在将 VM 映像迁移到托管磁盘后，发布过程是否会更改？

不，发布过程将保持不变。 

## <a name="next-steps"></a>后续步骤

请访问[虚拟机产品/服务发布者指南](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines)页。
