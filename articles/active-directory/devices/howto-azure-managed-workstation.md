---
title: 部署 Azure 托管的工作站的 Azure Active Directory
description: 了解如何部署 Azure 托管工作站的安全状态，以减少由于配置错误或者泄漏的漏洞的风险
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae7c823b9aea262556081354a108ac9509a284ab
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67110686"
---
# <a name="deploy-a-secure-workstation"></a>部署工作站的安全

现在，你已了解[保护工作站访问很重要的原因？](concept-azure-managed-workstation.md)就可以开始使用可用的工具部署过程。 本指南将使用定义配置文件创建从一开始更安全的工作站。

![安全的工作站的部署](./media/howto-azure-managed-workstation/deploying-secure-workstations.png)

在部署解决方案之前，必须选择要使用的配置文件。 请务必注意，可以应用所有所选配置文件并将移动到另一个通过分配基于你的要求的 Intune 中的配置文件。 可以在部署中，同时使用多个配置文件，然后将其分配使用标记的或组分配。

| 配置文件 | 低 | 增强版 | 高 | 专用 | 保护 | 隔离 |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Azure AD 中的用户 | 是 | 是 | 是 | 是 | 是 | 是 |
| Intune 托管 | 是 | 是 | 是 | 是 | 是 | 是 |
| Azure AD 注册设备 | 是 |  |  |  |  | |   |
| 已加入 Azure AD 的设备 |   | 是 | 是 | 是 | 是 | 是 |
| Intune 应用的安全基准 |   | 是 <br> （增强） | 是 <br> (HighSecurity) | 是 <br> (NCSC) | 是 <br> （安全） |  NA |
| 硬件满足安全 Windows 10 标准 |   | 是 | 是 | 是 | 是 | 是 |
| 启用 Microsoft Defender ATP |   | 是  | 是 | 是 | 是 | 是 |
| 删除的管理权限 |   |   | 是  | 是 | 是 | 是 |
| 使用 Microsoft Autopilot 部署 |   |   | 是  | 是 | 是 | 是 |
| 仅由 Intune 安装的应用 |   |   |   | 是 | 是 |是 |
| 限制为仅已批准列表的 Url |   |   |   | 是 | 是 |是 |
| Internet （入站/出站阻止） |   |   |   |  |  |是 |

## <a name="license-requirements"></a>许可要求

本指南中介绍的概念将假定 Microsoft 365 企业版 E5 或等效的 SKU。 可以使用较低的 Sku 实现一些本指南中的建议。 可以在上找到更多信息[Microsoft 365 企业版许可](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise)。

您可能想要配置[基于组的许可](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign)为用户自动预配许可证。

## <a name="azure-active-directory-configuration"></a>Azure Active Directory 配置

配置您的 Azure Active Directory (Azure AD) 目录中，将能够管理你的用户，这组、 和的管理员工作站的设备，需要启用标识服务和功能与[管理员帐户](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

当创建受保护的工作站管理员帐户时，你将到当前工作站公开帐户。 建议您此初始配置和从已知的安全设备的所有全局配置。 您可以考虑到的指导[防止恶意软件感染](https://docs.microsoft.com/windows/security/threat-protection/intelligence/prevent-malware-infection)以减少公开首先从攻击的首次体验的风险。

组织应要求多重身份验证，至少为其管理员。 请参阅[部署基于云的 MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)有关实现的指南。

### <a name="azure-ad-users-and-groups"></a>Azure AD 用户和组

从 Azure 门户中，浏览到**Azure Active Directory** > **用户** > **新用户**。 [创建安全工作站用户](https://docs.microsoft.com/Intune/quickstart-create-user)，谁将您的设备管理员。

* **名称**-安全工作站管理员
* **用户名称**- secure-ws-admin@identityitpro.com
* **目录角色** - **受限制的管理员**，然后选择以下管理角色
   * **Intune 管理员**
* **创建**

我们将创建两个组一个工作站的用户，一个用于工作站本身。 从 Azure 门户中，浏览到**Azure Active Directory** > **组** > **新组**

工作站的用户的第一个组。 您可能想要配置[基于组的许可](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign)中此组自动预配到用户的许可证的用户。

* **组类型**-安全
* **组名称**-保护工作站的用户
* **成员资格类型**-分配
* 将安全的工作站管理员用户添加到组
   * secure-ws-admin@identityitpro.com
* 可以添加要管理安全工作站添加到组的任何其他用户
* **创建**

第二个工作站的设备组。

* **组类型**-安全
* **组名称**-保护工作站
* **成员资格类型**-分配
* **创建**

### <a name="azure-ad-device-configuration"></a>Azure AD 设备配置

#### <a name="specify-who-can-join-devices-to-azure-ad"></a>指定谁可以将设备加入到 Azure AD

配置你的设备在 Active Directory 中设置以允许你管理的安全组，以将设备加入到你的域。 若要配置此设置从 Azure 门户，浏览到**Azure Active Directory** > **设备** > **设备设置**。 选择**Selected**下**用户可能会将设备加入到 Azure AD** ，然后选择"保护工作站用户"组。

#### <a name="removal-of-local-admin-rights"></a>删除的本地管理员权限

推出的一部分的 VIP、 DevOps 和安全级别的工作站的工作站用户将对其计算机没有管理员权限。 若要配置此设置从 Azure 门户，浏览到**Azure Active Directory** > **设备** > **设备设置**。 选择**无**下**其他本地管理员的 Azure ad 加入设备**。

#### <a name="require-multi-factor-authentication-to-join-devices"></a>需要多重身份验证才能加入设备

若要进一步加强到 Azure AD 加入设备的过程，浏览到**Azure Active Directory** > **设备** > **设备设置**选择**是**下**需要 Multi-factor Auth 才能加入设备**然后选择**保存**。

#### <a name="configure-mdm"></a>配置 MDM

从 Azure 门户中，浏览到**Azure Active Directory** > **移动性 （MDM 和 MAM）**  > **Microsoft Intune**。 更改该设置**MDM 用户作用域**到**所有**，然后选择**保存**因为我们将允许任何设备，若要在此方案中由 Intune 管理。 可一文中找到更多信息[Intune 快速入门：设置适用于 Windows 10 设备自动注册](https://docs.microsoft.com/Intune/quickstart-setup-auto-enrollment)。 我们将在以后的步骤中创建 Intune 配置和符合性策略。

#### <a name="azure-ad-conditional-access"></a>Azure AD 条件访问

Azure AD 条件访问可帮助在符合条件的设备上保留这些特权的管理任务。 我们已定义为成员用户**保护工作站用户**组将需要在登录到云的应用程序时执行多重身份验证。 我们将遵循的最佳实践指导原则，并从策略中排除了紧急访问帐户。 可以在文章中找到更多信息[在 Azure AD 中管理紧急访问帐户](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)

若要从 Azure 门户中配置条件性访问，请浏览到**Azure Active Directory** > **条件性访问** > **新策略**。

* **名称**-安全的设备所需的策略
* 分配
   * 用户和组 
      * 包括-**用户和组**-选择**保护工作站用户**前面创建的组
      * 中排除**用户和组**-选择你的组织的紧急访问帐户
   * 云应用 
      * 包括-**所有云应用**
* 访问控制
   * **授予**-选择**授予访问权限**单选按钮
      * **需要多重身份验证**
      * “要求将设备标记为合规” 
      * 为多个控件-**需要所有已选的控件**
* 启用策略-**上**

组织可以选择创建向块国家/地区的用户将访问公司资源的策略。 有关 IP 基于位置的条件性访问策略的详细信息可在文章[什么是 Azure Active Directory 条件访问中的位置条件？](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition)

## <a name="intune-configuration"></a>Intune 配置

### <a name="configure-enrollment-status"></a>配置注册状态

供应链管理中所述，确保安全的工作站受信任的全新设备，建议购买的设备将工厂设置为新设备时[Windows 10 专业版 S 模式](https://docs.microsoft.com/Windows/deployment/Windows-10-pro-in-s-mode)，这就限制了公开和在供应链管理的安全漏洞。 一旦您的供应商收到了设备，设备将不再从 S 模式下使用 Autopilot。 以下指南提供有关应用转换过程的详细信息。

我们想要确保在使用前完全配置的设备。 Intune 提供的一种手段**阻止设备，请使用安装的所有应用和配置文件之前**。 

1. 从**Azure 门户**导航到：
1. **Microsoft Intune** > **设备注册** > **Windows 注册** > **注册状态页 （预览版）**  > **默认** > **设置**。
1. 设置**显示应用程序配置文件安装进度**到**是**。
1. 设置**阻止设备，请使用安装的所有应用和配置文件之前**到**是**。

### <a name="create-an-autopilot-deployment-profile"></a>创建 Autopilot 部署配置文件

创建设备组之后，必须创建一个部署配置文件，以便你可以配置 Autopilot 设备。

1. 在 Intune Azure 门户中，选择**设备注册** > **Windows 注册** > **部署配置文件** >  **创建配置文件**。
1. 对于名称，输入**保护工作站部署配置文件**。 对于描述，输入**部署安全工作站**。
1. 集转换为是 Autopilot 为所有目标的设备。 此设置可确保列表中的所有设备获取向 Autopilot 部署服务都注册。  允许 48 小时内进行注册以便进行处理。
1. 有关部署模式下，选择**自部署 （预览）** 。 与用户设备注册，设备与此配置文件相关联。 注册设备需要用户凭据。
1. 在框中，为 Azure AD 加入**已加入 Azure AD**应选择而灰显状态。
1. 选择开箱体验 (OOBE)，配置以下选项和其他人设置为默认值，并选择的保留**确定**:
   1. 用户帐户类型：**标准**
1. 选择“创建”  以创建该配置文件。 现可用于向设备分配 Autopilot 部署配置文件。
1. 选择**分配** > **将分配给** > **所选组**
   1. **选择要包括的组**-保护工作站的用户

### <a name="configure-windows-update"></a>配置 Windows 更新

组织可以做的最重要事情之一是使 Windows 10 保持最新。 为了维护安全的状态中的 Windows 10，我们将部署更新通道来管理更新应用到工作站的发展。 此配置可在**Azure 门户** > **Microsoft Intune** > **软件更新** >  **Windows 10 更新圈**。

我们将**创建**新的更新通道，使用以下设置已更改的默认值。

* 名称- **Azure 托管的工作站更新**
* 服务频道- **Windows 预览体验-快**
* 质量更新延期期限 （天）- **3**
* 功能更新延迟期 （天）- **3**
* 自动更新行为-**自动安装并重启而无需最终用户控件**
* 暂停 Windows 更新-阻止用户**块**
* 需要用户的批准才能在工作时间-之外重新启动**所需**
* 允许用户重启 （参与重启）-**所需**
   * 自动重启后转换用户参与度的重新启动 （天数） – **3**
   * 暂停参与重启提醒 （天数） – **3**
   * 设置截止时间挂起重新启动 （天数） – **3**

单击**创建**然后在**分配**选项卡上添加**保护工作站**组作为包含的组。

有关 Windows 更新策略的其他信息可在[策略 CSP-更新](https://docs.microsoft.com/windows/client-management/mdm/policy-csp-update)

### <a name="windows-defender-atp-intune-integration"></a>Windows Defender ATP Intune 集成

Windows Defender ATP 和 Microsoft Intune 协同工作以帮助防止安全违规时，并帮助限制的漏洞影响。 功能将提供实时检测。 我们的部署大量审核和日志记录的终结点设备，还会提供 ATP。

若要在 Azure 门户中配置 Windows Defender ATP Intune 集成，浏览到**Microsoft Intune** > **设备符合性** > **Windows Defender ATP**.

1. 在步骤 1 下面**配置 Windows Defender ATP**，单击**将 Windows Defender ATP 连接 Windows Defender 安全中心中的 Microsoft Intune**。
1. 在 Windows Defender 安全中心：
   1. 选择**设置** > **高级功能**
   1. 有关**Microsoft Intune 连接**，选择**上**
   1. 选择**保存首选项**
1. 建立连接后，请返回到 Intune，然后单击**刷新**顶部。
1. 设置**连接的 Windows 设备版本 10.0.15063 及更高版本到 Windows Defender ATP**到**上**。
1. **保存**

可以在文章中找到更多信息[Windows Defender 高级威胁防护](https://docs.microsoft.com/Windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection)。

### <a name="completing-hardening-of-the-workstation-profile"></a>完成强化的工作站配置文件

若要成功完成解决方案的强化，下载并执行该脚本基于所需**配置文件级别**从下面的图表。

| 配置文件 | 下载位置 | 文件名 |
| --- | --- | --- |
| 低安全性 | 不适用 |  不适用 |
| 增强的安全性 | https://aka.ms/securedworkstationgit | 增强的工作站的 Windows10-(1809).ps1 |
| 高安全性  | https://aka.ms/securedworkstationgit | HighSecurityWorkstation-Windows10-(1809).ps1 |
| 专用 | https://github.com/pelarsen/IntunePowerShellAutomation | DeviceConfiguration_NCSC - Windows10 (1803) SecurityBaseline.ps1 |
| 专用法规遵从性 * | https://aka.ms/securedworkstationgit | DeviceCompliance_NCSC-Windows10(1803).ps1 |
| 保护 | https://aka.ms/securedworkstationgit | Secure-Workstation-Windows10-(1809)-SecurityBaseline.ps1 |

专用法规遵从性 * 是强制实施 NCSC Windows10 SecurityBaseline 中提供的专用的配置的脚本。

一旦选中的脚本成功执行，可以在 Microsoft Intune 中进行对配置文件和策略的更新。 增强和安全配置文件的脚本创建策略和配置文件，但您必须向其分配策略您**保护工作站**组。

* Intune 设备配置文件中由脚本创建可在**Azure 门户** > **Microsoft Intune** > **设备配置** > **配置文件**。
* 由脚本创建的 Intune 设备符合性策略可在**Azure 门户** > **Microsoft Intune** > **设备符合性**  > **策略**。

若要查看所做的更改还可以导出配置文件，并且将更改应用到导出文件 SECCON 中所述文档基于和其他强化的需要。

运行 Intune 数据导出脚本`DeviceConfiguration_Export.ps1`从[DeviceConfiguration GiuHub 存储库](https://github.com/microsoftgraph/powershell-intune-samples/tree/master/DeviceConfiguration)将提供的导出当前的所有现有 Intune 配置文件。

## <a name="additional-configurations-and-hardening-to-consider"></a>其他配置和强化考虑

提供的指导已启用的受保护的工作站，其他控件还应考虑，例如备用的浏览器访问，出站 HTTP 允许和阻止的网站和运行自定义 PowerShell 脚本的能力。

### <a name="restrictive-inbound-and-outbound-rules-in-firewall-configuration-service-provider-csp"></a>在防火墙配置服务提供程序 (CSP) 的入站和出站规则限制

入站和出站规则的其他管理可以更新以反映你允许和阻止的终结点。 在我们继续强化安全的工作站，我们将限制移到拒绝所有入站和出站为默认值，并添加允许的出站以反映公共和受信任的网站的站点。 防火墙配置服务提供程序的其他配置信息可以在文章中找到[防火墙 CSP](https://docs.microsoft.com/Windows/client-management/mdm/firewall-csp)。

默认受限制的建议如下：

* 拒绝所有入站
* 拒绝所有出站

Spamhaus 项目维护良好的列表，组织可以通过作为起始点，用于阻止站点称为[域块列表 （双）](https://www.spamhaus.org/dbl/)。

### <a name="managing-local-applications"></a>管理本地应用程序

删除本地应用程序，包括工作效率应用程序的删除将安全工作站于真正强化状态。 在本示例中，我们将将 Chrome 添加为默认浏览器并限制能够修改浏览器包括插件使用 Intune。

#### <a name="deploy-applications-using-intune"></a>使用 Intune 部署应用程序

在某些情况下，应用程序，如 Google Chrome 浏览器需要在受保护的工作站上。 下面的示例介绍如何向安全组中的设备安装 Chrome**保护工作站**之前创建。

1. 下载脱机安装程序[用于 Windows 64‑bit Chrome 捆绑包](https://cloud.google.com/chrome-enterprise/browser/download/)
1. 提取文件，并记下的位置`GoogleChromeStandaloneEnterprise64.msi`使用 Intune 进行安装
1. 在中**Azure 门户**浏览到**Microsoft Intune** > **客户端应用程序** > **应用** > **添加**
1. 下**应用类型**，选择**业务线**
1. 下**应用包文件**，选择`GoogleChromeStandaloneEnterprise64.msi`从提取的位置单击**确定**
1. 下**应用信息**，提供的说明和发布服务器，然后选择**确定**
1. 单击“添加” 
1. 上**分配**选择**适用于已注册的设备**下**分配类型**
1. 下**包括的组**，添加**保护工作站**前面创建的组
1. 单击**确定**然后**保存**

可以在其支持文章中找到配置 Chrome 设置的其他指导[使用 Microsoft Intune 管理 Chrome 浏览器](https://support.google.com/chrome/a/answer/9102677)。

#### <a name="configuring-the-company-portal-for-custom-apps"></a>配置自定义应用在公司门户

在安全模式下，安装应用程序将限制为 Intune 公司门户。 但是，安装门户需要访问 Microsoft Store。 在受保护的解决方案中，我们将在门户提供给所有设备使用公司门户的脱机模式。

安装 Intune 托管的副本[公司门户](https://docs.microsoft.com/Intune/store-apps-company-portal-app)将允许按需向受保护的工作站的用户推送的其他工具的功能。

某些组织可能需要安装 Windows 32 位应用程序或需要部署其他准备工作的应用程序。 对于这些应用程序， [Microsoft win32 内容准备工具](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool)将提供已准备好使用`.intunewin`格式文件进行安装。

### <a name="setting-up-custom-settings-using-powershell"></a>设置自定义设置，请使用 PowerShell

我们还将使用 PowerShell 来提供管理该主机的可扩展性的示例。 该脚本将在主机上的默认背景设置。 此功能也会出现在配置文件，并仅用于说明该功能。

在解决方案中可能需要在安全的工作站上设置了一些自定义控件和设置。 在本示例中，我们将更改背景的工作站使用 Powershell 可以轻松地为准备好使用工作站的安全标识设备。 虽然我们的示例将使用 PowerShell 来完成此任务，还在 Azure 门户中完成。

我们的示例将使用以下[免费的通用背景图像](https://i.imgur.com/OAJ28zO.png)并[SetDesktopBackground.ps1](https://gallery.technet.microsoft.com/scriptcenter/Set-Desktop-Image-using-5430c9fb/)从 Microsoft 脚本中心，以便允许 Windows 以加载开始上的背景。

1. 将脚本下载到本地设备
1. 更新 customerXXXX，并想要在脚本中使用，以反映此后台文件，并想要使用的部署的文件夹在后台下载位置。 在本示例中，我们将为 customerXXXX 到背景。  
1. 浏览到**Azure 门户** > **Microsoft Intune** > **设备配置** > **PowerShell脚本** > **添加**
1. 提供**名称**脚本并指定**脚本位置**下载到位置
1. 选择“配置” 
   1. 设置**运行此脚本使用登录凭据**到**是**
   1. 单击 **“确定”**
1. 单击“创建” 
1. 选择**分配** > **选择组**
   1. 添加安全组**保护工作站**之前创建和单击**保存**

### <a name="using-the-preview-mdm-security-baseline-for-october-2018"></a>使用预览：MDM 安全基线的 2018 年 10 月

Microsoft Intune 引入了安全基线管理功能提供简单的方法来强制执行常见的基线安全状态的管理员。 在基线提供增强型配置文件工作站下实现锁定的类似方法。

为安全的工作站，因为它不使用此基线的实现将与安全配置部署发生冲突。

|   |   |   |
| :---: | :---: | :---: |
| 上面锁 | 设备安装 | 远程桌面服务 |
| 应用程序运行时 | 设备锁定 | 远程管理 |
| 应用程序管理 | 事件日志服务 | 远程过程调用 |
| 自动播放 | 体验 | 搜索 |
| BitLocker | 攻击防护 | 智能屏幕 |
| 浏览器 | 文件资源管理器 | 系统要求|
| 连接 | Internet Explorer | Wi-Fi |
| 凭据委派 | 本地策略安全选项 | Windows 连接管理器 |
| 凭据 UI | MS 安全指南 | Windows Defender|
| 数据保护 | MSS 旧版 | Windows Ink 工作区 |
| Device Guard | 幂 | Windows PowerShell |

可以在文章中找到有关此预览功能的详细信息[Intune 的 Windows 安全基线设置](https://docs.microsoft.com/Intune/security-baseline-settings-windows)。

## <a name="enroll-and-validate-your-first-device"></a>注册并验证你的第一个设备

1. 若要注册设备，需要以下信息：
   * **序列号**-设备机箱上找到
   * **Windows 产品 ID** -下找到**系统** > **有关**从 Windows 设置菜单。
   * 运行[Get WindowsAutoPilotInfo](https://aka.ms/Autopilotshell)将为设备注册的 CSV 哈希文件提供所有所需的信息。 
      * 运行`Get-WindowsAutoPilotInfo – outputfile device1.csv`输出为可导入到 Intune 的 CSV 文件的信息。

   > [!NOTE]
   > 该脚本将需要提升的权限和运行为远程签名。 可以使用以下命令以允许脚本正确运行。 `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned`

   *  您可以通过登录到 Windows 10 版本 1809年或更高版本的设备收集的信息，来收集此信息或排序的新设备时，硬件分销商可以提供此信息。
1. 收集所需的信息并将返回到**Azure 门户**。 导航到**Microsoft Intune** > **设备注册** > **Windows 注册** > **设备-管理 Windows Autopilot 设备**，选择**导入**，并选择 CSV 文件创建或已提供。
1. 现在已注册的设备添加到安全组**保护工作站**之前创建。
1. 从你想要配置 Windows 10 设备，浏览到**Windows 设置** > **更新和安全** > **恢复**。 选择**开始**下**重置此电脑**并按照提示重置并重新配置使用配置的配置文件和符合性策略的设备。

配置设备后，完成评审并检查配置。 确认继续部署之前正确配置的第一个设备。

## <a name="assignment-and-monitoring"></a>分配和监视

分配设备和用户将需要的映射[选择配置文件](https://docs.microsoft.com/intune/device-profile-assign)到你的安全组和将授予对服务的权限的所有新用户将需要添加到安全组以及。

完成监视到配置文件使用的监视[Microsoft Intune 配置文件](https://docs.microsoft.com/intune/device-profile-monitor)。

## <a name="next-steps"></a>后续步骤

[Microsoft Intune](https://docs.microsoft.com/intune/index)文档

[Azure AD](https://docs.microsoft.com/azure/active-directory/index)文档