---
title: 将 Azure 事件中心专用群集移到另一个区域 | Microsoft Docs
description: 本文介绍如何将 Azure 事件中心专用群集从当前区域移到另一个区域。
ms.topic: how-to
ms.date: 09/01/2020
ms.openlocfilehash: 94be44ee8f9442a3a65e899d7a58524b2570f194
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89380608"
---
# <a name="move-an-azure-event-hubs-dedicated-cluster-to-another-region"></a>将 Azure 事件中心专用群集移到另一个区域
本文介绍如何导出现有事件中心专用群集的 Azure 资源管理器模板，然后使用该模板在另一个区域中创建具有相同配置设置的群集。 

如果在包含事件中心群集的 Azure 资源组中还有命名空间和事件中心等其他资源，则可能需要在资源组级别导出模板，以便可以通过一个步骤将所有相关资源移动到新区域。 本文中的步骤演示如何将事件中心群集导出到模板。 将资源组导出到模板的步骤类似。 

## <a name="prerequisites"></a>必备条件
确保可以在目标区域创建专用群集。 最简单的方法是使用 Azure 门户尝试[创建事件中心专用群集](event-hubs-dedicated-cluster-create-portal.md)。 此时会显示创建群集时所支持的区域的列表。 

## <a name="prepare"></a>准备
若要开始，请导出资源管理器模板。 此模板包含描述事件中心专用群集的设置。

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 选择“所有资源”，然后选择你的事件中心专用群集。
3. 选择“设置” > “导出模板”。 
4. 选择“导出模板”页中的“下载”。 

    :::image type="content" source="./media/move-cluster-across-regions/download-template.png" alt-text="下载资源管理器模板" lightbox="./media/move-cluster-across-regions/download-template.png":::
5. 找到从门户下载的 .zip 文件，并将该文件解压缩到所选的文件夹。

   此 zip 文件包含 .json 文件，后者包括模板和用于部署模板的脚本。


## <a name="move"></a>移动

部署模板以在目标区域中创建事件中心专用群集。 


1. 在 Azure 门户中，选择“创建资源”。
2. 在“搜索市场”中，键入“模板部署”并选择“模板部署(使用自定义模板进行部署)”  。
5. 选择“在编辑器中生成自己的模板”。
6. 选择“加载文件”，然后按说明加载在上一部分下载的 **template.json** 文件。
1. 更新 `location` 属性的值以指向新区域。 若要获取位置代码，请参阅 [Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。 区域的代码是不带空格的区域名称，例如 `West US` 等同于 `westus`。
1. 选择“保存”以保存该模板。 
1. 在“自定义部署”页上执行以下步骤： 
    1. 选择 Azure 订阅。 
    2. 选择现有的**资源组**或创建一个资源组。 
    3. 选择目标位置或区域。 如果选择了现有资源组，则此设置为只读。 
    4. 在“设置”部分中，执行以下步骤：    
        1. 输入新的群集名称。 

            :::image type="content" source="./media/move-cluster-across-regions/deploy-template.png" alt-text="下载资源管理器模板":::
    5. 在页面底部选择“查看 + 创建”。 
    1. 在“查看 + 创建”页上查看设置，然后选择“创建” 。  

## <a name="discard-or-clean-up"></a>丢弃或清理
部署后，如果要重新开始，则可以删除目标事件中心专用群集，并重复执行本文的[准备](#prepare)和[移动](#move)部分中所述的步骤。

若要提交更改并完成事件中心群集的移动，请删除原始区域中的事件中心群集。 

若要使用 Azure 门户删除事件中心群集（源或目标），请执行以下操作：

1. 在 Azure 门户顶部的搜索窗口中，键入“事件中心群集”，然后从搜索结果中选择“事件中心群集” 。 此时会在列表中看到事件中心群集。
2. 选择要删除的群集，并从工具栏中选择“删除”。 
3. 在“删除群集”页上，键入群集名称以确认要删除的内容，然后选择“删除”  。 

## <a name="next-steps"></a>后续步骤
本教程介绍了如何将事件中心专用群集从一个区域移动到另一个区域。 

有关将命名空间从一个区域移动到另一区域的说明，请参阅[跨区域移动事件中心命名空间](move-across-regions.md)。 

若要详细了解如何在区域之间移动资源，以及如何在 Azure 中进行灾难恢复，请参阅：

- [将资源移到新资源组或订阅中](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [将 Azure VM 移到另一区域](../site-recovery/azure-to-azure-tutorial-migrate.md)
