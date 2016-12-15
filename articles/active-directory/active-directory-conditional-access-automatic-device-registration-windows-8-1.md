---
title: "为加入 Windows 8.1 域的设备配置自动设备注册 | Microsoft Docs"
description: " 逐步说明如何配置组策略，以将加入 Windows 8.1 域的设备自动注册到 Azure AD。 "
services: active-directory
documentationcenter: 
author: femila
manager: swadhwa
editor: 
ms.assetid: 402271c6-d577-4264-8b75-fe78030b18e9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2016
ms.author: Markvi
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 03585694b3934147f1f5d58615155ba8ae4d1bc0


---
# <a name="configure-automatic-device-registration-for-windows-81-domain-joined-devices"></a>为加入 Windows 8.1 域的设备配置自动设备注册
你可以使用 Active Directory 组策略将已加入 Windows 8.1 域的设备配置为自动注册到 Azure AD。 若要配置组策略，必须拥有至少一个加入域的 Windows Server 2012 R2 或 Windows 8.1 计算机安装了组策略管理功能。 一旦域启用此组策略，任何登录到计算机的域用户都将自动且无提示地注册到 Azure AD 中的一个设备对象。 在 Azure AD 中，物理设备的每个已注册用户都有一个设备对象。请务必通读并完成“将已加入 Windows 域的设备自动注册到 Azure Active Directory”中所述的先决条件。

> [!NOTE]
> 有关如何设置自动设备注册的最新说明，请参阅[如何使用 Azure Active Directory 设置已加入 Windows 域的设备的自动注册](active-directory-conditional-access-automatic-device-registration-setup.md)。
> 
> 

## <a name="configure-the-group-policy-for-your-windows-81-domain-joined-devices"></a>为已加入 Windows 8.1 域的设备配置组策略
1. 打开“服务器管理器”，并导航到“工具” > “组策略管理”。
2. 从“组策略管理”，导航到与要启用“自动加入工作区”功能的域相对应的域节点。
3. 右键单击“组策略对象”并选择“新建”。 为组策略对象命名，例如**自动工作区加入**。 单击“确定”。
4. 右键单击你的新组策略对象，然后选择“编辑”。
5. 导航到“计算机配置” > “策略” > “管理模板” > “Windows 组件” > “工作区加入”。
6. 右键单击“自动将客户端计算机加入工作区”，然后选择“编辑”。
7. 选择“已启用”单选按钮，然后单击“应用”。 单击“确定”。
8. 现在你可以将该组策略对象链接到所选位置。 若要为组织中所有已加入域的 Windows 8.1 设备启用此策略，请将此组策略链接到相应域。

## <a name="unregistering-your-windows-81-domain-joined-devices"></a>取消注册已加入 Windows 8.1 域的设备
你可以选择取消注册已加入 Windows 8.1 域的设备，具体操作如下：修改在上一部分中创建的工作区加入组策略设置。 将“自动将客户端计算机加入工作区”策略设置为“已禁用”。 这将阻止新设备自动加入工作区。

通过以下两个选项之一，取消注册已加入现有域的 Windows 8.1 计算机：

* 选项 1：**使用“电脑设置”取消注册已加入 Windows 8.1 域的设备**
  
  1. 在 Windows 8.1 设备上，导航到“电脑设置” > “网络” > “工作区”
  2. 选择“退出”。
     必须针对已登录到计算机并已自动加入工作区的每个域用户都重复此过程。
* 选项 2：使用脚本取消注册已加入 Windows 8.1 域的设备
  
      1. 在 Windows 8.1 计算机上打开命令提示符，并执行以下命令：` %SystemRoot%\System32\AutoWorkplace.exe leave`

此命令必须在已登录到计算机的每个域用户的上下文中运行。

## <a name="event-viewer-errors-for-windows-81-domain-joined-devices"></a>事件查看器和针对已加入 Windows 8.1 域的设备的错误。
Windows 8.1 计算机上的 Windows 事件日志将显示与设备注册相关的消息。 你可以找到成功和不成功的事件消息。 

可以在事件查看器中的“应用程序和服务日志” > “Microsoft” > “Windows”>“工作区加入”下面找到事件日志。

## <a name="additional-details"></a>其他详细信息
组策略会在系统上启用一项计划任务，该任务将在用户的上下文中运行并在用户登录时触发。 登录完成之后，该任务将以无提示方式向 Azure AD 注册用户和设备。 在 Windows 8.1 设备上，可以在“Microsoft” > “Windows” > “工作区加入”下面的“任务计划程序库”中找到该计划任务。 该任务将运行并注册所有登录到计算机的 Active Directory 用户。 

## <a name="additional-topics"></a>其他主题
* [Azure Active Directory 设备注册概述](active-directory-conditional-access-device-registration-overview.md)
* [将已加入域的 Windows 10 设备自动注册到 Azure Active Directory](active-directory-conditional-access-automatic-device-registration.md)
* [为加入 Windows 7 域的设备配置自动设备注册](active-directory-conditional-access-automatic-device-registration-windows7.md)




<!--HONumber=Nov16_HO3-->


