---
title: 将 Azure 中继命名空间移动到另一个区域
description: 本文介绍如何将 Azure 中继命名空间从当前区域移到另一个区域。
ms.topic: how-to
ms.date: 09/03/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 60a182764639341fcda159356dd9fe6c65cfabd9
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2020
ms.locfileid: "89463493"
---
# <a name="move-an-azure-relay-namespace-to-another-region"></a>将 Azure 中继命名空间移动到另一个区域
本文介绍如何将 Azure 中继命名空间从一个区域移到另一个区域。 下面是概要步骤：

1. 将中继命名空间**导出**到 Azure 资源管理器模板。
1. 为模板中的资源** (区域) 更新位置**。 此外，从模板中删除所有 **动态** WCF 中继。 

    WCF 中继分为两种模式。 在第一种模式中，使用 Azure 门户或 Azure 资源管理器模板显式创建 WCF 中继。 在 Azure 门户的 " **WCF 中继** " 页上，可以看到在此模式下，中继的 **isDynamic** 属性设置为 **false** 。 

    在第二种模式下，当侦听器 (server) 连接给定终结点地址时，将自动生成 WCF 中继。 只要侦听器连接到中继，就会在 Azure 门户中的 WCF 中继列表中看到中继。 对于此模式下的中继， **isDynamic** 属性设置为 **true** ，因为它是动态生成的。 当侦听器断开连接时，动态 WCF 中继就会消失。 
1. 使用模板将资源**部署**到目标区域。

## <a name="prerequisites"></a>先决条件
确保 Azure 中继服务在目标区域中可用。 参阅[各区域的产品可用性](https://azure.microsoft.com/global-infrastructure/services/?products=service-bus&regions=all)。 
 
## <a name="prepare"></a>准备
若要开始，请导出资源管理器模板。 此模板包含描述 Azure 中继命名空间的设置。

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 选择 " **所有资源** "，然后选择 Azure 中继命名空间。
3. 选择左侧菜单中的 "**设置**" 下的 "**导出模板**"。
4. 选择 "**导出模板**" 页面上的 "**下载**"。

    :::image type="content" source="./media/move-across-regions/download-template.png" alt-text="下载资源管理器模板":::
5. 找到从门户下载的 .zip 文件，并将该文件解压缩到所选的文件夹。 此 zip 文件包含模板和参数 JSON 文件。 
1. 在所选编辑器中从提取的文件夹打开文件中的 **template.js** 。
1. 搜索 `location` ，并将属性的值替换为区域的新名称。 若要获取位置代码，请参阅 [Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。 区域的代码是不带空格的区域名称，例如， `West US` 等于 `westus` 。
1.  (类型：) 删除 **动态 WCF 中继** 资源的定义 `Microsoft.Relay/namespaces/WcfRelays` 。 动态 WCF 中继是在**中继**页面上将**isDynamic**属性设置为**true**的中继。 在下面的示例中， **echoservice** 是动态 WCF 中继，应从模板中删除其定义。 

    :::image type="content" source="./media/move-across-regions/dynamic-relays.png" alt-text="动态中继":::

## <a name="move"></a>移动
部署模板以在目标区域中创建中继命名空间。 

1. 在 Azure 门户中，选择“创建资源”。
2. 在 **"搜索应用商店**" 中，键入搜索文本的 **模板部署** ，选择 " **模板部署 (使用自定义模板进行部署") **，然后按 **enter**。

    :::image type="content" source="./media/move-across-regions/new-template-deployment.png" alt-text="新模板部署":::    
1. 在 " **模板部署** " 页上，选择 " **创建**"。

    :::image type="content" source="./media/move-across-regions/template-deployment-create-button.png" alt-text="新模板部署-创建 按钮":::        
1. 在 " **自定义部署** " 页上，选择 **"在编辑器中生成自己的模板"**。

    :::image type="content" source="./media/move-across-regions/build-template-link.png" alt-text="在编辑器中生成自己的模板-链接":::            
1. 在 " **编辑模板** " 页上，选择工具栏上的 " **加载文件** "，然后按照说明加载在上一节中下载的文件的 **template.js** 。

    :::image type="content" source="./media/move-across-regions/select-template.png" alt-text="选择模板":::                
1. 选择“保存”以保存该模板。 

    :::image type="content" source="./media/move-across-regions/save-template.png" alt-text="保存模板":::                    
1. 在“自定义部署”页上执行以下步骤： 
    1. 选择 Azure 订阅。 
    2. 选择现有的**资源组**或创建一个资源组。 
    3. 选择目标位置或区域。 如果选择了现有资源组，则此设置为只读。 
    4. 输入 **命名空间**的新名称。
    1. 选择“查看 + 创建”。 

        :::image type="content" source="./media/move-across-regions/deploy-template.png" alt-text="部署资源管理器模板":::
    1. 在 " **查看** " 和 "创建" 页上，选择页面底部的 " **创建** "。 
    
## <a name="verify"></a>验证
1. 部署成功后，选择 " **前往资源组**"。

    :::image type="content" source="./media/move-across-regions/resource-group-navigation-link.png" alt-text="中转到资源组链接":::    
1. 在 " **资源组** " 页上，选择 "Azure 中继命名空间"。 

    :::image type="content" source="./media/move-across-regions/select-namespace.png" alt-text="选择 Azure 中继命名空间":::    
1. 在 " **Azure 中继命名空间** " 页上，选择左侧菜单上的 " **混合连接** " 或 " **wcf 中继** "，验证是否创建了混合连接和 wcf 中继。 如果在导入模板之前忘记删除动态 WCF 中继的定义，请在 " **WCF 中继** " 页上将其删除。 当客户端连接到中继命名空间时，将自动创建动态 WCF 中继。 

## <a name="discard-or-clean-up"></a>丢弃或清理
部署后，如果你想要从头开始，你可以删除 **目标 Azure 中继命名空间**，并重复本文的 " [准备](#prepare) " 和 " [移动](#move) " 部分中描述的步骤。

若要提交更改并完成移动命名空间，请删除源区域中的 **Azure 中继命名空间** 。 

若要使用 Azure 门户删除 Azure 中继命名空间 (源或目标) ，请执行以下操作：

1. 在 "Azure 门户顶部的" 搜索 "窗口中，键入"**中继**"，并在搜索结果中从"**服务**"中选择"**中继**"。 会在列表中看到所有 Azure 中继命名空间。
2. 选择要删除的目标命名空间，以打开 " **中继** " 页。 
1. 在 " **中继** " 页上，从工具栏中选择 " **删除** "。 

    ![删除命名空间 - 按钮](./media/move-across-regions/delete-namespace-button.png)
3. 在 " **删除命名空间** " 页上，键入 Azure 中继命名空间的名称以确认删除，然后选择 " **删除**"。 

## <a name="next-steps"></a>后续步骤
在本教程中，已将 Azure 中继命名空间从一个区域移到另一个区域。 若要详细了解如何在区域之间移动资源，以及如何在 Azure 中进行灾难恢复，请参阅：

- [将资源移到新资源组或订阅中](../azure-resource-manager/management/move-resource-group-and-subscription.md)
