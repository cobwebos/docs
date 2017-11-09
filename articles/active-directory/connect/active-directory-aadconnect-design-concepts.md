---
title: "Azure AD Connect 设计概念 | Microsoft Docs"
description: "本主题详细说明某些实现设计方面的问题"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 4114a6c0-f96a-493c-be74-1153666ce6c9
ms.service: active-directory
ms.custom: azure-ad-connect
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 4041cacd72b1db74012497287030faf5d05ee6bf
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2017
---
# <a name="azure-ad-connect-design-concepts"></a>Azure AD Connect：设计概念
本主题旨在说明 Azure AD Connect 实现设计期间必须考虑到的各个方面。 本主题是特定领域的深入探讨，其他主题中也简要描述了这些概念。

## <a name="sourceanchor"></a>sourceAnchor
sourceAnchor 属性定义为*在对象生存期内不会变化的属性*。 它可将对象唯一标识为本地和 Azure AD 中的相同对象。 该属性也称为 **immutableId**，这两个名称可以换用。

在本主题中，“不可变”（即无法更改）一词非常重要。 由于此属性的值在设置之后就无法更改，因此请务必挑选可支持方案的设计。

该属性用于以下方案︰

* 构建新的同步引擎服务器，或者在灾难恢复方案后进行重建时，此属性会将 Azure AD 中的现有对象链接到本地对象。
* 如果从仅限云的标识转移到已同步的标识模型，则此属性可让对象将 Azure AD 中的现有对象与本地对象进行“硬匹配”。
* 如果使用联合，此属性将与 **userPrincipalName** 一起在声明中使用，以唯一标识用户。

本主题只讨论与用户相关的 sourceAnchor。 相同的规则适用于所有对象类型，但只有用户才需要考虑这个问题。

### <a name="selecting-a-good-sourceanchor-attribute"></a>选择良好的 sourceAnchor 属性
属性值必须遵循以下规则：

* 长度小于 60 个字符
  * 系统将 a-z、A-Z 或 0-9 以外的字符编码并计为 3 个字符
* 不包含特殊字符：&#92; ! # $ % & * + / = ? ^ &#96; { } | ~ < > ( ) ' ; : , [ ] " @ _
* 必须全局唯一
* 必须是字符串、整数或二进制数
* 不应基于用户的名称
* 不应区分大小写，避免使用可能因大小写而改变的值
* 应在创建对象时分配

如果选定的 sourceAnchor 不是字符串类型，Azure AD Connect 会将此属性值进行 Base64Encode 处理，确保不会出现特殊字符。 如果使用除 ADFS 以外的其他联合服务器，请确保服务器也能将此属性进行 Base64Encode 处理。

sourceAnchor 属性区分大小写。 “JohnDoe”与“johndoe”是不同的值。 但是，两个对象的不同之处不能只是大小写不同。

如果有单个本地林，应使用属性 **objectGUID**。 这也是在 Azure AD Connect 中使用快速设置时所用的属性，而且也是 DirSync 所用的属性。

如果有多个林，并且不在林和域之间移动用户，则 **objectGUID** 是适当的属性（即使在本例中）。

如果要在林和域之间移动用户，必须查找不会更改的属性或者在移动时可随用户移动的属性。 建议的方法是引入合成属性。 可以保存 GUID 等信息的属性也可能适用。 在对象创建期间，将创建新的 GUID 创建并对用户加上戳记。 可以在同步引擎服务器中创建自定义同步规则，根据 **objectGUID** 创建此值，并在 ADDS 中更新选择的属性。 当移动对象时，请务必同时复制此值的内容。

另一个解决方案是选择已知不会更改的现有属性。 常用的属性包括 **employeeID**。 如果打算使用包含字母的属性，请确保属性值的大小写（大写与小写）不会更改。 例如，包含用户姓名的属性就是不应使用的不当属性。 因为在结婚或离婚时，此姓名很可能会更改，所以不适用于此属性。 这也是无法在 Azure AD Connect 安装向导中选择 **userPrincipalName**、**mail** 和 **targetAddress** 等属性的原因之一。 这些属性还包含“@”字符，sourceAnchor 中不允许此字符。

### <a name="changing-the-sourceanchor-attribute"></a>更改 sourceAnchor 属性
在 Azure AD 中创建对象并同步标识之后，无法更改 sourceAnchor 属性值。

出于此原因，Azure AD Connect 实施以下限制：

* 只能在初始安装期间设置 sourceAnchor 属性。 如果重新运行安装向导，此选项是只读的。 如果需要更改此设置，必须卸载然后重新安装。
* 如果要安装其他 Azure AD Connect 服务器，则必须选择以前所用的同一 sourceAnchor 属性。 如果以前使用 DirSync，现在想要迁移到 Azure AD Connect，则必须使用 **objectGUID**，因为这是 DirSync 所用的属性。
* 如果 sourceAnchor 值在对象导出到 Azure AD 之后发生更改，Azure AD Connect Sync 将引发错误，并且不允许在更正问题且在源目录中改回 sourceAnchor 之前，对此对象进行任何其他更改。

## <a name="using-msds-consistencyguid-as-sourceanchor"></a>将 msDS-ConsistencyGuid 用作 sourceAnchor
默认情况下，Azure AD Connect（1.1.486.0 及更高版本）将 objectGUID 用作 sourceAnchor 属性。 ObjectGUID 是系统生成的。 创建本地 AD 对象时，不能指定其值。 如 [sourceAnchor](#sourceanchor) 部分所述，在某些情况下，需要指定 sourceAnchor 值。 如果方案适用，则必须使用可配置的 AD 属性（例如 msDS-ConsistencyGuid）作为 sourceAnchor 属性。

Azure AD Connect（1.1.524.0 及更高版本）现在可以方便地将 msDS-ConsistencyGuid 用作 sourceAnchor 属性。 使用此功能时，Azure AD Connect 会自动配置同步规则，以便：

1. 将 msDS-ConsistencyGuid 用作用户对象的 sourceAnchor 属性。 ObjectGUID 用于其他对象类型。

2. 对于任何给定的本地 AD 用户对象，如果其 msDS-ConsistencyGuid 属性未填充，Azure AD Connect 会将其 objectGUID 值写回到本地 Active Directory 中的 msDS-ConsistencyGuid 属性。 填充 msDS-ConsistencyGuid 属性以后，Azure AD Connect 就会将对象导出到 Azure AD。

>[!NOTE]
> 一旦将本地 AD 对象导入 Azure AD Connect（即，导入 AD 连接器空间并投影到 Metaverse），就再也不能更改其 sourceAnchor 值。 要为给定的本地 AD 对象指定 sourceAnchor 值，请先配置其 msDS-ConsistencyGuid 属性，然后再将其导入 Azure AD Connect。

### <a name="permission-required"></a>所需的权限
若要使用此功能，必须向用来通过本地 Active Directory 进行同步的 AD DS 帐户授予对本地 Active Directory 中的 msDS-ConsistencyGuid 属性的写入权限。

### <a name="how-to-enable-the-consistencyguid-feature---new-installation"></a>如何启用 ConsistencyGuid 功能 - 新安装
你可以在进行新安装期间将 ConsistencyGuid 用作 sourceAnchor。 本节将详细介绍 Express 和自定义安装。

  >[!NOTE]
  > 仅较新版本的 Azure AD Connect（1.1.524.0 及更高版本）支持在新安装期间将 ConsistencyGuid 用作 sourceAnchor。

### <a name="how-to-enable-the-consistencyguid-feature"></a>如何启用 ConsistencyGuid 功能
目前，该功能只能在新 Azure AD Connect 安装期间启用。

#### <a name="express-installation"></a>快速安装
使用“快速”模式安装 Azure AD Connect 时，Azure AD Connect 向导会根据以下逻辑，自动确定最适合用作 sourceAnchor 属性的 AD 属性：

* 首先，Azure AD Connect 向导会查询 Azure AD 租户，以便检索在上一 Azure AD Connect 安装（如果进行过）中用作 sourceAnchor 属性的 AD 属性。 如果该信息可用，Azure AD Connect 会使用同一 AD 属性。

  >[!NOTE]
  > 仅较新版的 Azure AD Connect（1.1.524.0 及更高版本）会将新安装期间所使用的 sourceAnchor 属性的相关信息存储在 Azure AD 租户中。 较旧版的 Azure AD Connect 不这样做。

* 如果所用的 sourceAnchor 属性的相关信息不可用，向导会检查你本地 Active Directory 中的 msDS-ConsistencyGuid 属性的状态。 如果该属性未在目录中的任何对象上配置，向导会将 msDS-ConsistencyGuid 用作 sourceAnchor 属性。 如果该属性已在目录中的一个或多个对象上配置，向导就会认为该属性正由其他应用程序使用，不适合用作 sourceAnchor 属性...

* 在这种情况下，向导会退回来，使用 objectGUID 作为 sourceAnchor 属性。

* 确定 sourceAnchor 属性以后，向导会将信息存储在 Azure AD 租户中。 该信息会供将来的 Azure AD Connect 安装使用。

快速安装完成后，向导会通知你已选取哪个属性作为“源定位点”属性。

![向导会通知为 sourceAnchor 选取的 AD 属性。](./media/active-directory-aadconnect-design-concepts/consistencyGuid-01.png)

#### <a name="custom-installation"></a>自定义安装
使用“自定义”模式安装 Azure AD Connect 时，Azure AD Connect 向导在配置 sourceAnchor 属性时提供两个选项：

![自定义安装 - sourceAnchor 配置](./media/active-directory-aadconnect-design-concepts/consistencyGuid-02.png)

| 设置 | 说明 |
| --- | --- |
| 让 Azure 为我管理源定位点 | 如果想要 Azure AD 选取属性，请选择此选项。 如果选择此选项，Azure AD Connect 向导会应用[在快速安装时使用的 sourceAnchor 属性选择逻辑](#express-installation)。 与快速安装类似，自定义安装完成后，向导会通知你已选取哪个属性作为“源定位点”属性。 |
| 特定的属性 | 如果希望指定现有的 AD 属性作为 sourceAnchor 属性，请选择此选项。 |

### <a name="how-to-enable-the-consistencyguid-feature---existing-deployment"></a>如何启用 ConsistencyGuid 功能 - 现有部署
如果你有现有的 Azure AD Connect 部署，它使用 objectGUID 作为 Source Anchor 属性，可将它切换至改为使用 ConsistencyGuid。

>[!NOTE]
> 仅较新版本的 Azure AD Connect（1.1.552.0 及更高版本）支持从使用 ObjectGuid 切换为使用 ConsistencyGuid 作为 Source Anchor 属性。

从使用 ObjectGuid 切换为使用 ConsistencyGuid 作为 Source Anchor 属性：

1. 启动 Azure AD Connect 向导，然后单击“配置”转到“任务”屏幕。

2. 选择“配置 Source Anchor”任务选项并单击“下一步”。

   ![为现有部署启用 ConsistencyGuid - 步骤 2](./media/active-directory-aadconnect-design-concepts/consistencyguidexistingdeployment01.png)

3. 输入 Azure AD 管理员凭据，然后单击“下一步”。

4. Azure AD Connect 向导会分析本地 Active Directory 中 msDS-ConsistencyGuid 属性的状态。 如果未在目录中的任何对象上配置该属性，Azure AD Connect 可以确定任何其他应用程序当前均未使用该属性，可以安全地将其用作 Source Anchor 属性。 单击“下一步”以继续。

   ![为现有部署启用 ConsistencyGuid - 步骤 4](./media/active-directory-aadconnect-design-concepts/consistencyguidexistingdeployment02.png)

5. 在“准备好配置”屏幕上，单击“配置”进行配置更改。

   ![为现有部署启用 ConsistencyGuid - 步骤 5](./media/active-directory-aadconnect-design-concepts/consistencyguidexistingdeployment03.png)

6. 完成配置后，该向导将指示 msDS-ConsistencyGuid 现正用作 Source Anchor 属性。

   ![为现有部署启用 ConsistencyGuid - 步骤 6](./media/active-directory-aadconnect-design-concepts/consistencyguidexistingdeployment04.png)

分析期间（步骤 4），如果该属性已在目录中的一个或多个对象上配置，向导就会认为该属性正由其他应用程序使用，于是返回一个错误，如下图所示。 如果先前已在 Azure AD Connect 主服务器上启用了 ConsistencyGuid 功能，并且要尝试在暂存服务器上执行相同的操作，也会发生此错误。

![为现有部署启用 ConsistencyGuid - 错误](./media/active-directory-aadconnect-design-concepts/consistencyguidexistingdeploymenterror.png)

 如果确定其他现有应用程序不使用该属性，则可以通过在指定“/SkipLdapSearchcontact”的情况下重启 Azure AD Connect 向导来取消显示该错误。 为此，请在命令提示符下运行以下命令：

```
"c:\Program Files\Microsoft Azure Active Directory Connect\AzureADConnect.exe" /SkipLdapSearch
```

### <a name="impact-on-ad-fs-or-third-party-federation-configuration"></a>对 AD FS 或第三方联合身份验证配置的影响
如果使用 Azure AD Connect 管理本地 AD FS 部署，Azure AD Connect 会自动更新声明规则，使用同一 AD 属性作为 sourceAnchor。 这样可确保由 ADFS 生成的 ImmutableID 声明与导出到 Azure AD 的 sourceAnchor 值一致。

如果在 Azure AD Connect 外部管理 AD FS，或者使用第三方联合身份验证服务器进行身份验证，则必须手动更新声明规则，以便 ImmutableID 声明与导出到 Azure AD 的 sourceAnchor 值一致，详见文章的[修改 AD FS 声明规则](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-management#modclaims)部分。 安装完成后，向导会返回以下警告：

![第三方联合身份验证配置](./media/active-directory-aadconnect-design-concepts/consistencyGuid-03.png)

### <a name="adding-new-directories-to-existing-deployment"></a>将新目录添加到现有部署
假设你在部署 Azure AD Connect 时启用了 ConsistencyGuid 功能，现在要将另一目录添加到部署中。 尝试添加目录时，Azure AD Connect 向导会检查目录中 mSDS-ConsistencyGuid 属性的状态。 如果该属性已在目录中的一个或多个对象上配置，向导就会认为该属性正由其他应用程序使用，于是返回一个错误，如下图所示。 如果确定该属性未由现有应用程序使用，则需联系支持部门，了解如何取消显示该错误。

![将新目录添加到现有部署](./media/active-directory-aadconnect-design-concepts/consistencyGuid-04.png)

## <a name="azure-ad-sign-in"></a>Azure AD 登录
将本地目录与 Azure AD 集成时，请务必了解同步设置对用户身份验证的方式有何影响。 Azure AD 使用 userPrincipalName (UPN) 对用户进行身份验证。 但是，在同步用户时，必须小心选择要用于 userPrincipalName 值的属性。

### <a name="choosing-the-attribute-for-userprincipalname"></a>选择 userPrincipalName 的属性
选择属性以便提供用于 Azure 的 UPN 值时，应确保

* 属性值符合 UPN 语法 (RFC 822)，其格式应为 username@domain
* 这些值的后缀符合 Azure AD 中其中一个已验证的自定义域

在快速设置中，属性的假设选择是 userPrincipalName。 如果 userPrincipalName 属性不包含希望用户用于登录 Azure 的值，则必须选择“自定义安装”。

### <a name="custom-domain-state-and-upn"></a>自定义域状态和 UPN
必须确保 UPN 后缀包含已验证的域。

John 是 contoso.com 中的用户。在将用户同步到 Azure AD 目录 contoso.onmicrosoft.com 之后，希望 John 使用本地 UPN john@contoso.com 登录到 Azure。为此，需要将 contoso.com 添加为 Azure AD 中的自定义域并进行验证，才能开始同步用户。 如果 John 的 UPN 后缀（例如 contoso.com）与 Azure AD 中已验证的域不匹配，Azure AD 会将该 UPN 后缀替换为 contoso.onmicrosoft.com。

### <a name="non-routable-on-premises-domains-and-upn-for-azure-ad"></a>不可路由的本地域与 Azure AD 的 UPN
有些组织使用不可路由的域（例如 contoso.local）或简单的单标签域（例如 contoso）。 在 Azure AD 中，无法验证不可路由的域。 Azure AD Connect 只能同步到 Azure AD 中已验证的域。 创建 Azure AD 目录时，将创建可路由的域，而该域将成为 Azure AD 的默认域，例如 contoso.onmicrosoft.com。因此，如果不想要同步到默认的 onmicrosoft.com 域，则必须在此类方案中验证任何其他可路由的域。

有关添加和验证域的详细信息，请阅读[将自定义域名添加到 Azure Active Directory](../active-directory-add-domain.md)。

Azure AD Connect 将检测你是否在不可路由的域环境中运行，并在适当的情况下警告你不要继续使用快速设置。 如果在不可路由的域中操作，用户的 UPN 可能也包含不可路由的后缀。 例如，如果在 contoso.local 下运行，Azure AD Connect 建议使用自定义设置而不是快速设置。 使用自定义设置，可以在用户同步到 Azure AD 之后，指定要用作 UPN 以供登录 Azure 的属性。

## <a name="next-steps"></a>后续步骤
了解有关 [将本地标识与 Azure Active Directory 集成](active-directory-aadconnect.md)的详细信息。
