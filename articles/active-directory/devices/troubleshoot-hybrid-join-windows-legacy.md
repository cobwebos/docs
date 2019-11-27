---
title: 排除旧版混合 Azure Active Directory 联接设备的故障
description: 排查已加入混合 Azure Active Directory 的下层设备问题。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: e168deea1ba442d48f483264c1e97ce618040f18
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74379119"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-down-level-devices"></a>排查已加入混合 Azure Active Directory 的下层设备问题 

本文仅适用于以下设备： 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 

对于 Windows 10 或 Windows Server 2016，请参阅[排查已加入混合 Azure Active Directory 的 Windows 10 和 Windows Server 2016 设备问题](troubleshoot-hybrid-join-windows-current.md)。

本文假设你已[配置已加入混合 Azure Active Directory 的设备](hybrid-azuread-join-plan.md)，以支持以下方案：

- 基于设备的条件性访问

本文提供有关如何解决潜在问题的故障排除指导。  

**应了解的内容：** 

- 下层 Windows 设备混合 Azure AD 加入的工作方式与它在 Windows 10 中的工作方式略有不同。 许多客户没有意识到他们需要配置 AD FS（对于联合域）或无缝 SSO（对于托管域）。
- 对于具有联合域的客户，如果服务连接点 (SCP) 配置为指向托管域名（例如 contoso.onmicrosoft.com 而非 contoso.com），则下层 Windows 设备混合 Azure AD 加入不会工作。
- 每个用户的最大设备数也适用于加入了混合 Azure AD 的下层设备。 
- 当有多个域用户登录到加入了混合 Azure AD 的下层设备时，同一物理设备在 Azure AD 中出现多次。  例如：如果 *jdoe* 和 *jharnett* 登录到某个设备，会在“用户信息”选项卡中为其中每个用户单独创建一个注册 (DeviceID)。 
- 由于操作系统的重新安装或手动重新注册，在用户信息选项卡上也可能会出现同一设备的多个条目。
- 设备的初始注册/加入配置为在登录或锁定/解锁时执行尝试。 可能会有 5 分钟的延迟，由任务计划程序任务触发。 
- 对于 Windows 7 SP1 或 Windows Server 2008 R2 SP1，请确保安装 [KB4284842](https://support.microsoft.com/help/4284842)。 此更新可防止将来因客户更改密码后无法访问受保护密钥而导致身份验证失败。

## <a name="step-1-retrieve-the-registration-status"></a>步骤 1：检索注册状态 

**验证注册状态：**  

1. 使用已执行混合 Azure AD 加入的用户帐户登录。
1. 打开命令提示符 
1. 键入 `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe" /i`

此命令会显示一个对话框，其中提供了有关加入状态的详细信息。

![适用于 Windows 的工作区加入](./media/troubleshoot-hybrid-join-windows-legacy/01.png)

## <a name="step-2-evaluate-the-hybrid-azure-ad-join-status"></a>步骤 2：评估混合 Azure AD 加入状态 

如果设备未加入混合 Azure AD，则可以通过单击“加入”按钮来尝试执行混合 Azure AD 加入。 如果尝试执行混合 Azure AD 加入失败，则会显示关于失败的详细信息。

**最常见的问题包括：**

- AD FS 或 Azure AD 配置不当或网络存在问题

    ![适用于 Windows 的工作区加入](./media/troubleshoot-hybrid-join-windows-legacy/02.png)
    
   - Autoworkplace.exe 无法以无提示方式通过 Azure AD 或 AD FS 进行身份验证。 可能的原因如下：AD FS 缺少或配置不当（对于联合域）、Azure AD 无缝单一登录缺少或配置不当（对于托管域）或者网络存在问题。 
   - 这可能是因为为用户启用了多重身份验证（MFA），并且未在 AD FS 服务器上配置 WIAORMULTIAUTHN。 
   - 另一种可能性是主领域发现 (HRD) 页面正在等待用户交互，从而阻止了 **autoworkplace.exe** 以无提示方式请求令牌。
   - 客户端的 IE 的 intranet 区域中可能缺少 AD FS 和 Azure AD URL。
   - 网络连接问题可能阻止 **autoworkplace.exe** 访问 AD FS 或 Azure AD URL。 
   - **了 autoworkplace.exe**要求客户端能够从客户端直接连接到组织的本地 AD 域控制器，这意味着仅当客户端连接到组织的 intranet 时，混合 Azure AD 联接才会成功。
   - 你的组织使用 Azure AD 无缝单一登录，设备的 IE intranet 设置中不存在 `https://autologon.microsoftazuread-sso.com` 或 `https://aadg.windows.net.nsatc.net`，未对 Intranet 区域启用“允许通过脚本更新状态栏”。
- 登录身份不是域用户

   ![适用于 Windows 的工作区加入](./media/troubleshoot-hybrid-join-windows-legacy/03.png)

   以下几种不同原因可能会导致此问题：

   - 已登录的用户不是域用户（例如，本地用户）。 低级别设备上的混合 Azure AD 联接仅支持域用户。
   - 客户端无法连接到域控制器。    
- 已达到配额

    ![适用于 Windows 的工作区加入](./media/troubleshoot-hybrid-join-windows-legacy/04.png)

- 服务未响应 

    ![适用于 Windows 的工作区加入](./media/troubleshoot-hybrid-join-windows-legacy/05.png)

此外，也可以在“应用程序和服务日志\Microsoft-Workplace Join”下面的事件日志中找到状态信息
  
**混合 Azure AD 加入失败的最常见原因是：** 

- 计算机既没有连接到组织的内部网络，也没有连接到与本地 AD 域控制器建立连接的 VPN。
- 使用本地计算机帐户登录到了计算机。 
- 服务配置问题： 
   - AD FS 服务器未配置为支持 **WIAORMULTIAUTHN**。 
   - 在 Azure AD 中，计算机的林内没有指向已验证域名的“服务连接点”对象 
   - 或者，如果你的域是托管的，则无缝 SSO 未配置或未在工作。
   - 用户已达到设备限制。 

## <a name="next-steps"></a>后续步骤

如有问题，请参阅[设备管理常见问题解答](faq.md)  
