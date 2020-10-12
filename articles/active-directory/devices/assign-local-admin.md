---
title: 如何管理 Azure AD 联接设备上的本地管理员
description: 了解如何将 Azure 角色分配给 Windows 设备的本地管理员组。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d3082e3dc45102bc8700c7d1285ef832d09712a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87419812"
---
# <a name="how-to-manage-the-local-administrators-group-on-azure-ad-joined-devices"></a>如何管理已加入 Azure AD 的设备上的本地管理员组

若要管理 Windows 设备，需要成为本地管理员组的成员。 作为 Azure Active Directory (Azure AD) 联接过程的一部分，Azure AD 会更新设备上此组的成员身份。 可以自定义成员身份更新以满足业务需求。 例如，如果希望帮助台员工在设备上执行需要管理员权限的任务，则成员身份更新会非常有帮助。

本文介绍本地管理员成员身份更新的工作方式，以及如何在 Azure AD 加入过程中对其进行自定义。 本文内容不适用于已 **加入混合 Azure AD** 的设备。

## <a name="how-it-works"></a>工作原理

使用 Azure AD join 连接 Windows 设备与 Azure AD 时 Azure AD 会将以下安全主体添加到设备上的本地管理员组：

- Azure AD 全局管理员角色
- Azure AD 设备管理员角色 
- 执行 Azure AD 联接的用户   

通过将 Azure AD 角色添加到本地管理员组，可以在 Azure AD 中随时更新可管理设备的用户，而无需修改设备上的任何内容。 目前，无法将组分配到管理员角色。
Azure AD 还会将 Azure AD 设备管理员角色添加到本地管理员组，以支持最小特权原则 (PoLP)。 除全局管理员之外，还可启用仅分配了设备管理员角色的用户来管理设备**。 

## <a name="manage-the-global-administrators-role"></a>管理全局管理员角色

要查看并更新全局管理员角色的成员身份，请参阅：

- [在 Azure Active Directory 中查看管理员角色的所有成员](../users-groups-roles/directory-manage-roles-portal.md)
- [在 Azure Active Directory 中向用户分配管理员角色](../fundamentals/active-directory-users-assign-role-azure-portal.md)


## <a name="manage-the-device-administrator-role"></a>管理设备管理员角色 

在 Azure 门户中，可以管理“设备”页上的设备管理员角色****。 要打开“设备”页，请执行以下操作****：

1. 以全局管理员身份登录到 [Azure 门户](https://portal.azure.com)。
1. 搜索并选择“Azure Active Directory”  。
1. 在“管理”部分单击“设备”。********
1. 在“设备”页上，单击“设备设置”********。

要修改设备管理员角色，请配置“已加入 Azure AD 的设备上的其他本地管理员”****。  

![其他本地管理员](./media/assign-local-admin/10.png)

>[!NOTE]
> 此选项需要 Azure AD Premium 租户。 

设备管理员已分配给所有已加入 Azure AD 的设备。 无法将设备管理员范围限定为一组特定设备。 更新设备管理员角色不一定会对受影响的用户产生直接影响。 在用户已登录到的设备上，当发生以下 *两项* 操作时，会进行权限提升：

- 超过4小时后，Azure AD 使用适当的权限颁发新的主刷新令牌。 
- 用户注销并重新登录，而不是锁定/取消锁定，以刷新其配置文件。

>[!NOTE]
> 上述操作不适用于以前未登录到相关设备的用户。 在这种情况下，将在首次登录设备后立即应用管理员权限。 

## <a name="manage-administrator-privileges-using-azure-ad-groups-preview"></a>使用 Azure AD 组 (预览来管理管理员权限) 

>[!NOTE]
> 此功能目前处于预览状态。

从 Windows 10 2004 update 开始，你可以使用 Azure AD 组来管理使用 [受限制的组](/windows/client-management/mdm/policy-csp-restrictedgroups) MDM 策略对加入 Azure AD 设备的管理员特权。 此策略允许你将单个用户或 Azure AD 组分配给加入 Azure AD 设备上的本地管理员组，这为你提供为不同设备组配置不同管理员的粒度。 

目前，Intune 中没有用于管理此策略的 UI，需要使用 [自定义 Oma-uri 设置](/mem/intune/configuration/custom-settings-windows-10)进行配置。 此策略的几个注意事项： 

- 通过策略添加 Azure AD 组需要可通过执行组 API 获取的组的 SID。 SID 由 `securityIdentifier` 组 API 中的属性定义。
- 强制执行 "限制组" 策略时，将删除组中不在成员列表中的任何当前成员。 因此，对新成员或组强制实施此策略将删除加入设备的用户、设备管理员角色和设备的全局管理员角色的现有管理员。 若要避免删除现有成员，需要在受限制的组策略中将其配置为成员列表的一部分。 
- 此策略仅适用于 Windows 10 设备管理员、用户、来宾、高级用户、远程桌面用户和远程管理用户上的以下已知组。 
- 使用 "受限制的组" 策略管理本地管理员不适用于混合 Azure AD 联接或 Azure AD 注册设备。
- 尽管受限制的组策略在 Windows 10 2004 更新之前存在，但它不支持将 Azure AD 组作为设备的本地管理员组的成员。 

## <a name="manage-regular-users"></a>管理常规用户

默认情况下，Azure AD 会将执行 Azure AD 联接的用户添加到设备上的管理员组。 如果希望防止常规用户成为本地管理员，可以使用以下选项：

- [Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot) - Windows Autopilot 提供了一个选项，可以防止执行联接的主用户成为本地管理员。 可通过[创建 Autopilot 配置文件](/intune/enrollment-autopilot#create-an-autopilot-deployment-profile)完成此操作。
- [批量注册](/intune/windows-bulk-enroll) - 在批量注册的上下文中执行的 Azure AD 联接发生在自动创建的用户的上下文中。 在已加入设备后才登录的用户不会被添加到管理员组。   

## <a name="manually-elevate-a-user-on-a-device"></a>手动提升设备上的用户 

除使用 Azure AD 联接过程之外，还可手动将常规用户提升为某个特定设备上的本地管理员。 此步骤要求用户已是本地管理员组的成员。 

从 **Windows 10 1709** 版本开始，你可以通过 " **设置-> 帐户" （> 其他用户**）来执行此任务。 选择“添加工作单位或学校用户”，在“用户帐户”下输入用户的 UPN，然后在“帐户类型”下选择“管理员”**************  
 
此外，还可使用命令提示符添加用户：

- 如果从本地 Active Directory 同步了租户用户，请使用 `net localgroup administrators /add "Contoso\username"`。
- 如果在 Azure AD 中创建了租户用户，请使用 `net localgroup administrators /add "AzureAD\UserUpn"`

## <a name="considerations"></a>注意事项 

无法将组分配给设备管理员角色，仅允许分配单个用户。

设备管理员已分配给所有已加入 Azure AD 的设备。 无法将他们的范围限定为一组特定设备。

从设备管理员角色中删除用户时，只要用户登录设备，他们仍拥有设备的本地管理员权限。 颁发新的主刷新令牌时，将在下次登录时撤消特权。 此吊销类似于权限提升，可能需要4小时。

## <a name="next-steps"></a>后续步骤

- 若要大致了解如何在 Azure 门户中管理设备，请参阅[使用 Azure 门户管理设备](device-management-azure-portal.md)
- 若要了解有关基于设备的条件性访问的详细信息，请参阅 [配置 Azure Active Directory 基于设备的条件性访问策略](../conditional-access/require-managed-devices.md)。
