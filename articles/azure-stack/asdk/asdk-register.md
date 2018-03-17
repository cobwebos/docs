---
title: "向 Azure 注册 ASDK |Microsoft 文档"
description: "描述如何使用 Azure 应用商店联合和使用情况报告以便注册 Azure 堆栈。"
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 9e2dbc71f6424b87945e346a42c86d4cde7f740e
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/17/2018
---
# <a name="register-azure-stack-with-azure"></a>将 Azure Stack 注册到 Azure
您可以注册 Azure 下载从 Azure 应用商店项以及设置商务数据向 Microsoft 报告回 Azure 堆栈开发工具包 (ASDK) 安装。 建议注册，因为它使你可以测试重要 Azure 堆栈功能，例如，应用商店联合和使用情况报告。 注册 Azure Stack 之后，使用情况将报告给 Azure 商业组件。 用于注册的订阅下会显示此信息。 但是，ASDK 用户不收取任何使用这些报告。


## <a name="register-azure-stack-with-azure"></a>将 Azure Stack 注册到 Azure 
请按照下列步骤以向 Azure 注册 ASDK。

> [!NOTE]
> 必须有权访问特权终结点的计算机从运行所有这些步骤。 有关 ASDK，这是开发工具包主机计算机。 

在使用这些步骤以向 Azure 注册 ASDK 之前, 确保你已安装了 Azure 堆栈 PowerShell 并下载 Azure 堆栈工具中所述[后期部署配置](asdk-post-deploy.md)文章。 

1. 以管理员身份打开 PowerShell 控制台。  

2. 运行以下 PowerShell 命令以向 Azure 注册 ASDK 安装 （将需要登录到你的 Azure 订阅和本地 ASDK 安装）：

    ```PowerShell
    # Add the Azure cloud subscription environment name. Supported environment names are AzureCloud or, if using a China Azure Subscription, AzureChinaCloud.
    Add-AzureRmAccount -EnvironmentName "AzureCloud"

    # Register the Azure Stack resource provider in your Azure subscription
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

    #Import the registration module that was downloaded with the GitHub tools
    Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

    #Register Azure Stack
    $AzureContext = Get-AzureRmContext
    $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"
    Set-AzsRegistration `
        -CloudAdminCredential $CloudAdminCred `
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