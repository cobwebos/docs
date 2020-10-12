---
title: 将 Azure 中继命名空间移到另一个区域
description: 本文介绍如何将 Azure 中继命名空间从当前区域移到另一个区域。
ms.topic: how-to
ms.date: 09/03/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 60a182764639341fcda159356dd9fe6c65cfabd9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89463493"
---
# <a name="move-an-azure-relay-namespace-to-another-region"></a>将 Azure 中继命名空间移到另一个区域
本文介绍了如何将 Azure 中继命名空间从一个区域移到另一个区域。 下面是概要步骤：

1. 将中继命名空间**导出**到 Azure 资源管理器模板。
1. 为模板中的资源**更新位置（区域）** 。 另外，请从模板中删除任何**动态** WCF 中继。 

    WCF 中继有两种模式。 在第一种模式中，WCF 中继是使用 Azure 门户或 Azure 资源管理器模板显式创建的。 在 Azure 门户的“WCF 中继”页上，对于此模式下的中继，你可以看到 **isDynamic** 属性设置为 **false**。 

    在第二种模式下，WCF 中继是在侦听器（服务器）针对给定的终结点地址进行连接时自动生成的。 只要侦听器连接到中继，就会在 Azure 门户的 WCF 中继列表中看到中继。 对于此模式下的中继，**isDynamic** 属性设置为 **true**，因为它是动态生成的。 当侦听器断开连接时，动态 WCF 中继就会消失。 
1. 使用模板将资源**部署**到目标区域。

## <a name="prerequisites"></a>先决条件
确保 Azure 中继服务在目标区域中可用。 参阅[各区域的产品可用性](https://azure.microsoft.com/global-infrastructure/services/?products=service-bus&regions=all)。 
 
## <a name="prepare"></a>准备
若要开始，请导出资源管理器模板。 此模板包含描述 Azure 中继命名空间的设置。

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 选择“所有资源”，然后选择你的 Azure 中继命名空间。
3. 在左侧菜单的“设置”下选择“导出模板”。
4. 在“导出模板”页上，选择“下载”。 

    :::image type="content" source="./media/move-across-regions/download-template.png" alt-text="下载资源管理器模板":::
5. 找到从门户下载的 .zip 文件，并将该文件解压缩到所选的文件夹。 此 zip 文件包含模板和参数 JSON 文件。 
1. 在所选编辑器中打开提取的文件夹中的 **template.json** 文件。
1. 搜索 `location`，并将该属性的值替换为区域的新名称。 若要获取位置代码，请参阅 [Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。 区域的代码是不带空格的区域名称，例如 `West US` 等同于 `westus`。
1. 删除**动态 WCF 中继**资源（类型：`Microsoft.Relay/namespaces/WcfRelays`）的定义。 动态 WCF 中继是“中继”页面上 **isDynamic** 属性设置为 **true** 的中继。 在下面的示例中，**echoservice** 是一个动态 WCF 中继，应从模板中删除其定义。 

    :::image type="content" source="./media/move-across-regions/dynamic-relays.png" alt-text="下载资源管理器模板":::

## <a name="move"></a>移动
部署模板以在目标区域中创建一个中继命名空间。 

1. 在 Azure 门户中，选择“创建资源”。
2. 在 **"搜索应用商店**" 中，键入搜索文本的 **模板部署** ，选择 " **模板部署 (使用自定义模板进行部署") **，然后按 **enter**。

    :::image type="content" source="./media/move-across-regions/new-template-deployment.png" alt-text="下载资源管理器模板":::    
1. 在“模板部署”页面上，选择“创建”。

    :::image type="content" source="./media/move-across-regions/template-deployment-create-button.png" alt-text="下载资源管理器模板":::        
1. 在“自定义部署”页上，选择“在编辑器中生成自己的模板”。

    :::image type="content" source="./media/move-across-regions/build-template-link.png" alt-text="下载资源管理器模板":::            
1. 在“编辑模板”页的工具栏上选择“加载文件”，然后按照说明加载你在上一部分下载的 **template.json** 文件。

    :::image type="content" source="./media/move-across-regions/select-template.png" alt-text="下载资源管理器模板":::                
1. 选择“保存”以保存该模板。 

    :::image type="content" source="./media/move-across-regions/save-template.png" alt-text="下载资源管理器模板":::                    
1. 在“自定义部署”页上执行以下步骤： 
    1. 选择 Azure 订阅。 
    2. 选择现有的**资源组**或创建一个资源组。 
    3. 选择目标位置或区域。 如果选择了现有资源组，则此设置为只读。 
    4. 输入新的**命名空间名称**。
    1. 选择“查看 + 创建”  。 

        :::image type="content" source="./media/move-across-regions/deploy-template.png" alt-text="下载资源管理器模板" **创建** "。 
    
## <a name="verify"></a>验证
1. 在部署成功后，选择“转到资源组”。

    :::image type="content" source="./media/move-across-regions/resource-group-navigation-link.png" alt-text="下载资源管理器模板":::    
1. 在“资源组”页面上，选择 Azure 中继命名空间。 

    :::image type="content" source="./media/move-across-regions/select-namespace.png" alt-text="下载资源管理器模板":::    
1. 在“Azure 中继命名空间”页的左侧菜单上，选择“混合连接”或“WCF 中继”，以验证是否创建了混合连接和 WCF 中继。 如果在导入模板之前忘记了删除动态 WCF 中继的定义，请在“WCF 中继”页面上删除它们。 动态 WCF 中继是在客户端连接到中继命名空间时自动创建的。 

## <a name="discard-or-clean-up"></a>丢弃或清理
在部署后，如果要重新开始，可以删除**目标 Azure 中继命名空间**，并重复本文的[准备](#prepare)和[移动](#move)部分中所述的步骤。

若要提交更改并完成命名空间的移动，请删除源区域中的 **Azure 中继命名空间**。 

若要使用 Azure 门户删除 Azure 中继命名空间（源或目标），请执行以下操作：

1. 在 Azure 门户顶部的搜索窗口中，键入“中继”，然后从搜索结果中的“服务”中选择“中继”。  你可以在列表中看到所有 Azure 中继命名空间。
2. 选择要删除的目标命名空间以打开“中继”页面。 
1. 从“中继”页面的工具栏中选择“删除”。 

    ![删除命名空间 - 按钮](./media/move-across-regions/delete-namespace-button.png)
3. 在“删除命名空间”页面上，键入确认删除的 Azure 中继命名空间的名称，然后选择“删除”。 

## <a name="next-steps"></a>后续步骤
在本教程中，你将某个 Azure 中继命名空间从一个区域移到了另一个区域。 若要详细了解如何在区域之间移动资源，以及如何在 Azure 中进行灾难恢复，请参阅：

- [将资源移到新资源组或订阅中](../azure-resource-manager/management/move-resource-group-and-subscription.md)
