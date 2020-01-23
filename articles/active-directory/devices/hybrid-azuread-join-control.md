---
title: 对混合 Azure AD 联接的受控验证-Azure AD
description: 在整个组织中同时启用混合 Azure AD 联接之前，如何对其进行控制验证
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
ms.openlocfilehash: b2c1a088e4c200dcc4a2ff35db942e3eb8480674
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2020
ms.locfileid: "76512085"
---
# <a name="controlled-validation-of-hybrid-azure-ad-join"></a>以受控方式验证混合 Azure AD 加入

当所有先决条件都准备就绪后，Windows 设备将自动作为 Azure AD 租户中的设备进行注册。 Azure AD 中这些设备标识的状态称为 "混合 Azure AD 联接"。 有关本文中所述概念的详细信息，请参阅[Azure Active Directory 中的设备管理简介](overview.md)和[计划混合 Azure Active Directory 加入实现](hybrid-azuread-join-plan.md)。

在整个组织中同时启用混合 Azure AD 联接之前，组织可能需要对其进行控制验证。 本文将介绍如何实现混合 Azure AD 联接的受控验证。

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-current-devices"></a>Windows 当前设备上的混合 Azure AD 联接的受控验证

对于运行 Windows 桌面操作系统的设备，支持的版本是 Windows 10 周年更新（版本 1607）或更高版本。 最佳做法是升级到最新版本的 Windows 10。

若要在 Windows 当前设备上对混合 Azure AD 联接进行受控验证，需执行以下操作：

1. 清除 Active Directory （AD）中的服务连接点（SCP）项（如果存在）
1. 使用组策略对象（GPO）为已加入域的计算机上的 SCP 配置客户端注册表设置
1. 如果使用的是 AD FS，还必须在 AD FS 服务器上使用 GPO 配置 SCP 的客户端注册表设置  
1. 你可能还需要在 Azure AD Connect 中[自定义同步选项](../hybrid/how-to-connect-post-installation.md#additional-tasks-available-in-azure-ad-connect)，以实现设备同步。 


### <a name="clear-the-scp-from-ad"></a>清除 AD 中的 SCP

使用 Active Directory 服务接口编辑器（ADSI 编辑器）来修改 AD 中的 SCP 对象。

1. 以企业管理员身份从和管理工作站或域控制器启动**ADSI 编辑器**桌面应用程序。
1. 连接到域的**配置命名上下文**。
1. 浏览到**CN = Configuration，dc = contoso，dc = com** > **cn = Services** > **cn = 设备注册配置**
1. 右键单击 " **CN = 设备注册配置**" 下的叶对象，然后选择 "**属性**"
   1. 从 "**属性编辑器**" 窗口中选择**关键字**，然后单击 "**编辑**"
   1. 选择**azureADId**和**azureADName**的值（一次一个）并单击 "**删除**"
1. 关闭**ADSI 编辑器**


### <a name="configure-client-side-registry-setting-for-scp"></a>为 SCP 配置客户端注册表设置

使用以下示例创建一个组策略对象（GPO）来部署注册表设置，在设备的注册表中配置 SCP 条目。

1. 打开组策略管理控制台并在你的域中创建新的组策略对象。
   1. 为新创建的 GPO 提供一个名称（例如，ClientSideSCP）。
1. 编辑 GPO 并找到以下路径：**计算机配置** > **首选项** > **Windows Settings** > **注册表**
1. 右键单击注册表，然后选择 "**新建** > **注册表项**"
   1. 在 "**常规**" 选项卡上，配置以下各项
      1. 操作：**更新**
      1. Hive： **HKEY_LOCAL_MACHINE**
      1. 密钥路径： **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. 值名称： **TenantId**
      1. 值类型： **REG_SZ**
      1. 值数据： Azure AD 实例的 GUID 或**目录 ID** （此值可在**Azure 门户** > **Azure Active Directory** > **Directory ID** > **属性**中找到）
   1. 单击 **“确定”**
1. 右键单击注册表，然后选择 "**新建** > **注册表项**"
   1. 在 "**常规**" 选项卡上，配置以下各项
      1. 操作：**更新**
      1. Hive： **HKEY_LOCAL_MACHINE**
      1. 密钥路径： **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. 值名称： **TenantName**
      1. 值类型： **REG_SZ**
      1. 值数据：如果你使用的是联合环境（如 AD FS），则已验证的**域名**。 已验证的**域名**或你的 onmicrosoft.com 域名例如，如果你使用的是托管环境 `contoso.onmicrosoft.com`
   1. 单击 **“确定”**
1. 关闭新创建的 GPO 的编辑器
1. 将新创建的 GPO 链接到包含已加入域的计算机的所需 OU，该 OU 属于你的受控推出群体

### <a name="configure-ad-fs-settings"></a>配置 AD FS 设置

如果使用 AD FS，则首先需要使用上述说明来配置客户端 SCP，方法是将 GPO 链接到 AD FS 服务器。 SCP 对象定义设备对象的授权来源。 它可以是本地的，也可以是 Azure AD。 为 AD FS 配置客户端 SCP 后，设备对象的源将建立为 Azure AD。

> [!NOTE]
> 如果无法在 AD FS 服务器上配置客户端 SCP，则设备标识的源将被视为 "本地"。 然后，在 ADFS 设备注册的属性 "MaximumInactiveDays" 中定义的规定期限后，ADFS 将开始从本地目录中删除设备对象。 可以使用[AdfsDeviceRegistration cmdlet](https://docs.microsoft.com/powershell/module/adfs/get-adfsdeviceregistration?view=win10-ps)找到 ADFS 设备注册对象。

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices"></a>Windows 下层设备上的混合 Azure AD 联接的受控验证

若要注册 Windows 下层设备，组织必须安装 Microsoft 下载中心提供的[适用于 Windows 10 计算机的 Microsoft 工作区加入](https://www.microsoft.com/download/details.aspx?id=53554)。

你可以使用软件分发系统（如 [Microsoft Endpoint Configuration Manager](https://docs.microsoft.com/configmgr/)）部署包。 此包支持使用标准无提示安装选项（包含 quiet 参数）。 Configuration Manager 的 Current Branch 提供优于早期版本的优势，例如可以跟踪已完成的注册。

安装程序会在系统上创建一项计划任务，该任务会在用户的上下文中运行。 当用户登录到 Windows 时触发该任务。 通过 Azure AD 进行身份验证后，该任务以无提示方式使用用户凭据将设备联接到 Azure AD。

要控制设备注册，应将 Windows Installer 包部署到所选的 Windows 下层设备组。

> [!NOTE]
> 如果在 AD 中未配置 SCP，则应遵循与使用组策略对象（GPO）在已加入域的计算机上[配置 scp 的客户端注册表设置](#configure-client-side-registry-setting-for-scp)相同的方法。


验证所有内容是否按预期运行后，可以通过[使用 Azure AD Connect 配置 SCP](hybrid-azuread-join-managed-domains.md#configure-hybrid-azure-ad-join)，使用 Azure AD 自动注册 Windows 当前和下层设备的其余部分。

## <a name="next-steps"></a>后续步骤

[计划混合 Azure Active Directory 加入实现](hybrid-azuread-join-plan.md)
