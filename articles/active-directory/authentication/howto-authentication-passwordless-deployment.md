---
title: 使用 Azure AD 规划无密码身份验证部署
description: 了解如何规划和部署 Azure 活动目录无密码身份验证实现
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
ms.openlocfilehash: e3ed549e51b911452bca7d4d4a16c7ef45594a8f
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81451425"
---
# <a name="plan-a-passwordless-authentication-deployment-in-azure-active-directory"></a>在 Azure 活动目录中规划无密码身份验证部署

> [!NOTE]
> 要创建此部署计划的脱机版本，请使用浏览器的"打印到 PDF"功能。

大多数网络攻击都从受攻击的用户名和密码开始。 组织试图通过要求用户使用以下方法之一来应对威胁：

- 长密码
- 复杂密码
- 频繁更改密码
- 多重身份验证 (MFA)

微软[的研究表明，](https://aka.ms/passwordguidance)这些努力惹恼了用户，并推高了支持成本。 有关详细信息，请参阅您的[Pa$$word并不重要](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)。

### <a name="benefits-of-passwordless-authentication"></a>无密码身份验证的好处

- **加强安全**。 删除密码作为攻击面，从而降低网络钓鱼和密码喷射攻击的风险。
-  **更好的用户体验**。 为用户提供从任何位置访问数据的便捷方式。 在移动时轻松访问应用程序和服务，如 Outlook、OneDrive 或 Office。
-  **可靠的见解**。 通过强大的日志记录和审核，深入了解用户无密码活动。

密码后，密码将被您拥有的东西以及您知道的东西替换。 例如，适用于企业的 Windows Hello 可以使用面部或指纹等生物识别手势，或者不使用网络传输的设备特定 PIN。

## <a name="passwordless-authentication-methods"></a>无密码身份验证方法
Microsoft 提供了三个无密码身份验证选项，涵盖许多方案。 这些方法可以串联使用：

- [适用于"企业"的 Windows](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless)最适合其专用 Windows 计算机上的用户。
- 使用[FIDO2 安全密钥](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless)的安全密钥登录对于登录到共享计算机（如自助服务终端）的用户、在电话使用受限的情况下以及高度特权标识的用户特别有用。
- 使用[Microsoft 身份验证器应用](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless)输入电话对于向使用移动设备的用户提供无密码选项非常有用。 身份验证器应用允许用户登录到任何平台或浏览器，将任何 iOS 或 Android 手机转换为强大的无密码凭据。 用户登录时，会收到手机的通知，将屏幕上显示的号码与手机上的号码匹配，然后使用其生物识别数据或 PIN 进行确认。

### <a name="passwordless-authentication-scenarios"></a>无密码身份验证方案

微软的无密码身份验证方法支持不同的方案。 考虑组织需求、先决条件以及每个身份验证方法的功能，以选择无密码身份验证策略。 我们建议每个使用 Windows 10 设备的组织都使用适用于企业的 Windows Hello。 然后，添加电话登录（使用 Microsoft 身份验证器应用）或安全密钥以用于其他方案。

| 方案 | 电话身份验证 | 安全密钥 | Windows Hello for Business |
| --- | --- | --- | --- |
| **计算机登录**： <br> 从分配的 Windows 10 设备 | **否** | **是** <br> 使用生物识别、PIN | **是**<br>带生物识别和/或 PIN |
| **计算机登录**： <br> 从共享 Windows 10 设备 | **否** | **是** <br> 使用生物识别、PIN  | **否** |
| **Web 应用登录**： <br>来自用户专用计算机 | **是** | **是** <br> 计算机登录启用了应用的单一登录 | **是**<br> 计算机登录启用了应用的单一登录 |
| **Web 应用登录**： <br> 从移动或非窗口设备 | **是** | **否** | **否** |
| **计算机登录**： <br> 非视窗计算机 | **否** | **否** | **否** |

有关为组织选择最佳方法的信息，请参阅[决定无密码方法](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless#choose-a-passwordless-method)。

## <a name="prerequisites"></a>先决条件

在开始无密码部署之前，组织必须满足以下先决条件：

| 先决条件 | Authenticator 应用 | FIDO2 安全密钥 |
| --- | --- | --- |
| 启用[Azure 多重身份验证和自助服务密码重置 （SSPR） 的组合注册](howto-registration-mfa-sspr-combined.md) | √ | √ |
| [用户可以执行 Azure 多重身份验证](howto-mfa-getstarted.md) | √ | √ |
| [用户已注册 Azure 多重身份验证和 SSPR](howto-registration-mfa-sspr-combined.md) | √ | √ |
| [用户已将其移动设备注册到 Azure 活动目录](../devices/overview.md) | √ |   |
| Windows 10 版本 1809 或更高版本使用支持的浏览器，如微软边缘或 Mozilla Firefox <br> （版本 67 或更高）。 <br> *微软推荐版本1903或更高版本为本机支持*。 |   | √ |
| 兼容的 FIDO2 安全密钥。 确保您使用的是经过 Microsoft[测试和验证的](howto-authentication-passwordless-enable.md)FIDO2 安全设备或其他兼容的 FIDO2 安全设备。 |   | √ |

### <a name="prerequisites-for-windows-hello-for-business"></a>企业 Windows Hello 的先决条件

Windows Hello 的先决条件在很大程度上取决于您是在本地配置、混合配置还是仅云配置中部署。 有关详细信息，请参阅[适用于企业的 Windows Hello 的先决条件的完整列表](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)。

### <a name="azure-multi-factor-authentication"></a>Azure 多重身份验证

用户将其无密码方法注册为 Azure 多重身份验证注册流的一部分。 在某些情况下，使用用户名和密码以及另一个已注册方法的多重身份验证可用作回退，以防在某些情况下无法使用其电话或安全密钥。

### <a name="licensing"></a>授权 
无密码身份验证没有额外费用，尽管某些先决条件可能需要高级订阅。 有关[Azure 活动目录许可页](https://azure.microsoft.com/pricing/details/active-directory/)中的详细信息和许可信息。 

## <a name="develop-a-plan"></a>制定计划

考虑您的业务需要和每种身份验证方法的用例。 然后选择最适合您需求的方法。

### <a name="use-cases"></a>用例

下表概述了在此项目期间要实现的用例。

| 区域 | 说明 |
| --- | --- |
| **访问** | 无密码登录可从公司网络内外的公司或个人设备获得。 |
| **审核** | 使用数据可供管理员近乎实时审核。 <br> 使用数据至少每 29 天下载一次到公司系统中，或者使用 SIEM 工具。 |
| **调控** | 定义和监视用户分配到适当身份验证方法和相关组的生命周期。 |
| **安全性** | 通过用户和组分配控制对适当身份验证方法的访问。 <br> 只有经过授权的用户才能使用无密码登录。 |
| **性能** | 记录和监视访问分配传播时间线。 <br> 为了便于使用，测量登录时间。 |
| **用户体验** | 用户知道移动兼容性。 <br> 用户可以配置身份验证器应用无密码登录。 |
| **支持** | 用户知道如何查找对无密码登录问题的支持。 |

### <a name="engage-the-right-stakeholders"></a>吸引适当的利益干系人

当技术项目失败时，通常是由于对影响、结果和责任的期望不匹配。 为了避免这些陷阱，[请确保您与合适的利益相关者接洽](../fundamentals/active-directory-deployment-plans.md#include-the-right-stakeholders)，并确保项目中的利益相关者角色得到充分理解。

### <a name="plan-communications"></a>规划沟通

沟通对于任何新服务的成功至关重要。 主动沟通用户体验将如何变化、何时更改，以及遇到问题时如何获得支持。

您与最终用户的通信应包括以下信息：

- [启用组合安全注册体验](howto-authentication-passwordless-phone.md)
- [下载微软身份验证器应用程序](../user-help/user-help-auth-app-download-install.md)
- [在 Microsoft 身份验证器应用程序中注册](howto-authentication-passwordless-phone.md)
- [使用手机登录](../user-help/user-help-auth-app-sign-in.md)

Microsoft 提供多重身份验证[通信模板](https://aka.ms/mfatemplates)、自助服务密码重置 （SSPR）[通信模板](https://www.microsoft.com/download/details.aspx?id=56768)和[最终用户文档](../user-help/security-info-setup-signin.md)，以帮助起草通信。 您可以通过选择该页面上[https://myprofile.microsoft.com](https://myprofile.microsoft.com/)**的安全信息**链接将用户发送到直接注册。

### <a name="plan-to-pilot"></a>计划进行试点

部署无密码身份验证时，应首先启用一个或多个试验组。 您可以为此专门[创建组](../fundamentals/active-directory-groups-create-azure-portal.md)。 将将参加试验的用户添加到组中。 然后，为所选组启用新的无密码身份验证方法。

组可以从本地目录或 Azure AD 同步。 对试用结果感到满意后，即可为所有用户打开无密码身份验证。

请参阅部署计划页面上[的试用](https://aka.ms/deploymentplans)版最佳实践。

## <a name="plan-passwordless-authentication-with-the-microsoft-authenticator-app"></a>使用 Microsoft 身份验证器应用规划无密码身份验证

微软身份验证器应用程序是从谷歌播放或苹果应用商店免费下载。 [了解有关下载微软身份验证器应用程序的更多内容](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6)。 让用户下载 Microsoft 身份验证器应用。 并按照说明启用电话登录。 

它将任何 iOS 或 Android 手机转换为强大的无密码凭据。 用户登录到任何平台或浏览器时，将通知通知到他们的手机，将屏幕上显示的号码与手机上的数字匹配，然后使用生物识别或 PIN 进行确认。 [查看有关 Microsoft 身份验证器应用如何工作的详细信息](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless#microsoft-authenticator-app)。

![使用身份验证器应用登录](./media/howto-authentication-passwordless-deployment/passwordless-dp-sign-in.png)

### <a name="technical-considerations-for-the-microsoft-authenticator-app"></a>微软身份验证器应用的技术注意事项

**AD FS 集成**- 当用户启用 Microsoft 身份验证器无密码凭据时，该用户的身份验证默认发送通知以进行审批。 混合租户中的用户将被禁止被定向到 ADFS 进行登录，除非他们选择"改用您的密码"。 此过程还绕过任何本地条件访问策略和传递身份验证流。 但是，如果指定*了login_hint，* 则用户将转发到 ADFS 并绕过使用无密码凭据的选项。

**Azure 多重身份验证服务器**- 通过组织的本地 Azure MFA 服务器启用多重身份验证的最终用户可以创建和使用单个无密码电话登录凭据。 如果用户尝试使用凭据升级 Microsoft 身份验证器的多个安装（5 个或更多），则此更改可能会导致错误。

**设备注册**- 要使用身份验证器应用进行无密码身份验证，设备必须在 Azure AD 租户中注册，并且不能是共享设备。 设备只能在单个租户中注册。 此限制意味着使用身份验证器应用的电话登录仅支持一个工作或学校帐户。

## <a name="plan-passwordless-authentication-with-fido2-security-keys"></a>使用 FIDO2 安全密钥规划无密码身份验证
有三种类型的无密码登录部署可用于安全密钥：

-    受支持的浏览器上的 Azure 活动目录 Web 应用
-    Azure 活动目录加入 Windows 10 设备
-    混合 Azure 活动目录加入 Windows 10 设备（预览）
     -    提供对基于云的和内部资源的访问。 有关访问本地资源的详细信息，请参阅[使用 FIDOP2 密钥访问本地资源的 SSO](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-on-premises)

您必须启用**兼容的 FIDO2 安全密钥**。 微软宣布[与FIDO2主要供应商建立关键合作关系](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Microsoft-passwordless-partnership-leads-to-innovation-and-great/ba-p/566493)。

**对于 Azure AD Web 应用和 Azure AD Windows 联接的设备**：

-    Windows 10 版本 1809 或更高版本使用受支持的浏览器，如 Microsoft 边缘或 Mozilla Firefox（版本 67 或更高版本）。 
-    Windows 10 版本 1809 支持 FIDO2 登录，可能需要部署 FIDO2 密钥制造商的软件。 我们建议您使用版本 1903 或更高版本。 

**对于混合 Azure 活动目录域加入设备**： 
-    Windows 10 内部信息内部版本 18945 或更高版本
-    运行 Windows Server 2016 或 2019 的完全修补域服务器。
-    最新版本的 Azure AD 连接

有关要求的完整列表，请参阅使用[Azure 活动目录将无密码安全密钥登录到 Windows 10 设备](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-windows#requirements)。


### <a name="security-key-life-cycle"></a>安全密钥生命周期

安全密钥允许访问您的资源，并且应规划这些物理设备的管理。

1. **密钥分发**：计划如何为组织预配密钥。 您可能有一个集中式预配过程，或者允许最终用户购买 FIDO 2.0 兼容密钥。
1. **密钥激活**：最终用户必须自行激活安全密钥。 最终用户在首次使用时[https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)注册其安全密钥并启用第二个因素（PIN 或生物识别）。
1. **禁用密钥**：虽然安全密钥功能处于预览阶段，但管理员无法从用户帐户中删除密钥。 用户必须将其删除。 如果钥匙丢失或被盗：
   1. 从启用无密码身份验证的任何组中删除用户。
   1. 验证他们已删除密钥作为身份验证方法。
   1. 颁发新密钥。 **密钥替换**：用户可以同时启用两个安全密钥。 更换安全密钥时，请确保用户也删除了要更换的密钥。

### <a name="enable-windows-10-support"></a>启用 Windows 10 支持

使用 FIDO2 安全密钥启用 Windows 10 登录需要启用 Windows 10 中的凭据提供程序功能。 选择以下选项之一：

- [使用 Intune 启用凭据提供程序](howto-authentication-passwordless-security-key-windows.md#enable-with-intune)
   - 建议使用 Intune 部署。
- [使用预配包启用凭据提供程序](howto-authentication-passwordless-security-key-windows.md#enable-with-a-provisioning-package)
   - 如果无法进行 Intune 部署，管理员必须在每台计算机上部署一个包以启用凭据提供程序功能。 包安装可以通过以下选项之一执行：
      - 组策略或配置管理器
      - 在 Windows 10 计算机上安装本地
- [使用组策略启用凭据提供程序](howto-authentication-passwordless-security-key-windows.md#enable-with-group-policy)
   - 仅适用于混合 Azure AD 联接设备。

#### <a name="enable-on-premises-integration"></a>启用本地集成

要启用对本地资源的访问，请按照步骤[将无密码安全密钥登录到本地资源（预览）。](howto-authentication-passwordless-security-key-on-premises.md)

> [!IMPORTANT]
> 对于任何混合的 Azure AD 加入设备，还必须完成这些步骤，以便使用 FIDO2 安全密钥进行 Windows 10 登录。

### <a name="register-security-keys"></a>注册安全密钥

用户必须在其 Azure 活动目录联接的 Windows 10 计算机上注册其安全密钥。

有关详细信息，请参阅[FIDO2 安全密钥的用户注册和管理](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys)。


## <a name="plan-auditing-security-and-testing"></a>规划审核、安全性和测试
规划符合组织和合规性框架的审核是部署的重要组成部分。

### <a name="auditing-passwordless"></a>审核无密码

Azure AD 具有提供技术和业务见解的报告。 让您的业务和技术应用程序所有者根据组织的要求对这些报告拥有和使用。

Azure 活动目录门户中的**身份验证**方法部分是管理员可以启用和管理无密码凭据设置的位置。

Azure AD 在以下时间将条目添加到审核日志：

- 管理员在"身份验证方法"部分进行更改。
- 用户对 Azure 活动目录中的凭据进行任何类型的更改。

下表提供了典型报告方案的一些示例：

|   | 管理风险 | 提高生产率 | 管理和符合性 |
| --- | --- | --- | --- |
| **报表类型** | 身份验证方法 - 注册为联合安全注册的用户 | 身份验证方法 – 注册应用通知的用户 | 登录：查看访问租户的人员以及如何访问租户 |
| **潜在行动** | 目标用户尚未注册 | 推动采用 Microsoft 身份验证器应用或安全密钥 | 撤消对管理员的访问或强制实施其他安全策略 |

**Azure AD 将大多数审核数据保留 30 天**，并通过 Azure 管理门户或 API 提供数据，以便下载到分析系统中。 如果需要更长的保留时间，则导出和使用 SIEM 工具中的日志（如 Azure Sentinel、Splunk 或相扑逻辑）。 [Azure Sentinel](../../sentinel/connect-azure-active-directory.md) [详细了解查看访问和使用报告](../reports-monitoring/overview-reports.md)。

用户可以通过导航到[https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)来注册和管理其凭据。 此链接将用户定向到通过组合 SSPR/多重身份验证注册体验启用的最终用户凭据管理体验。 Azure AD 记录 FIDO2 安全设备的注册，以及用户对身份验证方法的更改。

### <a name="plan-security"></a>规划安全性
作为此推出计划的一部分，Microsoft 建议为所有特权管理员帐户启用无密码身份验证。

当用户在安全密钥上启用或禁用帐户，或重置其 Windows 10 计算机上安全密钥的第二个因素时，将一个条目添加到安全日志中，并在以下事件 ID 下 *：4670*和*5382*。

### <a name="plan-testing"></a>计划测试

在部署的每个阶段测试方案和采用时，确保结果按预期进行。

#### <a name="testing-the-microsoft-authenticator-app"></a>测试微软身份验证器应用

以下是使用 Microsoft 身份验证器应用进行无密码身份验证的示例测试用例：

| 方案 | 预期结果 |
| --- | --- |
| 用户可以注册微软身份验证器应用程序 | 用户可以从aka.ms/mysecurityinfo注册应用 |
| 用户可以启用电话登录 | 为工作帐户配置的电话登录 |
| 用户可以访问带电话登录的应用 | 用户通过电话登录流到达应用程序。 |
| 通过在 Azure 活动目录门户中的身份验证方法屏幕中关闭 Microsoft 身份验证器无密码登录，测试回滚电话登录注册 | 以前启用的用户无法使用来自 Microsoft 身份验证器的无密码登录。 |
| 从 Microsoft 身份验证器应用中删除手机登录 | 工作帐户在 Microsoft 身份验证器上不再可用 |

#### <a name="testing-security-keys"></a>测试安全密钥

以下是使用安全密钥进行无密码身份验证的示例测试用例。

**无密码 FIDO 登录到 Azure 活动目录加入的 Windows 10 设备**

| 方案 | 预期结果 |
| --- | --- |
| 用户可以注册 FIDO2 设备 （1809） | 用户可以在"设置>帐户注册 FIDO2 设备>登录选项>安全密钥 |
| 用户可以重置 FIDO2 设备 （1809） | 用户可以使用制造商软件重置 FIDO2 设备 |
| 用户可以使用 FIDO2 设备登录 （1809） | 用户可以从登录窗口中选择安全密钥，并成功登录。 |
| 用户可以注册FIDO2设备 （1903） | 用户可以在"设置>帐户注册 FIDO2 设备>登录选项>安全密钥 |
| 用户可以重置 FIDO2 设备 （1903） | 用户可以在设置>帐户重置 FIDO2 设备>登录选项>安全密钥 |
| 用户可以使用 FIDO2 设备登录 （1903） | 用户可以从登录窗口中选择安全密钥，并成功登录。 |

**无密码 FIDO 登录到 Azure AD Web 应用**

| 方案 | 预期结果 |
| --- | --- |
| 用户可以使用微软边缘在aka.ms/mysecurityinfo注册FIDO2设备 | 注册应成功 |
| 用户可以在aka.ms/mysecurityinfo使用火狐注册FIDO2设备 | 注册应成功 |
| 用户可以使用 Microsoft 边缘使用 FIDO2 设备在线登录到 OneDrive | 登录应成功 |
| 用户可以使用 Firefox 使用 FIDO2 设备在线登录到 OneDrive | 登录应成功 |
| 通过在 Azure 活动目录门户中的身份验证方法窗口中关闭 FIDO2 安全密钥，测试回滚 FIDO2 设备注册 | 系统将提示用户使用安全密钥登录。 用户将成功登录，并显示错误："您的公司策略要求您使用其他方法登录"。 然后，用户应该能够选择其他方法并成功登录。 关闭窗口并再次登录以验证它们未看到相同的错误消息。 |

### <a name="plan-for-rollback"></a>规划回滚

尽管无密码身份验证是一项轻量级功能，对最终用户的影响最小，但可能需要回滚。

回滚需要管理员登录到 Azure 活动目录门户，选择所需的强身份验证方法，并将启用选项更改为 **"否**"。 此过程将关闭所有用户的无密码功能。

已注册 FIDO2 安全设备的用户在下次登录时将提示他们使用安全设备，然后看到以下错误：

![选择其他登录方式](./media/howto-authentication-passwordless-deployment/passwordless-choose-sign-in.png)

## <a name="deploy-and-troubleshoot-passwordless-authentication"></a>部署和无密码身份验证并排除故障故障

按照以下步骤与所选方法对齐。

### <a name="required-administrative-roles"></a>所需的管理角色

| Azure AD 角色 | 说明 |
| --- | --- |
| 全局管理员角色|能够实现合并注册体验的最小特权角色。 |
| 身份验证管理员 | 能够实现和管理身份验证方法的最小特权角色。 |
| 用户 | 在设备上配置身份验证器应用或为 Web 或 Windows 10 登录注册安全密钥设备的特权角色。 |

### <a name="deploy-phone-sign-in-with-the-microsoft-authenticator-app"></a>使用 Microsoft 身份验证器应用部署电话登录

按照本文中的步骤，使用[Microsoft 身份验证器应用启用无密码登录](howto-authentication-passwordless-phone.md)，使 Microsoft 身份验证器应用成为组织中的无密码身份验证方法。

### <a name="deploy-fido2-security-key-sign-in"></a>部署 FIDO2 安全密钥登录

按照本文中的步骤，为[Azure AD 启用无密码安全密钥登录](howto-authentication-passwordless-security-key.md)，将 FIDO2 安全密钥作为无密码身份验证方法启用。

### <a name="troubleshoot-phone-sign-in"></a>排除电话登录故障

| 方案 | 解决方案 |
| --- | --- |
| 用户无法执行合并注册。 | 确保启用[合并注册](concept-registration-mfa-sspr-combined.md)。 |
| 用户无法启用电话登录身份验证器应用。 | 确保用户处于部署范围。 |
| 用户不在无密码身份验证范围内，但显示无密码登录选项，他们无法完成。 | 当用户在创建策略之前在应用程序中启用了电话登录时，将发生此情况。 <br> *要启用登录*：将用户添加到启用无密码登录的用户范围。 <br> *阻止登录*：让用户从该应用程序中删除其凭据。 |

### <a name="troubleshoot-security-key-sign-in"></a>排除安全密钥登录的故障

| 方案 | 解决方案 |
| --- | --- |
| 用户无法执行合并注册。 | 确保启用[合并注册](concept-registration-mfa-sspr-combined.md)。 |
| 用户无法在其[安全设置](https://aka.ms/mysecurityinfo)中添加安全密钥。 | 确保启用[安全密钥](howto-authentication-passwordless-security-key.md)。 |
| 用户无法在 Windows 10 登录选项中添加安全密钥。 | [确保 Windows 登录的安全密钥](howto-authentication-passwordless-enable.md) |
| **错误消息**：我们发现此浏览器或操作系统不支持 FIDO2 安全密钥。 | 无密码的 FIDO2 安全设备只能在 Windows 10 版本 1809 或更高版本的受支持的浏览器（微软边缘、Firefox 版本 67）中注册。 |
| **错误消息**：您的公司策略要求您使用其他方法登录。 | 租户中启用了不确定的安全密钥。 |
| 用户无法在 Windows 10 版本 1809 上管理我的安全密钥 | 版本 1809 要求您使用 FIDO2 密钥供应商提供的安全密钥管理软件。 请与供应商联系以获取支持。 |
| 我认为我的 FIDO2 安全密钥可能有缺陷 - 如何测试它。 | 导航到[https://webauthntest.azurewebsites.net/](https://webauthntest.azurewebsites.net/)，输入测试帐户的凭据，插入可疑安全密钥，选择屏幕右上角的**+** 按钮，单击"创建"，然后完成创建过程。 如果此方案失败，您的设备可能有故障。 |

## <a name="next-steps"></a>后续步骤

- [为 Azure AD 的登录启用无密码安全密钥](howto-authentication-passwordless-security-key.md)
- [使用 Microsoft 身份验证器应用启用无密码登录](howto-authentication-passwordless-phone.md)
- [了解有关身份验证方法使用情况&见解详细信息](howto-authentication-methods-usage-insights.md)

