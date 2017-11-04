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
ms.date: 10/27/2017
ms.author: erikje
ms.openlocfilehash: 24cde66a132ae2e1ba0eb9b1564915746e5ca448
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2017
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
- [注册 Azure 堆栈资源提供程序](#register-azure-stack-resource-provider-in-azure)。

如果你没有满足这些要求的 Azure 订阅，则可以[创建免费的 Azure 帐户此处](https://azure.microsoft.com/en-us/free/?b=17.06)。 注册 Azure 堆栈会产生任何费用在你的 Azure 订阅。



## <a name="register-azure-stack-resource-provider-in-azure"></a>在 Azure 中注册 Azure 堆栈资源提供程序
> [!NOTE] 
> 此步骤只需在 Azure 堆栈环境中一次完成。
>

1. 以管理员身份启动 Powershell ISE。
2. 登录到是-EnvironmentName 参数设置为"AzureCloud"的 Azure 订阅的所有者的 Azure 帐户。
3. 注册 Azure 资源提供程序"Microsoft.AzureStack"。

示例： 
```Powershell
Login-AzureRmAccount -EnvironmentName "AzureCloud"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
```


## <a name="register-azure-stack-with-azure"></a>向 Azure 注册 Azure 堆栈

> [!NOTE]
>必须在主计算机上完成所有这些步骤。
>

1. [安装适用于 Azure 堆栈 PowerShell](azure-stack-powershell-install.md)。 
2. 复制[RegisterWithAzure.psm1 脚本](https://go.microsoft.com/fwlink/?linkid=842959)（如 C:\Temp) 的文件夹。
3. 以管理员身份启动 PowerShell ISE 并导入 RegisterWithAzure 模块。    
4. 从 RegisterWithAzure.psm1 脚本中，运行添加 AzsRegistration 模块。 替换以下占位符： 
    - *YourCloudAdminCredential*是一个包含 domain\cloudadmin 的本地域凭据的 PowerShell 对象 （对于开发工具包中，为 azurestack\cloudadmin）。
    - *YourAzureSubscriptionID*是你想要用于注册 Azure 堆栈的 Azure 订阅的 ID。
    - *YourAzureDirectoryTenantName*是与你的 Azure 订阅关联的 Azure 租户目录的名称。 将在此目录租户中创建注册资源。 
    - *YourPrivilegedEndpoint*是的名称[特权的终结点](azure-stack-privileged-endpoint.md)。

    ```powershell
    Add-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -AzureDirectoryTenantName $YourAzureDirectoryTenantName  -AzureSubscriptionId $YourAzureSubscriptionId -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Development 
    ```
5. 在弹出的登录窗口中，输入你的 Azure 订阅凭据。

## <a name="verify-the-registration"></a>验证注册

1. 登录到管理员门户 (https://adminportal.local.azurestack.external)。
2. 单击**更多的服务** > **应用商店管理** > **添加从 Azure**。
3. 如果你看到的项 （如 WordPress) 的 Azure 上提供的列表，已成功激活。

> [!NOTE]
> 完成注册后，将不再显示未注册的活动警告。

## <a name="next-steps"></a>后续步骤

[连接到 Azure Stack](azure-stack-connect-azure-stack.md)

