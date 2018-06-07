---
title: 将 Kubernetes 群集添加到 Azure Stack Marketplace | Microsoft Docs
description: 了解如何将 Kubernetes 群集添加到 Azure Stack Marketplace。
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
ms.date: 05/29/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: 0ba0e1f3d9e0f1cbb6ba4109a21fc29dc41df5fc
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34603447"
---
# <a name="add-a-kubernetes-cluster-to-the-azure-stack-marketplace"></a>将 Kubernetes 群集添加到 Azure Stack Marketplace

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

> [!note]  
> Azure Stack 上的 Azure 容器服务 (ACS) Kubernetes 目前为个人预览版。 若要请求访问根据本文中的说明进行操作所需的 Kubernetes Marketplace 项，请[提交请求来获取访问权限](https://aka.ms/azsk8)。

可以将 Kubernetes 群集作为 Marketplace 项提供给用户。 用户可以通过单个协调的操作部署 Kubernetes。

下面的文章着眼于使用 Azure 资源管理器模板为独立的 Kubernetes 群集部署和预配资源。 在开始之前，请检查 Azure Stack 和全球 Azure 租户设置。 收集关于 Azure Stack 的必需信息。 将所需资源添加到租户和 Azure Stack Marketplace。 群集依赖于 Ubuntu 服务器、自定义脚本以及要放置在 Marketplace 中的 Kubernetes 群集项。

## <a name="create-a-plan-an-offer-and-a-subscription"></a>创建计划、套餐和订阅

为 Kubernetes 群集 Marketplace 项创建计划、套餐和订阅。 也可以使用现有计划和套餐。

1. 登录到[管理门户](https://adminportal.local.azurestack.external)。

2. 创建一个计划作为基本计划。 有关说明，请参阅[在 Azure Stack 中创建计划](azure-stack-create-plan.md)。

3. 创建套餐。 有关说明，请参阅[在 Azure Stack 中创建套餐](azure-stack-create-offer.md)。

4. 选择“套餐”，并找到你创建的套餐。

5. 选择“套餐”边栏选项卡中的“概述”。

6. 选择“更改状态”。 选择“公共”。

7. 选择“+ 新建” > “套餐和计划” > “订阅”来创建新订阅。

    a. 输入**显示名称**。

    b. 输入**用户**。 请使用与你的租户关联的 Azure AD 帐户。

    c. **提供商说明**

    d. 将“目录租户”设置为你的 Azure Stack 的 Azure AD 租户。 

    e. 选择“套餐”。 选择你创建的套餐的名称。 记下订阅 ID。

## <a name="add-an-ubuntu-server-image"></a>添加 Ubuntu 服务器映像

将以下 Ubuntu 服务器映像添加到 Marketplace：

1. 登录到[管理门户](https://adminportal.local.azurestack.external)。

2. 选择“更多服务” > “Marketplace 管理”。

3. 选择“+ 从 Azure 添加”。

4. 输入 `UbuntuServer`。

5. 选择具有以下配置文件的服务器：
    - **发布者**：Canonical
    - **套餐**：UbuntuServer
    - **SKU**：16.04-LTS
    - **版本**：16.04.201802220

    > [!Note]  
    > 可能会列出 Ubuntu Server 16.04 LTS 的多个版本。 你需要添加匹配的版本。 Kubernetes 群集需要该项的确切版本。

6. 选择“下载”。

## <a name="add-a-custom-script-for-linux"></a>添加适用于 Linux 的自定义脚本

从 Marketplace 中添加 Kubernetes 群集：

1. 打开[管理门户](https://adminportal.local.azurestack.external)。

2. 选择“更多服务” > “Marketplace 管理”。

3. 选择“+ 从 Azure 添加”。

4. 输入 `Custom Script for Linux`。

5. 选择具有以下配置文件的服务器：
    - **套餐**：适用于 Linux 2.0 的自定义脚本
    - **版本**：2.0.3
    - **发布者**：Microsoft Corp

    > [!Note]  
    > 可能会列出适用于 Linux 的自定义脚本的多个版本。 你需要添加匹配的版本。 Kubernetes 群集需要该项的确切版本。

6. 选择“下载”。


## <a name="add-the-kubernetes-cluster-to-the-marketplace"></a>将 Kubernetes 群集添加到 Marketplace

1. 打开[管理门户](https://adminportal.local.azurestack.external)。

2. 选择“更多服务” > “Marketplace 管理”。

3. 选择“+ 从 Azure 添加”。

4. 输入 `Kubernetes Cluster`。

5. 选择 `Kubernetes Cluster`。

6. 选择“下载”。

    > [!note]  
    > Marketplace 项可能需要花费五分钟才会出现在 Marketplace 中。

    ![Kubernetes 群集](user\media\azure-stack-solution-template-kubernetes-deploy\marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes-cluster"></a>更新或删除 Kubernetes 群集 

更新 Kubernetes 群集项时，需要删除 Marketplace 中的项。 然后，可以按照本文中的说明将 Kubernetes 群集添加到 Marketplace。

若要删除 Kubernetes 群集项，请执行以下操作：

1. 记下当前项的名称，例如 `Microsoft.AzureStackKubernetesCluster.0.1.0`

2. 使用 PowerShell 连接到 Azure Stack。

3. 使用以下 PowerShell cmdlet 删除项：

    ```PowerShell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.1.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>后续步骤

[将 Kubernetes 群集部署到 Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)



[在 Azure Stack 中提供服务概述](azure-stack-offer-services-overview.md)