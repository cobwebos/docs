---
title: 将实现 Kubernetes 群集添加到 Azure 的堆栈 Marketplace |Microsoft 文档
description: 了解如何将 Kubernetes 群集添加到 Azure 堆栈应用商店。
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
ms.date: 05/08/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: c66b0d7ea5ade90c6bb8f88006f2a09bd407deaa
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
---
# <a name="add-a-kubernetes-cluster-to-the-azure-stack-marketplace"></a>将实现 Kubernetes 群集添加到 Azure 的堆栈 Marketplace

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

> [!note]  
> Azure 堆栈上的 Azure 容器服务 (ACS) Kubernetes 是处于特邀预览阶段。 若要请求访问在本文中，执行说明所需的 Kubernetes 应用商店项[提交请求以获取访问权限](https://aka.ms/azsk8)。

到你的用户，你可以为应用商店项中提供实现 Kubernetes 群集。 你的用户可以将 Kubernetes 部署在单个协调的操作。

以下文章查看使用 Azure 资源管理器模板部署和设置独立 Kubernetes 群集的资源。 在开始之前，检查你的 Azure 堆栈和全局 Azure 租户设置。 收集有关你的 Azure 堆栈所需的信息。 将所需的资源添加到你的租户和 Azure 堆栈应用商店。 群集取决于 Ubuntu 服务器、 自定义脚本，以及要在应用商店中的实现 Kubernetes 群集项。

## <a name="create-a-plan-an-offer-and-a-subscription"></a>创建计划、 产品和订阅

创建一个计划，优惠后，以及实现 Kubernetes 群集应用商店项目的订阅。 此外可以使用现有计划，并提供。

1. 登录到[管理门户。](https://adminportal.local.azurestack.external)

2. 创建计划为基的计划。 有关说明，请参阅[Azure 堆栈中创建计划](azure-stack-create-plan.md)。

3. 创建提议。 有关说明，请参阅[在 Azure 堆栈中创建提议](azure-stack-create-offer.md)。

4. 选择**提供**，并找到你创建的提议。

5. 选择**概述**优惠边栏选项卡中。

6. 选择**更改状态**。 选择**公共**。

7. 选择 **+ 新建** > **提供和计划** > **订阅**要创建新订阅。

    a. 输入**显示名称**。

    b. 输入**用户**。 使用与你的租户关联的 Azure AD 帐户。

    c. **访问接口说明**

    d. 设置**目录租户**到 Azure 堆栈的 Azure AD 租户。 

    e. 选择**提供**。 选择你创建的产品的名称。 记下订阅 id。

## <a name="add-an-ubuntu-server-image"></a>添加 Ubuntu 服务器映像

将以下的 Ubuntu Server 映像添加到应用商店：

1. 登录到[管理门户](https://adminportal.local.azurestack.external)。

2. 选择“更多服务” > “Marketplace 管理”。

3. 选择 **+ 添加从 Azure**。

4. 输入 `UbuntuServer`。

5. 选择具有以下配置文件的服务器：
    - **发布服务器**： 规范
    - **提供**: UbuntuServer
    - **SKU**: 16.04 LTS
    - **版本**: 16.04.201802220

6. 选择**下载。**

## <a name="add-a-custom-script-for-linux"></a>添加适用于 Linux 的自定义脚本

从应用商店添加 Kubernetes 群集：

1. 打开[管理门户](https://adminportal.local.azurestack.external)。

2. 选择“更多服务” > “Marketplace 管理”。

3. 选择 **+ 添加从 Azure**。

4. 输入 `Custom Script for Linux`。

5. 选择以下配置文件的脚本：
    - **提供**： 适用于 Linux 2.0 的自定义脚本
    - **版本**: 2.0.3
    - **发布服务器**: Microsoft Corp

6. 选择**下载。**


## <a name="add-the-kubernetes-cluster-to-the-marketplace"></a>将实现 Kubernetes 群集添加到应用商店

1. 打开[管理门户](https://adminportal.local.azurestack.external)。

2. 选择“更多服务” > “Marketplace 管理”。

3. 选择 **+ 添加从 Azure**。

4. 输入 `Kubernetes Cluster`。

5. 选择 `Kubernetes Cluster`。

6. 选择**下载。**

    > [!note]  
    > 可能需要五分钟的时间才会显示在应用商店的应用商店项。

    ![Kubernetes 群集](user\media\azure-stack-solution-template-kubernetes-deploy\marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes-cluster"></a>更新或删除 Kubernetes 群集 

在更新 Kubernetes 群集项时，你将需要删除应用商店中的项。 然后，可以按照本文中的说明将 Kubernetes 群集添加到应用商店。

若要删除 Kubernetes 群集项：

1. 如记下的当前项的名称 `Microsoft.AzureStackKubernetesCluster.0.1.0`

2. 连接到使用 PowerShell 的 Azure 堆栈。

3. 使用以下 PowerShell cmdlet 删除项：

    ```PowerShell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.1.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>后续步骤

[将实现 Kubernetes 群集部署到 Azure 的堆栈](/user/azure-stack-solution-template-kubernetes-deploy.md)

[在 Azure Stack 中提供服务概述](azure-stack-offer-services-overview.md)