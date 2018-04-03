---
title: 将 ASDK 注册到 Azure | Microsoft Docs
description: 介绍如何将 Azure Stack 注册到 Azure，以实现 Marketplace 联合并报告使用情况。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: a157879fb70a874ed99b39ffcdbfa49a65f6ab7c
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="register-azure-stack-with-azure"></a>将 Azure Stack 注册到 Azure
可将 Azure Stack 开发工具包 (ASDK) 安装注册到 Azure，以便从 Azure 下载 Marketplace 项，并设置向 Microsoft 报告商务数据的功能。 之所以建议注册，是因为这样可以测试重要的 Azure Stack 功能，例如 Marketplace 联合和使用情况报告。 注册 Azure Stack 之后，使用情况将报告给 Azure 商业组件。 用于注册的订阅下会显示此信息。 但是，ASDK 用户无需付费，不管他们报告的用量是多少。


## <a name="register-azure-stack-with-azure"></a>将 Azure Stack 注册到 Azure 
遵循以下步骤将 ASDK 注册到 Azure。

> [!NOTE]
> 所有这些步骤必须在可以访问特权终结点的计算机上运行。 对于 ASDK 来说，该计算机是开发工具包主机。 

在使用这些步骤将 ASDK 注册到 Azure 之前，请确保已安装 Azure Stack PowerShell，并已下载[部署后配置](asdk-post-deploy.md)一文中所述的 Azure Stack 工具。 

1. 以管理员身份打开 PowerShell 控制台。  

2. 运行以下 PowerShell 命令，将 ASDK 安装注册到 Azure（需要同时登录到 Azure 订阅和本地 ASDK 安装）：

    ```PowerShell
    # Add the Azure cloud subscription environment name. Supported environment names are AzureCloud or, if using a China Azure Subscription, AzureChinaCloud.
    Add-AzureRmAccount -EnvironmentName "AzureCloud"

    # Register the Azure Stack resource provider in your Azure subscription
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

    #Import the registration module that was downloaded with the GitHub tools
    Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

    #Register Azure Stack
    $AzureContext = Get-AzureRmContext
    $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
    Set-AzsRegistration `
        -PrivilegedEndpointCredential $CloudAdminCred `
        -PrivilegedEndpoint AzS-ERCS01 `
        -BillingModel Development

3. When the script completes, you should see this message: **Your environment is now registered and activated using the provided parameters.**

    ![](media/asdk-register/1.PNG) 

## Verify the registration was successful
Follow these steps to verify that the ASDK registration with Azure was successful.

1. Sign in to the [Azure Stack administration portal](https://adminportal.local.azurestack.external).

2. Click **Marketplace Management** > **Add from Azure**.

    ![](media/asdk-register/2.PNG) 

3. If you see a list of items available from Azure, your activation was successful.

    ![](media/asdk-register/3.PNG) 

## Next steps
[Add an Azure Stack marketplace item](asdk-marketplace-item.md)