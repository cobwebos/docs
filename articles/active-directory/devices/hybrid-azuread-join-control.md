---
title: 混合 Azure AD 联接的受控验证 - Azure AD
description: 了解如何对整个组织同时启用混合 Azure AD 联接进行受控验证
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: f43db805ccbb7d4e546c51bbe39350f4bbba2efb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049982"
---
# <a name="controlled-validation-of-hybrid-azure-ad-join"></a>以受控方式验证混合 Azure AD 加入

当所有先决条件都到位时，Windows 设备将自动注册为 Azure AD 租户中的设备。 Azure AD 中的这些设备标识的状态称为混合 Azure AD 联接。 有关本文中介绍的概念的详细信息，请参阅[Azure 活动目录中的设备管理简介](overview.md)和[计划混合 Azure 活动目录加入实现](hybrid-azuread-join-plan.md)。

组织可能需要对混合 Azure AD 联接进行受控验证，然后再在整个组织中同时启用它。 本文将解释如何完成对混合 Azure AD 联接的受控验证。

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-current-devices"></a>在 Windows 当前设备上对混合 Azure AD 联接进行受控验证

对于运行 Windows 桌面操作系统的设备，支持的版本是 Windows 10 周年更新（版本 1607）或更高版本。 最佳做法是升级到最新版本的 Windows 10。

要在当前 Windows 设备上对混合 Azure AD 联接进行受控验证，您需要：

1. 如果活动目录 （AD） 存在，请清除服务连接点 （SCP） 条目
1. 使用组策略对象 （GPO） 在域加入的计算机上配置 SCP 的客户端注册表设置
1. 如果使用 AD FS，还必须使用 GPO 在 AD FS 服务器上配置 SCP 的客户端注册表设置  
1. 您可能还需要在 Azure AD Connect 中[自定义同步选项](../hybrid/how-to-connect-post-installation.md#additional-tasks-available-in-azure-ad-connect)以启用设备同步。 


### <a name="clear-the-scp-from-ad"></a>从 AD 中清除 SCP

使用活动目录服务接口编辑器（ADSI 编辑）修改 AD 中的 SCP 对象。

1. 启动**ADSI 编辑**桌面应用程序，从和管理工作站或域控制器作为企业管理员。
1. 连接到域的**配置命名上下文**。
1. 浏览到**CN=配置，DC=contoso，DC_com** > **CN_服务** > **CN=设备注册配置**
1. 右键单击叶对象**CN=62a0ff2e-97b9-4513-943f-0d221bd30080**并选择**属性**
   1. 从**属性编辑器**窗口中选择**关键字**，然后单击 **"编辑"**
   1. 选择**azureADId**和**azureADName**的值（一次一个），然后单击"**删除"**
1. 关闭**ADSI 编辑**


### <a name="configure-client-side-registry-setting-for-scp"></a>为 SCP 配置客户端注册表设置

使用以下示例创建组策略对象 （GPO） 以部署在设备注册表中配置 SCP 条目的注册表设置。

1. 打开组策略管理控制台，并在域中创建新的组策略对象。
   1. 为新创建的 GPO 提供名称（例如，客户端SCP）。
1. 编辑 GPO 并找到以下路径：**计算机配置** > **首选项** > **Windows 设置** > **注册表**
1. 右键单击注册表并选择**新** > **注册表项**
   1. 在 **"常规"** 选项卡上，配置以下内容
      1. 操作：**更新**
      1. 蜂巢 **：HKEY_LOCAL_MACHINE**
      1. 关键路径：**软件_微软_视窗\当前版本_CDJ_AAD**
      1. 值名称：**租户 Id**
      1. 值类型 **：REG_SZ**
      1. 值数据：Azure AD 实例的 GUID 或**目录 ID（** 此值可在**Azure 门户** > **Azure 活动目录** > **属性** > **目录 ID**中找到 ）
   1. 单击“确定”****
1. 右键单击注册表并选择**新** > **注册表项**
   1. 在 **"常规"** 选项卡上，配置以下内容
      1. 操作：**更新**
      1. 蜂巢 **：HKEY_LOCAL_MACHINE**
      1. 关键路径：**软件_微软_视窗\当前版本_CDJ_AAD**
      1. 值名称：**租户名称**
      1. 值类型 **：REG_SZ**
      1. 价值数据 **：如果您使用的**是联合环境（如 AD FS）。" 例如，**domain name**如果您使用的是托管环境，`contoso.onmicrosoft.com`则已验证的域名或onmicrosoft.com域名
   1. 单击“确定”****
1. 关闭新创建的 GPO 的编辑器
1. 将新创建的 GPO 链接到包含属于受控卷展填充的域联接计算机的所需 OU

### <a name="configure-ad-fs-settings"></a>配置 AD FS 设置

如果您使用的是 AD FS，则首先需要使用上述说明配置客户端 SCP，将 GPO 链接到 AD FS 服务器。 SCP 对象定义设备对象的权威源。 它可以是本地广告或 Azure AD。 当客户端 SCP 配置为 AD FS 时，设备对象的源将建立为 Azure AD。

> [!NOTE]
> 如果未能在 AD FS 服务器上配置客户端 SCP，则设备标识的源将被视为本地。 然后，ADFS 将在 ADFS 设备注册的属性"最大非活动日"中定义的规定期间后，开始从本地目录中删除设备对象。 可以使用[获取 Adfs 设备注册 cmdlet 找到 ADFS](/powershell/module/adfs/get-adfsdeviceregistration?view=win10-ps)设备注册对象。

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices"></a>在 Windows 低级设备上对混合 Azure AD 联接进行受控验证

若要注册 Windows 下层设备，组织必须安装 Microsoft 下载中心提供的[适用于 Windows 10 计算机的 Microsoft 工作区加入](https://www.microsoft.com/download/details.aspx?id=53554)。

可以使用  [Microsoft Endpoint Configuration Manager](/configmgr/) 等软件分发系统部署该包。 此包支持使用标准无提示安装选项（包含 quiet 参数）。 Configuration Manager 的 Current Branch 提供优于早期版本的优势，例如可以跟踪已完成的注册。

安装程序会在系统上创建一项计划任务，该任务会在用户的上下文中运行。 当用户登录到 Windows 时触发该任务。 通过 Azure AD 进行身份验证后，该任务以无提示方式使用用户凭据将设备联接到 Azure AD。

要控制设备注册，应将 Windows 安装程序包部署到所选的 Windows 低级设备组。

> [!NOTE]
> 如果在 AD 中未配置 SCP，则应遵循与使用组策略对象 （GPO） 在域联接的计算机上[配置 SCP 客户端注册表设置](#configure-client-side-registry-setting-for-scp)相同的方法。


验证一切是否按预期工作后，可以使用[Azure AD 配置 SCP，](hybrid-azuread-join-managed-domains.md#configure-hybrid-azure-ad-join)自动将 Windows 的其余当前和下级设备注册到 Azure AD。

## <a name="next-steps"></a>后续步骤

[规划混合 Azure Active Directory 加入实现](hybrid-azuread-join-plan.md)
