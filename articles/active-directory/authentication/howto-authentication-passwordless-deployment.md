---
title: 使用 Azure AD 规划无密码 authentication 部署
description: 了解如何规划和部署 Azure Active Directory 无密码 authentication 实现
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: baselden
author: iainfoulds
manager: daveba
ms.reviewer: baselden, librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1cc8a62bd75a01cb6b7184cb52585c4f8b08cadb
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78378240"
---
# <a name="plan-a-passwordless-authentication-deployment-in-azure-active-directory"></a>在 Azure Active Directory 中规划无密码 authentication 部署

> [!NOTE]
> 若要创建此部署计划的脱机版本，请使用浏览器的打印到 PDF 功能。

大多数网络攻击以已泄露的用户名和密码开头。 组织通过要求用户使用以下方法之一来尝试应对威胁：

- 长密码
- 复杂密码
- 经常更改密码
- 多重身份验证 (MFA)

Microsoft 的[研究表明](https://aka.ms/passwordguidance)，这些工作会给用户带来烦恼并降低支持成本。 有关详细信息，请参阅[Pa $ $word 不重要](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)。

### <a name="benefits-of-passwordless-authentication"></a>无密码 authentication 的优点

- **提高了安全性**。 通过删除密码作为攻击面，降低网络钓鱼和密码喷涂攻击的风险。
-  **更好的用户体验**。 让用户能够方便地从任何位置访问数据。 提供对移动时的应用程序和服务（例如 Outlook、OneDrive 或 Office）的轻松访问。
-  **强健的见解**。 通过强大的日志记录和审核深入了解用户无密码活动。

使用无密码时，会将密码替换为你已有的内容以及你知道的内容。 例如，Windows Hello 企业版可以使用生物指纹或指纹等生物识别手势，或不通过网络传输的特定于设备的 PIN。

## <a name="passwordless-authentication-methods"></a>无密码身份验证方法
Microsoft 提供了三种无密码的身份验证选项，涵盖了许多情况。 这些方法可以一起使用：

- [Windows Hello 企业版](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless)最适用于专用 windows 计算机上的用户。
- 使用[FIDO2 安全](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless)密钥的安全密钥登录对登录到共享计算机（如网亭）的用户特别有用，这种情况下，如果使用 "电话" 受限制，并且具有高特权标识。
- 使用[Microsoft Authenticator 应用](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless)进行电话登录对于向具有移动设备的用户提供无密码选项非常有用。 验证器应用通过允许用户登录到任何平台或浏览器，将任何 iOS 或 Android 手机变成强的无密码凭据。 用户可以通过以下方式登录：向其手机发送通知，将屏幕上显示的数字与电话上的数字匹配，然后使用其生物识别数据或 PIN 来确认。

### <a name="passwordless-authentication-scenarios"></a>无密码 authentication 方案

Microsoft 的无密码身份验证方法可实现不同的方案。 请考虑你的组织需要、先决条件以及每种身份验证方法的功能，以选择你的无密码 authentication 战略。 建议使用 Windows 10 设备的每个组织都使用 Windows Hello for Business。 然后，为其他方案添加电话登录（包括 Microsoft Authenticator 应用）或安全密钥。

| 场景 | 电话身份验证 | 安全密钥 | Windows Hello 企业版 |
| --- | --- | --- | --- |
| **计算机登录**： <br> 从分配的 Windows 10 设备 | **是** | **是** <br> 带生物识别、PIN | **是**<br>带有生物识别识别和或 PIN |
| **计算机登录**： <br> 从共享 Windows 10 设备 | **是** | **是** <br> 带生物识别、PIN  | **是** |
| **Web 应用登录**： <br>从用户专用计算机 | **是** | **是** <br> 已通过计算机登录启用对应用的单一登录 | **是**<br> 已通过计算机登录启用对应用的单一登录 |
| **Web 应用登录**： <br> 从移动设备或非 windows 设备 | **是** | **是** | **是** |
| **计算机登录**： <br> 非 Windows 计算机 | **是** | **是** | **是** |

有关为你的组织选择最佳方法的信息，请参阅[确定无密码方法](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless#deciding-a-passwordless-method)。

## <a name="prerequisites"></a>先决条件

在开始无密码部署之前，组织必须满足以下先决条件：

| 先决条件 | Authenticator 应用 | FIDO2 安全密钥 |
| --- | --- | --- |
| 已启用[Azure 多重身份验证和自助密码重置（SSPR）的组合注册](howto-registration-mfa-sspr-combined.md)（预览功能） | √ | √ |
| [用户可以执行 Azure 多重身份验证](howto-mfa-getstarted.md) | √ | √ |
| [用户已注册 Azure 多重身份验证和 SSPR](howto-registration-mfa-sspr-combined.md) | √ | √ |
| [用户已将其移动设备注册到 Azure Active Directory](../devices/overview.md) | √ |   |
| Windows 10 版本1809或更高版本，使用受支持的浏览器（如 Microsoft Edge 或 Mozilla Firefox） <br> （版本67或更高版本）。 <br> *Microsoft 建议采用版本1903或更高版本来提供本机支持*。 |   | √ |
| 兼容的 FIDO2 安全密钥。 确保使用的是[经过 Microsoft 测试和验证](howto-authentication-passwordless-enable.md)的 FIDO2 安全设备或其他兼容的 FIDO2 安全设备。 |   | √ |

### <a name="prerequisites-for-windows-hello-for-business"></a>Windows Hello 企业版的先决条件

Windows Hello 的先决条件非常依赖于你是在本地、混合还是仅限云的配置中进行部署。 有关详细信息，请参阅[Windows Hello 企业版先决条件的完整列表](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)。

### <a name="azure-multi-factor-authentication"></a>Azure 多重身份验证

用户将其无密码方法注册为 Azure 多因素身份验证注册流的一部分。 使用用户名和密码以及另一个注册方法的多重身份验证可以作为回退，以防它们在某些情况下无法使用其电话或安全密钥。

### <a name="licensing"></a>许可 
无密码 authentication 无需额外付费，不过某些先决条件可能需要高级订阅。 [Azure Active Directory 许可页面](https://azure.microsoft.com/pricing/details/active-directory/)中提供详细的功能和许可信息。 

## <a name="develop-a-plan"></a>制定计划

考虑您的业务需求和每种身份验证方法的用例。 然后选择最符合需要的方法。

### <a name="use-cases"></a>用例

下表概述了要在此项目中实现的用例。

| 区域 | 说明 |
| --- | --- |
| **Access** | 在公司网络内部或外部的公司或个人设备中，可以使用无密码登录。 |
| **审核** | 管理员可使用使用情况数据进行近实时审核。 <br> 使用情况数据至少每29天下载到企业系统，或使用 SIEM 工具。 |
| **管理** | 定义和监视相应身份验证方法和关联组的用户分配生命周期。 |
| **安全性** | 可以通过用户和组分配来控制对适当身份验证方法的访问。 <br> 只有经过授权的用户才能使用无密码登录。 |
| **“性能”** | 访问分配传播时间线已记录并被监视。 <br> 为便于使用，对登录时间进行度量。 |
| **用户体验** | 用户了解移动兼容性。 <br> 用户可以配置无密码登录的验证器应用。 |
| **支持** | 用户了解如何查找对无密码登录问题的支持。 |

### <a name="engage-the-right-stakeholders"></a>吸引适当的利益干系人

当技术项目失败时，通常是由于对影响、结果和责任的预期不匹配而导致的。 若要避免这些问题，请[确保你参与了正确的利益干系人](../fundamentals/active-directory-deployment-plans.md#include-the-right-stakeholders)，并且项目中的利益干系人角色非常了解。

### <a name="plan-communications"></a>规划沟通

通信对于任何新服务的成功至关重要。 主动传达用户体验如何更改、何时发生更改，以及如何在遇到问题时获得支持。

与最终用户的通信应包含以下信息：

- [启用组合的安全注册体验](howto-authentication-passwordless-phone.md)
- [下载 Microsoft Authenticator 应用](../user-help/user-help-auth-app-download-install.md)
- [在 Microsoft Authenticator 应用中注册](howto-authentication-passwordless-phone.md)
- [用手机登录](../user-help/user-help-auth-app-sign-in.md)

Microsoft 提供多重身份验证[通信模板](https://aka.ms/mfatemplates)、自助服务密码重置（SSPR）[通信模板](https://www.microsoft.com/download/details.aspx?id=56768)和[最终用户文档](../user-help/security-info-setup-signin.md)，有助于草拟你的通信。 您可以通过选择该页面上的**安全信息**链接，将用户发送到[https://myprofile.microsoft.com](https://myprofile.microsoft.com/)以直接注册。

### <a name="plan-to-pilot"></a>规划试点

部署无密码 authentication 时，应该首先启用一个或多个试点组。 您可以专门为此目的[创建组](../fundamentals/active-directory-groups-create-azure-portal.md)。 将参与试点的用户添加到组。 然后，为所选组启用新的无密码身份验证方法。

可以从本地目录或从 Azure AD 同步组。 对试点结果满意后，可以针对所有用户切换无密码 authentication。

请参阅部署计划页上的[试验最佳实践](https://aka.ms/deploymentplans)。

## <a name="plan-passwordless-authentication-with-the-microsoft-authenticator-app"></a>通过 Microsoft Authenticator 应用计划无密码 authentication

Microsoft Authenticator 应用可从 Google Play 或 Apple App Store 免费下载。 [详细了解如何下载 Microsoft Authenticator 应用](https://www.microsoft.com/account/authenticator?cmp=h66ftb_42hbak)。 让用户下载 Microsoft Authenticator 应用。 并按照说明启用手机登录。 

它会将任何 iOS 或 Android 手机变成强的无密码凭据。 用户可以通过以下方式登录到任何平台或浏览器：向其手机发送通知，将屏幕上显示的数字与电话上的一个数字匹配，然后使用生物识别或 PIN 来确认。 [请参阅有关 Microsoft Authenticator 应用的工作原理的详细信息](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless#user-using-microsoft-authenticator-for-passwordless-sign-in)。 

![用验证器应用登录](./media/howto-authentication-passwordless-deployment/passwordless-dp-sign-in.png)

### <a name="technical-considerations-for-the-microsoft-authenticator-app"></a>Microsoft Authenticator 应用的技术注意事项

**AD FS 集成**-当用户启用 Microsoft Authenticator 无密码凭据时，针对该用户的身份验证将默认为发送批准通知。 混合租户中的用户被阻止定向到 ADFS 进行登录，除非他们选中 "改用密码"。 此过程还会绕过任何本地条件性访问策略和传递身份验证流。 但是，如果指定了*login_hint* ，则用户将转发到 ADFS，并跳过使用无密码凭据的选项。

**Azure 多重身份验证服务器**-通过组织的本地 Azure MFA 服务器启用多重身份验证的最终用户可以创建和使用单个无密码手机登录凭据。 如果用户尝试使用凭据升级 Microsoft Authenticator 的多个安装（5个或更多），则此更改可能会导致错误。

**设备注册**-若要使用验证器应用进行无密码身份验证，设备必须在 Azure AD 租户中注册，并且不能是共享设备。 设备只能在单个租户中注册。 此限制意味着使用验证器应用的手机登录仅支持一个工作或学校帐户。

## <a name="plan-passwordless-authentication-with-fido2-security-keys"></a>通过 FIDO2 安全密钥计划无密码 authentication
有三种类型的无密码登录部署可用于安全密钥：

-    在支持的浏览器上 Azure Active Directory web 应用
-    加入 Windows 10 设备 Azure Active Directory
-    已加入混合 Azure Active Directory Windows 10 设备（预览）
     -    提供对基于云的资源和本地资源的访问权限。 有关访问本地资源的详细信息，请参阅[使用 FIDOP2 密钥的 SSO 到本地资源](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-on-premises)

必须启用**兼容的 FIDO2 安全密钥**。 Microsoft[与 FIDO2 重要供应商宣布了重要的合作关系](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Microsoft-passwordless-partnership-leads-to-innovation-and-great/ba-p/566493)。

**对于 Azure AD web 应用和 Azure AD Windows 加入设备**：

-    Windows 10 版本1809或更高版本，使用受支持的浏览器（如 Microsoft Edge 或 Mozilla Firefox）（版本67或更高版本）。 
-    Windows 10 版本1809支持 FIDO2 登录，并可能需要部署 FIDO2 密钥制造商提供的软件。 建议使用版本1903或更高版本。 

**对于已加入域的混合 Azure Active Directory 设备**： 
-    Windows 10 预览体验内部版本18945或更高版本
-    已充分修补运行 Windows Server 2016 或2019的域服务器。
-    最新版本的 Azure AD Connect

有关要求的完整列表，请参阅[使用 Azure Active Directory 启用无密码安全密钥登录到 Windows 10 设备](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-windows#requirements)。


### <a name="security-key-life-cycle"></a>安全密钥生命周期

利用安全密钥，你可以访问你的资源，你应该计划对这些物理设备进行管理。

1. **密钥分发**：规划如何为你的组织设置密钥。 您可能有一个集中的设置过程或允许最终用户购买 FIDO 2.0 兼容的密钥。
1. **密钥激活**：最终用户必须自行激活安全密钥。 最终用户在[https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)上注册其安全密钥，并在首次使用时启用第二因素（PIN 或生物识别）。
1. **禁用密钥**：尽管安全密钥功能处于预览阶段，但管理员无法从用户帐户中删除密钥。 用户必须将其删除。 如果密钥丢失或被盗：
   1. 从任何启用了无密码 authentication 的组中删除用户。
   1. 验证它们已删除密钥作为身份验证方法。
   1. 发出新密钥。 **密钥替换**：用户可以同时启用两个安全密钥。 替换安全密钥时，请确保用户还删除了要替换的密钥。

### <a name="enable-windows-10-support"></a>启用 Windows 10 支持

如果使用 FIDO2 安全密钥启用 Windows 10 登录，则需要在 Windows 10 中启用凭据提供程序功能。 选择以下之一：

- [使用 Intune 启用凭据提供程序](howto-authentication-passwordless-security-key-windows.md#enable-with-intune)
   - 建议使用 Intune 部署。
- [启用带有预配包的凭据提供程序](howto-authentication-passwordless-security-key-windows.md#enable-with-a-provisioning-package)
   - 如果无法部署 Intune，则管理员必须在每台计算机上部署包以启用凭据提供程序功能。 可以通过以下选项之一来执行包安装：
      - 组策略或 Configuration Manager
      - Windows 10 计算机上的本地安装
- [使用组策略启用凭据提供程序](howto-authentication-passwordless-security-key-windows.md#enable-with-group-policy)
   - 仅支持混合 Azure AD 连接设备。

#### <a name="enable-on-premises-integration"></a>启用本地集成

若要启用对本地资源的访问，请执行以下步骤以[启用无密码安全密钥登录到本地资源（预览版）](howto-authentication-passwordless-security-key-on-premises.md)。

> [!IMPORTANT]
> 还必须完成所有混合 Azure AD 联接的设备的这些步骤，才能利用 Windows 10 登录的 FIDO2 安全密钥。

### <a name="register-security-keys"></a>注册安全密钥

用户必须在他们的每个 Azure Active Directory 联接的 Windows 10 计算机上注册其安全密钥。

有关详细信息，请参阅[用户注册和管理 FIDO2 安全密钥](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys)。


## <a name="plan-auditing-security-and-testing"></a>规划审核、安全和测试
规划满足您的组织和符合性框架的审核是您的部署的重要组成部分。

### <a name="auditing-passwordless"></a>审核无密码

Azure AD 具有提供技术和业务见解的报表。 让你的业务和技术应用程序所有者根据你的组织的要求，为其创建所有权并使用这些报表。

在 Azure Active Directory 门户中的 "**身份验证**方法" 部分，管理员可以在其中启用和管理无密码凭据的设置。

Azure AD 将条目添加到审核日志中：

- 管理员在 "身份验证方法" 部分中进行更改。
- 用户在 Azure Active Directory 中对其凭据进行任何类型的更改。

下表提供了一些典型报表方案示例：

|   | 管理风险 | 提高工作效率 | 管理和符合性 |
| --- | --- | --- | --- |
| **报表类型** | 身份验证方法-注册了用于合并安全注册的用户 | 身份验证方法–注册了应用通知的用户 | 登录：查看谁正在访问租户以及如何 |
| **可能的操作** | 尚未注册的目标用户 | 推动采用 Microsoft Authenticator 的应用程序或安全密钥 | 撤消对管理员的访问权限或强制执行其他安全策略 |

**Azure AD 将大多数审核数据保留30天**，并通过 Azure 管理门户或 API 提供数据以下载到分析系统。 如果需要更长的保留期，请在[Azure Sentinel](../../sentinel/connect-azure-active-directory.md)、Splunk 或 sumo logic 逻辑等 SIEM 工具中导出和使用日志。 [了解有关查看访问和使用情况报告的详细信息](../reports-monitoring/overview-reports.md)。

用户可以通过导航到[https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)来注册和管理其凭据。 此链接将用户定向到通过组合 SSPR/多重身份验证注册体验启用的最终用户凭据管理体验。 Azure AD 日志注册 FIDO2 安全设备，并将其更改为用户的身份验证方法。

### <a name="plan-security"></a>规划安全性
作为此推出计划的一部分，Microsoft 建议为所有特权管理员帐户启用无密码 authentication。

当用户启用或禁用安全密钥上的帐户，或重置其 Windows 10 计算机上的安全密钥的第二个因素时，会向安全日志中添加一个条目，并在以下事件 Id 下添加条目： *4670*和*5382*。

### <a name="plan-testing"></a>规划测试

在测试方案和采用时，部署的每个阶段都应确保结果与预期结果相同。

#### <a name="testing-the-microsoft-authenticator-app"></a>测试 Microsoft Authenticator 应用

下面是 Microsoft Authenticator 应用进行无密码身份验证的示例测试案例：

| 场景 | 预期结果 |
| --- | --- |
| 用户可以注册 Microsoft Authenticator 应用 | 用户可以从 aka.ms/mysecurityinfo 注册应用程序 |
| 用户可以启用手机登录 | 为工作帐户配置的电话登录 |
| 用户可以使用手机登录访问应用 | 用户通过手机登录流并进入应用程序。 |
| 通过在 Azure Active Directory 门户中的 "身份验证方法" 屏幕上关闭 Microsoft Authenticator 无密码登录来测试回滚手机登录注册 | 以前启用的用户无法使用 Microsoft Authenticator 中的无密码登录。 |
| 从 Microsoft Authenticator 应用中删除手机登录 | Microsoft Authenticator 上的工作帐户不再可用 |

#### <a name="testing-security-keys"></a>测试安全密钥

下面是用于对安全密钥进行无密码身份验证的示例测试案例。

**无密码 FIDO 登录到 Azure Active Directory 加入 Windows 10 设备**

| 场景 | 预期结果 |
| --- | --- |
| 用户可以注册 FIDO2 设备（1809） | 用户可以使用 at > 帐户 > 登录选项 > 安全密钥注册 FIDO2 设备 |
| 用户可以重置 FIDO2 设备（1809） | 用户可以使用制造商软件重置 FIDO2 设备 |
| 用户可以通过 FIDO2 设备登录（1809） | 用户可以从登录窗口中选择 "安全密钥"，并成功登录。 |
| 用户可以注册 FIDO2 设备（1903） | 用户可以在设置 > 帐户中注册 FIDO2 设备 > 登录选项 > 安全密钥 |
| 用户可以重置 FIDO2 设备（1903） | 用户可以在设置 > 帐户 > 登录选项 > 安全密钥中重置 FIDO2 设备 |
| 用户可以通过 FIDO2 设备登录（1903） | 用户可以从登录窗口中选择 "安全密钥"，并成功登录。 |

**无密码 FIDO 登录到 Azure AD web 应用**

| 场景 | 预期结果 |
| --- | --- |
| 用户可以使用 Microsoft Edge 在 aka.ms/mysecurityinfo 上注册 FIDO2 设备 | 注册应成功 |
| 用户可以使用 Firefox 在 aka.ms/mysecurityinfo 上注册 FIDO2 设备 | 注册应成功 |
| 用户可以使用 Microsoft Edge 通过 FIDO2 设备登录到 OneDrive online | 登录应成功 |
| 用户可以使用 Firefox 使用 FIDO2 设备登录到 OneDrive online | 登录应成功 |
| 通过在 Azure Active Directory 门户的 "身份验证方法" 窗口中关闭 FIDO2 安全密钥来测试回滚 FIDO2 设备注册 | 系统将提示用户使用其安全密钥登录。 用户将成功登录，并显示错误： "你的公司策略要求你使用其他方法登录"。 然后，用户应能够选择其他方法并成功登录。 关闭窗口并再次登录，以验证它们是否看不到相同的错误消息。 |

### <a name="plan-for-rollback"></a>规划回滚

尽管无密码 authentication 是一项轻型功能，对最终用户的影响最小，但可能需要回滚。

回滚要求管理员登录到 Azure Active Directory 门户，选择所需的强身份验证方法，并将 "启用" 选项更改为 "**否**"。 此过程将关闭所有用户的无密码功能。

在下一次登录时，系统会提示已注册 FIDO2 安全设备的用户使用该安全设备，并看到以下错误：

![选择另一种登录方式](./media/howto-authentication-passwordless-deployment/passwordless-choose-sign-in.png)

## <a name="deploy-and-troubleshoot-passwordless-authentication"></a>部署无密码 authentication 并对其进行故障排除

按照下面与所选方法一致的步骤进行操作。

### <a name="required-administrative-roles"></a>必需的管理角色

| Azure AD 角色 | 说明 |
| --- | --- |
| 全局管理员角色|可实现组合注册体验的最小特权角色。 |
| 身份验证管理员 | 最小特权角色可以实现和管理身份验证方法。 |
| 用户 | 用于在设备上配置验证器应用的最小特权角色，或用于注册 web 或 Windows 10 登录的安全密钥设备。 |

### <a name="deploy-phone-sign-in-with-the-microsoft-authenticator-app"></a>部署 Microsoft Authenticator 应用的手机登录

按照文章[使用 Microsoft Authenticator 应用启用无密码登录](howto-authentication-passwordless-phone.md)一文中的步骤，将 Microsoft Authenticator 应用程序启用为组织中的无密码身份验证方法。

### <a name="deploy-fido2-security-key-sign-in"></a>部署 FIDO2 安全密钥登录

按照文章为[Azure AD 启用无密码 security key 登录](howto-authentication-passwordless-security-key.md)一文中的步骤，将 FIDO2 安全密钥启用为无密码身份验证方法。

### <a name="troubleshoot-phone-sign-in"></a>电话登录疑难解答

| 场景 | 解决方案 |
| --- | --- |
| 用户无法执行合并注册。 | 确保启用了[组合注册](concept-registration-mfa-sspr-combined.md)。 |
| 用户无法启用手机登录验证器应用。 | 确保用户处于部署范围。 |
| 用户不在无密码 authentication 的范围内，但会出现无密码登录选项，而不能完成。 | 如果用户在创建策略之前在应用程序中启用了手机登录，则会发生这种情况。 <br> *若要启用登录*：请将用户添加到启用了无密码登录的用户的作用域中。 <br> *阻止登录*：让用户从该应用程序中删除其凭据。 |

### <a name="troubleshoot-security-key-sign-in"></a>安全密钥登录疑难解答

| 场景 | 解决方案 |
| --- | --- |
| 用户无法执行合并注册。 | 确保启用了[组合注册](concept-registration-mfa-sspr-combined.md)。 |
| 用户无法在其[安全设置](https://aka.ms/mysecurityinfo)中添加安全密钥。 | 确保启用[安全密钥](howto-authentication-passwordless-security-key.md)。 |
| 用户无法在 Windows 10 登录选项中添加安全密钥。 | [确保 Windows 登录时的安全密钥](howto-authentication-passwordless-enable.md) |
| **错误消息**：我们检测到此浏览器或操作系统不支持 FIDO2 安全密钥。 | 无密码 FIDO2 security 设备只能在 Windows 10 版本1809或更高版本上受支持的浏览器（Microsoft Edge，Firefox 版本67）中注册。 |
| **错误消息**：你的公司策略要求你使用其他方法登录。 | 不确定租户中已启用安全密钥。 |
| 用户无法在 Windows 10 版本1809上管理我的安全密钥 | 版本1809要求你使用 FIDO2 密钥供应商提供的安全密钥管理软件。 请与供应商联系以获得支持。 |
| 我认为我的 FIDO2 安全密钥可能有问题，如何对其进行测试。 | 导航到 " [https://webauthntest.azurewebsites.net/](https://webauthntest.azurewebsites.net/)"，输入测试帐户的凭据，插入可疑安全密钥，选择屏幕右上角的 " **+** " 按钮，单击 "创建"，然后执行创建过程。 如果此方案失败，则设备可能已损坏。 |

## <a name="next-steps"></a>后续步骤

- [启用用于 Azure AD 登录的无密码安全密钥](howto-authentication-passwordless-security-key.md)
- [启用 Microsoft Authenticator 应用的无密码登录](howto-authentication-passwordless-phone.md)
- [详细了解身份验证方法用法 & insights](howto-authentication-methods-usage-insights.md)

