---
title: 测试市场的 VM 产品/服务 | Microsoft Docs
description: 了解如何测试 Azure 市场的 VM 映像。
services: marketplace-publishing
documentationcenter: ''
author: HannibalSII
manager: hascipio
editor: ''
ms.assetid: 7a41c3c6-625c-4478-b804-e124dee89040
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2016
ms.author: hascipio
ms.openlocfilehash: 26f856059b381be91b9cdd1f98a11dc90813c0c5
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39715866"
---
# <a name="test-your-vm-offer-for-the-azure-marketplace-in-staging"></a>在过渡环境中测试 Azure 市场的 VM 产品/服务
过渡表示将 SKU 部署到专用“沙盒”，可在其中测试和验证它的功能，然后再部署到市场。 SKU 出现在过渡环境中，就像客户已对它进行部署一样。 VM 映像必须经过认证才能推送到过渡环境。

## <a name="step-1-push-your-offer-to-staging"></a>步骤 1：将产品/服务推送到过渡环境
1. 在“发布”选项卡上，单击“推送到过渡环境”。
   
    ![绘制](media/marketplace-publishing-vm-image-test-in-staging/vm-image-push-to-staging.png)
2. 如果发布门户通知任何错误，请更正它们。
3. 在“谁可以访问过渡的套餐？”对话框中，输入用于在 [Azure 预览门户](https://portal.azure.com)中预览套餐的 Azure 订阅列表。
   
   > [!NOTE]
   > 如果使用的是虚拟机和解决方案模板，请**不要**将订阅类型云解决方案提供商、DreamSpark 或 Azure 开放许可加入允许列表。
   > 
   > 

    > 如果使用的是虚拟机，当单击按钮“推送到过渡环境”时，后续步骤会在后台执行。 将能够在发布门户的“发布”选项卡下查看每个步骤的进度。 必须定期查看此页面（直到状态显示“已暂存”），了解需要更正的任何失败信息。

    > - 最初过渡请求将发送到验证 vhd 的认证团队。 但是，如果请求仅做了市场营销更改，可以跳过认证步骤。
    > - 认证完成后，产品/服务的复制可以跨所有 Azure 数据中心启动。 通常，需要 24-48 小时的时间才能完成复制，不过可能需要最多一周，具体取决于 vhd 的大小。 但是，如果请求仅做了市场营销更改，可以更快地进行复制。
    > - 复制完成后，产品/服务会在 [Azure 门户](http:/portal.azure.com)中可用。 此时，在发布门户中状态将变为“已暂存”。 仅使用与暂存套餐的订阅相关联的电子邮件 ID，暂存的套餐会在 [Azure 门户](http:/portal.azure.com)中可见。

1. 使用上一步中列出的 Azure 订阅之一，登录到 [Azure 预览门户](https://portal.azure.com)。
2. 找到产品/服务并验证 VM 映像点：
   
   * 请确保市场营销内容正确显示在 Marketplace 中。
   * VM 映像的端到端部署。
     
      ![img-map-portal](media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg)

> [!IMPORTANT]
> 产品/服务将保留在过渡环境中，直到在准备好推送到生产环境时，通过发布门户 [“发布”选项卡 &gt; 单击按钮“请求批推送到生产环境”] 通知 Microsoft 为止。 这是让所有团队成员检查所有内容的理想时间，以便为即将发布的产品/服务做好准备。
> 
> 过渡平台专为发布者在预览模式下测试产品/服务而设计。 我们强烈建议不要将此平台用于商业用途。
> 
> 

## <a name="next-steps"></a>后续步骤
现在产品/服务“已暂存”并且已测试其功能和市场营销内容，可以继续到最后发布阶段步骤 4：[将产品/服务部署到 Marketplace](marketplace-publishing-push-to-production.md)。

## <a name="see-also"></a>另请参阅
* 
  [入门：如何将产品/服务发布到 Azure 市场](marketplace-publishing-getting-started.md)

