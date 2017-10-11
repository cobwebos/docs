---
title: "使用 PowerShell 为 Azure 云服务中的角色启用远程桌面连接"
description: "如何使用 PowerShell 配置 Azure 云服务应用程序以允许远程桌面连接"
services: cloud-services
documentationcenter: 
author: thraka
manager: timlt
editor: 
ms.assetid: bf2f70a4-20dc-4302-a91a-38cd7a2baa62
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: adegeo
ms.openlocfilehash: 171f27c92ee9de14301ebb664e9ba3bcd98c394d
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2017
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-powershell"></a>使用 PowerShell 为 Azure 云服务中的角色启用远程桌面连接
> [!div class="op_single_selector"]
> * [Azure 门户](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [Azure 经典门户](cloud-services-role-enable-remote-desktop.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](../vs-azure-tools-remote-desktop-roles.md)
>
>

可以通过远程桌面访问在 Azure 中运行的角色的桌面。 可以使用远程桌面连接，在应用程序正在运行时排查和诊断其问题。

本文介绍如何使用 PowerShell 在云服务角色上启用远程桌面。 有关本文所需的先决条件，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/overview)。 PowerShell 使用远程桌面扩展，使用户能够在部署应用程序之后启用远程桌面。

## <a name="configure-remote-desktop-from-powershell"></a>从 PowerShell 配置远程桌面
使用 [Set-AzureServiceRemoteDesktopExtension](/powershell/module/azure/set-azureserviceremotedesktopextension?view=azuresmps-3.7.0) cmdlet 可以在云服务部署的指定角色或所有角色上启用远程桌面。 该 cmdlet 允许通过接受 PSCredential 对象的 *Credential* 参数为远程桌面用户指定用户名和密码。

如果以交互方式使用 PowerShell，可通过调用 [Get-Credentials](https://technet.microsoft.com/library/hh849815.aspx) cmdlet 轻松设置 PSCredential 对象。

```
$remoteusercredentials = Get-Credential
```

此命令会显示一个对话框，用于以安全方式为远程用户输入用户名和密码。

由于 PowerShell 有助于实现自动化方案，因此，还可以通过无需用户交互的方式设置 **PSCredential** 对象。 为此，需要设置一个安全密码。 首先指定一个纯文本密码，然后使用 [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx) 将其转换为安全字符串。 接下来，需要使用 [ConvertFrom-SecureString](https://technet.microsoft.com/library/hh849814.aspx) 将此安全字符串转换为加密的标准字符串。 现在可以使用 [Set-Content](https://technet.microsoft.com/library/ee176959.aspx) 将此加密的标准字符串保存到文件。

也可以创建安全密码文件，这样就不需要每次都键入密码。 此外，安全密码文件也比纯文本文件安全。 使用以下 PowerShell 创建安全的密码文件：

```
ConvertTo-SecureString -String "Password123" -AsPlainText -Force | ConvertFrom-SecureString | Set-Content "password.txt"
```

> [!IMPORTANT]
> 设置密码时请确保满足[复杂性要求](https://technet.microsoft.com/library/cc786468.aspx)。
>
>

要从安全密码文件创建凭据对象，必须读取该文件的内容并使用 [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx) 将其转换回安全字符串。

[Set-AzureServiceRemoteDesktopExtension](/powershell/module/azure/set-azureserviceremotedesktopextension?view=azuresmps-3.7.0) cmdlet 还接受 *Expiration* 参数，用以指定用户帐户过期的**日期时间**。 例如，可以将帐户设置为在当前日期和时间的几天后过期。

以下 PowerShell 示例显示如何在云服务上设置远程桌面扩展：

```
$servicename = "cloudservice"
$username = "RemoteDesktopUser"
$securepassword = Get-Content -Path "password.txt" | ConvertTo-SecureString
$expiry = $(Get-Date).AddDays(1)
$credential = New-Object System.Management.Automation.PSCredential $username,$securepassword
Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry
```
还可以选择指定部署槽以及要在其上启用远程桌面的角色。 如果未指定这些参数，该 cmdlet 将对**生产**部署槽中的所有角色启用远程桌面。

远程桌面扩展与部署相关联。 如果为服务创建新部署，必须对该部署启用远程桌面。 要始终启用远程桌面，应考虑将 PowerShell 脚本集成到部署工作流中。

## <a name="remote-desktop-into-a-role-instance"></a>通过远程桌面连接到角色实例
使用 [Get-AzureRemoteDesktopFile](/powershell/module/azure/get-azureremotedesktopfile?view=azuresmps-3.7.0) cmdlet 通过远程桌面连接到云服务的特定角色实例。 可以使用 *LocalPath* 参数将 RDP 文件下载到本地。 也可以使用 *Launch* 参数直接启动“远程桌面连接”对话框来访问云服务角色实例。

```
Get-AzureRemoteDesktopFile -ServiceName $servicename -Name "WorkerRole1_IN_0" -Launch
```


## <a name="check-if-remote-desktop-extension-is-enabled-on-a-service"></a>检查是否在服务上启用了远程桌面扩展
[Get-AzureServiceRemoteDesktopExtension](/powershell/module/azure/get-azureremotedesktopfile?view=azuresmps-3.7.0) cmdlet 显示是对服务部署启用还是禁用了远程桌面。 该 cmdlet 将返回启用了远程桌面扩展的远程桌面用户和角色的用户名。 默认情况下，这种情况发生在部署槽上，可以选择改用过渡槽。

```
Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
```

## <a name="remove-remote-desktop-extension-from-a-service"></a>从服务中删除远程桌面扩展
如果已对部署启用远程桌面扩展并需要更新远程桌面设置，请先删除该扩展， 然后使用新设置将它重新启用。 例如，要为远程用户帐户或已过期的帐户设置新密码。 在这种情况下，需要在已启用远程桌面扩展的现有部署上执行此操作。 对于新部署，只需直接应用该扩展。

若要从部署中删除远程桌面扩展，可以使用 [Remove-AzureServiceRemoteDesktopExtension](/powershell/module/azure/remove-azureserviceremotedesktopextension?view=azuresmps-3.7.0) cmdlet。 还可以选择指定要从中删除远程桌面扩展的部署槽和角色。

```
Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
```

> [!NOTE]
> 若要完全删除扩展配置，应结合 **UninstallConfiguration** 参数调用 *remove* cmdlet。
>
> **UninstallConfiguration** 参数将卸载任何已应用到服务的扩展配置。 每个扩展配置与服务配置相关联。 如果在未指定 **UninstallConfiguration** 的情况下调用 *remove* cmdlet，将取消<mark>部署</mark>与扩展配置之间的关联，因此会实际删除扩展。 但是，扩展配置仍与服务保持关联。
>
>

## <a name="additional-resources"></a>其他资源

[如何配置云服务](cloud-services-how-to-configure.md)
[云服务常见问题 - 远程桌面](cloud-services-faq.md)
