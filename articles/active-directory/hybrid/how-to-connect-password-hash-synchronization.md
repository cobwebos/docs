---
title: 使用 Azure AD Connect 同步实现密码哈希同步 | Microsoft Docs
description: 介绍密码哈希同步的工作原理及设置方法。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 05f16c3e-9d23-45dc-afca-3d0fa9dbf501
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/02/2019
ms.subservice: hybrid
ms.author: billmath
search.appverid:
- MET150
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0398ff7eb8931acc400b326ff92deaf75f0aa97e
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/09/2019
ms.locfileid: "73882837"
---
# <a name="implement-password-hash-synchronization-with-azure-ad-connect-sync"></a>使用 Azure AD Connect 同步实现密码哈希同步
本文提供将用户密码从本地 Active Directory 实例同步到基于云的 Azure Active Directory (Azure AD) 实例时所需的信息。

## <a name="how-password-hash-synchronization-works"></a>密码哈希同步的工作原理
Active Directory 域服务以实际用户密码的哈希值表示形式存储密码。 哈希值是单向数学函数（哈希算法）的计算结果。 没有任何方法可将单向函数的结果还原为纯文本版本的密码。 

为了同步密码，Azure AD Connect 同步将从本地 Active Directory 实例提取密码哈希。 同步到 Azure Active Directory 身份验证服务之前，已对密码哈希应用其他安全处理。 密码基于每个用户按时间顺序同步。

密码哈希同步过程的实际数据流类似于用户数据的同步。 但是，密码的同步频率高于其他属性的标准目录同步窗口。 密码哈希同步过程每隔 2 分钟运行一次。 无法修改此过程的运行频率。 同步某个密码时，该密码将覆盖现有的云密码。

首次启用密码哈希同步功能时，它将对范围内的所有用户执行初始密码同步。 无法显式定义一部分要同步的用户密码。 但是，如果有多个连接器，则可以使用[ADSyncAADPasswordSyncConfiguration](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant) cmdlet 为某些连接器禁用密码哈希同步，但不允许使用其他连接器。

更改本地密码时，更新后的密码会同步，此操作基本上在几分钟内就可完成。
密码哈希同步功能会自动重试失败的同步尝试。 如果尝试同步密码期间出现错误，该错误会被记录在事件查看器中。

同步密码对当前登录的用户没有任何影响。
当前的云服务会话不会立即受到已同步密码更改的影响，而是在登录云服务时才受到影响。 但是，当云服务要求再次身份验证时，就需要提供新的密码。

无论用户是否已登录到其公司网络，都必须第二次输入其公司凭据，以便向 Azure AD 进行身份验证。 但是，如果用户在登录时选中了“使我保持登录状态(KMSI)”复选框，则可以最大限度地避开这些模式。 这样选择可设置会话 Cookie 以在 180 天内绕过身份验证。 Azure AD 管理员可以启用或禁用 KMSI 行为。 此外，可以通过启用[无缝 SSO](how-to-connect-sso.md) 来减少密码提示，该无缝 SSO 可使连接到企业网络的企业设备上的用户自动登录。

> [!NOTE]
> 只有 Active Directory 的对象类型用户才支持密码同步。 不支持 iNetOrgPerson 对象类型。

### <a name="detailed-description-of-how-password-hash-synchronization-works"></a>密码哈希同步工作原理的详细说明

以下部分将深入说明 Active Directory 与 Azure AD 之间的密码哈希同步工作原理。

![详细的密码流程](./media/how-to-connect-password-hash-synchronization/arch3b.png)

1. 每隔两分钟，AD Connect 服务器上的密码哈希同步代理都会从 DC 请求存储的密码哈希（unicodePwd 属性）。  此请求通过用于同步 DC 之间数据的标准 [MS-DRSR](https://msdn.microsoft.com/library/cc228086.aspx) 复制协议进行。 服务帐户必须具有“复制目录更改”和“复制所有目录更改”AD 权限（默认情况下，在安装时授予），才能获取密码哈希。
2. 在发送前，DC 将使用密钥（即 RPC 会话密钥的 [MD5](https://www.rfc-editor.org/rfc/rfc1321.txt) 哈希）和 salt 对 MD4 密码哈希进行加密。 然后，它通过 RPC 将结果发送到密码哈希同步代理。 DC 还使用 DC 复制协议将 salt 传递给同步代理，因此该代理能够解密信封。
3. 密码哈希同步代理获得加密的信封后，将使用 [MD5CryptoServiceProvider](https://msdn.microsoft.com/library/System.Security.Cryptography.MD5CryptoServiceProvider.aspx)和 salt 生成密钥，以便将收到的数据重新解密为其原始的 MD4 格式。 密码哈希同步代理永远无权访问明文密码。 密码哈希同步代理使用 MD5 完全是为了实现与 DC 的复制协议兼容性，并仅在本地的 DC 和密码哈希同步代理之间使用。
4. 密码哈希同步代理通过先将哈希转换为 32 字节的十六进制字符串，然后使用 UTF-16 编码重新将此字符串转换为二进制，来将 16 字节的二进制密码哈希扩展为 64 字节。
5. 密码哈希同步代理通过将每个用户的 salt（包含 10 字节长度的 salt）添加到 64 字节的二进制字符串，来进一步保护原始哈希。
6. 然后，密码哈希同步代理将 MD4 哈希与每个用户的 salt 组合在一起，并将其输入到 [PBKDF2](https://www.ietf.org/rfc/rfc2898.txt) 函数。 使用 [HMAC-SHA256](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) 键控哈希算法的 1000 次迭代。 
7. 密码哈希同步代理获取生成的 32 字节哈希，将每个用户的 salt 和 SHA256 迭代次数连接到它（以供 Azure AD 使用），然后通过 SSL 将该字符串从 Azure AD Connect 传输到 Azure AD。</br> 
8. 当用户尝试登录到 Azure AD 并输入其密码时，将通过同一 MD4+salt+PBKDF2+HMAC-SHA256 过程运行密码。 如果生成的哈希与 Azure AD 中存储的哈希匹配，则用户输入的密码正确并进行身份验证。

> [!NOTE]
> 原始 MD4 哈希不会传送到 Azure AD。 与之相反，传输的是原始 MD4 哈希的 SHA256 哈希。 因此，如果获取了 Azure AD 中存储的哈希，将无法在本地“传递哈希”攻击中使用。

### <a name="security-considerations"></a>安全注意事项

同步密码时，纯文本版本的密码既不能向密码哈希同步功能公开，也不能向 Azure AD 或任何相关联的服务公开。

用户身份验证针对 Azure AD（而不是针对组织自己的 Active Directory 实例）进行。 Azure AD 中存储的 SHA 256 密码数据（原始 MD4 哈希的哈希）比 Active Directory 中存储的数据更安全。 而且，由于此 SHA256 哈希无法解密，因此无法将其带回到组织的 Active Directory 环境，并且在“传递哈希”攻击中显示为有效的用户密码。

### <a name="password-policy-considerations"></a>密码策略注意事项

有两种类型的密码策略受启用密码哈希同步的影响：

* 密码复杂性策略
* 密码过期策略

#### <a name="password-complexity-policy"></a>密码复杂性策略

启用密码哈希同步时，本地 Active Directory 实例中的密码复杂性策略会覆盖云中为同步的用户定义的复杂性策略。 可以使用本地 Active Directory 实例的所有有效密码来访问 Azure AD 服务。

> [!NOTE]
> 直接在云中创建的用户的密码仍受到云中定义的密码策略的约束。

#### <a name="password-expiration-policy"></a>密码过期策略

如果用户处于密码哈希同步的作用域中，则默认情况下，云帐户密码设置为*永不过期*。

可以继续使用在本地环境中过期的同步密码来登录云服务。 下次在本地环境中更改密码时，云密码会更新。

##### <a name="public-preview-of-the-enforcecloudpasswordpolicyforpasswordsyncedusers-feature"></a>*EnforceCloudPasswordPolicyForPasswordSyncedUsers*功能的公共预览版

如果有同步用户仅与 Azure AD 集成服务交互，并且还必须符合密码过期策略，则可以通过启用此*策略来强制他们遵守 Azure AD 密码过期策略EnforceCloudPasswordPolicyForPasswordSyncedUsers*功能。

禁用*EnforceCloudPasswordPolicyForPasswordSyncedUsers*时（这是默认设置），Azure AD Connect 将同步用户的 PasswordPolicies 属性设置为 "DisablePasswordExpiration"。 这是在每次同步用户密码时执行的，并指示 Azure AD 忽略该用户的云密码过期策略。 可以通过以下命令使用 Azure AD PowerShell 模块检查属性的值：

`(Get-AzureADUser -objectID <User Object ID>).passwordpolicies`


若要启用 EnforceCloudPasswordPolicyForPasswordSyncedUsers 功能，请使用 MSOnline PowerShell 模块运行以下命令：

`Set-MsolDirSyncFeature -Feature EnforceCloudPasswordPolicyForPasswordSyncedUsers -Enable $true`

启用后，Azure AD 不会进入每个同步用户，从 PasswordPolicies 属性中删除 `DisablePasswordExpiration` 值。 相反，当用户下次在本地 AD 中更改密码时，此值将设置为每个用户下次密码同步期间 `None`。  

建议在启用密码哈希同步之前启用 EnforceCloudPasswordPolicyForPasswordSyncedUsers，以便初始同步密码哈希不会将 `DisablePasswordExpiration` 值添加到用户的 PasswordPolicies 属性。

默认 Azure AD 密码策略要求用户每隔90天更改其密码。 如果 AD 中的策略也是90天，则这两个策略应该匹配。 但是，如果 AD 策略不是90天，则可以使用 Set-msolpasswordpolicy PowerShell 命令更新要匹配的 Azure AD 密码策略。

Azure AD 支持每个注册域单独的密码过期策略。

注意：如果在 Azure AD 中有需要使用不过期密码的同步帐户，则必须将 `DisablePasswordExpiration` 值显式添加到 Azure AD 中的用户对象的 PasswordPolicies 属性。  可以通过运行以下命令来执行此操作。

`Set-AzureADUser -ObjectID <User Object ID> -PasswordPolicies "DisablePasswordExpiration"`

> [!NOTE]
> 此功能目前处于公共预览阶段。

#### <a name="public-preview-of-synchronizing-temporary-passwords-and-force-password-on-next-logon"></a>用于同步临时密码和 "下次登录时强制密码" 的公共预览版

典型的做法是强制用户在首次登录时更改其密码，尤其是在管理员密码重置发生之后。  它通常称为设置 "临时" 密码，并通过选中 Active Directory （AD）中用户对象的 "用户必须在下次登录时更改密码" 标志来完成。
  
临时密码功能有助于确保在第一次使用时完成凭据的所有权转移，以最大程度地减少多个人员有权了解该凭据的时间。

若要支持同步用户的 Azure AD 中的临时密码，可以通过在 Azure AD Connect 服务器上运行以下命令来启用*ForcePasswordResetOnLogonFeature*功能，并将 <AAD Connector Name> 替换为特定于的连接器名称你的环境：

`Set-ADSyncAADCompanyFeature -ConnectorName "<AAD Connector name>" -ForcePasswordResetOnLogonFeature $true`

可以使用以下命令确定连接器名称：

`(Get-ADSyncConnector | where{$_.ListName -eq "Windows Azure Active Directory (Microsoft)"}).Name`

注意事项：强制用户在下次登录时更改其密码需要同时更改密码。  AD Connect 不会自行选取强制密码更改标志，它是在密码哈希同步过程中检测到的密码更改的补充。

> [!CAUTION]
> 如果未 Azure AD 在中启用自助服务密码重置（SSPR），则当用户在 Azure AD 中重置其密码时，将有一个令人困惑的体验，然后尝试使用新密码登录 Active Directory，因为新密码在 Active Directory 中无效. 仅当对租户启用了 SSPR 和密码写回时，才应使用此功能。

> [!NOTE]
> 此功能目前处于公共预览阶段。

#### <a name="account-expiration"></a>帐户过期

如果组织在用户帐户管理中使用了 accountExpires 属性，此属性不会同步到 Azure AD。 因此，环境中为密码哈希同步配置的过期 Active Directory 帐户仍会在 Azure AD 中处于活动状态。 我们建议，如果帐户已过期，工作流操作应触发一个 PowerShell 脚本以禁用用户的 Azure AD 帐户（使用 [Set-AzureADUser](https://docs.microsoft.com/powershell/module/azuread/set-azureaduser?view=azureadps-2.0) cmdlet）。 相反，在启用帐户后，Azure AD 实例应该开启。

### <a name="overwrite-synchronized-passwords"></a>覆盖已同步的密码

管理员可以使用 Windows PowerShell 手动重置密码。

在这种情况下，新密码会覆盖已同步密码，并且在云中定义的所有密码策略都会应用于新的密码。

如果再次更改本地密码，新密码则会同步到云，并会手动覆盖更新的密码。

同步密码对登录的 Azure 用户没有任何影响。 当前的云服务会话不会立即受到已同步密码更改的影响，而是在登录云服务时才受到影响。 KMSI 会延长此差异的持续时间。 当云服务要求再次身份验证时，需要提供新的密码。

### <a name="additional-advantages"></a>其他优点

- 通常情况下，密码哈希同步比联合身份验证服务易于实现。 它不需要任何其他服务器，并且不依赖于高度可用的联合身份验证服务来对用户进行身份验证。
- 除了联合身份验证，还可以启用密码哈希同步。 如果联合身份验证服务发生了中断，它可以用作回退。

## <a name="password-hash-sync-process-for-azure-ad-domain-services"></a>Azure AD 域服务的密码哈希同步过程

如果使用 Azure AD 域服务为需要使用 Keberos、LDAP 或 NTLM 的应用程序和服务提供旧身份验证，则某些附加的进程是密码哈希同步流的一部分。 Azure AD Connect 使用以下其他过程将密码哈希同步到 Azure AD 以便在 Azure AD 域服务中使用：

> [!IMPORTANT]
> 仅当你为 Azure AD 租户启用 Azure AD DS 时，Azure AD Connect 才同步旧密码哈希。 如果只使用 Azure AD Connect 将本地 AD DS 环境与 Azure AD 同步，则不会使用以下步骤。
>
> 如果旧版应用程序不使用 NTLM 身份验证或 LDAP 简单绑定，则建议你禁用 Azure AD DS 的 NTLM 密码哈希同步。 有关详细信息，请参阅[禁用弱密码套件和 NTLM 凭据哈希同步](../../active-directory-domain-services/secure-your-domain.md)。

1. Azure AD Connect 检索 Azure AD 域服务的租户实例的公钥。
1. 当用户更改其密码时，本地域控制器将在两个属性中存储密码更改（哈希）的结果：
    * NTLM 密码哈希的*unicodePwd* 。
    * Kerberos 密码哈希的*supplementalCredentials* 。
1. Azure AD Connect 通过目录复制通道（需要复制到其他域控制器的属性更改）来检测密码更改。
1. 对于每个更改了其密码的用户，Azure AD Connect 执行以下步骤：
    * 生成随机 AES 256 位对称密钥。
    * 生成第一轮加密所需的随机初始化向量。
    * 从*supplementalCredentials*属性中提取 Kerberos 密码哈希。
    * 检查 "Azure AD 域服务安全配置*SyncNtlmPasswords* " 设置。
        * 如果禁用此设置，则会生成随机的高熵 NTLM 哈希（与用户的密码不同）。 然后，将此哈希与从*supplementalCrendetials*属性 Exacted 的 Kerberos 密码哈希合并到一个数据结构中。
        * 如果启用，则将*unicodePwd*属性的值与从*supplementalCredentials*属性提取的 Kerberos 密码哈希合并到一个数据结构中。
    * 使用 AES 对称密钥加密单一数据结构。
    * 使用租户的 Azure AD 域服务公钥对 AES 对称密钥进行加密。
1. Azure AD Connect 将传输加密的 AES 对称密钥、包含密码哈希的加密数据结构以及要 Azure AD 的初始化向量。
1. Azure AD 存储加密的 AES 对称密钥、加密的数据结构和用户的初始化向量。
1. Azure AD 使用通过加密 HTTP 会话的内部同步机制将加密的 AES 对称密钥、加密的数据结构和初始化向量推送到 Azure AD 域服务。
1. Azure AD 域服务通过 Azure 密钥保管库检索租户实例的私钥。
1. 对于每个加密的数据集（表示单个用户的密码更改），Azure AD 域服务，然后执行以下步骤：
    * 使用其私钥对 AES 对称密钥进行解密。
    * 使用带有初始化向量的 AES 对称密钥来解密包含密码哈希的加密数据结构。
    * 将接收到的 Kerberos 密码哈希写入 Azure AD 域服务域控制器。 哈希将保存到用户对象的*supplementalCredentials*属性中，该属性已加密为域服务域控制器的公钥 Azure AD。
    * Azure AD 域服务将其收到的 NTLM 密码哈希写入 Azure AD 域服务域控制器。 哈希将保存到用户对象的*unicodePwd*属性中，该属性已加密为域服务域控制器的公钥 Azure AD。

## <a name="enable-password-hash-synchronization"></a>启用密码哈希同步

>[!IMPORTANT]
>如果要从 AD FS（或其他联合技术）迁移到密码哈希同步，我们强烈建议你按照[此处](https://aka.ms/adfstophsdpdownload)发布的详细部署指南进行操作。

使用“快速设置”选项安装 Azure AD Connect 时，会自动启用密码哈希同步。 有关详细信息，请参阅[通过快速设置开始使用 Azure AD Connect](how-to-connect-install-express.md)。

如果在安装 Azure AD Connect 时使用了自定义设置，则可在用户登录页上使用密码哈希同步。 有关详细信息，请参阅 [Azure AD Connect 的自定义安装](how-to-connect-install-custom.md)。

![启用密码哈希同步](./media/how-to-connect-password-hash-synchronization/usersignin2.png)

### <a name="password-hash-synchronization-and-fips"></a>密码哈希同步和 FIPS
如果已经根据美国联邦信息处理标准 (FIPS) 锁定服务器，则会禁用 MD5。

**若要为密码哈希同步启用 MD5，请执行以下步骤：**

1. 转到 %programfiles%\Azure AD Sync\Bin。
2. 打开 miiserver.exe.config。
3. 转到文件末尾的 configuration/runtime 节点。
4. 添加以下节点：`<enforceFIPSPolicy enabled="false"/>`
5. 保存所做更改。

下面显示了此代码片段的大致情况，供参考：

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

有关安全性与 FIPS 的信息，请参阅 [Azure AD password hash sync, encryption, and FIPS compliance](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/)（Azure AD 密码哈希同步、加密和 FIPS 符合性）。

## <a name="troubleshoot-password-hash-synchronization"></a>排查密码哈希同步问题
如果遇到密码哈希同步问题，请参阅[排查密码哈希同步问题](tshoot-connect-password-hash-synchronization.md)。

## <a name="next-steps"></a>后续步骤
* [Azure AD Connect 同步：自定义同步选项](how-to-connect-sync-whatis.md)
* [将本地标识与 Azure Active Directory 集成](whatis-hybrid-identity.md)
* [获取有关从 ADFS 迁移到密码哈希同步的分步部署计划](https://aka.ms/authenticationDeploymentPlan)
