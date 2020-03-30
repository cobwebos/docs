---
title: 使用 Azure 开发人员测试实验室中的共享映像添加 VM |微软文档
description: 了解如何使用 Azure DevTest Labs 中附加的共享映像库中的映像添加虚拟机 （VM）
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "68775586"
---
# <a name="add-a-vm-using-an-image-from-the-attached-shared-image-gallery"></a>使用附加的共享映像库中的图像添加 VM
Azure 开发人员测试实验室允许您将共享图像库附加到实验室，然后将库中的图像用作在实验室中创建的 VM 的基础。 要了解如何将共享图像库附加到实验室，请参阅[配置共享图像库](configure-shared-image-gallery.md)。 本文演示如何使用附加的共享映像库中的图像作为基础将 VM 添加到实验室。 

## <a name="azure-portal"></a>Azure 门户
在本节中，您将了解如何使用 Azure 门户根据附加的共享映像库中的图像将 VM 添加到实验室。 本节不提供有关使用 Azure 门户创建 VM 的详细分步说明。 有关这些详细信息，请参阅[创建 VM - Azure 门户](devtest-lab-add-vm.md)。 它仅突出显示从附加的共享图像库中选择图像并选择要使用的图像版本的步骤。 

将 VM 添加到实验室时，可以从附加的共享映像库中选择图像作为基本映像： 

![为基库选择共享映像](./media/add-vm-use-shared-image/select-shared-image-for-base.png)

然后，在 **"创建实验室资源**"页的 **"高级设置**"选项卡上，您可以选择要使用的图像版本作为基本映像：

![选择图像版本](./media/add-vm-use-shared-image/select-version-shared-image.png)

创建 VM 后，可以切换到使用映像的不同版本。 

## <a name="resource-manager-template"></a>资源管理器模板
如果使用 Azure 资源管理器模板使用共享映像库映像创建虚拟机，请在 **"属性"** 部分中为**共享 ImageId**指定值。 请参阅以下示例： 

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

有关完整的资源管理器模板示例，请参阅使用 GitHub 存储库中的[共享映像库映像示例创建虚拟机](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-sharedimage)。 

## <a name="rest-api"></a>REST API

1. 首先，您需要在共享映像库中获取映像的 ID。 一种方法是使用以下 GET 命令列出附加的共享映像库中的所有图像。 

    ```rest
    GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
    ```
2. 通过将从上一个调用收到的共享映像的 ID 传递给 在`properties.SharedImageId`虚拟机上调用 PUT 方法。

## <a name="next-steps"></a>后续步骤
要了解如何将共享映像库附加到实验室并对其进行配置，请参阅[配置共享映像库](configure-shared-image-gallery.md)。