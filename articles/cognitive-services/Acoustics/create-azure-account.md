---
title: 项目噪声 Azure 批处理帐户设置
titlesuffix: Azure Cognitive Services
description: 本操作指南介绍了如何设置与项目噪声 Unity 和 Unreal 引擎集成一起使用的 Azure Batch 帐户。
services: cognitive-services
author: ashtat
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: how-to
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 7a7f5f6738b4bc96b6248deb062c7b3f63048148
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/21/2019
ms.locfileid: "58309667"
---
# <a name="project-acoustics-azure-batch-account-setup"></a>项目噪声 Azure 批处理帐户设置
本操作指南介绍了如何设置与项目噪声 Unity 和 Unreal 引擎集成一起使用的 Azure Batch 帐户。

## <a name="get-an-azure-subscription"></a>获取 Azure 订阅
设置 Batch 和存储帐户前需要 [Azure 订阅](https://azure.microsoft.com/free/)。 如果是首次注册，Azure 将提供部分限时可用的资源和 $200 信用额度。

## <a name="create-azure-batch-and-storage-accounts"></a>创建 Azure Batch 和存储帐户
接下来，请按照[以下说明](https://docs.microsoft.com/azure/batch/batch-account-create-portal)设置 Azure Batch 和关联的 Azure 存储帐户。

为 Batch 和存储帐户选择默认选项：
  
  ![新的屏幕截图的 Azure Batch 帐户显示默认设置的选项](media/new-batch-account-create.png)

  ![新的屏幕截图的 Azure 存储帐户，其中显示默认设置的选项](media/batch-storage-account-create.png)

Azure 需要花费几分钟时间来部署帐户。 在门户右上角查看完成通知。
  
  ![屏幕截图显示 Azure 帐户部署通知](media/batch-accounts-deploy-notification.png)

帐户现在应显示在仪表板上。
  
  ![屏幕截图显示 Azure 门户仪表板，其中显示 Batch 和存储帐户](media/azure-portal-dashboard.png)

## <a name="set-up-acoustics-bake-ui-with-azure-credentials"></a>使用 Azure 凭据设置音响效果制作 UI
单击仪表板上的 Batch 帐户链接，然后单击 Batch 帐户页上的“密钥”链接以访问凭据。
  
  ![具有突出显示密钥页的链接的屏幕截图的 Azure Batch 帐户](media/batch-access-keys.png)

  ![使用访问密钥屏幕截图的 Azure Batch 帐户密钥页](media/batch-keys-info.png)

单击页面上的“存储帐户”链接，以访问 Azure 存储帐户凭据。
  
  ![使用访问密钥屏幕截图的 Azure 存储帐户密钥页](media/storage-keys-info.png)

输入中的这些凭据[Unity 制作插件](unity-baking.md)或[Unreal 制作插件](unreal-baking.md)。

## <a name="node-types-and-region-support"></a>节点类型和区域支持
项目噪声需要 Fsv2 和 H 系列计算优化可能不支持在所有 Azure 区域的 Azure VM 节点。 请检查[此表](https://azure.microsoft.com/global-infrastructure/services)，确保为 Batch 帐户选择正确的位置。
![显示按区域的 Azure 虚拟机的屏幕截图](media/azure-regions.png) 

## <a name="upgrading-your-quota"></a>升级配额
创建帐户时，将预配 Azure Batch 帐户，并且其计算核心限制为 20 个。 我们可能需要增加此限制的更快的制作时间，因为可以跨多个节点，您的场景中的探测点的数目可高达并行噪声工作负荷。 可以通过单击 Azure Batch 门户页上的“配额”链接并单击“请求增加配额”来请求增加配额：

![Azure 配额的屏幕截图页](media/azure-quotas.png)

## <a name="next-steps"></a>后续步骤
* 将集成到项目噪声插件你[Unity](unity-integration.md)或[Unreal](unreal-integration.md)项目

