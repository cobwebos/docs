---
title: 有关在 Azure 门户中做好准备以部署 Data Box Gateway 的教程 | Microsoft Docs
description: 本文为有关部署 Azure Data Box Gateway 第一篇教程，涉及到如何在 Azure 门户中做好准备
services: databox-edge-gateway
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: 553d6f716bbb6e98aa64ef07cb80d2d6cba370b6
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82561541"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-gateway"></a>教程：准备部署 Azure Data Box Gateway

本文是有关完全部署 Azure Data Box Gateway 的教程系列的第一篇教程。 本教程介绍如何在 Azure 门户中做好准备，以部署 Data Box Gateway 资源。

需要有管理员权限才能完成安装和配置过程。 门户准备只需不到 10 分钟的时间。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
>
> * 创建新资源
> * 下载虚拟设备映像
> * 获取激活密钥

## <a name="get-started"></a>入门

若要部署 Data Box Gateway，请按顺序参阅以下教程。

| **#** | **此步骤的内容** | **使用这些文档** |
| --- | --- | --- | 
| 1. |**[在 Azure 门户中做好部署 Data Box Gateway 的准备](data-box-gateway-deploy-prep.md)** |在预配 Data Box Gateway 虚拟设备之前创建并配置 Data Box Gateway 资源。 |
| 2. |**[在 Hyper-V 中预配 Data Box Gateway](data-box-gateway-deploy-provision-hyperv.md)** <br><br><br>**[在 VMware 中预配 Data Box Gateway](data-box-gateway-deploy-provision-vmware.md)**|对于 Hyper-V，可在 Windows Server 2016 或 Windows Server 2012 R2 上运行 Hyper-V 的主机系统上预配和连接 Data Box Gateway 虚拟设备。 <br><br><br> 对于 VMware，可在运行 VMware ESXi 6.0、6.5 或 6.7 的主机系统上预配和连接 Data Box Gateway 虚拟设备。<br></br> |
| 3. |**[连接、设置并激活 Data Box Gateway](data-box-gateway-deploy-connect-setup-activate.md)** |连接到本地 Web UI，完成设备设置，然后激活设备。 然后即可预配 SMB 共享。  |
| 4. |**[使用 Data Box Gateway 传输数据](data-box-gateway-deploy-add-shares.md)** |添加共享，并通过 SMB 或 NFS 连接到共享。 |

现在可以开始设置 Azure 门户。

## <a name="prerequisites"></a>先决条件

本部分说明 Data Box Gateway 资源、Data Box Gateway 设备和数据中心网络的配置先决条件。

### <a name="for-the-data-box-gateway-resource"></a>对于 Data Box Gateway 资源

在开始之前，请确保：

* 已为 Azure Stack Edge 资源启用 Microsoft Azure 订阅。 确保使用了受支持的订阅，例如 [Microsoft 企业协议 (EA)](https://azure.microsoft.com/overview/sales-number/)、[云解决方案提供商 (CSP)](https://docs.microsoft.com/partner-center/azure-plan-lp) 或 [Microsoft Azure 赞助](https://azure.microsoft.com/offers/ms-azr-0036p/)。
* 你在资源组级别拥有对 Azure Stack Edge/Data Box Gateway、IoT 中心和 Azure 存储资源的所有者或参与者访问权限。
    - 若要创建任何 Azure Stack Edge/Data Box Gateway 资源，你应该在资源组级别范围内具有参与者（或更高级别）权限。 你还需要确保已注册 `Microsoft.DataBoxEdge` 提供程序。 有关如何注册的信息，请转到[注册资源提供程序](data-box-gateway-manage-access-power-connectivity-mode.md#register-resource-providers)。
    - 若要创建存储帐户资源，你同样需要资源组级别范围内的参与者或更高级别访问权限。 Azure 存储在默认情况下是已注册的资源提供程序。
- 你对 Microsoft Graph API 有管理员或用户访问权限。 有关详细信息，请参阅 [Microsoft Graph 权限参考](https://docs.microsoft.com/graph/permissions-reference)。
- 具有 Microsoft Azure 存储帐户和访问凭据。

### <a name="for-the-data-box-gateway-device"></a>对于 Data Box Gateway 设备

在部署虚拟设备之前，请确保：

- 有权访问在 Windows Server 2012 R2 或更高版本上运行 Hyper-V 的主机系统或者运行可用于预配设备的 VMware（ESXi 6.0、6.5 或 6.7）的主机系统。
- 主机系统能够将以下资源专用于预配 Data Box 虚拟设备：
  
  - 至少 4 个虚拟处理器。
  - 至少 8 GB 的 RAM。
  - 一个网络接口。
  - 一个 250 GB 的 OS 磁盘。
  - 一个 2 TB 的用于系统数据的虚拟磁盘。

### <a name="for-the-datacenter-network"></a>对于数据中心网络

在开始之前，请确保：

- 数据中心内的网络按 Data Box Gateway 设备的网络要求配置。 有关详细信息，请参阅 [Data Box Gateway 系统要求](data-box-gateway-system-requirements.md)。

- 要使 Data Box Gateway 正常运行，应该做好以下准备：

    - 使用至少 10 Mbps 的下载带宽以确保设备更新。
    - 使用至少 20 Mbps 的专用上传和下载带宽传输文件。

## <a name="create-a-new-resource"></a>创建新资源

如果现有的 Data Box Gateway 资源可以管理虚拟设备，请跳过此步骤并转到[获取激活密钥](#get-the-activation-key)。

若要创建 Data Box Gateway 资源，请在 Azure 门户中执行以下步骤。

1. 使用 Microsoft Azure 凭据通过

    - URL [https://portal.azure.com](https://portal.azure.com) 登录到 Azure 门户。
    - 或者，通过 URL [https://portal.azure.us](https://portal.azure.us) 登录到 Azure 政府版门户。 有关更多详细信息，请参阅[使用门户连接到 Azure 政府版](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal)。
2. 在左窗格中，选择“+ 创建资源”  。 搜索“Azure Stack Edge/Data Box Gateway”  。 选择“Azure Stack Edge/Data Box Gateway”。 选择“创建”  。
3. 选择用于 Data Box Gateway 设备的订阅。 选择要将 Data Box Gateway 资源部署到的区域。 有关可使用 Azure Stack Edge 资源的所有区域的列表，请参阅[可用的 Azure 产品(按区域)](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)。 选择离要部署设备的地理区域最近的位置。 在“Data Box Gateway”选项中，选择“创建”。  

    ![搜索 Data Box Gateway 服务](media/data-box-gateway-deploy-prep/data-box-gateway-edge-sku.png)

4. 在“基本信息”选项卡上，输入或选择以下“项目详细信息”。  
    
    |设置  |值  |
    |---------|---------|
    |订阅    |系统会根据前面所做的选择自动填充此字段。 订阅将链接到你的计费帐户。 |
    |资源组  |选择现有的组，或创建新组。<br>详细了解 [Azure 资源组](../azure-resource-manager/management/overview.md)。     |

5. 输入或选择以下“实例详细信息”。 

    |设置  |值  |
    |---------|---------|
    |名称   | 用于标识资源的友好名称。<br>该名称的长度必须介于 2 和 50 个字符之间，只能包含字母、数字和连字符。<br> 名称以字母或数字开头和结尾。        |   
    |区域     |有关可使用 Azure Stack Edge 资源的所有区域的列表，请参阅[可用的 Azure 产品(按区域)](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)。 对于 Azure 政府版，可以选择 [Azure 区域](https://azure.microsoft.com/global-infrastructure/regions/)中所列的所有政府区域。 <br> 选择离要部署设备的地理区域最近的位置。|
    
    ![创建 Data Box Gateway 资源](media/data-box-gateway-deploy-prep/data-box-gateway-resource.png)
    
6. 选择“查看 + 创建”  。
 
7. 在“查看 + 创建”选项卡上，查看“定价详细信息”、“使用条款”和资源的详细信息。    选择“创建”  。

    ![查看 Data Box Gateway 资源详细信息](media/data-box-gateway-deploy-prep/data-box-gateway-resource1.png)

创建资源需要几分钟时间。 成功创建并部署资源后，你会收到通知。 选择“转到资源”。 

![查看 Data Box Gateway 资源详细信息](media/data-box-gateway-deploy-prep/data-box-gateway-resource2.png)

## <a name="download-the-virtual-device-image"></a>下载虚拟设备映像

创建 Data Box Gateway 资源后，下载相应的虚拟设备映像，以便在主机系统上预配虚拟设备。 虚拟设备映像特定于操作系统。

> [!IMPORTANT]
> 在 Data Box Gateway 上运行的软件只能结合 Data Box Gateway 资源使用。

在 [Azure 门户](https://portal.azure.com/)中执行以下步骤，以下载虚拟设备映像。

1. 选择创建的资源，然后选择“概述”。  如果有现有的 Azure Data Box Gateway 资源，请选择该资源并转到“概述”。  选择“设备设置”。 

    ![新的 Data Box Gateway 资源](media/data-box-gateway-deploy-prep/data-box-gateway-resource-created.png)

2. 在“下载映像”磁贴上，选择与主机服务器上的操作系统对应的、用于预配 VM 的虚拟设备映像。  映像文件约为 5.6 GB。
   
   * [Windows Server 2012 R2 及更高版本上的适用于 Hyper-V 的 VHDX](https://aka.ms/dbe-vhdx-2012)。
   * [适用于 VMWare ESXi 6.0、6.5 或 6.7 的 VMDK](https://aka.ms/dbe-vmdk)。

    ![下载 Data Box Gateway 虚拟设备映像](media/data-box-gateway-deploy-prep/data-box-gateway-download-image.png)

5. 下载该文件并将其解压缩到本地驱动器，记下解压缩文件所在的位置。


## <a name="get-the-activation-key"></a>获取激活密钥

启动并运行 Data Box Gateway 资源后，需要获取激活密钥。 此密钥用于激活 Data Box Gateway 设备并将其连接到资源。 如果你仍在 Azure 门户中，则现在可以获取此密钥。

1. 选择创建的资源，然后选择“概述”。  在“设备设置”中，转到“配置并激活”磁贴。  

    ![“配置并激活”磁贴](media/data-box-gateway-deploy-prep/data-box-gateway-configure-activate.png)

2. 选择“生成密钥”以创建激活密钥。  选择复制图标复制密钥并将其保存供日后使用。

    ![获取激活密钥](media/data-box-gateway-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - 生成的激活密钥将在三天后过期。
> - 如果密钥已过期，请生成新密钥。 旧密钥不再有效。

## <a name="next-steps"></a>后续步骤

在本教程中，我们已了解有关 Data Box Gateway 的主题，例如：

> [!div class="checklist"]
> * 创建新资源
> * 下载虚拟设备映像
> * 获取激活密钥

请继续学习下一篇教程，了解如何为 Data Box Gateway 预配虚拟机。 根据主机操作系统参阅以下操作的详细说明：

> [!div class="nextstepaction"]
> [在 Hyper-V 中预配 Data Box Gateway](./data-box-gateway-deploy-provision-hyperv.md)

OR

> [!div class="nextstepaction"]
> [在 VMware 中预配 Data Box Gateway](./data-box-gateway-deploy-provision-vmware.md)


