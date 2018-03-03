---
title: "Azure 堆栈中的存储帐户 |Microsoft 文档"
description: "了解如何创建 Azure 堆栈存储帐户。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
ms.assetid: e1152110-b756-4c1a-9fa2-73fe3ab0ad8e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/28/2018
ms.author: mabrigg
ms.openlocfilehash: a599d809ba3da8487a6c5d115bf04922a546e6ad
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2018
---
# <a name="storage-accounts-in-azure-stack"></a>Azure 堆栈中的存储帐户
存储帐户包括 Blob 和表服务和存储数据对象的唯一命名空间。 默认情况下，你的帐户中的数据是仅提供给你的存储帐户所有者。

1. 在 Azure 堆栈 POC 计算机，登录`https://adminportal.local.azurestack.external`作为[管理员](azure-stack-connect-azure-stack.md)，然后单击**新建** > **数据 + 存储** >  **存储帐户**。

   ![](media/azure-stack-provision-storage-account/image01.png)
2. 在**创建存储帐户**边栏选项卡，为你的存储帐户键入一个名称。 创建一个新**资源组**，或选择一个现有，然后单击**创建**以创建存储帐户。

   ![](media/azure-stack-provision-storage-account/image02.png)
3. 若要查看新的存储帐户，单击**的所有资源**，然后搜索存储帐户并单击其名称。

    ![](media/azure-stack-provision-storage-account/image03.png)

### <a name="next-steps"></a>接下来的步骤
[使用 Azure Resource Manager 模板](user/azure-stack-arm-templates.md)

[了解有关 Azure 存储帐户](../storage/common/storage-create-storage-account.md)

[下载 Azure 堆栈 Azure 一致的存储验证指南](http://aka.ms/azurestacktp1doc)
