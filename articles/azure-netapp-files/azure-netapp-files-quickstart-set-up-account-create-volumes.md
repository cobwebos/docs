---
title: 设置 Azure NetApp 文件，然后创建一个卷 |Microsoft Docs
description: 介绍如何快速设置 Azure NetApp 文件，然后创建一个卷。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstarts
ms.date: 2/20/2019
ms.author: b-juche
ms.openlocfilehash: 363589116cc7b936358ce0b16cbaba9a42c66234
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58078068"
---
# <a name="set-up-azure-netapp-files-and-create-a-volume"></a>设置 Azure NetApp 文件并创建卷 

本文介绍如何快速设置 Azure NetApp 文件，并创建卷。 

## <a name="before-you-begin"></a>开始之前 

你需要加入公共预览版计划并且列入允许列表，然后才能访问 Microsoft.NetApp 资源提供程序。 有关加入公共预览版计划的详细信息，请参阅 [Azure NetApp 文件公共预览版注册页](https://aka.ms/nfspublicpreview)。 

## <a name="register-for-azure-netapp-files-and-netapp-resource-provider"></a>注册 Azure 的 NetApp 文件和 NetApp 资源提供程序

1. 从 Azure 门户中，单击右上角上的 Azure Cloud Shell 图标。

      ![Azure Cloud Shell 图标](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. 指定已被列入 Azure NetApp 文件的允许列表的订阅：
    
        az account set --subscription <subscriptionId>

3. 注册 Azure 资源提供程序： 
    
        az provider register --namespace Microsoft.NetApp --wait  

    注册过程可能需要一些时间才能完成。

## <a name="create-a-netapp-account"></a>创建 NetApp 帐户

1. 在 Azure 门户的搜索框中，输入**Azure NetApp 文件**，然后选择**Azure NetApp 文件 （预览版）** 从显示的列表。

      ![选择 Azure 的 NetApp 文件](../media/azure-netapp-files/azure-netapp-files-select-azure-netapp-files.png)

2. 单击“+ 添加”来创建新的 NetApp 帐户。

     ![创建新的 NetApp 帐户](../media/azure-netapp-files/azure-netapp-files-create-new-netapp-account.png)

3. 在新的 NetApp 帐户窗口中，提供以下信息： 
   1. 输入**myaccount1**帐户名称。 
   2. 选择订阅。
   3. 选择**新建**若要创建新的资源组。 输入**myRG1**为资源组名称。 单击“确定”。 
   4. 选择你帐户的位置。  

      ![新的 NetApp 帐户窗口](../media/azure-netapp-files/azure-netapp-files-new-account-window.png)  

      ![资源组窗口](../media/azure-netapp-files/azure-netapp-files-resource-group-window.png)

4. 单击**创建**若要创建新的 NetApp 帐户。

## <a name="set-up-a-capacity-pool"></a>设置容量池

1. 从 Azure NetApp 文件管理边栏选项卡，选择你的 NetApp 帐户 (**myaccount1**)。

    ![选择 NetApp 帐户](../media/azure-netapp-files/azure-netapp-files-select-netapp-account.png)  

2. 从 Azure NetApp 文件管理边栏选项卡的 NetApp 帐户，单击**容量池**。

    ![单击容量池](../media/azure-netapp-files/azure-netapp-files-click-capacity-pools.png)  

3. 单击 **+ 将池添加**。 

    ![单击添加池](../media/azure-netapp-files/azure-netapp-files-click-add-pools.png)  

4. 提供容量池的信息： 
    1. 输入**mypool1**与池的名称。
    2. 选择**高级**的服务级别。 
    3. 指定**4 (TiB)** 作为池大小。 

5. 单击“确定”。

## <a name="create-a-volume-for-azure-netapp-files"></a>为 Azure NetApp 文件创建卷

1. 从 Azure NetApp 文件管理边栏选项卡的 NetApp 帐户，单击**卷**。

    ![单击卷](../media/azure-netapp-files/azure-netapp-files-click-volumes.png)  

2. 单击“+ 添加卷”。

    ![单击添加卷](../media/azure-netapp-files/azure-netapp-files-click-add-volumes.png)  

3. 在创建卷窗口中，提供该卷的信息： 
   1. 输入**myvol1**作为卷名称。 
   2. 输入**myfilepath1**作为文件路径将用于创建该卷的导出路径。
   3. 选择容量池 (**mypool1**)。
   4. 对于配额使用默认值。 
   5. 在虚拟网络中，单击**新建**若要创建新的 Azure 虚拟网络 (Vnet)。  然后填写以下信息：
       * 输入**myvnet1**作为 Vnet 名称。
       * 指定的地址空间，例如，10.7.0.0/16。
       * 输入**myANFsubnet**作为子网名称。
       * 指定子网地址范围，例如，10.7.0.0/24。
       * 选择**Microsoft.NetApp/volumes**为子网委派。
       * 单击**确定**以创建该 Vnet。
   6. 在子网中，选择新创建的 Vnet (**myvnet1**) 作为委托子网。

      ![创建卷窗口](../media/azure-netapp-files/azure-netapp-files-create-volume-window.png)  

      ![创建虚拟网络窗口](../media/azure-netapp-files/azure-netapp-files-create-virtual-network-window.png)  

4. 单击“查看 + 创建”。

    ![查看和创建窗口](../media/azure-netapp-files/azure-netapp-files-review-and-create-window.png)  

5. 查看该卷的信息，然后单击**创建**。  
    创建的卷将显示在卷边栏选项卡。

    ![创建卷](../media/azure-netapp-files/azure-netapp-files-create-volume-created.png)  

## <a name="next-steps"></a>后续步骤  

* [了解 Azure NetApp 文件的存储层次结构](azure-netapp-files-understand-storage-hierarchy.md)
* [通过使用 Azure 的 NetApp 文件管理卷](azure-netapp-files-manage-volumes.md) 
