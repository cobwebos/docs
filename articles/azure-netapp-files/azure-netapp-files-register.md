---
title: 注册 Azure NetApp 文件 | Microsoft Docs
description: 描述如何注册以使用 Azure NetApp 文件。
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
ms.openlocfilehash: 6f5d84dea2e835fd12a062b628181354295ed9f6
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79274055"
---
# <a name="register-for-azure-netapp-files"></a>注册 Azure NetApp 文件

> [!IMPORTANT] 
> 在注册 Azure NetApp 文件资源提供程序之前，你必须收到来自 Azure NetApp 文件团队的一封电子邮件，确认你已被授予对该服务的访问权限。 

本文介绍如何注册 Azure NetApp 文件，以便你可以开始使用该服务。

## <a name="waitlist"></a>提交用于访问服务的候补请求

1. 提交候补请求，通过[Azure Netapp 文件候补提交页](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u)访问 Azure netapp 文件服务。 

    候补注册不保证立即访问服务。 

2. 在继续执行其他任务之前，请先从 Azure NetApp 文件团队等待官方确认电子邮件。 

## <a name="resource-provider"></a>注册 NetApp 资源提供程序

若要使用该服务，必须为 Azure NetApp 文件注册 Azure 资源提供程序。

> [!NOTE] 
> 即使没有为服务授予访问权限，也能成功注册 NetApp 资源提供程序。 但是，如果没有访问授权，则任何 Azure 门户或 API 请求创建 NetApp 帐户或任何其他 Azure NetApp 文件资源都将被拒绝，并出现以下错误：  
>
> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"NotFound","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidResourceType\",\r\n \"message\": \"The resource type could not be found in the namespace 'Microsoft.NetApp' for api version '2017-08-15'.\"\r\n }\r\n}"}]}`


1. 从 Azure 门户中，单击右上角的 Azure Cloud Shell 图标：

      ![Azure Cloud Shell 图标](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. 如果你的 Azure 帐户上有多个订阅，请选择已列入 Azure NetApp 文件的允许列表的订阅：
    
        az account set --subscription <subscriptionId>

3. 在 Azure Cloud Shell 控制台中，输入以下命令来验证你的订阅是否已列入允许列表：
    
        az feature list | grep NetApp

   命令输出如下所示：
   
       "id": "/subscriptions/<SubID>/providers/Microsoft.Features/providers/Microsoft.NetApp/features/ANFGA",  
       "name": "Microsoft.NetApp/ANFGA" 
       
   `<SubID>` 是你的订阅 ID。

    如果看不到功能名称 `Microsoft.NetApp/ANFGA`，则无法访问该服务。 停止执行此步骤。 按照[提交用于访问服务的候补请求](#waitlist)中的说明来请求服务访问，然后再继续。 

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