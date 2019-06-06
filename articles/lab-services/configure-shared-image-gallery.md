---
title: 在 Azure 开发测试实验室中配置共享的映像库 |Microsoft Docs
description: 了解如何在 Azure 开发测试实验室中配置共享的映像库
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
ms.date: 05/30/2019
ms.author: spelluru
ms.openlocfilehash: fba969b70ae052c928f33888d3c93eb7683ae9f7
ms.sourcegitcommit: ec7b0bf593645c0d1ef401a3350f162e02c7e9b8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2019
ms.locfileid: "66455780"
---
# <a name="configure-a-shared-image-gallery-in-azure-devtest-labs"></a>在 Azure 开发测试实验室中配置共享映像库
开发测试实验室现在支持[共享映像库](/virtual-machines/windows/shared-image-galleries.md)功能。 它允许实验室用户在创建实验室资源的同时从共享位置访问映像。 它还可以帮助围绕自定义托管 VM 映像生成结构和组织。 共享映像库功能支持：

- 映像的托管全局复制
- 对映像进行版本控制和分组以简化管理
- 在支持可用性区域的区域中通过区域冗余存储 (ZRS) 帐户使映像具有高可用性。 ZRS 可以更好地恢复映像，防止区域性故障。
- 使用基于角色的访问控制 (RBAC) 跨订阅甚至租户共享映像。

有关详细信息，请参阅[共享映像库文档](../virtual-machines/windows/shared-image-galleries.md)。 
 
如果你有大量的托管映像需要维护，并想要使其在整个公司中可用，可将共享映像库用作存储库，以便更轻松地更新和共享映像。 实验室所有者可以将现有的共享映像库附加到实验室。 附加此库后，实验室用户可以依据这些最新映像创建虚拟机。 此功能的一个主要优点是开发测试实验室现在可以利用跨实验室、跨订阅和跨区域共享的映像。 

## <a name="considerations"></a>注意事项
- 一次，您可以仅将一个共享的映像库附加到实验室。 如果你想要将附加另一个库，你将需要分离现有和附加另一个。 
- 开发测试实验室目前不支持通过实验室库上传图像。 
- 在创建时使用的共享的映像库映像的虚拟机，开发测试实验室始终使用此图像的已发布的版本。
- 尽管开发测试实验室自动可尽量尝试确保共享的映像库将映像复制到实验室所在的区域，但它并不总是可能。 若要避免用户遇到问题，从这些映像创建 Vm，请确保映像已复制到实验室的区域。"

## <a name="use-azure-portal"></a>使用 Azure 门户
1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 选择**所有服务**在左侧导航菜单中。
1. 选择**开发测试实验室**从列表中。
1. 从实验室列表中，选择你**实验室**。
1. 选择**配置和策略**中**设置**在左侧菜单中的部分。
1. 选择**共享图像库**下**虚拟机基础**在左侧菜单中。

    ![共享的图像库菜单](./media/configure-shared-image-gallery/shared-image-galleries-menu.png)
1. 将现有的共享的映像库附加到你的实验室，通过单击**附加**按钮，然后在下拉列表中选择您的库。

    ![附加](./media/configure-shared-image-gallery/attach-options.png)
1. 转到附加的库和配置到图库**启用或禁用**共享的映像创建 VM。

    ![启用或禁用](./media/configure-shared-image-gallery/enable-disable.png)
1. 实验室用户然后可以创建使用通过单击启用的映像的虚拟机 **+ 添加**并查找中的图像**选择基准**页。

    ![实验室用户](./media/configure-shared-image-gallery/lab-users.png)
## <a name="use-azure-resource-manager-template"></a>使用 Azure 资源管理器模板

### <a name="attach-a-shared-image-gallery-to-your-lab"></a>将共享的映像库附加到你的实验室
如果使用 Azure 资源管理器模板以将共享的映像库附加到你的实验室，需要将其添加资源管理器模板的资源部分下，在下面的示例所示：

```json
"resources": [
{
    "apiVersion": "2018-10-15-preview",
    "type": "Microsoft.DevTestLab/labs",
    "name": "[parameters('newLabName')]",
    "location": "[resourceGroup (). location]",
    "resources": [
    {
        "apiVersion": "2018-10-15-preview",
        "name": "[variables('labVirtualNetworkName')]",
        "type": "virtualNetworks",
        "dependsOn": [
            "[resourceId('Microsoft.DevTestLab/labs', parameters('newLabName'))]"
        ]
    },
    {
        "apiVersion":"2018-10-15-preview",
        "name":"myGallery",
        "type":"sharedGalleries",
        "properties": {
            "galleryId":"[parameters('existingSharedGalleryId')]",
            "allowAllImages": "Enabled"
        },
        "dependsOn":[
            "[resourceId('Microsoft.DevTestLab/labs', parameters('newLabName'))]"
        ]
    }
    ]
} 

```

有关完整的资源管理器模板示例，请参阅我们的公共 GitHub 存储库中的以下资源管理器模板示例：[配置共享的映像库创建实验室时](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-lab-shared-gallery-configured)。

### <a name="create-a-vm-using-an-image-from-the-shared-image-gallery"></a>创建使用共享的映像库中的映像的 VM
如果你使用 Azure 资源管理器模板创建虚拟机使用共享的映像库映像，使用下面的示例：

```json

"resources": [
{
    "apiVersion": "2018-10-15-preview",
    "type": "Microsoft.DevTestLab/labs/virtualMachines",
    "name": "[variables('resourceName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "sharedImageId": "[parameters('existingSharedImageId')]",
        "size": "[parameters('newVMSize')]",
        "isAuthenticationWithSshKey": false,
        "userName": "[parameters('userName')]",
        "sshKey": "",
        "password": "[parameters('password')]",
        "labVirtualNetworkId": "[variables('labVirtualNetworkId')]",
        "labSubnetName": "[variables('labSubnetName')]"
    }
}
],

```

若要了解详细信息，请参阅我们的公共 GitHub 上的这些资源管理器模板示例。
[创建虚拟机使用共享的映像库映像](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-sharedimage)。

## <a name="use-api"></a>使用 API

- 使用 API 版本 2018年-10-15-preview。
- 若要附加您的库，请发送请求，如以下代码片段中所示：
    
    ``` 
    PUT [Lab Resource Id]/SharedGalleries/[newGalleryName]
    Body: 
    {
        “properties”:{
            “galleryId”: “[Shared Image Gallery resource Id]”,
            “allowAllImages”:”Enabled”
        }
    }
    ```
- 若要查看在你共享的映像库中的所有映像，可以列出所有共享的映像，以及由其资源 Id

    ```
    GET [Lab Resource Id]/SharedGalleries/mySharedGallery/SharedImages
    ````
- 若要创建使用共享的映像的 VM，可以执行 PUT 上的虚拟机和虚拟机属性中，传递的共享的映像时所获取的上一个调用 ID。 为属性。SharedImageId


## <a name="next-steps"></a>后续步骤
对项目，请参阅以下文章：

- [指定为实验室的必需项目](devtest-lab-mandatory-artifacts.md)
- [创建自定义项目](devtest-lab-artifact-author.md)
- [将项目存储库添加到实验室](devtest-lab-artifact-author.md)
- [对项目故障进行诊断](devtest-lab-troubleshoot-artifact-failure.md)
