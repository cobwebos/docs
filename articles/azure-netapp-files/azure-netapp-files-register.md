---
title: 注册 Azure NetApp 文件 | Microsoft Docs
description: 介绍如何注册以使用 Azure NetApp 文件。
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
ms.date: 05/06/2019
ms.author: b-juche
ms.openlocfilehash: fbe0b82008d7b15332c4e2cd62c49c611f20fe89
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2019
ms.locfileid: "65794696"
---
# <a name="register-for-azure-netapp-files"></a>注册 Azure NetApp 文件

> [!IMPORTANT] 
> 在注册之前 Azure NetApp 文件资源提供程序，你应已收到一封电子邮件从 Azure NetApp 文件团队确认您已被授予对服务的访问。 

在本文中，了解如何注册 Azure NetApp 文件，以便你可以开始使用该服务。

## <a name="waitlist"></a>提交被插入等待队列请求用于访问服务

1. 提交被插入等待队列的访问通过 Azure NetApp 文件服务请求[Azure NetApp 文件被插入等待队列提交页面](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u)。 

    候补名单注册不保证我们的服务立即访问。 

2. 在继续执行其他任务之前等待来自 Azure NetApp 文件团队的官方确认电子邮件。 

## <a name="resource-provider"></a>注册 NetApp 资源提供程序

若要使用该服务，必须为 Azure NetApp 文件注册 Azure 资源提供程序。

> [!NOTE] 
> 你将能够成功即使没有被授予访问权限的服务注册 NetApp 资源提供程序。 但是，而无需访问授权的任何 Azure 门户或 API 创建 NetApp 帐户或任何其他 Azure NetApp 文件资源请求将被拒绝并显示以下错误：  
>
> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"NotFound","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidResourceType\",\r\n \"message\": \"The resource type could not be found in the namespace 'Microsoft.NetApp' for api version '2017-08-15'.\"\r\n }\r\n}"}]}`


1. 从 Azure 门户中，单击右上角的 Azure Cloud Shell 图标：

      ![Azure Cloud Shell 图标](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. 如果你的 Azure 帐户上有多个订阅，请选择已列入 Azure NetApp 文件的允许列表的订阅：
    
        az account set --subscription <subscriptionId>

3. 在 Azure Cloud Shell 控制台中，输入以下命令来验证你的订阅是否已列入允许列表：
    
        az feature list | grep NetApp

   命令输出如下所示：
   
       "id": "/subscriptions/<SubID>/providers/Microsoft.Features/providers/Microsoft.NetApp/features/publicPreviewADC",  
       "name": "Microsoft.NetApp/publicPreviewADC" 
       
   `<SubID>` 是你的订阅 ID。

    如果您看不到功能名称`Microsoft.NetApp/publicPreviewADC`，没有对服务的访问。 在此步骤处停止。 按照中的说明进行操作[提交用于访问服务被插入等待队列请求](#waitlist)才能继续操作之前的请求服务访问权限。 

4. 在 Azure Cloud Shell 控制台中，输入以下命令来注册 Azure 资源提供程序： 
    
        az provider register --namespace Microsoft.NetApp --wait

   `--wait` 参数指示控制台等待注册完成。 注册过程可能需要一些时间才能完成。

5. 在 Azure Cloud Shell 控制台中，输入以下命令来验证 Azure 资源提供程序是否已注册： 
    
        az provider show --namespace Microsoft.NetApp

   命令输出如下所示：
   
        {
        "id": "/subscriptions/<SubID>/providers/Microsoft.NetApp",
        "namespace": "Microsoft.NetApp", 
        "registrationState": "Registered", 
        "resourceTypes": […. 

   `<SubID>` 是你的订阅 ID。  `state` 参数值表示 `Registered`。

6. 从 Azure 门户中，单击“订阅”边栏选项卡。
7. 在“订阅”边栏选项卡中，单击你的订阅 ID。 
8. 在订阅设置中，单击“资源提供程序”来验证 Microsoft.NetApp 提供程序是否指示了“已注册”状态： 

      ![已注册的 Microsoft.NetApp](../media/azure-netapp-files/azure-netapp-files-registered-resource-providers.png)


## <a name="next-steps"></a>后续步骤

[创建 NetApp 帐户](azure-netapp-files-create-netapp-account.md)