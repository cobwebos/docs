---
title: Azure AD Connect：完成混合 Azure AD 加入所要执行的配置后任务 | Microsoft Docs
description: 本文档详细介绍完成混合 Azure AD 加入所要执行的配置后任务
services: active-directory
documentationcenter: ''
author: anandyadavmsft
manager: samueld
editor: billmath
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: anandyadavmsft
ms.openlocfilehash: 02f1cbd1f2b8f7b0bec2f8016a300ede1d6f0439
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
ms.locfileid: "34354508"
---
# <a name="post-configuration-tasks-for-hybrid-azure-ad-join"></a>混合 Azure AD 加入所需的配置后任务

运行 Azure AD Connect 为组织配置混合 Azure AD 加入后，必须执行几个附加的步骤才能最终完成该设置。  请仅执行适用于自己设备的步骤。

## <a name="1-configure-controlled-rollout-optional"></a>1.配置受控的实施（可选）
完成所有配置步骤后，运行 Windows 10 和 Windows Server 2016 的所有已加入域的设备会自动注册到 Azure AD。 如果你希望进行受控的实施而不是执行这种自动注册，可以使用组策略有选择地启用或禁用自动实施。  应在启动其他配置步骤之前设置此组策略：Azure AD
* 在 Active Directory 中创建组策略对象。
* 将其命名为 (ex- Hybrid Azure AD join)。
* 编辑策略并转到：“计算机配置”>“策略”>“管理模板”>“Windows 组件”>“设备注册”。

>[!NOTE]
>对于 2012R2，请通过“计算机配置”>“策略”>“管理模板”>“Windows 组件”>“工作区加入”>“自动工作区加入客户端计算机”完成策略设置

* 禁用此设置：“将已加入域的计算机注册为设备”。
* 依次单击“应用”、“确定”。
* 将 GPO 链接到所选的位置（组织单位、安全组或所有设备的域）。

## <a name="2-configure-network-with-device-registration-endpoints"></a>2.使用设备注册终结点配置网络
确保可从组织网络中的计算机访问以下 URL，以便注册到 Azure AD：

* https://enterpriseregistration.windows.net
* https://login.microsoftonline.com
* https://device.login.microsoftonline.com 

## <a name="3-implement-wpad-for-windows-10-devices"></a>3.对 Windows 10 设备实现 WPAD
如果组织通过出站代理访问 Internet，请实现 Web 代理自动发现 (WPAD)，使 Windows 10 计算机能够注册到 Azure AD。

## <a name="4-configure-the-scp-in-any-forests-that-were-not-configured-by-azure-ad-connect"></a>4.在未通过 Azure AD Connect 配置的任何林中配置 SCP 

服务连接点 (SCP) 包含设备用来自动注册的 Azure AD 租户信息。  运行从 Azure AD Connect 下载的 PowerShell 脚本 ConfigureSCP.ps1。

## <a name="5-configure-any-federation-service-that-was-not-configured-by-azure-ad-connect"></a>5.配置尚未通过 Azure AD Connect 配置的任何联合身份验证服务

如果组织使用联合身份验证服务登录到 Azure AD，则 Azure AD 信赖方信任中的声明规则必须允许设备身份验证。 如果将联合身份验证与 AD FS 配合使用，请转到 [AD FS 帮助](https://aka.ms/aadrptclaimrules)以生成声明规则。 如果使用非 Microsoft 联合身份验证解决方案，该联系相关的提供商获得指导。  

>[!NOTE]
>如果使用了 Windows 下层设备，在接收发往 Azure AD 信任的请求时，该服务必须支持颁发 authenticationmethod 和 wiaormultiauthn 声明。 在 AD FS 中，应添加一个用于传递身份验证方法的颁发转换规则。

## <a name="6-enable-azure-ad-seamless-sso-for-windows-down-level-devices"></a>6.为 Windows 下层设备启用 Azure AD 无缝 SSO

如果组织使用密码哈希同步或直通身份验证登录到 Azure AD，请使用该登录方法启用 Azure AD 无缝 SSO，以便对 Windows 下层设备进行身份验证：https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso。 

## <a name="7-set-azure-ad-policy-for-windows-down-level-devices"></a>7.为 Windows 下层设备设置 Azure AD 策略

若要注册 Windows 下层设备，需确保 Azure AD 策略允许用户注册设备。 

* 在 Azure 门户中登录到自己的帐户。
* 转到：“Azure Active Directory”>“设备”>“设备设置”
* 将“用户可以向 Azure AD 注册其设备”设置为“全部”。
* 点击“保存”(Save)

## <a name="8-add-azure-ad-endpoint-to-windows-down-level-devices"></a>8.将 Azure AD 终结点添加到 Windows 下层设备

将 Azure AD 设备身份验证终结点添加到 Windows 下层设备上的本地 Intranet 区域，避免对设备进行身份验证时出现证书提示：https://device.login.microsoftonline.com 

如果使用[无缝 SSO](https://aka.ms/hybrid/sso)，另请在该区域中启用“允许通过脚本更新状态栏”，并添加以下终结点：https://autologon.microsoftazuread-sso.com 

## <a name="9-install-microsoft-workplace-join-on-windows-down-level-devices"></a>9.在 Windows 下层设备上安装 Microsoft 工作区加入

安装程序会在设备系统上创建一个在用户上下文中运行的计划任务。 当用户登录到 Windows 时触发该任务。 使用 Windows 集成身份验证完成身份验证后，该任务将使用用户凭据以静默方式将设备联接到 Azure AD。 下载中心的网址为 https://www.microsoft.com/en-us/download/details.aspx?id=53554。 

## <a name="10-configure-group-policy-to-allow-device-registration"></a>10.配置组策略以允许设备注册

* 在 Active Directory 中创建组策略对象（如果尚未创建）。
* 将其命名为 (ex- Hybrid Azure AD join)。
* 编辑策略并转到：“计算机配置”>“策略”>“管理模板”>“Windows 组件”>“设备注册”
* 启用：“将已加入域的计算机注册为设备”
* 依次单击“应用”、“确定”。
* 将 GPO 链接到所选的位置（组织单位、安全组或所有设备的域）。

>[!NOTE]
>对于 2012R2，请通过“计算机配置”>“策略”>“管理模板”>“Windows 组件”>“工作区加入”>“自动工作区加入客户端计算机”完成策略设置

## <a name="next-steps"></a>后续步骤
[配置设备写回](./active-directory-aadconnect-feature-device-writeback.md)
