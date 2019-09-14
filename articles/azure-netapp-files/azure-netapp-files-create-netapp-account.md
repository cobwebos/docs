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
ms.topic: conceptual
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: 25cae58663f6fa7ef27995c10509eb33e49dd4c7
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2019
ms.locfileid: "70012571"
---
# <a name="create-a-netapp-account"></a>创建 NetApp 帐户
创建 NetApp 帐户将能够设置容量池并随后创建卷。 使用“Azure NetApp 文件”边栏选项卡来创建新的 NetApp 帐户。

## <a name="before-you-begin"></a>开始之前
你必须收到 Azure NetApp 文件团队发送的一封电子邮件，确认你已被授予对该服务的访问权限。 请参阅[提交候补请求以访问服务](azure-netapp-files-register.md#waitlist)。

还必须使用 NetApp 资源提供程序注册订阅。 请参阅[注册 NetApp 资源提供程序](azure-netapp-files-register.md#resource-provider)。

## <a name="steps"></a>步骤 

1. 登录到 Azure 门户。 
2. 使用以下方法之一访问“Azure NetApp 文件”边栏选项卡：  
   * 在 Azure 门户的搜索框中搜索“Azure NetApp 文件”。  
   * 在导航栏中单击“所有服务”，然后筛选找到“Azure NetApp 文件”。  

   可以通过单击“Azure NetApp 文件”边栏选项卡旁边的星星图标来“收藏”它。 

3. 单击“+ 添加”来创建新的 NetApp 帐户。  
   此时将出现“新建 NetApp 帐户”窗口。  

4. 提供 NetApp 帐户的以下信息： 
   * **帐户名称**  
     为订阅指定一个唯一名称。
   * **订阅**  
     从现有的订阅中选择一个订阅。
   * **资源组**   
     使用现有资源组，或创建一个新的资源组。
   * **Location**  
     选择要将帐户及其子资源放置到的区域。  

     ![新建 NetApp 帐户](../media/azure-netapp-files/azure-netapp-files-new-netapp-account.png)


5. 单击“创建”。     
   你创建的 NetApp 帐户现在出现在“Azure NetApp 文件”边栏选项卡中。 

> [!NOTE] 
> 如果尚未获得 Azure NetApp 文件服务的访问权限，则当你尝试创建第一个 NetApp 帐户时，将收到以下错误：  
>
> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"NotFound","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidResourceType\",\r\n \"message\": \"The resource type could not be found in the namespace 'Microsoft.NetApp' for api version '2017-08-15'.\"\r\n }\r\n}"}]}`

## <a name="next-steps"></a>后续步骤  

[设置容量池](azure-netapp-files-set-up-capacity-pool.md)

