---
title: 使用 Azure 开发测试实验室中的共享映像添加 VM |Microsoft Docs
description: 了解如何使用 Azure 开发测试实验室中附加的共享映像库中的映像添加虚拟机 (VM)
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2019
ms.author: spelluru
ms.openlocfilehash: c13b2c3d00a0ab0af0f7785feae645d907ffdaa3
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2019
ms.locfileid: "68775586"
---
# <a name="add-a-vm-using-an-image-from-the-attached-shared-image-gallery"></a>使用连接的共享映像库中的映像添加 VM
Azure 开发测试实验室允许将共享映像库附加到实验室, 然后使用库中的映像作为实验室中创建的 Vm 的基础。 若要了解如何将共享映像库附加到实验室, 请参阅[配置共享映像库](configure-shared-image-gallery.md)。 本文介绍如何使用附加的共享映像库中的映像作为基准, 将 VM 添加到实验室。 

## <a name="azure-portal"></a>Azure 门户
在本部分中, 将了解如何使用 Azure 门户基于连接的共享映像库中的映像将 VM 添加到实验室。 本部分不提供使用 Azure 门户创建 VM 的详细分步说明。 有关详细信息, 请参阅[创建 VM-Azure 门户](devtest-lab-add-vm.md)。 它仅重点介绍从附加的共享映像库中选择映像的步骤, 并选择要使用的映像版本。 

向实验室添加 VM 时, 可以从附加的共享映像库中选择一个映像作为基本映像: 

![为基选择共享映像](./media/add-vm-use-shared-image/select-shared-image-for-base.png)

然后, 在 "**创建实验室资源**" 页的 "**高级设置**" 选项卡上, 可以选择要用作基础映像的映像版本:

![选择映像版本](./media/add-vm-use-shared-image/select-version-shared-image.png)

创建 VM 后, 可以切换到使用不同版本的映像。 

## <a name="resource-manager-template"></a>资源管理器模板
如果使用 Azure 资源管理器模板创建使用共享映像库映像的虚拟机, 请在 "**属性**" 部分中指定**sharedImageId**的值。 请参阅以下示例： 

```json
"resources": [
{
    ...
    "properties": {
         "sharedImageId": "/subscriptions/111111111-1111-1111-1111-111111111111/resourcegroups/mydtlrg/providers/microsoft.devtestlab/labs/mydtllab/sharedgalleries/spsig/sharedimages/myimagefromgallery",
        "sharedImageVersion": "1.0.1",
        ...
    }
}
],
```

有关完整的资源管理器模板示例, 请参阅 GitHub 存储库中的[使用共享映像库映像创建虚拟机图像](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-sharedimage)示例。 

## <a name="rest-api"></a>REST API

1. 首先, 需要获取共享映像库中的映像的 ID。 一种方法是使用以下 GET 命令列出附加的共享映像库中的所有映像。 

    ```rest
    GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
    ```
2. 通过将从上一次调用时收到的共享映像的 ID 传递给`properties.SharedImageId`, 在虚拟机上调用 PUT 方法。

## <a name="next-steps"></a>后续步骤
若要了解如何将共享映像库附加到实验室并对其进行配置, 请参阅[配置共享映像库](configure-shared-image-gallery.md)。