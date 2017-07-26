---
title: "Azure Active Directory Connect Health 操作"
description: "本文介绍在部署 Azure AD Connect Health 后可以执行的其他操作。"
services: active-directory
documentationcenter: 
author: karavar
manager: femila
ms.assetid: 86cc3840-60fb-43f9-8b2a-8598a9df5c94
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: fa9983a3d53cf8d6278163a2b7d08c86ae2f0637
ms.contentlocale: zh-cn
ms.lasthandoff: 07/04/2017

---
# <a name="azure-active-directory-connect-health-operations"></a>Azure Active Directory Connect Health 操作
本主题介绍可通过使用 Azure Active Directory (Azure AD) Connect Health 执行的各种操作。

## <a name="enable-email-notifications"></a>启用电子邮件通知
可以对 Azure AD Connect Health 服务进行配置，以便在警报指示标识基础结构运行不正常时发送电子邮件通知。 当生成并解析警报后，会发送电子邮件通知。

![Azure AD Connect Health 电子邮件通知设置的屏幕快照](./media/active-directory-aadconnect-health/email_noti_discover.png)

> [!NOTE]
> 默认情况下电子邮件通知处于启用状态。
>
>

### <a name="to-enable-azure-ad-connect-health-email-notifications"></a>启用 Azure AD Connect Health 电子邮件通知的步骤
1. 打开需要接收电子邮件通知的服务的“警报”边栏选项卡。
2. 单击操作栏中的“通知设置”按钮。
3. 在电子邮件通知开关处，选择“开”。
4. 若要让所有全局管理员能够接收电子邮件通知，请选中该复选框。
5. 若想通过任何其他电子邮件地址接收电子邮件通知，则可在“其他电子邮件收件人”框中进行指定相应邮件地址。 若要从该列表中删除电子邮件地址，可右键单击相应的条目，然后选择“删除”。
6. 若要保存所做更改，请单击“保存”。 只有在保存之后，更改才会生效。

## <a name="delete-a-server-or-service-instance"></a>删除服务器或服务实例

在某些情况下，可能需要从被监视的服务器中删除某个服务器。 以下是关于从 Azure AD Connect Health 服务中删除服务器的说明。

删除服务器时，请注意以下事项：

* 此操作会导致再也无法从该服务器收集任何数据。 将从监视服务中删除此服务器。 执行此操作之后，将无法查看该服务器的新警报、监视数据或使用情况分析数据。
* 此操作不会从服务器中卸载 Health 代理。 如果在执行此步骤之前未卸载 Health 代理，执行此步骤时，则可能会在服务器上看到与 Health 代理相关的错误事件。
* 此操作不会删除已从该服务器上收集的数据。 将根据 Azure 数据保留策略删除该数据。
* 执行此操作后，如果要重新开始监视同一服务器，必须在此服务器上先卸载 Health 代理，然后重新安装。

### <a name="to-delete-a-server-from-the-azure-ad-connect-health-service"></a>从 Azure AD Connect Health 服务中删除服务器的步骤
用于 Active Directory 联合身份验证服务 (AD FS) 的 Azure AD Connect Health 和 Azure AD Connect（同步）：

1. 通过选择要删除的服务器名称，从“服务器列表”边栏选项卡中打开“服务器”边栏选项卡。
2. 在操作栏中的“服务器”边栏选项卡上，单击“删除”。
3. 在确认框中键入服务器名称以进行确认。
4. 单击“删除” 。

用于 Azure Active Directory 域服务的 Azure AD Connect Health：

1. 打开“域控制器”仪表板。
2. 选择要删除的域控制器。
3. 在操作栏中，单击“删除所选项”。
4. 确认删除服务器的操作。
5. 单击“删除” 。

### <a name="delete-a-service-instance-from-azure-ad-connect-health-service"></a>从 Azure AD Connect Health Service 中删除服务实例
在某些情况下，可能需要删除某个服务实例。 以下是关于从 Azure AD Connect Health 服务中删除服务实例的说明。

删除服务实例时，请注意以下事项：

* 此操作将从监视服务中删除当前服务实例。
* 此操作不会从任何服务器中卸载或删除已作为此服务器实例的一部分进行监视的 Health 代理。 如果在执行此步骤之前未卸载 Health 代理，执行此步骤时，则可能会在服务器上看到与 Health 代理相关的错误事件。
* 将根据 Microsoft Azure 数据保留策略删除此服务实例中的所有数据。
* 执行此操作后，如果要开始监视此服务，请在所有服务器上先卸载 Health 代理，然后重新安装。 执行此操作后，如果要再次开始监视同一服务器，请先在此服务器上卸载、重新安装并注册 Health 代理。

#### <a name="to-delete-a-service-instance-from-the-azure-ad-connect-health-service"></a>从 Azure AD Connect Health 服务中删除服务实例的步骤
1. 通过选择要删除的服务标识符（场名称），从“服务列表”边栏选项卡中打开“服务”边栏选项卡。 
2. 在操作栏中的“服务器”边栏选项卡上，单击“删除”。
3. 在确认框中键入服务名称（例如：sts.contoso.com）以进行确认。
4. 单击“删除” 。
   <br><br>

[//]: # (Start of RBAC section)
## <a name="manage-access-with-role-based-access-control"></a>使用基于角色的访问控制管理访问权限
用于 Azure AD Connect Health 的[基于角色的访问控制 (RBAC)](../role-based-access-control-configure.md) 可向全局管理员以外的用户和组提供访问权限。 RBAC 会将角色分配给目标用户和组，并提供一个机制将全局管理员的权限限制到目录以内。

### <a name="roles"></a>角色
Azure AD Connect Health 支持以下内置角色：

| 角色 | 权限 |
| --- | --- |
| 所有者 |所有者可以在 Azure AD Connect Health 中管理访问权限（例如将角色分配到用户或组）、从门户查看所有信息（例如查看警报信息），以及更改设置（例如设置电子邮件通知）。 <br>默认情况下，将为 Azure AD 全局管理员分配此角色，且此角色是无法更改的。 |
| 参与者 |参与者可以在 Azure AD Connect Health 中从门户中*查看所有信息*（例如查看警报信息）以及*更改设置*（例如设置电子邮件通知）。 |
| 读取器 |读取者可以在 Azure AD Connect Health 中从门户*查看所有信息*（例如查看警报）。 |

即使可在门户体验中使用其他所有角色（例如“用户访问管理员”或“DevTest 实验室用户”），这些角色也不会对 Azure AD Connect Health 中的访问权限产生影响。

### <a name="access-scope"></a>访问范围
Azure AD Connect Health 支持两个级别的访问管理：

* 所有服务实例：这是大多数情况下的建议路径。 它控制由 Azure AD Connect Health 监视的所有角色类型的所有服务实例（例如，AD FS 场）的访问权限。
* 服务实例：在某些情况下，可能需要根据角色类型或服务实例来区分访问。 在此情况下，你可以在服务实例级别管理访问。  

如果最终用户有权在目录或服务实例级别访问，则会被授予权限。

### <a name="allow-users-or-groups-access-to-azure-ad-connect-health"></a>允许用户或组访问 Azure AD Connect Health
以下步骤显示如何允许访问。
#### <a name="step-1-select-the-appropriate-access-scope"></a>步骤 1：选择适当的访问范围
若要允许用户访问 Azure AD Connect Health 中的*所有服务实例*级别，请在 Azure AD Connect Health 打开主边栏选项卡。<br>

#### <a name="step-2-add-users-and-groups-and-assign-roles"></a>步骤 2：添加用户、组并分配角色
1. 在“配置”部分中，单击“用户”。<br>
   ![Azure AD Connect Health RBAC 主边栏选项卡的屏幕快照（其中突出显示了“用户”）](./media/active-directory-aadconnect-health/RBAC_main_blade.png)
2. 选择“添加”。
3. 在“选择角色”窗格中，选择一个角色（例如“所有者”）。<br>
   ![Azure AD Connect Health RBAC“用户”窗口的屏幕快照](./media/active-directory-aadconnect-health/RBAC_add.png)
4. 键入目标用户或组的名称或标识符。 可以同时选择一个或多个用户或组。 单击“选择”。
   ![Azure AD Connect Health RBAC“用户”窗口的屏幕快照](./media/active-directory-aadconnect-health/RBAC_select_users.png)
5. 选择“确定”。<br>
6. 完成角色分配后，用户和组将显示在列表中。<br>
   ![Azure AD Connect Health RBAC 用户窗口的屏幕快照（其中突出显示了新用户）](./media/active-directory-aadconnect-health/RBAC_user_list.png)

现在，列出的用户和组具有基于所分配角色的相应访问权限。

> [!NOTE]
> * 全局管理员始终拥有所有操作的完全访问权限，但全局管理员帐户不会出现在上述列表中。
> * “邀请用户”功能在 Azure AD Connect Health 中不受支持。
>
>

#### <a name="step-3-share-the-blade-location-with-users-or-groups"></a>步骤 3：与用户或组共享边栏选项卡位置
1. 分配权限后，用户可以转到[此处](http://aka.ms/aadconnecthealth)，访问 Azure AD Connect Health。
2. 在边栏选项卡上，用户可将边栏选项卡或卡上的其他部件固定到仪表板。 只需单击“固定到仪表板”图标。<br>
   ![Azure AD Connect Health RBAC 固定边栏选项卡的屏幕快照，其中突出显示了固定图标](./media/active-directory-aadconnect-health/RBAC_pin_blade.png)

> [!NOTE]
> 分配有“读取者”角色的用户无法从 Azure 应用商店获取 Azure AD Connect Health 扩展。 这类用户无法执行实现上述操作所必需的“创建”操作。 但此用户仍可通过转到前一链接来访问边栏选项卡。 为方便后续使用，用户可以将边栏选项卡固定到仪表板。
>
>

### <a name="remove-users-or-groups"></a>删除用户或组
可删除已添加到 Azure AD Connect Health RBAC 中的用户或组。 只需右键单击用户或组，然后选择“删除”。<br>
![Azure AD Connect Health RBAC 用户窗口的屏幕快照，其中突出显示了“删除”](./media/active-directory-aadconnect-health/RBAC_remove.png)

[//]: # (End of RBAC section)

## <a name="next-steps"></a>后续步骤
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Azure AD Connect Health 代理安装](active-directory-aadconnect-health-agent-install.md)
* [在 AD FS 中使用 Azure AD Connect Health](active-directory-aadconnect-health-adfs.md)
* [使用用于同步的 Azure AD Connect Health](active-directory-aadconnect-health-sync.md)
* [在 AD DS 中使用 Azure AD Connect Health](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect Health 常见问题](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect Health 版本历史记录](active-directory-aadconnect-health-version-history.md)

