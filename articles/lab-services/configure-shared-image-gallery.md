---
title: 在 Azure 开发测试实验室中配置共享映像库 |Microsoft Docs
description: 了解如何在 Azure 开发测试实验室中配置共享映像库
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
ms.openlocfilehash: 7591f22286f9ac451a15dd926adab0212adb190e
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691287"
---
# <a name="configure-a-shared-image-gallery-in-azure-devtest-labs"></a>在 Azure 开发测试实验室中配置共享映像库
开发测试实验室现在支持[共享映像库](../virtual-machines/windows/shared-image-galleries.md)功能。 它允许实验室用户在创建实验室资源的同时访问共享位置中的图像。 它还可帮助你围绕自定义托管 VM 映像构建结构和组织。 共享映像库功能支持：

- 托管的映像全局复制
- 对图像进行版本控制和分组以便于管理
- 使映像在支持可用性区域的区域中使用区域冗余存储（ZRS）帐户高度可用。 ZRS 提供更好的复原能力来应对区域性的故障。
- 使用基于角色的访问控制（RBAC），在多个订阅之间共享，甚至在租户之间共享。

有关详细信息，请参阅[共享映像库文档](../virtual-machines/windows/shared-image-galleries.md)。 
 
如果你有大量的托管映像需要维护，并想要使其在整个公司中可用，可将共享映像库用作存储库，以便更轻松地更新和共享映像。 作为实验室所有者，你可以将现有的共享映像库附加到实验室。 附加此库后，实验室用户可以从这些最新映像创建计算机。 此功能的一个主要优点是，开发测试实验室现在可以利用跨实验室、跨订阅和跨区域共享映像的优势。 

> [!NOTE]
> 若要了解与共享映像库服务相关的成本，请参阅[共享图像库的帐单](../virtual-machines/windows/shared-image-galleries.md#billing)。

## <a name="considerations"></a>注意事项
- 一次只能将一个共享映像库附加到实验室。 如果要附加其他库，则需要分离现有库，并附加另一个库。 
- 开发测试实验室目前仅支持共享映像库通用化映像。
- 开发测试实验室当前不支持通过实验室向库上传图像。 
- 使用共享映像库映像创建虚拟机时，开发测试实验室始终使用此映像的最新发布版本。 但是，如果映像有多个版本，则用户可以在虚拟机创建期间转到 "高级设置" 选项卡，选择从早期版本创建计算机。  
- 尽管开发测试实验室会自动尽力确保共享图像库将图像复制到实验室所在的区域，但并不总是这样。 若要避免用户在从这些映像中创建 Vm 时遇到问题，请确保已将映像复制到实验室的区域。 "

## <a name="use-azure-portal"></a>使用 Azure 门户
1. 登录 [Azure 门户](https://portal.azure.com)。
1. 选择左侧导航菜单中的 "**所有服务**"。
1. 从列表中选择 "**开发测试实验室**"。
1. 从实验室列表中，选择**实验室**。
1. 在左侧菜单的 "**设置**" 部分中选择 "**配置和策略**"。
1. 在左侧菜单中的 "**虚拟机**" 下选择**共享映像库**。

    ![共享图像库菜单](./media/configure-shared-image-gallery/shared-image-galleries-menu.png)
1. 单击 "**附加**" 按钮，然后在下拉列表中选择库，将现有的共享映像库附加到实验室。

    ![附加](./media/configure-shared-image-gallery/attach-options.png)
1. 中转到连接的库，并配置库，以便为 VM 创建**启用或禁用**共享映像。 从列表中选择一个映像库以对其进行配置。 

    默认情况下，"**允许将所有映像用作虚拟机库**" 设置为 **"是"**。 这意味着在创建新的实验室 VM 时，会向实验室用户提供附加的共享映像库中可用的所有映像。 如果需要限制对某些映像的访问，请将 "**允许所有映像全部使用为虚拟机库**" 更改为 "**否**"，并选择要在创建 vm 时允许的映像，然后选择 "**保存**" 按钮。

    ![启用或禁用](./media/configure-shared-image-gallery/enable-disable.png)
1. 然后，实验室用户可以使用启用的映像创建虚拟机，方法是单击 " **+ 添加**"，然后在 "**选择基本**页" 中查找映像。

    ![实验室用户](./media/configure-shared-image-gallery/lab-users.png)
## <a name="use-azure-resource-manager-template"></a>使用 Azure 资源管理器模板

### <a name="attach-a-shared-image-gallery-to-your-lab"></a>将共享映像库附加到实验室
如果使用 Azure 资源管理器模板将共享映像库附加到实验室，则需要将其添加到资源管理器模板的 resources 部分下，如以下示例中所示：

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

有关完整的资源管理器模板示例，请参阅公共 GitHub 存储库中的以下资源管理器模板示例：[创建实验室时配置共享映像库](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-lab-shared-gallery-configured)。

## <a name="use-rest-api"></a>使用 REST API

### <a name="get-a-list-of-labs"></a>获取实验室列表 

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs?api-version= 2018-10-15-preview
```

### <a name="get-the-list-of-shared-image-galleries-associated-with-a-lab"></a>获取与实验室关联的共享映像库的列表

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries?api-version= 2018-10-15-preview
   ```

### <a name="create-or-update-shared-image-gallery"></a>创建或更新共享映像库

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

### <a name="list-images-in-a-shared-image-gallery"></a>列出共享图像库中的图像

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
```



## <a name="next-steps"></a>后续步骤
请参阅以下文章，了解如何使用连接的共享映像库中的映像创建 VM：[使用库中的共享映像创建 vm](add-vm-use-shared-image.md)
