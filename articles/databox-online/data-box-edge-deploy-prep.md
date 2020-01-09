---
title: 教程：准备 Azure 门户、数据中心环境，用于部署 Azure Data Box Edge | Microsoft Docs
description: 本文为有关部署 Azure Data Box Edge 第一篇教程，涉及到如何在 Azure 门户中做好准备。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 06/03/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: d9778544fd9e20dc3244a37c644117d1cd505a96
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75438662"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-edge"></a>教程：准备部署 Azure Data Box Edge  

本文是有关完全部署 Azure Data Box Edge 的教程系列的第一篇教程。 本教程介绍如何在 Azure 门户中做好准备，以部署 Data Box Edge 资源。

需要有管理员权限才能完成安装和配置过程。 门户准备只需不到 10 分钟的时间。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建新资源
> * 获取激活密钥

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

### <a name="get-started"></a>入门

若要部署 Data Box Edge，请按顺序参阅以下教程。

| **#** | **此步骤的内容** | **使用这些文档** |
| --- | --- | --- | 
| 1. |**[在 Azure 门户中做好部署 Data Box Edge 的准备](data-box-edge-deploy-prep.md)** |在安装 Data Box Edge 物理设备之前创建并配置 Data Box Edge 资源。 |
| 2. |**[安装 Data Box Edge](data-box-edge-deploy-install.md)**|将 Data Box Edge 物理设备拆包、装入机架并布线。  |
| 3. |**[连接、设置并激活 Data Box Edge](data-box-edge-deploy-connect-setup-activate.md)** |连接到本地 Web UI，完成设备设置，然后激活设备。 现已准备好在设备设置 SMB 或 NFS 共享。  |
| 4. |**[使用 Data Box Edge 传输数据](data-box-edge-deploy-add-shares.md)** |添加共享，并通过 SMB 或 NFS 连接到共享。 |
| 5. |**[使用 Data Box Edge 转换数据](data-box-edge-deploy-configure-compute.md)** |在设备上配置计算模块，以便在将数据移到 Azure 时转换数据。 |

现在可以开始设置 Azure 门户。

## <a name="prerequisites"></a>必备条件

以下说明了 Data Box Edge 资源、Data Box Edge 设备和数据中心网络的配置先决条件。

### <a name="for-the-data-box-edge-resource"></a>在 Data Box Edge 资源方面

在开始之前，请确保：

- 为 Data Box Edge 资源启用 Microsoft Azure 订阅。 不支持即用即付订阅。
- 你在资源组级别拥有 Data Box Edge/Data Box Gateway、IoT 中心和 Azure 存储资源的所有者或参与者访问权限。

    - 若要创建任何 Data Box Edge/Data Box Gateway 资源，你应该具有资源组级别范围内的参与者（或更高级别）权限。 你还需要确保已注册 `Microsoft.DataBoxEdge` 提供程序。 有关如何注册的信息，请转到[注册资源提供程序](data-box-edge-manage-access-power-connectivity-mode.md#register-resource-providers)。
    - 若要创建任何 IoT 中心资源，请确保已注册 Microsoft.Devices 提供程序。 有关如何注册的信息，请转到[注册资源提供程序](data-box-edge-manage-access-power-connectivity-mode.md#register-resource-providers)。
    - 若要创建存储帐户资源，你同样需要资源组级别范围内的参与者或更高级别访问权限。 Azure 存储在默认情况下是已注册的资源提供程序。
- 你拥有对 Azure Active Directory 图形 API 的管理员或用户访问权限。 有关详细信息，请参阅 [Azure Active Directory 图形 API](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-)。
- 具有 Microsoft Azure 存储帐户和访问凭据。

### <a name="for-the-data-box-edge-device"></a>对于 Data Box Edge 设备

在部署物理设备之前，请确保：

- 你已经查看配送包中包含的安全信息。
- 在数据中心的标准 19 英寸机架中提供一个 1U 插槽用于安装设备。
- 将设备安全放置在平坦、稳定的水平工作台面上。
- 用于安装设备的位置具有来自独立源的标准交流电，或具有带不间断电源 (UPS) 的机架电源分配单元 (PDU)。
- 有权访问物理设备。


### <a name="for-the-datacenter-network"></a>对于数据中心网络

在开始之前，请确保：

- 数据中心内的网络按 Data Box Edge 设备的网络要求配置。 有关详细信息，请参阅 [Data Box Edge 系统要求](data-box-edge-system-requirements.md)。

- 若要使 Data Box Edge 正常运行，请做好以下准备：

    - 使用至少 10 Mbps 的下载带宽以确保设备更新。
    - 使用至少 20 Mbps 的专用上传和下载带宽传输文件。

## <a name="create-a-new-resource"></a>创建新资源

如果现有的 Data Box Edge 资源可以管理物理设备，请跳过此步骤并转到[获取激活密钥](#get-the-activation-key)。

若要创建 Data Box Edge 资源，在 Azure 门户中执行以下步骤。

1. 使用 Microsoft Azure 凭据通过 
    
    - URL [https://portal.azure.com](https://portal.azure.com) 登录到 Azure 门户。
    - 或者，通过 URL [https://portal.azure.us](https://portal.azure.us) 登录到 Azure 政府版门户。 有关更多详细信息，请参阅[使用门户连接到 Azure 政府版](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal)。

2. 在左窗格中，选择“+ 创建资源”  。 搜索 **Data Box Edge/Data Box Gateway**。 选择“Data Box Edge/Data Box Gateway”。  选择“创建”  。
3. 选择要用于 Data Box Edge 设备的订阅。 选择要部署 Data Box Edge 资源的区域。 在此版本中，可以选择“美国东部”、“东南亚”和“西欧”。 

    选择离要部署设备的地理区域最近的位置。 该区域只存储用于设备管理的元数据。 实际数据可以存储在任何存储帐户中。 
    
    在“Data Box Edge”选项中，选择“创建”   。

    ![搜索 Data Box Edge 服务](media/data-box-edge-deploy-prep/data-box-edge-sku.png)

3. 在“基本信息”选项卡上，输入或选择以下“项目详细信息”。  
    
    |设置  |值  |
    |---------|---------|
    |订阅    |系统会根据前面所做的选择自动填充此字段。 订阅将链接到你的计费帐户。 |
    |资源组  |选择现有的组，或创建新组。<br>详细了解 [Azure 资源组](../azure-resource-manager/management/overview.md)。     |

4. 输入或选择以下“实例详细信息”。 

    |设置  |值  |
    |---------|---------|
    |名称   | 用于标识资源的友好名称。<br>该名称的长度必须介于 2 和 50 个字符之间，只能包含字母、数字和连字符。<br> 名称以字母或数字开头和结尾。        |
    |区域     |在此版本中，可以选择“美国东部”、“东南亚”和“西欧”来部署资源。 如果使用 Azure 政府版，则可选择 [Azure 区域](https://azure.microsoft.com/global-infrastructure/regions/)中显示的所有可用的政府区域。<br> 选择离要部署设备的地理区域最近的位置。|

    ![项目和实例详细信息](media/data-box-edge-deploy-prep/data-box-edge-resource.png)

5. 在完成时选择“下一步:  送货地址”。

    - 如果已经有一个设备，请选择与“我有 Data Box Edge 设备”对应的组合框。 
    - 如果这是你订购的新设备，请输入联系人姓名、公司、寄送设备的地址，以及联系人信息。

    ![新设备的送货地址](media/data-box-edge-deploy-prep/data-box-edge-resource1.png)

6. 在完成时选择“下一步:  查看 + 创建”。

7. 在“查看 + 创建”选项卡上，查看“定价详细信息”、“使用条款”和资源的详细信息。    选择与“我已经查看隐私条款”对应的组合框。 

    ![查看 Data Box Edge 资源详细信息和隐私条款](media/data-box-edge-deploy-prep/data-box-edge-resource2.png)

8. 选择“创建”  。

创建资源需要几分钟时间。 成功创建并部署资源后，你会收到通知。 选择“转到资源”。 

![转到 Data Box Edge 资源](media/data-box-edge-deploy-prep/data-box-edge-resource3.png)

在你下单以后，Microsoft 会审核订单并通过电子邮件联系你，核对配送详细信息。

![关于审核 Data Box Edge 订单的通知](media/data-box-edge-deploy-prep/data-box-edge-resource4.png)

## <a name="get-the-activation-key"></a>获取激活密钥

Data Box Edge 资源启动并运行后，需要获取激活密钥。 此密钥用于激活 Data Box Edge 设备并将其连接到资源。 如果你仍在 Azure 门户中，则现在可以获取此密钥。

1. 选择所创建的资源。 选择“概述”，然后选择“设备设置”   。

    ![选择“设备设置”](media/data-box-edge-deploy-prep/data-box-edge-select-devicesetup.png)

2. 在“激活”磁贴上，选择“生成密钥”以创建激活密钥。   选择复制图标复制密钥并将其保存供日后使用。

    ![获取激活密钥](media/data-box-edge-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - 生成的激活密钥将在三天后过期。
> - 如果密钥已过期，请生成新密钥。 旧密钥不再有效。

## <a name="next-steps"></a>后续步骤

在本教程中，我们已了解有关 Data Box Edge 的主题，例如：

> [!div class="checklist"]
> * 创建新资源
> * 获取激活密钥

请继续学习下一教程，了解如何安装 Data Box Edge。

> [!div class="nextstepaction"]
> [安装 Data Box Edge](./data-box-edge-deploy-install.md)



