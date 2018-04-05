---
title: 为 Azure 云服务中的角色启用远程桌面连接 | Microsoft Docs
description: 如何配置 Azure 云服务应用程序以允许远程桌面连接
services: cloud-services
documentationcenter: na
author: ghogen
manager: douge
editor: ''
ms.assetid: f5727ebe-9f57-4d7d-aff1-58761e8de8c1
ms.service: multiple
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/06/2018
ms.author: ghogen
ms.openlocfilehash: ebe536cc5838e3e6f0a2c15950ec766c1fd44bfe
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2018
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-visual-studio"></a>使用 Visual Studio 为 Azure 云服务中的角色启用远程桌面连接

> [!div class="op_single_selector"]
> * [Azure 门户](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

可以通过远程桌面访问在 Azure 中运行的角色的桌面。 可以使用远程桌面连接，在应用程序正在运行时排查和诊断其问题。

Visual Studio 为云服务提供的发布向导中包括一个选项，用于在发布过程中使用提供的凭据启用远程桌面。 使用 Visual Studio 2017 版本 15.4 和更低版本时，比较适合使用此选项。

但是，使用 Visual Studio 2017 版本 15.5 和更高版本时，我们建议避免通过发布向导启用远程桌面，除非你是以独立的开发人员身份工作。 如果其他开发人员会打开你的项目，则应该通过 Azure 门户、PowerShell 或持续部署工作流中的发布定义启用远程桌面。 推出此建议的原因是 Visual Studio 与云服务 VM 中远程桌面的通信方式发生了变化，本文会对此做出解释。

## <a name="configure-remote-desktop-through-visual-studio-2017-version-154-and-earlier"></a>通过 Visual Studio 2017 版本 15.4 和更低版本配置远程桌面

使用 Visual Studio 2017 版本 15.4 和更低版本时，可以使用发布向导中的“为所有角色启用远程桌面”选项。 在 Visual Studio 2017 版本 15.5 和更高版本中仍可以使用该向导，但不要使用“远程桌面”选项。

1. 在 Visual Studio 的“解决方案资源管理器”中，右键单击云服务项目并选择“发布”以启动发布向导。

2. 根据需要登录到 Azure 订阅，并选择“下一步”。

3. 在“设置”页上选择“为所有角色启用远程桌面”，然后选择“设置...”链接打开“远程桌面配置”对话框。

4. 在对话框底部，选择“更多选项”。 此命令会显示一个下拉列表框，可在其中创建或选择证书，以便在通过远程桌面连接时可以加密凭据信息。

   > [!Note]
   > 用于建立远程桌面连接的证书与用于其他 Azure 操作的证书不同。 远程访问证书必须有一个私钥。

5. 从列表中选择一个证书，或选择“&lt;创建...&gt;”。 如果创建新证书，请在出现提示时提供新证书的友好名称，并选择“确定”。 新证书将显示在下拉列表框中。

6. 提供用户名和密码。 不能使用现有帐户。 请不要使用“Administrator”作为新帐户的用户名。

7. 选择帐户的过期日期，在此日期之后，远程桌面连接会被阻止。

8. 提供全部所需的信息后，选择“确定”。 Visual Studio 会将远程桌面设置（包括使用所选证书加密的密码）添加到项目的 `.cscfg` 和 `.csdef` 文件。

9. 使用“下一步”按钮完成所有剩余步骤，准备好发布云服务时，选择“发布”。 如果未准备好发布，请选择“取消”，并在系统提示是否保存更改时选择“是”。 以后可以使用这些设置发布云服务。

## <a name="configure-remote-desktop-when-using-visual-studio-2017-version-155-and-later"></a>使用 Visual Studio 2017 版本 15.5 和更高版本时配置远程桌面

使用 Visual Studio 2017 版本 15.5 和更高版本时，仍可以使用发布向导来处理云服务项目。 如果你是以独立的开发人员身份工作，则也可以使用“为所有角色启用远程桌面”选项。

如果你是团队的一分子，则应使用 [Azure 门户](cloud-services-role-enable-remote-desktop-new-portal.md)或 [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md) 在 Azure 云服务中启用远程桌面。

推出此建议的原因是 Visual Studio 2017 版本 15.5 和更高版本与云服务 VM 的通信方式发生了变化。 通过发布向导启用远程桌面时，早期版本的 Visual Studio 通过所谓的“RDP 插件”来与 VM 通信。 而 Visual Studio 2017 版本 15.5 和更高版本则是使用更安全且更灵活的“RDP 扩展”进行通信。 此项变化也符合这一事实：用于启用远程桌面的 Azure 门户和 PowerShell 方法也使用 RDP 扩展。

当 Visual Studio 与 RDP 扩展通信时，会通过 SSL 传输纯文本密码。 但是，项目的配置文件只存储加密的密码，而该密码只能使用最初加密时所用的本地证书解密成纯文本。

如果每次都从同一台开发计算机部署云服务项目，则可以使用该本地证书。 在这种情况下，仍可以使用发布向导中的“为所有角色启用远程桌面”选项。

但是，如果你或其他开发人员想要从不同的计算机部署云服务项目，则其他这些计算机无法提供所需的证书来解密密码。 因此，会出现以下错误消息：

```output
Applying remote desktop protocol (RDP) extension.
Certificate with thumbprint [thumbprint] doesn't exist.
```

每次部署云服务时都可以更改密码，但是，需要使用远程桌面的每个人并不方便执行该操作。

如果要与团队共享项目，则最好是取消选中发布向导中的选项，改为直接通过 [Azure 门户](cloud-services-role-enable-remote-desktop-new-portal.md)或 [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md) 启用远程桌面。

### <a name="deploying-from-a-build-server-with-visual-studio-2017-version-155-and-later"></a>使用 Visual Studio 2017 版本 15.5 和更高版本从生成服务器部署

可以在生成代理中，从装有 Visual Studio 2017 版本 15.5 或更高版本的生成服务器（例如，使用 Visual Studio Team Services）部署云服务项目。 使用此方法时，部署将在可提供加密证书的同一台计算机上进行。

若要使用 Visual Studio Team Services 中的 RDP 扩展，请在生成定义中包含以下详细信息：

1. 在 MSBuild 参数中包含 `/p:ForceRDPExtensionOverPlugin=true`，确保部署使用 RDP 扩展而不是 RDP 插件。 例如：

    ```
    msbuild AzureCloudService5.ccproj /t:Publish /p:TargetProfile=Cloud /p:DebugType=None
        /p:SkipInvalidConfigurations=true /p:ForceRDPExtensionOverPlugin=true
    ```

1. 在生成步骤的后面，添加“Azure 云服务部署”步骤并设置其属性。

1. 在部署步骤的后面，添加“Azure Powershell”步骤，将其“显示名称”属性设置为“Azure 部署: 启用 RDP 扩展”（或其他适当的名称），然后选择相应的 Azure订阅。

1. 将“脚本类型”设置为“内联”，并在“内联脚本”字段中粘贴以下代码。 （也可以使用此脚本在项目中创建 `.ps1` 文件，将“脚本类型”设置为“脚本文件路径”，并将“脚本路径”设置为指向该文件。）

    ```ps
    Param(
        [Parameter(Mandatory=$True)]
        [string]$username,

        [Parameter(Mandatory=$True)]
        [string]$password,

        [Parameter(Mandatory=$True)]
        [string]$serviceName,

        [Datetime]$expiry = ($(Get-Date).AddYears(1))
    )

    Write-Host "Service Name: $serviceName"
    Write-Host "User Name: $username"
    Write-Host "Expiry: $expiry"

    $securepassword = ConvertTo-SecureString -String $password -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential $username,$securepassword

    # Try to remote existing RDP Extensions
    try
    {
        $existingRDPExtension = Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
        if ($existingRDPExtension -ne $null)
        {
            Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
        }
    }
    catch
    {
    }

    Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry -Verbose
    ```

## <a name="connect-to-an-azure-role-by-using-remote-desktop"></a>使用远程桌面连接到 Azure 角色

在 Azure 上发布云服务并启用远程桌面后，可以使用 Visual Studio 服务器资源管理器登录到云服务 VM：

1. 在服务器资源管理器中，展开“Azure”节点，然后展开云服务及其角色之一的节点，以显示实例列表。

2. 右键单击某个实例节点，并选择“使用远程桌面进行连接”。

3. 输入前面创建的用户名和密码。 现在已登录到远程会话。

## <a name="additional-resources"></a>其他资源

[如何配置云服务](cloud-services-how-to-configure-portal.md)