---
title: "Azure AD SSPR 与密码写回 | Microsoft Docs"
description: "使用 Azure AD 和 Azure AD Connect 将密码写回到本地目录"
services: active-directory
keywords: "Active Directory 密码管理, 密码管理, Azure AD 自助密码重置"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: gahug
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: joflore
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: 7783d0fc30b4b7d098a89595771993b33fb0aa97
ms.contentlocale: zh-cn
ms.lasthandoff: 07/06/2017

---
# <a name="password-writeback-overview"></a>密码写回概述

使用此功能，可将 Azure AD 配置为将密码写回本地 Active Directory。 有了此功能，你无需再设置和管理复杂的本地自助服务密码重置解决方案。此功能提供了一种基于云的便利方法，让你的用户能够在任何位置重置本地密码。 密码写回是一个 [Azure Active Directory Connect](./connect/active-directory-aadconnect.md) 组件，[Azure Active Directory Premium](active-directory-editions.md) 的当前订户可以启用和使用该组件。

密码写回提供以下功能

* **零延迟反馈** - 密码写回是一项同步操作。 如果用户的密码不符合策略或因某种原因而无法重置或更改，用户会立即收到通知。
* **支持使用 AD FS 或其他联合技术的用户重置密码** - 使用密码写回功能，只要联合用户帐户同步到你的 Azure AD 租户，用户就能够从云中管理他们的本地 AD 密码。
* **支持使用[密码哈希同步](./connect/active-directory-aadconnectsync-implement-password-synchronization.md)的用户重置密码** - 当密码重置服务检测到同步用户帐户已启用密码哈希同步时，我们会同时重置此帐户的本地密码和云密码。
* **支持从访问面板和 Office 365 更改密码** - 当联合或密码同步用户更改其过期或未过期的密码时，我们将这些密码写回到你的本地 AD 环境。
* **支持管理员从 Azure 门户重置密码时写回密码** - 无论何时管理员在 [Azure 门户](https://portal.azure.com)中重置用户的密码，只要该用户已联合或密码已同步，我们便还会设置管理员在本地 AD 上选择的密码。 Office 管理门户当前不支持此功能。
* **实施本地 AD 密码策略** - 当用户重置其密码时，在将重置提交到该目录之前，我们要确保它符合你的本地 AD 策略。 这包括历史记录、复杂性、年龄、密码筛选器以及你在本地 AD 中定义的所有其他密码限制。
* **不需要任何入站防火墙规则** - 密码写回功能使用 Azure 服务总线中继作为底层通信通道，这意味着你无需打开防火墙上的任何入站端口即可使用此功能。
* **本地 Active Directory 的受保护组中存在的用户帐户不支持** - 有关受保护组的详细信息，请参阅 [Active Directory 中的受保护帐户和组](https://technet.microsoft.com/library/dn535499.aspx)。

## <a name="how-password-writeback-works"></a>密码写回的工作原理

当联合或密码哈希同步用户在云中重置或更改其密码时，将发生以下情况：

1. 我们检查用户具有何种类型的密码。
    * 如果我们看到密码在本地管理
        * 我们将检查写回服务是否在运行，如果是，我们让用户继续操作
        * 如果写回服务未运行，我们告知用户暂时不能重置其密码
2. 接下来，用户通过适当的身份验证界面，到达重置密码屏幕。
3. 用户选择一个新密码并进行确认。
4. 单击提交时，我们使用写回设置过程中创建的公钥来加密纯文本密码。
5. 加密密码后，我们将其包括在一个有效负载中，该负载通过 HTTPS 通道发送到租户特定的服务总线中继（我们在写回设置过程中也为你设置了此项）。 此中继受随机生成的密码保护，只有你的本地安装知道该密码。
6. 一旦消息到达服务总线，密码重置终结点便自动唤醒并看到有重置请求挂起。
7. 然后，该服务使用云定位点属性查找相关用户。 要使查找成功：

    * 该用户对象必须存在于 AD 连接器空间中
    * 该用户对象必须链接到相应的 MV 对象
    * 该用户对象必须链接到相应的 AAD 连接器对象。
    * AD 连接器对象到 MV 的链接必须具有同步规则 `Microsoft.InfromADUserAccountEnabled.xxx` 的链接。 <br> <br>
    从云中进行调用时，同步引擎使用 cloudAnchor 属性查找 AAD 连接器空间对象，然后遵循该链接依次回到 MV 对象和 AD 对象。 由于同一用户可能有多个 AD 对象（多林），因此，同步引擎将依赖 `Microsoft.InfromADUserAccountEnabled.xxx` 链接选取正确的对象。

    > [!Note]
    > 由于此逻辑，Azure AD Connect 必须能够与用于密码写回 PDC 模拟器，若要进行通信。 如果需要手动启用，可以通过右键单击 Active Directory 同步连接器的“属性”，然后选择“配置目录分区”将 Azure AD Connect 连接到 PDC 模拟器。 在这里，请查找“域控制器连接设置”部分，并检查标题为“仅使用首选的域控制器”的框。 即使首选的 DC 不是 PDC 模拟器，Azure AD Connect 也会尝试连接到 PDC 来执行密码写回。

8. 一旦找到用户帐户，我们将尝试直接在相应的 AD 林中重置密码。
9. 如果密码设置操作成功，我们将告诉用户其密码已被更改。
    > [!NOTE]
    > 使用密码同步将用户密码同步到 Azure AD 时，可能本地密码策略比云密码策略要弱。 在这种情况下，我们仍然强制执行本地策略，并允许密码哈希同步对该密码的哈希进行同步。 这样无论使用密码同步或联合身份验证来提供单一登录，都可以确保本地策略在云中强制执行。

10. 如果密码设置操作失败，我们会将错误返回给用户，让他们再试一次。
    * 操作失败的原因如下
        * 服务已关闭
        * 用户选择的密码不符合组织策略
        * 我们在本地 AD 中找不到该用户

    我们对于许多这类情况都有一个特定消息，并告知用户他们可以执行哪些操作来解决问题。

## <a name="configuring-password-writeback"></a>配置密码写回

如果你要使用密码写回，我们始终建议使用 [Azure AD Connect](./connect/active-directory-aadconnect-get-started-express.md) 的自动更新功能。

DirSync 和 Azure AD Sync 不再支持启用密码写回，详见文章[从 DirSync 和 Azure AD Sync 升级](connect/active-directory-aadconnect-dirsync-deprecated.md)以帮助你转换。

以下步骤假定已在环境中使用[快速](./connect/active-directory-aadconnect-get-started-express.md)或[自定义](./connect/active-directory-aadconnect-get-started-custom.md)设置配置了 Azure AD Connect。

1. 若要配置和启用密码写回，请登录到 Azure AD Connect 服务器并启动“Azure AD Connect”配置向导。
2. 在“欢迎”屏幕中，单击“配置”。
3. 在“其他任务”屏幕上，单击“自定义同步选项”，然后选择“下一步”。
4. 在“连接到 Azure AD”屏幕上输入全局管理员凭据，然后选择“下一步”。
5. 在“连接目录”和“域和 OU 筛选”屏幕上，可以选择“下一步”。
6. 在“可选功能”屏幕上，选中“密码写回”旁边的框，然后单击“下一步”。
   ![在 Azure AD Connect 中启用密码写回][Writeback]
7. 在“准备配置”屏幕上，单击“配置”并等待进程完成。
8. 看到配置完成后，可以单击“退出”

## <a name="licensing-requirements-for-password-writeback"></a>密码写回的许可要求

有关许可的信息，请参阅主题[密码写回所需的许可证](active-directory-passwords-licensing.md#licenses-required-for-password-writeback)或以下站点

* [Azure Active Directory 定价站点](https://azure.microsoft.com/pricing/details/active-directory/)
* [企业移动性 + 安全性](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Secure Productive Enterprise](https://www.microsoft.com/secure-productive-enterprise/default.aspx)

### <a name="on-premises-authentication-modes-supported-for-password-writeback"></a>密码写回支持的本地身份验证模式

密码写回适用于以下用户密码类型：

* **仅限云的用户**：密码写回不适用于这种情况，因为没有任何本地密码
* **密码同步的用户**：受密码写回的支持
* **联合用户**：受密码写回的支持
* **直通身份验证用户**：受密码写回的支持

### <a name="user-and-admin-operations-supported-for-password-writeback"></a>密码写回支持的用户和管理员操作

在以下所有情况下，都会写回密码：

* **支持的最终用户操作**
  * 最终用户以自助形式执行的任何自愿更改密码操作
  * 任何最终用户以自助形式执行的任何强制更改密码操作（例如，在密码过期时）
  * 最终用户通过[密码重置门户](https://passwordreset.microsoftonline.com)以自助形式执行的任何密码重置操作
* **支持的管理员操作**
  * 管理员以自助形式执行的任何自愿更改密码操作
  * 任何管理员以自助形式执行的任何强制更改密码操作（例如，在密码过期时）
  * 管理员通过[密码重置门户](https://passwordreset.microsoftonline.com)以自助形式执行的任何密码重置操作
  * 任何管理员通过 [Azure 经典门户](https://manage.windowsazure.com)发起的任何最终用户密码重置操作
  * 任何管理员通过 [Azure 门户](https://portal.azure.com)发起的任何最终用户密码重置操作

### <a name="user-and-admin-operations-not-supported-for-password-writeback"></a>密码写回不支持的用户和管理员操作

在以下任意情况下，都**不会**写回密码：

* **不支持的最终用户操作**
  * 任何最终用户使用 PowerShell v1、v2 或 Azure AD 图形 API 重置其自己的密码
* **不支持的管理员操作**
  * 任何管理员通过 [Office 管理门户](https://portal.office.com)发起的任何最终用户密码重置操作
  * 管理员通过 PowerShell v1、v2 或 Azure AD 图形 API 发起的任何最终用户密码重置操作

尽管我们正在努力消除这些限制，但对于何时能够分享解决方法，我们还无法提供具体的时间表。

## <a name="password-writeback-security-model"></a>密码写回安全模型

密码写回是高度安全的服务。  为确保你的信息受保护，我们启用了一个 4 层安全模型，如下所述。

* **租户特定的服务总线中继**
  * 如果用户设置该服务，我们将建立租户特定的服务总线中继，该中继受随机生成的强密码保护，而 Microsoft 绝不会接触此密码。
* **锁定的加密强密码加密密钥**
  * 创建服务总线中继后，我们创建一个强大的非对称密钥对，用于在经过线路时加密密码。 此密钥仅驻留在公司在云中的密钥存储内，就像目录中的任何密码一样，将被牢牢锁住并接受审核。
* **行业标准 TLS**
  1. 云中发生密码重置或更改操作时，我们将采用纯文本密码并用公钥对其进行加密。
  2. 然后，我们将其置于 HTTPS 消息中，该消息使用 Microsoft 的 SSL 证书通过加密通道发送到服务总线中继。
  3. 此消息到达服务总线后，本地代理将唤醒并使用先前生成的强密码对服务总线进行身份验证。
  4. 本地代理选取加密的消息，使用我们生成的私钥对消息进行解密。
  5. 然后，本地代理尝试通过 AD DS SetPassword API 设置密码。
     * 通过此步骤，我们可以在云中强制实施你的 AD 本地密码策略（复杂性、年龄、历史记录、筛选器等）。
* **消息过期策略** 
  * 如果由于本地服务关闭而使消息位于服务总线中，消息将会超时并在几分钟后删除，以便进一步提高安全性。

### <a name="password-writeback-encryption-details"></a>密码写回加密详细信息

下面介绍了在用户提交密码重置请求之后、但该请求到达本地环境之前，为了确保最高的服务可靠性和安全性，该请求所要经历的加密步骤。

* **步骤 1 - 使用 2048 位 RSA 密钥进行密码加密** - 用户提交要写回本地的密码之后，首先会使用 2048 位 RSA 密钥来加密提交的密码本身。
* **步骤 2 - 使用 AES-GCM 进行包级别的加密** - 然后，使用 AES-GCM 加密整个包（密码 + 所需的元数据）。 这可以防止任何人通过查看/篡改内容来直接访问底层服务总线通道。
* **步骤 3 - 所有通信通过 TLS/SSL 发生** - 与服务总线的所有通信都在 SSL/TLS 通道中发生。 这可以保护未经授权的第三方发送的内容。
* **每隔 6 个月自动滚动更新密钥** - 将每隔 6 个月，或者每当在 Azure AD Connect 中禁用/重新启动密码写回时，自动滚动更新所有这些密钥，确保最高的服务安全性与可靠性。

### <a name="password-writeback-bandwidth-usage"></a>密码写回带宽用量

密码写回是一种带宽消耗量很低的服务，只在以下情况下，才将请求发回到本地代理：

1. 通过 Azure AD Connect 启用或禁用该功能时发送两条消息。
2. 在服务运行的持续时间内，如果服务有检测信号，则每隔 5 分钟发送一条消息。
3. 每次提交新密码时发送两条消息
    * 第一条消息是执行操作的请求
    * 第二条消息包含操作结果，在以下情况下发送：
        * 每次在用户自助重置密码期间提交新密码时。
        * 每次在用户执行密码更改操作期间提交新密码时。
        * 每次在管理员发起的用户密码重置期间提交新密码时（仅限通过 Azure 管理门户）。

#### <a name="message-size-and-bandwidth-considerations"></a>消息大小和带宽注意事项

上述每条消息的大小通常小于 1 kb，即使是在承受极高负载的情况下，密码写回服务本身消耗的带宽每秒也就是几个 kb。 由于每条消息是只在密码更新操作有需要时才实时发送的，并且消息很小，因此，写回功能的带宽用量确实微不足道，产生的影响可以忽略不计。

## <a name="next-steps"></a>后续步骤

以下链接提供了有关使用 Azure AD 进行密码重置的其他信息

* [**快速入门**](active-directory-passwords-getting-started.md) - 启动并运行 Azure AD 自助服务密码管理 
* [**授权**](active-directory-passwords-licensing.md) - 配置 Azure AD 授权
* [**数据**](active-directory-passwords-data.md) - 了解所需的数据以及如何使用它进行密码管理
* [**推出**](active-directory-passwords-best-practices.md) - 使用此处提供的指南计划 SSPR 并将其部署到用户
* [**自定义**](active-directory-passwords-customize.md) - 自定义公司的 SSPR 体验的外观。
* [**策略**](active-directory-passwords-policy.md) - 了解并设置 Azure AD 密码策略
* [**报告**](active-directory-passwords-reporting.md) - 了解用户是否访问 SSPR 功能，以及在何时何处进行访问
* [**深入技术探究**](active-directory-passwords-how-it-works.md) - 了解幕后的工作原理
* [**常见问题**](active-directory-passwords-faq.md) - 如何？ 为什么？ 什么？ 何处？ 谁？ 何时？ - 常见问题的答案
* [**故障排除**](active-directory-passwords-troubleshoot.md) - 了解如何解决使用 SSPR 时遇到的常见问题

[Writeback]: ./media/active-directory-passwords-writeback/enablepasswordwriteback.png "在 Azure AD Connect 中启用密码写回"

