---
title: 同步使用电子邮件登录 Azure 的来宾用户帐户 | Microsoft Docs
description: 本文介绍如何同步使用备用 ID 登录应用程序的来宾用户帐户。
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/19/2018
ms.author: billmath
ms.openlocfilehash: 4e6cf3bb4a691380a5fe22f5afdf749b40f15ef3
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2018
---
# <a name="synchronizing-guest-user-accounts-that-use-email-for-sign-in-preview"></a>同步使用电子邮件登录的来宾用户帐户（预览版）

>[!NOTE]
> 此功能目前处于公开预览状态。

以下方案解决了本地 AD 环境中外部用户（例如合作伙伴）使用备用登录方法时可能存在的问题。

在上面的示例中，Nina Morin 在 Fabrikam 工作，她使用以下电子邮件地址：nmorin@fabrikam.com。Nina 是 Contoso 的合作伙伴，她需要访问 Contoso 的某些应用程序。 Contoso 为 Nina 创建了一个帐户，并指示 Nina 使用其电子邮件地址来登录这些应用程序。

对于 Contoso 的本地应用程序而言，此方案能够起到很大的作用。 但是，Contoso 目前正在将这些应用程序迁移到云中，并希望其合作伙伴获得相同的体验。

此方案能够解决这种需求。


## <a name="pre-requisites-and-assumptions"></a>先决条件和假设
本部分包含尝试设置此方案之前需要注意的先决条件和假设条件列表。

### <a name="pre-requisites"></a>先决条件
- 用于配置 Azure AD Connect、验证域和配置域联合身份验证设置的全局管理员凭据
- Azure AD Connect 1.1.524.0 或更高版本
- 经过验证的域，用于设置外部用户的云 UPN（示例：bmcontoso.com）。
- 用于对外部用户进行身份验证的联合身份验证服务。 如果使用 AD FS，其版本必须是 2012 R2 或更高
- 在计算机上安装 MSOL PowerShell v1.1，以验证联合身份验证设置。 有关详细信息，请参阅 [Azure ActiveDirectory (MSOnline)](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0)。


### <a name="assumptions"></a>假设 
- 已成功设置并安装 Azure AD Connect。 有关如何安装 Azure AD Connect 的信息，请参阅 [Azure AD Connect 和联合身份验证](active-directory-aadconnectfed-whatis.md)。
本文档作出以下假设：
- 已设置一个联合身份验证服务，并且该服务能够成功对用户进行身份验证。
- 外部用户可以使用其外部电子邮件地址进行身份验证。
- - 已设置并配置备用 ID 登录。 用户可以使用其备用 ID 进行身份验证。 有关设置 AD FS 备用 ID 的其他信息，请参阅[配置备用登录 ID](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)。

## <a name="task-1--prepare-the-environment"></a>任务 1：准备环境
以下任务主要提供参考，让你准备好开始同步外部帐户，使这些帐户能够使用备用 ID（例如 mail 属性）登录。

在继续执行第二个任务之前，请定义下表中的项。

![体系结构](media/active-directory-aadconnect-guest-sync/guest2.png)

|环境方面|用途|环境中的实现|
|-----|-----|-----|
|云 UPN 属性|在云中填充外部用户对象 UPN 的属性。 外部帐户的 UPN 后缀必须是先决条件中定义的值。 这是一个已验证的域。|* 示例：UserPrincipalName (nmorin@bmcontoso.com)|
|登录地址|外部用户在登录时要键入的属性。 此属性必须采用电子邮件地址格式，在大多数情况下，它与外部用户的实际电子邮件地址一致。|* 示例：mail (nmorin@fabrikam.com)|
|Azure AD Connect 范围的筛选器|允许针对外部标识，以限定本指南稍后定义的同步规则范围的筛选器。 限定范围的典型方法包括：组织中的预先定义的 OU、特定的命名约定、特定的域，等等。|* 示例：OU contains Externals|
|Azure AD 租户|向 Azure AD Connect 显示的 Azure AD 租户名称。|示例：contoso.onmicrosoft.com|

以下屏幕截图包含三个边框。
- **Externals** OU：在 Azure AD Connect 范围的筛选器中使用，是外部用户的位置。
- **mail** 属性：由外部用户在登录时使用。
- **userPrincipalName** 属性：与本地环境联合的已验证域。

![用户](media/active-directory-aadconnect-guest-sync/guest1.png)

## <a name="task-2--configure-azure-ad-connect"></a>任务 2：配置 Azure AD Connect
定义上述信息后，可以继续设置 Azure AD Connect 同步规则。 若要设置规则，请使用 Azure AD Connect 同步规则编辑器。 有关该编辑器的详细信息，请参阅[声明性预配](active-directory-aadconnectsync-understanding-declarative-provisioning.md)。

### <a name="how-to-configure-the-synchronization-rule"></a>如何配置同步规则
使用以下过程配置 Azure AD Connect。

1. 转到“开始”-“Azure AD Connect”-“同步规则编辑器”，打开 Azure AD Connect 同步规则编辑器。
2. 在“同步规则编辑器”屏幕上，确保方向为“入站”，在右侧单击“添加新规则”。
3. 在“说明”页上，配置以下设置并单击“下一步”。
    - **名称** - 输入规则的名称。 
    - **连接的系统** - 本地 AD 环境
    - **连接的系统对象类型** - user
    - **Metaverse 对象类型** - person
    - **链接类型** - Join
    - **优先顺序** - 90
    - 
![](media/active-directory-aadconnect-guest-sync/guest3.png)

4. 在“范围筛选器”屏幕上，单击“添加组”。
5. 使用下拉列表配置筛选器。 输入以下值并单击“下一步”。 此操作创建一个只应用到外部 OU 中对象的筛选器。
    - **属性** - dn
    - **运算符** - CONTAINS
    - **值** - Externals
 
 ![筛选器](media/active-directory-aadconnect-guest-sync/guest4.png)

6. 在“联接规则”屏幕上，单击“下一步”。
7. 在“转换”屏幕上，单击“添加转换”。 输入以下信息：
    - **FlowType** - Constant
    - **目标属性** - userType
    - **源** - Guest
8. 在“转换”屏幕上，单击“添加转换”。 输入以下信息：
    - **FlowType** - Direct
    - **目标属性** - onPremisesUserPrincipalName
    - **源** - mail
9. 在“转换”屏幕上，单击“添加转换”。 输入以下信息：
    - **FlowType** - Direct
    - **目标属性** - userPrincipalName
    - **源** - userPrincipalName ![转换](media/active-directory-aadconnect-guest-sync/guest5.png)
10. 单击 **“添加”**。 
11. 在“同步规则编辑器”屏幕上，确保方向为“出站”，在右侧单击“添加新规则”。
12. 在“说明”页上，配置以下设置并单击“下一步”。
    - **名称** - 输入规则的名称。 
    - **连接的系统** - Azure AD 租户
    - **连接的系统对象类型** - user
    - **Metaverse 对象类型** - person
    - **链接类型** - Join
    - **优先顺序** - 90
    - 
![规则](media/active-directory-aadconnect-guest-sync/guest6.png)

13. 在“范围筛选器”屏幕上，单击“下一步”。
14. 在“联接规则”屏幕上，单击“下一步”。
15. 在“转换”屏幕上，单击“添加转换”。  输入以下信息：
    - **FlowType** - Direct
    - **目标属性** - userType
    - **源** - userType
9. 在“转换”屏幕上，单击“添加转换”。 输入以下信息：
    - **FlowType** - Direct
    - **目标属性** - onPremisesUserPrincipalName
    - **源** - onPremisesUserPrincipalName ![转换](media/active-directory-aadconnect-guest-sync/guest7.png)
10. 单击 **“添加”**。 
11. 配置这些规则后，需要运行完全同步。 使用 PowerShell 启动完全同步。 完成同步后，可以继续执行下一步。

``` powershell
    Start-ADSyncSyncCycle -PolicyType Initial
```

## <a name="task-3--federation"></a>任务 3：联合身份验证
以下任务供参考，其中描述了在正常运行该方案之前需要做好的几项准备工作。

可以使用 Azure PowerShell 来验证 Azure AD 的联合身份验证设置。 本文档使用 MSOL PowerShell 版本 1.1。 可在[此处](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0)安装此版本。

### <a name="verify-the-federation-settings"></a>验证联合身份验证设置
使用以下过程验证联合身份验证设置。
1. 打开 Windows PowerShell，使用以下命令连接到 Azure AD：
``` powershell
      Connect-MSOLservice
```
2.  输入全局管理员凭据
3.  现在输入以下命令：
  ``` powershell
      Get-MSOLDomainFederationSettings
  ```
4.   应会返回联合身份验证信息。 请注意，**ActiveLogonUri** 是联合服务器的 URL。

  ![联合](media/active-directory-aadconnect-guest-sync/guest8.png)

### <a name="verify-alternate-login-id"></a>验证备用登录 ID
本文档使用 AD FS 作为标识提供者 (Idp)。 如果使用不同的 Idp，这些步骤可能有所不同。

1. 打开 Windows PowerShell 并输入以下命令：
   ```powershell
    Get-ADFSClaimsProviderTrust
   ```
2. 应会看到 AD FS 信息。  记下 **AlternateLoginID** 和 **LookupForests**。

![ADFS](media/active-directory-aadconnect-guest-sync/guest9.png)

## <a name="task-4--testing"></a>任务 4：测试
若要验证此方案是否正常工作，需要登录到已配置为使用 Idp 进行身份验证的终结点。 为了执行此项测试，我们在 Azure 中部署了一个网站并使用以下 URL：contososampapp.azurewebsites.net

### <a name="verify-that-you-can-sign-in-with-the-alternate-id"></a>验证是否可以使用备用 ID 登录
1. 登录到终结点。</br>
![终结点](media/active-directory-aadconnect-guest-sync/guest10.png)
1. 输入用户名，随后将会重定向到联合身份验证登录页。
![联合身份验证](media/active-directory-aadconnect-guest-sync/guest11.png)
1. 输入凭据。
2. 现在应已成功登录。
![Success](media/active-directory-aadconnect-guest-sync/guest12.png)

## <a name="next-steps"></a>后续步骤
- [Azure Active Directory B2B 协作用户的属性](../../active-directory/b2b/user-properties.md#key-properties-of-the-azure-ad-b2b-collaboration-user)
- [配置备用登录 ID](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)
- [Azure AD Connect：版本发布历史记录](active-directory-aadconnect-version-history.md)
