---
title: 部署 Azure 托管的工作站的 Azure Active Directory
description: 了解如何部署安全的 Azure 托管的工作站，以减少由于配置错误或者泄漏的漏洞的风险。
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
ms.openlocfilehash: 51d8bbc8b8be9679fbf024d7c51de53c430dc493
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2019
ms.locfileid: "67550484"
---
# <a name="deploy-a-secure-azure-managed-workstation"></a>部署安全的 Azure 托管的工作站

现在，你[了解安全工作站](concept-azure-managed-workstation.md)，就可以开始部署过程。 本指南中，你可以使用定义配置文件创建从一开始更安全的工作站。

![安全的工作站的部署](./media/howto-azure-managed-workstation/deploying-secure-workstations.png)

在可以部署解决方案之前，必须选择一个配置文件。 可以在部署中同时使用多个配置文件，并向它们分配标记或组。
> [!NOTE]
> 根据需要由您的要求，请应用任何配置文件。 可以通过将其分配在 Intune 中移动到另一个配置文件。

| 配置文件 | 低 | 增强版 | 高 | 专用 | 保护 | 隔离 |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Azure AD 中的用户 | 是 | 是 | 是 | 是 | 是 | 是 |
| Intune 托管 | 是 | 是 | 是 | 是 | 是 | 是 |
| 设备的已注册 Azure AD | 是 |  |  |  |  | |   |
| 设备-已加入 Azure AD |   | 是 | 是 | 是 | 是 | 是 |
| Intune 应用的安全基准 |   | 是 <br> （增强） | 是 <br> (HighSecurity) | 是 <br> (NCSC) | 是 <br> （安全） |  NA |
| 硬件满足安全 Windows 10 标准 |   | 是 | 是 | 是 | 是 | 是 |
| 启用 Microsoft Defender ATP |   | 是  | 是 | 是 | 是 | 是 |
| 删除的管理权限 |   |   | 是  | 是 | 是 | 是 |
| 使用 Microsoft Autopilot 部署 |   |   | 是  | 是 | 是 | 是 |
| 仅由 Intune 安装的应用 |   |   |   | 是 | 是 |是 |
| 限制为已批准列表的 Url |   |   |   | 是 | 是 |是 |
| Internet 阻止 （入站/出站） |   |   |   |  |  |是 |

## <a name="license-requirements"></a>许可要求

本指南中介绍的概念假定您有 Microsoft 365 企业版 E5 或等效的 SKU。 可以使用较低的 Sku 实现一些本指南中的建议。 有关详细信息，请参阅[Microsoft 365 企业版许可](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise)。

若要自动执行许可证预配，请考虑[基于组的许可](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign)为你的用户。

## <a name="azure-active-directory-configuration"></a>Azure Active Directory 配置

Azure Active Directory (Azure AD) 管理用户、 组和管理员工作站的设备。 需要启用标识服务和功能[管理员帐户](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)。

创建受保护的工作站管理员帐户时，您公开该帐户当前工作站。 请确保使用已知的安全设备执行此初始配置和所有全局配置。 若要减小的首次体验攻击可能性，请考虑以下[指南，防止恶意软件感染](https://docs.microsoft.com/windows/security/threat-protection/intelligence/prevent-malware-infection)。

你还应该为你的管理员至少要求多重身份验证。 请参阅[部署基于云的 MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)有关实现的指南。

### <a name="azure-ad-users-and-groups"></a>Azure AD 用户和组

1. 从 Azure 门户中，浏览到**Azure Active Directory** > **用户** > **新用户**。
1. 按照中的步骤创建您的设备管理员[创建用户教程](https://docs.microsoft.com/Intune/quickstart-create-user)。
1. 输入：
   * **名称**-安全工作站管理员
   * **用户名称** - `secure-ws-admin@identityitpro.com`
   * **目录角色** - **受限制的管理员**，然后选择**Intune 管理员**角色。
1. 选择“创建”  。

接下来，创建两个组： 工作站的用户和工作站设备。

从 Azure 门户中，浏览到**Azure Active Directory** > **组** > **新组**。

1. 对于工作站的用户组，你可能想要配置[基于组的许可](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign)自动预配许可证分配给用户。
1. 对于工作站的用户组中，输入：
   * **组类型**-安全
   * **组名称**-保护工作站的用户
   * **成员资格类型**-分配
1. 将安全的工作站管理员用户添加： `secure-ws-admin@identityitpro.com`
1. 可以添加要管理安全工作站的任何其他用户。
1. 选择“创建”  。

1. 对于工作站设备组，请输入：
   * **组类型**-安全
   * **组名称**-保护工作站
   * **成员资格类型**-分配
1. 选择“创建”  。

### <a name="azure-ad-device-configuration"></a>Azure AD 设备配置

#### <a name="specify-who-can-join-devices-to-azure-ad"></a>指定谁可以将设备加入到 Azure AD

在 Active Directory 中设置以允许你管理的安全组，以将设备加入到你的域的设备配置。 若要配置此设置从 Azure 门户：

1. 转到**Azure Active Directory** > **设备** > **设备设置**。
1. 选择**Selected**下**用户可能会将设备加入到 Azure AD**，然后选择"保护工作站用户"组。

#### <a name="removal-of-local-admin-rights"></a>删除的本地管理员权限

此方法要求的 VIP、 DevOps 和安全级别工作站用户计算机上具有任何管理员权限。 若要配置此设置从 Azure 门户：

1. 转到**Azure Active Directory** > **设备** > **设备设置**。
1. 选择**无**下**其他本地管理员的 Azure ad 加入设备**。

#### <a name="require-multi-factor-authentication-to-join-devices"></a>需要多重身份验证才能加入设备

若要进一步加强到 Azure AD 加入设备的过程：

1. 转到**Azure Active Directory** > **设备** > **设备设置**。
1. 选择**是**下**需要 Multi-factor Auth 才能加入设备**。
1. 选择“保存”。 

#### <a name="configure-mdm"></a>配置 MDM

通过 Azure 门户：

1. 浏览到**Azure Active Directory** > **移动性 （MDM 和 MAM）**  > **Microsoft Intune**。
1. 更改**MDM 用户作用域**将设置为**所有**。
1. 选择“保存”。 

这些步骤可让你管理使用 Intune 在任何设备。 有关详细信息，请参阅[Intune 快速入门：设置适用于 Windows 10 设备自动注册](https://docs.microsoft.com/Intune/quickstart-setup-auto-enrollment)。 以后的步骤中创建 Intune 配置和符合性策略。

#### <a name="azure-ad-conditional-access"></a>Azure AD 条件访问

Azure AD 条件访问可帮助限制对符合条件的设备的特权的管理任务。 预定义的成员**保护工作站用户**执行多重身份验证登录到云的应用程序时所需的组。 最佳做法是从策略中排除紧急访问帐户。 有关详细信息，请参阅[在 Azure AD 中管理紧急访问帐户](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)。

若要从 Azure 门户中配置条件性访问：

1. 转到**Azure Active Directory** > **条件性访问** > **新策略**。
1. 输入：
   * **名称**-安全的设备所需的策略
   * 分配
     * 用户和组 
       * 包括-**用户和组**-选择**保护工作站用户**前面创建的组。
       * 中排除**用户和组**-选择你的组织的紧急访问帐户。
     * **云应用**-包括**所有云应用**。
    * 访问控制
      * **授予**-选择**授予访问权限**单选按钮。
        * **需要多重身份验证**。
        * **要求设备标记为兼容**。
        * 为多个控件的**需要所有已选的控件**。
    * 启用策略-**上**。

您可以选择创建策略，阻止用户将无法访问公司资源的国家/地区。 有关 IP 基于位置的条件性访问策略的详细信息，请参阅[Azure Active Directory 条件访问中的位置条件](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition)。

## <a name="intune-configuration"></a>Intune 配置

### <a name="configure-enrollment-status"></a>配置注册状态

请务必确保安全工作站是一个干净的受信任的设备。 购买新的设备，您可以坚持，它们是出厂设置为[Windows 10 专业版 S 模式](https://docs.microsoft.com/Windows/deployment/Windows-10-pro-in-s-mode)，这在供应链管理过程就限制了漏洞的风险。 从您的供应商收到设备后，可以使用 Autopilot 以将其从 S 模式。 以下指南提供有关应用转换过程的详细信息。

若要确保设备完全配置使用之前，Intune 提供的一种手段**阻止设备，请使用安装的所有应用和配置文件之前**。

从 **Azure 门户**：
1. 转到**Microsoft Intune** > **设备注册** > **Windows 注册** > **注册状态页** > **默认** > **设置**。
1. 设置**显示应用程序配置文件安装进度**到**是**。
1. 设置**阻止设备，请使用安装的所有应用和配置文件之前**到**是**。

### <a name="create-an-autopilot-deployment-profile"></a>创建 Autopilot 部署配置文件

创建设备组之后，必须创建用于配置 Autopilot 设备的部署配置文件。

在 Azure 门户中 Intune:

1. 选择**设备注册** > **Windows 注册** > **部署配置文件** > **创建配置文件**.
1. 输入：
   * 名称-**保护工作站部署配置文件**。
   * 说明-**部署安全工作站**。
   * 设置**将所有目标的设备转换为 Autopilot**到**是**。 此设置可确保列表中的所有设备获取向 Autopilot 部署服务都注册。 允许 48 小时内进行注册以便进行处理。
1. 选择“**下一步**”。
   * 有关**部署模式**，选择**自部署 （预览）** 。 设备与此配置文件与相关联的用户的注册设备。 注册设备需要用户凭据。
   * **加入 Azure AD**框应显示**已加入 Azure AD**和灰显。
   * 选择你 Langugage （区域），用户帐户类型**标准**。 
1. 选择“**下一步**”。
   * 如果您预先配置了一个，请选择作用域标记。
1. 选择“**下一步**”。
1. 选择**分配** > **将分配给** > **所选组**。 在中**选择要包括的组**，选择**保护工作站用户**。
1. 选择“**下一步**”。
1. 选择“创建”  以创建该配置文件。 现可用于向设备分配 Autopilot 部署配置文件。

### <a name="configure-windows-update"></a>配置 Windows 更新

保持最新 Windows 10 是可以做的最重要事情之一。 若要维护 Windows 处于安全状态，在部署更新通道管理速度更新应用到工作站。 

本指南建议您创建新的更新通道，并更改以下默认设置：

在 Azure 门户中：

1. 转到**Microsoft Intune** > **软件更新** > **Windows 10 更新圈**。
1. 输入：
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

1. 选择“创建”  。
1. 上**分配**选项卡上，添加**保护工作站**组。

有关 Windows 更新策略的其他信息，请参阅[策略 CSP-更新](https://docs.microsoft.com/windows/client-management/mdm/policy-csp-update)。

### <a name="windows-defender-atp-intune-integration"></a>Windows Defender ATP Intune 集成

Windows Defender ATP 和 Microsoft Intune 协同工作以帮助防止安全漏洞的威胁。 它们还可以限制漏洞的影响。 ATP 功能提供实时的威胁检测以及启用丰富的审核和日志记录的终结点设备。

若要配置 Windows Defender ATP 与 Intune 集成，请转到 Azure 门户。

1. 浏览到**Microsoft Intune** > **设备符合性** > **Windows Defender ATP**。
1. 在步骤 1 下面**配置 Windows Defender ATP**，选择**将 Windows Defender ATP 连接 Windows Defender 安全中心中的 Microsoft Intune**。
1. 在 Windows Defender 安全中心：
   1. 选择**设置** > **高级功能**。
   1. 有关**Microsoft Intune 连接**，选择**上**。
   1. 选择**保存首选项**。
1. 建立连接后，返回到 Intune，然后选择**刷新**顶部。
1. 设置**连接的 Windows 设备版本 10.0.15063 及更高版本到 Windows Defender ATP**到**上**。
1. 选择“保存”。 

有关详细信息，请参阅[Windows Defender 高级威胁防护](https://docs.microsoft.com/Windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection)。

### <a name="finish-workstation-profile-hardening"></a>完成强化的工作站配置文件

若要成功完成解决方案的强化，下载并执行相应的脚本。 查找所需的下载链接**配置文件级别**:

| 配置文件 | 下载位置 | 文件名 |
| --- | --- | --- |
| 低安全性 | 不适用 |  不适用 |
| 增强的安全性 | https://aka.ms/securedworkstationgit | 增强的工作站的 Windows10-(1809).ps1 |
| 高安全性  | https://aka.ms/securedworkstationgit | HighSecurityWorkstation-Windows10-(1809).ps1 |
| 专用 | https://github.com/pelarsen/IntunePowerShellAutomation | DeviceConfiguration_NCSC - Windows10 (1803) SecurityBaseline.ps1 |
| 专用法规遵从性 * | https://aka.ms/securedworkstationgit | DeviceCompliance_NCSC-Windows10(1803).ps1 |
| 保护 | https://aka.ms/securedworkstationgit | Secure-Workstation-Windows10-(1809)-SecurityBaseline.ps1 |

\* 专用的符合性是强制实施 NCSC Windows10 SecurityBaseline 中提供的专用的配置的脚本。

已成功执行该脚本后，您可以对配置文件和策略在 Intune 中的进行更新。 增强和安全配置文件的脚本创建策略和配置文件，但必须将分配到的策略，你**保护工作站**组。

* 下面是在哪里可以找到由脚本创建的 Intune 设备配置文件：**Azure 门户** > **Microsoft Intune** > **设备配置** > **配置文件**。
* 下面是可以在其中找到 Intune 设备符合性策略创建的脚本：**Azure 门户** > **Microsoft Intune** > **设备符合性** > **策略**。

若要查看由脚本所做的更改，您可以导出配置文件。 这种方式可以确定所需的 SECCON 文档中所述的其他强化。

运行 Intune 数据导出脚本`DeviceConfiguration_Export.ps1`从[DeviceConfiguration GiuHub 存储库](https://github.com/microsoftgraph/powershell-intune-samples/tree/master/DeviceConfiguration)导出所有当前 Intune 配置文件。

## <a name="additional-configurations-and-hardening-to-consider"></a>其他配置和强化考虑

按照此处的指南，现已部署工作站的安全。 但是，您还应考虑其他控件。 例如：

* 限制对备用的浏览器访问
* 允许出站 HTTP
* 块选择网站
* 为运行自定义 PowerShell 脚本设置权限

### <a name="set-rules-in-the-firewall-configuration-service-provider-csp"></a>在防火墙配置服务提供商 (CSP) 中设置规则

根据需要为你允许和阻止的终结点，可以对入站和出站规则的管理进行其他更改。 在继续强化安全的工作站，可以放宽拒绝所有入站和出站流量的限制。 你可以添加允许的出站站点，以包括公共和受信任的网站。 有关详细信息，请参阅[防火墙配置服务](https://docs.microsoft.com/Windows/client-management/mdm/firewall-csp)。

默认受限制的建议如下：

* 拒绝所有入站
* 拒绝所有出站

Spamhaus 项目维持[域块列表 （双）](https://www.spamhaus.org/dbl/)： 要作为起始点，用于阻止站点的良好资源。

### <a name="manage-local-applications"></a>管理本地应用程序

删除本地应用程序，包括工作效率应用程序时，安全工作站将移到真正强化状态。 在这里，您将 Chrome 添加为默认浏览器和使用 Intune 来限制用户可以修改浏览器和其插件。

#### <a name="deploy-applications-using-intune"></a>使用 Intune 部署应用程序

在某些情况下，应用程序，如 Google Chrome 浏览器需要在受保护的工作站上。 下面的示例介绍如何向安全组中的设备安装 Chrome**保护工作站**。

1. 下载脱机安装程序[Chrome 捆绑包的 Windows 64‑bit](https://cloud.google.com/chrome-enterprise/browser/download/)。
1. 提取文件，并记下的位置`GoogleChromeStandaloneEnterprise64.msi`文件。
1. 在中**Azure 门户**浏览到**Microsoft Intune** > **客户端应用程序** > **应用** > **添加**。
1. 下**应用类型**，选择**业务线**。
1. 下**应用包文件**，选择`GoogleChromeStandaloneEnterprise64.msi`文件从提取的位置，然后选择**确定**。
1. 下**应用信息**，提供的说明和发布服务器。 选择“确定”  。
1. 选择 **添加** 。
1. 上**分配**选项卡上，选择**适用于已注册的设备**下**分配类型**。
1. 下**包括的组**，添加**保护工作站**组。
1. 选择“确定”，然后选择“保存”   。

有关配置 Chrome 设置的更多指导，请参阅[使用 Microsoft Intune 管理 Chrome 浏览器](https://support.google.com/chrome/a/answer/9102677)。

#### <a name="configuring-the-company-portal-for-custom-apps"></a>配置自定义应用在公司门户

在安全模式下，应用程序的安装被限制到 Intune 公司门户。 但是，安装门户需要访问 Microsoft Store。 在受保护解决方案中，可以让通过脱机模式下的所有设备可用的公司门户。

Intune 托管的副本[公司门户](https://docs.microsoft.com/Intune/store-apps-company-portal-app)可用于按需访问其他工具，您可以向下推送到受保护的工作站的用户。

您可能需要安装 Windows 32 位应用程序或其他应用的部署需要特殊的准备工作。 在这种情况下， [Microsoft win32 内容准备工具](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool)可以提供随时可用`.intunewin`格式文件进行安装。

### <a name="use-powershell-to-create-custom-settings"></a>使用 PowerShell 创建自定义设置

此外可以使用 PowerShell 来扩展管理功能的主机。 此示例脚本设置在主机上的默认背景。 它是一种功能，它也可通过 Azure 门户和配置文件。 示例脚本仅用于演示 PowerShell 功能。

您可能需要在安全工作站上设置了一些自定义控件和设置。 此示例使用 Powershell 的功能可以轻松地识别该设备是随时可用的安全工作站更改背景的工作站。

[SetDesktopBackground.ps1](https://gallery.technet.microsoft.com/scriptcenter/Set-Desktop-Image-using-5430c9fb/)从 Microsoft 脚本中心脚本允许 Windows 加载这[免费的通用背景图像](https://i.imgur.com/OAJ28zO.png)上启动。

1. 将脚本下载到本地设备。
1. 更新 customerXXXX 和背景图像的下载位置。 在本示例中，我们将为 customerXXXX 到背景。  
1. 浏览到**Azure 门户** > **Microsoft Intune** > **设备配置** > **PowerShell脚本** > **添加**。
1. 提供**名称**脚本并指定**脚本位置**。
1. 选择“配置”  。
   1. 设置**运行此脚本使用登录凭据**到**是**。
   1. 选择“确定”  。
1. 选择“创建”  。
1. 选择**分配** > **选择组**。
   1. 添加安全组**保护工作站**。
   1. 选择“保存”。 

## <a name="enroll-and-validate-your-first-device"></a>注册并验证你的第一个设备

1. 若要注册设备，需要以下信息：
   * **序列号**-设备机箱上找到。
   * **Windows 产品 ID** -下找到**系统** > **有关**从 Windows 设置菜单。
   * 你可以运行[Get WindowsAutoPilotInfo](https://aka.ms/Autopilotshell)获取设备注册的所有所需的信息的 CSV 哈希文件。
   
     运行`Get-WindowsAutoPilotInfo – outputfile device1.csv`，您可以导入到 Intune 的 CSV 文件作为将信息输出。

     > [!NOTE]
     > 该脚本需要提升的权限。 运行为远程签名。 `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned`命令使脚本正常运行。

   * 可以通过登录到 Windows 10 版本 1809年或更高版本的设备收集此信息。 硬件分销商还可以提供此信息。
1. 在中**Azure 门户**，请转到**Microsoft Intune** > **设备注册** > **Windows 注册** > **设备-管理 Windows Autopilot 设备**。
1. 选择**导入**并选择 CSV 文件。
1. 添加到设备**保护工作站**安全组。
1. 在你想要配置 Windows 10 设备，请转到**Windows 设置** > **更新和安全** > **恢复**。
   1. 选择**开始**下**重置此电脑**。
   1. 按照提示重置，并使用配置的配置文件和符合性策略重新配置设备。

配置设备后，完成评审并检查配置。 确认继续部署之前正确配置的第一个设备。

## <a name="assign-and-monitor"></a>分配和监视

若要分配的设备和用户，需要将映射[选择配置文件](https://docs.microsoft.com/intune/device-profile-assign)到安全组。 所有新用户需要对服务的权限必须添加到安全组以及。

您可以使用监视配置文件[Intune 配置文件监视](https://docs.microsoft.com/intune/device-profile-monitor)。

## <a name="next-steps"></a>后续步骤

* 详细了解如何[Microsoft Intune](https://docs.microsoft.com/intune/index)。
* 了解[Azure AD](https://docs.microsoft.com/azure/active-directory/index)。
