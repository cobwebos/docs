---
title: 项目噪声 Azure Batch 帐户设置
titlesuffix: Azure Cognitive Services
description: 本操作说明介绍了如何设置 Azure Batch 帐户, 以便与项目噪声 Unity 和 Unreal engine 集成配合使用。
services: cognitive-services
author: ashtat
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: kegodin
ROBOTS: NOINDEX
ms.openlocfilehash: f11dfda62ebb53aba6254f2db4eace7c524141d4
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704883"
---
# <a name="project-acoustics-azure-batch-account-setup"></a>项目噪声 Azure Batch 帐户设置
本操作说明介绍了如何设置 Azure Batch 帐户, 以便与项目噪声 Unity 和 Unreal engine 集成配合使用。

## <a name="get-an-azure-subscription"></a>获取 Azure 订阅
设置 Batch 和存储帐户前需要 [Azure 订阅](https://azure.microsoft.com/free/)。 如果是首次注册，Azure 将提供部分限时可用的资源和 $200 信用额度。

## <a name="create-azure-batch-and-storage-accounts"></a>创建 Azure Batch 和存储帐户
接下来，请按照[以下说明](https://docs.microsoft.com/azure/batch/batch-account-create-portal)设置 Azure Batch 和关联的 Azure 存储帐户。

为 Batch 和存储帐户选择默认选项：
  
  ![显示默认设置 Azure Batch 新帐户选项的屏幕截图](media/new-batch-account-create.png)

  ![显示默认设置的 Azure 存储新帐户选项的屏幕截图](media/batch-storage-account-create.png)

Azure 需要花费几分钟时间来部署帐户。 在门户右上角查看完成通知。
  
  ![Azure 帐户部署通知的屏幕截图](media/batch-accounts-deploy-notification.png)

帐户现在应显示在仪表板上。
  
  ![显示 Batch 和存储帐户的 Azure 门户面板的屏幕截图](media/azure-portal-dashboard.png)

## <a name="set-up-acoustics-bake-ui-with-azure-credentials"></a>使用 Azure 凭据设置音响效果制作 UI
单击仪表板上的 Batch 帐户链接，然后单击 Batch 帐户页上的“密钥”链接以访问凭据。
  
  ![突出显示了 "链接到密钥" 页 Azure Batch 帐户的屏幕截图](media/batch-access-keys.png)

  ![包含访问密钥的 Azure Batch 帐户密钥 "页的屏幕截图](media/batch-keys-info.png)

单击页面上的“存储帐户”链接，以访问 Azure 存储帐户凭据。
  
  ![包含访问密钥的 Azure 存储帐户密钥页的屏幕截图](media/storage-keys-info.png)

在[Unity 制作插件](unity-baking.md)或[Unreal 制作插件](unreal-baking.md)中输入这些凭据。

## <a name="node-types-and-region-support"></a>节点类型和区域支持
项目噪声需要在所有 Azure 区域中都不支持 Fsv2 和 H 系列计算优化的 Azure VM 节点。 请检查[此表](https://azure.microsoft.com/global-infrastructure/services)，确保为 Batch 帐户选择正确的位置。
![按区域显示 Azure 虚拟机的屏幕截图](media/azure-regions.png) 

## <a name="upgrading-your-quota"></a>升级配额
创建帐户时，将预配 Azure Batch 帐户，并且其计算核心限制为 20 个。 我们可能想要增加此限制, 以加快制作的速度, 因为你可以在多个节点之间并行化你的噪声工作负荷, 直至场景中的探测点数目。 可以通过单击 Azure Batch 门户页上的“配额”链接并单击“请求增加配额”来请求增加配额：

![Azure 配额页的屏幕截图](media/azure-quotas.png)

## <a name="next-steps"></a>后续步骤
* 将项目噪声插件集成到[Unity](unity-integration.md)或[Unreal](unreal-integration.md)项目中

