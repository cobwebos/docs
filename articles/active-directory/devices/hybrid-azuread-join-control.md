---
title: 受控的混合 Azure AD 加入-Azure AD 的验证
description: 了解如何执行混合 Azure AD 联接的一次性全部在整个组织启用之前受控的验证
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: article
ms.date: 05/30/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd5b388f92a875fb2635037a6eae3ff3b6a95793
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66513285"
---
# <a name="controlled-validation-of-hybrid-azure-ad-join"></a>以受控方式验证混合 Azure AD 加入

当所有系统必备组件到位，将自动为你的 Azure AD 租户中的设备注册 Windows 设备。 Azure AD 中的这些设备标识状态称为混合 Azure AD 联接。 可以在文章中找到有关本文中所述的概念的详细信息[Azure Active Directory 中的设备管理简介](overview.md)和[计划你的混合 Azure Active Directory 联接实现](hybrid-azuread-join-plan.md).

组织可能想要执行混合 Azure AD 联接的一次性其整个组织启用之前受控的验证。 本文将介绍如何完成混合 Azure AD 联接的受控的验证。

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-current-devices"></a>Windows 当前设备上的混合 Azure AD 联接的受控的验证

对于运行 Windows 桌面操作系统的设备，支持的版本是 Windows 10 周年更新（版本 1607）或更高版本。 最佳做法是升级到最新版本的 Windows 10。

若要执行 Windows 当前设备上的混合 Azure AD 联接的受控的验证，需要：

1. 如果它存在，请清除服务连接点 (SCP) 条目从 Active Directory (AD)
1. 使用组策略对象 (GPO) 已加入域的计算机上配置 SCP 的客户端的注册表设置
1. 如果使用 AD FS，还必须使用 GPO 在 AD FS 服务器上配置 SCP 的客户端的注册表设置  



### <a name="clear-the-scp-from-ad"></a>清除从 AD SCP

使用 Active Directory 服务界面编辑器 (Adsi) 来修改在 AD 中的 SCP 对象。

1. 启动**ADSI Edit**从桌面应用程序和管理工作站或域控制器以企业管理员身份。
1. 连接到**配置命名上下文**你的域名。
1. 浏览到**CN = Configuration，DC = contoso，DC = com** > **CN = Services** > **CN = Device Registration Configuration**
1. 右键单击下的叶对象**CN = Device Registration Configuration** ，然后选择**属性**
   1. 选择**关键字**从**属性编辑器**窗口，然后单击**编辑**
   1. 选择的值**azureADId**并**azureADName** （一次一个） 并单击**删除**
1. 关闭**ADSI 编辑**


### <a name="configure-client-side-registry-setting-for-scp"></a>配置 SCP 的客户端的注册表设置

使用下面的示例创建要部署注册表设置的设备注册表中配置 SCP 条目，组策略对象 (GPO)。

1. 打开组策略管理控制台，并在域中创建新的组策略对象。
   1. 提供你新创建的 GPO 的名称 (例如，ClientSideSCP)。
1. 编辑 GPO，并找到以下路径：**计算机配置** > **首选项** > **Windows 设置** > **注册表**
1. 右键单击注册表并选择**新建** > **注册表项**
   1. 上**常规**选项卡上，配置以下各项
      1. 操作：**更新**
      1. 配置单元：**HKEY_LOCAL_MACHINE**
      1. 项路径：**SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. 值名称：**TenantId**
      1. 值类型：**REG_SZ**
      1. 值数据：GUID 或**Directory ID**的 Azure AD 实例 (此值可在**Azure 门户** > **Azure Active Directory**  >  **属性** > **Directory ID**)
   1. 单击 **“确定”**
1. 右键单击注册表并选择**新建** > **注册表项**
   1. 上**常规**选项卡上，配置以下各项
      1. 操作：**更新**
      1. 配置单元：**HKEY_LOCAL_MACHINE**
      1. 项路径：**SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. 值名称：**TenantName**
      1. 值类型：**REG_SZ**
      1. 值数据：你已验证**域名**Azure AD 中 (例如，`contoso.onmicrosoft.com`或任何其他已验证的域名在目录中)
   1. 单击 **“确定”**
1. 关闭新创建的 gpo 编辑器
1. 将新创建的 GPO 链接到所需的 OU 包含属于受控的推出填充的已加入域的计算机

### <a name="configure-ad-fs-settings"></a>配置 AD FS 设置

如果使用 AD FS，首先需要配置客户端的 SCP 使用前面所述，但将 GPO 链接到 AD FS 服务器的说明进行操作。 适用于 AD FS 来建立设备标识的源为 Azure AD 需要此配置。

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices"></a>Windows 下层设备上的混合 Azure AD 联接的受控的验证

若要注册 Windows 下层设备，组织必须安装[适用于 Windows 10 计算机的 Microsoft 工作区加入](https://www.microsoft.com/download/details.aspx?id=53554)可从 Microsoft 下载中心获得。

可以通过使用如软件分发系统部署该包 [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager)。 此包支持使用标准无提示安装选项（包含 quiet 参数）。 Configuration Manager 的 Current Branch 提供优于早期版本的优势，例如可以跟踪已完成的注册。

安装程序会在用户上下文中运行的系统上创建计划的任务。 当用户登录到 Windows 时触发该任务。 通过 Azure AD 进行身份验证后，该任务以无提示方式使用用户凭据将设备联接到 Azure AD。

若要控制的设备注册，您应该向所选组的 Windows 下层设备部署的 Windows Installer 包。

> [!NOTE]
> 如果在 AD 中，未配置 SCP，则应遵循相同的方法，如所述向[配置 SCP 的客户端的注册表设置](#configure-client-side-registry-setting-for-scp)) 使用组策略对象 (GPO) 已加入域的计算机上。


在验证一切按预期工作后，自动注册到 Azure AD 的其他 Windows 当前和低级别设备[配置使用 Azure AD Connect 的 SCP](hybrid-azuread-join-managed-domains.md#configure-hybrid-azure-ad-join)。

## <a name="next-steps"></a>后续步骤

[计划混合 Azure Active Directory 加入实现](hybrid-azuread-join-plan.md)
