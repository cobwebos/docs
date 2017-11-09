---
title: "Azure IoT 套件和 Azure Active Directory | Microsoft Docs"
description: "介绍 Azure IoT 套件如何使用 Azure Active Directory 管理权限。"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 246228ba-954a-4d96-b6d6-e53e4590cb4f
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 4c4916037ad762dbb9dee803dfd45ec2fd89272b
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2017
---
# <a name="permissions-on-the-azureiotsuitecom-site"></a>azureiotsuite.com 站点权限

## <a name="what-happens-when-you-sign-in"></a>登录时发生的情况

在 [azureiotsuite.com][lnk-azureiotsuite] 上首次登录时，站点会基于当前所选 Azure Active Directory (AAD) 租户和 Azure 订阅来确定你拥有的权限级别。

1. 站点首先从 Azure 查明用户所属的 AAD 租户以填充用户名旁显示的租户列表。 当前，站点一次只能获取一个租户的用户令牌。 因此，当使用右上角的下拉列表切换租户时，站点会使你登录到该租户，以获取该租户的令牌。

2. 接下来，站点从 Azure 查明你已与所选租户关联的订阅。 创建新的预配置解决方案时，会看到可用订阅。

3. 最后，站点检索标记为预配置解决方案的订阅和资源组中的所有资源，并填充主页上的磁贴。

下列各部分介绍用于控制对预配置解决方案的访问的角色。

## <a name="aad-roles"></a>AAD 角色

AAD 角色可控制设置预配置解决方案以及在预配置解决方案中管理用户的能力。

有关 AAD 中的管理员角色的详细信息，可参阅[在 Azure AD 中分配管理员角色][lnk-aad-admin]。 本文重点介绍预配置解决方案使用的**全局管理员**和**用户**目录角色。

### <a name="global-administrator"></a>全局管理员

对于每个 AAD 租户，可以有多个全局管理员：

* 在创建某个 AAD 租户时，默认情况下会成为该租户的全局管理员。
* 全局管理员可以预配预配置解决方案，并被分配了其 AAD 租户中应用程序的**管理员**角色。
* 如果同一 AAD 租户中的其他用户创建了一个应用程序，则授予全局管理员的默认角色是“只读”。
* 全局管理员可以使用 [Azure 门户][lnk-portal]为用户分配应用程序角色。

### <a name="domain-user"></a>域用户

每个 AAD 租户可以有多个域用户：

* 域用户可以通过 [azureiotsuite.com][lnk-azureiotsuite] 站点设置预配置解决方案。 默认情况下在预配应用程序中，向域用户授予“管理员”角色。
* 域用户可使用 [azure-iot-remote-monitoring][lnk-rm-github-repo]、[azure-iot-predictive-maintenance][lnk-pm-github-repo] 或 [azure-iot-connected-factory][lnk-cf-github-repo] 存储库中的 build.cmd 脚本创建应用程序。 但是，向域用户授予的默认角色为“只读”，这是因为域用户没有分配角色的权限。
* 如果 AAD 租户中的其他用户创建了一个应用程序，则默认情况下，授予域用户针对该应用程序的“只读”角色。
* 域用户无法为应用程序分配角色；因此即使是其自己设置的应用程序，也无法为应用程序添加用户或用户角色。

### <a name="guest-user"></a>来宾用户

每个 AAD 租户可以有多个来宾用户。 来宾用户在 AAD 租户中拥有有限的权利集。 因此，来宾用户无法在 AAD 租户中设置预配置解决方案。

有关 AAD 中用户及角色的详细信息，请参阅以下资源：

* [在 Azure AD 中创建用户][lnk-create-edit-users]
* [将用户分配到应用][lnk-assign-app-roles]

## <a name="azure-subscription-administrator-roles"></a>Azure 订阅管理员角色

Azure 管理员角色可控制将 Azure 订阅映射到 AD 租户的能力。

可以在[如何添加或更改 Azure 协同管理员、服务管理员和帐户管理员][lnk-admin-roles]一文中找到有关 Azure 管理员角色的详细信息。

## <a name="application-roles"></a>应用程序角色

应用程序角色可在预配置解决方案中控制对设备的访问。

预配应用程序中定义有两个定义的角色和一个隐式角色：

* **管理员：**具有添加、管理、删除设备和修改设置的完全控制权限。
* **只读：**可以查看设备、规则、操作、作业和遥测数据。

可以在 [RolePermissions.cs][lnk-resource-cs] 源文件中找到分配给每个角色的权限。

### <a name="changing-application-roles-for-a-user"></a>更改用户的应用程序角色

可以使用下面的过程在 Active Directory 中使用户成为预配置解决方案的管理员。

必须是 AAD 全局管理员才能更改用户的角色：

1. 转到 [Azure 门户][lnk-portal]。
2. 选择“Azure Active Directory”。
3. 请确保使用的是预配解决方案时在 azureiotsuite.com 上选择的目录。 如果有多个目录与订阅关联，单击门户右上角的帐户名称，可以在这些目录之间进行切换。
4. 依次单击“企业应用程序”、“所有应用程序”。
4. 显示具有**任何**状态的**所有应用程序**。 然后搜索具有预配置解决方案名称的应用程序。
5. 单击与预配置解决方案名称匹配的应用程序名称。
6. 单击“用户和组”。
7. 选择要切换角色的用户。
8. 单击“分配”并选择要分配给用户的角色（如“管理员”），单击复选标记。

## <a name="faq"></a>常见问题

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-aad-tenant-how-do-i-complete-this-task"></a>我是服务管理员，要更改我的订阅与特定 AAD 租户之间的目录映射。 如何完成此任务？

1. 转到 [Azure 经典门户][lnk-classic-portal]，在左侧的服务列表中单击“设置”。
2. 选择要将目录映射更改为的订阅。
3. 单击“编辑目录”。
4. 在下拉列表中选择要使用的“目录”。 单击向前箭头。
5. 确认目录映射和受影响的协同管理员。 如果要从另一个目录移动，则会删除原始目录中的所有协同管理员。

### <a name="im-a-domain-usermember-on-the-aad-tenant-and-ive-created-a-preconfigured-solution-how-do-i-get-assigned-a-role-for-my-application"></a>我是 AAD 租户上的域用户/成员，我创建了一个预配置解决方案。 如何针对我的应用程序向我分配角色？

请求全局管理员将你设为 AAD 租户上的全局管理员，然后自己为用户分配角色。 或请求全局管理员直接为你分配一个角色。 如果要更改预配置解决方案部署到的 AAD 租户，请参阅下一个问题。

### <a name="how-do-i-switch-the-aad-tenant-my-remote-monitoring-preconfigured-solution-and-application-are-assigned-to"></a>如何切换将我的远程监视预配置解决方案和应用程序分配到的 AAD 租户？

可以从 <https://github.com/Azure/azure-iot-remote-monitoring> 中运行云部署，并使用新创建的 AAD 租户重新部署。 默认情况下，你在创建 AAD 租户时会成为全局管理员，因此拥有添加用户以及向这些用户分配角色的权限。

1. 在 [Azure 门户][lnk-portal]中创建 AAD 目录。
2. 转到 <https://github.com/Azure/azure-iot-remote-monitoring>。
3. 运行 `build.cmd cloud [debug | release] {name of previously deployed remote monitoring solution}`（例如 `build.cmd cloud debug myRMSolution`）
4. 出现提示时，将 **tenantid** 设置为新创建的租户，而不是以前的租户。

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organisational-account"></a>我在使用组织帐户登录时要更改服务管理员或协同管理员

请参阅支持文章[使用组织帐户登录时更改服务管理员和协同管理员][lnk-service-admins]。

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>为何会出现以下错误？ “帐户没有创建解决方案的正确权限。 请咨询帐户管理员或使用其他帐户进行尝试。”

请查看以下指南示意图：

![][img-flowchart]

> [!NOTE]
> 如果在验证你是 AAD 租户的全局管理员和订阅的共同管理员后，仍看到此错误，请让帐户管理员删除该用户，并按以下顺序重新分配必要的权限。 首先，将用户添加为全局管理员，然后将用户添加为 Azure 订阅的协同管理员。 如果问题仍然存在，请联系[帮助和支持][lnk-help-support]。

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>为何在我具有 Azure 订阅时会出现以下错误？ “创建预配置解决方案需要 Azure 订阅。 只需几分钟即可创建一个免费试用帐户。”

如果确定具有 Azure 订阅，请验证订阅的租户映射，并确保在下拉列表中选择正确租户。 如果验证了所需租户是正确的，请按照上图，验证订阅和此 AAD 租户的映射。

## <a name="next-steps"></a>后续步骤
若要继续了解 IoT 套件，请参阅如何[自定义预配置解决方案][lnk-customize]。

[img-flowchart]: media/iot-suite-v1-permissions/flowchart.png

[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-rm-github-repo]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-pm-github-repo]: https://github.com/Azure/azure-iot-predictive-maintenance
[lnk-cf-github-repo]: https://github.com/Azure/azure-iot-connected-factory
[lnk-aad-admin]: ../active-directory/active-directory-assign-admin-roles.md
[lnk-classic-portal]: https://manage.windowsazure.com/
[lnk-portal]: https://portal.azure.com/
[lnk-create-edit-users]: ../active-directory/active-directory-create-users.md
[lnk-assign-app-roles]: ../active-directory/active-directory-coreapps-assign-user-azure-portal.md
[lnk-service-admins]: https://azure.microsoft.com/support/changing-service-admin-and-co-admin/
[lnk-admin-roles]: ../billing/billing-add-change-azure-subscription-administrator.md
[lnk-resource-cs]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs
[lnk-help-support]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[lnk-customize]: iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md
