---
title: 如何管理已加入 Azure AD 的设备上的本地管理员组 | Microsoft Docs
description: 了解如何将 Azure 角色分配给 Windows 设备的本地管理员组。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2018
ms.author: markvi
ms.reviewer: ravenn
ms.openlocfilehash: b72cab6ce90f0cbb74afd3e00a9aec73fb2908f7
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2018
ms.locfileid: "40003774"
---
# <a name="how-to-manage-the-local-administrators-group-on-azure-ad-joined-devices"></a>如何管理已加入 Azure AD 的设备上的本地管理员组

若要管理 Windows 设备，需要成为本地管理员组的成员。 作为 Azure Active Directory (Azure AD) 联接过程的一部分，Azure AD 会更新设备上此组的成员身份。 可以自定义成员身份更新以满足业务需求。 例如，如果希望帮助台员工在设备上执行需要管理员权限的任务，则成员身份更新会非常有帮助。

本文介绍了成员身份更新的工作原理以及在加入 Azure AD 期间如何对其进行自定义。 本文的内容不适用于加入“混合” Azure AD。


## <a name="how-it-works"></a>工作原理

使用 Azure AD 联接将 Windows 设备与 Azure AD 连接时，Azure AD 会将以下安全原则添加到设备上的本地管理员组：

- Azure AD 全局管理员角色
- Azure AD 设备管理员角色 
- 执行 Azure AD 联接的用户   

通过将 Azure AD 角色添加到本地管理员组，可以在 Azure AD 中随时更新可管理设备的用户，而无需修改设备上的任何内容。 目前，无法将组分配到管理员角色。
Azure AD 还会将 Azure AD 设备管理员角色添加到本地管理员组，以支持最小特权原则 (PoLP)。 除全局管理员之外，还可启用仅分配了设备管理员角色的用户来管理设备。 


## <a name="manage-the-global-administrators-role"></a>管理全局管理员角色

要查看并更新全局管理员角色的成员身份，请参阅：

- [在 Azure Active Directory 中查看管理员角色的所有成员](../users-groups-roles/directory-manage-roles-portal.md)

- [在 Azure Active Directory 中向用户分配管理员角色](../fundamentals/active-directory-users-assign-role-azure-portal.md)


## <a name="manage-the-device-administrator-role"></a>管理设备管理员角色 

在 Azure 门户中，可以管理“设备”页上的设备管理员角色。 要打开“设备”页，请执行以下操作：

1. 以全局管理员或用户管理员身份登录到 [Azure 门户](https://portal.azure.com)。
2. 在左侧导航栏中，单击“Azure Active Directory”。 
3. 在“管理”部分单击“设备”。
4. 在“设备”页上，单击“设备设置”。

要修改设备管理员角色，请配置“已加入 Azure AD 的设备上的其他本地管理员”。  

![其他本地管理员](./media/assign-local-admin/10.png)

 
设备管理员已分配给所有已加入 Azure AD 的设备。 无法将设备管理员范围限定为一组特定设备。 更新设备管理员角色不一定会对受影响的用户产生直接影响。 对于用户已登录的设备，在以下情况下会发生特权更新：
     

- 用户退出时。
- 颁发新的主刷新令牌 4 个小时之后。 




## <a name="manage-regular-users"></a>管理常规用户

默认情况下，Azure AD 会将执行 Azure AD 联接的用户添加到设备上的管理员组。 如果希望防止常规用户成为本地管理员，可以使用以下选项：

- [Windows Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) - Windows Autopilot 提供了一个选项，可以防止执行联接的主用户成为本地管理员。 可通过[创建 Autopilot 配置文件](https://docs.microsoft.com/intune/enrollment-autopilot#create-an-autopilot-deployment-profile)完成此操作。
 
- [批量注册](https://docs.microsoft.com/intune/windows-bulk-enroll) - 在批量注册的上下文中执行的 Azure AD 联接发生在自动创建的用户的上下文中。 在已加入设备后才登录的用户不会被添加到管理员组。   



## <a name="manually-elevate-a-user-on-a-device"></a>手动提升设备上的用户 

除使用 Azure AD 联接过程之外，还可手动将常规用户提升为某个特定设备上的本地管理员。 此步骤要求用户已是本地管理员组的成员。 

从 Windows 10 1709 版本开始，可通过选择“添加工作或学校用户”，从“设置”->“帐户”->“其他用户”中执行此任务。
 
此外，还可使用命令提示符添加用户：

- 如果从本地 Active Directory 同步了租户用户，请使用 `net localgroup administrators /add “Contoso\username”`。

- 如果在 Azure AD 中创建了租户用户，请使用 `net localgroup administrators /add “AzureAD\UserUpn”`


## <a name="considerations"></a>注意事项 

无法将组分配给设备管理员角色，仅允许分配单个用户。

设备管理员已分配给所有已加入 Azure AD 的设备。 无法将他们的范围限定为一组特定设备。

从设备管理员角色中删除用户时，只要用户登录设备，他们仍拥有设备的本地管理员权限。 下次登录期间或者颁发新的主刷新令牌 4 小时后，将撤销该权限。



## <a name="next-steps"></a>后续步骤

- 若要大致了解如何在 Azure 门户中管理设备，请参阅[使用 Azure 门户管理设备](device-management-azure-portal.md)

- 有关基于设备的条件性访问的详细信息，请参阅[配置 Azure Active Directory 基于设备的条件性访问策略](../conditional-access/require-managed-devices.md)。


