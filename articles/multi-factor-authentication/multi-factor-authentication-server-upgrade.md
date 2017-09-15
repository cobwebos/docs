---
title: "Azure MFA 服务器升级 | Microsoft Docs"
description: "将 Azure 多重身份验证服务器升级到较新版本的相关步骤和指南。"
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 50bb8ac3-5559-4d8b-a96a-799a74978b14
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: joflore
ms.reviewer: yossib
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 6e4e09f8539aad56f92ad9137f4a6b9eb0d82370
ms.contentlocale: zh-cn
ms.lasthandoff: 06/28/2017

---


# <a name="upgrade-to-the-latest-azure-multi-factor-authentication-server"></a>升级到最新的 Azure 多重身份验证服务器

本文将指导你完成 Azure 多重身份验证 (MFA) 服务器 v6.0 或更高版本的升级过程。 如果需要升级旧版 PhoneFactor 代理，请参阅[将 PhoneFactor 代理升级到 Azure 多重身份验证服务器](multi-factor-authentication-get-started-server-upgrade.md)。

如果要从 v6.x 或更低版本升级到 v7.x 或更高版本，所有组件都需从 .NET 2.0 更改为 .NET 4.5。 所有组件还需要 Microsoft Visual C++ 2015 Redistributable Update 1 或更高版本。 MFA 服务器安装程序将安装这些组件的 x86 和 x64 版本（如果尚未安装）。 如果用户门户和移动应用 Web 服务在不同的服务器上运行，则需要先安装这些包，然后才能对这些组件升级。 可以在 [Microsoft 下载中心](https://www.microsoft.com/en-us/download/)搜索最新的 Microsoft Visual C++ 2015 Redistributable 更新。 

## <a name="install-the-latest-version-of-azure-mfa-server"></a>安装最新版本的 Azure MFA 服务器

1. 参照[下载 Azure 多重身份验证服务器](multi-factor-authentication-get-started-server.md#download-the-azure-multi-factor-authentication-server)中的说明，获取最新版本的 Azure MFA 服务器。
2. 在主 MFA 服务器上备份位于 C:\Program Files\Multi-Factor Authentication Server\Data\PhoneFactor.pfdata（假定为默认安装位置）中的 MFA 服务器数据文件。
3. 如果运行多个服务器以实现高可用性，请将验证 MFA 服务器的客户端系统更改为停止向要升级的服务器发送流量。 如果使用负载均衡器，请从负载均衡器中删除 MFA 服务器，完成升级后再将服务器添加回场中。
4. 在每个 MFA 服务器上运行新的安装程序。 首先升级从属服务器，因为它们可以读取主服务器复制的旧数据文件。 

  运行安装程序前，无需卸载当前的 MFA 服务器。 安装程序执行就地升级。 安装路径是从以前的安装的注册表中选取的，因此它会安装在同一位置（例如 C:\Program Files\Multi-Factor Authentication Server）。 
  
5. 如果系统提示安装 Microsoft Visual C++ 2015 Redistributable 更新包，则接受提示。 将同时安装更新包的 x86 和 x64 版本。
5. 如果使用 Web 服务 SDK，系统会提示安装新 Web 服务 SDK。 在安装新的 Web 服务 SDK 时，请确保虚拟目录名称与以前安装的虚拟目录（例如，MultiFactorAuthWebServiceSdk）相匹配。
6. 在所有从属服务器上重复以上步骤。 将其中一个从属服务器提升为新的主服务器，然后升级旧的主服务器。 

## <a name="upgrade-the-user-portal"></a>升级用户门户

1. 备份位于用户门户安装位置（例如，C:\inetpub\wwwroot\MultiFactorAuth）的虚拟目录中的 web.config 文件。 如果对默认主题进行了任何更改，还需备份 App_Themes\Default 文件夹。 建议创建 Default 文件夹副本并创建新主题，而不是更改默认主题。
2. 如果用户门户在其他 MFA 服务器组件所在的服务器上运行，MFA 服务器安装会提示更新用户门户。 接受提示并安装用户门户更新。 查看虚拟目录名称是否与之前安装的虚拟目录（例如，MultiFactorAuth）名称一致。
3. 如果用户门户位于自己的服务器上，请从一个 MFA 服务器的安装位置复制 MultiFactorAuthenticationUserPortalSetup64.msi 文件，再将它置于用户门户 Web 服务器上。 运行安装程序。 

  如果看到内容为“需要 Microsoft Visual C++ 2015 Redistributable Update 1 或更高版本”的错误消息，请从 [Microsoft 下载中心](https://www.microsoft.com/download/)下载并安装最新的更新程序包。 同时安装 x86 和 x64 版本。

4. 安装已更新的用户门户软件后，将在步骤 1 中备份的 web.config 文件与新的 web.config 文件进行比较。 如果新的 web.config 文件中不存在新属性，则将 web.config 备份复制到虚拟目录以覆盖新文件。 另一种做法是将备份文件中的 appSettings 值和 Web 服务 SDK URL 复制/粘贴到新的 web.config。

如果用户门户在多个服务器上运行，则对所有这些服务器重复上述安装步骤。 


## <a name="upgrade-the-mobile-app-web-service"></a>升级移动应用 Web 服务

1. 备份位于移动应用 Web 服务安装位置（例如，C:\inetpub\wwwroot\app 或 C:\inetpub\wwwroot\MultiFactorAuthMobileAppWebService）的虚拟目录中的 web.config 文件。
2. 从 MFA 服务器的安装位置复制 MultiFactorAuthenticationMobileAppWebServiceSetup64.msi 文件，再将它置于移动应用注册 Web 服务器上。
3. 运行安装程序。 

  如果出现错误，指示需要 Microsoft Visual C++ 2015 Redistributable Update 1 或更高版本，则从 [Microsoft 下载中心](https://www.microsoft.com/download/)下载并安装最新的更新包。 同时安装 x86 和 x64 版本。

4. 安装已更新的移动应用 Web 服务软件后，将在第 1 步中备份的 web.config 文件与新的 web.config 文件进行比较。 如果新的 web.config 文件中没有新属性，可以将保存的 web.config 复制回虚拟目录中，并覆盖新文件。 另一种做法是将备份文件中的 appSettings 值和 Web 服务 SDK URL 复制/粘贴到新的 web.config。

如果移动应用 Web 服务在多个服务器上运行，则对所有这些服务器重复上述安装步骤。 

## <a name="upgrade-the-ad-fs-adapters"></a>升级 AD FS 适配器


### <a name="if-mfa-runs-on-different-servers-than-ad-fs"></a>如果 MFA 在 AD FS 以外的服务器上运行

以下说明仅适用于多重身份验证服务器与 AD FS 服务器分开运行的情况。 如果这两个服务在相同的服务器上运行，则跳过此部分，转到安装步骤。 

1. 保存已在 AD FS 中注册的 MultiFactorAuthenticationAdfsAdapter.config 文件副本，或运行以下 PowerShell 命令导出配置：`Export-AdfsAuthenticationProviderConfigurationData -Name [adapter name] -FilePath [path to config file]`。 适配器名称为“WindowsAzureMultiFactorAuthentication”或“AzureMfaServerAuthentication”，具体取决于以前安装的版本。
2. 将以下文件从 MFA 服务器安装位置复制到 AD FS 服务器：

  - MultiFactorAuthenticationAdfsAdapterSetup64.msi
  - Register-MultiFactorAuthenticationAdfsAdapter.ps1
  - Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
  - MultiFactorAuthenticationAdfsAdapter.config

3. 将 `-ConfigurationFilePath [path]` 添加到 `Register-AdfsAuthenticationProvider` 命令末尾，从而编辑 Register-MultiFactorAuthenticationAdfsAdapter.ps1 脚本。 将 [路径] 替换为 MultiFactorAuthenticationAdfsAdapter.config 文件或上一步中导出的配置文件的完整路径。 

  检查新 MultiFactorAuthenticationAdfsAdapter.config 中的属性，看它们是否与旧配置文件匹配。 如果在新版本中添加或删除了任何属性，请将这些属性值从旧配置文件复制到新配置文件，或者修改旧配置文件以保持一致。

### <a name="install-new-ad-fs-adapters"></a>安装新的 AD FS 适配器

> [!IMPORTANT] 
> 你的用户无需执行本部分的步骤 3 至步骤 8 中的双重验证。 如果在多个群集中配置了 AD FS，可以独立地删除、升级和还原场中的每个群集，以免停机。

1. 从场中删除部分 AD FS 服务器。 更新这些服务器，其他服务器仍正常运行。
2. 在从 AD FS 场中删除的每个服务器上安装新的 AD FS 适配器。 如果在每个 AD FS 服务器上安装了 MFA 服务器，则可以通过 MFA 服务器管理 UX 进行更新。 如果没有，则通过运行 MultiFactorAuthenticationAdfsAdapterSetup64.msi 进行更新。 

  如果看到内容为“需要 Microsoft Visual C++ 2015 Redistributable Update 1 或更高版本”的错误消息，请从 [Microsoft 下载中心](https://www.microsoft.com/download/)下载并安装最新的更新程序包。 同时安装 x86 和 x64 版本。

3. 转到“AD FS” > “身份验证策略” > “编辑全局多重身份验证策略”。 取消选中“WindowsAzureMultiFactorAuthentication”或“AzureMFAServerAuthentication”（具体取决于当前安装的版本）。 

  完成这一步后，便无法在此 AD FS 群集中通过 MFA 服务器进行双重验证，除非完成到第 8 步。

4. 通过运行 Unregister-MultiFactorAuthenticationAdfsAdapter.ps1 PowerShell 脚本，注销 AD FS 适配器的较旧版本。 确保 -Name 参数（“WindowsAzureMultiFactorAuthentication”或“AzureMFAServerAuthentication”）与第 3 步中显示的名称一致。 这一点适用于同一 AD FS 群集中的所有服务器，因为这些服务器进行了集中配置。
5. 通过运行 Register-MultiFactorAuthenticationAdfsAdapter.ps1 PowerShell 脚本注册新的 AD FS 适配器。 这一点适用于同一 AD FS 群集中的所有服务器，因为这些服务器进行了集中配置。
6. 在从 AD FS 场中删除的每个服务器上重新启动 AD FS 服务。
7. 将更新后的服务器重新添加到 AD FS 场，并删除场中的其他服务器。
8. 转到“AD FS” > “身份验证策略” > “编辑全局多重身份验证策略”。 选中“AzureMfaServerAuthentication”。
9. 重复步骤 2，以更新刚从 AD FS 场中删除的服务器，并重新启动这些服务器上的 AD FS 服务。
10. 将这些服务器重新添加到 AD FS 场。

## <a name="next-steps"></a>后续步骤

- 获取[使用 Azure 多重身份验证与第三方 VPN 的高级方案](multi-factor-authentication-advanced-vpn-configurations.md)的示例

- [将 MFA 服务器与 Windows Server Active Directory 同步](multi-factor-authentication-get-started-server-dirint.md)

- 为应用程序[配置 Windows 身份验证](multi-factor-authentication-get-started-server-windows.md)

