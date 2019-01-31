---
title: 设置 Project Acoustics 的 Azure 帐户
titlesuffix: Azure Cognitive Services
description: 有关如何设置使用音响效果所需的 Azure Batch 和存储帐户，请遵循以下指南。
services: cognitive-services
author: ashtat
manager: cgronlun
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: 638ea3e707352a95cb1fb407add365a83d11faa7
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55148636"
---
# <a name="create-an-azure-batch-account"></a>创建 Azure Batch 帐户
有关如何设置使用音响效果所需的 Azure Batch 和存储帐户，请遵循以下指南。 有关作为 Project Acoustics 的一部分开发的 Unity 插件的信息，请参阅[什么是音响效果](what-is-acoustics.md)。 有关如何将音响效果合并到 Unity 项目中的信息，请参阅[入门](getting-started.md)。  

## <a name="get-an-azure-subscription"></a>获取 Azure 订阅
设置 Batch 和存储帐户前需要 [Azure 订阅](https://azure.microsoft.com/free/)。 如果是首次注册，Azure 将提供部分限时可用的资源和 $200 信用额度。

## <a name="create-azure-batch-and-storage-accounts"></a>创建 Azure Batch 和存储帐户
接下来，请按照[以下说明](https://docs.microsoft.com/azure/batch/batch-account-create-portal)设置 Azure Batch 和关联的 Azure 存储帐户。

为 Batch 和存储帐户选择默认选项：
  
  ![新建 Batch 帐户](media/NewBatchAccountCreate.png)

  ![新建存储帐户](media/BatchStorageAccountCreate.png)

Azure 需要花费几分钟时间来部署帐户。 在门户右上角查看完成通知。
  
  ![帐户已部署](media/BatchAccountsDeployNotification.png)

帐户现在应显示在仪表板上。
  
  ![门户仪表板](media/AzurePortalDashboard.png)

## <a name="set-up-acoustics-bake-ui-with-azure-credentials"></a>使用 Azure 凭据设置音响效果制作 UI
单击仪表板上的 Batch 帐户链接，然后单击 Batch 帐户页上的“密钥”链接以访问凭据。
  
  ![Batch 密钥链接](media/BatchAccessKeys.png)

  ![Batch 帐户凭据](media/BatchKeysInfo.png)

单击页面上的“存储帐户”链接，以访问 Azure 存储帐户凭据。
  
  ![存储帐户凭据](media/StorageKeysInfo.png)

在“制作”选项卡上输入这些凭据，如[制作 UI 演练](bake-ui-walkthrough.md)中所述。

## <a name="node-types-and-region-support"></a>节点类型和区域支持
Project Acoustics 需要 F 和 H 系列的计算优化 Azure VM 节点，可能并非所有 Azure 区域都支持这些节点。 请检查[此表](https://azure.microsoft.com/global-infrastructure/services)，确保为 Batch 帐户选择正确的位置。 此时，美国东部、美国中北部、美国中南部、美国西部、美国西部 2、北欧、西欧和日本西部均支持 H 系列的虚拟机。

## <a name="upgrading-your-quota"></a>升级配额
创建帐户时，将预配 Azure Batch 帐户，并且其计算核心限制为 20 个。 你可能想要增加此限制，以实现更快的制作时间，因为你可在众多节点（多达场景中的探测点数）中并行运行音响效果工作负荷。 可以通过单击 Azure Batch 门户页上的“配额”链接并单击“请求增加配额”来请求增加配额：

![增加 Azure 配额](media/azurequotas.png)

## <a name="next-steps"></a>后续步骤
* [将音响效果集成到 Unity 项目](getting-started.md)入门
* 了解[示例场景](sample-walkthrough.md)

