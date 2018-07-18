---
title: Azure Stack 开发工具包 (ASDK) 的部署后配置 | Microsoft Docs
description: 介绍在安装 Azure Stack 开发工具包 (ASDK) 之后建议做出的配置更改。
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
ms.date: 06/05/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 23d99c498c139da3a145a1df230f419b4591b256
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38598435"
---
# <a name="post-asdk-installation-configuration-tasks"></a>安装 ASDK 后的配置任务

[安装 Azure Stack 开发工具包 (ASDK)](asdk-install.md) 之后，需要实施建议的安装后配置更改。

## <a name="install-azure-stack-powershell"></a>安装 Azure Stack PowerShell

需要安装与 Azure Stack 兼容的 Azure PowerShell 模块才能使用 Azure Stack。

通过 PowerShell 库安装适用于 Azure Stack 的 PowerShell 命令。 若要注册 PSGallery 存储库，请打开权限提升的 PowerShell 会话并运行以下命令：

``` Powershell
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted
```

可以使用 API 版本配置文件来指定与 Azure Stack 兼容的 AzureRM 模块。  API 版本配置文件提供一种管理 Azure 与 Azure Stack 之间版本差异的方式。 API 版本配置文件是一组具有特定 API 版本 AzureRM PowerShell 模块。 可通过 PowerShell 库获得的 **AzureRM.Bootstrapper** 模块会提供使用 API 版本配置文件所需的 PowerShell cmdlet。

无论是否与 ASDK 主机建立了 Internet 连接，都可以安装最新 Azure Stack PowerShell 模块：

> [!IMPORTANT]
> 在安装所需版本之前，请务必[卸载任何现有 Azure PowerShell 模块](.\.\azure-stack-powershell-install.md#uninstall-existing-versions-of-the-azure-stack-powershell-modules)。

- **已从 ASDK 主机建立 Internet 连接**。 运行以下 PowerShell 脚本，在开发工具包安装中安装以下模块：

  ``` PowerShell
  # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet. 
  Install-Module `
    -Name AzureRm.BootStrapper

  # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
  Use-AzureRmProfile `
    -Profile 2017-03-09-profile -Force

  # Install Azure Stack Module Version 1.3.0. If running a pre-1804 version of Azure Stack, change the -RequiredVersion value to 1.2.11.
  Install-Module -Name AzureStack -RequiredVersion 1.3.0 

  ```

  如果安装成功，输出中会显示 AzureRM 和 AzureStack 模块。

- **未从 ASDK 主机建立 Internet 连接**。 在离线场景中，必须先使用以下 PowerShell 命令，将 PowerShell 模块下载到已建立 Internet 连接的计算机：

  ```PowerShell
  $Path = "<Path that is used to save the packages>"

  Save-Package `
    -ProviderName NuGet `
    -Source https://www.powershellgallery.com/api/v2 `
    -Name AzureRM `
    -Path $Path `
    -Force `
    -RequiredVersion 1.2.11

  Save-Package `
    -ProviderName NuGet `
    -Source https://www.powershellgallery.com/api/v2 `
    -Name AzureStack `
    -Path $Path `
    -Force `
  # Install Azure Stack Module Version 1.3.0. If running a pre-1804 version of Azure Stack, change the -RequiredVersion value to 1.2.11.  
    -RequiredVersion 1.3.0
  ```

  接下来，将下载的包复制到 ASDK 计算机，将该位置注册为默认存储库，并从此存储库安装 AzureRM 和 AzureStack 模块：

    ```PowerShell  
    $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
    $RepoName = "MyNuGetSource"

    Register-PSRepository `
      -Name $RepoName `
      -SourceLocation $SourceLocation `
      -InstallationPolicy Trusted

    Install-Module AzureRM `
      -Repository $RepoName

    Install-Module AzureStack `
      -Repository $RepoName
    ```

## <a name="download-the-azure-stack-tools"></a>下载 Azure Stack 工具

[AzureStack-Tools](https://github.com/Azure/AzureStack-Tools) 是托管 PowerShell 模块的 GitHub 存储库，可用于管理资源并将其部署到 Azure Stack。 若要获取这些工具，请克隆 GitHub 存储库，或运行以下脚本来下载 AzureStack-Tools 文件夹：

  ```PowerShell
  # Change directory to the root directory. 
  cd \

  # Enforce usage of TLSv1.2 to download the Azure Stack tools archive from GitHub
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
  invoke-webrequest `
    https://github.com/Azure/AzureStack-Tools/archive/master.zip `
    -OutFile master.zip

  # Expand the downloaded files.
  expand-archive master.zip `
    -DestinationPath . `
    -Force

  # Change to the tools directory.
  cd AzureStack-Tools-master
  ```

## <a name="validate-the-asdk-installation"></a>验证 ASDK 安装
若要确保 ASDK 部署成功，可以遵循以下步骤使用 Test-AzureStack cmdlet：

1. 以 AzureStack\AzureStackAdmin 身份登录到 ASDK 主机。
2. 以管理员身份打开 PowerShell（非 PowerShell ISE）。
3. 运行：`Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint`
4. 运行：`Test-AzureStack`

此项测试可能需要几分钟时间才能完成。 如果安装成功，将返回如下所示的输出：

![test-azurestack](media/asdk-post-deploy/test-azurestack.png)

如果失败，请遵循故障排除步骤来获取帮助。

## <a name="activate-the-administrator-and-tenant-portals"></a>激活管理员门户和租户门户
在完成使用 Azure AD 的部署以后，必须激活 Azure Stack 管理员门户和租户门户。 对于目录的所有用户来说，此激活是指同意为 Azure Stack 门户和 Azure 资源管理器提供正确的权限（已在同意页上列出）。

- 如果在管理员门户中操作，请导航到 https://adminportal.local.azurestack.external/guest/signup，阅读信息，然后单击“接受”。 接受后即可添加服务管理员，但这些管理员不能也是目录租户管理员。

- 如果在租户门户中操作，请导航到 https://portal.local.azurestack.external/guest/signup，阅读信息，然后单击“接受”。 接受后，目录中的用户即可登录到租户门户。 

> [!NOTE] 
> 如果门户未激活，则只有目录管理员可以登录并使用门户。 其他用户在登录时会看到错误，指出管理员尚未授予其他用户权限。 如果管理员原本不属于 Azure Stack 注册到的目录，则必须将 Azure Stack 目录追加到激活 URL。 例如，如果 Azure Stack 注册到 fabrikam.onmicrosoft.com 和管理员用户是admin@contoso.com，导航到https://portal.local.azurestack.external/guest/signup/fabrikam.onmicrosoft.com来激活门户。 

## <a name="reset-the-password-expiration-policy"></a>重置密码过期策略 
若要确保开发工具包主机的密码不在评估期结束之前过期，请在部署 ASDK 之后执行以下步骤。

### <a name="to-change-the-password-expiration-policy-from-powershell"></a>若要通过 Powershell 更改密码过期策略，请执行以下步骤：
在提升权限的 Powershell 控制台中，运行以下命令：

```powershell
Set-ADDefaultDomainPasswordPolicy -MaxPasswordAge 180.00:00:00 -Identity azurestack.local
```

### <a name="to-change-the-password-expiration-policy-manually"></a>若要手动更改密码过期策略，请执行以下步骤：
1. 在开发工具包主机上打开**组策略管理**(GPMC。MMC)，并导航到**组策略管理**–**林： azurestack.local** –**域**–**请将 azurestack.local**。
2. 右键单击“默认域策略”，然后单击“编辑”。
3. 在组策略管理编辑器中，导航到**计算机配置**–**策略**– **Windows 设置**–**安全设置**–**帐户策略**–**密码策略**。
4. 在右窗格中，双击“密码最长期限”。
5. 在“密码最长期限属性”对话框中，将“密码有效天数”值更改为 **180**，然后单击“确定”。

![组策略管理控制台](media/asdk-post-deploy/gpmc.png)


## <a name="next-steps"></a>后续步骤
[将 ASDK 注册到 Azure](asdk-register.md)
