---
title: 配置 Azure 多重身份验证的应用密码-Azure Active Directory
description: 了解如何在 Azure 多重身份验证中配置和使用旧版应用程序的应用密码
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/05/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b247b64d563bc2b12c5bffff6a460d77cb96207
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84485480"
---
# <a name="enable-and-use-azure-multi-factor-authentication-with-legacy-applications-using-app-passwords"></a>启用 Azure 多重身份验证并将其用于使用应用密码的旧版应用程序

某些应用程序（如 Office 2010 或更早版本）和 iOS 11 之前的 Apple Mail 不支持多重身份验证。 应用未配置为接受身份验证或提示的辅助形式。 若要在为用户帐户启用 Azure 多重身份验证的情况下安全地使用这些应用程序，可以使用应用密码。 这些应用密码替换了你的传统密码，以允许应用绕过多重身份验证并正常工作。

Microsoft Office 2013 客户端及更高版本支持现代身份验证。 Office 2013 客户端（包括 Outlook）支持新式身份验证协议，并且可以启用以进行双重验证。 启用客户端后，客户端即不要求使用应用密码。

本文介绍如何为不支持多重身份验证提示的旧版应用程序启用和使用应用密码。

>[!NOTE]
> 应用密码不适用于基于多重身份验证策略和新式身份验证的条件性访问。

## <a name="overview-and-considerations"></a>概述和注意事项

如果为 Azure 多重身份验证启用了用户帐户，则请求进行其他验证将中断常规登录提示。 一些较旧的应用程序不会在登录过程中了解这一中断，因此身份验证会失败。 若要维护用户帐户安全性并启用 Azure 多重身份验证，可以使用应用密码，而不是用户的常规用户名和密码。 如果在登录过程中使用了应用密码，则没有其他验证提示，因此身份验证成功。

应用密码是自动生成的，不由用户指定。 这种自动生成的密码会使攻击者更难猜到，因此更安全。 用户无需跟踪密码或每次都输入密码，因为每个应用程序只需输入一次应用密码。

使用应用密码时，请注意以下事项：

* 每个用户的应用密码限制为40。
* 缓存密码并在本地方案中使用密码的应用程序可能会失败，因为该应用程序密码不是工作或学校帐户之外的已知。 这种情况的一个例子是 Exchange 电子邮件位于本地而存档邮件位于云中。 在这种情况下，不能使用同一密码。
* 在用户帐户上启用 Azure 多重身份验证后，应用密码可用于大多数非浏览器客户端（如 Outlook 和 Microsoft Skype for Business）。 但是，不能通过非浏览器应用程序（例如 Windows PowerShell）使用应用程序密码来执行管理操作。 即使用户具有管理帐户，也无法执行这些操作。
    * 若要运行 PowerShell 交吧，应创建具有强密码的服务帐户，并且不为该帐户启用双重验证。

>[!WARNING]
> 在客户端同时与本地和云自动发现终结点通信的混合环境中，应用密码无效。 需要域密码才能进行本地身份验证。 需要应用密码才能进行云身份验证。

### <a name="app-password-names"></a>应用密码名称

应用密码名称应能反映使用它们的设备。 如果有安装了非浏览器应用（如 Outlook、Word 和 Excel）的笔记本电脑，可为这些应用创建名为 Laptop 的应用密码。 为在台式计算机上运行的相同应用程序创建名为 Desktop 的另一个应用密码。

建议为每个设备创建一个应用密码，而不是每个应用程序创建一个应用密码。

## <a name="federated-or-single-sign-on-app-passwords"></a>联合或单一登录应用密码

Azure AD 支持联合或单一登录（SSO），具有本地 Active Directory 域服务（AD DS）。 如果你的组织与 Azure AD 联合，并且你使用的是 Azure 多重身份验证，则以下应用密码注意事项适用：

>[!NOTE]
> 以下要点仅适用于联合 (SSO) 客户。

* 应用密码由 Azure AD 进行验证，从而绕过了联合。 仅在设置应用密码时，才会主动使用联合。
* 与被动流程不同，对于联合 (SSO) 用户，不会与标识提供者 (IdP) 联系。 应用密码存储在工作或学校帐户中。 如果用户离开公司，则该用户的信息通过 **DirSync** 实时流向工作或学校帐户。 帐户的禁用/删除可能需要长达三个小时才能同步，这会延迟 Azure AD 的应用密码的禁用/删除。
* 应用密码功能不遵循“本地客户端访问控制”设置。
* 应用密码功能不提供本地身份验证日志记录或审核功能。

某些高级体系结构需要结合使用多因素身份验证和客户端凭据。 凭据可以包括工作或学校帐户的用户名和密码以及应用密码。 要求取决于如何执行身份验证。 对于针对本地基础结构进行身份验证的客户端，需要使用工作或学校帐户用户名和密码。 对于针对 Azure AD 进行身份验证的客户端，需要使用应用密码。

例如，假设有以下体系结构：

* Active Directory 的本地实例与 Azure AD 联合。
* 使用 Exchange online。
* 在本地使用 Skype for Business。
* 使用 Azure 多重身份验证。

在此方案中，使用以下凭据：

* 若要登录到 Skype for Business，请使用工作或学校帐户的用户名和密码。
* 若要从连接到 Exchange Online 的 Outlook 客户端访问通讯簿，请使用应用密码。

## <a name="allow-users-to-create-app-passwords"></a>允许用户创建应用密码

默认情况下，用户无法创建应用密码。 必须先启用应用密码功能，然后用户才能使用它们。 若要使用户能够创建应用密码，请完成以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 搜索并选择 " **Azure Active Directory**"，然后选择 "**用户**"。
3. 在 "*用户*" 窗口顶部的导航栏中选择 "**多重身份验证**"。
4. 在“多重身份验证”下，选择“服务设置”。
5. 在“服务设置”页上，选择“允许用户创建应用密码，以登录非浏览器应用”选项。 

    ![Azure 门户的屏幕截图，其中显示了允许用户使用应用密码进行多重身份验证的服务设置](media/concept-authentication-methods/app-password-authentication-method.png)

## <a name="create-an-app-password"></a>创建应用密码

当用户完成 Azure 多重身份验证的初始注册时，可以选择在注册过程结束时创建应用密码。

用户还可以在注册后创建应用密码。 有关适用于用户的详细信息和详细步骤，请参阅 [Azure 多重身份验证中的应用密码是什么？](../user-help/multi-factor-authentication-end-user-app-passwords.md)。

## <a name="next-steps"></a>后续步骤

有关如何允许用户快速注册 Azure 多重身份验证的详细信息，请参阅[组合式 security information registration 概述](concept-registration-mfa-sspr-combined.md)。
