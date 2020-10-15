---
title: 自定义 Azure Active Directory Connect 的安装
description: 本文介绍 Azure AD Connect 的自定义安装选项。 使用本文中的说明来通过 Azure AD Connect 安装 Active Directory。
services: active-directory
keywords: 什么是 Azure AD Connect, 安装 Active Directory, Azure AD 所需的组件
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 6d42fb79-d9cf-48da-8445-f482c4c536af
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/10/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3afeadff71bd373354b891bd6690d94d28fc0805
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92096345"
---
# <a name="custom-installation-of-azure-active-directory-connect"></a>Azure Active Directory Connect 的自定义安装
如果需要更多的安装选项，请在 Azure Active Directory (中使用 *自定义设置* Azure AD) "连接"。 例如，如果有多个林，或者要配置可选功能，请使用这些设置。 在 [快速安装](how-to-connect-install-express.md) 不满足部署或拓扑需求的所有情况下，使用自定义设置。

先决条件：
- [下载 Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771)。
- 完成 [Azure AD Connect：硬件和先决条件](how-to-connect-install-prerequisites.md)中的先决条件步骤。 
- 请确保具有 [Azure AD Connect 帐户和权限](reference-connect-accounts-permissions.md)中所述的帐户。

## <a name="custom-installation-settings"></a>自定义安装设置 

若要为 Azure AD Connect 设置自定义安装，请完成以下各节所述的向导页面。

### <a name="express-settings"></a>快速设置
在 " **快速设置** " 页上，选择 " **自定义** " 开始安装自定义设置。  本文的其余部分将指导你完成自定义安装过程。 使用以下链接可快速前往特定页面的信息：

- [必需的组件](#install-required-components)
- [用户登录](#user-sign-in)
- [连接到 Azure AD](#connect-to-azure-ad)
- [同步](#sync-pages)

### <a name="install-required-components"></a>安装所需的组件
当你安装同步服务时，你可以将 "可选配置" 部分保留为未选中状态。 Azure AD Connect 自动设置所有内容。 它设置 SQL Server 2012 Express LocalDB 实例、创建相应的组并分配权限。 如果要更改默认值，请清除相应的框。  下表总结了这些选项，并提供指向其他信息的链接。 

![显示 Azure AD Connect 中所需安装组件的可选选项的屏幕截图。](./media/how-to-connect-install-custom/requiredcomponents2.png)

| 可选配置 | 说明 |
| --- | --- |
|指定自定义安装位置| 允许您更改 Azure AD Connect 的默认安装路径。|
| 使用现有的 SQL Server |允许您指定 SQL Server 名称和实例名称。 如果已有要使用的数据库服务器，请选择此选项。 对于 " **实例名称**"，请输入实例名称、逗号和端口号（如果 SQL Server 实例没有启用浏览）。  然后指定 Azure AD Connect 数据库的名称。  你的 SQL 特权确定是否可以创建新的数据库，或者你的 SQL 管理员是否必须提前创建数据库。  如果你具有 SQL Server 管理员 (SA) 权限，请参阅 [使用现有数据库安装 Azure AD Connect](how-to-connect-install-existing-database.md)。  如果已 (DBO) 委托权限，请参阅 [使用 SQL 委派的管理员权限安装 Azure AD Connect](how-to-connect-install-sql-delegation.md)。 |
| 使用现有的服务帐户 |默认情况下，Azure AD Connect 为同步服务提供虚拟服务帐户。 如果使用 SQL Server 的远程实例或使用要求身份验证的代理，则可以使用域中的 *托管服务帐户* 或受密码保护的服务帐户。 在这些情况下，请输入要使用的帐户。 若要运行安装，你必须是 SQL 中的 SA，以便可以创建服务帐户的登录凭据。 有关详细信息，请参阅 [Azure AD Connect 帐户和权限](reference-connect-accounts-permissions.md#adsync-service-account)。 </br></br>通过使用最新版本，SQL 管理员现在可以对数据库进行带外设置。 然后，Azure AD Connect 管理员可以安装数据库所有者权限。  有关详细信息，请参阅[使用 SQL 委派的管理员权限安装 Azure AD Connect](how-to-connect-install-sql-delegation.md)。|
| 指定自定义同步组 |默认情况下，在安装同步服务时，Azure AD Connect 会创建四个服务器本地的组。 这些组是管理员、操作员、浏览和密码重置。 在此可以指定自己的组。 组必须位于服务器上的本地组中。 它们不能位于域中。 |
| (预览导入同步设置) |允许您从 Azure AD Connect 的其他版本导入设置。  有关详细信息，请参阅 [导入和导出 Azure AD Connect 配置设置](how-to-connect-import-export-config.md)。|

### <a name="user-sign-in"></a>用户登录
安装所需的组件后，选择用户的单一登录方法。 下表简要介绍了可用的选项。 有关登录方法的完整说明，请参阅[用户登录](plan-connect-user-signin.md)。

![显示 "用户登录" 页的屏幕截图。 已选择 "密码哈希同步" 选项。](./media/how-to-connect-install-custom/usersignin4.png)

| 单一登录选项 | 说明 |
| --- | --- |
| 密码哈希同步 |用户可以使用他们在本地网络中使用的相同密码登录到 Microsoft 云服务，例如 Microsoft 365。 用户密码作为密码哈希同步到 Azure AD。 在云中进行身份验证。 有关详细信息，请参阅 [密码哈希同步](how-to-connect-password-hash-synchronization.md)。 |
|直通身份验证|用户可以使用他们在本地网络中使用的相同密码登录到 Microsoft 云服务，例如 Microsoft 365。  通过向本地 Active Directory 域控制器传递用户密码进行验证。
| 使用 AD FS 进行联合身份验证 |用户可以使用他们在本地网络中使用的相同密码登录到 Microsoft 云服务，例如 Microsoft 365。  用户被重定向到其本地 Azure 目录联合身份验证服务 (AD FS) 实例登录。 身份验证在本地进行。 |
| 使用 PingFederate 进行联合身份验证|用户可以使用他们在本地网络中使用的相同密码登录到 Microsoft 云服务，例如 Microsoft 365。  用户被重定向到其本地 PingFederate 实例以登录。 身份验证在本地进行。 |
| 不配置 |未安装或配置用户登录功能。 如果已有第三方联合服务器或其他解决方案，请选择此选项。 |
|启用单一登录|密码哈希同步和传递身份验证均提供此选项。 它为企业网络中的桌面用户提供单一登录体验。 有关详细信息，请参阅 [单一登录](how-to-connect-sso.md)。 </br></br>**注意：** 对于 AD FS 客户，此选项不可用。 AD FS 已提供相同级别的单一登录。</br>

### <a name="connect-to-azure-ad"></a>连接到 Azure AD
在 " **连接到 Azure AD** " 页上，输入全局管理员帐户和密码。 如果在上一页上选择了 " **与 AD FS 联合** "，请不要使用计划启用联合身份验证的域中的帐户登录。 

你可能想要使用 Azure AD 租户随附的默认 *onmicrosoft.com* 域中的帐户。 此帐户仅用于在 Azure AD 中创建服务帐户。 安装完成后，不会使用此方法。
  
![显示 "连接到 Azure AD" 页的屏幕截图。](./media/how-to-connect-install-custom/connectaad.png)

如果全局管理员帐户已启用多重身份验证，则在登录窗口中再次提供密码，并且必须完成多重身份验证质询。 质询可以是验证代码或电话呼叫。  

![显示 "连接到 Azure AD" 页的屏幕截图。 多重身份验证字段提示用户输入代码。](./media/how-to-connect-install-custom/connectaadmfa.png)

全局管理员帐户也可以启用 [特权标识管理](../privileged-identity-management/pim-getting-started.md) 。

如果出现错误或存在连接问题，请参阅 [排查连接问题](tshoot-connect-connectivity.md)。

## <a name="sync-pages"></a>同步页

以下部分介绍 **同步** 部分中的页面。

### <a name="connect-your-directories"></a>连接目录
若要连接到 Active Directory 域服务 (Azure AD DS) ，Azure AD Connect 需要具有足够权限的帐户的林名称和凭据。

![显示 "连接目录" 页的屏幕截图。](./media/how-to-connect-install-custom/connectdir01.png)

输入林名称并选择 "  **添加目录**" 后，将显示一个窗口。 下表对你的选项进行了说明。

| 选项 | 说明 |
| --- | --- |
| 创建新帐户 | 创建 Azure AD Connect 在目录同步期间需要连接到 Active Directory 林的 Azure AD DS 帐户。 选择此选项后，请输入企业管理员帐户的用户名和密码。  Azure AD Connect 使用提供的企业管理员帐户创建所需的 Azure AD DS 帐户。 可以采用 NetBIOS 格式或 FQDN 格式输入域部分。 即，输入 *FABRIKAM\administrator* 或 *com\administrator*。 |
| 使用现有帐户 | 提供 Azure AD Connect 可用于在目录同步期间连接到 Active Directory 林的现有 Azure AD DS 帐户。 可以采用 NetBIOS 格式或 FQDN 格式输入域部分。 即，输入 *FABRIKAM\syncuser* 或 *com\syncuser*。 此帐户可以是普通的用户帐户，因为该帐户只需默认的读取权限。 但根据你的方案，你可能需要更多的权限。 有关详细信息，请参阅 [Azure AD Connect 帐户和权限](reference-connect-accounts-permissions.md#create-the-ad-ds-connector-account)。 |

![屏幕截图：显示 "连接目录" 页和一个 D 林帐户窗口，您可以在其中选择创建新帐户或使用现有帐户。](./media/how-to-connect-install-custom/connectdir02.png)

>[!NOTE]
> 从生成1.4.18.0 中，不能使用企业管理员帐户或域管理员帐户作为 Azure AD DS 连接器帐户。 当你选择 " **使用现有帐户**" 时，如果你尝试输入企业管理员帐户或域管理员帐户，你将看到以下错误： "不允许对 AD 林帐户使用企业或域管理员帐户。 让 Azure AD Connect 创建帐户，或指定具有正确权限的同步帐户。 "
>

### <a name="azure-ad-sign-in-configuration"></a>Azure AD 登录配置
在 " **Azure AD 登录配置** " 页上，查看本地 Azure AD DS 中 (UPN) 域中的用户主体名称。 这些 UPN 域已在 Azure AD 中进行了验证。 在此页上，你将配置用于 userPrincipalName 的属性。

![显示 "Azure A D 登录配置" 页上未验证的域的屏幕截图。](./media/how-to-connect-install-custom/aadsigninconfig2.png)  

查看标记为 " **未添加** " 或 " **未验证**" 的每个域。 请确保已在 Azure AD 中验证你使用的域。 验证域后，选择 "循环刷新" 图标。 有关详细信息，请参阅 [添加和验证域](../fundamentals/add-custom-domain.md)。

用户登录到 Azure AD 和 Microsoft 365 时使用 *userPrincipalName* 属性。 在同步用户之前，Azure AD 应验证域（也称为 UPN 后缀）。 Microsoft 建议保留默认属性 userPrincipalName。 

如果 userPrincipalName 属性是不可且无法验证，则可以选择另一个属性。 例如，你可以选择 "电子邮件" 作为保存登录 ID 的属性。 使用 userPrincipalName 以外的其他属性时，它称为 *替代 ID*。 

备用 ID 属性值必须遵循 RFC 822 标准。 可以将备用 ID 与密码哈希同步、传递身份验证和联合使用。 在 Active Directory 中，属性不能定义为多值，即使它只有单个值也是如此。 有关备用 ID 的详细信息，请参阅 [传递身份验证：常见问题](./how-to-connect-pta-faq.md#does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname)。

>[!NOTE]
> 启用直通身份验证时，必须至少有一个已验证的域，才能继续执行自定义安装过程。

> [!WARNING]
> 备用 Id 与所有 Microsoft 365 工作负荷都不兼容。 有关详细信息，请参阅 [配置备用登录 id](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)。
>

### <a name="domain-and-ou-filtering"></a>域和 OU 筛选
默认情况下，将同步所有域和组织单位 (Ou) 。 如果不想将某些域或 Ou 同步到 Azure AD，则可以清除相应的选项。  

![显示 "域和 O U 筛选" 页的屏幕截图。](./media/how-to-connect-install-custom/domainoufiltering.png)  

此页配置基于域和基于 OU 的筛选。 如果打算进行更改，请参阅 [基于域的筛选](how-to-connect-sync-configure-filtering.md#domain-based-filtering) 和 [基于 OU 的筛选](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering)。 某些 Ou 对功能至关重要，因此应将其保留为选中状态。

如果将基于 OU 的筛选用于版本早于1.1.524.0 的 Azure AD Connect，则默认情况下将同步新 Ou。 如果不想要同步新 Ou，则可以在 [基于 OU 的筛选](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) 步骤之后调整默认行为。 对于 Azure AD Connect 1.1.524.0 或更高版本，可以指示是否要同步新 Ou。

如果打算使用 [基于组的筛选](#sync-filtering-based-on-groups)，请确保包含该组的 OU，且未使用 ou 筛选对其进行筛选。 在计算基于组的筛选之前计算 OU 筛选。

由于防火墙限制，可能无法访问某些域。 默认情况下未选择这些域，它们会显示警告。  

![显示无法访问的域的屏幕截图。](./media/how-to-connect-install-custom/unreachable.png)  

如果看到此警告，请确保这些域确实无法访问，并且应出现警告。

### <a name="uniquely-identifying-your-users"></a>唯一标识用户

在 " **标识用户** " 页上，选择如何在本地目录中标识用户，以及如何使用 sourceAnchor 属性来识别它们。

#### <a name="select-how-users-should-be-identified-in-your-on-premises-directories"></a>选择应如何在本地目录中标识用户
通过使用 " *跨林匹配* " 功能，可以定义 Azure AD DS 林中的用户如何以 Azure AD 表示。 用户只能在所有林上表示一次，或者可能包含已启用和已禁用帐户的组合。 在某些林中，用户还可以被呈现为联系人。

![显示可用于唯一标识用户的页面的屏幕截图。](./media/how-to-connect-install-custom/unique2.png)

| 设置 | 说明 |
| --- | --- |
| [用户仅在所有林中表示一次](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |将所有用户在 Azure AD 中创建为单独的对象。 不会在元节中联接对象。 |
| [邮件属性](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |如果邮件属性在不同的林中具有相同的值，此选项将联接用户和联系人。 当使用 GALSync 创建联系人时，请使用此选项。 如果选择此选项，则其 mail 属性为未填充的用户对象不会同步到 Azure AD。 |
| [ObjectSID 和 msExchangeMasterAccountSID/Msrtcsip-originatorsid-OriginatorSID 特性](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |此选项将帐户林中的已启用用户与资源林中的已禁用用户进行联接。 在 Exchange 中，此配置称为链接邮箱。 如果只使用 Lync 并且资源林中没有 Exchange，则可以使用此选项。 |
| SAMAccountName 和 MailNickName 属性 |此选项在需要找到用户登录 ID 的属性上进行联接。 |
| 选择特定属性 |此选项允许选择自己的属性。 如果选择此选项， (选择) 属性的用户对象将不会同步到 Azure AD。 **限制：** 只有元节中已经存在的属性可用于此选项。 |

#### <a name="select-how-users-should-be-identified-by-using-a-source-anchor"></a>选择如何使用源锚点标识用户
在用户对象的生存期内， *sourceAnchor* 属性是不可变的。 这是将本地用户与 Azure AD 中的用户进行链接的主密钥。

| 设置 | 说明 |
| --- | --- |
| 让 Azure 管理源锚点 | 如果希望 Azure AD 为你选取属性，请选择此选项。 如果选择此选项，Azure AD Connect 会应用 [msds-consistencyguid As sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor)中所述的 sourceAnchor 属性选择逻辑。 自定义安装完成后，你将看到选取哪个属性作为 sourceAnchor 属性。 |
| 选择特定属性 | 如果要将现有的 AD 属性指定为 sourceAnchor 属性，请选择此选项。 |

由于 sourceAnchor 属性无法更改，因此必须选择相应的属性。 objectGUID 就是不错的候选项。 除非在林或域之间移动用户帐户，否则此属性不会更改。 不要选择结婚时会或更改分配时可以更改的属性。 

不能使用包含 at 符号 ( @ ) 的特性，因此不能使用电子邮件和 userPrincipalName。 属性也区分大小写，因此在林间移动对象时，请确保保留大小写。 二进制属性采用 Base64 编码，但其他属性类型仍处于未编码状态。 

在联合方案和某些 Azure AD 接口中，sourceAnchor 属性也称为 *immutableID*。 

有关源定位点的详细信息，请参阅 [设计概念](plan-connect-design-concepts.md#sourceanchor)。

### <a name="sync-filtering-based-on-groups"></a>根据组同步筛选
使用 "筛选组" 功能，只允许同步一小部分的对象来进行试验。 若要使用此功能，请在 Active Directory 的本地实例中为此目的创建一个组。 然后添加应该以直属成员身份与 Azure AD 同步的用户和组。 稍后可以添加用户或从此组中删除用户，以维护应该出现在 Azure AD 中的对象的列表。 

要同步的所有对象必须是组的直接成员。 用户、组、联系人和计算机或设备都必须是直接成员。 嵌套的组成员身份未解析。 添加组作为成员时，只会添加该组本身。 不添加其成员。

![显示页面的屏幕截图，你可以在其中选择筛选用户和设备的方式。](./media/how-to-connect-install-custom/filter2.png)

> [!WARNING]
> 此功能仅用于支持试验部署。 不要将其用于完整的生产部署。
>

在完整的生产部署中，难于维护单个组及其所有对象以进行同步。 请使用 [配置筛选](how-to-connect-sync-configure-filtering.md)中所述的方法之一，而不是使用筛选组功能。

### <a name="optional-features"></a>可选功能
在下一页上，你可以为方案选择可选功能。

>[!WARNING]
>Azure AD Connect 版本1.0.8641.0 及更早版本依赖于 Azure 访问控制服务进行密码写回。  此服务已于2018年11月7日停用。  如果你使用 Azure AD Connect 的任何版本并启用了密码写回，则当服务停用时，用户可能无法更改或重置其密码。 这些版本的 Azure AD Connect 不支持密码写回。
>
>有关详细信息，请参阅 [从 Azure 访问控制服务迁移](../azuread-dev/active-directory-acs-migration.md)。
>
>如果要使用密码写回，请下载 [最新版本的 Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)。

![显示 "可选功能" 页的屏幕截图。](./media/how-to-connect-install-custom/optional2a.png)

> [!WARNING]
> 如果 DirSync)  (的 Azure AD Sync 或直接同步处于活动状态，则不激活 Azure AD Connect 中的任何写回功能。



| 可选功能 | 说明 |
| --- | --- |
| Exchange 混合部署 |Exchange 混合部署功能允许在本地和 Microsoft 365 中共存 Exchange 邮箱。 Azure AD Connect 将一组特定 [属性](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) 从 Azure AD 同步到本地目录中。 |
| Exchange 邮件公用文件夹 | "Exchange 邮件公用文件夹" 功能允许你将已启用邮件的公用文件夹对象从 Active Directory 的本地实例同步到 Azure AD。 |
| Azure AD 应用程序和属性筛选 |通过启用 Azure AD 应用和属性筛选，可以定制同步的属性集。 此选项会在向导中额外添加两个配置页。 有关详细信息，请参阅 [Azure AD 应用程序和属性筛选](#azure-ad-app-and-attribute-filtering)。 |
| 密码哈希同步 |如果选择了 "联合" 作为登录解决方案，则可以启用 "密码哈希同步"。 然后，你可以将它用作备份选项。  </br></br>如果选择了 "传递身份验证"，则可以启用此选项以确保支持旧客户端并提供备份。</br></br> 有关详细信息，请参阅 [密码哈希同步](how-to-connect-password-hash-synchronization.md)。|
| 密码写回 |使用此选项可确保将 Azure AD 中产生的密码更改写回到本地目录。 有关详细信息，请参阅[密码管理入门](../authentication/tutorial-enable-sspr.md)。 |
| 组写回 |如果使用 Microsoft 365 组，则可以表示 Active Directory 的本地实例中的组。 仅当在 Active Directory 的本地实例中有 Exchange 时，此选项才可用。 有关详细信息，请参阅 [Azure AD Connect 组写回](how-to-connect-group-writeback.md)。|
| 设备写回 |对于条件访问方案，使用此选项可以将 Azure AD 中的设备对象写回 Active Directory 的本地实例中。 有关详细信息，请参阅[在 Azure AD Connect 中启用设备写回](how-to-connect-device-writeback.md)。 |
| 目录扩展属性同步 |选择此选项可将指定的属性同步到 Azure AD。 有关详细信息，请参阅[目录扩展](how-to-connect-sync-feature-directory-extensions.md)。 |

### <a name="azure-ad-app-and-attribute-filtering"></a>Azure AD 应用程序和属性筛选
如果要限制同步到 Azure AD 的属性，请通过选择所使用的服务开始。 如果更改此页上的选项，则必须通过重新运行安装向导来明确选择新的服务。

![显示可选 Azure A D apps 功能的屏幕截图。](./media/how-to-connect-install-custom/azureadapps2.png)

此页基于你在上一步中选择的服务，显示已同步的所有属性。 此列表是要同步的所有对象类型的组合。 如果需要某些属性保持同步，可以从这些属性中清除所选内容。

![显示可选 Azure D 特性功能的屏幕截图。](./media/how-to-connect-install-custom/azureadattributes2.png)

> [!WARNING]
> 删除属性可能会影响功能。 有关最佳实践和建议，请参阅 [要同步的属性](reference-connect-sync-attributes-synchronized.md#attributes-to-synchronize)。
>

### <a name="directory-extension-attribute-sync"></a>目录扩展属性同步
可以使用组织添加的自定义属性或 Active Directory 中的其他属性，在 Azure AD 中扩展架构。 若要使用此功能，请在 " **可选功能** " 页上，选择 " **目录扩展属性同步**"。在 " **目录扩展** " 页上，可以选择要同步的更多属性。

>[!NOTE]
>" **可用特性** " 字段区分大小写。

![显示 "目录扩展" 页的屏幕截图。](./media/how-to-connect-install-custom/extension2.png)

有关详细信息，请参阅[目录扩展](how-to-connect-sync-feature-directory-extensions.md)。

### <a name="enabling-single-sign-on"></a>启用单一登录
在 " **单一登录** " 页上，可以配置用于密码同步或传递身份验证的单一登录。 为每个要同步到 Azure AD 的林执行一次此步骤。 配置涉及两个步骤：

1.  在 Active Directory 的本地实例中创建所需的计算机帐户。
2.  配置客户端计算机的 intranet 区域，以支持单一登录。

#### <a name="create-the-computer-account-in-active-directory"></a>在 Active Directory 中创建计算机帐户
对于已添加到 Azure AD Connect 中的每个林，需要提供域管理员凭据，以便可以在每个林中创建计算机帐户。 凭据仅用于创建帐户。 它们不存储或用于任何其他操作。 在 " **启用单一登录** " 页上添加凭据，如下图所示。

![显示 "启用单一登录" 页的屏幕截图。 添加林凭据。](./media/how-to-connect-install-custom/enablesso.png)

>[!NOTE]
>可以跳过不想使用单一登录的林。

#### <a name="configure-the-intranet-zone-for-client-machines"></a>为客户端计算机配置 intranet 区域
若要确保客户端在 intranet 区域中自动登录，请确保 URL 是 intranet 区域的一部分。 此步骤可确保加入域的计算机在连接到公司网络时，自动将 Kerberos 票证发送到 Azure AD。

在具有组策略管理工具的计算机上：

1.  打开组策略管理工具。
2.  编辑将应用于所有用户的组策略。 例如，默认域策略。
3.  请访问 "**用户配置**"  >  **管理模板**  >  **Windows 组件**"  >  **internet Explorer**  >  **internet 控制面板**  >  **安全性页**。 然后选择“站点到区域分配列表”****。
4.  启用策略。 然后，在 "" 对话框中，输入的值名称 `https://autologon.microsoftazuread-sso.com` 和值 `1` 。 你的设置应如下图所示。
  
    ![显示 intranet 区域的屏幕截图。](./media/how-to-connect-install-custom/sitezone.png)

6.  选择 **"确定"** 两次。

## <a name="configuring-federation-with-ad-fs"></a>配置与 AD FS 的联合
只需单击几下鼠标，就能使用 Azure AD Connect 配置 AD FS。 在开始之前，需要：

* 用于联合服务器的 Windows Server 2012 R2 或更高版本。 应启用远程管理。
* 用于 Web 应用程序代理服务器的 Windows Server 2012 R2 或更高版本。 应启用远程管理。
* 要使用 (例如，sts.contoso.com) 的联合身份验证服务名称的 TLS/SSL 证书。

>[!NOTE]
>即使不使用 Azure AD Connect 来管理联合身份验证信任，你也可以通过使用更新 AD FS 场的 TLS/SSL 证书。

### <a name="ad-fs-configuration-prerequisites"></a>AD FS 配置先决条件
若要使用 Azure AD Connect 配置 AD FS 场，请确保已在远程服务器上启用 WinRM。 请确保已完成 " [联合先决条件](how-to-connect-install-prerequisites.md#prerequisites-for-federation-installation-and-configuration)" 中的其他任务。 另外，请确保遵循 [Azure AD Connect 和联合/WAP 服务器](reference-connect-ports.md#table-3---azure-ad-connect-and-ad-fs-federation-serverswap) 表中列出的端口要求。

### <a name="create-a-new-ad-fs-farm-or-use-an-existing-ad-fs-farm"></a>创建新的 AD FS 场或使用现有的 AD FS 场
您可以使用现有的 AD FS 场或创建一个新的场。 如果选择创建新的证书，则必须提供 TLS/SSL 证书。 如果 TLS/SSL 证书受密码保护，系统会提示提供密码。

![显示 "A D F S 场" 页的屏幕截图](./media/how-to-connect-install-custom/adfs1.png)

如果选择使用现有 AD FS 场，你会看到一个页面，可在其中配置 AD FS 和 Azure AD 之间的信任关系。

>[!NOTE]
>您可以使用 Azure AD Connect 来仅管理一个 AD FS 场。 如果你具有在所选 AD FS 场中配置 Azure AD 的现有联合信任，Azure AD Connect 从头开始重新创建信任。

### <a name="specify-the-ad-fs-servers"></a>指定 AD FS 服务器
指定要在其中安装 AD FS 的服务器。 你可以根据容量需求添加一个或多个服务器。 设置此配置之前，请将所有 AD FS 服务器联接到 Active Directory。 Web 应用程序代理服务器不需要执行此步骤。 

Microsoft 建议安装一台 AD FS 服务器用于测试和试验部署。 在初始配置后，可以通过再次运行 Azure AD Connect 来添加和部署更多服务器以满足缩放需求。

> [!NOTE]
> 设置此配置之前，请确保所有服务器已加入 Azure AD 域。
>


![显示 "联合服务器" 页的屏幕截图。](./media/how-to-connect-install-custom/adfs2.png)

### <a name="specify-the-web-application-proxy-servers"></a>指定 Web 应用程序代理服务器
指定 Web 应用程序代理服务器。 Web 应用程序代理服务器部署在外围网络中，面向 extranet。 它支持来自 extranet 的身份验证请求。 你可以根据容量需求添加一个或多个服务器。 

Microsoft 建议安装一台用于测试和试验部署的 Web 应用程序代理服务器。 在初始配置后，可以通过再次运行 Azure AD Connect 来添加和部署更多服务器以满足缩放需求。 建议你拥有相当多的代理服务器，以满足来自 intranet 的身份验证。

> [!NOTE]
> - 如果你使用的帐户不是 Web 应用程序代理服务器上的本地管理员，则系统会提示你提供管理员凭据。
> - 指定 Web 应用程序代理服务器之前，请确保 Azure AD Connect 服务器和 Web 应用程序代理服务器之间存在 HTTP/HTTPS 连接。
> - 确保 Web 应用程序服务器与 AD FS 服务器之间的 HTTP/HTTPS 连接允许通过身份验证请求。
>


![显示 "Web 应用程序代理服务器" 页的屏幕截图。](./media/how-to-connect-install-custom/adfs3.png)

系统会提示输入凭据，以便 web 应用程序服务器可以建立到 AD FS 服务器的安全连接。 这些凭据必须为 AD FS 服务器上的本地管理员帐户。

![显示 "凭据" 页的屏幕截图。 管理员凭据在 "用户名" 字段和 "密码" 字段中输入。](./media/how-to-connect-install-custom/adfs4.png)

### <a name="specify-the-service-account-for-the-ad-fs-service"></a>指定 AD FS 服务的服务帐户
AD FS 服务需要域服务帐户对用户进行身份验证，并在 Active Directory 中查找用户信息。 它可以支持两种类型的服务帐户：

* **组托管服务帐户**：此帐户类型已引入到 Windows Server 2012 AD DS。 这种类型的帐户提供 AD FS 等服务。 这是一个不需要定期更新密码的帐户。 如果 AD FS 服务器所属的域中已有 Windows Server 2012 域控制器，请使用此选项。
* **域用户帐户**：这种类型的帐户要求你提供密码，并在过期后定期对其进行更新。 仅当你的 AD FS 服务器所属的域中没有 Windows Server 2012 域控制器时，才使用此选项。

如果选择了 " **创建组托管服务帐户** "，但从未在 Active Directory 中使用过此功能，请输入企业管理员凭据。 这些凭据用于启动密钥存储，以及在 Active Directory 中启用该功能。

> [!NOTE]
> Azure AD Connect 检查 AD FS 服务是否已注册为域中 (SPN) 的服务主体名称。  Azure AD DS 不允许同时注册重复的 Spn。  如果发现重复的 SPN，则在删除 SPN 之前，你无法继续操作。

![显示 "A D F S 服务帐户" 页的屏幕截图。](./media/how-to-connect-install-custom/adfs5.png)

### <a name="select-the-azure-ad-domain-that-you-want-to-federate"></a>选择要联合的 Azure AD 域
使用 " **Azure AD 域** " 页可以设置 AD FS 和 Azure AD 之间的联合关系。 此处，将 AD FS 配置为提供要 Azure AD 的安全令牌。 你还可以将 Azure AD 配置为信任此 AD FS 实例中的令牌。 

在此页上，只能在初始安装中配置单个域。 以后可以通过再次运行 Azure AD Connect 来配置其他域。

![显示 "Azure A D 域" 页的屏幕截图。](./media/how-to-connect-install-custom/adfs6.png)

### <a name="verify-the-azure-ad-domain-selected-for-federation"></a>验证选择用于联合的 Azure AD 域
选择要联合的域时，Azure AD Connect 提供可用于验证未验证域的信息。 有关详细信息，请参阅 [添加和验证域](../fundamentals/add-custom-domain.md)。

![显示 "Azure A D 域" 页面的屏幕截图，其中包括可用于验证域的信息。](./media/how-to-connect-install-custom/verifyfeddomain.png)

> [!NOTE]
> Azure AD Connect 尝试在配置阶段验证域。 如果未将所需的域名系统添加 (DNS) 记录，则无法完成配置。
>


## <a name="configuring-federation-with-pingfederate"></a>配置使用 PingFederate 的联合身份验证
只需单击几下鼠标，即可在 Azure AD Connect 中配置 PingFederate。 需要以下先决条件：
- PingFederate 8.4 或更高版本。  有关详细信息，请参阅 [PingFederate integration with Azure Active Directory and Microsoft 365](https://docs.pingidentity.com/bundle/O365IG20_sm_integrationGuide/page/O365IG_c_integrationGuide.html)。
- 要使用 (例如，sts.contoso.com) 的联合身份验证服务名称的 TLS/SSL 证书。

### <a name="verify-the-domain"></a>验证域
选择使用 PingFederate 设置联合后，将要求验证要联合的域。  从下拉菜单中选择该域。

![显示 "Azure A D 域" 页的屏幕截图。 示例域 "contoso.com" 已选中。](./media/how-to-connect-install-custom/ping1.png)

### <a name="export-the-pingfederate-settings"></a>导出 PingFederate 设置


将 PingFederate 配置为每个联合 Azure 域的联合服务器。  选择 " **导出设置** " 以与 PingFederate 管理员共享此信息。  联合服务器管理员更新配置，然后提供 PingFederate 服务器 URL 和端口号，以便 Azure AD Connect 可以验证元数据设置。  

![显示 "PingFederate 设置" 页的屏幕截图。 页面顶部附近会出现 "导出设置" 按钮。](./media/how-to-connect-install-custom/ping2.png)

与 PingFederate 管理员联系以解决任何验证问题。  下图显示了与 Azure 没有有效信任关系的 PingFederate 服务器的相关信息。

![显示服务器信息的屏幕截图：找到了 PingFederate 服务器，但缺少或禁用了 Azure 的服务提供程序连接。](./media/how-to-connect-install-custom/ping5.png)




### <a name="verify-federation-connectivity"></a>验证联合身份验证连接性
Azure AD Connect 尝试验证它从上一步中的 PingFederate 元数据中检索到的身份验证终结点。  Azure AD Connect 首先尝试使用本地 DNS 服务器解析终结点。  接下来，它将尝试使用外部 DNS 提供程序解析终结点。  与 PingFederate 管理员联系以解决任何验证问题。  

![显示 "验证连接性" 页的屏幕截图。](./media/how-to-connect-install-custom/ping3.png)

### <a name="verify-federation-sign-in"></a>验证联合登录
最后，可以通过登录到联合域来验证新配置的联合登录流。 如果登录成功，则已成功配置 PingFederate 的联合身份验证。

![显示 "验证联合登录名" 页的屏幕截图。 底部的消息指示登录成功。](./media/how-to-connect-install-custom/ping4.png)

## <a name="configure-and-verify-pages"></a>配置和验证页面
配置将在 " **配置** " 页上发生。

> [!NOTE]
> 如果配置了联合，请确保在继续安装之前已 [为联合服务器配置了名称解析](how-to-connect-install-prerequisites.md#name-resolution-for-federation-servers) 。
>



![显示 "已准备好配置" 页的屏幕截图。](./media/how-to-connect-install-custom/readytoconfigure2.png)

### <a name="use-staging-mode"></a>使用暂存模式
可以使用过渡模式并行设置新的同步服务器。 如果要使用此设置，则只能将一个同步服务器导出到云中的一个目录。 但如果想要从另一台服务器（例如运行 DirSync 的服务器）移动，则可以在暂存模式下启用 Azure AD Connect。 

启用暂存设置后，同步引擎将导入并同步数据。 但它不会将任何数据导出到 Azure AD 或 Active Directory。 在暂存模式下，将禁用密码同步功能和密码写回功能。

![显示 "启用过渡模式" 选项的屏幕截图。](./media/how-to-connect-install-custom/stagingmode.png)

在暂存模式下，你可以对同步引擎进行所需的更改，并查看要导出的内容。 如果配置看起来正常，请再次运行安装向导，并禁用过渡模式。 

现在，数据将从服务器导出到 Azure AD。 确保同时禁用其他服务器，以便只有一台服务器在主动导出。

有关详细信息，请参阅[过渡模式](how-to-connect-sync-staging-server.md)。

### <a name="verify-your-federation-configuration"></a>验证联合配置
当你选择 " **验证** " 按钮时，AZURE AD CONNECT 验证 DNS 设置。 它将检查以下设置：

* **Intranet 连接**
    * 解析联合 FQDN： Azure AD Connect 检查 DNS 是否可以解析联合 FQDN，以确保连接性。 如果 Azure AD Connect 无法解析 FQDN，则验证将失败。 若要完成验证，请确保存在用于联合身份验证服务 FQDN 的 DNS 记录。
    * DNS A 记录： Azure AD Connect 检查联合身份验证服务是否有 A 记录。 如果没有 A 记录，验证将失败。 若要完成验证，请为联合 FQDN) 创建 A 记录 (而不是 CNAME 记录。
* **Extranet 连接**
    * 解析联合 FQDN： Azure AD Connect 检查 DNS 是否可以解析联合 FQDN，以确保连接性。

      ![显示 "安装完成" 页的屏幕截图。](./media/how-to-connect-install-custom/completed.png)

      ![显示 "安装完成" 页的屏幕截图。 消息指示 intranet 配置已验证。](./media/how-to-connect-install-custom/adfs7.png)

若要验证端到端身份验证，请手动执行以下一项或多项测试：

* 同步完成后，请在 Azure AD Connect 中使用 " **验证联合登录** 附加任务" 来验证你选择的本地用户帐户的身份验证。
* 在 intranet 上已加入域的计算机上，确保可以从浏览器登录。 连接到 https://myapps.microsoft.com。 然后使用登录帐户验证登录。 内置 Azure AD DS 管理员帐户未同步，无法将其用于验证。
* 确保你可以从 extranet 上的设备登录。 在家庭计算机或移动设备上，连接到 https://myapps.microsoft.com 。 然后提供凭据。
* 验证富客户端登录。 连接到 https://testconnectivity.microsoft.com。 然后选择 " **office 365**  >  **office 365 Single Sign-On Test**"。

## <a name="troubleshoot"></a>疑难解答
本部分包含有关在安装 Azure AD Connect 时遇到问题时可以使用的疑难解答信息。

自定义 Azure AD Connect 安装时，在 " **安装所需的组件** " 页上，可以选择 " **使用现有 SQL Server**"。 你可能会看到以下错误： "ADSync 数据库已包含数据并且无法覆盖。 请删除现有数据库，然后重试。 "

![显示 "安装所需组件" 页的屏幕截图。 页面底部出现错误。](./media/how-to-connect-install-custom/error1.png)

你会看到此错误，因为指定的 SQL Server 的 SQL 实例上已存在名为 *ADSync* 的数据库。

卸载 Azure AD Connect 后，通常会看到此错误。  卸载 Azure AD Connect 时，不会从运行 SQL Server 的计算机中删除该数据库。

若要修复此问题：

1. 检查 Azure AD Connect 在卸载之前使用的 ADSync 数据库。 请确保不再使用该数据库。

2. 备份数据库。

3. 删除数据库：
    1. 使用 **Microsoft SQL Server Management Studio** 连接到 SQL 实例。 
    1. 找到 **ADSync** 数据库并右键单击它。
    1. 在上下文菜单上，选择 " **删除**"。
    1. 选择 **"确定"** 以删除数据库。

![显示 Microsoft SQL Server Management Studio 的屏幕截图。 选择了 "D 同步"。](./media/how-to-connect-install-custom/error2.png)

删除 ADSync 数据库后，请选择 " **安装** " 以重试安装。

## <a name="next-steps"></a>后续步骤
安装完成后，注销 Windows。 然后再次登录，使用 Synchronization Service Manager 或同步规则编辑器。

安装 Azure AD Connect 后，可以 [验证安装并分配许可证](how-to-connect-post-installation.md)。

有关在安装过程中启用的功能的详细信息，请参阅 [防止意外删除](how-to-connect-sync-feature-prevent-accidental-deletes.md) 和 [Azure AD Connect Health](how-to-connect-health-sync.md)。

有关其他常见主题的详细信息，请参阅 [Azure AD Connect 同步：计划程序](how-to-connect-sync-feature-scheduler.md) 和 [将本地标识与 Azure AD 集成](whatis-hybrid-identity.md)。
