---
title: "使用 Azure Active Directory 设备注册设置本地条件访问 | Microsoft Docs"
description: "在 Windows Server 2012 R2 中使用 Active Directory 联合身份验证服务 (AD FS) 启用对本地应用程序的条件访问的分步指南。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 6ae9df8b-31fe-4d72-9181-cf50cfebbf05
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/21/2017
ms.author: markvi
ms.reviewer: jairoc
ms.translationtype: HT
ms.sourcegitcommit: 7bf5d568e59ead343ff2c976b310de79a998673b
ms.openlocfilehash: 1a6f1c6566468188daa71939db8345280b7a529f
ms.contentlocale: zh-cn
ms.lasthandoff: 08/01/2017

---
# <a name="setting-up-on-premises-conditional-access-by-using-azure-active-directory-device-registration"></a>使用 Azure Active Directory 设备注册设置本地条件访问
当你要求用户在工作区将其个人设备加入到 Azure Active Directory (Azure AD) 设备注册服务时，他们的设备会被标记为组织已知的设备。 以下是在 Windows Server 2012 R2 中使用 Active Directory 联合身份验证服务 (AD FS) 启用对本地应用程序的条件访问的分步指南。

> [!NOTE]
> 使用在 Azure Active Directory 设备注册服务条件访问策略中注册的设备时，需要 Office 365 许可证或 Azure AD Premium 许可证。 其中包括由本地资源中的 AD FS 强制执行的策略。
> 
> 有关本地资源条件访问方案的详细信息，请参阅[跨公司应用程序从任一设备加入工作区以实现 SSO 和无缝第二重身份验证](https://technet.microsoft.com/library/dn280945.aspx)。

这些功能可供购买了 Azure Active Directory Premium 许可证的客户使用。

## <a name="supported-devices"></a>支持的设备
* 已加入域的 Windows 7 设备
* Windows 8.1 个人设备和已加入域的设备
* 适用于 Safari 浏览器的 iOS 6 和更高版本
* Android 4.0 或更高版本、Samsung GS3 或更高版本的手机、Samsung Galaxy Note 2 或更高版本的平板电脑

## <a name="scenario-prerequisites"></a>方案先决条件
* Office 365 或 Azure Active Directory Premium 订阅
* 一个 Azure Active Directory 租户
* Windows Server Active Directory（Windows Server 2008 或更高版本）
* Windows Server 2012 R2 中更新的架构
* Azure Active Directory Premium 许可证
* Windows Server 2012 R2 联合身份验证服务，为 Azure AD 的 SSO 配置
* Windows Server 2012 R2 Web 应用程序代理 
* Microsoft Azure Active Directory Connect (Azure AD Connect) [（下载 Azure AD Connect）](http://www.microsoft.com/en-us/download/details.aspx?id=47594)
* 已验证的域

## <a name="known-issues-in-this-release"></a>本版本中的已知问题
* 基于设备的条件访问策略需要将设备对象从 Azure Active Directory 写回 Active Directory。 将设备对象写回到 Active Directory 最长可能需要 3 个小时。
* 在客户端证书身份验证过程中，iOS 7 设备始终会提示用户选择证书。
* 低于 iOS 8.3 的某些 iOS 8 版本不会正常工作。

## <a name="scenario-assumptions"></a>方案假设条件
本方案假设你有 Azure AD 租户与本地 Active Directory 所组成的混合环境。 这些租户应使用 Azure AD Connect 进行连接，并且应具有已验证的域和适用于 SSO 的 AD FS。 可以根据要求使用以下清单来帮助你配置环境。

## <a name="checklist-prerequisites-for-conditional-access-scenario"></a>清单：条件访问方案的先决条件
将 Azure AD 租户连接到本地 Active Directory 实例。

## <a name="configure-azure-active-directory-device-registration-service"></a>配置 Azure Active Directory 设备注册服务
使用本指南为组织部署和配置 Azure Active Directory 设备注册服务。

本指南假设你已配置 Windows Server Active Directory，并已订阅 Microsoft Azure Active Directory。 请参阅前面所述的先决条件。

若要为 Azure Active Directory 租户部署 Azure Active Directory 设备注册服务，请按顺序完成以下清单中的任务。 引用链接将你转到概念主题时，请稍后返回到此清单，以便可以继续执行剩余任务。 一些任务包含方案验证步骤，可帮助你确认步骤是否成功完成。

## <a name="part-1-enable-azure-active-directory-device-registration"></a>第 1 部分：启用 Azure Active Directory 设备注册
遵循清单中的步骤来启用和配置 Azure Active Directory 设备注册服务。

| 任务 | 引用 | 
| --- | --- |
| 在 Azure Active Directory 租户中启用设备注册，允许设备加入工作区。 默认情况下，不会为该服务启用 Azure 多重身份验证。 但是，建议在注册设备时使用多重身份验证。 在 Active Directory 注册服务中启用多重身份验证之前，请确保为多重身份验证提供程序配置了 AD FS。 |[启用 Azure Active Directory 设备注册](active-directory-device-registration-get-started.md)| 
|设备将通过查找已知的 DNS 记录来发现 Azure Active Directory 设备注册服务。 请配置公司 DNS，使设备能够发现你的 Azure Active Directory 设备注册服务。 |[配置 Azure Active Directory 设备注册发现](active-directory-device-registration-get-started.md)| 


## <a name="part-2-deploy-and-configure-windows-server-2012-r2-active-directory-federation-services-and-set-up-a-federation-relationship-with-azure-ad"></a>第 2 部分：部署并配置 Windows Server 2012 R2 Active Directory 联合身份验证服务，并设置与 Azure AD 之间的联合关系

| 任务 | 引用 |
| --- | --- |
| 部署具有 Windows Server 2012 R2 架构扩展的 Active Directory 域服务。 不需要将任何域控制器升级到 Windows Server 2012 R2。 只需要升级架构。 |[升级 Active Directory 域服务架构](#upgrade-your-active-directory-domain-services-schema) |
| 设备将通过查找已知的 DNS 记录来发现 Azure Active Directory 设备注册服务。 请配置公司 DNS，使设备能够发现你的 Azure Active Directory 设备注册服务。 |[准备 Active Directory 支持设备](#prepare-your-active-directory-to-support-devices) |

## <a name="part-3-enable-device-writeback-in-azure-ad"></a>第 3 部分：在 Azure AD 中启用设备写回
| 任务 | 引用 |
| --- | --- |
| 完成第 2 部分“在 Azure AD Connect 中启用设备写回”。 完成后，返回到本指南。 |[在 Azure AD Connect 中启用设备写回](#upgrade-your-active-directory-domain-services-schema) |

## <a name="optional-part-4-enable-multi-factor-authentication"></a>[可选] 第 4 部分：启用多重身份验证
强烈建议配置多重身份验证的多个选项之一。 如果你希望要求进行多重身份验证，请参阅[选择多重身份验证安全解决方案](../multi-factor-authentication/multi-factor-authentication-get-started.md)。 其中包含每个解决方案的说明，以及可帮助配置所选解决方案的链接。

## <a name="part-5-verification"></a>第 5 部分：验证
部署现已完成，你可以尝试一些方案。 使用以下链接来体验服务并熟悉各项功能。

| 任务 | 引用 |
| --- | --- |
| 使用 Azure Active Directory 设备注册服务将某些设备加入工作区。 你可以加入 iOS、Windows 和 Android 设备。 |[使用 Azure Active Directory 设备注册服务将设备加入工作区](#join-devices-to-your-workplace-using-azure-active-directory-device-registration) |
| 使用管理员门户查看和启用或禁用已注册的设备。 在此任务中，将使用管理员门户查看一些已注册的设备。 |[Azure Active Directory 设备注册服务概览](active-directory-device-registration-get-started.md) |
| 验证是否能够将设备对象从 Azure Active Directory 写回到 Windows Server Active Directory。 |[验证注册的设备是否已写回到 Active Directory](#verify-registered-devices-are-written-back-to-active-directory) |
| 用户可以注册其设备，则可以在 AD FS 中创建只允许已注册设备访问的应用程序访问策略。 在此任务中，将创建一个应用程序访问规则和自定义的访问被拒绝消息。 |[创建应用程序访问策略和自定义的访问被拒绝消息](#create-an-application-access-policy-and-custom-access-denied-message) |

## <a name="integrate-azure-active-directory-with-on-premises-active-directory"></a>将 Azure Active Directory 与本地 Active Directory 集成
此步骤可帮助你使用 Azure AD Connect 将 Azure AD 租户与本地 Active Directory 集成。 尽管 Azure 经典门户中提供了这些步骤，但请记下本部分中所列的所有特殊说明。

1. 使用作为 Azure AD 全局管理员的帐户登录到 Azure 经典门户。
2. 在左窗格中选择“Active Directory”。
3. 在“目录”选项卡中选择目录。
4. 选择“目录集成”选项卡。
5. 在“部署和管理”部分，执行步骤 1 到 3，将 Azure Active Directory 与你的本地目录集成。
   
   1. 添加域。
   2. 使用 [Azure AD Connect 的自定义安装](connect/active-directory-aadconnect-get-started-custom.md)中的说明安装并运行 Azure AD Connect。
   3. 验证和管理目录同步此步骤提供了有关单一登录的说明。
   
   另外，使用 AD FS 配置联合身份验证，如 [Azure AD Connect 的自定义安装](connect/active-directory-aadconnect-get-started-custom.md)中所述。

## <a name="upgrade-your-active-directory-domain-services-schema"></a>升级 Active Directory 域服务架构
> [!NOTE]
> 升级 Active Directory 架构后，无法还原此过程。 建议你首先在测试环境中执行升级操作。
> 

1. 使用同时具有企业管理员和架构管理员权限的帐户登录到域控制器。
2. 将 [media]\support\adprep 目录和子目录复制到其中一个 Active Directory 域控制器（其中“[media]”是 Windows Server 2012 R2 安装媒体的路径）。
4. 在命令提示符下，转到 adprep 目录并运行 adprep.exe /forestprep。 遵照屏幕说明完成架构升级。

## <a name="prepare-your-active-directory-to-support-devices"></a>准备 Active Directory 以提供设备支持
> [!NOTE]
> 这是在准备 Active Directory 林以提供设备支持时必须完成的一次性操作。 若要完成此过程，必须使用企业管理员权限登录，并且 Active Directory 林必须具有 Windows Server 2012 R2 架构。
> 


### <a name="prepare-your-active-directory-forest"></a>准备 Active Directory 林
1. 在联合服务器上，打开 Windows PowerShell 命令窗口，然后键入 Initialize-ADDeviceRegistration。 
2. 出现 **ServiceAccountName** 提示时，输入选择作为 AD FS 服务帐户的服务帐户名。 如果该帐户为 gMSA 帐户，请以 domain\accountname$ 格式输入该帐户。 对于域帐户，请使用 **domain\accountname** 格式。

### <a name="enable-device-authentication-in-ad-fs"></a>在 AD FS 中启用设备身份验证
1. 在联合服务器上，打开 AD FS 管理控制台，并转到“AD FS” > “身份验证策略”。
2. 从“操作”窗格中选择“编辑全局主要身份验证”。
3. 选中“启用设备身份验证”，然后选择“确定”。
4. 默认情况下，AD FS 会定期从 Active Directory 中删除未使用的设备。 使用 Azure Active Directory 设备注册服务时禁用此任务，以便能够在 Azure 中管理设备。

### <a name="disable-unused-device-cleanup"></a>禁用未使用设备的清理
在联合服务器上，打开 Windows PowerShell 命令窗口，然后键入 Set-AdfsDeviceRegistration -MaximumInactiveDays 0。

### <a name="prepare-azure-ad-connect-for-device-writeback"></a>准备 Azure AD Connect 以进行设备写回
完成第 1 部分：准备 Azure AD Connect。

## <a name="join-devices-to-your-workplace-by-using-azure-active-directory-device-registration-service"></a>使用 Azure Active Directory 设备注册服务将设备加入工作区

### <a name="join-an-ios-device-by-using-azure-active-directory-device-registration"></a>使用 Azure Active Directory 设备注册加入 iOS 设备
Azure Active Directory 设备注册为 iOS 设备使用无线配置文件注册过程。 此过程将在用户使用 Safari 连接到配置文件注册 URL 时开始。 该 URL 的格式如下：

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/"yourdomainname"

在这种情况下，`yourdomainname` 是你使用 Azure Active Directory 配置的域名。 例如，如果域名为 contoso.com，则 URL 如下所示：

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/contoso.com

可通过许多不同的方法让用户访问此 URL。 例如，我们建议的方法之一是在 AD FS 中，将此 URL 发布在自定义的应用程序访问被拒绝消息中。 此信息将在下一节中进行介绍：[创建应用程序访问策略和自定义访问被拒绝消息](#create-an-application-access-policy-and-custom-access-denied-message)。

### <a name="join-a-windows-81-device-by-using-azure-active-directory-device-registration"></a>使用 Azure Active Directory 设备注册加入 Windows 8.1 设备
1. 在 Windows 8.1 设备上，选择“电脑设置” > “网络” > “工作区”。
2. 以 UPN 格式输入你的用户名；例如，**dan@contoso.com**。
3. 选择“加入”。
4. 出现提示时，请使用凭据登录。 随后将加入该设备。

### <a name="join-a-windows-7-device-by-using-azure-active-directory-device-registration"></a>使用 Azure Active Directory 设备注册加入 Windows 7 设备
若要注册已加入域的 Windows 7 设备，需要部署设备注册软件包。 此软件包名为 Workplace Join for Windows 7，可以从 [Microsoft Connect 网站](https://connect.microsoft.com/site1164)下载。 

有关如何使用此包的说明，可在[如何使用 Azure Active Directory 配置已加入域的 Windows 设备的自动注册](active-directory-conditional-access-automatic-device-registration-setup.md)中找到。

## <a name="verify-that-registered-devices-are-written-back-to-active-directory"></a>验证注册的设备是否已写回到 Active Directory
可以使用 LDP.exe 和 ADSI 编辑器来查看并确认已将设备对象写回到 Active Directory。 Active Directory 管理员工具中提供了这两个程序。

默认情况下，从 Azure Active Directory 写回的设备对象与 AD FS 场位于同一个域中。

    CN=RegisteredDevices,defaultNamingContext

## <a name="create-an-application-access-policy-and-custom-access-denied-message"></a>创建应用程序访问策略和自定义的访问被拒绝消息
考虑以下方案：在 AD FS 中创建了一个应用程序信赖方信任，并配置了一个只允许已注册设备的颁发授权规则。 现在，只允许已注册设备访问该应用程序。 

若要方便用户访问该应用程序，可以配置自定义的访问被拒绝消息，其中包含了有关如何加入其设备的说明。 现在，你的用户可以顺利地注册其设备，以便访问应用程序。

以下步骤演示如何实现此方案。

> [!NOTE]
> 本部分假设已在 AD FS 中为应用程序配置了信赖方信任。
> 

1. 打开 AD FS MMC 工具，并导航到“AD FS” > “信任关系” > “信赖方信任”。
2. 找到要向其应用此新访问规则的应用程序。 右键单击该应用程序，然后选择“编辑声明规则”。
3. 选择“颁发授权规则”选项卡，然后选择“添加规则”。
4. 从“声明规则”模板下拉列表中，选择“根据传入声明允许或拒绝用户”。 然后，选择“下一步”。
5. 在“声明规则名称”字段中，键入“允许从已注册的设备访问”。
6. 从“传入声明类型”下拉列表中，选择“为注册用户”。
7. 在“传入声明值”字段中，键入“true”。
8. 选中“允许访问具有此传入声明的用户”单选按钮。
9. 选择“完成”，然后选择“应用”。
10. 删除比你创建的规则更宽松的所有规则。 例如，删除默认规则“允许访问所有用户”。

现在，应用程序已配置为仅允许用户从其已注册的且已加入工作区的设备进行访问。 有关更高级的访问策略，请参阅[使用附加多重身份验证管理敏感应用程序的风险](https://technet.microsoft.com/library/dn280949.aspx)。

接下来，为应用程序配置一条自定义错误消息。 该错误消息通知用户，只有将设备加入工作区后，才能访问该应用程序。 可以使用自定义 HTML 和 PowerShell 创建自定义的应用程序访问被拒绝消息。

在联合服务器上，打开 PowerShell 命令窗口，然后键入以下命令。 将该命令的某些部分替换为特定于系统的项：

    Set-AdfsRelyingPartyWebContent -Name "relying party trust name" -ErrorPageAuthorizationErrorMessage
在访问此应用程序之前，必须先注册设备。


            **如果使用 iOS 设备，请选择此链接加入设备**：

    a href='https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/yourdomain.com

将此 iOS 设备加入工作区。

如果使用的是 Windows 8.1 设备，则可以选择“电脑设置”> “网络” > “工作区”加入设备。

在前面的命令中，“relying party trust name”是 AD FS 中应用程序信赖方信任对象的名称。
“yourdomain.com”是使用 Azure Active Directory 配置的域名（例如，contoso.com）。
请务必删除传递到 Set-AdfsRelyingPartyWebContent cmdlet 的 HTML 内容中的所有换行符（如果有的话）。

现在，当用户通过未注册到 Azure Active Directory 设备注册服务的设备访问应用程序时，他们将看到类似于以下屏幕截图的页。

![用户未将其设备注册到 Azure AD 时看到的错误消息屏幕截图](./media/active-directory-conditional-access/error-azureDRS-device-not-registered.gif)



