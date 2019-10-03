---
title: 部署 Azure 托管的工作站-Azure Active Directory
description: 了解如何部署安全的 Azure 托管的工作站以降低因配置错误或安全问题而造成的破坏风险。
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
ms.openlocfilehash: be9e6374d92fbb7bb1c4b5a2a9e154119c5baf87
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377485"
---
# <a name="deploy-a-secure-azure-managed-workstation"></a>部署安全的 Azure 托管工作站

[了解安全工作站](concept-azure-managed-workstation.md)后, 就可以开始部署过程了。 在本指南中, 你将使用定义的配置文件来创建更安全的工作站。

![安全工作站的部署](./media/howto-azure-managed-workstation/deploying-secure-workstations.png)

你必须选择一个配置文件, 然后才能部署解决方案。 你可以同时在部署中使用多个配置文件, 并为其分配标记或组。
> [!NOTE]
> 根据要求应用任何配置文件。 可以通过在 Intune 中分配来移动到另一个配置文件。

| 配置文件 | 低 | 增强版 | 高 | 特殊化 | 安全 | 独立 |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Azure AD 中的用户 | 是 | 是 | 是 | 是 | 是 | 是 |
| Intune 管理 | 是 | 是 | 是 | 是 | 是 | 是 |
| 设备-Azure AD 已注册 | 是 |  |  |  |  | |   |
| 已联接设备 Azure AD |   | 是 | 是 | 是 | 是 | 是 |
| 已应用 Intune 安全基线 |   | 是 <br> 性 | 是 <br> (HighSecurity) | 是 <br> NCSC | 是 <br> 保护 |  不可用 |
| 硬件符合安全 Windows 10 标准 |   | 是 | 是 | 是 | 是 | 是 |
| 已启用 Microsoft Defender ATP |   | 是  | 是 | 是 | 是 | 是 |
| 删除管理权限 |   |   | 是  | 是 | 是 | 是 |
| 使用 Microsoft Autopilot 进行部署 |   |   | 是  | 是 | 是 | 是 |
| 仅由 Intune 安装的应用 |   |   |   | 是 | 是 |是 |
| 仅限批准列表的 Url |   |   |   | 是 | 是 |是 |
| 已阻止 Internet (入站/出站) |   |   |   |  |  |是 |

## <a name="license-requirements"></a>许可要求

本指南中介绍的概念假设您 Microsoft 365 企业版 E5 或等效 SKU。 本指南中的某些建议可通过较低版本的 Sku 来实现。 有关详细信息, 请参阅[Microsoft 365 企业版许可](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise)。

若要自动进行许可证预配, 请考虑为用户提供[基于组的许可](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign)。

## <a name="azure-active-directory-configuration"></a>Azure Active Directory 配置

Azure Active Directory (Azure AD) 管理管理员工作站的用户、组和设备。 需要使用[管理员帐户](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)启用标识服务和功能。

创建安全工作站管理员帐户时, 会将该帐户公开给当前工作站。 请确保使用已知的安全设备执行此初始配置和所有全局配置。 若要降低首次攻击的风险, 请考虑遵循[指导防止恶意软件感染的指导](https://docs.microsoft.com/windows/security/threat-protection/intelligence/prevent-malware-infection)。

还应要求对管理员至少进行多重身份验证。 有关实现指南, 请参阅[部署基于云的 MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted) 。

### <a name="azure-ad-users-and-groups"></a>Azure AD 用户和组

1. 在 Azure 门户中, 浏览到 " **Azure Active Directory** > 用户" "**用户** >  **"。**
1. 按照[创建用户教程](https://docs.microsoft.com/Intune/quickstart-create-user)中的步骤创建设备管理员。
1. 输入：
   * **名称**-安全工作站管理员
   * **用户名** - `secure-ws-admin@identityitpro.com`
   * **Directory 角色** - **限制管理员**, 并选择**Intune 管理员**角色。
1. 选择“创建”。

接下来, 创建两个组: 工作站用户和工作站设备。

在 Azure 门户中, 浏览到**Azure Active Directory** > **组** > ""**新建组**"。

1. 对于工作站用户组, 你可能想要配置[基于组的许可](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign), 以自动为用户预配许可证。
1. 对于 "工作站用户" 组, 请输入:
   * **组类型**-安全性
   * **组名称**-安全工作站用户
   * **成员资格类型**-已分配
1. 添加安全工作站管理员用户:`secure-ws-admin@identityitpro.com`
1. 你可以添加将管理安全工作站的任何其他用户。
1. 选择“创建”。

1. 对于 "工作站设备" 组, 请输入:
   * **组类型**-安全性
   * **组名称**-安全工作站
   * **成员资格类型**-已分配
1. 选择“创建”。

### <a name="azure-ad-device-configuration"></a>Azure AD 设备配置

#### <a name="specify-who-can-join-devices-to-azure-ad"></a>指定可将设备加入到 Azure AD

在 Active Directory 中配置设备设置, 以允许管理安全组将设备加入到域。 若要从 Azure 门户配置此设置:

1. 请参阅**Azure Active Directory** > 设备 > ""**设备设置**"。
1. 选择 "**用户可以将设备加入 Azure AD** **" 下的**"选择", 然后选择 "安全工作站用户" 组。

#### <a name="removal-of-local-admin-rights"></a>删除本地管理权限

此方法要求 VIP、DevOps 和安全级别工作站的用户在其计算机上没有管理员权限。 若要从 Azure 门户配置此设置:

1. 请参阅**Azure Active Directory** > 设备 > ""**设备设置**"。
1. 在**Azure AD 联接设备上的其他本地管理员**下选择 "**无**"。

#### <a name="require-multi-factor-authentication-to-join-devices"></a>需要多重身份验证才能加入设备

进一步增强将设备加入 Azure AD 的过程:

1. 请参阅**Azure Active Directory** > 设备 > ""**设备设置**"。
1. 选择 "**需要多重身份验证来加入设备**" 下的 **"是"** 。
1. 选择**保存**。

#### <a name="configure-mdm"></a>配置 MDM

通过 Azure 门户：

1. 浏览到**Azure Active Directory** > **移动性 (MDM 和 MAM)**  > **Microsoft Intune**。
1. 将**MDM 用户作用域**设置更改为 "**全部**"。
1. 选择**保存**。

这些步骤允许你用 Intune 管理任何设备。 有关详细信息, 请[参阅 Intune 快速入门:设置 Windows 10 设备](https://docs.microsoft.com/Intune/quickstart-setup-auto-enrollment)的自动注册。 在以后的步骤中, 将创建 Intune 配置和合规性策略。

#### <a name="azure-ad-conditional-access"></a>Azure AD 条件性访问

Azure AD 条件性访问可帮助将特权管理任务限制为符合要求的设备。 登录到云应用程序时, 需要**安全工作站用户**组的预定义成员才能执行多重身份验证。 最佳做法是从策略中排除紧急访问帐户。 有关详细信息, 请参阅[管理 Azure AD 中的紧急访问帐户](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)。

若要配置 Azure 门户的条件访问:

1. 中转到**Azure Active Directory** > **条件访问** > **新策略**。
1. 输入：
   * **名称**-安全设备必需策略
   * 作业
     * 用户和组
       * 包括-**用户和组**-选择之前创建的 "**安全工作站用户**" 组。
       * 排除-**用户和组**-选择组织的紧急访问帐户。
     * **云应用**-包括**所有云应用**。
    * 访问控制
      * "**授予** **访问权限**" 单选按钮。
        * **需要多重身份验证**。
        * **要求设备标记为合规**。
        * 对于多个控件-**需要所有选定的控件**。
    * 启用策略启用 **。**

你可以选择创建阻止用户无法访问公司资源的国家/地区的策略。 有关基于 IP 位置的条件性访问策略的详细信息, 请参阅[Azure Active Directory 条件访问中的位置条件](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition)。

## <a name="intune-configuration"></a>Intune 配置

### <a name="configure-enrollment-status"></a>配置注册状态

确保安全工作站是可信的干净设备, 这一点很重要。 购买新设备时, 可以坚持将它们设置为[在 S 模式下出厂设置为 Windows 10 专业](https://docs.microsoft.com/Windows/deployment/Windows-10-pro-in-s-mode)版, 这会限制在供应链管理期间暴露于漏洞。 从供应商接收到设备后, 可以使用 Autopilot 在 S 模式下进行更改。 以下指南提供了有关应用转换过程的详细信息。

为了确保在使用之前对设备进行完全配置, Intune 提供了一种在**安装所有应用和配置文件之前阻止设备使用**的方法。

从 **Azure 门户**：
1. 请参阅**Microsoft Intune** > **设备注册** > **Windows 注册**注册状态页 > 默认设置 > 。 > 
1. 将 "**显示应用程序配置文件的安装进度**" 设置为 **"是"** 。
1. 设置**阻止设备使用, 直到将所有应用和配置文件安装**为 **"是"** 。

### <a name="create-an-autopilot-deployment-profile"></a>创建 Autopilot 部署配置文件

创建设备组后, 你必须创建一个部署配置文件来配置 Autopilot 设备。

在 Azure 门户中的 Intune 中:

1. 选择 "**设备注册** > " "**Windows 注册** > **部署配置** > 文件" "**创建配置文件**"
1. 输入：
   * 名称-**安全工作站部署配置文件**。
   * 说明-**安全工作站的部署**。
   * 将 "**所有目标设备转换为 Autopilot** " 设置为 **"是"** 。 此设置可确保列表中的所有设备都注册到 Autopilot 部署服务中。 允许处理注册的48小时。
1. 选择“**下一步**”。
   * 对于**部署模式**, 请选择 "**自部署 (预览版)** "。 具有此配置文件的设备将与注册该设备的用户关联。 注册设备需要用户凭据。 需要注意的是, 在**自我部署**模式下部署设备将允许你在共享模型中部署便携式计算机。 在第一次将设备分配给用户之前, 不会进行用户分配。 因此, 在完成用户分配之前, 将不会启用 BitLocker 等任何用户策略。 有关如何登录到安全设备的更多详细信息, 请参阅[所选配置文件](https://docs.microsoft.com/intune/device-profile-assign)。
   * "**联接 Azure AD 为**" 框应显示已**联接 Azure AD**并灰显。
   * 选择 Langugage (区域)、用户帐户类型 "**标准**"。 
1. 选择“**下一步**”。
   * 如果已预先配置了一个作用域标记, 请选择它。
1. 选择“**下一步**”。
1. 选择分配 > 给**所选组**的**分配** > 。 在 "**选择要包括的组**" 中, 选择 "**安全工作站用户**"。
1. 选择“**下一步**”。
1. 选择“创建”以创建该配置文件。 现在可以将 Autopilot 部署配置文件分配给设备。

Autopilot 中的设备注册根据设备类型和角色提供不同的用户体验。 在我们的部署示例中, 我们将演示一个模型, 其中的安全设备是大容量部署的并且可以共享, 但当首次使用时, 设备会分配给用户。 有关详细信息, 请参阅[Intune Autopilot 设备注册](https://docs.microsoft.com/intune/device-enrollment)。

### <a name="configure-windows-update"></a>配置 Windows 更新

将 Windows 10 保持最新状态是你可以执行的最重要的任务之一。 若要以安全状态维护 Windows, 请部署更新环, 以管理将更新应用到工作站的节奏。 

本指南建议创建新的更新环, 并更改以下默认设置:

在 Azure 门户中：

1. 请参阅**Microsoft Intune** > **软件更新** > **Windows 10 更新环**。
1. 输入：
   * 名称- **Azure 托管工作站更新**
   * 维护渠道- **Windows 有问必答-快速**
   * 质量更新延迟 (天)- **3**
   * 功能更新延迟期 (天)- **3**
   * 自动更新行为-**无需最终用户控制即可自动安装和重新启动**
   * 阻止用户暂停 Windows 更新-**阻止**
   * 要求用户批准在工作时间之外重新启动-**必需**
   * 允许用户重新启动 (已重新启动)-**必需**
   * 自动重新启动后将用户切换到预定重启 (天)- **3**
   * 暂停预定重启提醒 (天)- **3**
   * 设置等待重启的截止时间 (天)- **3**

1. 选择“创建”。
1. 在 "**分配**" 选项卡上, 添加 "**安全工作站**" 组。

有关 Windows 更新策略的其他信息, 请参阅[策略 CSP-更新](https://docs.microsoft.com/windows/client-management/mdm/policy-csp-update)。

### <a name="windows-defender-atp-intune-integration"></a>Windows Defender ATP Intune 集成

Windows Defender ATP 和 Microsoft Intune 一起工作以帮助防止安全漏洞。 它们还可以限制违规造成的影响。 ATP 功能提供实时威胁检测, 并对终结点设备启用广泛的审核和日志记录。

若要配置 Windows Defender ATP 和 Intune 的集成, 请参阅 Azure 门户。

1. 浏览到**Microsoft Intune** > **设备符合性** > **Windows Defender ATP**。
1. 在步骤1中, 在**配置 Windows DEFENDER atp**下, 选择 **"将 Windows defender ATP 连接到 Windows defender 安全中心 Microsoft Intune"** 。
1. 在 Windows Defender 安全中心:
   1. 选择 "**设置** > " "**高级功能**"。
   1. 对于 " **Microsoft Intune 连接**", 请选择 **"打开"** 。
   1. 选择 "**保存首选项**"。
1. 建立连接后, 返回到 Intune, 并选择顶部的 "**刷新**"。
1. 将 "**连接 windows 设备版本 10.0.15063" 和更高版本设置为 "Windows DEFENDER ATP** **"** 。
1. 选择**保存**。

有关详细信息, 请参阅[Windows Defender 高级威胁防护](https://docs.microsoft.com/Windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection)。

### <a name="finish-workstation-profile-hardening"></a>完成工作站配置文件强化

若要成功完成解决方案的强化, 请下载并执行相应的脚本。 查找所需**配置文件级别**的下载链接:

| 配置文件 | 下载位置 | 文件名 |
| --- | --- | --- |
| 低安全性 | 不可用 |  不可用 |
| 增强的安全性 | https://aka.ms/securedworkstationgit | 增强-Windows 10-(1809). ps1 |
| 高安全性  | https://aka.ms/securedworkstationgit | HighSecurityWorkstation-Windows 10-(1809). ps1 |
| 特殊化 | https://github.com/pelarsen/IntunePowerShellAutomation | DeviceConfiguration_NCSC-Windows 10 (1803) SecurityBaseline |
| 专用符合性 * | https://aka.ms/securedworkstationgit | DeviceCompliance_NCSC-Windows10(1803).ps1 |
| 安全 | https://aka.ms/securedworkstationgit | Secure-Workstation-Windows10-(1809)-SecurityBaseline.ps1 |

\*专用符合性是强制执行 NCSC Windows 10 SecurityBaseline 中提供的专用配置的脚本。

成功执行脚本后, 你可以在 Intune 中对配置文件和策略进行更新。 增强和安全配置文件的脚本会为你创建策略和配置文件, 但必须将策略分配到**安全工作站**组。

* 可在以下位置找到由脚本创建的 Intune 设备配置文件:**Azure 门户** **Microsoft Intune 设备**配置的配置文件 > 。 >  > 
* 可在以下位置找到由脚本创建的 Intune 设备符合性策略:**Azure 门户** **Microsoft Intune 设备符合**性策略 > 。 >  > 

若要查看脚本所做的更改, 可以导出配置文件。 这样, 你就可以根据[SECCON 文档](https://docs.microsoft.com/windows/security/threat-protection/windows-security-configuration-framework/windows-security-configuration-framework)中所述, 确定可能需要的其他强化。

从[DeviceConfiguration GiuHub 存储库](https://github.com/microsoftgraph/powershell-intune-samples/tree/master/DeviceConfiguration)运行`DeviceConfiguration_Export.ps1` Intune 数据导出脚本, 以导出所有当前 Intune 配置文件。

## <a name="additional-configurations-and-hardening-to-consider"></a>需要考虑的其他配置和强化

按照此处的指南, 你已部署了安全工作站。 但是, 还应考虑其他控件。 例如：

* 限制对备用浏览器的访问
* 允许出站 HTTP
* 阻止选择网站
* 设置运行自定义 PowerShell 脚本的权限

### <a name="set-rules-in-the-firewall-configuration-service-provider-csp"></a>在防火墙配置服务提供程序 (CSP) 中设置规则

你可以根据你允许的和阻止的终结点的需要, 对入站和出站规则的管理进行其他更改。 继续强化安全工作站时, 可以放宽拒绝所有入站和出站流量的限制。 你可以添加允许的出站站点以包含通用和受信任的网站。 有关详细信息, 请参阅[防火墙配置服务](https://docs.microsoft.com/Windows/client-management/mdm/firewall-csp)。

默认限制的建议如下:

* 拒绝所有入站
* 拒绝所有出站

Spamhaus 项目维护[域块列表 (双)](https://www.spamhaus.org/dbl/): 用作阻止站点起点的良好资源。

### <a name="manage-local-applications"></a>管理本地应用程序

如果删除了本地应用程序 (包括生产力应用程序), 则安全工作站会移动到真正的强制性状态。 此处, 将 Chrome 添加为默认浏览器, 并使用 Intune 限制用户修改浏览器及其插件的功能。

#### <a name="deploy-applications-using-intune"></a>使用 Intune 部署应用程序

在某些情况下, 安全工作站上需要诸如 Google Chrome 浏览器之类的应用程序。 以下示例说明了如何将 Chrome 安装到安全组安全**工作站**中的设备上。

1. 下载[适用于 Windows 64 位](https://cloud.google.com/chrome-enterprise/browser/download/)的脱机安装程序 Chrome 捆绑包。
1. 提取文件并记下`GoogleChromeStandaloneEnterprise64.msi`文件的位置。
1. 在**Azure 门户**浏览到**Microsoft Intune** > **客户端应用** > "**应用** > **添加**。
1. 在 "**应用类型**" 下, 选择 "**业务线**"。
1. 在 "**应用包文件**" 下`GoogleChromeStandaloneEnterprise64.msi` , 从提取的位置选择文件, 然后选择 **"确定"** 。
1. 在 "**应用信息**" 下, 提供说明和发布者。 选择“确定”。
1. 选择 **添加** 。
1. 在 "**分配**" 选项卡上, 选择 "可用于**分配类型** **的已注册设备**"。
1. 在 "**包含的组**" 下, 添加**安全工作站**组。
1. 选择“确定”，然后选择“保存”。

有关配置 Chrome 设置的更多指导, 请参阅[管理 Chrome Browser with Microsoft Intune](https://support.google.com/chrome/a/answer/9102677)。

#### <a name="configuring-the-company-portal-for-custom-apps"></a>配置自定义应用的公司门户

在安全模式下, 应用程序安装仅限于 Intune 公司门户。 但是, 安装门户需要访问 Microsoft Store。 在受保护的解决方案中, 可以通过脱机模式向所有设备提供公司门户。

[公司门户](https://docs.microsoft.com/Intune/store-apps-company-portal-app)的 Intune 管理的副本使你可以按需访问可推送到受保护工作站用户的其他工具。

你可能需要安装 Windows 32 位应用或部署需要特别准备的其他应用。 在这种情况下, [Microsoft win32 内容准备工具](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool)可以提供随时可用`.intunewin`的格式化文件进行安装。

### <a name="use-powershell-to-create-custom-settings"></a>使用 PowerShell 创建自定义设置

你还可以使用 PowerShell 来扩展主机管理功能。 此示例脚本在主机上设置默认背景。 它也是一种通过 Azure 门户和配置文件提供的功能。 示例脚本仅用于说明 PowerShell 功能。

你可能需要在安全工作站上设置一些自定义控件和设置。 此示例使用 Powershell 将设备识别为可用、安全工作站的功能, 来更改工作站的背景。

Microsoft 脚本中心的[SetDesktopBackground](https://gallery.technet.microsoft.com/scriptcenter/Set-Desktop-Image-using-5430c9fb/)脚本允许 Windows 在启动时加载此[普通的通用背景图像](https://i.imgur.com/OAJ28zO.png)。

1. 将脚本下载到本地设备。
1. 更新背景图像的 customerXXXX 和下载位置。 在我们的示例中, 我们将 customerXXXX 替换为背景。  
1.  > 浏览到**Microsoft Intune** **设备配置** **"PowerShell 脚本** 添加 > 的Azure门户。 >  > 
1. 提供脚本的**名称**, 并指定**脚本位置**。
1. 选择“配置”。
   1. 设置 "**使用登录凭据运行此脚本** **"** 。
   1. 选择“确定”。
1. 选择“创建”。
1. 选择**分配** > **选择组**。
   1. 添加安全组安全**工作站**。
   1. 选择**保存**。

## <a name="enroll-and-validate-your-first-device"></a>注册并验证你的第一个设备

1. 若要注册你的设备, 你需要以下信息:
   * **序列号**-在设备底盘上找到。
   * **Windows 产品 ID** -在 "windows 设置"**菜单中的**"**系统** > " 下找到。
   * 您可以运行[WindowsAutoPilotInfo](https://aka.ms/Autopilotshell)以获取 CSV 哈希文件, 其中包含设备注册所需的所有信息。
   
     运行`Get-WindowsAutoPilotInfo – outputfile device1.csv`以将信息输出为 CSV 文件, 你可以将该文件导入到 Intune。

     > [!NOTE]
     > 此脚本需要提升的权限。 它以远程签名的方式运行。 `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned`命令使脚本正常运行。

   * 可以通过登录到 Windows 10 版本1809或更高版本的设备来收集此信息。 硬件经销商还可以提供此信息。
1. 在**Azure 门户**中, 请参阅**Microsoft Intune** > **设备注册** > **windows 注册** > **设备-管理 windows Autopilot 设备**。
1. 选择 "**导入**" 并选择 CSV 文件。
1. 将设备添加到**安全工作站**安全组。
1. 在要配置的 windows 10 设备上, 打开 " **windows 设置** > **更新 & 安全** > **恢复**"。
   1. 选择 "**重置此电脑**" 下的 "**开始**"。
   1. 按照提示操作, 通过配置的配置文件和合规性策略重置并重新配置设备。

配置设备后, 请完成检查并检查配置。 在继续进行部署之前, 请确认第一个设备配置正确。

## <a name="assign-and-monitor"></a>分配和监视

要分配设备和用户, 需要将[所选配置文件](https://docs.microsoft.com/intune/device-profile-assign)映射到安全组。 还必须将需要服务权限的所有新用户添加到安全组。

可以通过[Intune 配置文件监视](https://docs.microsoft.com/intune/device-profile-monitor)来监视配置文件。

## <a name="next-steps"></a>后续步骤

* 详细了解[Microsoft Intune](https://docs.microsoft.com/intune/index)。
* 了解[Azure AD](https://docs.microsoft.com/azure/active-directory/index)。
