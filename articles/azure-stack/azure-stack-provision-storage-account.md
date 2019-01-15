---
title: Azure Stack 中的存储帐户 | Microsoft Docs
description: 了解如何创建 Azure Stack 存储帐户。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 1/14/2019
ms.author: mabrigg
ms.openlocfilehash: 66a6fa88956db40ced9ee3fd13a0f74585c528fc
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2019
ms.locfileid: "54303201"
---
# <a name="storage-accounts-in-azure-stack"></a>Azure Stack 中的存储帐户
存储帐户包含 Blob 和表服务，以及存储数据对象的唯一命名空间。 默认情况下，只有你，即存储帐户所有者，才能使用帐户中的数据。

1. 在 Azure Stack POC 计算机上，以[管理员](azure-stack-connect-azure-stack.md)身份登录到 `https://adminportal.local.azurestack.external`，然后单击“+ 创建资源” > “数据 + 存储” > “存储帐户”。

   ![](media/azure-stack-provision-storage-account/image01.png)
2. 在“创建存储帐户”边栏选项卡中，键入存储帐户的名称。 创建新**资源组**或选择现有的资源组，然后单击“创建”以创建存储帐户。

   ![](media/azure-stack-provision-storage-account/image02.png)
3. 若要查看新存储帐户，请单击“所有资源”，然后搜索该存储帐户并单击其名称。

    ![](media/azure-stack-provision-storage-account/image03.png)

### <a name="next-steps"></a>后续步骤
[使用 Azure 资源管理器模板](user/azure-stack-arm-templates.md)

[了解 Azure 存储帐户](../storage/common/storage-create-storage-account.md)

[下载与 Azure 一致的 Azure Stack 存储验证指南](https://aka.ms/azurestacktp1doc)
