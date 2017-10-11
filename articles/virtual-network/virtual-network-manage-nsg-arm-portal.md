---
title: "使用 Azure 门户管理 NSG| Microsoft 文档"
description: "了解如何使用 Azure 门户管理现有的 NSG。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 5d55679d-57da-457c-97dc-1e1973909ee5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2016
ms.author: jdial
ms.openlocfilehash: e9bcf8a893ff209337f6a5763b631a22f8514e20
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="manage-nsgs-using-the-portal"></a>使用门户创建 NSG

> [!div class="op_single_selector"]
> * [门户](virtual-network-manage-nsg-arm-portal.md)
> * [PowerShell](virtual-network-manage-nsg-arm-ps.md)
> * [Azure CLI](virtual-network-manage-nsg-arm-cli.md)
>

[!INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

> [!NOTE]
> Azure 具有用于创建和处理资源的两个不同的部署模型：[Resource Manager 和经典](../resource-manager-deployment-model.md)。 本文介绍如何使用 Resource Manager 部署模型。Microsoft 建议对大多数新的部署使用该模型，而不是经典部署模型。
>

[!INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

## <a name="retrieve-information"></a>检索信息
可以查看现有 NSG、检索现有 NSG 的规则和查找与 NSG 关联的资源。

### <a name="view-existing-nsgs"></a>查看现有 NSG

若要查看订阅中的所有现有 NSG，请完成以下步骤：

1. 从浏览器导航到 http://portal.azure.com，并在必要时用 Azure 帐户登录。

2. 单击“**浏览**” > “**网络安全组**”。

    ![Azure 门户 - NSG](./media/virtual-network-manage-nsg-arm-portal/figure1.png)

3. 在“**网络安全组**”边栏选项卡中，查看 NSG 列表。

    ![Azure 门户 - NSG](./media/virtual-network-manage-nsg-arm-portal/figure2.png)

### <a name="view-nsgs-in-a-resource-group"></a>查看资源组中的 NSG

若要查看 **RG-NSG** 资源组中的 NSG 列表，请完成以下步骤：

1. 单击“**资源组**” > “**RG-NSG**” > “**...**”。

    ![Azure 门户 - NSG](./media/virtual-network-manage-nsg-arm-portal/figure3.png)

2. 在资源列表中，查找显示 NSG 图标的项，如以下“**资源**”边栏选项卡中所示。

    ![Azure 门户 - NSG](./media/virtual-network-manage-nsg-arm-portal/figure4.png)

### <a name="list-all-rules-for-an-nsg"></a>列出 NSG 的所有规则

若要查看 **NSG-FrontEnd** NSG 的规则，请完成以下步骤：

1. 在“**网络安全组**”边栏选项卡或上述“**资源**”边栏选项卡中，单击“**NSG-FrontEnd**”。

2. 在“**设置**”选项卡中，单击“**入站安全规则**”。

    ![Azure 门户 - NSG](./media/virtual-network-manage-nsg-arm-portal/figure5.png)

3. “**入站安全规则**”边栏选项卡如下所示。

    ![Azure 门户 - NSG](./media/virtual-network-manage-nsg-arm-portal/figure6.png)

4. 在“**设置**”选项卡中，单击“**出站安全规则**”，查看出站规则。

    > [!NOTE]
    > 若要查看默认规则，请单击显示规则的边栏选项卡顶部的“**默认规则**”图标。
    >

### <a name="view-nsgs-associations"></a>查看 NSG 关联项

若要查看与 **NSG-FrontEnd** NSG 相关联的资源，请完成以下步骤：

1. 在“**网络安全组**”边栏选项卡或上述“**资源**”边栏选项卡中，单击“**NSG-FrontEnd**”。

2. 在“**设置**”选项卡中，单击“**子网**”，查看与 NSG 关联的子网。

    ![Azure 门户 - NSG](./media/virtual-network-manage-nsg-arm-portal/figure7.png)

3. 在“**设置**”选项卡中，单击“**网络接口**”，查看与 NSG 关联的 NIC。

## <a name="manage-rules"></a>管理规则
可向现有 NSG 添加规则、编辑现有规则和删除规则。

### <a name="add-a-rule"></a>添加规则
若要向 **NSG-FrontEnd** NSG 添加规则，允许来自任何计算机的**入站**流量流入端口 **443**，请完成以下步骤：

1. 在“**网络安全组**”边栏选项卡或上述“**资源**”边栏选项卡中，单击“**NSG-FrontEnd**”。
2. 在“**设置**”选项卡中，单击“**入站安全规则**”。
3. 在“**入站安全规则**”边栏选项卡中，单击“**添加**”。 然后，在“**添加入站安全规则**”边栏选项卡中，填充如下所示的值，并单击“**确定**”。

    ![Azure 门户 - NSG](./media/virtual-network-manage-nsg-arm-portal/figure8.png)

    几秒钟后，即可在“**入站安全规则**”边栏选项卡中查看新规则。

    ![Azure 门户 - NSG](./media/virtual-network-manage-nsg-arm-portal/figure9.png)

### <a name="change-a-rule"></a>更改规则
要将上面创建的规则更改为仅允许来自 **Internet** 的入站流量，请完成以下步骤：

1. 在“**网络安全组**”边栏选项卡或上述“**资源**”边栏选项卡中，单击“**NSG-FrontEnd**”。
2. 在“**设置**”选项卡中，单击上文创建的规则。
3. 在“**allow-https**”边栏选项卡中，对“**Source**”属性进行如下更改，并单击“**保存**”。

    ![Azure 门户 - NSG](./media/virtual-network-manage-nsg-arm-portal/figure10.png)

### <a name="delete-a-rule"></a>删除规则

若要删除上文创建的规则，请完成以下步骤：

1. 在“**网络安全组**”边栏选项卡或上述“**资源**”边栏选项卡中，单击“**NSG-FrontEnd**”。
2. 在“**设置**”选项卡中，单击上文创建的规则。
3. 在“**allow-https**”边栏选项卡中，单击“**删除**”，并单击“**是**”。

    ![Azure 门户 - NSG](./media/virtual-network-manage-nsg-arm-portal/figure11.png)

## <a name="manage-associations"></a>管理关联项
可将 NSG 关联到子网和 NIC。 还可取消 NSG 与任何相关联的资源之间的关联。

### <a name="associate-an-nsg-to-a-nic"></a>将 NSG 关联到 NIC
若要关联 **NSG-FrontEnd** NSG 与 **TestNICWeb1** NIC，请完成以下步骤：

1. 在“**网络安全组**”边栏选项卡或上述“**资源**”边栏选项卡中，单击“**NSG-FrontEnd**”。
2. 在“**设置**”选项卡中，依次单击“**网络接口**” > “**关联**” > ”**TestNICWeb1**”。

    ![Azure 门户 - NSG](./media/virtual-network-manage-nsg-arm-portal/figure12.png)

### <a name="dissociate-an-nsg-from-a-nic"></a>取消 NSG 与 NIC 之间的关联

若要取消关联 **NSG-FrontEnd** NSG 与 **TestNICWeb1** NIC，请完成以下步骤：

1. 在 Azure 门户中，单击“**资源组**” > “**RG-NSG**” > “**...**” > “**TestNICWeb1**”。

2. 在“**TestNICWeb1**”边栏选项卡中，单击“**更改安全性...**” > “**无**”。

    ![Azure 门户 - NSG](./media/virtual-network-manage-nsg-arm-portal/figure13.png)

> [!NOTE]
> 还可使用此边栏选项卡将 NIC 关联到任意现有 NSG。
>

### <a name="dissociate-an-nsg-from-a-subnet"></a>取消 NSG 与子网之间的关联

若要取消关联 **NSG-FrontEnd** NSG 与 **FrontEnd** 子网，请完成以下步骤：

1. 在 Azure 门户中，依次单击“**资源组**” > “**RG-NSG**” > “**...**” > “**TestVNet**”。

2. 在“**设置**”边栏选项卡中，单击“**子网**” > “**FrontEnd**” > “**网络安全组**” > “**无**”。

    ![Azure 门户 - NSG](./media/virtual-network-manage-nsg-arm-portal/figure14.png)

3. 在“**FrontEnd**”边栏选项卡中，单击“**保存**”。

    ![Azure 门户 - NSG](./media/virtual-network-manage-nsg-arm-portal/figure15.png)

### <a name="associate-an-nsg-to-a-subnet"></a>将 NSG 关联到子网

若要重新关联 **NSG-FrontEnd** NSG 与 **FrontEnd** 子网，请完成以下步骤：

1. 在 Azure 门户中，单击“**资源组**” > “**RG-NSG**” > “**...**” > “**TestVNet**”。
2. 在“**设置**”边栏选项卡中，单击“**子网**” > “**FrontEnd**” > “**网络安全组**” > “**NSG-FrontEnd**”。
3. 在“**FrontEnd**”边栏选项卡中，单击“**保存**”。

> [!NOTE]
> 还可以在 NSG 的“**设置**”边栏选项卡中关联 NSG 与子网。
>

## <a name="delete-an-nsg"></a>删除 NSG
仅当 NSG 不与任何资源关联时，才能删除 NSG。 若要删除 NSG，请完成以下步骤：

1. 在 Azure 门户中，依次单击“**资源组**” > “**RG-NSG**” > “**...**” > “**NSG-FrontEnd**”。
2. 在“**设置**”边栏选项卡中，单击“**网络接口**”。
3. 如果有任何 NIC 列出，请按照[取消关联 NSG 与 NIC](#Dissociate-an-NSG-from-a-NIC) 中的第 2 步操作。
4. 对每个 NIC 重复步骤 3。
5. 在“**设置**”边栏选项卡中，单击“**子网**”。
6. 如果有任何子网列出，请单击相应的子网，并按照[取消关联 NSG 与子网](#Dissociate-an-NSG-from-a-subnet)中的第 2 步和第 3 步操作。
7. 向左滚动到“**NSG-FrontEnd**”边栏选项卡，并依次单击“**删除**” > “**是**”。

    ![Azure 门户 - NSG](./media/virtual-network-manage-nsg-arm-portal/figure16.png)

## <a name="next-steps"></a>后续步骤
* 为 NSG [启用日志记录](virtual-network-nsg-manage-log.md)。
