---
title: 混合 FIDO2 安全密钥的已知问题和故障排除-Azure Active Directory
description: '了解有关使用 Azure Active Directory (预览版对无密码混合 FIDO2 安全密钥登录进行故障排除的一些已知问题和方法) '
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 08/19/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecab82e43bff6c0d1d83c9c1cdc38cafd809e277
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/01/2020
ms.locfileid: "89236601"
---
# <a name="troubleshooting-for-hybrid-deployments-of-fido2-security-keys-in-azure-ad-preview"></a>Azure AD (预览版中的 FIDO2 安全密钥的混合部署疑难解答) 

本文介绍了有关混合 Azure AD 联接设备和无密码登录到本地资源的常见问题。 使用此无密码功能，可以使用 FIDO2 安全密钥在 Windows 10 设备上启用混合 Azure AD 加入设备的 Azure AD 身份验证。 用户可以使用新式凭据（如 FIDO2 密钥）在其设备上登录 Windows，并 Active Directory 域服务 (使用无缝单一登录 AD DS) 基于的资源，通过无缝单一登录 (的资源。

支持在混合环境中使用以下用户方案：

* 使用 FIDO2 安全密钥登录到混合 Azure AD 联接设备，并获取对本地资源的 SSO 访问权限。
* 使用 FIDO2 安全密钥登录到 Azure AD 联接的设备，并获取对本地资源的 SSO 访问权限。

若要开始使用 FIDO2 安全密钥以及对本地资源的混合访问，请参阅以下文章：

* [无密码安全密钥](howto-authentication-passwordless-security-key.md)
* [无密码 Windows 10](howto-authentication-passwordless-security-key-windows.md)
* [本地无密码](howto-authentication-passwordless-security-key-on-premises.md)

> [!NOTE]
> FIDO2 安全密钥是 Azure Active Directory 的公共预览功能。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="known-issues"></a>已知问题

* [用户无法使用 FIDO2 安全密钥登录，因为 Windows Hello 人脸太快，而且是默认登录机制](#users-are-unable-to-sign-in-using-fido2-security-keys-as-windows-hello-face-is-too-quick-and-is-the-default-sign-in-mechanism)
* [用户在创建混合 Azure AD 联接的计算机后，不能立即使用 FIDO2 安全密钥](#users-arent-able-to-use-fido2-security-keys-immediately-after-they-create-a-hybrid-azure-ad-joined-machine)
* [使用 FIDO2 安全密钥登录并收到凭据提示后，用户无法获取到 NTLM 网络资源的 SSO](#users-are-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-a-fido2-security-key-and-receiving-a-credential-prompt)

### <a name="users-are-unable-to-sign-in-using-fido2-security-keys-as-windows-hello-face-is-too-quick-and-is-the-default-sign-in-mechanism"></a>用户无法使用 FIDO2 安全密钥登录，因为 Windows Hello 人脸太快，而且是默认登录机制

Windows Hello 人脸是注册用户的设备的最佳体验。 FIDO2 安全密钥用于共享设备或 Windows Hello 企业版注册为屏障。

如果 Windows Hello 人脸阻止用户尝试 FIDO2 安全密钥登录方案，则用户可以通过在 "设置" 中删除人脸注册 **> 登录选项**来关闭 Hello 人脸登录。

### <a name="users-arent-able-to-use-fido2-security-keys-immediately-after-they-create-a-hybrid-azure-ad-joined-machine"></a>用户在创建混合 Azure AD 联接的计算机后，不能立即使用 FIDO2 安全密钥

在全新安装的混合 Azure AD 计算机上执行域加入和重新启动过程后，你必须使用密码登录并等待策略同步，然后才能使用 FIDO2 安全密钥登录。

此行为是已加入域的设备的已知限制，并不特定于 FIDO2 安全密钥。

若要检查当前状态，请使用 `dsregcmd /status` 命令。 检查 *AzureAdJoined* 和 *DomainJoined* 是否都显示 *"是"*。

### <a name="users-are-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-a-fido2-security-key-and-receiving-a-credential-prompt"></a>使用 FIDO2 安全密钥登录并收到凭据提示后，用户无法获取到 NTLM 网络资源的 SSO

请确保对足够的 Dc 进行修补，以响应资源请求。 若要检查是否可以看到运行该功能的服务器，请查看 `nltest /dsgetdc:<dc name> /keylist /kdc`

如果可以看到具有此功能的 DC，则用户的密码可能会在登录后发生更改，或者存在其他问题。 收集下面部分中详细介绍的日志，以便 Microsoft 支持团队进行调试。

## <a name="troubleshoot"></a>故障排除

有两个区域可以解决 [窗口客户端问题](#windows-client-issues)或 [部署问题](#deployment-issues)。

### <a name="windows-client-issues"></a>Windows 客户端问题

若要收集有助于排查登录 Windows 的问题或从 Windows 10 设备访问本地资源的数据，请完成以下步骤：

1. 打开 **反馈中心** 应用。 请确保名称位于应用左下方，然后选择 " **创建新的反馈项目**"。

    对于 "反馈项类型"，请选择 " *问题*"。

1. 选择 " *安全" 和 "隐私* " 类别，然后选择 " *FIDO* " 子类别。
1. 切换 " *将附加的文件和诊断发送到 Microsoft" 复选框，以及我的反馈*。
1. 选择 " *重新创建我的问题*"，然后单击 " *开始捕获*"。
1. 用 FIDO2 安全密钥锁定和解锁计算机。 如果出现此问题，请尝试用其他凭据解锁。
1. 返回到 **反馈中心**，选择 " **停止捕获**"，并提交反馈。
1. 依次前往 " *反馈* " 页和 " *我的反馈* " 选项卡。选择最近提交的反馈。
1. 在右上角选择 " *共享* " 按钮，获取反馈的链接。 当你打开支持案例或回复分配给现有支持案例的工程师时，请包含此链接。

收集以下事件日志和注册表项信息：

**注册密钥**

* *HKEY_LOCAL_MACHINE \SOFTWARE\Policies\Microsoft\FIDO [ \* ]*
* *HKEY_LOCAL_MACHINE \SOFTWARE\Policies\Microsoft\PasswordForWork \* [ \* ]*
* *HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\Policies\PasswordForWork \* [ \* ]*

**诊断信息**

* 实时内核转储
* 收集 AppX 包信息
* UIF 上下文文件

**事件日志**

* *%SystemRoot%\System32\winevt\Logs\Microsoft-Windows-AAD%40Operational.evtx*
* *%SystemRoot%\System32\winevt\Logs\Microsoft-Windows-WebAuthN%40Operational.evtx*
* *%SystemRoot%\System32\winevt\Logs\Microsoft-Windows-HelloForBusiness%40Operational.evtx*

### <a name="deployment-issues"></a>部署问题

若要解决部署 Azure AD Kerberos 服务器时遇到的问题，请使用包含在 Azure AD Connect 中的新 PowerShell 模块。

#### <a name="viewing-the-logs"></a>查看日志

Azure AD Kerberos 服务器 PowerShell cmdlet 使用与标准 Azure AD Connect 向导相同的日志记录。 若要查看 cmdlet 中的信息或错误详细信息，请完成以下步骤：

1. 在 Azure AD Connect 服务器上，浏览到 `C:\ProgramData\AADConnect\` 。 此文件夹默认已隐藏。
1. 打开并查看 `trace-*.log` 位于目录中的最新文件。

#### <a name="viewing-the-azure-ad-kerberos-server-objects"></a>查看 Azure AD Kerberos 服务器对象

若要查看 Azure AD Kerberos 服务器对象并验证它们的顺序是否正确，请完成以下步骤：

1. 在 Azure AD Connect 服务器上，打开 PowerShell 并导航到 `C:\Program Files\Microsoft Azure Active Directory Connect\AzureADKerberos\`
1. 运行以下 PowerShell 命令，从 Azure AD 和本地 AD DS 查看 Azure AD Kerberos 服务器。

    将 *corp.contoso.com* 替换为本地 AD DS 域的名称。

    ```powershell
    Import-Module ".\AzureAdKerberos.psd1"

    # Specify the on-premises AD DS domain.
    $domain = "corp.contoso.com"

    # Enter an Azure Active Directory Global Admin username and password.
    $cloudCred = Get-Credential

    # Enter a Domain Admin username and password.
    $domainCred = Get-Credential

    # Get the Azure AD Kerberos Server Object
    Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential
    $domainCred
    ```

命令从 Azure AD 和本地 AD DS 输出 Azure AD Kerberos 服务器的属性。 查看属性以验证所有内容是否都正确。 使用下表验证属性。

第一组属性来自本地 AD DS 环境中的对象。 第二部分 (以 * 云 * * 开头的属性从 Azure AD 中的 Kerberos Server 对象 ) ：

| 属性           | 说明  |
|--------------------|--------------|
| ID                 | AD DS 域控制器对象的唯一 *Id* 。 |
| DomainDnsName      | AD DS 域的 DNS 域名。 |
| ComputerAccount    | DC)  (Azure AD Kerberos Server 对象的计算机帐户对象。 |
| UserAccount        | 已禁用的用户帐户对象，该对象保存 Azure AD Kerberos 服务器 TGT 加密密钥。 此帐户的 DN 是 *cn = krbtgt_AzureAD，cn = Users，<Domain-DN>* |
| KeyVersion         | Azure AD Kerberos 服务器 TGT 加密密钥的密钥版本。 创建密钥时，将分配该版本。 然后，每次对密钥进行旋转时，版本都会递增。 增量是基于复制元数据的，可能大于1。<br /><br /> 例如，初始 *KeyVersion* 可能为 *192272*。 第一次轮换密钥时，版本可能会前进到 *212621*。<br /><br /> 要验证的重要一点是，本地对象的 *KeyVersion* 和云对象的 *CloudKeyVersion* 是相同的。 |
| KeyUpdatedOn       | 更新或创建 Azure AD Kerberos 服务器 TGT 加密密钥的日期和时间。 |
| KeyUpdatedFrom     | 上次更新 Azure AD Kerberos 服务器 TGT 加密密钥的 DC。 |
| CloudId            | Azure AD 对象的 *Id* 。 必须与上述 *Id* 匹配。 |
| CloudDomainDnsName | Azure AD 对象中的 *DomainDnsName* 。 必须与上面的 *DomainDnsName* 匹配。 |
| CloudKeyVersion    | Azure AD 对象中的 *KeyVersion* 。 必须与上面的 *KeyVersion* 匹配。 |
| CloudKeyUpdatedOn  | Azure AD 对象中的 *KeyUpdatedOn* 。 必须与上面的 *KeyUpdatedOn* 匹配。 |

## <a name="next-steps"></a>后续步骤

若要开始使用 FIDO2 安全密钥以及对本地资源的混合访问，请参阅以下文章：

* [无密码 FIDO2 安全密钥](howto-authentication-passwordless-security-key.md)
* [无密码 Windows 10](howto-authentication-passwordless-security-key-windows.md)
* [无密码本地](howto-authentication-passwordless-security-key-on-premises.md)
