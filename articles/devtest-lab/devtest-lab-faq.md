---
title: "Azure 开发测试实验室常见问题解答 | Microsoft Docs"
description: "查找 Azure 开发测试实验室常见问题的答案"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: afe83109-b89f-4f18-bddd-b8b4a30f11b4
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: 7a9b8037967f0180d2f7d2a7d100d0cc3ad9e4a2
ms.lasthandoff: 04/07/2017


---
# <a name="azure-devtest-labs-faq"></a>Azure 开发测试实验室常见问题
本文将回答一些关于 Azure 开发测试实验室的最常见问题。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="general"></a>常规
* [如果未在此处找到相关问题怎么办？](#what-if-my-question-isnt-answered-here)
* [为什么要使用 Azure 开发测试实验室？](#why-should-i-use-azure-devtest-labs)
* [“无忧的自助服务”是什么意思？](#what-does-worry-free-self-service-mean)
* [如何使用 Azure 开发测试实验室？](#how-can-i-use-azure-devtest-labs)
* [Azure 开发测试实验室将如何计费？](#how-am-i-billed-for-azure-devtest-labs)

## <a name="security"></a>“安全”
* [Azure 开发测试实验室中有哪些安全级别？](#what-are-the-different-security-levels-in-azure-devtest-labs)
* [如何通过创建角色来允许用户执行特定任务？](#how-do-i-create-a-role-to-allow-users-to-perform-a-specific-task)

## <a name="cicd-integration--automation"></a>CI/CD 集成和自动化
* [Azure 开发测试实验室可与我的 CI/CD 工具链集成吗？](#does-azure-devtest-labs-integrate-with-my-cicd-toolchain)

## <a name="virtual-machines"></a>虚拟机
* [为什么我无法在 Azure 虚拟机边栏选项卡上看到可在 Azure 开发测试实验室中看到的某些 VM？](#why-cant-i-see-certain-vms-in-the-azure-virtual-machines-blade-that-i-see-within-azure-devtest-labs)
* [自定义映像与公式之间的区别是什么？](#what-is-the-difference-between-custom-images-and-formulas)
* [如何一次性从同一模板创建多个 VM？](#how-do-i-create-multiple-vms-from-the-same-template-at-once)
* [如何将现有 Azure VM 移到 Azure 开发测试实验室？](#how-do-i-move-my-existing-azure-vms-into-my-azure-devtest-labs-lab)
* [可将多个磁盘附加到我的 VM 中吗？](#can-i-attach-multiple-disks-to-my-vms)
* [如果要将 Windows OS 映像用于我的测试，是否需要购买 MSDN 订阅？](#if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription)
* [如何自动上传 VHD 文件以创建自定义映像？](#how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images)
* [如何自动删除实验室中的所有 VM？](#how-can-i-automate-the-process-of-deleting-all-the-vms-in-my-lab)

## <a name="artifacts"></a>项目
* [项目是什么？](#what-are-artifacts)

## <a name="lab-configuration"></a>实验室配置
* [如何从 Azure Resource Manager 模板创建实验室？](#how-do-i-create-a-lab-from-an-azure-resource-manager-template)
* [为什么我的 VM 创建于具有随机名称的不同资源组？我能重新命名或修改这些资源组吗？](#why-are-my-vms-created-in-different-resource-groups-with-arbitrary-names-can-i-rename-or-modify-these-resource-groups)
* [在同一订阅下，可创建多少个实验室？](#how-many-labs-can-i-create-under-the-same-subscription)
* [每个实验室可以创建多少个 VM？](#how-many-vms-can-i-create-per-lab)
* [如何向我的实验室共享直接链接？](#how-do-i-share-a-direct-link-to-my-lab)
* [什么是 Microsoft 帐户？](#what-is-a-microsoft-account)

## <a name="troubleshooting"></a>故障排除
* [ 创建过程中项目失败。如何解决它？](#my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it)
* [为什么我的虚拟网络没有正确保存？](#why-isnt-my-existing-virtual-network-saving-properly)
* [从 PowerShell 预配时，为什么会收到“找不到父资源”错误？](#why-do-i-get-a-parent-resource-not-found-error-when-provisioning-a-vm-from-powershell)  
* [VM 部署失败时，可在何处查找错误详细信息？](#where-can-i-find-more-error-information-if-a-vm-deployment-fails)  

### <a name="what-if-my-question-isnt-answered-here"></a>如果未在此处找到相关问题怎么办？
如果此处未列出你的问题，请联系我们寻求答案。

* 在此常见问题解答末尾的 [Disqus 线程](#comments)处发布问题，并与 Azure 缓存团队和其他社区成员就本文进行讨论。
* 若希望更多的人看到你的问题，可以将问题发布在 [Azure 开发测试实验室 MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs)并与 Azure 开发测试实验室团队和社区的其他成员讨论。
* 若要提出功能请求，请将请求和想法提交到 [Azure 开发测试实验室用户之声](https://feedback.azure.com/forums/320373-azure-devtest-labs)。

### <a name="why-should-i-use-azure-devtest-labs"></a>为什么要使用 Azure 开发测试实验室？
Azure 开发测试实验室可为你的团队节省时间和金钱。 开发人员可使用几个不同的基项创建自己的环境，并使用项目快速部署和配置应用程序。 使用自定义映像和公式，可将虚拟机保存为模板，并轻松重现。 此外，实验室会提供多个可配置策略，允许实验室管理员减少浪费和管理团队的环境。 这些策略包括自动关机、成本阈值、每个用户的最大 VM 数量和最大 VM 大小。 若要更深入了解 Azure 开发测试实验室，请参阅[概述](devtest-lab-overview.md)或观看[介绍视频](/documentation/videos/videos/what-is-azure-devtest-labs)。

### <a name="what-does-worry-free-self-service-mean"></a>“无忧的自助服务”是什么意思？
“无忧的自助服务”意味着开发人员和测试人员按需创建自己的环境，管理员可获得一种安全感，因为知道 Azure 开发测试实验室可帮助尽量减少浪费和控制成本。 管理员可以指定允许的 VM 大小、最大 VM 数量以及 VM 的启动和关机时间。 Azure 开发测试实验室还可帮助你轻松监视成本和设置警报以注意实验室中的资源是怎样使用的。

### <a name="how-can-i-use-azure-devtest-labs"></a>如何使用 Azure 开发测试实验室？
需要开发或测试环境且希望快速对其进行再现和/或使用成本节省策略进行管理时，Azure 开发测试实验室都非常有帮助。

我们的客户将 Azure 开发测试实验室用于以下情形：

* 在同一位置管理开发和测试环境，使用策略来降低成本和自定义映像以在整个团队中共享生成。
* 使用自定义映像开发应用程序保存整个开发阶段的磁盘状态。
* 跟踪与进度相关的成本。
* 为质量保证测试创建大容量测试环境。
* 使用项目和公式轻松在各种环境上配置和重现应用程序。
* 为编程马拉松分发 VM（协作开发或测试工作）并在事件结束时轻松对其取消预配。

### <a name="how-am-i-billed-for-azure-devtest-labs"></a>Azure 开发测试实验室将如何计费？
Azure 开发测试实验室是免费的服务，意味着可免费创建实验室；配置策略、模板和项目。 仅支付在实验室中使用的 Azure 资源（例如虚拟机、存储帐户和虚拟网络）。 若要了解实验室资源的成本，请参阅 [Azure 开发测试实验室定价](https://azure.microsoft.com/pricing/details/devtest-lab/)。

### <a name="what-are-the-different-security-levels-in-azure-devtest-labs"></a>Azure 开发测试实验室中有哪些安全级别？
安全访问权限由 [Azure 基于角色的访问控制 (RBAC)](../active-directory/role-based-access-built-in-roles.md) 决定。 若要了解如何访问，最好先了解权限、角色和由 RBAC 定义的作用域之间的差异。

* **权限** - 权限是对特定操作的定义访问。 例如，权限可以是对所有虚拟机的读取访问权限。
* **角色** - 角色是可进行分组和分配给用户的一组权限。 例如，“订阅所有者”对订阅中所有资源都具有访问权限。
* **作用域** - 作用域是 Azure 资源的层次结构中的级别。 例如，作用域可以是资源组、单个实验室或整个订阅。

Azure 开发测试实验室的作用域中存在两种定义用户权限的角色：实验室所有者和实验室用户。

* **实验室所有者** - 实验室所有者具有对实验室中所有资源的访问权限。 因此，它们可以修改策略、读取和写入任意 VM、更改虚拟网络等。
* **实验室用户** - 实验室用户可查看实验室中的所有资源（例如 VM、策略和虚拟网络），但不能修改其他用户创建的策略和 VM。 还可能在 Azure 开发测试实验室中创建自定义角色，可参阅本文[授予对特定实验室策略的用户权限](devtest-lab-grant-user-permissions-to-specific-lab-policies.md)了解操作方法。

由于作用域是分层的，因此用户在某个作用域具有权限时，将会自动授予他所包含的每个较低级别的作用域的权限。 例如，如果用户分配到订阅所有者的角色，那么他可访问订阅中的所有资源。 这些资源包括所有虚拟机、所有虚拟网络和所有实验室。 因此，订阅所有者将自动继承实验室所有者的角色。 但是反过来则不适用。 实验室所有者具有访问实验室的权限，其低于订阅级别的作用域。 因此，实验室所有者不能查看虚拟机或虚拟网络或实验室之外的任何资源。

### <a name="how-do-i-create-a-role-to-allow-users-to-perform-a-specific-task"></a>如何通过创建角色来允许用户执行特定任务？
在此处找到有关如何创建自定义角色，并将权限分配给该角色的综合性文章。 下面是创建该角色的脚本示例“开发测试实验室高级用户”，其具有权限启动和停止实验室中的所有 VM：

    $policyRoleDef = Get-AzureRmRoleDefinition "DevTest Labs User"
    $policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*')
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "DevTest Labs Advance User"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action")
    $policyRoleDef = New-AzureRmRoleDefinition -Role $policyRoleDef  

### <a name="does-azure-devtest-labs-integrate-with-my-cicd-toolchain"></a>Azure 开发测试实验室可与我的 CI/CD 工具链集成吗？
如果你使用的是 VSTS，则可使用 [Azure 开发测试实验室任务扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)自动化 Azure 开发测试实验室的发布管道。 此扩展的某些用途包括：

* 自动创建和部署 VM 并使用最新版本通过 Azure 文件复制或 PowerShell VSTS 任务对其进行配置。
* 测试后自动获取 VM 的状态以在同一 VM 中再现 Bug 进行进一步调查。
* 当不再需要时删除发布管道末尾的 VM。

以下博客文章提供了有关使用 VSTS 扩展的指南和信息：

* [Azure 开发测试实验室 – VSTS 扩展](https://blogs.msdn.microsoft.com/devtestlab/2016/06/15/azure-devtest-labs-vsts-extension/)
* [在现有 Azure 开发测试实验室中从 VSTS 部署新 VM](http://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS)
* [使用 VSTS 发布管理对 Azure 开发测试实验室进行连续部署](http://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs)

对于其他 CI/CD 工具链，上述所有可通过 VSTS 任务扩展完成的方案同样可使用 [Azure PowerShell cmdlets](../azure-resource-manager/resource-group-template-deploy.md) 和 [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/) 通过部署[Azure Resource Manager 模板](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates)完成。 还可使用 [用于开发测试实验室的 REST API](http://aka.ms/dtlrestapis) 与你的工具链进行集成。  

### <a name="why-cant-i-see-certain-vms-in-the-azure-virtual-machines-blade-that-i-see-within-azure-devtest-labs"></a>为什么我无法在 Azure 虚拟机边栏选项卡上看到可在 Azure 开发测试实验室中看到的某些 VM？
在 Azure 开发测试实验室中创建 VM 时，会授予访问该 VM 的权限。 可在实验室边栏选项卡和“虚拟机”边栏选项卡中查看它。 开发测试实验室角色中的用户可查看所有在实验室中通过实验室的“所有虚拟机”边栏选项卡创建的虚拟机。 但是，不会自动授予开发测试实验室角色中的用户对他人创建的 VM 资源的读取访问权限。 因此，这些虚拟机不会显示在“虚拟机”边栏选项卡中。

### <a name="what-is-the-difference-between-custom-images-and-formulas"></a>自定义映像与公式之间的区别是什么？
自定义映像是 VHD（虚拟硬盘），而公式为可使用其他设置（可保存和再现）配置的映像。 若要使用同一基本、不可变的映像快速创建多个环境，选择自定义映像可能会更好。 若要使用最新位、虚拟网络/子网或特定大小再现 VM 的配置，选择公式可能会更好。 有关更深入的说明，请参阅文章[比较实验室中的自定义映像和公式](devtest-lab-comparing-vm-base-image-types.md)。

### <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>如何一次性从同一模板创建多个 VM？
创建 VM 和[从 Windows PowerShell 部署 Azure Resource Manager 模板](../azure-resource-manager/resource-group-template-deploy.md)时，可使用 [VSTS 任务扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)或[生成 Azure Resource Manager 模板](devtest-lab-add-vm-with-artifacts.md#save-azure-resource-manager-template)。

### <a name="how-do-i-move-my-existing-azure-vms-into-my-azure-devtest-labs-lab"></a>如何将现有 Azure VM 移到 Azure 开发测试实验室？
我们正设计解决方案，直接将 VM 移动到 Azure 开发测试实验室，但目前你可以将现有 VM 复制到 Azure 开发测试实验室，如下所示：

1. 使用此 [Windows PowerShell 脚本](https://github.com/Azure/azure-devtestlab/blob/master/Scripts/CopyVHDFromVMToLab.ps1)复制现有 VM 的 VHD 文件
2. 在 Azure 开发测试实验室内[创建自定义映像](devtest-lab-create-template.md)。
3. 在实验室中从自定义映像创建 VM

### <a name="can-i-attach-multiple-disks-to-my-vms"></a>可将多个磁盘附加到我的 VM 中吗？
支持将多个磁盘附加到 VM。  

### <a name="if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription"></a>如果要将 Windows OS 映像用于我的测试，是否需要购买 MSDN 订阅？
如果需要在 Azure 中使用 Windows 客户端 OS 映像（Windows 7 或更高版本）进行开发或测试，必须：

- [购买 MSDN 订阅](https://www.visualstudio.com/products/how-to-buy-vs)。
- 如果你具有企业协议，请通过 [Enterprise 开发/测试优惠](https://azure.microsoft.com/en-us/offers/ms-azr-0148p)创建 Azure 订阅。

若要深入了解每个 MSDN 产品/服务的 Azure 信用额度，请参阅 [Visual Studio 订户的每月 Azure 信用额度](https://azure.microsoft.com/en-us/pricing/member-offers/msdn-benefits-details/)。

### <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>如何自动上传 VHD 文件以创建自定义映像？
有两个选项：

* [Azure AzCopy](../storage/storage-use-azcopy.md#blob-upload) 可用于将 VHD 文件复制或上传到与实验室相关的存储帐户。
* [Microsoft Azure 存储资源管理器](../vs-azure-tools-storage-manage-with-storage-explorer.md) 是在 Windows、OSX 和 Linux 上运行的独立应用。   

请遵下以下步骤查找与实验室关联的模板存储帐户：

1. 登录到 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
2. 选择左侧面板中的“资源组”。
3. 查找并选择与实验室关联的资源组。
4. 在“概述”边栏选项卡上，选择其中一个存储帐户。
5. 选择“Blob”。
6. 查找列表中的上传。 如果不存在，则返回到步骤 4，并尝试另一个存储帐户。
7. 将该 **URL** 用作 AzCopy 命令中的目标。

### <a name="how-can-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>如何自动删除实验室中的所有 VM？
除了通过 Azure 门户删除实验室中的 VM，还可使用 PowerShell 脚本删除实验室中的所有 VM。 在下例中，修改“要更改的值”注释下的参数值。 可以从 Azure 门户的实验室边栏选项卡中检索 `subscriptionId`、`labResourceGroup` 和 `labName` 值。

    # Delete all the VMs in a lab

    # Values to change
    $subscriptionId = "<Enter Azure subscription ID here>"
    $labResourceGroup = "<Enter lab's resource group here>"
    $labName = "<Enter lab name here>"

    # Login to your Azure account
    Login-AzureRmAccount

    # Select the Azure subscription that contains the lab. This step is optional
    # if you have only one subscription.
    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    # Get the lab that contains the VMs to delete.
    $lab = Get-AzureRmResource -ResourceId ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)

    # Get the VMs from that lab.
    $labVMs = Get-AzureRmResource | Where-Object {
              $_.ResourceType -eq 'microsoft.devtestlab/labs/virtualmachines' -and
              $_.ResourceName -like "$($lab.ResourceName)/*"}

    # Delete the VMs.
    foreach($labVM in $labVMs)
    {
        Remove-AzureRmResource -ResourceId $labVM.ResourceId -Force
    }




### <a name="what-are-artifacts"></a>项目是什么？
项目是用于将最新位或开发工具部署到 VM 的可自定义元素。 仅需单击几次即可在创建期间将其附加到你的 VM 中，且预配 VM 后，项目将部署和配置你的 VM。 我们的[公共 GitHub 存储库](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts)中有各种预先存在的项目，但你也可轻松[创作自己的项目](devtest-lab-artifact-author.md)。

### <a name="how-do-i-create-a-lab-from-an-azure-resource-manager-template"></a>如何从 Azure Resource Manager 模板创建实验室？
我们提供了 [实验室 Azure Resource Manager 模板的 GitHub 存储库](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates)，你可对其按原样部署，或对其进行修改以为你的实验室创建自定义模板。 其中每个模板都提供了链接，可单击链接在自己的 Azure 订阅下按原样部署实验室，或[使用 PowerShell 或 Azure CLI](../azure-resource-manager/resource-group-template-deploy.md) 自定义模板和部署。

### <a name="why-are-my-vms-created-in-different-resource-groups-with-arbitrary-names-can-i-rename-or-modify-these-resource-groups"></a>为什么我的 VM 创建于具有随机名称的不同资源组？ 我能重新命名或修改这些资源组吗？
以这种方式创建资源组以便 Azure 开发测试实验室管理用户权限和访问虚拟机。 虽然你可以将 VM 移动到具有所需名称的另一资源组，但不推荐此操作。 我们正努力改善该体验，允许更大的灵活性。   

### <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>在同一订阅下，可创建多少个实验室？
没有对每个订阅可创建的实验室数量进行限定。 但对每个订阅使用的资源进行了限定。 可参阅[对 Azure 订阅施加的限制和配额](../azure-subscription-service-limits.md)和[如何增加这些限制](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests)。

### <a name="how-many-vms-can-i-create-per-lab"></a>每个实验室可以创建多少个 VM？
没有对每个实验室可创建的 VM 数量进行限定。 但是，目前实验室仅支持大约 40 个 VM 同时在标准存储中运行，25 个 VM 同时在高级存储中运行。 我们目前正努力提高这些限制。

### <a name="how-do-i-share-a-direct-link-to-my-lab"></a>如何向我的实验室共享直接链接？
若要将直接链接共享给实验室用户，可执行以下步骤：

1. 浏览到 Azure 门户中的实验室。
2. 从浏览器复制实验室 URL 并与实验室的用户进行共享。

> [!NOTE]
> 如果实验室用户是具有 [Microsoft 帐户](#what-is-a-microsoft-account)的外部用户且不属于公司的 Active Directory，则他们在导航到所提供的链接时可能会收到错误。 若用户收到错误，请指导他们在 Azure 门户的右上角单击其名称，然后在菜单的“目录”部分选择实验室所在的目录。
>
>

### <a name="what-is-a-microsoft-account"></a>什么是 Microsoft 帐户？
Microsoft 帐户会用于 Microsoft 设备和服务中几乎所有的操作。 这是登录到 Skype、Outlook.com、OneDrive、Windows Phone 和 Xbox LIVE 时所用的电子邮件地址和密码 - 意味着你的文件、照片、联系人和设置可跟随你移动到任何设备。

> [!NOTE]
> Microsoft 帐户以前称为“Windows Live ID”。
>
>

### <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>VM 创建过程中项目失败。 如何解决它？
请参阅由我们的某位 MVP 撰写的博客文章 [How to troubleshoot failing Artifacts in AzureDevTestLabs](http://www.visualstudiogeeks.com/blog/DevOps/How-to-troubleshoot-failing-artifacts-in-AzureDevTestLabs)（如何排查 AzureDevTestLabs 中的失败项目），了解如何获取失败项目的相关日志。

### <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>为什么我的虚拟网络没有正确保存？
一种可能是虚拟网络名称包含具句号。 如果包含，请尝试删除句号或将它们替换为连字符，然后再重新保存虚拟网络。

### <a name="why-do-i-get-a-parent-resource-not-found-error-when-provisioning-a-vm-from-powershell"></a>从 PowerShell 预配 VM 时，为什么会收到“找不到父资源”错误？
若一个资源是另一个资源的父级，则在创建子资源之前，父资源必须存在。 如果不存在，则会收到 **ParentResourceNotFound** 错误。 如果未指定父资源上的依赖项，子资源可能会在父资源之前部署。

VM 是资源组中实验室下的子资源。 使用 Azure Resource Manager 模板通过 PowerShell 部署时，PowerShell 脚本中提供的资源组名称应为实验室的资源组名称。 有关详细信息，请参阅[排查常见的 Azure 部署错误](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-common-deployment-errors#parentresourcenotfound)。

### <a name="where-can-i-find-more-error-information-if-a-vm-deployment-fails"></a>VM 部署失败时，可在何处查找错误详细信息？
VM 部署错误将在活动日志中捕获。 在实验室的 VM 边栏选项卡（在“我的虚拟机”列表中选择 VM 后，将显示该选项卡）中的资源菜单上，可通过“审核日志”或“虚拟机诊断”找到实验室 VM 活动日志。

有时，在开始部署 VM 前（例如在超过与 VM 一起创建的资源的订阅限制时）将发生部署错误。 此情况下，将在实验室级别“活动日志”中捕获错误详细信息，该日志位于“配置和策略”设置的底部。 若要深入了解如何在 Azure 中使用活动日志，请参阅[查看活动日志以审核对资源的操作](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-audit)。

