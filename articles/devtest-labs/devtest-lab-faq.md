---
title: Azure 开发测试实验室常见问题解答 | Microsoft Docs
description: 本文提供有关 Azure 开发测试实验室的部分常见问题解答 (FAQ)。
ms.topic: article
ms.date: 07/17/2020
ms.openlocfilehash: 3f36b8238ccb69b3b1f14166b522e47d5debe54e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87289374"
---
# <a name="azure-devtest-labs-faq"></a>Azure 开发测试实验室常见问题
获取关于 Azure 开发测试实验室的某些最常见问题的解答。

## <a name="blog-post"></a>博客文章
我们的开发测试实验室团队博客已于 2019 年 3 月 20 日停用。 

### <a name="where-can-i-track-feature-updates-from-now-on"></a>从现在起，在哪里可以跟踪功能更新？
从现在起，我们将在 Azure 博客和 Azure 更新上发布功能更新和信息性博客文章。 这些博客文章还将在需要时链接到我们的文档。

订阅[开发测试实验室 Azure 博客](https://azure.microsoft.com/blog/tag/azure-devtest-labs/)和[开发测试实验室 Azure 更新](https://azure.microsoft.com/updates/?product=devtest-lab)随时了解开发测试实验室中的新功能。

### <a name="what-happens-to-the-existing-blog-posts"></a>现有的博客文章会发生什么情况？
我们目前正在努力将现有博客文章（不包括中断更新）迁移到[开发测试实验室文档](devtest-lab-overview.md)。 弃用 MSDN 博客时，它将重定向到开发测试实验室的文档概述。 重定向后，你可以在“筛选条件”标题中搜索要查找的文章。 我们尚未迁移所有帖子，但应在本月底完成。 


### <a name="where-do-i-see-outage-updates"></a>我在哪里可以看到中断更新？
从现在开始，我们将使用 Twitter 句柄发布中断更新。 在 Twitter 上关注我们，获取有关中断和已知 Bug 的最新更新。

### <a name="twitter"></a>Twitter
我们的 Twitter 句柄：[@azlabservices](https://twitter.com/azlabservices)

## <a name="general"></a>常规
### <a name="what-if-my-question-isnt-answered-here"></a>如果未在此处找到相关问题怎么办？
如果此处未列出问题，请联系我们寻求答案。

- 在本常见问题解答的末尾发布问题。
- 若希望更多人看到问题，可以将问题发布在[针对 Azure 开发测试实验室的 Microsoft 问答页](/answers/topics/azure-devtestlabs.html)上。 与 Azure 开发测试实验室团队和其他社区成员进行讨论。
- 要提出功能请求，请将请求和想法提交到 [Azure DevTest Labs User Voice](https://feedback.azure.com/forums/320373-azure-devtest-labs)（Azure 开发测试实验室用户之声）。

### <a name="what-is-a-microsoft-account"></a>什么是 Microsoft 帐户？
Microsoft 帐户可用于通过 Microsoft 设备和服务执行的几乎所有操作。 它是用于登录 Skype、Outlook.com、OneDrive、Windows Phone、Azure 和 Xbox Live 的电子邮件地址和密码。 单一帐户意味着文件、照片、联系人和设置可跟随用户移动到任何设备。

> [!NOTE]
> Microsoft 帐户以前称为“Windows Live ID”。

### <a name="why-should-i-use-azure-devtest-labs"></a>为什么要使用 Azure 开发测试实验室？
Azure 开发测试实验室可为团队节省时间和金钱。 开发人员可使用几个不同的基项创建自己的环境。 还可使用项目快速部署和配置应用程序。 使用自定义映像和公式，可将虚拟机 (VM) 保存为模板，并在团队中轻松重现。 开发测试实验室还提供多个可配置策略，可供实验室管理员用于减少浪费和管理团队的环境。 这些策略包括自动关机、成本阈值、每个用户的最大 VM 数量和最大 VM 大小。 有关开发测试实验室更深入的说明，请参阅[概述](devtest-lab-overview.md)或观看[介绍视频](https://channel9.msdn.com/Blogs/Azure/what-is-azure-devtest-labs)。

### <a name="what-does-worry-free-self-service-mean"></a>“无忧的自助服务”是什么意思？
“无忧的自助服务”是指开发人员和测试人员按需创建自己的环境。 管理员知道开发测试实验室可帮助设置适当的访问权限、尽量减少浪费和控制成本，因而感到安心无忧。 管理员可以指定允许的 VM 大小、最大 VM 数量以及 VM 的启动和关机时间。 开发测试实验室还可简化成本监视和警报设置，可帮助用户始终注意实验室资源的使用方式。

### <a name="how-can-i-use-devtest-labs"></a>如何使用开发测试实验室？
在以下任何情况下，开发测试实验室都非常有用：需要开发或测试环境，且希望快速重现该环境，或使用节省成本的策略管理该环境。
下面列出了客户使用开发测试实验室的某些情形：

- 在一个位置管理开发和测试环境。 使用策略来降低成本和创建自定义映像，以便在团队中共享生成。
- 使用自定义映像开发应用程序，以便保存整个开发阶段的磁盘状态。
- 跟踪与进度相关的成本。
- 为质量保证测试创建大容量测试环境。
- 使用项目和公式轻松在各种环境中配置和重现应用程序。
- 为编程马拉松分发 VM（协作开发或测试工作），并在事件结束时轻松对其取消预配。

### <a name="how-am-i-billed-for-devtest-labs"></a>开发测试实验室如何计费？
开发测试实验室是一项免费服务。 可免费在开发测试实验室中创建实验室，配置策略、模板和项目。 只需为在实验室中使用的 Azure 资源（例如 VM、存储帐户和虚拟网络）付费。 有关实验室资源费用的详细信息，请参阅 [Azure 开发测试实验室定价](https://azure.microsoft.com/pricing/details/devtest-lab/)。

## <a name="security"></a>安全性

### <a name="what-are-the-different-security-levels-in-devtest-labs"></a>开发测试实验室中有哪些安全级别？
安全访问权限由基于角色的访问控制 (RBAC) 决定。 要了解访问权限的工作方式，最好先了解 RBAC 定义的权限、角色与作用域之间的差异。

- **权限**：权限是针对特定操作定义的访问权限。 例如，权限可以是对所有 VM 的读取访问权限。
- **角色**：角色是可进行分组和分配给用户的一组权限。 例如，具有“订阅所有者”角色的用户对订阅中所有资源都具有访问权限。
- **范围**：作用域是 Azure 资源的层次结构中的一个级别。 例如，作用域可以是资源组、单个实验室或整个订阅。

开发测试实验室的作用域中有两种定义用户权限的角色：

- **实验室所有者**：实验室所有者对实验室中的所有资源都具有访问权限。 实验室所有者可进行修改策略、读取和写入任意 VM、更改虚拟网络等操作。
- **实验室用户**：实验室用户可查看所有实验室资源（例如 VM、策略和虚拟网络）。 但是，实验室用户不能修改其他用户创建的策略和 VM。

还可以在开发测试实验室中创建自定义角色。 要了解如何在开发测试实验室中创建自定义角色，请参阅[向用户授予特定实验室策略的权限](devtest-lab-grant-user-permissions-to-specific-lab-policies.md)。

由于作用域是分层的，因此在用户具有某个作用域的权限时，系统会自动向该用户授予该作用域内每个较低级别的作用域的权限。 例如，如果向用户分配了订阅所有者角色，那么用户可访问订阅中的所有资源。 这些资源包括 VM、虚拟网络和实验室。 订阅所有者会自动继承实验室所有者的角色。 但是反过来则不适用。 实验室所有者具有访问实验室的权限，其低于订阅级别的作用域。 因此，实验室所有者不能查看 VM、虚拟网络或实验室之外的其他任何资源。

### <a name="how-do-i-define-role-based-access-control-for-my-devtest-labs-environments-to-ensure-that-it-can-govern-while-developerstest-can-do-their-work"></a>如何为我的开发测试实验室环境定义基于角色的访问控制才能确保在开发人员/测试人员完成其工作时由 IT 部门进行治理？
可以使用一种广泛的模式，但具体细节取决于你的组织。

中心 IT 部门只应拥有必需的控制，让项目和应用程序团队拥有所需级别的控制。 通常情况下，这意味着中心 IT 部门拥有订阅并负责核心 IT 功能，例如网络配置。 订阅的**所有者**集应该小。 这些所有者可以在需要时指定其他所有者，或者应用订阅级别的策略，例如“禁止公共 IP”。

部分用户（例如第 1 层或第 2 层支持人员）可能需要在整个订阅中进行访问。 在这种情况下，建议为这些用户提供管理资源所需的“参与者”访问权限，但不提供用户访问权限，也不调整策略。

开发测试实验室资源应该由那些接近项目/应用程序团队的所有者拥有。 这是因为他们了解团队对计算机和必需软件的要求。 在大多数组织中，此开发测试实验室资源的所有者通常是项目/开发主管。 此所有者可以管理实验室环境中的用户和策略，可以管理开发测试实验室环境中的所有 VM。

应该将项目/应用程序团队成员添加到“开发测试实验室用户”角色中。 这些用户可以创建虚拟机（前提是遵循实验室和订阅级别的策略）。 他们也可以管理自己的虚拟机。 他们不能管理属于其他用户的虚拟机。

有关详细信息，请参阅 [Azure 企业基架 - 规范性订阅治理文档](/azure/architecture/cloud-adoption/appendix/azure-scaffold)。


### <a name="how-do-i-create-a-role-to-allow-users-to-do-a-specific-task"></a>如何通过创建角色来允许用户执行特定任务？
有关如何创建自定义角色，并将权限分配给该角色的综合性文章，请参阅[向用户授予特定实验室策略的权限](devtest-lab-grant-user-permissions-to-specific-lab-policies.md)。 下面的脚本示例创建“开发测试实验室高级用户”角色，该角色具有启动和停止实验室中所有 VM 的权限：


```powershell
$policyRoleDef = Get-AzRoleDefinition "DevTest Labs User"
$policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*')
$policyRoleDef.Id = $null
$policyRoleDef.Name = "DevTest Labs Advanced User"
$policyRoleDef.IsCustom = $true
$policyRoleDef.AssignableScopes.Clear()
$policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>")
$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action")
$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action")
$policyRoleDef = New-AzRoleDefinition -Role $policyRoleDef  
```

### <a name="how-can-an-organization-ensure-corporate-security-policies-are-in-place"></a>组织如何确保企业安全策略部署到位？
组织可通过以下措施实现此目的：

- 制定并发布全面的安全策略。 该策略阐述了软件和云资产的可接受用法相关规则。 它还规定了哪些行为肯定会违反策略。
- 开发自定义映像、自定义项目和部署过程，以便在使用 Active Directory 定义的安全领域内进行协调。 此方法会强制实施企业边界，并规定一套通用的环境控制措施。 开发人员在整个过程中开发和遵循安全开发生命周期时，可以考虑这些针对环境的控制措施。 目标也是使提供的环境不会过度施加限制，从而对开发带来阻碍，而是实施一套合理的控制措施。 包含实验室虚拟机的组织单位 (OU) 的组策略可以是生产环境中的所有组策略的子集。 或者，它们可以是一个用于适当缓解任何所识别到的风险的补充集。

### <a name="how-can-an-organization-ensure-data-integrity-to-ensure-that-remoting-developers-cant-remove-code-or-introduce-malware-or-unapproved-software"></a>组织如何确保数据完整性，以防止远程开发人员删除代码或者引入恶意软件或未经批准的软件？
可通过多个控制层来缓解在开发测试实验室中进行协作的外部远程顾问、合同工或员工带来的威胁。

如前所述，第一步是必须草拟并制定可接受的使用策略，其中明确阐述了违反策略所造成的后果。

第一个远程访问控制层是通过不与实验室直接连接的 VPN 来应用远程访问策略。

第二个控制层是应用组策略对象集，以防止通过远程桌面进行复制粘贴。 可以实施一个网络策略来禁止环境中的出站服务，例如环境外部的 FTP 和 RDP 服务。 用户定义的路由可以强制将所有 Azure 网络流量发回到本地，但除非通过可以扫描内容和会话的代理进行控制，否则该路由无法考虑到可能允许上传数据的所有 URL。 在支持开发测试实验室的虚拟网络中，公共 IP 可能限制为禁止桥接外部网络资源。

最终，需要在整个组织中应用相同类型的限制，这将考虑到可移动媒体或者可接受发布内容的外部 URL 使用的所有可能方法。 评审和实施安全策略时，请咨询安全专家。 有关更多建议，请参阅 [Microsoft 网络安全性](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs)。

## <a name="lab-configuration"></a>实验室配置

### <a name="how-do-i-create-a-lab-from-a-resource-manager-template"></a>如何从资源管理器模板模板创建实验室？
我们提供了[实验室 Azure 资源管理器模板的 GitHub 存储库](https://azure.microsoft.com/resources/templates/101-dtl-create-lab)，可按原样部署，也可对其进行修改，为实验室创建自定义模板。 每个模板都有一个链接，用于在自己的 Azure 订阅中按原样部署实验室。 或者，可自定义模板，并[使用 PowerShell 或 Azure CLI 进行部署](../azure-resource-manager/templates/deploy-powershell.md)。


### <a name="can-i-have-all-virtual-machines-to-be-created-in-a-common-resource-group-instead-having-each-machine-in-its-own-resource-group"></a>是否可以将所有虚拟机创建在公共资源组中，而不是在其专属资源组中创建每台计算机？
可以，作为实验室所有者，你可以让实验室为你分配资源组，也可以在指定的公共资源组中创建所有虚拟机。

单独的资源组方案：
-   开发测试实验室为你创建的每个公共/专用 IP 虚拟机新建资源组
-   开发测试实验室为相同大小的共享 IP 计算机创建资源组。

常见资源组方案：
-   所有虚拟机都创建在指定的公共资源组中。 详细了解[实验室的资源组分配](https://aka.ms/RGControl)。

### <a name="how-do-i-maintain-a-naming-convention-across-my-devtest-labs-environment"></a>如何在整个开发测试实验室环境中保留命名约定？
你可能需要将当前企业命名约定扩展到 Azure 运营，并在整个开发测试实验室环境中保持这种命名约定的一致性。 部署开发测试实验室时，我们建议使用特定的起始策略。 通过中心脚本和 JSON 模板部署这些策略，以实施一致性。 可以通过在订阅级别应用的 Azure 策略来实施命名策略。 有关 Azure Policy 的 JSON 示例，请参阅 [Azure Policy 示例](../governance/policy/samples/index.md)。

### <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>在同一订阅下，可创建多少个实验室？
没有对每个订阅可创建的实验室数量进行限定。 但对每个订阅使用的资源量进行了限定。 可参阅 [Azure 订阅的限制和配额](../azure-resource-manager/management/azure-subscription-service-limits.md)和[如何增加这些限制](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests)。


### <a name="how-many-vms-can-i-create-per-lab"></a>每个实验室可以创建多少个 VM？
没有对每个实验室可创建的 VM 数量进行限定。 但对每个订阅使用的资源（VM 核心数、公共 IP 地址等）进行了限制。 可参阅 [Azure 订阅的限制和配额](../azure-resource-manager/management/azure-subscription-service-limits.md)和[如何增加这些限制](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests)。

### <a name="how-do-i-determine-the-ratio-of-users-per-lab-and-the-overall-number-of-labs-that-are-needed-across-an-organization"></a>如何确定每个实验室的用户数与整个组织中所需实验室总数的比？
我们建议将同一开发项目关联的业务单位和开发组关联到同一实验室。 这样，便可以对这两个组应用相同类型的策略、映像和关闭策略。

可能还需要考虑地理边界。 例如，美国 (US) 东北部的开发人员可能会使用美国东部 2 区域中预配的实验室。 另外，位于德克萨斯州达拉斯市和科罗拉多州丹佛市的开发人员可能会定向为使用美国中南部的资源。 如果能够与外部第三方展开协作，则可为这些开发人员分配未由内部开发人员使用的实验室。

还可以将实验室用于 Azure DevOps Projects 中的特定项目。 然后，通过允许访问这两组资源的指定 Azure Active Directory 组应用安全性。 分配到该实验室的虚拟网络可以是用于整合用户的另一个边界。

### <a name="how-can-we-prevent-the-deletion-of-resources-within-a-lab"></a>如何防止删除实验室中的资源？
我们建议在实验室级别设置适当的权限，以便只有经过授权的用户才能删除资源或更改实验室策略。 应将开发人员放置在“开发测试实验室用户”组中。 开发人员主管或基础结构主管应是“开发测试实验室所有者”。 我们建议仅指定两个实验室所有者。 此策略根据代码存储库的扩展而扩展，以避免损坏。 实验室用户有权使用资源，但不能更新实验室策略。 请参阅以下文章，其中列出了每个内置组在实验室中拥有的角色和权限：[在 Azure 开发测试实验室中添加所有者和用户](devtest-lab-add-devtest-user.md)。

### <a name="how-do-i-share-a-direct-link-to-my-lab"></a>如何向我的实验室共享直接链接？

1. 在 [Azure 门户](https://portal.azure.com)中，转到实验室。
2. 从浏览器复制“实验室 URL”并与实验室用户共享。

> [!NOTE]
> 如果实验室用户是具有 Microsoft 帐户，但没有贵组织的 Active Directory 实例成员身份的外部用户，则该用户尝试访问共享链接时可能会看到错误消息。 如果外部用户看到错误消息，请让该用户首先在 Azure 门户的右上角选择其名字。 然后，在菜单的目录部分，该用户可选择实验室所在的目录。

## <a name="virtual-machines"></a>虚拟机

### <a name="why-cant-i-see-vms-on-the-virtual-machines-page-that-i-see-in-devtest-labs"></a>为什么我无法在“虚拟机”页上看到可在开发测试实验室中看到的 VM？
在开发测试实验室中创建 VM 时，用户具有访问该 VM 的权限。 可在“实验室”页上和“虚拟机”页上查看该 VM。 分配到“开发测试实验室所有者”角色的用户可查看在实验室中通过实验室的“所有虚拟机”页创建的所有 VM 。 但是，系统不会自动向具有“开发测试实验室用户”角色的用户授予对其他用户创建的 VM 资源的读取访问权限。 因此，这些 VM 不会显示在“虚拟机”页上。


### <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>如何一次性从同一模板创建多个 VM？
可通过两种方式同时从同一模板创建多个 VM：

- 可以使用 [Azure DevOps 任务扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)。
- 创建 VM 时，可以[生成资源管理器模板](devtest-lab-add-vm.md#save-azure-resource-manager-template)和[从 Windows PowerShell 部署资源管理器模板](../azure-resource-manager/templates/deploy-powershell.md)。
- 你还可以指定在虚拟机创建期间创建多个计算机实例。 若要了解有关创建多个虚拟机实例的详细信息，请参阅有关[创建实验室虚拟机](devtest-lab-add-vm.md)的文档。

### <a name="how-do-i-move-my-existing-azure-vms-into-my-devtest-labs-lab"></a>如何将现有 Azure VM 移到开发测试实验室？
将现有 VM 复制到开发测试实验室：

1.  使用此 [Windows PowerShell 脚本](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/Scripts/CopyVirtualMachines/CopyAzVHDFromVMToLab.ps1)复制现有 VM 的 VHD 文件。
2.  在开发测试实验室内创建[自定义映像](devtest-lab-create-template.md)。
3.  在实验室中从自定义映像创建 VM。

### <a name="can-i-attach-multiple-disks-to-my-vms"></a>可将多个磁盘附加到我的 VM 中吗？

可以，可将多个磁盘附加到 VM 中。

### <a name="are-gen-2-images-supported-by-devtest-labs"></a>开发测试实验室支持第 2 代映像吗？
是。 开发测试实验室服务支持 [第2代映像](../virtual-machines/windows/generation-2.md)。 但是，如果第1代和第2代版本都可用于映像，则在创建 VM 时，开发测试 Labs 只显示映像的第1代版本。 如果该映像只有第2代可用版本，则会看到该映像。 

### <a name="if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription"></a>如果要将 Windows OS 映像用于我的测试，是否需要购买 MSDN 订阅？
要在 Azure 中使用 Windows 客户端 OS 映像（Windows 7 或更高版本）进行开发或测试，请执行以下步骤之一：

- [购买 MSDN 订阅](https://www.visualstudio.com/products/how-to-buy-vs)。
- 如果具有企业协议，请通过 [Enterprise 开发/测试套餐](https://azure.microsoft.com/offers/ms-azr-0148p)创建 Azure 订阅。

若要深入了解每个 MSDN 套餐的 Azure 信用额度，请参阅 [Visual Studio 订户的每月 Azure 信用额度](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)。


### <a name="how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>如何自动删除实验室中的所有 VM？
作为实验室所有者，可在 Azure 门户中从实验室删除 VM。 还可以使用 PowerShell 脚本删除实验室中的所有 VM。 在下例中，修改“要更改的值”注释下方的参数值。 可以从 Azure 门户的“实验室”窗格中检索 `subscriptionId`、`labResourceGroup` 和 `labName` 值。

```powershell
# Delete all the VMs in a lab.

# Values to change:
$subscriptionId = "<Enter Azure subscription ID here>"
$labResourceGroup = "<Enter lab's resource group here>"
$labName = "<Enter lab name here>"

# Sign in to your Azure account.
Connect-AzAccount

# Select the Azure subscription that has the lab. This step is optional
# if you have only one subscription.
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab that has the VMs that you want to delete.
$lab = Get-AzResource -ResourceId ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)

# Get the VMs from that lab.
$labVMs = Get-AzResource | Where-Object {
          $_.ResourceType -eq 'microsoft.devtestlab/labs/virtualmachines' -and
          $_.Name -like "$($lab.Name)/*"}

# Delete the VMs.
foreach($labVM in $labVMs)
{
    Remove-AzResource -ResourceId $labVM.ResourceId -Force
}
```

## <a name="environments"></a>环境

### <a name="how-can-i-use-resource-manager-templates-in-my-devtest-labs-environment"></a>如何在开发测试实验室环境中使用资源管理器模板？
可以按照[开发测试实验室中的环境功能](devtest-lab-test-env.md)一文中所述的步骤，将资源管理器模板部署到开发测试实验室环境中。 从根本上说，将资源管理器模板签入 Git 存储库（Azure Repos 或 GitHub），并将[模板的专用存储库](devtest-lab-test-env.md)添加到实验室。 如果使用开发测试实验室托管开发计算机，则此方案可能没有用；但如果要构建具有生产代表性的过渡环境，则此方案可能很有用。

还值得注意的是，每个实验室或每个用户的虚拟机数量选项仅限制在实验室自身中本机创建的计算机数量，而不是由任何环境（资源管理器模板）创建的计算机数量。

## <a name="custom-images"></a>自定义映像

### <a name="how-can-i-set-up-an-easily-repeatable-process-to-bring-my-custom-organizational-images-into-a-devtest-labs-environment"></a>如何设置一个易于重复的过程以便将我的自定义组织映像导入开发测试实验室环境？
请观看[这一有关映像工厂模式的视频](https://sec.ch9.ms/ch9/8e8a/9ea0b8d4-b803-4f23-bca4-4808d9368e8a/dtlimagefactory_mid.mp4)。 此方案是一个高级方案，提供的脚本都只是示例脚本。 如果需要进行任何更改，则需要管理和维护在环境中使用的脚本。

有关创建映像工厂的详细信息，请参阅[在 Azure 开发测试实验室中创建自定义映像工厂](image-factory-create.md)。

### <a name="what-is-the-difference-between-a-custom-image-and-a-formula"></a>自定义映像与公式之间的区别是什么？
自定义映像是托管映像。 而公式是可使用其他设置配置，并在随后保存和重现的映像。 要使用同一不可变的基本映像快速创建多个环境，最好使用自定义映像。 要使用最新位（作为虚拟网络或子网的一部分，或作为特定大小的 VM）重现 VM 的配置，选择公式可能会更好。 有关更深入的说明，请参阅[比较自定义映像和 DevTest 实验室中的公式](devtest-lab-comparing-vm-base-image-types.md)。

### <a name="when-should-i-use-a-formula-vs-custom-image"></a>何时应使用公式以及自定义映像？
通常，此方案中的决定因素是成本和重用。 如果你的方案中有许多用户/实验室需要在基础映像之上使用具有大量软件的映像，则可以通过创建自定义映像来降低成本。 这意味着该映像创建一次。 它减少了虚拟机设置时间以及由于在设置时运行虚拟机而产生的成本。

但是，需要注意的另一个因素是软件包的更改频率。 如果运行每日内部版本，并要求该软件位于用户的虚拟机上，请考虑使用公式而不是自定义映像。

有关更深入的说明，请参阅[比较自定义映像和 DevTest 实验室中的公式](devtest-lab-comparing-vm-base-image-types.md)。

### <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>如何自动上传 VHD 文件以创建自定义映像？

可通过两种方式自动上传 VHD 文件以创建自定义映像：

- 使用 [AzCopy](../storage/common/storage-use-azcopy-v10.md) 将 VHD 文件复制或上传到与实验室关联的存储帐户。
- 使用 [Azure 存储资源管理器](../vs-azure-tools-storage-manage-with-storage-explorer.md)。 存储资源管理器是在 Windows、OS X 和 Linux 上运行的独立应用。

查找与实验室关联的目标存储帐户：

1.  登录 [Azure 门户](https://portal.azure.com)。
2.  在左侧菜单上，选择“资源组”。
3.  查找并选择与实验室关联的资源组。
4.  在“概述”下，选择一个存储帐户。
5.  选择“Blob”。
6.  查找列表中的上传。 如果不存在，则返回到步骤 4，并尝试另一个存储帐户。
7.  将该 URL 用作 AzCopy 命令中的目标。

何时应使用 Azure 市场映像以及自己的自定义组织映像？

### <a name="when-should-i-use-an-azure-marketplace-image-vs-my-own-custom-organizational-image"></a>何时应使用 Azure 市场映像以及自己的自定义组织映像？
除非有特定考虑或组织要求，否则默认情况下应使用 Azure 市场映像。 一些常见示例包括：

- 复杂的软件设置，需要将应用程序作为基本映像的一部分包含在内。
- 安装和设置应用程序可能需要花费数小时，这没有有效利用 Azure 市场映像上添加的计算时间。
- 开发人员和测试人员需要快速访问虚拟机，并希望最大限度地缩短新虚拟机的设置时间。
- 所有计算机必须具备的合规性或法规条件（例如安全策略）。
- 不应轻易使用自定义映像。 它们会引入额外的复杂性，因为你现在必须管理那些底层基础映像的 VHD 文件。 此外，还需要使用软件更新定期修补这些基础映像。 这些更新包括新的操作系统 (OS) 更新，以及软件包本身所需的任何更新或配置更改。

## <a name="artifacts"></a>项目

### <a name="what-are-artifacts"></a>项目是什么？
项目是用于将最新位或开发工具部署到 VM 的可自定义元素。 创建 VM 时，将项目附加到 VM。 预配 VM 后，项目可以部署和配置 VM。 [公共 GitHub 存储库](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts)中提供了多种预先存在的项目。 也可以[创作自己的项目](devtest-lab-artifact-author.md)。

### <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>VM 创建过程中项目失败。 如何解决它？
要了解如何获取失败项目的日志，请参阅[如何诊断开发测试实验室中的项目失败](devtest-lab-troubleshoot-artifact-failure.md)。

### <a name="when-should-an-organization-use-a-public-artifact-repository-vs-private-artifact-repository-in-devtest-labs"></a>组织何时应在开发测试实验室中使用公共项目存储库或专用项目存储库？
[公共项目存储库](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts)提供初始的一组最常用软件包。 它有助于快速完成部署，避免花费时间来重新生成常用的开发人员工具和加载项。你可以选择部署组织自有的专用存储库。 可以同时使用公共和专用存储库。 还可以选择禁用公共存储库。 部署专用存储库的条件应根据以下问题和注意事项而定：

- 组织是否必须在其开发测试实验室套餐中提供企业许可的软件？ 如果是，则应创建专用存储库。
- 组织是否开发可提供特定操作的、在总体预配过程中所需的自定义软件？ 如果是，则应部署专用存储库。
- 如果组织的监管策略需要隔离，并且外部存储库不在组织的直接配置管理范畴内，则应部署专用项目存储库。 在此过程中，可以复制公共存储库的初始副本，并将其与专用存储库集成。 然后可以禁用公共存储库，使组织中的任何人都不再可以访问它。 此方法会强制组织中的所有用户只能使用组织批准的单个存储库，并可以最大程度地减少配置偏差。


### <a name="should-an-organization-plan-for-a-single-repository-or-allow-multiple-repositories"></a>组织是要规划单个存储库还是允许多个存储库？
作为组织总体监管和配置管理策略的一部分，我们建议使用集中式存储库。 使用多个存储库时，它们可能会逐渐变成孤立的非托管软件。 使用中心存储库时，多个团队可对其项目使用此存储库中的项目。 此方法强制实施标准化、安全性和易管理性方面的措施，并消除了重复工作。 在集中化过程中，建议采取以下措施来实现长期管理和可持续性：

- 将 Azure Repos 与 Azure 订阅用于身份验证和授权的同一 Azure Active Directory 租户相关联。
- 在 Azure Active Directory 中创建名为 `All DevTest Labs Developers` 的集中管理的组。 应将参与项目开发的任何开发人员置于此组中。
- 可使用同一个 Azure Active Directory 组来提供对 Azure Repos 存储库和实验室的访问。
- 在 Azure Repos 中，应使用分支或分叉将开发中存储库与主要生产存储库相隔离。 在正确完成代码评审后，使用拉取请求将内容仅添加到主分支。 代码评审员批准更改后，负责主分支维护工作的开发人员主管合并更新的代码。

## <a name="cicd-integration"></a>CI/CD 集成

### <a name="does-devtest-labs-integrate-with-my-cicd-toolchain"></a>开发测试实验室可与我的 CI/CD 工具链集成吗？
如果使用的是 Azure DevOps，则可使用[开发测试实验室任务扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)自动化开发测试实验室的发布管道。 可使用此扩展执行的任务包括：

- 自动创建和部署 VM。 还可以使用 Azure 文件复制或 PowerShell Azure DevOps Services 任务配置具有最新生成的 VM。
- 测试后自动获取 VM 的状态以在同一 VM 中再现 Bug 进行进一步调查。
- 当不再需要时，删除发布管道末尾的 VM。

以下博客文章提供了有关使用 Azure DevOps Services 扩展的指南和信息：

- [开发测试实验室和 Azure DevOps 扩展](integrate-environments-devops-pipeline.md)
- [在现有开发测试实验室中从 Azure DevOps Services 部署新 VM](https://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS)
- [使用Azure DevOps Services 发布管理对开发测试实验室进行持续部署](https://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs)

对于其他持续集成 (CI)/持续交付 (CD) 工具链，可通过使用 [Azure PowerShell cmdlet](../azure-resource-manager/templates/deploy-powershell.md) 和 [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/) 部署 [Azure 资源管理器模板](https://azure.microsoft.com/resources/templates/)来实现相同的方案。 还可使用[用于开发测试实验室的 REST API](https://aka.ms/dtlrestapis) 与工具链进行集成。

## <a name="networking"></a>网络

### <a name="when-should-i-create-a-new-virtual-network-for-my-devtest-labs-environment-vs-using-an-existing-virtual-network"></a>何时应该为开发测试实验室环境创建新的虚拟网络以及使用现有的虚拟网络？
如果你的 VM 需要与现有基础结构进行交互，则考虑使用开发测试实验室环境中的现有虚拟网络。 如果使用 ExpressRoute，可能需要将虚拟网络/子网的数量降至最低，以便不会对分配给订阅的使用的 IP 地址空间进行分段。

请考虑使用此处的虚拟网络对等互连模式 ([中心辐射模型](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)) 。 此方法支持跨订阅的 vnet/子网通信。 除此之外，每个开发测试实验室环境可以有其自己的虚拟网络。

每个订阅的虚拟网络数量有[限制](../azure-resource-manager/management/azure-subscription-service-limits.md)。 默认数量为 50，但此限制可以提高到 100。

### <a name="when-should-i-use-a-shared-ip-vs-public-ip-vs-private-ip"></a>何时应使用共享 IP 与公用 IP 与专用 IP？

如果使用站点到站点 VPN 或快速路由，则考虑使用专用 IP，以便你的计算机可通过内部网络访问，并且无法通过公共 Internet 访问。

> [!NOTE]
> 实验室所有者可以更改此子网策略，以确保不会有人意外地为其 VM 创建公共 IP 地址。 订阅所有者应创建一个阻止创建公用 IP 的订阅策略。

当使用共享的公共 IP 时，实验室中的虚拟机会共享公共 IP 地址。 当需要避免违反给定订阅的公共 IP 地址限制时，此方法很有用。

### <a name="how-do-i-ensure-that-development-and-test-virtual-machines-are-unable-to-reach-the-public-internet-are-there-any-recommended-patterns-to-set-up-network-configuration"></a>如何确保开发和测试虚拟机无法访问公共 Internet？ 是否有任何建议的模式来设置网络配置？

是的。 有两个方面需要考虑，即入站和出站流量。

- **入站流量** - 如果虚拟机没有公共 IP 地址，则无法通过 Internet 访问它。 一种常见方法是确保设置订阅级别的策略，以便任何用户都不能创建公共 IP 地址。
- **出站流量** - 如果要防止虚拟机直接访问公共 Internet 并强制流量通过企业防火墙，则可以使用强制路由通过快速路由或 VPN 在本地路由流量。

> [!NOTE]
> 如果你的代理服务器在没有代理设置的情况下阻止流量，则不要忘记向实验室的项目存储帐户添加例外。

此外，还可以将网络安全组用于虚拟机或子网。 此步骤增添了一层额外的保护以允许/阻止流量。

## <a name="troubleshooting"></a>疑难解答

### <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>为什么我的虚拟网络没有正确保存？
一种可能是虚拟网络名称包含具句号。 如果包含句号，请尝试将其删除，或将其替换为连字符。 然后再次尝试保存虚拟网络。

### <a name="why-do-i-get-a-parent-resource-not-found-error-when-i-provision-a-vm-from-powershell"></a>从 PowerShell 预配 VM 时，为什么会收到“找不到父资源”错误？
若一个资源是另一个资源的父资源，则该父资源必须存在才可创建子资源。 如果父资源不存在，则会看到 ParentResourceNotFound 消息。 如果未指定与父资源的依赖关系，子资源可能会在父资源之前部署。

VM 是资源组中实验室下的子资源。 使用资源管理器模板通过 PowerShell 部署 VM 时，PowerShell 脚本中提供的资源组名称应为实验室的资源组名称。 有关详细信息，请参阅[排查常见的 Azure 部署错误](../azure-resource-manager/templates/common-deployment-errors.md)。

### <a name="where-can-i-find-more-error-information-if-a-vm-deployment-fails"></a>VM 部署失败时，可在何处查找错误详细信息？
活动日志中会捕获 VM 部署错误。 在实验室的 VM 页（在“我的虚拟机”列表中选择 VM 后，会显示该页面）中的资源菜单上，可在“审核日志”或“虚拟机诊断”下方找到实验室 VM 活动日志 。

有时，部署错误发生在 VM 部署开始之前。 一个示例是超过与 VM 一起创建的资源的订阅限制时。 在此情况下，会在实验室级别活动日志中捕获错误详细信息。 活动日志位于“配置和策略”设置的底部。 若要深入了解如何在 Azure 中使用活动日志，请参阅[查看活动日志以审核对资源的操作](../azure-resource-manager/management/view-activity-logs.md)。

### <a name="why-do-i-get-location-is-not-available-for-resource-type-error-when-trying-to-create-a-lab"></a>为什么在尝试创建实验室时，我会收到“位置不适用于资源类型”的错误？
尝试创建实验室时，你会看到一条类似于以下内容的错误消息：

```
The provided location 'australiacentral' is not available for resource type 'Microsoft.KeyVault/vaults'. List of available regions for the resource type is 'northcentralus,eastus,northeurope,westeurope,eastasia,southeastasia,eastus2,centralus,southcentralus,westus,japaneast,japanwest,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia,canadacentral,canadaeast,uksouth,ukwest,westcentralus,westus2,koreacentral,koreasouth,francecentral,southafricanorth
```

可以通过执行以下步骤之一来解决此错误：

#### <a name="option-1"></a>选项 1
查看[可用产品(按区域)](https://azure.microsoft.com/global-infrastructure/services/)页上 Azure 区域中资源类型的可用性。 如果资源类型在特定区域不可用，则开发测试实验室不支持在该区域创建实验室。 创建实验室时选择其他区域。

#### <a name="option-2"></a>方法 2
如果资源类型在你所在区域可用，请检查其是否已注册到你的订阅。 此操作可以在订阅所有者级别完成，如[本文](../azure-resource-manager/management/resource-providers-and-types.md)所示。
