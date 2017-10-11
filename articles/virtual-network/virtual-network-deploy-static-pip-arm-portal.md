---
title: "具有静态公共 IP 地址-Azure 门户创建 VM |Microsoft 文档"
description: "了解如何使用 Azure 门户的静态公共 IP 地址创建 VM。"
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
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="create-a-vm-with-a-static-public-ip-address-using-the-azure-portal"></a>具有静态公共 IP 地址使用 Azure 门户创建 VM

> [!div class="op_single_selector"]
> * [Azure 门户](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [Azure CLI](virtual-network-deploy-static-pip-arm-cli.md)
> * [模板](virtual-network-deploy-static-pip-arm-template.md)
> * [PowerShell （经典）](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

> [!NOTE]
> Azure 具有用于创建和使用资源的两个不同的部署模型：[资源管理器和经典](../resource-manager-deployment-model.md)。 本文介绍如何使用资源管理器部署模型时，Microsoft 建议大多数新部署，而不是经典部署模型。

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name="create-a-vm-with-a-static-public-ip"></a>使用静态公共 IP 创建 VM

若要使用静态公共 IP 地址在 Azure 门户创建 VM，完成以下步骤：

1. 从浏览器中，导航到[Azure 门户](https://portal.azure.com)，如有必要，使用你的 Azure 帐户登录。
2. 在门户的左上角，单击**新建**>>**计算**>**Windows Server 2012 R2 Datacenter**。
3. 在**选择部署模型**列表中，选择**资源管理器**单击**创建**。
4. 在**基础知识**边栏选项卡，如下所示，输入 VM 信息，然后单击**确定**。
   
    ![Azure 门户的基础知识](./media/virtual-network-deploy-static-pip-arm-portal/figure1.png)
5. 在**选择大小**边栏选项卡，单击**A1 标准**如下所示，然后单击**选择**。
   
    ![Azure 门户-请选择一个大小](./media/virtual-network-deploy-static-pip-arm-portal/figure2.png)
6. 在**设置**边栏选项卡，单击**公共 IP 地址**，然后在**创建公共 IP 地址**边栏选项卡下**分配**，单击**静态**如下所示。 然后单击**确定**。
   
    ![Azure 门户-创建公共 IP 地址](./media/virtual-network-deploy-static-pip-arm-portal/figure3.png)
7. 在**设置**边栏选项卡，单击**确定**。
8. 查看**摘要**边栏选项卡，如下所示，，然后单击**确定**。
   
    ![Azure 门户-创建公共 IP 地址](./media/virtual-network-deploy-static-pip-arm-portal/figure4.png)
9. 请注意您的仪表板中的新磁贴。
   
    ![Azure 门户-创建公共 IP 地址](./media/virtual-network-deploy-static-pip-arm-portal/figure5.png)
10. 创建 VM 后，**设置**边栏选项卡将显示如下所示
    
    ![Azure 门户-创建公共 IP 地址](./media/virtual-network-deploy-static-pip-arm-portal/figure6.png)

