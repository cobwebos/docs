---
title: 将服务总线命名空间移动到另一个区域 |Microsoft Docs
description: 本文介绍如何将 Azure 服务总线命名空间从当前区域移到另一个区域。
ms.topic: how-to
ms.date: 06/23/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 573ea96437cf6cb76854ffa417fd3ad3fb86138b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88860795"
---
# <a name="move-an-azure-service-bus-namespace-to-another-region"></a>将 Azure 服务总线命名空间移动到另一个区域
在各种情况下，你想要将现有的服务总线命名空间从一个区域移到另一个区域。 例如，可能需要创建一个具有相同配置的命名空间，以便进行测试。 在 [灾难恢复规划](service-bus-geo-dr.md)过程中，可能还需要在另一个区域中创建辅助命名空间。

下面是概要步骤：

1. 将当前区域中的服务总线命名空间导出到 Azure 资源管理器模板。 
1. 更新模板中资源的位置。 此外，请从模板中删除默认订阅筛选器，因为你无法创建默认规则，因为它是自动创建的。 
1. 使用模板将服务总线命名空间部署到目标区域。 
1. 验证部署以确保所有主题的命名空间、队列、主题和订阅都已在目标区域中创建。 
1. 在处理完所有消息后，通过从源区域中删除命名空间来完成移动。 

## <a name="prerequisites"></a>必备条件
确保你的帐户使用的 Azure 服务总线和功能在目标区域中受支持。
 
## <a name="prepare"></a>准备
若要开始，请导出资源管理器模板。 此模板包含描述服务总线命名空间的设置。

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 选择 " **所有资源** "，然后选择服务总线命名空间。
3. 选择“设置” > “导出模板”。 
4. 选择“导出模板”页中的“下载”。 

    :::image type="content" source="./media/move-across-regions/download-template.png" alt-text="下载资源管理器模板":::
5. 找到从门户下载的 .zip 文件，并将该文件解压缩到所选的文件夹。 此 zip 文件包含模板和参数 JSON 文件。 
1. 在提取的文件夹中打开文件上的 template.js。 
1. 搜索 `location` ，将属性的值替换为该区域或位置的新名称。 若要获取位置代码，请参阅 [Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。 区域的代码是不带空格的区域名称，例如 `West US` 等同于 `westus`。
1. 删除类型为的资源的定义 `Microsoft.ServiceBus/namespaces/topics/subscriptions/rules` 。 别忘了删除 `,` 此部分前面的逗号 () 字符，以使 JSON 保持有效。  

    > [!NOTE]
    > 不能使用资源管理器模板为订阅创建默认规则。 当在目标区域中创建订阅时，将自动创建默认规则。 

## <a name="move"></a>移动
部署模板以在目标区域中创建服务总线命名空间。 

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

    :::image type="content" source="./media/move-across-regions/resource-group-navigation-link.png" alt-text="下载资源管理器模板" 页上，选择服务总线命名空间。 

    :::image type="content" source="./media/move-across-regions/select-namespace.png" alt-text="下载资源管理器模板" 页上，验证你是否看到了源区域中的队列、主题和订阅。 
    1. 在右侧窗格底部的命名空间中可以看到 **队列** 。         
    
        :::image type="content" source="./media/move-across-regions/queue-namespace.png" alt-text="下载资源管理器模板" 选项卡以查看命名空间中的主题
    
        :::image type="content" source="./media/move-across-regions/topics-namespace.png" alt-text="下载资源管理器模板":::
    3. 选择用于验证是否已创建订阅的主题。 

        :::image type="content" source="./media/move-across-regions/topic-subscriptions.png" alt-text="下载资源管理器模板":::      
    
    

## <a name="discard-or-clean-up"></a>丢弃或清理
部署后，如果你想要从头开始，则可以删除 **目标服务总线命名空间**，并重复本文 [准备](#prepare) 和 [移动](#move) 部分所述的步骤。

若要提交更改并完成服务总线命名空间的移动，请删除 **源服务总线命名空间**。 请确保在删除命名空间前处理所有消息。 

若要使用 Azure 门户 (源或目标) 删除服务总线命名空间，请执行以下操作：

1. 在 "Azure 门户顶部的" 搜索 "窗口中，键入" **Service bus**"，然后从搜索结果中选择" **服务总线** "。 列表中会显示服务总线命名空间。
2. 选择要删除的目标命名空间，并从工具栏中选择“删除”。 

    ![删除命名空间 - 按钮](./media/move-across-regions/delete-namespace-button.png)
3. 在 " **删除资源** " 页上，验证所选的资源，并通过键入 **"是"** 确认删除，然后选择 " **删除**"。 

    其他选项是删除具有服务总线命名空间的资源组。 在 " **资源组** " 页上，选择工具栏上的 " **删除资源组** "，然后确认删除。 

## <a name="next-steps"></a>后续步骤

在本教程中，已将 Azure 服务总线命名空间从一个区域移到另一个区域，并清理了源资源。  若要详细了解如何在区域之间移动资源，以及如何在 Azure 中进行灾难恢复，请参阅：

- [将资源移到新资源组或订阅中](../azure-resource-manager/management/move-resource-group-and-subscription.md)
