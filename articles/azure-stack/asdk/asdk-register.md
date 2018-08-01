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
ms.date: 07/25/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 2a447931ea850c4ccbe618270de5fbbc9b9eaea7
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2018
ms.locfileid: "39366590"
---
# <a name="azure-stack-registration"></a>Azure Stack 注册
可将 Azure Stack 开发工具包 (ASDK) 安装注册到 Azure，以便从 Azure 下载市场项，并设置向 Microsoft 报告商务数据的功能。 需要注册才能支持完整的 Azure Stack 功能，包括市场联合。 之所以建议注册，是因为这样可以测试重要的 Azure Stack 功能，例如市场联合和使用情况报告。 注册 Azure Stack 之后，使用情况将报告给 Azure 商业组件。 用于注册的订阅下会显示此信息。 但是，ASDK 用户无需付费，不管他们报告的用量是多少。

如果未注册 ASDK，则可能会看到“需要激活”警告警报，建议注册 Azure Stack 开发工具包。 这是预期的行为。

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

2. 运行以下 PowerShell 命令，将 ASDK 安装注册到 Azure。 你将需要登录到你的 Azure 订阅和本地 ASDK 安装。 如果还没有 Azure 订阅，你可以[创建免费的 Azure 帐户此处](https://azure.microsoft.com/free/?b=17.06)。 注册 Azure Stack 不会对 Azure 订阅收取任何费用。  

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
  ```
3. 完成脚本后，应会看到此消息：**环境现在已注册和激活使用提供的参数。**

    ![现在注册你的环境](media/asdk-register/1.PNG)

## <a name="verify-the-registration-was-successful"></a>验证注册成功
请按照下列步骤以验证与 Azure 的 ASDK 注册成功。

1. 登录到[Azure Stack 管理门户](https://adminportal.local.azurestack.external)。

2. 单击**Marketplace 管理** > **从 Azure 添加**。

    ![](media/asdk-register/2.PNG)

3. 请参阅 Azure 上提供的项的列表，如果你激活已成功。

    ![](media/asdk-register/3.PNG)

## <a name="next-steps"></a>后续步骤

  [添加 Azure Stack 市场项](.\.\azure-stack-marketplace.md)
