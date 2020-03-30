---
title: 使用现有 Azure 负载均衡器配置虚拟机规模集 - Azure 门户
description: 了解如何使用现有 Azure 负载均衡器配置虚拟机规模集。
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 03/25/2020
ms.openlocfilehash: b2a83b226a4b2ddbbd554783de6a2b5c85c92f0a
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349728"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-portal"></a>使用 Azure 门户使用现有 Azure 负载均衡器配置虚拟机规模集

在本文中，您将了解如何使用现有的 Azure 负载均衡器配置虚拟机规模集。 

## <a name="prerequisites"></a>先决条件

- Azure 订阅。
- 将部署虚拟机规模集的订阅中的现有标准 sKU 负载均衡器。
- 虚拟机缩放集的 Azure 虚拟网络。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 中的[https://portal.azure.com](https://portal.azure.com)Azure 门户。



## <a name="deploy-virtual-machine-scale-set-with-existing-load-balancer"></a>使用现有负载均衡器部署虚拟机规模集

在本节中，您将使用现有的 Azure 负载均衡器在 Azure 门户中创建虚拟机缩放集。

> [!NOTE]
> 以下步骤假定以前已部署名为**myVNet**的虚拟网络和名为**myLoadBalancer 的**Azure 负载均衡器。

1. 在屏幕的左上角，单击"**创建资源** > **计算** > **虚拟机比例集**"或搜索市场搜索中设置的**虚拟机比例。**

2. 选择 **“创建”**。

3. 在 **"创建虚拟机缩放集**"中，输入或选择"**基础知识"** 选项卡中的此信息：

    | 设置                        | “值”                                                                                                 |
    |--------------------------------|-------------------------------------------------------------------------------------------------------|
    | **项目详情**            |                                                                                                       |
    | 订阅                   | 选择 Azure 订阅                                                                        |
    | 资源组                 | 选择"创建新"，输入**我的资源组**，然后选择"确定"，或选择现有资源组。 |
    | **缩放设置详细信息**          |                                                                                                       |
    | 虚拟机规模集名称 | 输入**myVMSS**                                                                                      |
    | 区域                         | 选择“美国东部 2”****                                                                                    |
    | 可用性区域              | 选择 **"无"**                                                                                       |
    | **实例详细信息**           |                                                                                                       |
    | 图像                          | 选择**Ubuntu 服务器 18.04 LTS**                                                                    |
    | Azure Spot 实例            | 选择**否**                                                                                         |
    | 大小                           | 默认保留                                                                                      |
    | **管理员帐户**      |                                                                                                       |
    | 身份验证类型            | 选择**密码**                                                                                   |
    | 用户名                       | 输入管理员用户名        |
    | 密码                       | 输入管理员密码    |
    | 确认密码               | 重新输入管理员密码 |


    :::image type="content" source="./media/vm-scale-sets/create-vm-scale-set-01.png" alt-text="创建虚拟机缩放集。" border="true":::

4. 选择 **"网络"** 选项卡。

5. 在 **"网络"** 选项卡中输入或选择此信息：

     设置                           | “值”                                                    |
    |-----------------------------------|----------------------------------------------------------|
    | **虚拟网络配置** |                                                          |
    | 虚拟网络                   | 选择**myVNet**或您现有的虚拟网络。      |
    | **负载平衡**                |                                                          |
    | 使用负载均衡器               | 选择“是”****                                           |
    | **负载均衡设置**       |                                                          |
    | 负载平衡选项            | 选择**Azure 负载均衡器**                           |
    | 选择负载均衡器            | 选择**myLoad 平衡器**或现有负载均衡器 |
    | 选择后端池             | 选择 **"我的后端池**"或"现有后端池"。  |

    :::image type="content" source="./media/vm-scale-sets/create-vm-scale-set-02.png" alt-text="创建虚拟机缩放集。" border="true":::

6. 选择"**管理**"选项卡。

7. 在 **"管理"** 选项卡中，将 **"启动诊断**"设置为 **"关闭**"。

8. 选择蓝色 **"审阅 + 创建**"按钮。

9. 查看设置并选择"**创建**"按钮。

## <a name="next-steps"></a>后续步骤

在本文中，您部署了一个虚拟机缩放集与现有的 Azure 负载均衡器。  要了解有关虚拟机缩放集和负载均衡器的更多详细信息，请参阅：

- [什么是 Azure 负载均衡器？](load-balancer-overview.md)
- [什么是虚拟机规模集？](../virtual-machine-scale-sets/overview.md)
