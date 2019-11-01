---
title: 启用 Azure AD 域服务的密码哈希同步 | Microsoft Docs
description: 本教程介绍如何使用 Azure AD Connect 来启用与 Azure Active Directory 域服务托管域之间的密码哈希同步。
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: iainfou
ms.openlocfilehash: 41e61376d12d447dd480a39ef7200db6af7cca89
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73172863"
---
# <a name="tutorial-enable-password-synchronization-in-azure-active-directory-domain-services-for-hybrid-environments"></a>教程：在混合环境的 Azure Active Directory 域服务中启用密码同步

对于混合环境，可以配置一个 Azure Active Directory (Azure AD) 租户，以使用 Azure AD Connect 来与本地 Active Directory 域服务 (AD DS) 环境保持同步。 默认情况下，Azure AD Connect 不会同步 Azure Active Directory 域服务 (Azure AD DS) 所需的旧 NT LAN 管理器 (NTLM) 和 Kerberos 密码哈希。

若要将 Azure AD DS 与从本地 AD DS 环境同步的帐户配合使用，需要将 Azure AD Connect 配置为同步 NTLM 和 Kerberos 身份验证所需的密码哈希。 配置 Azure AD Connect 之后，本地帐户创建或密码更改事件也会将旧密码哈希同步到 Azure AD。

如果使用仅限云的帐户且不使用本地 AD DS 环境，则无需执行这些步骤。

在本教程中，学习以下内容：

> [!div class="checklist"]
> * 为何需要旧 NTLM 和 Kerberos 密码哈希
> * 如何为 Azure AD Connect 配置旧密码哈希同步

如果你没有 Azure 订阅，可以在开始之前[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

若要完成本教程，需要以下各资源：

* 一个有效的 Azure 订阅。
    * 如果你没有 Azure 订阅，请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，该租户已使用 Azure AD Connect 同步到本地目录。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
    * 如果需要，请[为密码哈希同步启用 Azure AD Connect][enable-azure-ad-connect]。
* 在 Azure AD 租户中启用并配置 Azure Active Directory 域服务托管域。
    * 如果需要，请[创建并配置 Azure Active Directory 域服务实例][create-azure-ad-ds-instance]。

## <a name="password-hash-synchronization-using-azure-ad-connect"></a>使用 Azure AD Connect 实现密码哈希同步

使用 Azure AD Connect 可将用户帐户和组等对象从本地 AD DS 环境同步到 Azure AD 租户。 在此过程中，密码哈希同步允许帐户在本地 AD DS 环境和 Azure AD 中使用相同的密码。

若要对托管域上的用户进行身份验证，Azure AD DS 需要密码哈希，其格式适用于 NTLM 和 Kerberos 身份验证。 除非为租户启用了 Azure AD DS，否则 Azure AD 不会以 NTLM 或 Kerberos 身份验证所需的格式存储密码哈希。 出于安全考虑，Azure AD 也不以明文形式存储任何密码凭据。 因此，Azure AD 无法基于用户的现有凭据自动生成这些 NTLM 或 Kerberos 密码哈希。

可将 Azure AD Connect 配置为同步 Azure AD DS 所需的 NTLM 或 Kerberos 密码哈希。 确保已完成[为密码哈希同步启用 Azure AD Connect][enable-azure-ad-connect] 的步骤。 如果你有 Azure AD Connect 的现有实例，请[下载并更新到最新版本][azure-ad-connect-download]，以确保可以同步 NTLM 和 Kerberos 的旧密码哈希。 此功能不可用于早期的 Azure AD Connect 版本或旧式 DirSync 工具。 需要 Azure AD Connect *1.1.614.0* 或更高版本。

## <a name="enable-synchronization-of-password-hashes"></a>启用密码哈希同步

安装 Azure AD Connect 并将其配置为与 Azure AD 同步后，接下来请配置 NTLM 和 Kerberos 的旧密码哈希同步。 可以使用一个 PowerShell 脚本来配置所需的设置，然后启动与 Azure AD 的完全密码同步。 Azure AD Connect 密码哈希同步过程完成后，用户可以通过 Azure AD DS 登录到使用旧 NTLM 或 Kerberos 密码哈希的应用程序。

1. 在安装了 Azure AD Connect 的计算机上，通过“开始”菜单打开“Azure AD Connect”>“同步服务”。 
1. 选择“连接器”选项卡。  此时会列出用于在本地 AD DS 环境与 Azure AD 之间建立同步的连接信息。

    “类型”中会列出“Windows Azure Active Directory (Microsoft)”（适用于 Azure AD 连接器）或“Active Directory 域服务”（适用于本地 AD DS 连接器）。    请记下连接器名称，以便在下一步骤所述的 PowerShell 脚本中使用。

    ![在同步服务管理器中列出连接器名称](media/tutorial-configure-password-hash-sync/service-sync-manager.png)

    此示例屏幕截图中使用了以下连接器：

    * Azure AD 连接器名为 *contoso.onmicrosoft.com - AAD*
    * 本地 AD DS 连接器名为 *onprem.contoso.com*

1. 将以下 PowerShell 脚本复制并粘贴到安装了 Azure AD Connect 的计算机上。 该脚本触发完全密码同步（包括旧密码哈希）。 使用上一步骤中的连接器名称更新 `$azureadConnector` 和 `$adConnector` 变量。

    在每个 AD 林中运行此脚本，以将本地帐户 NTLM 和 Kerberos 密码哈希同步到 Azure AD。

    ```powershell
    # Define the Azure AD Connect connector names and import the required PowerShell module
    $azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"
    $adConnector = "<CASE SENSITIVE AD DS CONNECTOR NAME>"
    Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1"

    # Create a new ForceFullPasswordSync configuration parameter object then
    # update the existing connector with this new configuration
    $c = Get-ADSyncConnector -Name $adConnector
    $p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
    $p.Value = 1
    $c.GlobalParameters.Remove($p.Name)
    $c.GlobalParameters.Add($p)
    $c = Add-ADSyncConnector -Connector $c

    # Disable and re-enable Azure AD Connect to force a full password synchronization
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true
    ```

    将旧密码哈希同步到 Azure AD 可能需要一段时间，具体时间取决于目录的大小（根据帐户和组的数目度量）。 密码在同步到 Azure AD 之后，将同步到 Azure AD DS 托管域。

## <a name="next-steps"></a>后续步骤

本教程介绍了以下内容：

> [!div class="checklist"]
> * 为何需要旧 NTLM 和 Kerberos 密码哈希
> * 如何为 Azure AD Connect 配置旧密码哈希同步

> [!div class="nextstepaction"]
> [了解 Azure AD 域服务托管域中的同步工作原理](synchronization.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[enable-azure-ad-connect]: ../active-directory/hybrid/how-to-connect-install-express.md

<!-- EXTERNAL LINKS -->
[azure-ad-connect-download]: https://www.microsoft.com/download/details.aspx?id=47594
