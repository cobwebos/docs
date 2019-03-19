---
title: 注册 Azure NetApp 文件 | Microsoft Docs
description: 介绍了如何提交请求以在 Azure NetApp 文件服务中进行登记。
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
ms.date: 01/04/2018
ms.author: b-juche
ms.openlocfilehash: 86c016a5dbcc0d78378e59bc6b3606ddf2c54f64
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58122329"
---
# <a name="register-for-azure-netapp-files"></a>注册 Azure NetApp 文件
在使用 Azure NetApp 文件之前，必须提交请求以在 Azure NetApp 文件服务中进行登记。  在登记后，进行注册以使用该服务。

## <a name="request-to-enroll-in-the-service"></a>请求在服务中进行登记
你需要加入公共预览版计划并且列入允许列表，然后才能访问 Microsoft.NetApp 资源提供程序。 有关加入公共预览版计划的详细信息，请参阅 [Azure NetApp 文件公共预览版注册页](https://aka.ms/nfspublicpreview)。 


## <a name="register-the-netapp-resource-provider"></a>注册 NetApp 资源提供程序

若要使用该服务，必须为 Azure NetApp 文件注册 Azure 资源提供程序。 

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