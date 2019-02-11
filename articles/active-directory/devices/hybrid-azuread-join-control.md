---
title: 控制设备的混合 Azure AD 联接 | Microsoft Docs
description: 了解如何在 Azure Active Directory 中控制设备的混合 Azure AD 联接。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2018
ms.author: markvi
ms.reviewer: sandeo
ms.openlocfilehash: 52f3859edcc8a524c56f91ad2a5346a69ea534ed
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2019
ms.locfileid: "55103957"
---
# <a name="control-the-hybrid-azure-ad-join-of-your-devices"></a>控制设备的混合 Azure AD 加入

混合 Azure Active Directory (Azure AD) 联接是自动将已加入域的本地设备注册到 Azure AD 的过程。 在某些情况下，你并不希望自动注册所有设备。 例如，在初始部署期间验证一切是否都按预期运行时，这一点就非常必要。

本文指导如何控制设备的混合 Azure AD 联接。 


## <a name="prerequisites"></a>先决条件

本文假定你熟悉以下内容：

-  [Azure Active Directory 中的设备管理简介](../device-management-introduction.md)
 
-  [计划混合 Azure Active Directory 加入实现](hybrid-azuread-join-plan.md)

-  [为托管域配置混合 Azure Active Directory 联接](hybrid-azuread-join-managed-domains.md)[为联合域配置混合 Azure Active Directory 联接](hybrid-azuread-join-federated-domains.md)



## <a name="control-windows-current-devices"></a>控制 Windows 当前设备

对于运行 Windows 桌面操作系统的设备，支持的版本是 Windows 10 周年更新（版本 1607）或更高版本。 最佳做法是升级到最新版本的 Windows 10。

在设备启动或用户登录时，所有 Windows 当前设备将自动向 Azure AD 注册。 可以使用组策略对象 (GPO) 或 System Center Configuration Manager 控制此行为。

若要控制 Windows 当前设备，需要： 


1.  **对于所有设备**：禁用自动设备注册。
2.  **对于所选的设备**：启用自动设备注册。

在确认一切均按预期工作后，可以对所有设备再次启用自动设备注册功能。



### <a name="group-policy-object"></a>组策略对象 

可以通过部署以下 GPO 来控制设备的设备注册行为：将已加入域的计算机注册为设备。

设置 GPO：

1.  打开“服务器管理器”，再依次转到“工具” > “组策略管理”。

2.  转到与要禁用或启用自动注册的域所对应的域节点。

3.  右键单击“组策略对象”并选择“新建”。

4.  输入组策略对象的名称（例如，混合 Azure AD 联接）。 

5.  选择“确定”。

6.  右键单击新建的 GPO，并选择“编辑”。

7.  转到“计算机配置” > “策略” > “管理模板” > “Windows 组件” > “设备注册”。 

8.  右键单击“将已加入域的计算机注册为设备”，然后选择“编辑”。

    > [!NOTE] 
    > 已从早期版本的组策略管理控制台对该组策略模板进行了重命名。 如果使用早期版本的控制台，请转到“计算机配置” > “策略” > “管理模板” > “Windows 组件” > “工作区加入” > “自动将客户端计算机加入工作区”。 

9.  选择以下某一项设置，然后选择“应用”：

    - **已禁用**：防止自动注册设备。
    - **已启用**：启用自动设备注册功能。

10. 选择“确定”。

需要将 GPO 链接到所选位置。 例如，若要对组织中所有已加入域的当前设备设置此策略，请将 GPO 链接到域。 若要执行受控部署，请对组织单位或安全组中已加入域的 Windows 当前设备设置此策略。

### <a name="configuration-manager-controlled-deployment"></a>Configuration Manager 控制的部署 

可以通过配置以下客户端设置来控制当前设备的设备注册行为：自动向 Azure Active Directory 注册已加入域的新 Windows 10 设备。

配置客户端设置：

1.  打开 Configuration Manager，然后转到“云服务”。

2.  在“设备设置”下，为“自动向 Azure Active Directory 注册已加入域的新 Windows 10 设备”选择下列某一设置：

    - **否**：防止自动注册设备。
    - **是**：启用自动设备注册功能。


3.  选择“确定”。
    

需要将此客户端设置链接到所选位置。 例如，若要对组织中的所有 Window 当前设备配置此客户端设置，请将客户端设置链接到域。 若要执行受控部署，请对组织单位或安全组中已加入域的 Windows 当前设备配置此客户端设置。

> [!Important]
> 虽然上述配置负责处理现有的已加入域的 Windows 10 设备，但由于设备上组策略或 Configuration Manager 设置的应用可能会延迟，新加入域的设备可能仍会尝试完成混合 Azure AD 联接。 
>
> 要避免此问题，我们建议你创建一个新的 Sysprep 映像（用作预配方法的示例）。 从以前从未进行混合 Azure AD 联接并且已应用组策略设置或 Configuration Manager 客户端设置的设备创建该映像。 此外，还必须使用新映像预配加入组织域的新计算机。 

## <a name="control-windows-down-level-devices"></a>控制 Windows 下层设备

若要注册 Windows 下层设备，需要在[适用于非 Windows 10 计算机的 Microsoft 工作区加入](https://www.microsoft.com/download/details.aspx?id=53554)网页中通过下载中心下载并安装 Windows Installer 包 (.msi)。

可以使用 [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager) 等软件分发系统部署该包。 此包支持使用标准无提示安装选项（包含 quiet 参数）。 Configuration Manager 的 Current Branch 提供优于早期版本的优势，例如可以跟踪已完成的注册。

安装程序会在系统上创建一项计划任务，该任务会在用户的上下文中运行。 当用户登录到 Windows 时触发该任务。 通过 Azure AD 进行身份验证后，该任务以无提示方式使用用户凭据将设备联接到 Azure AD。

若要控制设备注册，应仅对所选的 Windows 下层设备组部署 Windows Installer 包。 如果已确认一切均按预期工作，便可以将包部署到所有下层设备。


## <a name="next-steps"></a>后续步骤

* [Azure Active Directory 中的设备管理简介](../device-management-introduction.md)



