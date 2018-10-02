---
title: 将 ASDK 注册到 Azure | Microsoft Docs
description: 介绍如何将 Azure Stack 注册到 Azure，以实现市场联合并报告使用情况。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: c90e51d1aa1c3215c40baeae2c5494ef90b01132
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2018
ms.locfileid: "47584347"
---
# <a name="azure-stack-registration"></a>Azure Stack 注册
可将 Azure Stack 开发工具包 (ASDK) 安装注册到 Azure，以便从 Azure 下载市场项，并设置向 Microsoft 报告商务数据的功能。 需要注册才能支持完整的 Azure Stack 功能，包括市场联合。 之所以建议注册，是因为这样可以测试重要的 Azure Stack 功能，例如市场联合和使用情况报告。 注册 Azure Stack 之后，使用情况将报告给 Azure 商业组件。 用于注册的订阅下会显示此信息。 但是，ASDK 用户无需付费，不管他们报告的用量是多少。

如果未注册 ASDK，则可能会看到“需要激活”警告警报，建议注册 Azure Stack 开发工具包。 此行为是预期的行为。

## <a name="prerequisites"></a>必备组件
在遵照这些说明将 ASDK 注册到 Azure 之前，请确保已安装 Azure Stack PowerShell，并已下载[部署后配置](asdk-post-deploy.md)一文中所述的 Azure Stack 工具。

此外，在用于向 Azure 注册 ASDK 的计算机上，PowerShell 语言模式必须设置为 **FullLanguageMode**。 若要验证当前的语言模式是否设置为 Full，请打开权限提升的 PowerShell 窗口，并运行以下 PowerShell 命令：

```PowerShell  
$ExecutionContext.SessionState.LanguageMode
```

确保输出返回的是 **FullLanguageMode**。 如果返回了其他任何语言模式，则需要在另一台计算机上运行注册，或者将语言模式设置为 **FullLanguageMode**，然后才能继续。

## <a name="register-azure-stack-with-azure"></a>将 Azure Stack 注册到 Azure
遵循以下步骤将 ASDK 注册到 Azure。

> [!NOTE]
> 所有这些步骤必须在可以访问特权终结点的计算机上运行。 对于 ASDK 来说，该计算机是开发工具包主机。

1. 以管理员身份打开 PowerShell 控制台。  

2. 运行以下 PowerShell 命令，将 ASDK 安装注册到 Azure。 需要同时登录到 Azure 订阅和本地 ASDK 安装。 如果还没有 Azure 订阅，你可以[创建免费的 Azure 帐户此处](https://azure.microsoft.com/free/?b=17.06)。 注册 Azure Stack 不会对 Azure 订阅收取任何费用。<br><br>如果使用同一 Azure 订阅 ID 在 Azure Stack 的多个实例上运行注册脚本，请在运行 **Set-AzsRegistration** cmdlet 时为注册设置唯一的名称。 **RegistrationName** 参数的默认值为 **AzureStackRegistration**。 但是，如果在多个 Azure Stack 实例上使用同一名称，该脚本会失败。

    ```PowerShell  
    # Add the Azure cloud subscription environment name. 
    # Supported environment names are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
    Add-AzureRmAccount -EnvironmentName "AzureCloud"

    # Register the Azure Stack resource provider in your Azure subscription
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

    #Import the registration module that was downloaded with the GitHub tools
    Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

    #Register Azure Stack
    $AzureContext = Get-AzureRmContext
    $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
    $RegistrationName = "<unique-registration-name>"
    $UsageReporting = True # Set to False if you using a Capacity Billing Model
    Set-AzsRegistration `
        -PrivilegedEndpointCredential $CloudAdminCred `
        -PrivilegedEndpoint AzS-ERCS01 `
        -BillingModel Development
        -RegistrationName $RegistrationName
        -EnableUsageReporting $UsageReporting
    ```
3. 该脚本完成后，会显示以下消息：“现已使用提供的参数注册并激活环境”。

    ![环境现已注册](media/asdk-register/1.PNG)

## <a name="verify-the-registration-was-successful"></a>验证注册是否成功
遵循以下步骤来验证 ASDK 是否已成功注册到 Azure。

1. 登录到 [Azure Stack 管理门户](https://adminportal.local.azurestack.external)。

2. 单击“市场管理” > “从 Azure 添加”。

    ![](media/asdk-register/2.PNG)

3. 如果看到 Azure 提供的项列表，则表示激活成功。

    ![](media/asdk-register/3.PNG)

## <a name="move-a-registration-resource"></a>移动注册资源
在同一订阅下的资源组之间移动注册资源**是**支持。 有关将资源移到新的资源组的详细信息，请参阅[将资源移到新的资源组或订阅](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)。


## <a name="next-steps"></a>后续步骤
[添加 Azure Stack 市场项](.\.\azure-stack-marketplace.md)
