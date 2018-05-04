---
title: 创建网络安全组 - Azure 门户 | Microsoft Docs
description: 了解如何使用 Azure 门户创建和部署网络安全组。
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 5bc8fc2e-1e81-40e2-8231-0484cd5605cb
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d8a66de0b0239fef12168733eca7af85c8b08f82
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2018
---
# <a name="create-a-network-security-group-using-the-azure-portal"></a>使用 Azure 门户创建网络安全组

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

本文介绍 Resource Manager 部署模型。 还可[在经典部署模型中创建 NSG](virtual-networks-create-nsg-classic-ps.md)。

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]


## <a name="create-the-nsg-frontend-nsg"></a>创建 NSG-FrontEnd NSG
若要如上述方案所示创建 **NSG-FrontEnd** NSG，请完成以下步骤：

1. 在浏览器中导航到 https://portal.azure.com，并根据需要使用 Azure 帐户登录。
2. 选择“+ 创建资源”> > “网络安全组”。
   
    ![Azure 门户 - NSG](./media/virtual-networks-create-nsg-arm-pportal/figure11.png)
3. 在“网络安全组”下，选择“添加”。
   
    ![Azure 门户 - NSG](./media/virtual-networks-create-nsg-arm-pportal/figure12.png)
4. 在“创建网络安全组”下，在 *RG-NSG* 资源组中创建一个名为 *NSG-FrontEnd* 的 NSG，然后选择“创建”。
   
    ![Azure 门户 - NSG](./media/virtual-networks-create-nsg-arm-pportal/figure13.png)

## <a name="create-rules-in-an-existing-nsg"></a>在现有 NSG 中创建规则
若要从 Azure 门户在现有 NSG 中创建规则，请完成以下步骤：

1. 选择“所有服务”，然后搜索“网络安全组”。 当“网络安全组”出现时，将其选中。
2. 在 NSG 列表中，选择“NSG-FrontEnd” > “入站安全规则”
   
    ![Azure 门户 - NSG-FrontEnd](./media/virtual-networks-create-nsg-arm-pportal/figure2.png)
3. 在“入站安全规则”列表中，选择“添加”。
   
    ![Azure 门户 - 添加规则](./media/virtual-networks-create-nsg-arm-pportal/figure3.png)
4. 在“添加入站安全规则”下，创建一个名为 *web-rule* 的规则，使其优先级为 *200*，允许端口 *80* 通过 *TCP* 访问来自任何来源的任何 VM，然后选择“确定”。 请注意，这些设置中大多数已为默认值。
   
    ![Azure 门户 - 规则设置](./media/virtual-networks-create-nsg-arm-pportal/figure4.png)
5. 几秒钟后可在 NSG 中看到新规则。
   
    ![Azure 门户 - 新建规则](./media/virtual-networks-create-nsg-arm-pportal/figure5.png)
6. 重复到第 6 步以创建名为 *rdp-rule* 的入站规则，其优先级为 *250*，允许通过 *TCP* 经端口 *3389* 访问来自任何来源的任何 VM。

## <a name="associate-the-nsg-to-the-frontend-subnet"></a>将 NSG 关联到 FrontEnd 子网

1. 选择“所有服务”，输入“资源组”，当“资源组”出现时将其选中，然后选择“RG-NSG”。
2. 在“RG-NSG”下，选择“...” > “TestVNet”。
   
    ![Azure 门户 - TestVNet](./media/virtual-networks-create-nsg-arm-pportal/figure14.png)
3. 在“设置”下，选择“子网” > “FrontEnd” > “网络安全组” > “NSG-FrontEnd”。
   
    ![Azure 门户 - 子网设置](./media/virtual-networks-create-nsg-arm-pportal/figure15.png)
4. 在“FrontEnd”边栏选项卡中，选择“保存”。
   
    ![Azure 门户 - 子网设置](./media/virtual-networks-create-nsg-arm-pportal/figure16.png)

## <a name="create-the-nsg-backend-nsg"></a>创建 NSG-BackEnd NSG
若要创建 **NSG-BackEnd** NSG 并将它关联到 **BackEnd** 子网，请完成以下步骤：

1. 若要创建名为 *NSG-BackEnd* 的 NSG，请重复[创建 NSG-FrontEnd NSG](#Create-the-NSG-FrontEnd-NSG) 中的步骤。
2. 若要创建下表中的**入站**规则，请重复[在现有 NSG 中创建规则](#Create-rules-in-an-existing-NSG)中的步骤。
   
   | 入站规则 | 出站规则 |
   | --- | --- |
   | ![Azure 门户 - 入站规则](./media/virtual-networks-create-nsg-arm-pportal/figure17.png) |![Azure 门户 - 出站规则](./media/virtual-networks-create-nsg-arm-pportal/figure18.png) |
3. 若要将 **NSG-Backend** NSG 关联到 **BackEnd** 子网，请重复[将 NSG 关联到 FrontEnd 子网](#Associate-the-NSG-to-the-FrontEnd-subnet)中的步骤。

## <a name="next-steps"></a>后续步骤
* 了解如何[管理现有 NSG](manage-network-security-group.md)
* 为 NSG [启用日志记录](virtual-network-nsg-manage-log.md)。