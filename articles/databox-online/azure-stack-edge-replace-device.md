---
title: 替换 Azure Stack Edge Pro 设备 |Microsoft Docs
description: 描述如何获取 Azure Stack Edge Pro 设备的替换。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 07/20/2020
ms.author: alkohli
ms.openlocfilehash: ec16a2b42b818e96399b8fdbad4a0951f84ef825
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90893890"
---
# <a name="replace-your-azure-stack-edge-pro-device"></a>替换 Azure Stack Edge Pro 设备

本文介绍如何获取 Azure Stack Edge Pro 设备的替换。 如果现有设备有硬件故障或需要升级，则需要替换设备。 


在本文中，学习如何：

> [!div class="checklist"]
>
> * 为硬件问题打开支持票证
> * 在 Azure 门户中为替换设备创建新资源
> * 安装，激活更换设备
> * 返回原始设备

## <a name="open-a-support-ticket"></a>打开支持票证

如果现有设备有硬件故障，请打开支持票证。 Microsoft 支持部门将确定现场可替换装置 (FRU) 不适用于此情况，或设备需要硬件升级。 无论是哪种情况，支持部门都将订购替换设备。

1. 向 Microsoft 支持部门打开支持票证，表示你希望退回设备。 选择 "问题类型" 作为 **Azure Stack Edge Pro 硬件**"。

    ![创建支持票证](media/azure-stack-edge-replace-device/open-support-ticket-1.png)  

2. Microsoft 支持部门的工程师将与你联系。 提供发货详细信息。
<!--3. If you need a return shipping box, you can request it. Answer **Yes** to the question **Need an empty box to return**.-->


## <a name="create-a-resource-for-replacement-device"></a>为替代设备创建资源

按照以下步骤创建资源。

1. 按照 [创建新资源](azure-stack-edge-deploy-prep.md#create-a-new-resource) 中的步骤为替代设备创建资源。 

2. 请确保选中此复选框，使其 **具有 Azure Stack Edge Pro 设备**。 

    ![替换设备的资源](media/azure-stack-edge-replace-device/replace-resource-1.png)  

## <a name="install-and-activate-the-replacement-device"></a>安装并激活更换设备

请按照以下步骤安装和激活更换设备：

1. [安装设备](azure-stack-edge-deploy-install.md)。

2. 针对之前创建的新资源[激活设备](azure-stack-edge-deploy-connect-setup-activate.md)。

## <a name="return-your-existing-device"></a>返回现有设备

按照所有步骤操作，退回原始设备：

1. [清除设备上的数据](azure-stack-edge-return-device.md#erase-data-from-the-device)。
2. 启动原始设备的[设备返回](azure-stack-edge-return-device.md#initiate-device-return)。
3. [安排取件](azure-stack-edge-return-device.md#schedule-a-pickup)。
4. [删除](azure-stack-edge-return-device.md#delete-the-resource)与退回的设备相关联的资源。


## <a name="next-steps"></a>后续步骤

- 了解如何 [返回 Azure Stack Edge Pro 设备](azure-stack-edge-return-device.md)。
