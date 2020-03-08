---
title: 部署 Azure 托管的工作站-Azure Active Directory
description: 了解如何部署安全的 Azure 托管的工作站以降低因配置错误或安全问题而造成的破坏风险。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d02b0299b6267fdd9d880d5bc0fe8c93d0edadc
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78672610"
---
# <a name="deploy-a-secure-azure-managed-workstation"></a>部署安全的 Azure 托管工作站

[了解安全工作站](concept-azure-managed-workstation.md)后，就可以开始部署过程了。 在本指南中，你将使用定义的配置文件来创建更安全的工作站。

![安全工作站的部署](./media/howto-azure-managed-workstation/deploying-secure-workstations.png)

部署解决方案之前，请选择一个配置文件。 你可以同时在部署中使用多个配置文件，并为其分配标记或组。

> [!NOTE]
> 根据要求应用任何配置文件。 可以通过将其分配到 Microsoft Intune 来移到另一个配置文件。

| 配置文件 | 低 | 增强版 | 高 | 专用 | 保护 | 隔离 |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Azure AD 中的用户 | 是 | 是 | 是 | 是 | 是 | 是 |
| Intune 管理 | 是 | 是 | 是 | 是 | 是 | 是 |
| 设备-Azure AD 已注册 | 是 |  |  |  |  | |   |
| 已联接设备 Azure AD |   | 是 | 是 | 是 | 是 | 是 |
| 已应用 Intune 安全基线 |   | 是 <br> 性 | 是 <br> (HighSecurity) | 是 <br> NCSC | 是 <br> 保护 | NA |
| 硬件符合安全 Windows 10 标准 |   | 是 | 是 | 是 | 是 | 是 |
| 已启用 Microsoft Defender ATP |   | 是  | 是 | 是 | 是 | 是 |
| 删除管理权限 |   |   | 是  | 是 | 是 | 是 |
| 使用 Microsoft Autopilot 进行部署 |   |   | 是  | 是 | 是 | 是 |
| 仅由 Intune 安装的应用 |   |   |   | 是 | 是 |是 |
| 仅限批准列表的 Url |   |   |   | 是 | 是 |是 |
| 已阻止 Internet （入站/出站） |   |   |   |  |  |是 |

> [!NOTE]
> 在安全工作站指南中，将为配置文件和策略分配**设备**。 用户不会直接向用户应用策略，这允许设备共享（共享设备）生效。 如果你的部署中未共享安全工作站，或者需要单独的用户策略，则可以将用户策略配置文件分配给用户和设备。 

## <a name="license-requirements"></a>许可要求

本指南中介绍的概念假设您 Microsoft 365 企业版 E5 或等效 SKU。 本指南中的某些建议可通过较低版本的 Sku 来实现。 有关详细信息，请参阅[Microsoft 365 企业版许可](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise)。

若要自动进行许可证预配，请考虑为用户提供[基于组的许可](../users-groups-roles/licensing-groups-assign.md)。

## <a name="azure-active-directory-configuration"></a>Azure Active Directory 配置

Azure Active Directory （Azure AD）管理管理员工作站的用户、组和设备。 使用[管理员帐户](../users-groups-roles/directory-assign-admin-roles.md)启用标识服务和功能。

创建安全工作站管理员帐户时，会将该帐户公开给当前工作站。 请确保使用已知的安全设备执行此初始配置和所有全局配置。 若要降低首次攻击的风险，请考虑遵循[指导防止恶意软件感染的指导](/windows/security/threat-protection/intelligence/prevent-malware-infection)。

需要多因素身份验证，至少为你的管理员。 有关实现指南，请参阅[部署基于云的 MFA](../authentication/howto-mfa-getstarted.md) 。

### <a name="azure-ad-users-and-groups"></a>Azure AD 用户和组

1. 在 Azure 门户中，浏览到**Azure Active Directory** > **用户** > **新用户**。
1. 按照[创建用户教程](/Intune/quickstart-create-user)中的步骤创建设备管理员。
1. 输入：

   * **名称**-安全工作站管理员
   * **用户名** - `secure-ws-admin@identityitpro.com`
   * **目录角色** - **有限的管理员**，并选择**Intune 管理员**角色。

1. 选择“创建”。

接下来，创建两个组：工作站用户和工作站设备。

在 Azure 门户中，浏览到 "**新建组**" >  > **组** **Azure Active Directory** "。

1. 对于工作站用户组，你可能想要配置[基于组的许可](../users-groups-roles/licensing-groups-assign.md)，以自动为用户预配许可证。
1. 对于 "工作站用户" 组，请输入：

   * **组类型**-安全性
   * **组名称**-安全工作站用户
   * **成员资格类型**-已分配

1. 添加安全工作站管理员用户： `secure-ws-admin@identityitpro.com`
1. 你可以添加将管理安全工作站的任何其他用户。
1. 选择“创建”。
1. 对于 "工作站设备" 组，请输入：

   * **组类型**-安全性
   * **组名称**-安全工作站
   * **成员资格类型**-已分配

1. 选择“创建”。

### <a name="azure-ad-device-configuration"></a>Azure AD 设备配置

#### <a name="specify-who-can-join-devices-to-azure-ad"></a>指定可将设备加入到 Azure AD

在 Active Directory 中配置设备设置，以允许管理安全组将设备加入到域。 若要从 Azure 门户配置此设置：

1.  > **设备设置**中转到**Azure Active Directory** > **设备**"。
1. 选择 "**用户可以将设备加入 Azure AD** **" 下的 "选择"** ，然后选择 "安全工作站用户" 组。

#### <a name="removal-of-local-admin-rights"></a>删除本地管理权限

此方法要求 VIP、DevOps 和安全级别工作站的用户在其计算机上没有管理员权限。 若要从 Azure 门户配置此设置：

1.  > **设备设置**中转到**Azure Active Directory** > **设备**"。
1. 在**Azure AD 联接设备上的其他本地管理员**下选择 "**无**"。

#### <a name="require-multi-factor-authentication-to-join-devices"></a>需要多重身份验证才能加入设备

进一步增强将设备加入 Azure AD 的过程：

1.  > **设备设置**中转到**Azure Active Directory** > **设备**"。
1. 选择 "**需要多重身份验证来加入设备**" 下的 **"是"** 。
1. 选择“保存”。

#### <a name="configure-mobile-device-management"></a>配置移动设备管理

通过 Azure 门户：

1. 浏览到**Azure Active Directory** > **移动性（MDM 和 MAM）**  > **Microsoft Intune**。
1. 将**MDM 用户作用域**设置更改为 "**全部**"。
1. 选择“保存”。

这些步骤允许你用 Intune 管理任何设备。 有关详细信息，请参阅[Intune 快速入门：设置适用于 Windows 10 设备的自动注册](/Intune/quickstart-setup-auto-enrollment)。 在以后的步骤中，将创建 Intune 配置和合规性策略。

#### <a name="azure-ad-conditional-access"></a>Azure AD 条件性访问

Azure AD 条件性访问可帮助将特权管理任务限制为符合要求的设备。 登录到云应用程序时，需要**安全工作站用户**组的预定义成员才能执行多重身份验证。 最佳做法是从策略中排除紧急访问帐户。 有关详细信息，请参阅[管理 Azure AD 中的紧急访问帐户](../users-groups-roles/directory-emergency-access.md)。

## <a name="intune-configuration"></a>Intune 配置

### <a name="configure-enrollment-status"></a>配置注册状态

确保安全工作站是可信的干净设备，这一点很重要。 购买新设备时，可以坚持将它们设置为[在 S 模式下出厂设置为 Windows 10 专业](/Windows/deployment/Windows-10-pro-in-s-mode)版，这会限制在供应链管理期间暴露于漏洞。 从供应商接收到设备后，可以使用 Autopilot 在 S 模式下进行更改。 以下指南提供了有关应用转换过程的详细信息。

为了确保在使用之前对设备进行完全配置，Intune 提供了一种在**安装所有应用和配置文件之前阻止设备使用**的方法。

从 **Azure 门户**：

1. 请参阅**Microsoft Intune** > **设备注册** > **Windows 注册** > **注册状态页** > **默认** > **设置**。
1. 将 "**显示应用程序配置文件的安装进度**" 设置为 **"是"** 。
1. 设置**阻止设备使用，直到将所有应用和配置文件安装**为 **"是"** 。

### <a name="create-an-autopilot-deployment-profile"></a>创建 Autopilot 部署配置文件

创建设备组后，你必须创建一个部署配置文件来配置 Autopilot 设备。

在 Azure 门户中的 Intune 中：

1.  > "**创建配置文件**"，选择 "**设备注册**" > **Windows 注册** > **部署配置文件**。
1. 输入：

   * 名称-**安全工作站部署配置文件**。
   * 说明-**安全工作站的部署**。
   * 将 "**所有目标设备转换为 Autopilot** " 设置为 **"是"** 。 此设置可确保列表中的所有设备都注册到 Autopilot 部署服务中。 允许处理注册的48小时。

1. 选择“**下一页**”。

   * 对于**部署模式**，请选择 "**自部署（预览版）** "。 具有此配置文件的设备将与注册该设备的用户关联。 注册设备需要用户凭据。 需要注意的是，在**自我部署**模式下部署设备将允许你在共享模型中部署便携式计算机。 在第一次将设备分配给用户之前，不会进行用户分配。 因此，在完成用户分配之前，将不会启用 BitLocker 等任何用户策略。 有关如何登录到安全设备的详细信息，请参阅[所选配置文件](/intune/device-profile-assign)。
   * "**联接 Azure AD 为**" 框应显示已**联接 Azure AD**并灰显。
   * 选择语言（区域），用户帐户类型为 "**标准**"。 

1. 选择“**下一页**”。

   * 如果已预先配置了一个作用域标记，请选择它。

1. 选择“**下一页**”。
1. 选择**分配** **给** > **所选组** > 分配。 在 "**选择要包括的组**" 中，选择 "**安全工作站**"。
1. 选择“**下一页**”。
1. 选择“创建”以创建该配置文件。 现在可以将 Autopilot 部署配置文件分配给设备。

Autopilot 中的设备注册根据设备类型和角色提供不同的用户体验。 在我们的部署示例中，我们将演示一个模型，其中的安全设备是大容量部署的并且可以共享，但当首次使用时，设备会分配给用户。 有关详细信息，请参阅[Intune Autopilot 设备注册](/intune/device-enrollment)。

### <a name="configure-windows-update"></a>配置 Windows 更新

将 Windows 10 保持最新状态是你可以执行的最重要的任务之一。 若要以安全状态维护 Windows，请部署更新环，以管理将更新应用到工作站的节奏。 

本指南建议创建新的更新环，并更改以下默认设置：

在 Azure 门户中：

1. 请参阅**Microsoft Intune** > **软件更新** > **Windows 10 更新环**。
1. 输入：

   * 名称- **Azure 托管的工作站更新**
   * 维护渠道- **Windows 有问必答-快速**
   * 质量更新延迟（天）- **3**
   * 功能更新延迟期（天）- **3**
   * 自动更新行为-**无需最终用户控制即可自动安装和重新启动**
   * 阻止用户暂停 Windows 更新-**阻止**
   * 要求用户批准在工作时间之外重新启动-**必需**
   * 允许用户重新启动（已重新启动）-**必需**
   * 自动重新启动后将用户切换到预定重启（天）- **3**
   * 暂停预定重启提醒（天）- **3**
   * 设置等待重启的截止时间（天）- **3**

1. 选择“创建”。
1. 在 "**分配**" 选项卡上，添加 "**安全工作站**" 组。

有关 Windows 更新策略的详细信息，请参阅[策略 CSP-更新](/windows/client-management/mdm/policy-csp-update)。

### <a name="windows-defender-atp-intune-integration"></a>Windows Defender ATP Intune 集成

Windows Defender ATP 和 Microsoft Intune 一起工作以帮助防止安全漏洞。 它们还可以限制违规造成的影响。 ATP 功能提供实时威胁检测，并对终结点设备启用广泛的审核和日志记录。

若要配置 Windows Defender ATP 和 Intune 的集成，请参阅 Azure 门户。

1. 浏览到**Microsoft Intune** > **设备符合性** > **Windows Defender ATP**。
1. 在步骤1中，在**配置 Windows DEFENDER atp**下，选择 **"将 Windows defender ATP 连接到 Windows defender 安全中心 Microsoft Intune"** 。
1. 在 Windows Defender 安全中心：

   1. 选择 "**设置**" > "**高级功能**"。
   1. 对于 " **Microsoft Intune 连接**"，请选择 **"打开"** 。
   1. 选择 "**保存首选项**"。

1. 建立连接后，返回到 Intune，并选择顶部的 "**刷新**"。
1. 将 "**连接 windows 设备版本 10.0.15063" 和更高版本设置为 "Windows DEFENDER ATP** **"** 。
1. 选择“保存”。

有关详细信息，请参阅[Windows Defender 高级威胁防护](/Windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection)。

### <a name="finish-workstation-profile-hardening"></a>完成工作站配置文件强化

若要成功完成解决方案的强化，请下载并执行相应的脚本。 查找所需**配置文件级别**的下载链接：

| 配置文件 | 下载位置 | 文件名 |
| --- | --- | --- |
| 低安全性 | 空值 | 空值 |
| 增强的安全性 | https://aka.ms/securedworkstationgit | 增强-Windows 10-（1809）. ps1 |
| 高安全性 | https://aka.ms/securedworkstationgit | HighSecurityWorkstation-Windows 10-（1809）. ps1 |
| 专用 | https://github.com/pelarsen/IntunePowerShellAutomation | DeviceConfiguration_NCSC Windows 10 （1803） SecurityBaseline |
| 专用符合性 * | https://aka.ms/securedworkstationgit | DeviceCompliance_NCSC-Windows10(1803).ps1 |
| 保护 | https://aka.ms/securedworkstationgit | Secure-Workstation-Windows10-(1809)-SecurityBaseline.ps1 |

\* 专用符合性是强制实施在 NCSC Windows 10 SecurityBaseline 中提供的专用配置的脚本。

成功执行脚本后，你可以在 Intune 中对配置文件和策略进行更新。 增强和安全配置文件的脚本会为你创建策略和配置文件，但必须将策略分配到**安全工作站**设备组。

* 可以在以下位置找到由脚本创建的 Intune 设备配置文件： **Azure 门户** > **Microsoft Intune** > **设备配置** > **配置**文件。
* 可以在以下位置找到由脚本创建的 Intune 设备符合性策略： **Azure 门户** > **Microsoft Intune** > **设备符合性** > **策略**。

若要查看脚本所做的更改，可以导出配置文件。 这样，你就可以根据[SECCON 文档](/windows/security/threat-protection/windows-security-configuration-framework/windows-security-configuration-framework)中所述，确定可能需要的其他强化。

从[DeviceConfiguration GiuHub 存储库](https://github.com/microsoftgraph/powershell-intune-samples/tree/master/DeviceConfiguration)运行 Intune 数据导出 `DeviceConfiguration_Export.ps1` 脚本，以导出所有当前 Intune 配置文件。

## <a name="additional-configurations-and-hardening-to-consider"></a>需要考虑的其他配置和强化

按照此处的指南，你已部署了安全工作站。 但是，还应考虑其他控件。 例如：

* 限制对备用浏览器的访问
* 允许出站 HTTP
* 阻止选择网站
* 设置运行自定义 PowerShell 脚本的权限

### <a name="set-rules-in-the-firewall-configuration-service-provider-csp"></a>在防火墙配置服务提供程序（CSP）中设置规则

你可以根据你允许的和阻止的终结点的需要，对入站和出站规则的管理进行其他更改。 继续强化安全工作站时，可以放宽拒绝所有入站和出站流量的限制。 你可以添加允许的出站站点以包含通用和受信任的网站。 有关详细信息，请参阅[防火墙配置服务](/Windows/client-management/mdm/firewall-csp)。

限制的 URL 流量管理包括：

* 拒绝受保护的工作站配置文件脚本中设置的所有入站流量。
* 拒绝除所选 Azure 和 Microsoft 服务外的所有出站，包括 Azure Cloud Shell 和允许 Azure 密码重置的能力。

```
[HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings]
"ProxyEnable"=dword:00000001
"ProxyServer"="127.0.0.1:80"
"ProxyOverride"="*.azure.com;*.azure.net;*.microsoft.com;*.windowsupdate.com;*.microsoftonline.com;*.microsoftonline.cn;*.windows.net;*.windowsazure.com;*.windowsazure.cn;*.azure.cn;*.loganalytics.io;*.applicationinsights.io;*.vsassets.io;*.azure-automation.net;*.visualstudio.com,portal.office.com;*.aspnetcdn.com;*.sharepointonline.com;*.msecnd.net;*.msocdn.com;*.webtrends.com"
"AutoDetect"=dword:00000000
```

如果希望向阻止规则提供更多粒度，则可以参阅维护[域块列表的 Spamhaus 项目（双）](https://www.spamhaus.org/dbl/)：要用作阻止站点实现的一组高级规则的好资源。

### <a name="manage-local-applications"></a>管理本地应用程序

如果删除了本地应用程序（包括生产力应用程序），则安全工作站会移动到真正的强制性状态。 此处，将 Chrome 添加为默认浏览器，并使用 Intune 限制用户修改浏览器及其插件的功能。

#### <a name="deploy-applications-using-intune"></a>使用 Intune 部署应用程序

在某些情况下，安全工作站上需要诸如 Google Chrome 浏览器之类的应用程序。 以下示例说明了如何将 Chrome 安装到安全组安全**工作站**中的设备上。

1. 下载[适用于 Windows 64 位](https://cloud.google.com/chrome-enterprise/browser/download/)的脱机安装程序 Chrome 捆绑包。
1. 提取文件并记下 `GoogleChromeStandaloneEnterprise64.msi` 文件的位置。
1. 在 " **Azure 门户**浏览到**Microsoft Intune** > **客户端应用** > **应用**" > "**添加**"。
1. 在 "**应用类型**" 下，选择 "**业务线**"。
1. 在 "**应用包文件**" 下，从提取的位置选择 `GoogleChromeStandaloneEnterprise64.msi` 文件，然后选择 **"确定"** 。
1. 在 "**应用信息**" 下，提供说明和发布者。 选择“确定”。
1. 选择 **添加** 。
1. 在 "**分配**" 选项卡上，选择 "可用于**分配类型** **的已注册设备**"。
1. 在 "**包含的组**" 下，添加**安全工作站**组。
1. 选择“确定”，然后选择“保存”。

有关配置 Chrome 设置的详细信息，请参阅[管理 Chrome Browser with Microsoft Intune](https://support.google.com/chrome/a/answer/9102677)。

#### <a name="configuring-the-company-portal-for-custom-apps"></a>配置自定义应用的公司门户

在安全模式下，应用程序安装仅限于 Intune 公司门户。 但是，安装门户需要访问 Microsoft Store。 在受保护的解决方案中，可以通过脱机模式向所有设备提供公司门户。

[公司门户](/Intune/store-apps-company-portal-app)的 Intune 管理的副本使你可以按需访问可推送到受保护工作站用户的其他工具。

你可能需要安装 Windows 32 位应用或部署需要特别准备的其他应用。 在这种情况下， [Microsoft win32 内容准备工具](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool)可以提供随时可用的 `.intunewin` 格式化文件进行安装。

### <a name="conditional-access-only-allowing-secured-workstation-ability-to-access-azure-portal"></a>条件性访问仅允许受保护的工作站访问 Azure 门户

Azure AD 提供管理和限制的功能，以及可访问 Azure 云管理门户的人员和内容。 启用[条件性访问](../conditional-access/overview.md)将确保只有安全工作站可以管理或更改资源。 部署此功能时，如果[紧急访问](../users-groups-roles/directory-emergency-access.md)功能只能或应该用于极端情况以及通过策略管理的帐户，则这一点非常重要。

> [!NOTE]
> 你将需要创建用户组，并将你的紧急用户添加为可以绕过条件访问策略。 对于我们的示例，我们有一个称为**急诊 BreakGlass**的安全组

1. 浏览到**Azure 门户** > **Microsoft Intune** > **条件性访问策略** > **新策略**。
1. 提供策略的**名称**。
1. 选择**用户和组** > **选择用户和组** 
1. 选择 "**包括** > **目录角色**" > 选择 "全局管理员"、"特权角色管理员"、"特权身份验证管理员、安全管理员、合规性管理员、条件性访问管理员、应用程序管理员、云应用程序管理员、Intune 服务管理员的 > 角色"。
1. 选择 "**排除**" > 选择**用户和组**> 选择 "**选择排除的用户**" > 选择**紧急 BreakGlass**组。
1. 选择 "**云应用" 或 "操作**" > 选择**所有云应用**
1. 选择**条件**> 选择**设备平台**> 选择 **"配置是" > 选择 "选择" "** **设备平台**" 选择**Windows**
1. 选择 "**访问控制**" > 选择 "**授予访问权限** **"** > 选择 "**要求设备标记为符合**"。 
1. 选择**启用策略** > 
 
此策略设置将确保你的管理员必须使用由 Intune 和 WDATP 设置的兼容 Windows 设备。 

组织还应考虑在其环境中阻止旧的身份验证协议。 可以通过多种方法来完成此任务。有关阻止旧身份验证协议的详细信息，请参阅[如何：阻止旧身份验证与条件访问 Azure AD](../conditional-access/block-legacy-authentication.md)。

### <a name="use-powershell-to-create-custom-settings"></a>使用 PowerShell 创建自定义设置

你还可以使用 PowerShell 来扩展主机管理功能。 此示例脚本在主机上设置默认背景。 它也是一种通过 Azure 门户和配置文件提供的功能。 示例脚本仅用于说明 PowerShell 功能。

你可能需要在安全工作站上设置一些自定义控件和设置。 此示例使用 Powershell 将设备识别为可用、安全工作站的功能，来更改工作站的背景。

Microsoft 脚本中心的[SetDesktopBackground](https://gallery.technet.microsoft.com/scriptcenter/Set-Desktop-Image-using-5430c9fb/)脚本允许 Windows 在启动时加载此[普通的通用背景图像](https://i.imgur.com/OAJ28zO.png)。

1. 将脚本下载到本地设备。
1. 更新背景图像的 customerXXXX 和下载位置。 在我们的示例中，我们将 customerXXXX 替换为背景。
1. 浏览到**Azure 门户** > **Microsoft Intune** > **设备配置** > " **添加**"。 > 
1. 提供脚本的**名称**，并指定**脚本位置**。
1. 选择“配置”。
   1. 设置 "**使用登录凭据运行此脚本** **"** 。
   1. 选择“确定”。
1. 选择“创建”。
1. 选择 "**分配**" > **选择组**。
   1. 添加安全组安全**工作站**。
   1. 选择“保存”。

## <a name="enroll-and-validate-your-first-device"></a>注册并验证你的第一个设备

1. 若要注册你的设备，你需要以下信息：
   * **序列号**-在设备底盘上找到。
   * **Windows 产品 ID** -从 "windows 设置" 菜单**中的 "** **系统** > " 下找到。
   * 您可以运行[WindowsAutoPilotInfo](https://aka.ms/Autopilotshell)以获取 CSV 哈希文件，其中包含设备注册所需的所有信息。
   
     运行 `Get-WindowsAutoPilotInfo – outputfile device1.csv` 以将信息输出为可导入到 Intune 中的 CSV 文件。

     > [!NOTE]
     > 此脚本需要提升的权限。 它以远程签名的方式运行。 `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned` 命令使脚本正常运行。

   * 可以通过登录到 Windows 10 版本1809或更高版本的设备来收集此信息。 硬件经销商还可以提供此信息。
1. 在**Azure 门户**中，请参阅**Microsoft Intune** > **设备注册** > **Windows 注册** > **设备-管理 Windows Autopilot 设备**。
1. 选择 "**导入**" 并选择 CSV 文件。
1. 将设备添加到**安全工作站**安全组。
1. 在要配置的 Windows 10 设备上，请参阅**Windows 设置** > **更新 & 安全** > **恢复**。
   1. 选择 "**重置此电脑**" 下的 "**开始**"。
   1. 按照提示操作，通过配置的配置文件和合规性策略重置并重新配置设备。

配置设备后，请完成检查并检查配置。 在继续进行部署之前，请确认第一个设备配置正确。

## <a name="assign-devices"></a>分配设备

要分配设备和用户，需要将[所选配置文件](/intune/device-profile-assign)映射到安全组。 还必须将需要服务权限的所有新用户添加到安全组。

## <a name="using-sentinel-and-windows-defender-atp-to-monitor-and-respond-to-security-incidents"></a>使用 Sentinel 和 Windows Defender ATP 监视和响应安全事件

监视安全工作站部署的方法是：启用 [Sentinel]，并利用[威胁和漏洞管理](/windows/security/threat-protection/microsoft-defender-atp/next-gen-threat-and-vuln-mgt)，本指南不会提供详尽的威胁，而是监视并响应潜在的安全事件。

我们将使用**Azure Sentinel**执行以下操作： 

* 从 Intune、Azure 门户和 Azure AD 收集用于用户和设备监视的数据
* 帮助调查用户和设备配置可疑活动
* 并推动使用 WDATP 探索安全调查的能力

Sentinel 监视要求设置到数据源的连接器，如 Azure AD。

1. 在**Azure 门户**中，请参阅**Azure Sentinel （预览版）** > 选择 "**添加**"
1. 在 "**选择要添加到 Azure 的工作区**" 中，选择 "**创建新工作区**"
1. 输入：
   * **Log Analytics 工作区**-"安全工作站监视"
   * **订阅**-选择活动订阅
   * **资源组**-选择 "新建" * * > 安全工作站 RG > **"确定"**
   * **位置**-选择最适合你的部署的地理位置
   * **价格层**-选择**每 GB （2018）**
1. 选择“确定”。

接下来，我们将可用的安全工作站数据源连接到监视。

1. 在**Azure 门户**中，请参阅**Azure Sentinel 工作区**> 选择**安全工作站监视**工作区
1. 选择**数据连接器**
1. 查看必备组件后，请选择**Azure Active Directory** > 打开连接器页面 > "。 转到 "配置"，并选择 "**连接**" Azure AD 登录日志，并 Azure AD "审核日志"。
1. 查看先决条件后，选择 " **Azure 活动**> 打开连接器页面 >。 继续配置 Azure 活动日志 > 选择订阅 > 选择 "**连接**"

当 Sentinel 收集数据时，你将能够通过选择 " **Azure 门户**" 来观察活动，请参阅**Azure Sentinel 概述** 

我们将使用**Windows DEFENDER ATP （WDATP）** 执行以下操作：

* 持续观察和监视漏洞和配置错误
* 充分利用 WDATP 来确定动态威胁的优先级
* 将漏洞与终结点检测和响应（EDR）警报相关联
* 使用仪表板在调查过程中确定计算机级别的漏洞
* 将修正推送到 Intune

配置[DEFENDER ATP 仪表板](https://securitycenter.windows.com/machines)。 使用[威胁 & 漏洞管理仪表板概述](/windows/security/threat-protection/microsoft-defender-atp/tvm-dashboard-insights)。

## <a name="monitoring-application-activity-using-microsoft-monitoring-agent-mma"></a>使用 Microsoft Monitoring Agent （MMA）监视应用程序活动
从专用工作站开始，会启用应用保险箱来监视工作站上的应用程序活动。 若要将监视集成到 Log Analytics 工作区中，必须遵循 MMA 代理和配置。 

> [!NOTE]
> 专用工作站配置文件包含 AppLocker 监视策略。 要在客户端上监视应用程序活动，需要部署策略

部署 MMA 代理和 Intune PowerShell 脚本

1. 将安装[脚本下载到本地设备](https://aka.ms/securedworkstationgit)。
1. 更新参数， **$WorkSpaceID**和 **$WorkSpaceKey**
1. 浏览到**Azure 门户** > **Microsoft Intune** > **设备配置** > " **添加**"。 > 
1. 提供脚本的**名称**，并指定**脚本位置**。
1. 选择“配置”。
   1. 设置 "**使用登录凭据运行此脚本** **"** 。
   1. 选择“确定”。
1. 选择“创建”。
1. 选择 "**分配**" > **选择组**。
   1. 添加安全组安全**工作站**。
   1. 选择“保存”。

接下来，你必须设置用于接收新日志的 Log Analytics
1. 在**Azure 门户**中转到**Log Analytics 工作区**> 选择 "安全工作站监视"
1. 选择 "**高级设置**" > **数据** > **Windows 事件日志**
1. 在 **"从以下事件日志收集事件"** 中 
1. 输入：
   * "Microsoft-Windows-AppLocker/EXE 和 DLL" > 取消选择**信息**
   * "Microsoft-Windows-AppLocker/MSI 和脚本" > 取消选择**信息**
   * "Microsoft-Windows-AppLocker/打包应用程序部署" > 取消选择**信息**
   * "Microsoft-AppLocker/封装应用-执行" > 取消选择**信息**
1. 选择“保存”

应用程序日志记录将在所选 Log Analytics 工作区中可用。

## <a name="monitoring"></a>监视

* 了解如何[通过 Azure Sentinel 检测威胁](/azure/sentinel/tutorial-detect-threats)
* [用 Azure Sentinel 调查事件](/azure/sentinel/tutorial-investigate-cases)
* [在 Azure Sentinel 中设置自动威胁响应](/azure/sentinel/tutorial-respond-threats-playbook)
* 了解如何查看[公开评分](/windows/security/threat-protection/microsoft-defender-atp/tvm-exposure-score)
* 查看[安全建议](/windows/security/threat-protection/microsoft-defender-atp/tvm-security-recommendation)
* 管理安全[修正](/windows/security/threat-protection/microsoft-defender-atp/tvm-remediation)
* 管理[终结点检测和响应](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)
* 监视配置文件和[Intune 配置文件监视](/intune/device-profile-monitor)。

## <a name="next-steps"></a>后续步骤

* 详细了解[Microsoft Intune](/intune/index)。
* 了解[Azure AD](../index.yml)。
* 使用[Microsoft Defender 高级威胁防护](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)
* 发现[Azure Sentinel](/azure/sentinel/)
