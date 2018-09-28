---
title: 将 Kubernetes 添加到 Azure Stack Marketplace |Microsoft Docs
description: 了解如何将 Kubernetes 添加到 Azure Stack Marketplace。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: 978cce4642dc61143bd829fcae03357fd8c969c2
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2018
ms.locfileid: "47405506"
---
# <a name="add-kubernetes-to-the-azure-stack-marketplace"></a>将 Kubernetes 添加到 Azure Stack Marketplace

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

> [!note]  
> Azure Stack 上的 Kubernetes 处于预览状态。

你可以向用户提供作为 Marketplace 项的 Kubernetes。 用户可以通过单个协调的操作部署 Kubernetes。

下面的文章着眼于使用 Azure 资源管理器模板为独立的 Kubernetes 群集部署和预配资源。 Kubernetes 群集 Marketplace 项 0.3.0 需要 1808年的 Azure Stack 版本。 在开始之前，请检查 Azure Stack 和全球 Azure 租户设置。 收集关于 Azure Stack 的必需信息。 将所需资源添加到租户和 Azure Stack 市场。 群集取决于 Ubuntu 服务器、 自定义脚本，以及要在 marketplace 中的 Kubernetes 项。

## <a name="create-a-plan-an-offer-and-a-subscription"></a>创建计划、套餐和订阅

创建一个计划、 产品/服务，以及 Kubernetes Marketplace 项的订阅。 也可以使用现有计划和套餐。

1. 登录到[管理门户](https://adminportal.local.azurestack.external)。

1. 创建一个计划作为基本计划。 有关说明，请参阅[在 Azure Stack 中创建计划](azure-stack-create-plan.md)。

1. 创建套餐。 有关说明，请参阅[在 Azure Stack 中创建套餐](azure-stack-create-offer.md)。

1. 选择“套餐”，并找到你创建的套餐。

1. 选择“套餐”边栏选项卡中的“概述”。

1. 选择“更改状态”。 选择“公共”。

1. 选择 **+ 创建资源** > **提供了和计划** > **订阅**若要创建新的订阅。

    a. 输入**显示名称**。

    b. 输入**用户**。 请使用与你的租户关联的 Azure AD 帐户。

    c. **提供商说明**

    d. 将“目录租户”设置为你的 Azure Stack 的 Azure AD 租户。 

    e. 选择“套餐”。 选择你创建的套餐的名称。 记下订阅 ID。

## <a name="add-an-ubuntu-server-image"></a>添加 Ubuntu 服务器映像

将以下 Ubuntu 服务器映像添加到市场：

1. 登录到[管理门户](https://adminportal.local.azurestack.external)。

1. 选择**所有服务**，然后在**管理**类别中，选择**Marketplace 管理**。

1. 选择“+ 从 Azure 添加”。

1. 输入 `UbuntuServer`。

1. 选择具有以下配置文件的服务器的最新版本：
    - **发布者**：Canonical
    - **套餐**：UbuntuServer
    - **SKU**：16.04-LTS

1. 选择“下载”。

## <a name="add-a-custom-script-for-linux"></a>添加适用于 Linux 的自定义脚本

通过 Marketplace 添加 Kubernetes:

1. 打开[管理门户](https://adminportal.local.azurestack.external)。

1. 选择**所有服务**，然后在**管理**类别中，选择**Marketplace 管理**。

1. 选择“+ 从 Azure 添加”。

1. 输入 `Custom Script for Linux`。

1. 选择具有以下配置文件的服务器：
    - **套餐**：适用于 Linux 2.0 的自定义脚本
    - **版本**: 2.0.6
    - **发布者**：Microsoft Corp

    > [!Note]  
    > 可能会列出适用于 Linux 的自定义脚本的多个版本。 你需要添加匹配的版本。 Kubernetes 需要的项的确切版本。

1. 选择“下载”。


## <a name="add-kubernetes-to-the-marketplace"></a>将 Kubernetes 添加到 marketplace

1. 打开[管理门户](https://adminportal.local.azurestack.external)。

1. 选择**所有服务**，然后在**管理**类别中，选择**Marketplace 管理**。

1. 选择“+ 从 Azure 添加”。

1. 输入 `Kubernetes`。

1. 选择 `Kubernetes Cluster`。

1. 选择“下载”。

    > [!note]  
    > 市场项可能需要花费五分钟才会出现在市场中。

    ![Kubernetes](user\media\azure-stack-solution-template-kubernetes-deploy\marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes"></a>更新或删除 Kubernetes 

当更新 Kubernetes 项时，您将需要删除在 Marketplace 中的项。 然后，可以按照这篇文章中的说明添加到 marketplace 的 Kubernetes。

若要删除的 Kubernetes 项：

1. 连接到使用 PowerShell 的 Azure Stack 操作员。 有关说明，请参阅[连接到 Azure Stack 操作员的 PowerShell](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-configure-admin)。

2. 在库中查找当前的 Kubernetes 群集项。

    ```PowerShell  
    Get-AzsGalleryItem | Select Name
    ```
    
3. 记下当前项的名称，例如 `Microsoft.AzureStackKubernetesCluster.0.2.0`

4. 使用以下 PowerShell cmdlet 删除项：

    ```PowerShell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.3.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>后续步骤

[将 Kubernetes 部署到 Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)



[在 Azure Stack 中提供服务概述](azure-stack-offer-services-overview.md)