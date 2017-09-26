---
title: "基于 Azure Active Directory 组的许可的其他方案 | Microsoft 文档"
description: "有关基于 Azure Active Directory 组的许可的更多方案"
services: active-directory
keywords: "Azure AD 许可"
documentationcenter: 
author: curtand
manager: femila
editor: piotrci
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/02/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: 87cc66752dae1f4bd0903607d8a8ae9bd9125b11
ms.contentlocale: zh-cn
ms.lasthandoff: 09/13/2017

---

# <a name="scenarios-limitations-and-known-issues-using-groups-to-manage-licensing-in-azure-active-directory"></a>在 Azure Active Directory 中使用组管理许可的方案、限制和已知问题

使用以下信息和示例更深入地了解 Azure Active Directory (Azure AD) 基于组的许可。

## <a name="usage-location"></a>使用位置

某些 Microsoft 服务不能在所有位置使用。 在将许可证分配给用户之前，管理员必须为该用户指定“使用位置”属性。 在 [Azure 门户](https://portal.azure.com)中，你可以在“用户”&gt;“个人资料”&gt;“设置”中指定。

对于组许可证分配，任何没有指定使用位置的用户将继承该目录的位置。 如果用户位于多个位置，请在将用户添加到具有许可证的组之前，在用户对象中正确反映这种状态。

> [!NOTE]
> 组许可证分配永远不会修改用户的现有使用位置值。 建议你始终在 Azure AD 中的用户创建流程中设置使用位置（例如通过 AAD Connect 配置），这可确保许可证分配的结果始终正确，并且用户不会在不允许的位置收到服务。

## <a name="use-group-based-licensing-with-dynamic-groups"></a>对动态组使用基于组的许可

可对任何安全组使用基于组的许可，这意味着，可将它与 Azure AD 动态组组合使用。 动态组针对用户对象属性运行规则，在组中自动添加和删除用户。

例如，可以为要分配给用户的某些产品组创建动态组。 每个组都按照规则进行填充，根据用户属性添加用户，并且向每个组分配了你希望其接收的许可证。 可在本地分配属性并将它与 Azure AD 同步，或者直接在云中管理属性。

将用户添加到组片刻之后，即会将许可证分配给他们。 更改属性后，用户将离开组，许可证会被删除。

### <a name="example"></a>示例

假设以下示例：某个本地标识管理解决方案可以确定哪些用户应该有权访问 Microsoft Web 服务。 该解决方案使用 **extensionAttribute1** 来存储表示用户应该具有的许可证的字符串值。 Azure AD Connect 会将该值与 Azure AD 同步。

用户可能需要某一个许可证而不是另一个，或同时需要这两个许可证。 以下是一个示例：将 Office 365 企业版 E5 和企业移动性 + 安全性 (EMS) 许可证分发给组中的用户：

#### <a name="office-365-enterprise-e5-base-services"></a>Office 365 企业版 E5：基本服务

![Office 365 企业版 E5 基本服务的屏幕截图](media/active-directory-licensing-group-advanced/o365-e5-base-services.png)

#### <a name="enterprise-mobility--security-licensed-users"></a>企业移动性 + 安全性：许可的用户

![企业移动性 + 安全性许可的用户屏幕截图](media/active-directory-licensing-group-advanced/o365-e5-licensed-users.png)

如果希望用户具有两种许可证，则修改一个用户并将其 extensionAttribute1 值设置为 `EMS;E5_baseservices;`。 可在本地执行此项修改。 将更改与云同步后，该用户会自动添加到这两个组，然后获得了许可证。

![显示如何设置用户的 extensionAttribute1 的屏幕截图](media/active-directory-licensing-group-advanced/user-set-extensionAttribute1.png)

> [!WARNING]
> 修改现有组的成员身份规则时请小心。 更改规则时，将重新评估组成员资格，并删除不再符合新规则的用户（在此过程中，仍然匹配新规则的用户不会受到影响）。 在此过程中，将删除这些用户的许可证，从而导致服务丢失，或在某些情况下导致数据丢失。

> 如果拥有大型动态组，并且依赖于该组进行许可证分配，请先考虑对较小的测试组验证所有主要更改，然后再将更改应用到主要组。

## <a name="multiple-groups-and-multiple-licenses"></a>多个组和多个许可证

用户可能是具有许可证的多个组的成员。 下面是需要注意的事项：

- 相同产品的多个许可证可能重叠，从而导致向用户应用所有已启用的服务。 以下示例显示了两个许可组：*E3 基本服务*包含首先向用户部署的基础服务。 *E3 扩展服务*包含只向某些用户部署的其他服务（Sway 和 Planner）。 在本示例中，用户已添加到两个组：

  ![启用的服务的屏幕截图](media/active-directory-licensing-group-advanced/view-enabled-services.png)

  因此，该用户获得了已启用产品的 12 个服务中的 7 个，同时只需为此产品使用 1 个许可证。

- 选择 *E3* 许可证可显示更多详细信息，包括有关哪些组导致为用户启用了哪些服务的信息。

  ![组启用的服务的屏幕截图](media/active-directory-licensing-group-advanced/view-enabled-service-by-group.png)

## <a name="direct-licenses-coexist-with-group-licenses"></a>直接许可证与组许可证共存

如果用户从组继承许可证，则无法直接在用户属性中删除或修改该许可证分配。 更改必须在组中进行，然后传播给所有用户。

但是，除了继承的许可证以外，还可以将相同的产品许可证直接分配给用户。 可以只为一个用户启用产品中的其他服务，而不会影响到其他用户。

可以删除直接分配的许可证，不会影响继承的许可证。 假设用户从某个组继承了 Office 365 企业版 E3 许可证。

1. 最初，该用户只从 E3 基本服务组继承许可证，这将启用四项服务计划，如下所示：

  ![E3 组启用的服务的屏幕截图](media/active-directory-licensing-group-advanced/e3-group-enabled-services.png)

2. 选择“分配”可直接向该用户分配 E3 许可证。 在本例中，我们要禁用除 Yammer Enterprise 以外的所有服务计划：

  ![演示如何直接向用户分配许可证的屏幕截图](media/active-directory-licensing-group-advanced/assign-license-to-user.png)

3. 因此，该用户仍然只会使用 E3 产品的 1 个许可证。 但是，直接分配只会为该用户启用 Yammer Enterprise 服务。 可以看到组成员身份与直接分配分别会启用哪些服务：

  ![继承分配与直接分配的屏幕截图](media/active-directory-licensing-group-advanced/direct-vs-inherited-assignment.png)

4. 使用直接分配时，允许执行以下操作：

  - 可在用户对象中直接关闭 Yammer Enterprise。 插图中此服务的“开/关”切换器的启用方式与其他服务相反。 这是因为此服务是直接对用户启用的，因此可以修改。
  - 也可以将其他服务启用为直接分配的许可证的一部分。
  - 可以使用“删除”按钮删除用户的直接许可证。 可以看到，该用户现在只拥有继承的组许可证，并且只有原始服务保持启用状态：

    ![演示如何删除直接分配的屏幕截图](media/active-directory-licensing-group-advanced/remove-direct-license.png)

## <a name="managing-new-services-added-to-products"></a>管理添加到产品的新服务
当 Microsoft 向产品添加新服务时，相应服务将在分配了产品许可证的所有组中默认启用。 租户中订阅了有关产品更改通知的用户将提前收到电子邮件，通知他们即将推出的服务添加件。

作为管理员，你可以查看受更改影响的所有组并执行操作，例如在每个组中禁用新服务。 例如，如果已创建的组仅面向部署的特定服务，则可以重新访问这些组，并确保禁用任何新添加的服务。

此过程如以下示例所示：

1. 最初，将 Office 365 企业版 E5 产品分配给多个组。 其中一个名为 O365 E5 - Exchange only 的组旨在仅为其成员启用 Exchange Online（计划 2）服务。

2. Microsoft 向你发送通知，告知 E5 产品将使用 Microsoft Stream 这项新服务进行扩展。 当该服务在你的租户中可用时，可以执行以下操作：

3. 转到 [“Azure Active Directory”>“许可证”>“所有产品”](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products)边栏选项卡，选择“Office 365 企业版 E5”，然后选择“许可的组”以查看具有该产品的所有组的列表。

4. 单击要查看的组（此处为 O365 E5 - Exchange only）。 随后将打开“许可证”选项卡。单击 E5 许可证会打开一个列出所有已启用服务的边栏选项卡。
> [!NOTE]
> 除了 Exchange Online 服务外，Microsoft Stream 服务也已自动添加到此组中并已启用：

  ![添加到组许可证的新服务的屏幕截图](media/active-directory-licensing-group-advanced/manage-new-services.png)

5. 如果要禁用此组中的新服务，请单击服务旁边的“开/关”切换器，然后单击“保存”按钮确认更改。 Azure AD 现在将处理组中的所有用户以应用更改；添加到该组的任何新用户均不会启用 Microsoft Stream 服务。

  > [!NOTE]
  > 用户仍然可以通过一些其他许可证分配（用户作为成员的其他组或直接许可证分配）启用该服务。

6. 如有必要，请为分配了此产品的其他组执行相同的步骤。

## <a name="use-powershell-to-see-who-has-inherited-and-direct-licenses"></a>使用 PowerShell 查看谁拥有继承的许可证和直接许可证
可以使用 PowerShell 脚本来检查用户是具有直接分配的许可证还是具有从组继承的许可证。

1. 运行 `connect-msolservice` cmdlet 进行身份验证并连接到租户。

2. 可以使用 `Get-MsolAccountSku` 来发现该租户中所有已预配的产品许可证。

  ![Get-Msolaccountsku cmdlet 的屏幕截图](media/active-directory-licensing-group-advanced/get-msolaccountsku-cmdlet.png)

3. 通过[此 PowerShell 脚本](./active-directory-licensing-ps-examples.md#check-if-user-license-is-assigned-directly-or-inherited-from-a-group)将 AccountSkuId 值用于感兴趣的许可证。 这会生成具有此许可证的用户列表以及如何分配此许可证的信息。

## <a name="use-audit-logs-to-monitor-group-based-licensing-activity"></a>使用审核日志来监视基于组的许可活动

可以使用 [Azure AD 审核日志](./active-directory-reporting-activity-audit-logs.md#audit-logs)查看与基于组的许可相关的所有活动，其中包括：
- 更改组许可证的人员
- 系统开始处理组许可证更改的时间以及完成时间
- 基于组合许可证分配，对用户进行了哪些许可证更改。

>[!NOTE]
> 在门户的 Azure Active Directory 部分中大多数边栏选项卡上可以使用审计日志。 根据你访问筛选器的位置，可能会预应用筛选器，以仅显示与边栏选项卡的上下文相关的活动。 如果未看到所期望的结果，请检查[筛选选项](./active-directory-reporting-activity-audit-logs.md#filtering-audit-logs)，或访问[“Azure Active Directory”>“活动”>“审核日志”](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Audit)下未经筛选的审核日志。

### <a name="find-out-who-modified-a-group-license"></a>确定组许可证的修改者

1. 将“活动”筛选器设置为“设置组许可证”，然后单击“应用”。
2. 结果包括在组中设置或修改的许可证的所有情况。
>[!TIP]
> 你还可以在“目标”筛选器中键入组的名称，以确定结果范围。

3. 单击列表视图中的项以查看已更改内容的详细信息。 在“修改的属性”下，列出了许可证分配的旧值和新值。

下面是最新组许可证更改的详细信息示例：

![组许可证更改屏幕截图](media/active-directory-licensing-group-advanced/audit-group-license-change.png)

### <a name="find-out-when-group-changes-started-and-finished-processing"></a>了解组更改开始和完成处理的时间

如果组许可证发生更改，Azure AD 会开始将更改应用到所有用户。

1. 若要查看组何时开始处理，请将“活动”筛选器设置为“开始将基于组的许可证应用到用户”。 请注意，操作的执行组件是 Microsoft Azure AD 基于组的许可，这是用于执行所有组许可证更改的系统帐户。
>[!TIP]
> 单击列表中的项，查看“修改的属性”字段，它显示经过选择进行处理的许可证更改。 如果对组进行多次更改，并且不确定处理了哪次更改，则该字段很有用。

2. 同样，若要查看组完成处理的时间，可使用筛选器值“完成将基于组的许可证应用到用户”。
>[!TIP]
> 在这种情况下，“修改的属性”字段包含结果摘要，这对于快速检查处理是否会导致任何错误而言非常有用。 示例输出：
> ```
Modified Properties
...
Name : Result
Old Value : []
New Value : [Users successfully assigned licenses: 6, Users for whom license assignment failed: 0.];
> ```

3. 若要查看如何处理组的完整日志（包括所有用户更改），请设置以下筛选器：
  - **启动者（执行组件）**：“Microsoft Azure AD 基于组的许可”
  - **日期范围**（可选）：你知道特定组开始处理和完成处理的自定义时间范围

此示例输出显示处理开始时间、产生的所有用户更改以及处理完成时间。

![组许可证更改屏幕截图](media/active-directory-licensing-group-advanced/audit-group-processing-log.png)

>[!TIP]
> 单击与“更改用户许可证”相关的项可显示应用于单个用户的许可证更改的详细信息。

## <a name="limitations-and-known-issues"></a>限制和已知问题

如果使用基于组的许可，最好是熟悉以下限制和已知问题的列表。

- 基于组的许可目前不支持包含其他组的组（嵌套组）。 如果将许可证应用到某个嵌套组，只会向该组的直接一级用户成员应用许可证。

- 该功能仅可用于安全组。 目前不支持 Office 组，无法在许可证分配过程中使用它们。

- [Office 365 管理门户](https://portal.office.com )目前不支持基于组的许可。 如果用户从组继承许可证，此许可证会在 Office 管理门户中显示为普通的用户许可证。 如果尝试修改该许可证或尝试删除它，门户会返回错误消息。 无法直接修改用户的继承的组许可证。

- 如果从组中删除某个用户，导致其丢失许可证，则该许可证中的服务计划（例如 SharePoint Online）设置为“挂起”状态。 服务计划不会设置为最终已禁用状态。 这项预防措施可避免管理员在执行组成员身份管理期间由于失误而意外删除用户数据。

- 如果针对大型组（例如，100,000 个用户）分配或修改许可证，则可能会影响性能。 具体而言，Azure AD 自动化生成的大量更改可能会对 Azure AD 与本地系统之间的目录同步性能产生负面影响。

- 在某些高负载情况下，许可证处理可能会延迟，一些更改（例如，添加/删除组许可证或在组中添加/删除用户）可能需要很长时间才能处理。 如果发现处理更改所需时间超过 24 小时，请[开具支持票证](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/supportRequest)，以便让我们调查。 在此功能*正式发布*之前，我们将改进此功能的性能特性。

- 许可证管理自动化不会自动根据环境中的所有更改类型做出反应。 例如，可能会用完许可证，导致某些用户进入错误状态。 若要释放可用的许可座席计数，可以删除其他用户的某些直接分配的许可证。 但是，系统不会自动对此项更改做出反应，也不会修复处于该错误状态的用户。

  此类限制的解决方法之一是转到 Azure AD 中的“组”边栏选项卡，并单击“重新处理”。 在可能的情况下，此命令会处理该组中的所有用户并修复错误状态。

- 由于 Exchange Online 中包含重复的代理地址配置，导致无法将许可证分配给用户时，基于组的许可不会记录错误；在许可证分配期间会跳过这些用户。 有关如何识别和解决此问题的详细信息，请参阅[本节](./active-directory-licensing-group-problem-resolution-azure-portal.md#license-assignment-fails-silently-for-a-user-due-to-duplicate-proxy-addresses-in-exchange-online)。

## <a name="next-steps"></a>后续步骤

若要详细了解通过基于组的许可进行许可证管理的其他方案，请参阅：

* [Azure Active Directory 中基于组的许可是什么？](active-directory-licensing-whatis-azure-portal.md)
* [将许可证分配到 Azure Active Directory 中的组](active-directory-licensing-group-assignment-azure-portal.md)
* [识别和解决 Azure Active Directory 中组的许可问题](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [如何将单个许可用户迁移到 Azure Active Directory 中基于组的许可](active-directory-licensing-group-migration-azure-portal.md)

