---
title: Azure AD SSPR 与密码写回 | Microsoft Docs
description: 使用 Azure AD 和 Azure AD Connect 将密码写回本地目录
services: active-directory
keywords: Active Directory 密码管理, 密码管理, Azure AD 自助密码重置
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: a7a81f51e0873da38f25deb687e4d03ccf67e11a
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2018
---
# <a name="password-writeback-overview"></a>密码写回概述

使用密码写回服务，可以将 Azure Active Directory (Azure AD) 配置为，将密码写回本地 Active Directory。 借助密码写回服务，无需再设置和管理复杂的本地自助密码重置 (SSPR) 解决方案，而是使用基于云的便捷方法，随时随地重置本地密码。 密码写回服务是 [Azure Active Directory Connect](./connect/active-directory-aadconnect.md) 组成部分，可供 [Azure Active Directory Premium](active-directory-whatis.md) 版本的当前订阅者启用和使用。

密码写回提供以下功能：

* **提供零延迟反馈**：密码写回是一项同步操作。 如果用户的密码不符合策略或因任何原因而无法重置或更改，用户会立即收到通知。
* **支持使用 Active Directory 联合身份验证服务 (AD FS) 或其他联合技术的用户重置密码**：使用密码写回服务，只要联合用户帐户同步到 Azure AD 租户，用户就可以在云中管理本地 Active Directory 密码。
* **支持使用[密码哈希同步](./connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md)的用户重置密码**：如果密码重置服务检测到同步用户帐户已启用密码哈希同步，我们会同时重置此帐户的本地密码和云密码。
* 支持使用直通身份验证的用户重置密码：使用密码写回服务，只要直通身份验证帐户同步到 Azure AD 租户，用户就可以在云中管理本地 Active Directory 密码。
* **支持在访问面板和 Office 365 中更改密码**：如果联合用户或密码同步用户更改已到期或未到期的密码，我们会将这些密码写回本地 Active Directory 环境。
* **支持管理员在 Azure 门户中重置密码时写回密码**：无论何时管理员在 [Azure 门户](https://portal.azure.com)中重置用户密码，只要是联合用户或密码同步用户，我们就还会在本地 Active Directory 中设置管理员选择的密码。 Office 管理门户暂不支持此功能。
* **强制实施本地 Active Directory 密码策略**：如果用户重置密码，我们会先确保它符合本地 Active Directory 策略要求，然后再将重置密码提交到相应目录。 此评审包括检查历史记录、复杂性、期限、密码筛选器，以及已在本地 Active Directory 中定义的其他任何密码限制。
* **不需要任何入站防火墙规则**：密码写回服务使用 Azure 服务总线中继作为基础信道。 无需在防火墙上打开任何入站端口，即可使用此功能。
* **不支持本地 Active Directory 中受保护的组内的用户帐户**：若要详细了解受保护的组，请参阅 [Active Directory 中受保护的帐户和组](https://technet.microsoft.com/library/dn535499.aspx)。

## <a name="how-password-writeback-works"></a>密码写回的工作原理

当联合或密码哈希同步用户在云中重置或更改其密码时，将发生以下情况：

1. 我们检查用户具有何种类型的密码。 如果发现密码是在本地托管：
   * 我们会检查密码写回服务是否在正常运行。 如果是，将让用户继续操作。
   * 如果密码写回服务未在运行，我们会告知用户暂不能重置密码。
2. 接下来，用户通过相应的身份验证入口，到达“重置密码”页。
3. 用户选择一个新密码并进行确认。
4. 如果用户选择“提交”，我们使用在密码写回服务设置过程中创建的对称密钥来加密纯文本密码。
5. 加密密码后，我们将其包括在一个有效负载中，该负载通过 HTTPS 通道发送到租户特定的服务总线中继（我们在写回设置过程中也设置了此项）。 此中继受随机生成的密码保护，只有本地安装知道该密码。
6. 在消息到达服务总线后，密码重置终结点便自动唤醒，并看到有待处理的重置请求。
7. 然后，服务使用云定位点属性查找用户。 要使查找成功：

    * Active Directory 连接器空间中必须有用户对象。
    * 用户对象必须链接到相应的 metaverse (MV) 对象。
    * 用户对象必须链接到相应的 Azure Active Directory 连接器对象。
    * Active Directory 连接器对象与 MV 的链接必须设有同步规则 `Microsoft.InfromADUserAccountEnabled.xxx`。 <br> <br>
    当云中有调用发出时，同步引擎使用 cloudAnchor 属性，查找 Azure Active Directory 连接器空间对象。 然后，它依次链接回 MV 对象和 Active Directory 对象。 由于同一用户可能有多个 Active Directory 对象（多林），因此同步引擎依赖 `Microsoft.InfromADUserAccountEnabled.xxx` 链接选取正确的对象。

    > [!Note]
    > 鉴于有此逻辑，Azure AD Connect 必须能够与主域控制器 (PDC) 仿真器进行通信，这样密码写回服务才能正常运行。 如果需要手动启用此通信，可以将 Azure AD Connect 连接到 PDC 仿真器。 右键单击 Active Directory 同步连接器的“属性”，再选择“配置目录分区”。 随后，查找“域控制器连接设置”部分，并选中“仅使用首选域控制器”框。 即使首选域控制器不是 PDC 仿真器，Azure AD Connect 也会尝试连接到 PDC 来执行密码写回。

8. 找到用户帐户后，我们便会尝试直接在相应的 Active Directory 林中重置密码。
9. 如果密码设置操作成功，我们将告诉用户其密码已被更改。
    > [!NOTE]
    > 如果用户密码是使用密码同步功能同步到 Azure AD，本地密码策略可能会弱于云密码策略。 在这种情况下，我们仍强制实施本地策略，并改用密码哈希同步功能来同步相应密码的哈希。 此策略可确保在云中强制实施本地策略，无论使用密码同步还是联合身份验证来提供单一登录，都不例外。

10. 如果密码设置操作失败，我们会将错误返回给用户，让他们再试一次。 操作失败的可能原因如下：
    * 服务已关闭。
    * 用户选择的密码不符合组织策略。
    * 在本地 Active Directory 中找不到用户。

    我们对于这些情况大多有特定的消息，告知用户可以执行哪些操作来解决问题。

## <a name="configure-password-writeback"></a>配置密码写回服务

如果要使用密码写回，我们始终建议使用 [Azure AD Connect](./connect/active-directory-aadconnect-get-started-express.md) 的自动更新功能。

不再支持使用 DirSync 和 Azure AD Sync 启用密码写回服务。 若要详细了解如何有助于实现转换，请参阅[从 DirSync 和 Azure AD Sync 升级](connect/active-directory-aadconnect-dirsync-deprecated.md)。

若要执行以下步骤，需要已在环境中使用[快速](./connect/active-directory-aadconnect-get-started-express.md)或[自定义](./connect/active-directory-aadconnect-get-started-custom.md)设置配置了 Azure AD Connect。

1. 若要配置和启用密码写回服务，请登录 Azure AD Connect 服务器，并启动“Azure AD Connect”配置向导。
2. 在“欢迎”页上，选择“配置”。
3. 在“其他任务”页上，依次选择“自定义同步选项”和“下一步”。
4. 在“连接到 Azure AD”页上，输入全局管理员凭据，再选择“下一步”。
5. 在“连接目录”和“域/OU 筛选”页上，选择“下一步”。
6. 在“可选功能”页上，选中“密码写回”旁边的框，并选择“下一步”。
   ![在 Azure AD Connect 中启用密码写回][Writeback]
7. 在“已准备好进行配置”页上，选择“配置”，并等待进程完成。
8. 在配置完成后，选择“退出”。

有关与密码写回服务相关的疑难解答任务，请参阅疑难解答文章中的[排查密码写回问题](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback)部分。

## <a name="active-directory-permissions"></a>Active Directory 权限

若要纳入 SSPR 范围，在 Azure AD Connect 实用工具中指定的帐户必须设置以下各项：

* **重置密码** 
* **更改密码** 
* 对 `lockoutTime` 的写入权限  
* 对 `pwdLastSet` 的写入权限
* 对以下任一项的扩展权限：
   * 相应林中各个域的根对象
   * 要纳入 SSPR 范围的用户组织单位 (OU)

如果不确定上述帐户指的是哪个帐户，请打开 Azure Active Directory Connect 配置 UI，并选择“查看当前配置”选项。 需要向其授予权限的帐户列在“已同步的目录”下。

设置这些权限后，每个林的 MA 服务帐户都可以代表相应林中的用户帐户管理密码。 

> [!IMPORTANT]
> 如果疏忽了分配这些权限，即使写回服务似乎已正确配置，但用户仍会在尝试在云中管理本地密码时看到错误。
>

> [!NOTE]
> 将这些权限复制到目录中的所有对象可能需要一小时或更长时间才能完成。
>

若要设置密码写回服务正常运行所需的相应权限，请完成以下步骤：

1. 使用具有适当域管理权限的帐户，打开“Active Directory 用户和计算机”。
2. 在“视图”菜单中，确保“高级功能”处于启用状态。
3. 在左侧面板中，右键单击表示域根的对象，并依次选择“属性” > “安全性” > “高级”。
4. 在“权限”选项卡中，选择“添加”。
5. 选取要将权限应用到的帐户（通过 Azure AD Connect 安装程序）。
6. 在“应用到”下拉列表中，选择“子用户”对象。
7. 在“权限”下，选中下面各项的框：
    * **重置密码**
    * **更改密码**
    * **写入 lockoutTime**
    * **写入 pwdLastSet**
8. 选择“应用”/“确定”以应用更改，并退出所有打开的对话框。

## <a name="licensing-requirements-for-password-writeback"></a>密码写回的许可要求

若要了解许可，请参阅[密码写回所需的许可证](active-directory-passwords-licensing.md#licenses-required-for-password-writeback)或以下网站：

* [Azure Active Directory 定价网站](https://azure.microsoft.com/pricing/details/active-directory/)
* [企业移动性 + 安全性](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 企业版](https://www.microsoft.com/secure-productive-enterprise/default.aspx)

### <a name="on-premises-authentication-modes-that-are-supported-for-password-writeback"></a>密码写回服务支持的本地身份验证模式

密码写回服务支持以下用户密码类型：

* **仅限云用户**：密码写回服务不适用于这种情况，因为没有本地密码。
* **密码同步用户**：受密码写回服务支持。
* **联合用户**：受密码写回服务支持。
* **直通身份验证用户**：受密码写回服务支持。

### <a name="user-and-admin-operations-that-are-supported-for-password-writeback"></a>密码写回服务支持的用户和管理员操作

在以下所有情况下，都会写回密码：

* **支持的最终用户操作**
  * 最终用户以自助形式执行的任何自愿更改密码操作
  * 任何最终用户自助强制更改密码操作（例如，密码到期）
  * 源自[密码重置门户](https://passwordreset.microsoftonline.com)的任何最终用户自助密码重置操作
* **支持的管理员操作**
  * 管理员以自助形式执行的任何自愿更改密码操作
  * 任何管理员自助强制更改密码操作（例如，密码到期）
  * 源自[密码重置门户](https://passwordreset.microsoftonline.com)的任何管理员自助密码重置操作
  * 任何管理员通过 [Azure 门户](https://portal.azure.com)发起的任何最终用户密码重置操作

### <a name="user-and-admin-operations-that-are-not-supported-for-password-writeback"></a>密码写回服务不支持的用户和管理员操作

在以下任意情况下，都*不会*写回密码：

* **不支持的最终用户操作**
  * 任何最终用户使用 PowerShell 版本 1、版本 2 或 Azure AD 图形 API 重置自己的密码
* **不支持的管理员操作**
  * 任何管理员通过 [Office 管理门户](https://portal.office.com)发起的任何最终用户密码重置操作
  * 任何管理员发起的最终用户密码重置操作（使用 PowerShell 版本 1、版本 2 或 Azure AD 图形 API）

尽管我们在努力消除这些限制，但对于何时能够分享解决方法，我们还无法提供具体的时间表。

## <a name="password-writeback-security-model"></a>密码写回安全模型

密码写回是高度安全的服务。 为确保信息受到保护，我们启用了 4 层安全模型，具体如下所述：

* **租户特定的服务总线中继**
  * 如果用户设置服务，我们会设置租户专属服务总线中继，此中继受随机生成的强密码保护，而 Microsoft 永远无法访问此密码。
* **锁定的加密强密码加密密钥**
  * 创建服务总线中继后，我们会创建对称强密钥，用于在经过线路时加密密码。 此密钥仅驻留在公司在云中的密钥存储内，会被牢牢锁定并接受审核，就像目录中的其他任何密码一样。
* **行业标准传输层安全性 (TLS)**
  1. 云中发生密码重置或更改操作时，我们会采用纯文本密码并用公钥对其进行加密。
  2. 我们将它置于使用 Microsoft SSL 证书通过加密通道发送到服务总线中继的 HTTPS 消息中。
  3. 此消息到达服务总线后，本地代理便会唤醒，并使用先前生成的强密码对服务总线进行身份验证。
  4. 本地代理选取加密的消息，使用我们生成的私钥对消息进行解密。
  5. 本地代理尝试通过 AD DS SetPassword API 设置密码。 通过此步骤，我们可以在云中强制实施 Active Directory 本地密码策略（如复杂性、期限、历史记录和筛选器）。
* **消息过期策略** 
  * 如果由于本地服务关闭而导致消息位于服务总线中，消息会超时并在几分钟后遭到删除。 消息超时和删除进一步提高了安全性。

### <a name="password-writeback-encryption-details"></a>密码写回加密详细信息

在用户提交密码重置请求后，重置请求会先经历多个加密步骤，然后才会到达本地环境。 这些加密步骤可确保实现最高的服务可靠性和安全性。 这些步骤如下所述：

* **第 1 步：使用 2048 位 RSA 密钥加密密码**：在用户提交要写回本地的密码后，提交的密码本身会使用 2048 位 RSA 密钥进行加密。
* **第 2 步：使用 AES-GCM 进行包级加密**：使用 AES-GCM 加密整个包（密码及所需的元数据）。 此加密可防止任何可直接访问基础服务总线通道的人员查看或篡改内容。
* **第 3 步：所有通信都是通过 TLS/SSL 进行**：与服务总线的所有通信都是在 SSL/TLS 通道中发生。 此加密可保护内容不被未经授权的第三方查看/篡改。
* **每 6 个月自动滚动更新密钥**：每 6 个月或每当在 Azure AD Connect 中禁用并重新启用密码写回服务时。 自动滚动更新所有密钥是为了确保实现最高的服务安全性。

### <a name="password-writeback-bandwidth-usage"></a>密码写回带宽用量

密码写回服务是低带宽服务，只有在以下情况下，才会将请求发送回本地代理：

* 通过 Azure AD Connect 启用或禁用此功能时，发送两条消息。
* 在服务运行的持续时间内，如果服务有检测信号，则每隔 5 分钟发送一条消息。
* 每当提交新密码时，发送两条消息：
    * 第一条消息是操作执行请求。
    * 第二条消息包含操作结果，在以下情况下发送：
        * 每次在用户自助重置密码期间提交新密码时。
        * 每次在用户执行密码更改操作期间提交新密码时。
        * 每当在管理员发起的用户密码重置操作期间提交新密码时（仅限通过 Azure 管理门户）。

#### <a name="message-size-and-bandwidth-considerations"></a>消息大小和带宽注意事项

上述每条消息的大小通常小于 1KB。 即使是在承受极高负载的情况下，密码写回服务本身占用的带宽也就是每秒几个 KB。 由于每条消息是只在密码更新操作要求时才实时发送的，并且消息很小，因此密码写回功能的带宽用量微不足道，产生的影响可以忽略不计。

## <a name="next-steps"></a>后续步骤

* [如何成功推出 SSPR？](active-directory-passwords-best-practices.md)
* [重置或更改密码](active-directory-passwords-update-your-own-password.md)。
* [注册自助服务密码重置](active-directory-passwords-reset-register.md)。
* [是否有许可问题？](active-directory-passwords-licensing.md)
* [SSPR 使用哪些数据？应为用户填充哪些数据？](active-directory-passwords-data.md)
* [哪些身份验证方法可供用户使用？](active-directory-passwords-how-it-works.md#authentication-methods)
* [SSPR 有哪些策略选项？](active-directory-passwords-policy.md)
* [如何报告 SSPR 中的活动？](active-directory-passwords-reporting.md)
* [SSPR 中的所有选项有哪些？它们有哪些含义？](active-directory-passwords-how-it-works.md)
* [我认为有些功能被破坏。如何对 SSPR 进行故障排除？](active-directory-passwords-troubleshoot.md)
* [我有在别处未涵盖的问题](active-directory-passwords-faq.md)

[Writeback]: ./media/active-directory-passwords-writeback/enablepasswordwriteback.png "在 Azure AD Connect 中启用密码写回"
