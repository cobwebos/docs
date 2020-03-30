---
title: 在 Azure 开发人员测试实验室中配置共享映像库 |微软文档
description: 了解如何在 Azure 开发人员测试实验室中配置共享映像库
services: devtest-lab
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
ms.date: 09/05/2019
ms.author: spelluru
ms.openlocfilehash: 9593d60f76802cd515ca85616bce028cf3aa0d49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589311"
---
# <a name="configure-a-shared-image-gallery-in-azure-devtest-labs"></a>在 Azure 开发测试实验室中配置共享映像库
DevTest 实验室现在支持[共享图像库](../virtual-machines/windows/shared-image-galleries.md)功能。 它使实验室用户能够在创建实验室资源的同时从共享位置访问图像。 它还可以帮助您围绕自定义管理的 VM 映像构建结构和组织。 共享图像库功能支持：

- 映像的托管全局复制
- 图像的版本和分组，便于管理
- 在支持可用性区域的区域区域中，通过区域冗余存储 （ZRS） 帐户使映像高度可用。 ZRS 提供更好的复原能力来应对区域性的故障。
- 使用基于角色的访问控制 （RBAC） 在订阅之间，甚至租户之间共享。

有关详细信息，请参阅[共享图像库文档](../virtual-machines/windows/shared-image-galleries.md)。 
 
如果你有大量的托管映像需要维护，并想要使其在整个公司中可用，可将共享映像库用作存储库，以便更轻松地更新和共享映像。 作为实验室所有者，您可以将现有的共享图像库附加到实验室。 连接此库后，实验室用户可以从这些最新图像创建计算机。 此功能的一个主要好处是，DevTest Labs 现在可以利用跨实验室、跨订阅和区域共享图像的优势。 

> [!NOTE]
> 要了解与共享图像库服务关联的成本，请参阅[共享图像库的计费](../virtual-machines/windows/shared-image-galleries.md#billing)。

## <a name="considerations"></a>注意事项
- 一次只能将一个共享图像库附加到实验室。 如果要附加另一个库，则需要分离现有库并附加另一个库。 
- DevTest Labs 目前仅支持共享图像库通用图像。
- DevTest 实验室目前不支持通过实验室将图像上载到库。 
- 使用共享映像库映像创建虚拟机时，DevTest Labs 始终使用此映像的最新发布版本。 但是，如果映像有多个版本，用户可以选择在虚拟机创建期间使用"高级设置"选项卡从早期版本创建计算机。  
- 尽管 DevTest Labs 会自动做出最佳尝试，以确保共享图像库将图像复制到实验室存在的区域，但并不总是可能的。 为了避免用户在这些映像中创建 VM 时出现问题，请确保图像已复制到实验室的区域。

## <a name="use-azure-portal"></a>使用 Azure 门户
1. 登录到 Azure[门户](https://portal.azure.com)。
1. 选择左侧导航菜单上**的所有服务**。
1. 从列表中选择**开发人员测试实验室**。
1. 从实验室列表中，选择您的**实验室**。
1. 在左侧菜单的 **"设置"** 部分中选择 **"配置"和"策略**"。
1. 在左侧菜单的 **"虚拟机"底座**下选择**共享图像库**。

    ![共享图像库菜单](./media/configure-shared-image-gallery/shared-image-galleries-menu.png)
1. 单击"**附加"** 按钮并在下拉列表中选择库，将现有共享图像库附加到实验室。

    ![附加](./media/configure-shared-image-gallery/attach-options.png)
1. 转到附加的库并配置库以**启用或禁用**共享映像以进行 VM 创建。 从列表中选择一个映像库来配置它。 

    默认情况下，**允许将所有映像用作虚拟机库**设置为 **"是**"。 这意味着在创建新的实验室 VM 时，实验室用户可以使用附加的共享映像库中的所有映像。 如果需要限制对某些映像的访问，请更改 **"允许将所有映像用作虚拟机库**"更改为 **"否**"，然后选择创建 VM 时要允许的图像，然后选择"**保存**"按钮。

    ![启用或禁用](./media/configure-shared-image-gallery/enable-disable.png)
1. 然后，实验室用户可以通过单击 **+Add**并在**选择基**页上查找图像来使用启用的图像创建虚拟机。

    ![实验室用户](./media/configure-shared-image-gallery/lab-users.png)
## <a name="use-azure-resource-manager-template"></a>使用 Azure 资源管理器模板

### <a name="attach-a-shared-image-gallery-to-your-lab"></a>将共享图像库附加到实验室
如果使用 Azure 资源管理器模板将共享映像库附加到实验室，则需要将其添加到资源管理器模板的资源部分下，如以下示例所示：

```json
"resources": [
{
    "apiVersion": "2018-10-15-preview",
    "type": "Microsoft.DevTestLab/labs",
    "name": "mylab",
    "location": "eastus",
    "resources": [
    {
        "apiVersion":"2018-10-15-preview",
        "name":"myGallery",
        "type":"sharedGalleries",
        "properties": {
            "galleryId":"/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/mySharedGalleryRg/providers/Microsoft.Compute/galleries/mySharedGallery",
            "allowAllImages": "Enabled"
        }
    }
    ]
}
```

有关完整的资源管理器模板示例，请参阅我们的公共 GitHub 存储库中的这些资源管理器模板示例：[在创建实验室时配置共享映像库](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-lab-shared-gallery-configured)。

## <a name="use-api"></a>使用 API

### <a name="shared-image-galleries---create-or-update"></a>共享图像库 - 创建或更新

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}?api-version= 2018-10-15-preview
Body: 
{
    "properties":{
        "galleryId": "[Shared Image Gallery resource Id]",
        "allowAllImages": "Enabled"
    }
}

```

### <a name="shared-image-galleries-images---list"></a>共享图像库图像 - 列表 

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
```




## <a name="next-steps"></a>后续步骤
请参阅以下有关使用附加的共享映像库中的映像创建 VM的文章：[使用库中的共享映像创建 VM](add-vm-use-shared-image.md)
