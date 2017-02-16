---
title: "排查已加入 Azure AD 域的 Windows 下层客户端计算机的自动注册问题 | Microsoft 文档"
description: "排查已加入 Azure AD 域的 Windows 下层客户端计算机的自动注册问题。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 535badb5444fd4c29211f83556c0a750ee8adf70
ms.openlocfilehash: 0e031760b84aa59fefda2469606475b36dbc1ff7


---
# <a name="troubleshooting-the-auto-registration-of-azure-ad-domain-joined-computers-for-windows-down-level-clients"></a>排查已加入 Azure AD 域的 Windows 下层客户端计算机的自动注册问题 

本主题仅适用于以下客户端： 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 
 

对于 Windows 10 或 Windows Server 2016，请参阅[排查已加入 Azure AD 域的 Windows 10 和 Windows Server 2016 计算机的自动注册问题](active-directory-conditional-access-automatic-device-registration-troubleshoot-windows.md)。

本主题假设已根据[如何配置已加入域的 Windows 设备的 Azure Active Directory 自动注册](active-directory-conditional-access-automatic-device-registration-get-started.md)中所述配置了已加入域的设备的自动注册。
 
本主题提供有关如何解决潜在问题的故障排除指导。  
为确保成功解决问题而需要注意的事项： 

- 在 Azure AD 上注册这些客户端是针对每个用户/设备执行的。 例如：如果 jdoe 和 jharnett 登录到了此设备，将在用户信息选项卡中为其中的每个用户单独创建一个注册 (DeviceID)。  

- 这些客户端的注册默认配置为在登录或锁定/解锁时尝试，可能有 5 分钟的延迟，因为此活动是使用任务计划程序任务触发的。 

- 重新安装操作系统或者手动取消注册再重新注册可能会在 Azure AD 上创建新的注册，并导致 Azure 门户中的用户信息选项卡下出现多个条目。 


## <a name="step-1-retrieve-the-registration-status"></a>步骤 1：检索注册状态 

**验证注册状态：**  

1. 以管理员身份打开命令提示符 

2. 键入 `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /i"`

此命令将显示一个对话框，其中提供了有关加入状态的更多详细信息。

![适用于 Windows 的工作区加入](./media/active-directory-conditional-access-automatic-device-registration-troubleshoot-windows-legacy/01.png)


## <a name="step-2-evaluate-the-registration-status"></a>步骤 2：评估注册状态 

如果加入不成功，对话框中会提供有关发生的问题的详细信息。

**最常见的问题包括：**

- AD FS 或 Azure AD 配置不当

    ![适用于 Windows 的工作区加入](./media/active-directory-conditional-access-automatic-device-registration-troubleshoot-windows-legacy/02.png)

- 登录身份不是域用户

    ![适用于 Windows 的工作区加入](./media/active-directory-conditional-access-automatic-device-registration-troubleshoot-windows-legacy/03.png)

- 已达到配额

    ![适用于 Windows 的工作区加入](./media/active-directory-conditional-access-automatic-device-registration-troubleshoot-windows-legacy/04.png)

- 服务未响应 

    ![适用于 Windows 的工作区加入](./media/active-directory-conditional-access-automatic-device-registration-troubleshoot-windows-legacy/05.png)

也可以在“应用程序和服务日志\Microsoft 工作区加入”下面的事件日志中找到状态信息。
  
**注册失败的最常见原因包括：** 

- 计算机不在组织的内部网络上，或者在未与本地 AD 域控制器建立连接的 VPN 上。

- 使用本地计算机帐户登录到了计算机。 

- 服务配置问题： 

  - 联合服务器已配置为支持 **WIAORMULTIAUTHN**。 

  - 在计算机所属的 AD 林中的 Azure AD 内，不存在指向已验证域名的“服务连接点”对象。

  - 用户已达到设备限制。 请参阅“Azure Active Directory 设备注册入门”。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅[自动设备注册常见问题](active-directory-conditional-access-automatic-device-registration-faq.md) 


<!--HONumber=Feb17_HO1-->


