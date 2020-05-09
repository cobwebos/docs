---
title: 将 Azure 事件中心命名空间移动到另一个区域 |Microsoft Docs
description: 本文介绍如何将 Azure 事件中心命名空间从当前区域移到另一个区域。
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 04/14/2020
ms.author: spelluru
ms.reviewer: shvija
ms.openlocfilehash: 5b96bf1c538b3c5589a1993a0353292fadd0936d
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690487"
---
# <a name="move-an-azure-event-hubs-namespace-to-another-region"></a>将 Azure 事件中心命名空间移动到另一个区域
在各种情况下，你想要将现有的事件中心命名空间从一个区域移到另一个区域。 例如，你可能想要创建一个具有相同配置的命名空间进行测试。 在[灾难恢复规划](event-hubs-geo-dr.md#setup-and-failover-flow)过程中，可能还需要在另一个区域中创建辅助命名空间。

> [!NOTE]
> 本文介绍如何导出现有事件中心命名空间的 Azure 资源管理器模板，然后使用该模板在另一个区域中创建具有相同配置设置的命名空间。 但是，此过程不会移动尚未处理的事件。 你需要先处理原始命名空间中的事件，然后再将其删除。

## <a name="prerequisites"></a>先决条件

- 确保帐户使用的服务和功能在目标区域中受支持。
- 对于预览版功能，请确保你的订阅已列入目标区域的允许列表。
- 如果为命名空间中的事件中心启用了**捕获功能**，请在移动事件中心命名空间之前移动[Azure 存储或 Azure Data Lake Store 第2代](../storage/common/storage-account-move.md)或[Azure Data Lake Store 第1代](../data-lake-store/data-lake-store-migration-cross-region.md)帐户。 还可以按照本文中所述的步骤，将包含存储和事件中心命名空间的资源组移动到另一个区域。 
- 如果事件中心命名空间位于**事件中心群集**中，请在执行本文中的步骤之前，在**目标区域**中[创建一个专用群集](event-hubs-dedicated-cluster-create-portal.md)。 你还可以使用[GitHub 上的快速入门模板](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-cluster-namespace-eventhub/)来创建事件中心群集。 在模板中，删除 JSON 的命名空间部分以仅创建群集。 

## <a name="prepare"></a>准备
若要开始，请导出资源管理器模板。 此模板包含描述事件中心命名空间的设置。

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 选择 "**所有资源**"，然后选择事件中心命名空间。

3. 选择 >**设置** > ""**导出模板**"。

4. 选择 "**导出模板**" 页中的 "**下载**"。

    ![下载资源管理器模板](./media/move-across-regions/download-template.png)

5. 找到从门户下载的 .zip 文件，并将该文件解压缩到所选的文件夹。

   此 zip 文件包含包含模板的 json 文件和用于部署模板的脚本。


## <a name="move"></a>移动

部署模板以在目标区域中创建事件中心命名空间。 


1. 在 Azure 门户中，选择 "**创建资源**"。

2. 在“搜索市场”中键入“模板部署”，然后按 **ENTER**。  

3. 选择“模板部署”。****

4. 选择“创建”。 

5. 选择“在编辑器中生成自己的模板”****。

6. 选择“加载文件”，然后按说明加载在上一部分下载的 **template.json** 文件。****

7. 选择 "**保存**" 以保存模板。 

8. 在 "**自定义部署**" 页上，执行以下步骤： 

    1. 选择 Azure**订阅**。 

    2. 选择现有的**资源组**或创建一个资源组。 如果源命名空间位于事件中心群集中，请在目标区域中选择包含群集的资源组。 

    3. 选择目标**位置**或区域。 如果选择了现有的资源组，则此设置为只读。 

    4. 在 "**设置**" 部分中，执行以下步骤：
    
        1. 输入新的**命名空间名称**。 

            ![部署资源管理器模板](./media/move-across-regions/deploy-template.png)

        2. 如果源命名空间位于**事件中心群集**中，请在 "**外部 ID**" 中输入**资源组**和**事件中心群集**的名称。 

              ```
              /subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<CLUSTER'S RESOURCE GROUP>/providers/Microsoft.EventHub/clusters/<CLUSTER NAME>
              ```   
        3. 如果命名空间中的事件中心使用存储帐户来捕获事件，请为`StorageAccounts_<original storage account name>_external` "字段" 指定 "资源组名称" 和 "存储帐户"。 
            
            ```
            /subscriptions/0000000000-0000-0000-0000-0000000000000/resourceGroups/<STORAGE'S RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>
            ```    
    5. 选择“我同意上述条款和条件”复选框。**** 
    
    6. 现在，选择 "**选择购买**" 以启动部署过程。 

## <a name="discard-or-clean-up"></a>丢弃或清理
部署后，如果你想要从头开始，你可以删除**目标事件中心命名空间**，并重复本文[准备](#prepare)和[移动](#move)部分所述的步骤。

若要提交更改并完成移动事件中心命名空间，请删除**源事件中心命名空间**。 请确保在删除命名空间前处理了命名空间中的所有事件。 

使用 Azure 门户删除事件中心命名空间（源或目标）：

1. 在 "Azure 门户顶部的" 搜索 "窗口中，键入"**事件中心**"，然后从搜索结果中选择"**事件中心**"。 会在列表中看到事件中心命名空间。

2. 选择要删除的目标命名空间，并在工具栏中选择 "**删除**"。 

    ![删除命名空间-按钮](./media/move-across-regions/delete-namespace-button.png)

3. 在 "**删除资源**" 页上，验证所选的资源，并通过键入 **"是"** 确认删除，然后选择 "**删除**"。 

## <a name="next-steps"></a>后续步骤

在本教程中，已将 Azure 事件中心命名空间从一个区域移到另一个区域，并清理了源资源。  若要详细了解如何在区域之间移动资源，以及如何在 Azure 中进行灾难恢复，请参阅：


- [将资源移到新资源组或订阅中](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [将 Azure VM 移到另一区域](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
