---
title: "Azure 堆栈中的存储帐户 |Microsoft 文档"
description: "了解如何创建 Azure 堆栈存储帐户。"
services: azure-stack
documentationcenter: 
author: vhorne
manager: byronr
editor: 
ms.assetid: e1152110-b756-4c1a-9fa2-73fe3ab0ad8e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 3/1/2017
ms.author: victorh
ms.openlocfilehash: 41c9ee37c43d4ad41c51ea2ed023d3b47d460dd1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="storage-accounts-in-azure-stack"></a>Azure 堆栈中的存储帐户
存储帐户包括 Blob 和表服务和存储数据对象的唯一命名空间。 默认情况下，你的帐户中的数据是仅提供给你的存储帐户所有者。

1. 在 Azure 堆栈 POC 计算机，登录`https://adminportal.local.azurestack.external`作为[管理员](azure-stack-connect-azure-stack.md)，然后单击**新建** > **数据 + 存储** >  **存储帐户**。

   ![](media/azure-stack-provision-storage-account/image01.png)
2. 在**创建存储帐户**边栏选项卡，为你的存储帐户键入一个名称。 创建一个新**资源组**，或选择一个现有，然后单击**创建**以创建存储帐户。

   ![](media/azure-stack-provision-storage-account/image02.png)
3. 若要查看新的存储帐户，单击**的所有资源**，然后搜索存储帐户并单击其名称。

    ![](media/azure-stack-provision-storage-account/image03.png)

### <a name="next-steps"></a>后续步骤

            [使用 Azure 资源管理器模板](user/azure-stack-arm-templates.md)

[了解有关 Azure 存储帐户](../storage/common/storage-create-storage-account.md)

[下载 Azure 堆栈 Azure 一致的存储验证指南](http://aka.ms/azurestacktp1doc)
