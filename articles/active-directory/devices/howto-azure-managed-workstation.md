---
title: 部署 Azure 管理的工作站 - Azure 活动目录
description: 了解如何部署安全、Azure 管理的工作站，以降低由于配置错误或危害而导致的违规风险。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672610"
---
# <a name="deploy-a-secure-azure-managed-workstation"></a>部署安全的 Azure 管理工作站

现在，您[已经了解了安全工作站](concept-azure-managed-workstation.md)，是时候开始部署了。 使用本指南，您可以使用定义的配置文件创建从一开始就更安全的工作站。

![部署安全工作站](./media/howto-azure-managed-workstation/deploying-secure-workstations.png)

在部署解决方案之前选择配置文件。 您可以在部署中同时使用多个配置文件，并使用标记或组分配它们。

> [!NOTE]
> 根据您的要求应用任何配置文件。 您可以通过在 Microsoft Intune 中分配它来移动到另一个配置文件。

| 配置文件 | 低 | 增强版 | 高 | 专用 | 担保 | 隔离 |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Azure AD 中的用户 | 是 | 是 | 是 | 是 | 是 | 是 |
| Intune 托管 | 是 | 是 | 是 | 是 | 是 | 是 |
| 设备 - 已注册的 Azure AD | 是 |  |  |  |  | |   |
| 设备 - Azure AD 已加入 |   | 是 | 是 | 是 | 是 | 是 |
| 应用了 Intune 安全基线 |   | 是 <br> （增强） | 是 <br> （高安全性） | 是 <br> （NCSC） | 是 <br> （安全） | NA |
| 硬件符合安全 Windows 10 标准 |   | 是 | 是 | 是 | 是 | 是 |
| 微软后卫ATP启用 |   | 是  | 是 | 是 | 是 | 是 |
| 删除管理权限 |   |   | 是  | 是 | 是 | 是 |
| 使用微软自动驾驶仪进行部署 |   |   | 是  | 是 | 是 | 是 |
| 仅由 Intune 安装的应用 |   |   |   | 是 | 是 |是 |
| 仅限于已批准列表的 URL |   |   |   | 是 | 是 |是 |
| 互联网被阻止（入站/出站） |   |   |   |  |  |是 |

> [!NOTE]
> 在安全**工作站中，** 将分配带有配置文件和策略的设备。 用户不会直接应用策略，从而允许设备共享（共享设备）生效。 如果在部署中未共享安全工作站，或者需要单个用户策略，则可以将用户策略配置文件的分配分配给用户和设备。 

## <a name="license-requirements"></a>许可要求

本指南中介绍的概念假定您拥有 Microsoft 365 企业版 E5 或等效 SKU。 本指南中的一些建议可以使用较低的 SKU 实现。 有关详细信息，请参阅[Microsoft 365 企业许可](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise)。

要自动进行许可证预配，请考虑为用户[提供基于组的许可](../users-groups-roles/licensing-groups-assign.md)。

## <a name="azure-active-directory-configuration"></a>Azure 活动目录配置

Azure 活动目录 （Azure AD） 管理管理员工作站的用户、组和设备。 使用[管理员帐户](../users-groups-roles/directory-assign-admin-roles.md)启用标识服务和功能。

创建安全的工作站管理员帐户时，会将该帐户公开给当前工作站。 请确保使用已知安全设备执行此初始配置和所有全局配置。 为了减少首次体验的攻击暴露，请考虑遵循[指南以防止恶意软件感染](/windows/security/threat-protection/intelligence/prevent-malware-infection)。

需要多重身份验证，至少对于管理员而言。 有关实施指南[，请参阅部署基于云的 MFA。](../authentication/howto-mfa-getstarted.md)

### <a name="azure-ad-users-and-groups"></a>Azure AD 用户和组

1. 从 Azure 门户，浏览到**Azure 活动目录** > **用户** > **新用户**。
1. 按照[创建用户教程](/Intune/quickstart-create-user)中的步骤创建设备管理员。
1. 输入：

   * **名称**- 安全工作站管理员
   * **用户名** - `secure-ws-admin@identityitpro.com`
   * **目录角色** - **受限管理员**并选择**Intune 管理员**角色。

1. 选择 **“创建”**。

接下来，您将创建两个组：工作站用户和工作站设备。

从 Azure 门户，浏览到**Azure 活动目录** > **组** > **"新组**"。

1. 对于工作站用户组，您可能希望配置[基于组的许可](../users-groups-roles/licensing-groups-assign.md)，以自动向用户预配许可证。
1. 对于工作站用户组，输入：

   * **组类型**- 安全性
   * **组名称**- 安全工作站用户
   * **成员资格类型**- 已分配

1. 添加安全工作站管理员用户：`secure-ws-admin@identityitpro.com`
1. 您可以添加将管理安全工作站的任何其他用户。
1. 选择 **“创建”**。
1. 对于工作站设备组，输入：

   * **组类型**- 安全性
   * **组名称**- 安全工作站
   * **成员资格类型**- 已分配

1. 选择 **“创建”**。

### <a name="azure-ad-device-configuration"></a>Azure AD 设备配置

#### <a name="specify-who-can-join-devices-to-azure-ad"></a>指定谁可以将设备加入 Azure AD

在 Active Directory 中配置设备设置，以允许管理安全组将设备加入域。 要从 Azure 门户配置此设置，请执行以下规定：

1. 转到**Azure 活动目录** > **设备** > **设备设置**。
1. 选择"用户"下的 **"选择****"可以将设备加入 Azure AD**，然后选择"安全工作站用户"组。

#### <a name="removal-of-local-admin-rights"></a>删除本地管理员权限

此方法要求 VIP、DevOps 和安全级别的工作站的用户在其计算机上没有管理员权限。 要从 Azure 门户配置此设置，请执行以下规定：

1. 转到**Azure 活动目录** > **设备** > **设备设置**。
1. 在**Azure AD 联接设备上的其他本地管理员下**选择 **"无**"。

#### <a name="require-multi-factor-authentication-to-join-devices"></a>加入设备需要多重身份验证

要进一步加强将设备连接到 Azure AD 的过程：

1. 转到**Azure 活动目录** > **设备** > **设备设置**。
1. 在 **"需要多因素身份验证"** 下选择 **"是**"以加入设备。
1. 选择“保存”。****

#### <a name="configure-mobile-device-management"></a>配置移动设备管理

通过 Azure 门户：

1. 浏览到**Azure 活动目录** > **移动性 （MDM 和 MAM）** > **微软 Intune**。
1. 将**MDM 用户范围**设置更改为 **"全部**"。
1. 选择“保存”。****

这些步骤允许您使用 Intune 管理任何设备。 有关详细信息，请参阅[Intune 快速启动：为 Windows 10 设备设置自动注册](/Intune/quickstart-setup-auto-enrollment)。 在后续步骤中创建 Intune 配置和合规性策略。

#### <a name="azure-ad-conditional-access"></a>Azure AD 条件访问

Azure AD 条件访问可帮助将特权管理任务限制为符合的设备。 **安全工作站用户**组的预定义成员在登录到云应用程序时需要执行多重身份验证。 最佳做法是从策略中排除紧急访问帐户。 有关详细信息，请参阅在[Azure AD 中管理紧急访问帐户](../users-groups-roles/directory-emergency-access.md)。

## <a name="intune-configuration"></a>调谐配置

### <a name="configure-enrollment-status"></a>配置注册状态

请务必确保您的安全工作站是值得信赖的干净设备。 购买新设备时，您可以坚持它们出厂设置为[S 模式下的 Windows 10 Pro，](/Windows/deployment/Windows-10-pro-in-s-mode)这限制了供应链管理期间的漏洞暴露。 从供应商收到设备后，可以使用自动驾驶仪从 S 模式更改该设备。 以下指南提供了有关应用转换过程的详细信息。

为了确保设备在使用前完全配置，Intune 提供了一种**在安装所有应用和配置文件之前阻止设备使用**的方法。

从**Azure 门户**：

1. 转到**微软 Intune** > **设备注册** > **Windows** > **注册状态页面** > **默认设置** > **Settings**。
1. **将"显示应用配置文件安装进度"** 设置为 **"是**"。
1. 设置**块设备使用，直到所有应用和配置文件安装**到**是**。

### <a name="create-an-autopilot-deployment-profile"></a>创建 Autopilot 部署配置文件

创建设备组后，必须创建部署配置文件以配置自动驾驶仪设备。

在 Azure 门户中的 Intune 中：

1. 选择**设备注册** > **Windows 注册** > **部署配置文件** > **创建配置文件**。
1. 输入：

   * 名称 -**安全工作站部署配置文件**。
   * 描述 -**部署安全工作站**。
   * 将“将所有目标设备转换为 Autopilot”**** 设置为“是”****。 此设置可确保列表中的所有设备均注册到 Autopilot 部署服务。 等待 48 小时来处理注册。

1. 选择“下一步”。

   * 对于**部署模式**，选择**自部署（预览）。** 具有此配置文件的设备与注册该设备的用户相关联。 必须具备用户凭据，才能注册设备。 必须注意，在**自部署**模式下部署设备将允许您在共享模型中部署便携式计算机。 在首次将设备分配给用户之前，不会发生用户分配。 因此，在完成用户分配之前，不会启用任何用户策略（如 BitLocker）。 有关如何登录到安全设备的详细信息，请参阅[选定的配置文件](/intune/device-profile-assign)。
   * "**以 Azure AD 身份加入"应**显示**Azure AD 已加入**并灰显。
   * 选择您的语言（区域）、用户帐户类型**标准**。 

1. 选择“下一步”。

   * 如果已预配置作用域标记，请选择作用域标记。

1. 选择“下一步”。
1. 选择 > **Assign to**分配给 > **选定组****的分配。** 在 **"选择要包括的组"** 中，请选择 **"安全工作站**"。
1. 选择“下一步”。
1. 选择“创建”**** 以创建该配置文件。 Autopilot 部署配置文件现在即可分配给设备。

自动驾驶仪中的设备注册根据设备类型和角色提供不同的用户体验。 在我们的部署示例中，我们演示了一个模型，其中安全设备是批量部署的，可以共享，但当首次使用时，设备将分配给用户。 有关详细信息，请参阅[Intune 自动驾驶仪设备注册](/intune/device-enrollment)。

### <a name="configure-windows-update"></a>配置 Windows 更新

使 Windows 10 保持最新是您可以执行的最重要操作之一。 要将 Windows 保持在安全状态，请部署更新环来管理将更新应用于工作站的速度。 

本指南建议您创建新的更新环并更改以下默认设置：

在 Azure 门户中：

1. 转到**微软 Intune** > **软件更新** > **Windows 10 更新环**。
1. 输入：

   * 名称 - **Azure 管理的工作站更新**
   * 服务通道 - **Windows 内幕 - 快速**
   * 质量更新延迟（天） - **3**
   * 功能更新延迟期（天） - **3**
   * 自动更新行为 -**自动安装和重新启动，无需最终用户控制**
   * 阻止用户暂停 Windows 更新 -**阻止**
   * 需要用户批准才能在工作时间以外重新启动 -**必需**
   * 允许用户重新启动（参与重新启动） -**必需**
   * 自动重新启动后将用户转换为参与式重新启动 （天） - **3**
   * 打盹接线式重启提醒（天） - **3**
   * 设置待处理重新启动的截止时间（天） - **3**

1. 选择 **“创建”**。
1. 在"**分配"** 选项卡上，添加 **"安全工作站"** 组。

有关 Windows 更新策略的详细信息，请参阅[策略 CSP - 更新](/windows/client-management/mdm/policy-csp-update)。

### <a name="windows-defender-atp-intune-integration"></a>视窗后卫ATP调谐集成

Windows Defender ATP 和微软 Intune 协同工作，帮助防止安全漏洞。 它们还可以限制违规的影响。 ATP 功能提供实时威胁检测，并可对端点设备进行广泛的审核和日志记录。

要配置 Windows Defender ATP 和 Intune 的集成，请转到 Azure 门户。

1. 浏览到**微软Intune** > **设备符合** > **Windows防御者ATP。**
1. 在**配置 Windows 防御者 ATP**下的步骤 1 中，选择在 Windows**防御器安全中心中选择将 Windows 防御者 ATP 连接到微软 Intune。**
1. 在“Windows Defender 安全中心”中：

   1. 选择 **"设置** > **高级功能**"。
   1. 对于**微软 Intune 连接**，请选择 **"打开**"。
   1. 选择“保存首选项”****。

1. 建立连接后，返回到 Intune 并选择顶部**的"刷新**"。
1. 将“将 Windows 设备版本 10.0.15063 及更高版本连接到 Windows Defender ATP”设置为“启用”********。
1. 选择“保存”。****

有关详细信息，请参阅 [Windows Defender 高级威胁防护](/Windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection)

### <a name="finish-workstation-profile-hardening"></a>完成工作站轮廓硬化

要成功完成解决方案的强化，请下载并执行相应的脚本。 查找所需**配置文件级别的**下载链接：

| 配置文件 | 下载位置 | 文件名 |
| --- | --- | --- |
| 低安全性 | 空值 | 空值 |
| 增强的安全性 | https://aka.ms/securedworkstationgit | 增强型工作站-Windows10-（1809）。ps1 |
| 高安全性 | https://aka.ms/securedworkstationgit | 高安全工作站-Windows10-（1809）。ps1 |
| 专用 | https://github.com/pelarsen/IntunePowerShellAutomation | DeviceConfiguration_NCSC - Windows10 （1803） 安全基线.ps1 |
| 专业化合规* | https://aka.ms/securedworkstationgit | DeviceCompliance_NCSC-视窗10（1803）。ps1 |
| 担保 | https://aka.ms/securedworkstationgit | 安全工作站-Windows10-（1809）-安全基线.ps1 |

\*专用合规性是强制执行 NCSC Windows10 安全基线中提供的专用配置的脚本。

脚本成功执行后，您可以对 Intune 中的配置文件和策略进行更新。 增强和安全配置文件的脚本为您创建策略和配置文件，但您必须将策略分配给**安全工作站**设备组。

* 在这里，您可以找到由脚本创建的 Intune 设备配置文件 **：Azure 门户** > **Microsoft Intune** > **设备配置文件** > **Profiles**。
* 在这里，您可以找到由脚本创建的 Intune 设备合规性策略 **：Azure 门户** > **Microsoft Intune** > **设备合规性** > **策略**。

要查看脚本所做的更改，可以导出配置文件。 这样，您可以确定[SECCON 文档中](/windows/security/threat-protection/windows-security-configuration-framework/windows-security-configuration-framework)可能要求的其他强化。

从[设备配置 GiuHub](https://github.com/microsoftgraph/powershell-intune-samples/tree/master/DeviceConfiguration) `DeviceConfiguration_Export.ps1`存储库运行 Intune 数据导出脚本以导出所有当前的 Intune 配置文件。

## <a name="additional-configurations-and-hardening-to-consider"></a>需要考虑的其他配置和强化

通过按照此处的指导，您部署了一个安全工作站。 但是，您还应考虑其他控件。 例如：

* 限制对备用浏览器的访问
* 允许出站 HTTP
* 阻止选择网站
* 设置运行自定义 PowerShell 脚本的权限

### <a name="set-rules-in-the-firewall-configuration-service-provider-csp"></a>在防火墙配置服务提供商 （CSP） 中设置规则

您可以根据需要对允许的和阻止的终结点的入站和出站规则的管理进行其他更改。 当您继续强化安全工作站时，可以放松拒绝所有入站和出站流量的限制。 您可以添加允许的出站网站，以包括常见和受信任的网站。 有关详细信息，请参阅[防火墙配置服务](/Windows/client-management/mdm/firewall-csp)。

限制性 URL 流量管理包括：

* 拒绝所有入站流量 - 在安全工作站配置文件脚本中设置。
* 拒绝除选定的 Azure 和 Microsoft 服务（包括 Azure 云外壳）和允许 Azure 密码重置之外的所有出站。

```
[HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings]
"ProxyEnable"=dword:00000001
"ProxyServer"="127.0.0.1:80"
"ProxyOverride"="*.azure.com;*.azure.net;*.microsoft.com;*.windowsupdate.com;*.microsoftonline.com;*.microsoftonline.cn;*.windows.net;*.windowsazure.com;*.windowsazure.cn;*.azure.cn;*.loganalytics.io;*.applicationinsights.io;*.vsassets.io;*.azure-automation.net;*.visualstudio.com,portal.office.com;*.aspnetcdn.com;*.sharepointonline.com;*.msecnd.net;*.msocdn.com;*.webtrends.com"
"AutoDetect"=dword:00000000
```

如果要为阻止规则提供更细粒度，可以参考维护[域阻止列表 （DBL）](https://www.spamhaus.org/dbl/)的 Spamhaus 项目：一种用作用于实现阻止站点的高级规则的高级资源。

### <a name="manage-local-applications"></a>管理本地应用程序

当本地应用程序（包括生产力应用程序）被删除时，安全工作站将移动到真正强化的状态。 在这里，您将 Chrome 添加为默认浏览器，并使用 Intune 限制用户修改浏览器及其插件的能力。

#### <a name="deploy-applications-using-intune"></a>使用 Intune 部署应用程序

在某些情况下，安全工作站上需要使用 Google Chrome 浏览器等应用程序。 下面的示例提供了将 Chrome 安装到**安全组"安全工作站"** 中的设备的说明。

1. 下载 Windows [64 位的](https://cloud.google.com/chrome-enterprise/browser/download/)离线安装程序 Chrome 捆绑包。
1. 提取文件并记下`GoogleChromeStandaloneEnterprise64.msi`文件的位置。
1. 在**Azure 门户**中浏览到**Microsoft Intune** > **客户端** > **应用应用** > **添加**。
1. 在 **"应用类型**"下，选择**业务线**。
1. 在**应用包文件**下，从`GoogleChromeStandaloneEnterprise64.msi`提取的位置选择文件，然后选择 **"确定**"。
1. 在**应用信息**下，提供说明和发布者。 选择“确定”。
1. 选择“添加”****。
1. 在"**分配"** 选项卡上，选择 **"为"分配"类型**下的**已注册设备**提供。
1. 在 **"包含的组**"下，添加**安全工作站**组。
1. 选择“确定”，然后选择“保存”********。

有关配置 Chrome 设置的详细信息，请参阅[使用 Microsoft Intune 管理 Chrome 浏览器](https://support.google.com/chrome/a/answer/9102677)。

#### <a name="configuring-the-company-portal-for-custom-apps"></a>为自定义应用配置公司门户

在安全模式下，应用程序安装仅限于 Intune 公司门户。 但是，安装门户需要访问 Microsoft 应用商店。 在安全解决方案中，您可以通过脱机模式使公司门户对所有设备可用。

[Intune 管理的公司门户](/Intune/store-apps-company-portal-app)副本可让您按需访问其他工具，您可以将这些工具向下推送到安全工作站的用户。

您可能需要安装需要特殊准备的其他 Windows 32 位应用或其他应用。 在这种情况下[，Microsoft win32 内容准备工具](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool)可以提供即用`.intunewin`格式文件进行安装。

### <a name="conditional-access-only-allowing-secured-workstation-ability-to-access-azure-portal"></a>条件访问仅允许安全工作站访问 Azure 门户的能力

Azure AD 提供了管理和限制可以访问 Azure 云管理门户的人员和内容的能力。 启用[条件访问](../conditional-access/overview.md)将确保只有安全工作站才能管理或更改资源。 在部署此功能时，您必须考虑，如果[紧急访问](../users-groups-roles/directory-emergency-access.md)功能可以或应该仅用于极端情况和通过策略管理的帐户。

> [!NOTE]
> 您需要创建一个用户组，并包括可以绕过条件访问策略的紧急用户。 例如，我们有一个安全小组，名为**紧急碎玻璃**

1. 浏览到**Azure 门户** > **Microsoft Intune** > **条件访问 - 策略** > **新策略**。
1. 为策略提供**名称**。
1. 选择**用户和组** > **选择用户和组** 
1. 选择 **"包括** > **目录角色**>选择>全局管理员、特权角色管理员、特权身份验证管理员、安全管理员、合规性管理员、条件访问管理员、应用程序管理员、云应用程序管理员、Intune 服务管理员的角色
1. 选择 **"排除**>选择**用户和组**>**选择排除用户**>选择**紧急破玻璃**组。
1. 选择 **"云应用"或操作**>选择**所有云应用**
1. 选择**条件**>选择**设备平台**>选择配置**是**>**选择设备平台**选择**窗口**
1. 选择 **"访问控制**">选择 **"授予访问****是**">选择 **"需要的设备"以标记为"符合"。** 
1. 选择**启用策略** > **On**
 
此策略集将确保管理员必须使用符合的 Windows 设备（由 Intune 和 WDATP 设置）。 

组织还应考虑在其环境中阻止旧版身份验证协议。 完成此任务的方法有多种，有关阻止旧版身份验证协议的详细信息，请参阅一文：[使用条件访问阻止对 Azure AD 的旧身份验证](../conditional-access/block-legacy-authentication.md)。

### <a name="use-powershell-to-create-custom-settings"></a>使用 PowerShell 创建自定义设置

您还可以使用 PowerShell 扩展主机管理功能。 此示例脚本在主机上设置默认背景。 此功能也可通过 Azure 门户和配置文件提供。 示例脚本仅用于说明 PowerShell 功能。

您可能需要在安全工作站上设置一些自定义控件和设置。 此示例通过使用 Powershell 能够轻松地将设备标识为随时可以使用的安全工作站，从而更改工作站的背景。

来自 Microsoft 脚本中心的[SetDesktopBackground.ps1](https://gallery.technet.microsoft.com/scriptcenter/Set-Desktop-Image-using-5430c9fb/)脚本允许 Windows 在启动时加载此[免费的通用背景映像](https://i.imgur.com/OAJ28zO.png)。

1. 将脚本下载到本地设备。
1. 更新客户XXXX和背景图像的下载位置。 在我们的示例中，我们将客户XXXX替换为背景。
1. 浏览到**Azure 门户** > **Microsoft Intune** > **设备配置** > **PowerShell 脚本** > **添加**。
1. 为脚本提供**名称**并指定**脚本位置**。
1. 选择 **"配置**"。
   1. **使用登录的凭据将运行此脚本**设置为 **"是**"。
   1. 选择“确定”。
1. 选择 **“创建”**。
1. 选择**分配** > **选择组**。
   1. 添加安全组 **"安全工作站**"。
   1. 选择“保存”。****

## <a name="enroll-and-validate-your-first-device"></a>注册并验证第一台设备

1. 要注册设备，您需要以下信息：
   * **序列号**- 在设备机箱上找到。
   * **Windows 产品 ID** - 在"从 Windows 设置"菜单中找到 **"系统** > **有关**"。
   * 您可以运行[Get-WindowsAutoPilotInfo](https://aka.ms/Autopilotshell)来获取包含设备注册所需的所有信息的 CSV 哈希文件。
   
     运行`Get-WindowsAutoPilotInfo – outputfile device1.csv`以将信息输出为可导入到 Intune 的 CSV 文件。

     > [!NOTE]
     > 脚本需要提升的权限。 它以远程签名运行。 该`Set-ExecutionPolicy -ExecutionPolicy RemoteSigned`命令允许脚本正确运行。

   * 您可以通过登录到 Windows 10 版本 1809 或更高版本的设备来收集此信息。 您的硬件经销商也可以提供此信息。
1. 在**Azure 门户**中，转到**Microsoft Intune** > **设备注册** > **Windows 注册** > **设备 - 管理 Windows 自动驾驶仪设备**。
1. 选择 **"导入**"并选择 CSV 文件。
1. 将设备添加到**安全工作站**安全组。
1. 在要配置的 Windows 10 设备上，转到**Windows 设置** > **更新&安全** > **恢复**。
   1. 选择 **"****重置此电脑**"下开始。
   1. 按照提示，在配置了配置文件和合规性策略后重置和重新配置设备。

配置设备后，完成审阅并检查配置。 在继续部署之前，确认第一个设备配置正确。

## <a name="assign-devices"></a>分配设备

要分配设备和用户，您需要将[所选配置文件](/intune/device-profile-assign)映射到安全组。 还必须将需要服务权限的所有新用户添加到安全组中。

## <a name="using-sentinel-and-windows-defender-atp-to-monitor-and-respond-to-security-incidents"></a>使用哨兵和 Windows 防御者 ATP 监控和响应安全事件

通过启用 [Sentinel] 并利用[威胁和漏洞管理](/windows/security/threat-protection/microsoft-defender-atp/next-gen-threat-and-vuln-mgt)可以监控安全工作站部署 该指南不会提供详尽的威胁搜索，而是提供良好的常识工作，以监视和响应潜在的安全事件。

我们将使用**Azure 哨兵**： 

* 从 Intune、Azure 门户和 Azure AD 收集数据，以便用户和设备监视
* 帮助调查用户和设备配置可疑活动
* 并推动使用 WDATP 探索安全调查的能力

哨兵监视要求设置与数据源（如 Azure AD）的连接器。

1. 在**Azure 门户**中，转到**Azure 哨兵（预览）>** 选择 **"添加"**
1. 在 **"选择要添加到 Azure 哨兵的工作区****"中选择创建新工作区**
1. 输入：
   * **日志分析工作区**- "安全工作站监控"
   * **订阅**- 选择活动订阅
   * **资源组**- 选择 [创建新] >安全工作站 RG >**确定**
   * **位置**- 选择地理位置最适合您的部署的位置
   * **价格等级**- 选择**每 GB （2018）**
1. 选择 **"确定**"。

接下来，我们将将可用的安全工作站数据源连接到监视。

1. 在**Azure 门户**中，转到**Azure 哨兵工作区**>选择**安全工作站监视**工作区
1. 选择**数据连接器**
1. 在查看先决条件后>选择**Azure 活动目录**>打开连接器页。 继续配置，然后同时为 Azure AD 登录日志和 Azure AD 审核日志选择 **"连接**"。
1. 在查看先决条件后>选择 **"打开**连接器"页中的 Azure > 活动。 继续配置 Azure 活动日志>选择订阅>选择**连接**

当 Sentinel 收集数据时，您将能够通过选择**Azure 门户**来观察活动，请转到 Azure **Sentinel 概述** 

我们将使用**Windows 防御者 ATP （WDATP）** 进行：

* 持续观察和监控漏洞和错误配置
* 利用 WDATP 确定野外动态威胁的优先级
* 驱动漏洞与端点检测和响应 （EDR） 警报的相关性
* 在调查期间使用仪表板识别机器级漏洞
* 将修正推送到 Intune

配置[您的防御者 ATP 仪表板](https://securitycenter.windows.com/machines)。 使用[威胁&漏洞管理仪表板概述](/windows/security/threat-protection/microsoft-defender-atp/tvm-dashboard-insights)中的指导。

## <a name="monitoring-application-activity-using-microsoft-monitoring-agent-mma"></a>使用 Microsoft 监视代理 （MMA） 监视应用程序活动
从专用工作站开始，应用程序储物柜可用于监视工作站上的应用程序活动。 要将监视集成到日志分析工作区中，必须遵循 MMA 代理和配置。 

> [!NOTE]
> 专用工作站配置文件包含 AppLocker 监视策略。 需要部署策略来监视客户端上的应用程序活动

使用 Intune 电源外壳脚本部署 MMA 代理

1. 将设置[脚本下载到本地设备](https://aka.ms/securedworkstationgit)。
1. 更新参数 **、$WorkSpaceID**和 **$WorkSpaceKey**
1. 浏览到**Azure 门户** > **Microsoft Intune** > **设备配置** > **PowerShell 脚本** > **添加**。
1. 为脚本提供**名称**并指定**脚本位置**。
1. 选择 **"配置**"。
   1. **使用登录的凭据将运行此脚本**设置为 **"是**"。
   1. 选择“确定”。
1. 选择 **“创建”**。
1. 选择**分配** > **选择组**。
   1. 添加安全组 **"安全工作站**"。
   1. 选择“保存”。****

接下来，必须设置日志分析以接收新日志
1. 在**Azure 门户**中，转到**日志分析工作区**>选择 - "安全工作站监视"
1. 选择**高级设置** > **数据** > **窗口事件日志**
1. 在**从以下事件日志收集事件** 
1. 输入：
   * "微软-Windows-AppLocker/EXE 和 DLL" >取消选择**信息**
   * "微软-Windows-AppLocker/MSI 和脚本">取消选择**信息**
   * "微软-Windows-AppLocker/打包应用部署">"取消选择**信息"**
   * "微软-Windows-AppLocker/打包的应用程序执行">取消选择**信息**
1. 选择 **"保存"**

应用程序日志记录将在您选择的日志分析工作区中可用。

## <a name="monitoring"></a>监视

* 了解如何使用[Azure 哨兵检测威胁](/azure/sentinel/tutorial-detect-threats)
* [使用 Azure 哨兵调查事件](/azure/sentinel/tutorial-investigate-cases)
* [在 Azure Sentinel 中设置自动威胁响应](/azure/sentinel/tutorial-respond-threats-playbook)
* 了解如何查看[您的曝光分数](/windows/security/threat-protection/microsoft-defender-atp/tvm-exposure-score)
* 查看[安全建议](/windows/security/threat-protection/microsoft-defender-atp/tvm-security-recommendation)
* 管理安全[补救](/windows/security/threat-protection/microsoft-defender-atp/tvm-remediation)
* 管理[端点检测和响应](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)
* 使用[Intune 配置文件监视监视配置文件](/intune/device-profile-monitor)。

## <a name="next-steps"></a>后续步骤

* 了解有关微软[Intune](/intune/index)的更多。
* 了解[Azure AD](../index.yml)。
* 与[微软防御者高级威胁保护合作](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)
* 发现[Azure 哨兵](/azure/sentinel/)
