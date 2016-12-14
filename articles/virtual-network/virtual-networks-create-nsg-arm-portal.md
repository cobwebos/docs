---
title: "如何使用 Azure 门户在 ARM 模式下创建 NSG | Microsoft 文档"
description: "了解如何使用 Azure 门户在 ARM 模式下创建和部署 NSG"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
tags: azure-resource-manager
ms.assetid: faee5ac8-f4c4-4f97-ade5-197a37aad496
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 44233c124ec56d01f2fb8261fba0a7612ac23678


---
# <a name="how-to-manage-nsgs-using-the-azure-portal"></a>如何使用 Azure 门户管理 NSG
[!INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

本文介绍资源管理器部署模型。 还可[在经典部署模型中创建 NSG](virtual-networks-create-nsg-classic-ps.md)。

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

下面的示例 PowerShell 命令需要基于上述方案创建的简单环境。 若要运行本文档中所显示的命令，请首先通过部署[此模板](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd)构建测试环境，单击“**部署至 Azure**”，根据需要替换默认参数值，然后按照门户中的说明进行操作。 下面的步骤使用 **RG-NSG** 作为模板部署到的资源组的名称。

## <a name="create-the-nsg-frontend-nsg"></a>创建 NSG-FrontEnd NSG
若要如上述方案所示创建 **NSG-FrontEnd** NSG，请按照以下步骤进行操作。

1. 从浏览器导航到 http://portal.azure.com，并在必要时用 Azure 帐户登录。
2. 单击“**浏览**” > “**网络安全组**”。
   
    ![Azure 门户 - NSG](./media/virtual-networks-create-nsg-arm-pportal/figure11.png)
3. 在“**网络安全组**”边栏选项卡中，单击“**添加**”。
   
    ![Azure 门户 - NSG](./media/virtual-networks-create-nsg-arm-pportal/figure12.png)
4. 在“**创建网络安全组**”边栏选项卡中，在 *RG-NSG* 资源组中创建名为 *NSG-FrontEnd* 的 NSG，然后单击“**创建**”。
   
    ![Azure 门户 - NSG](./media/virtual-networks-create-nsg-arm-pportal/figure13.png)

## <a name="create-rules-in-an-existing-nsg"></a>在现有 NSG 中创建规则
若要从 Azure 门户在现有 NSG 中创建规则，请按照以下步骤进行操作。

1. 单击“**浏览**” > “**网络安全组**”。
2. 在 NSG 列表中，单击“**NSG-FrontEnd**” > “**入站安全规则**”
   
    ![Azure 门户 - NSG-FrontEnd](./media/virtual-networks-create-nsg-arm-pportal/figure2.png)
3. 在“**入站安全规则**”列表中，单击“**添加**”。
   
    ![Azure 门户 - 添加规则](./media/virtual-networks-create-nsg-arm-pportal/figure3.png)
4. 在“**添加入站安全规则**”边栏选项卡中创建一个名为 *web-rule* 的规则，其优先级为 *200*，允许端口 *80* 通过 *TCP* 访问来自任何来源的任何 VM，然后单击“**确定**”。 请注意，这些设置中大多数已为默认值。
   
    ![Azure 门户 - 规则设置](./media/virtual-networks-create-nsg-arm-pportal/figure4.png)
5. 几秒钟后可在 NSG 中看到新规则。
   
    ![Azure 门户 - 新建规则](./media/virtual-networks-create-nsg-arm-pportal/figure5.png)
6. 重复到第 6 步以创建名为 *rdp-rule* 的入站规则，其优先级为 *250*，允许通过 *TCP* 经端口 *3389* 访问来自任何来源的任何 VM。

## <a name="associate-the-nsg-to-the-frontend-subnet"></a>将 NSG 关联到 FrontEnd 子网
1. 单击“**浏览 >**” > “**资源组**” > “**RG NSG**”。
2. 在“**RG-NSG**”边栏选项卡中，单击“**...**” > “**TestVNet**”。
   
    ![Azure 门户 - TestVNet](./media/virtual-networks-create-nsg-arm-pportal/figure14.png)
3. 在“**设置**”边栏选项卡中，单击“**子网**” > “**FrontEnd**” > “**网络安全组**” > “**NSG-FrontEnd**”。
   
    ![Azure 门户 - 子网设置](./media/virtual-networks-create-nsg-arm-pportal/figure15.png)
4. 在“**FrontEnd**”边栏选项卡中，单击“**保存**”。
   
    ![Azure 门户 - 子网设置](./media/virtual-networks-create-nsg-arm-pportal/figure16.png)

## <a name="create-the-nsg-backend-nsg"></a>创建 NSG-BackEnd NSG
若要创建 **NSG-BackEnd** NSG 并将它关联到 **BackEnd** 子网，请执行以下步骤。

1. 重复[创建 NSG-FrontEnd NSG](#Create-the-NSG-FrontEnd-NSG) 中的步骤以创建名为 *NSG-BackEnd* 的 NSG
2. 重复[在现有 NSG 中创建规则](#Create-rules-in-an-existing-NSG)中的步骤以创建下表中的**入站**规则。
   
   | 入站规则 | 出站规则 |
   | --- | --- |
   | ![Azure 门户 - 入站规则](./media/virtual-networks-create-nsg-arm-pportal/figure17.png) |![Azure 门户 - 出站规则](./media/virtual-networks-create-nsg-arm-pportal/figure18.png) |
3. 重复[将 NSG 关联到 FrontEnd 子网](#Associate-the-NSG-to-the-FrontEnd-subnet)中的步骤以将 **NSG-Backend** NSG 关联到 **BackEnd** 子网。

## <a name="next-steps"></a>后续步骤
* 了解如何[管理现有 NSG](virtual-network-manage-nsg-arm-portal.md)
* 为 NSG [启用日志记录](virtual-network-nsg-manage-log.md)。




<!--HONumber=Nov16_HO3-->


