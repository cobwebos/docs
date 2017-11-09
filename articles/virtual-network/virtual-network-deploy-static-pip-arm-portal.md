---
title: "创建具有静态公共 IP 地址的 VM - Azure 门户 | Microsoft 文档"
description: "了解如何使用 Azure 门户创建具有静态公共 IP 地址的 VM。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: e9546bcc-f300-428f-b94a-056c5bd29035
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 233e4eea8439320c1c7446e2c2b2e9d379351a3e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-vm-with-a-static-public-ip-address-using-the-azure-portal"></a>使用 Azure 门户创建具有静态公共 IP 地址的 VM

> [!div class="op_single_selector"]
> * [Azure 门户](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [Azure CLI](virtual-network-deploy-static-pip-arm-cli.md)
> * [模板](virtual-network-deploy-static-pip-arm-template.md)
> * [PowerShell（经典）](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

> [!NOTE]
> Azure 具有用于创建和处理资源的两个不同的部署模型：[Resource Manager 和经典](../resource-manager-deployment-model.md)。 本文介绍如何使用 Resource Manager 部署模型。Microsoft 建议对大多数新的部署使用该模型，而不是经典部署模型。

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name="create-a-vm-with-a-static-public-ip"></a>使用静态公共 IP 创建 VM

若要在 Azure 门户中使用静态公共 IP 地址创建 VM，请完成以下步骤：

1. 从浏览器导航到 [Azure 门户](https://portal.azure.com)，并在必要时用 Azure 帐户登录。
2. 在门户左上角，单击“**新建**”>>“**计算**”>“**Windows Server 2012 R2 Datacenter**”。
3. 在“**选择部署模型**”列表中，选择“**Resource Manager**”，并单击“**创建**”。
4. 在“**基本信息**”边栏选项卡中，输入如下所示的 VM 信息，并单击“**确定**”。
   
    ![Azure 门户 - 基本信息](./media/virtual-network-deploy-static-pip-arm-portal/figure1.png)
5. 在“**选择大小**”边栏选项卡中，单击“**A1 标准**”（如下所示），并单击“**选择**”。
   
    ![Azure 门户 - 选择大小](./media/virtual-network-deploy-static-pip-arm-portal/figure2.png)
6. 在“**设置**”边栏选项卡中，单击“**公共 IP 地址**”，并在“**创建公共 IP 地址**”边栏选项卡的“**分配**”下，单击“**静态**”，如下所示。 然后，单击“确定”。
   
    ![Azure 门户 - 创建公共 IP 地址](./media/virtual-network-deploy-static-pip-arm-portal/figure3.png)
7. 在“**设置**”边栏选项卡中，单击“**确定**”。
8. 查看“**摘要**”边栏选项卡（如下所示），并单击“**确定**”。
   
    ![Azure 门户 - 创建公共 IP 地址](./media/virtual-network-deploy-static-pip-arm-portal/figure4.png)
9. 请注意仪表板中的新磁贴。
   
    ![Azure 门户 - 创建公共 IP 地址](./media/virtual-network-deploy-static-pip-arm-portal/figure5.png)
10. 创建 VM 后，“**设置**”边栏选项卡将如下所示
    
    ![Azure 门户 - 创建公共 IP 地址](./media/virtual-network-deploy-static-pip-arm-portal/figure6.png)

