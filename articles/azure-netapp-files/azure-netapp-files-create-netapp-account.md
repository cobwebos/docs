---
title: 创建用于访问 Azure NetApp 文件的 NetApp 帐户 | Microsoft Docs
description: 介绍了如何访问 Azure NetApp 文件和创建 NetApp 帐户，以便可以设置容量池并创建卷。
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
ms.topic: get-started-article
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: ad8cc550ce69e4dc4c19a569718fa873a65b3620
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2018
ms.locfileid: "39010338"
---
# <a name="create-a-netapp-account"></a>创建 NetApp 帐户
创建 NetApp 帐户将能够设置容量池并随后创建卷。 使用“Azure NetApp 文件”边栏选项卡来创建新的 NetApp 帐户。

## <a name="before-you-begin"></a>开始之前
必须已将你列入访问 Microsoft.NetApp Azure 资源提供程序的允许列表并针对使用 Azure NetApp 文件服务进行了配置。  

[Azure NetApp 文件公共预览版注册页面](https://aka.ms/nfspublicpreview)。 

## <a name="steps"></a>步骤 

1. 从你的预览版邀请中找到预览版 Azure 门户 URL，然后登录到该门户。 
2.  使用以下方法之一访问“Azure NetApp 文件”边栏选项卡：  
  * 在 Azure 门户的搜索框中搜索“Azure NetApp 文件”。  
  * 在导航栏中单击“所有服务”，然后筛选找到“Azure NetApp 文件”。  

  可以通过单击“Azure NetApp 文件”边栏选项卡旁边的星星图标来“收藏”它。 

3. 单击“+ 添加”来创建新的 NetApp 帐户。  
  此时将出现“新建 NetApp 帐户”窗口。  

4. 提供 NetApp 帐户的以下信息： 
  * **帐户名称**  
    为订阅指定一个唯一名称。
  *  **订阅**  
    从现有的订阅中选择一个订阅。
  * **资源组**   
    使用现有资源组，或创建一个新的资源组。
  * **位置**  
    选择要将帐户及其子资源放置到的区域。  
    目前，Azure NetApp 文件服务仅在美国东部区域中受支持。  

    ![新建 NetApp 帐户](../media/azure-netapp-files/azure-netapp-files-new-netapp-account.png)


5. 单击“创建”。     
  你创建的 NetApp 帐户现在出现在“Azure NetApp 文件”边栏选项卡中。 

## <a name="next-steps"></a>后续步骤  

1. [设置容量池](azure-netapp-files-set-up-capacity-pool.md)
2. [为 Azure NetApp 文件创建卷](azure-netapp-files-create-volumes.md)
3. [为卷配置导出策略（可选）](azure-netapp-files-configure-export-policy.md)
