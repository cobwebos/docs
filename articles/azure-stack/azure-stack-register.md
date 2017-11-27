---
title: "注册 Azure 堆栈 |Microsoft 文档"
description: "向你的 Azure 订阅中注册 Azure 堆栈。"
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2017
ms.author: erikje
ms.openlocfilehash: 6ce8f86592ece59e338578be86c2cb673c35dbc1
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2017
---
# <a name="register-azure-stack-with-your-azure-subscription"></a>注册你的 Azure 订阅的 Azure 堆栈

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

你可以注册[Azure 堆栈](azure-stack-poc.md)与 Azure 下载从 Azure 应用商店项以及设置回向 Microsoft 报告的商业数据。 

> [!NOTE]
>建议注册，因为它使你可以测试重要 Azure 堆栈功能，例如 marketplace 联合和使用情况报告。 注册 Azure 堆栈后，使用情况报告给 Azure 商务。 你可以在用于注册的订阅下看到它。 Azure 堆栈开发工具包用户不收取任何使用这些报告。
>


## <a name="get-azure-subscription"></a>获取 Azure 订阅

在之前向 Azure 注册 Azure 堆栈，您必须：

- Azure 订阅的订阅 ID。 若要获取的 ID，登录到 Azure，单击**更多的服务** > **订阅**，单击你想要使用，的订阅并在列表视图**Essentials**可以找到**订阅 ID**。 当前不支持中国、 德国和我们政府云订阅。
- 用户名和密码是订阅的所有者的帐户 （MSA/2FA 帐户也支持）。
- Azure 订阅与 Azure Active Directory。 通过将鼠标悬停在 Azure 门户右上角用户标识，可以在 Azure 中找到此目录。 

如果你没有满足这些要求的 Azure 订阅，则可以[创建免费的 Azure 帐户此处](https://azure.microsoft.com/en-us/free/?b=17.06)。 注册 Azure 堆栈会产生任何费用在你的 Azure 订阅。


## <a name="register-azure-stack-with-azure"></a>向 Azure 注册 Azure 堆栈

> [!NOTE]
> 必须具有访问特权终结点的计算机从运行所有这些步骤。 对于 Azure 堆栈开发工具包，它将是主计算机。 如果你使用的集成的系统，请联系 Azure 堆栈运算符。
>

1. 打开 PowerShell 控制台以管理员身份和[安装适用于 Azure 堆栈 PowerShell](azure-stack-powershell-install.md)。  

2. 添加将用于注册 Azure 堆栈的 Azure 帐户。 若要执行此操作，运行`Add-AzureRmAccount`cmdlet 和 EnvironmentName 参数设置为"AzureCloud"。 系统将提示你输入你的 Azure 帐户凭据，并且你可能必须为使用双因素身份验证，具体情况视你的帐户的配置。 

   ```Powershell
   Add-AzureRmAccount -EnvironmentName "AzureCloud"
   ```

3. 如果你有多个订阅，运行以下命令以选择你想要使用：  

   ```powershell
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. 在你的 Azure 订阅中注册 AzureStack 资源提供程序。 为此，请运行以下命令：

   ```Powershell
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. 删除任何现有版本对应于注册的 Powershell 模块和[从 GitHub 下载的最新版本](azure-stack-powershell-download.md)。  

6. 从"AzureStack 工具主机"目录创建与前面步骤中，导航到"注册"文件夹，然后导入".\RegisterWithAzure.psm1"模块：  

   ```powershell 
   Import-Module .\RegisterWithAzure.psm1 
   ```

7. 在相同的 PowerShell 会话中，运行以下脚本。 提示输入凭据时，指定`azurestack\cloudadmin`作为用户和密码是什么你使用相同的本地管理员在部署过程。  

   ```powershell
   $AzureContext = Get-AzureRmContext
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"
   Add-AzsRegistration `
       -CloudAdminCredential $CloudAdminCred `
       -AzureSubscriptionId $AzureContext.Subscription.SubscriptionId `
       -AzureDirectoryTenantName $AzureContext.Tenant.TenantId `
       -PrivilegedEndpoint AzS-ERCS01 `
       -BillingModel Development 
   ```

   | 参数 | 说明 |
   | -------- | ------------- |
   | CloudAdminCredential | 经常使用的云域凭据[访问特权终结点](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)。 用户名采用以下格式"\<Azure 堆栈域\>\cloudadmin"。 对于开发工具包，即会将用户名设置为"azurestack\cloudadmin"。 如果你使用的集成的系统，请联系你的 Azure 堆栈运算符来获取此值。|
   | AzureSubscriptionId | 你用于注册 Azure 堆栈中的 Azure 订阅。|
   | AzureDirectoryTenantName | 与你的 Azure 订阅相关联的 Azure 租户目录的名称。 将在此目录租户中创建注册资源。 |
   | PrivilegedEndpoint | 预配置远程 PowerShell 控制台，它提供功能，如日志收集和其他文章部署任务。 开发工具包"AzS ERCS01"虚拟机上托管特权终结点。 如果你使用的集成的系统，请联系你的 Azure 堆栈运算符来获取此值。 若要了解详细信息，请参见[使用特权终结点](azure-stack-privileged-endpoint.md)主题。|
   | BillingModel | 使用你的订阅的计费模型。 允许的值为此参数是"容量"、"PayAsYouUse"和"开发"。 为开发工具包，此值设置为"开发"。 如果你使用的集成的系统，请联系你的 Azure 堆栈运算符来获取此值。 |

8. 完成脚本后，你将看到一条消息"正在激活 Azure 堆栈 （此步骤可能需要 10 分钟的时间才能完成）。" 

## <a name="verify-the-registration"></a>验证注册

1. 登录到管理员门户 (https://adminportal.local.azurestack.external)。
2. 单击**更多的服务** > **应用商店管理** > **添加从 Azure**。
3. 如果你看到的项 （如 WordPress) 的 Azure 上提供的列表，已成功激活。

> [!NOTE]
> 完成注册后，将不再显示未注册的活动警告。

## <a name="next-steps"></a>后续步骤

[连接到 Azure Stack](azure-stack-connect-azure-stack.md)

