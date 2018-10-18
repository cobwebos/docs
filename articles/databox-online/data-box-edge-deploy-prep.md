---
title: 有关在 Azure 门户中做好准备以部署 Data Box Edge 的教程 | Microsoft Docs
description: 本文为有关部署 Azure Data Box Edge 第一篇教程，涉及到如何在 Azure 门户中做好准备
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/08/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 35ac28d687c8bc6636a7d8e10f54ffb5b219a776
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2018
ms.locfileid: "49167454"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-edge-preview"></a>教程：准备部署 Azure Data Box Edge（预览版）


本文是有关完全部署 Azure Data Box Edge 的教程系列的第一篇教程。 本教程介绍如何在 Azure 门户中做好准备，以部署 Data Box Edge 资源。 

需要有管理员权限才能完成安装和配置过程。 门户准备只需不到 10 分钟的时间。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建新资源
> * 获取激活密钥

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。


> [!IMPORTANT]
> Data Box Edge 以预览版提供。 在订购和部署此解决方案之前，请查看 [Azure 预览版服务的条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 

### <a name="get-started"></a>入门

若要部署 Data Box Edge，请按顺序参阅以下教程。

| **#** | **此步骤的内容** | **使用这些文档** |
| --- | --- | --- | 
| 1. |**[在 Azure 门户中做好部署 Data Box Edge 的准备](data-box-edge-deploy-prep.md)** |在安装 Data Box Edge 物理设备之前创建并配置 Data Box Edge 资源。 |
| 2. |**[安装 Data Box Edge](data-box-edge-deploy-install.md)**|将 Data Box Edge 物理设备拆包、装入机架并布线。  |
| 3. |**[连接、设置并激活 Data Box Edge](data-box-edge-deploy-connect-setup-activate.md)** |连接到本地 Web UI，完成设备设置，然后激活设备。 现已准备好在设备设置 SMB 或 NFS 共享。  |
| 4. |**[使用 Data Box Edge 传输数据](data-box-edge-deploy-add-shares.md)** |添加共享，并通过 SMB 或 NFS 连接到共享。 |
| 5. |**[使用 Data Box Edge 转换数据](data-box-edge-deploy-configure-compute.md)** |在设备上配置 Edge 模块，以便在将数据移到 Azure 时转换数据。 |

现在可以开始设置 Azure 门户。

## <a name="prerequisites"></a>先决条件

本部分说明 Data Box Edge 资源、Data Box Edge 设备和数据中心网络的配置先决条件。

### <a name="for-the-data-box-edge-resource"></a>在 Data Box Edge 资源方面

在开始之前，请确保：

* 应该为 Data Box Edge 资源启用 Microsoft Azure 订阅。
* 具有 Microsoft Azure 存储帐户和访问凭据。

### <a name="for-the-data-box-edge-device"></a>对于 Data Box Edge 设备

在部署物理设备之前，请确保：

- 在数据中心的标准 19 英寸机架中提供一个 1U 插槽用于安装设备。 
- 确保将设备安全放置在平坦、稳定的水平工作台面上。
- 确认用于安装设备的位置具有来自独立源的标准交流电，或具有带不间断电源 (UPS) 的机架电源分配单元 (PDU)。
- 有权访问物理设备。


### <a name="for-the-datacenter-network"></a>对于数据中心网络

在开始之前，请确保：

* 数据中心内的网络按 Data Box Edge 设备的网络要求配置。 有关详细信息，请参阅 [Data Box Edge 系统要求](data-box-gateway-system-requirements.md)。

* Data Box Edge 始终有专用的 20 Mbps Internet 带宽（或更高带宽）。 此带宽不应与任何其他应用程序共享。 如果使用网络限制，为使限制正常工作，我们建议使用 32-Mbps 或更高的 Internet 带宽。

## <a name="create-a-new-resource"></a>创建新资源

执行以下步骤，创建新的 Data Box Edge 资源。 

如果现有的 Data Box Edge 资源可以管理物理设备，请跳过此步骤并转到[获取激活密钥](#get-the-activation-key)。

在 Azure 门户中执行以下步骤以创建 Data Box 资源。

1. 使用 Microsoft Azure 凭据通过以下 URL 登录到 Azure 预览门户：[https://aka.ms/databox-edge](https://aka.ms/databox-edge)。 

2. 选择要用于 Data Box Edge 预览版的订阅。 选择要部署 Data Box Edge 资源的区域。 在“Data Box Edge”选项中，单击“创建”。

    ![搜索 Data Box Edge 服务](media/data-box-edge-deploy-prep/data-box-edge-sku.png)

3. 对于新资源，请输入或选择以下信息。
    
    |设置  |值  |
    |---------|---------|
    |资源名称   | 用于标识资源的友好名称。<br>资源名称的长度必须介于 2 和 50 个字符之间，只能包含字母、数字和连字符。<br> 名称以字母或数字开头和结尾。        |
    |订阅    |订阅将链接到你的计费帐户。 |
    |资源组  |选择现有的组，或创建新组。<br>详细了解 [Azure 资源组](../azure-resource-manager/resource-group-overview.md)。     |
    |位置     |此版本可在美国东部、美国西部 2 区、东南亚和西欧使用。 <br> 选择离要部署设备的地理区域最近的位置。|
    
    ![创建 Data Box Edge 资源](media/data-box-edge-deploy-prep/data-box-edge-resource.png)
    
4. 单击“确定”。
 
创建资源需要几分钟时间。 成功创建资源后，你会收到相应的通知。


## <a name="get-the-activation-key"></a>获取激活密钥

Data Box Edge 资源启动并运行后，需要获取激活密钥。 此密钥用于激活 Data Box Edge 设备并将其连接到资源。 如果你仍在 Azure 门户中，则现在可以获取此密钥。

1. 单击创建的资源，然后单击“概述”。

2. 单击“生成密钥”以创建激活密钥。 单击复制图标复制密钥，并将其保存供日后使用。

    ![获取激活密钥](media/data-box-edge-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - 生成的激活密钥将在 3 天后过期。 
> - 如果密钥已过期，请生成新密钥。 旧密钥不再有效。

## <a name="next-steps"></a>后续步骤

在本教程中，我们已了解有关 Data Box Edge 的主题，例如：

> [!div class="checklist"]
> * 创建新资源
> * 获取激活密钥

请继续学习下一教程，了解如何安装 Data Box Edge。 

> [!div class="nextstepaction"]
> [安装 Data Box Edge](./data-box-edge-deploy-install.md)



