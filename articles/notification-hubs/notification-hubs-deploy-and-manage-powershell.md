---
title: 使用 PowerShell 部署和管理通知中心
description: 如何使用 PowerShell 为自动化创建和管理通知中心
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 7c58f2c8-0399-42bc-9e1e-a7f073426451
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: e6334659d41ba201cfdde190ccc9bfa0d58009e3
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2020
ms.locfileid: "82891199"
---
# <a name="deploy-and-manage-notification-hubs-using-powershell"></a>使用 PowerShell 部署和管理通知中心

## <a name="overview"></a>概述

本文说明如何使用 PowerShell 来创建和管理 Azure 通知中心。 本文将演示以下常见自动化任务。

- 创建通知中心
- 设置凭据

如果还需要为通知中心创建新的服务总线命名空间，请参阅[使用 PowerShell 管理服务总线](../service-bus-messaging/service-bus-powershell-how-to-provision.md)。

不支持直接使用 Azure PowerShell 随附的 cmdlet 来管理通知中心。 在 PowerShell 中，最佳方法是引用 Microsoft.Azure.NotificationHubs.dll 程序集。 该程序集是随 [Microsoft Azure 通知中心 NuGet 包](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)一起分发的。

## <a name="prerequisites"></a>必备条件

- Azure 订阅。 Azure 是基于订阅的平台。 有关获取订阅的详细信息，请参阅[购买选项]、[会员套餐]或[免费试用]。
- 配备 Azure PowerShell 的计算机。 有关说明，请参阅[安装和配置 Azure PowerShell]。
- 大致了解 PowerShell 脚本、NuGet 包和 .NET Framework。

## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>包含对适用于服务总线的 .NET 程序集的引用

Azure PowerShell 中的 PowerShell cmdlet 尚不支持管理 Azure 通知中心。 若要预配通知中心，可以使用 [Microsoft Azure 通知中心 NuGet 包](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)中提供的 .NET 客户端。

首先，请确保脚本可以找到 **Microsoft.Azure.NotificationHubs.dll** 程序集，该程序集在 Visual Studio 项目中以 NuGet 包的形式安装。 为了灵活起见，该脚本执行以下步骤：

1. 确定调用它的路径。
2. 遍历路径直到找到名为 `packages` 的文件夹为止。 此文件夹是在为 Visual Studio 项目安装 NuGet 包时创建的。
3. 以递归方式在 `packages` 文件夹中搜索名为 `Microsoft.Azure.NotificationHubs.dll` 的程序集。
4. 引用该程序集，以便类型可供以后使用。

下面说明如何在 PowerShell 脚本中实现这些步骤：

``` powershell
try
{
    # WARNING: Make sure to reference the latest version of Microsoft.Azure.NotificationHubs.dll
    Write-Output "Adding the [Microsoft.Azure.NotificationHubs.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.Azure.NotificationHubs.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.Azure.NotificationHubs.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error("Could not add the Microsoft.Azure.NotificationHubs.dll assembly to the script. Make sure you build the solution before running the provisioning script.")
}
```

## <a name="create-the-namespacemanager-class"></a>创建 `NamespaceManager` 类

若要预配通知中心，请从 SDK 创建 [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager?view=azure-dotnet) 类的实例。

可以使用 Azure PowerShell 随附的 [Get-AzureSBAuthorizationRule] cmdlet 检索用于提供连接字符串的授权规则。 对 `NamespaceManager` 实例的引用存储于 `$NamespaceManager` 变量中。 `$NamespaceManager` 用于设置通知中心。

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager=[Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```

## <a name="provisioning-a-new-notification-hub"></a>设置新通知中心

若要预配新的通知中心，请使用[通知中心的 .NET API]。

该脚本的此部分会设置四个本地变量。

1. `$Namespace`：将此变量设置为要在其中创建通知中心的命名空间的名称。
2. `$Path`：将此路径设置为新通知中心的名称。  例如“MyHub”。
3. `$WnsPackageSid`：从 [Windows 开发人员中心](https://developer.microsoft.com/en-us/windows)将此变量设置为 Windows 应用的包 SID。
4. `$WnsSecretkey`：从 [Windows 开发人员中心](https://developer.microsoft.com/en-us/windows)将此变量设置为 Windows 应用的机密密钥。

这些变量可用于连接命名空间，以及创建配置为使用 Windows 应用 Windows 通知中心 (WNS) 凭据处理 WNS 通知的新通知中心。 有关获取包 SID 和机密密钥的信息，请参阅[通知中心入门](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)教程。

- 脚本代码段使用 `NamespaceManager` 对象来检查 `$Path` 标识的通知中心是否存在。
- 如果不存在，则脚本会使用 WNS 凭据创建 `NotificationHubDescription`，并将其传递给 `NamespaceManager` 类 `CreateNotificationHub` 方法。

``` powershell
$Namespace = "<Enter your namespace>"
$Path  = "<Enter a name for your notification hub>"
$WnsPackageSid = "<your package sid>"
$WnsSecretkey = "<enter your secret key>"

$WnsCredential = New-Object -TypeName Microsoft.Azure.NotificationHubs.WnsCredential -ArgumentList $WnsPackageSid,$WnsSecretkey

# Query the namespace
$CurrentNamespace = Get-AzureSBNamespace -Name $Namespace

# Check if the namespace already exists
if ($CurrentNamespace)
{
    Write-Output "The namespace [$Namespace] in the [$($CurrentNamespace.Region)] region was found."

    # Create the NamespaceManager object used to create a new notification hub
    $sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
    Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
    $NamespaceManager = [Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
    Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."

    # Check to see if the Notification Hub already exists
    if ($NamespaceManager.NotificationHubExists($Path))
    {
        Write-Output "The [$Path] notification hub already exists in the [$Namespace] namespace."  
    }
    else
    {
        Write-Output "Creating the [$Path] notification hub in the [$Namespace] namespace."
        $NHDescription = New-Object -TypeName Microsoft.Azure.NotificationHubs.NotificationHubDescription -ArgumentList $Path;
        $NHDescription.WnsCredential = $WnsCredential;
        $NamespaceManager.CreateNotificationHub($NHDescription);
        Write-Output "The [$Path] notification hub was created in the [$Namespace] namespace."
    }
}
else
{
    Write-Host "The [$Namespace] namespace does not exist."
}
```

## <a name="additional-resources"></a>其他资源

- [使用 PowerShell 管理服务总线](../service-bus-messaging/service-bus-powershell-how-to-provision.md)
- [How to create Service Bus queues, topics and subscriptions using a PowerShell script](https://docs.microsoft.com/archive/blogs/paolos/how-to-create-service-bus-queues-topics-and-subscriptions-using-a-powershell-script)（如何使用 PowerShell 脚本创建服务总线队列、主题和订阅）
- [How to create a Service Bus Namespace and an Event Hub using a PowerShell script](https://docs.microsoft.com/archive/blogs/paolos/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script)（如何使用 PowerShell 脚本创建服务总线命名空间和事件中心）

一些现成的脚本也可供下载：

- [服务总线 PowerShell 脚本](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)

[购买选项]: https://azure.microsoft.com/pricing/purchase-options/
[会员套餐]: https://azure.microsoft.com/pricing/member-offers/
[免费试用版]: https://azure.microsoft.com/pricing/free-trial/
[安装和配置 Azure PowerShell]: /powershell/azureps-cmdlets-docs
[通知中心的 .NET API]: https://docs.microsoft.com/dotnet/api/overview/azure/notification-hubs?view=azure-dotnet
[Get-AzureSBNamespace]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azuresbnamespace
[New-AzureSBNamespace]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azuresbnamespace
[Get-AzureSBAuthorizationRule]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azuresbauthorizationrule
