---
title: "规划迁移的 IaaS 资源的经典到 Azure 资源管理器 |Microsoft 文档"
description: "规划迁移的 IaaS 资源的经典到 Azure 资源管理器"
services: virtual-machines-windows
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 78492a2c-2694-4023-a7b8-c97d3708dcb7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/01/2017
ms.author: kasing
ms.openlocfilehash: db23eba9ff8debd5268cd02bc4f37c4e6501bfac
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="planning-for-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>规划迁移的 IaaS 资源的经典到 Azure 资源管理器
而 Azure 资源管理器提供许多令人惊叹的功能，以确保顺利迁移旅计划至关重要。 在规划上花费时间将确保确实执行迁移活动时遇到问题。

> [!NOTE]
> 以下指南大量已分配由 Azure 客户咨询团队和云解决方案架构师在大型环境中迁移与客户合作。 此类本文档将继续获取更新，当出现成功的新模式，因此检查不时到时间以查看是否有任何新的建议。

有四个常规阶段，迁移旅程的：<br>

![迁移阶段](../media/virtual-machines-windows-migration-classic-resource-manager/plan-labtest-migrate-beyond.png)

## <a name="plan"></a>计划

### <a name="technical-considerations-and-tradeoffs"></a>技术注意事项和缺点

根据你的技术要求大小、 地理区域和操作方案，你可能需要考虑：

1. 为什么需要为你的组织 Azure 资源管理器？  迁移的业务原因有哪些？
2. Azure 资源管理器的技术原因有哪些？  新增功能 （如果有） 要利用其他 Azure 服务？
3. 哪些应用程序 （或虚拟机的设置） 将包含在迁移？
4. 迁移 API 支持哪些方案？  查看[不受支持的功能和配置](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#unsupported-features-and-configurations)。
5. 将你的操作团队现在支持应用程序/Vm 中的经典模式和 Azure 资源管理器？
6. 如何 （如果根本） 未 Azure 资源管理器更改你的 VM 部署，管理、 监视和报告进程？  是否需要更新你的部署脚本？
7. 什么是通信警报利益干系人 （最终用户、 应用程序所有者和基础结构拥有者） 计划？
8. 根据环境的复杂性，应存在为维护期间的应用程序是向最终用户和应用程序所有者不可用？  如果是这样，多长？
9. 什么是培训计划，以确保利益干系人桩和熟练在 Azure 资源管理器？
10. 计划管理或为迁移的项目管理计划是什么？
11. 什么是为 Azure 资源管理器迁移和其他的时间线相关技术道路地图？  它们以最佳方式对齐？

### <a name="patterns-of-success"></a>成功的模式

成功的客户具有详细的前面的问题是讨论、 记录和控制的计划。  请确保迁移计划广泛与发起人和利益干系人的通信。  为你自己了解有关迁移的选项;强烈建议在阅读了此迁移文档的下面设置。

* [平台支持的迁移的 IaaS 资源的经典到 Azure 资源管理器概述](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [技术深入了解如何在平台支持迁移从经典到 Azure 资源管理器](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [规划迁移的 IaaS 资源的经典到 Azure 资源管理器](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [使用 PowerShell 将从经典 IaaS 资源迁移到 Azure 资源管理器](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [使用 CLI 将从经典 IaaS 资源迁移到 Azure 资源管理器](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [社区工具，可迁移的 IaaS 资源的经典到 Azure 资源管理器帮助](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [查看最常见的迁移错误](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [查看有关迁移 IaaS 资源从经典到 Azure 资源管理器中的最常见问题](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="pitfalls-to-avoid"></a>应避免

- 若要计划的故障。  此迁移技术步骤证明非常有效，而结果也是可预测。
- API 将考虑所有方案的迁移支持的平台的假设。 读取[不受支持的功能和配置](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#unsupported-features-and-configurations)以了解支持哪些方案。
- 不打算为最终用户的潜在应用程序中断。  计划足够的缓冲区来充分警告可能不可用的应用程序时间的最终用户。


## <a name="lab-test"></a>实验室测试

**复制环境和执行测试迁移**
  > [!NOTE]
  > 通过使用一个不正式受 Microsoft 支持部门支持的社区提供的工具执行现有环境的精确的复制。 因此，它是**可选**步骤，但它是找出问题，而无需触摸生产环境的最佳办法。 如果使用社区提供的工具不是一个选项，然后阅读下面的验证/准备/中止试运行建议。
  >

  执行你确切 （计算、 网络和存储） 的实验室测试是方案的确保顺利迁移的最佳办法。 这将有助于确保：

  - 完全单独实验室或现有的非生产环境，以进行测试。 我们建议完全单独实验室可以反复迁移并且可破坏性地修改。  下面列出了脚本来收集/hydrate 从实际的订阅的元数据。
  - 它是在单独的订阅中创建实验室一个好办法。 原因是将重复，撤销实验室和具有单独，独立的订阅将减少的一些实际将获取意外删除的可能性。

  可以使用 AsmMetadataParser 工具完成此操作。 [阅读有关此工具此处详细信息](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

### <a name="patterns-of-success"></a>成功的模式

以下是在很多更大的迁移中发现的问题。 这不是一个详尽的列表，并应参考[不受支持的功能和配置](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#unsupported-features-and-configurations)有关详细信息。  你可能需要也可能不会遇到这些技术问题，但如果这样做解决这些尝试迁移之前将确保更流畅的体验。

- **验证/准备/中止试运行**-这可能是最重要的步骤，以确保经典为 Azure 资源管理器迁移成功。 迁移 API 包括三个主要步骤： 验证、 准备和提交。 验证将读取经典环境的状态并返回结果的所有问题。 但是，因为 Azure 资源管理器堆栈中可能存在一些问题，验证将不会捕获的所有内容。 迁移过程的下一步，准备将帮助公开这些问题。 准备将元数据将从移动经典到 Azure 资源管理器中，但将不提交移动，并将不删除或更改经典端上的任何内容。 试运行涉及准备迁移，则中止 (**未提交所导致**) 迁移准备。 验证/准备/中止试运行的目标是若要查看所有 Azure 资源管理器堆栈中的元数据，对其进行检查 (*以编程方式或在门户*)，并验证所有内容正确，迁移和浏览技术问题。  它还可带来迁移持续时间的意义上以便你可以相应地规划的停机时间。  验证/准备/中止不会导致任何用户的停机时间;因此，它是与应用程序使用非破坏性。
  - 以下各项将需要解决之前试运行，但如果它们错过了试运行测试也安全地将刷新这些准备步骤。 企业在迁移期间，我们发现试运行要确保迁移准备情况的安全和重要方法。
  - 当准备正在运行，该控件将为整个虚拟网络中，锁定平面 （Azure 管理操作），因此任何更改可以在验证/准备/中止期间不进行对 VM 元数据。  但否则为任何应用程序函数 (RD，VM 使用情况等) 不会受到影响。  Vm 的用户不会知道正在执行试运行。

- **高速路由线路以及 VPN**。 当前 Express 授权链接的路由网关不能迁移而无需停机。 解决方法，请参阅[迁移 ExpressRoute 线路和虚拟网络关联从经典到资源管理器部署模型](../../expressroute/expressroute-migration-classic-resource-manager.md)。

- **VM 扩展**-虚拟机扩展可能是为迁移正在运行的 Vm 最大障碍之一。 VM 扩展的修正可能需要起到 1-2 天，因此请相应地计划。  一个有效的 Azure 代理需要报告回运行的 Vm 的 VM 扩展状态。 如果状态为正在运行的 VM 处于作为错误返回，这将暂停迁移。 代理本身不需要处于工作状态，以启用迁移，但如果扩展在 VM 上存在，然后 （与 DNS) 和出站 internet 连接的工作代理将会迁移所需的向前移动。
  - 如果在迁移期间，除 BGInfo 版本 1 的所有 VM 扩展，与 DNS 服务器的连接将丢失。\*需要首先将从每个 VM 之前准备迁移和重新随后重新添加到 VM Azure 资源管理器在迁移后删除。  **这项仅用于正在运行的 Vm。**  如果 Vm 停止解除分配的因此 VM 扩展不需要删除。

  > [!NOTE]
  > 许多扩展像 Azure 诊断和安全中心监视将重新安装本身迁移后，因此删除它们并不是问题。

  - 此外，确保网络安全组不限制出站 internet 访问。 这可能发生在某些网络安全组配置。 要迁移到 Azure 资源管理器中的 VM 扩展需要出站 internet 访问 （和 DNS）。
  - BGInfo 扩展的两个版本存在，并称为版本 1 和 2。  

      - 如果 VM 使用 BGInfo 版本 1 扩展插件，你可以将此扩展，如是。 迁移 API 跳过此扩展。 在迁移后，可以添加 BGInfo 扩展。
      - 如果 VM 使用基于 JSON 的 BGInfo 版本 2 扩展，使用 Azure 门户创建 VM。 提供代理正常工作，并且具有出站 internet 访问 （和 DNS），迁移 API 在迁移到 Azure 资源管理器中，包含此扩展。

  - **修正选项 1**。 如果你知道你的 Vm 不会出站 internet 访问，可用的 DNS 服务，并在虚拟机上使用 Azure 的代理，然后卸载之前准备迁移过程中所有 VM 扩展，然后重新安装 VM 扩展后迁移。
  - **修正选项 2**。 如果 VM 扩展是而言太大的障碍，另一个选项是迁移之前的所有虚拟机到关闭/解除分配。 迁移解除分配的 Vm，然后在 Azure 资源管理器端重新启动它们。 这样做的好处是将迁移 VM 扩展。 其缺点在于面向虚拟 Ip 的所有公众将都会丢失 （这可能都是非初学者），并且显然将关闭 Vm 上工作应用程序造成影响要大得多。

    > [!NOTE]
    > 如果针对所迁移正在运行的 Vm 配置 Azure 安全中心策略，则需要对安全策略进行删除扩展之前，先停止，否则监视扩展的安全性将后重新安装会自动在 VM 上将其删除。

- **可用性集**-虚拟网络 (vNet) 要迁移到 Azure 资源管理器，包含的 Vm 必须是在一个可用性集中，经典部署 （即云服务） 或 Vm 必须所有不是任何可用性集中。 具有多个可用性集在云服务中不兼容与 Azure 资源管理器中，并将暂停迁移。  此外，不能有在可用性集中时，某些 Vm 和不在可用性集中的一些虚拟机。 若要解决此问题，你将需要来修正或重新安排你的云服务。  计划相应地这可能很耗时。

- **Web/辅助角色部署**-云服务包含 web 和辅助角色无法迁移到 Azure 资源管理器。 可以开始迁移之前，首先必须从虚拟网络删除 web/辅助角色。  典型的解决方案是只是将 web/辅助角色实例移到单独的经典虚拟网络还链接到 ExpressRoute 线路，或者将代码迁移到较新的 PaaS 应用程序服务 （此讨论已超出本文的范围）。 在前重新部署用例，创建新的经典虚拟网络、 移动/重新部署到该新虚拟网络时，web/辅助角色然后从要移动的虚拟网络中删除的部署。 所需的任何代码更改。 新[虚拟网络对等互连](../../virtual-network/virtual-network-peering-overview.md)功能可以用于对在一起等包含 web/辅助角色和相同的 Azure 区域，如正在迁移的虚拟网络中的其他虚拟网络的经典虚拟网络 (**虚拟网络迁移已按对等虚拟网络不能迁移后**)，因此提供任何性能损失，没有延迟/带宽处罚的相同的功能。 给定添加[虚拟网络对等互连](../../virtual-network/virtual-network-peering-overview.md)，web/辅助角色部署现在轻松地缓解并不会阻止迁移到 Azure 资源管理器。

- **Azure 资源管理器配额**-Azure 区域的经典部署模型和 Azure 资源管理器同时具有单独的配额限制。 即使已在迁移方案中使用新硬件不*（我们正在交换现有 Vm 从经典到 Azure 资源管理器）*，Azure 资源管理器配额仍需处于具有足够的容量的位置，可以开始迁移之前。 下面列出了我们已了解的主要限制会导致问题。  打开配额支持票证以引发限制。

    > [!NOTE]
    > 这些限制需要在要迁移你当前环境所在的同一区域中引发。
    >

    - 网络接口
    - 负载平衡器
    - 公共 Ip
    - 静态公共 Ip
    - 核心
    - 网络安全组
    - 路由表

    你可以检查当前 Azure 资源管理器配额的最新版本的 Azure PowerShell 中使用以下命令。

    **计算** *（内核、 可用性集）*

    ```powershell
    Get-AzureRmVMUsage -Location <azure-region>
    ```

    **网络** *（虚拟网络、 静态公共 Ip、 公共 Ip，网络安全组，网络接口，负载平衡器路由表）*

    ```powershell
    Get-AzureRmUsage /subscriptions/<subscription-id>/providers/Microsoft.Network/locations/<azure-region> -ApiVersion 2016-03-30 | Format-Table
    ```

    **存储** *（存储帐户）*

    ```powershell
    Get-AzureRmStorageUsage
    ```

- **节流限制的 azure 资源管理器 API** -如果你有足够大的环境 （如。 > 400 VNET 中 Vm)，则可能会达到限制为写入操作的默认 API (当前`1200 writes/hour`) 在 Azure 资源管理器。 开始迁移之前，应引发支持票证以增加此限制为你的订阅。


- **设置超时出 VM 状态**-如果任何虚拟机具有的状态`provisioning timed out`，这需要能够解析预迁移。 通过取消设置/重新设置 VM （删除它，使该磁盘，并重新创建 VM） 情况下，若要执行此操作的唯一方法是停机时间状态。

- **RoleStateUnknown VM 状态**-如果迁移暂停由于`role state unknown`错误消息，检查 VM 使用门户，并确保它正在运行。 此错误通常会消失其几分钟后拥有 （无所需的修正） 时，将通常暂时性类型通常出现在虚拟机期间`start`， `stop`，`restart`操作。 **推荐做法：**重试几分钟后再次迁移。

- **Fabric 群集不存在**-在某些情况下，某些奇数由于各种原因无法迁移虚拟机。 这些已知的情况下之一是如果 VM 已最近 （过去一周或操作） 中创建和发生驻留的程序尚未配置适用于 Azure 资源管理器工作负荷的 Azure 群集。  你将收到的错误消息指出`fabric cluster does not exist`并且无法迁移虚拟机。 根据群集将很快变已启用 Azure 资源管理器，等待几天通常将解决此特定问题。 但是，一个直接的解决方法是到`stop-deallocate`VM，然后继续向前迁移，并启动虚拟机备份在 Azure 资源管理器在迁移后。

### <a name="pitfalls-to-avoid"></a>应避免

- 不需要快捷方式，并省略验证/准备/中止试运行迁移。
- 大多数，如果不是全部的潜在问题将呈现在验证/准备/中止步骤期间。

## <a name="migration"></a>迁移

### <a name="technical-considerations-and-tradeoffs"></a>技术注意事项和缺点

现在你已准备因为你已经与你的环境处理通过已知问题。

对于实际的迁移，你可能需要考虑：

1. 规划和计划具有增加优先级的虚拟网络 （迁移的最小单位）。  首先，执行简单的虚拟网络和进度的更复杂的虚拟网络。
2. 大多数客户将具有非生产环境和生产环境。  上次计划的生成。
3. **（可选）**计划维护停机时间，并且保留足够的缓冲区，以防出现意外的问题。
4. 与通信并符合你的支持团队，以防出现问题。

### <a name="patterns-of-success"></a>成功的模式

技术指导原则_实验室测试_应该被视为部分，并将其缓解在实际迁移之前。  具有充分的测试，迁移是实际的非事件。  对于生产环境中，它可能具有更多的支持，如受信任的 Microsoft 合作伙伴或 Microsoft 高级服务很有帮助。

### <a name="pitfalls-to-avoid"></a>应避免

未完全测试可能导致问题，迁移的延迟时间。  

## <a name="beyond-migration"></a>超出迁移

### <a name="technical-considerations-and-tradeoffs"></a>技术注意事项和缺点

既然你已经在 Azure 资源管理器中，最大化平台。  读取[概述 Azure 资源管理器的](../../azure-resource-manager/resource-group-overview.md)要了解有关其他好处。

请考虑以下事项：

- 绑定迁移的过程中其他活动。  大多数客户选择应用程序维护窗口。  如果是这样，你可能想要使用此类停机事件启用加密和迁移到托管磁盘等其他 Azure 资源管理器功能。
- 重新访问技术和商业原因的 Azure 资源管理器中;启用仅在 Azure 资源管理器可用不适用于你的环境的其他服务。
- 更新你的环境与 PaaS 服务。

### <a name="patterns-of-success"></a>成功的模式

是你现在想要启用 Azure 资源管理器中哪些服务上目的性。  许多客户查找下面系列令人瞩目的其 Azure 环境：

- [基于角色的访问控制](../../azure-resource-manager/resource-group-overview.md#access-control)。
- [更容易和更容易控制部署的 azure 资源管理器模板](../../azure-resource-manager/resource-group-overview.md#template-deployment)。
- [标记](../../azure-resource-manager/resource-group-using-tags.md)。
- [活动控件](../../azure-resource-manager/resource-group-audit.md)
- [资源策略](../../azure-resource-manager/resource-manager-policy.md)

### <a name="pitfalls-to-avoid"></a>应避免

请记住启动此 Classic 到 Azure 资源管理器迁移旅程的原因。  原始的业务原因了哪些内容？ 未实现的业务原因？


## <a name="next-steps"></a>后续步骤

* [平台支持的迁移的 IaaS 资源的经典到 Azure 资源管理器概述](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [技术深入了解如何在平台支持迁移从经典到 Azure 资源管理器](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [使用 PowerShell 将从经典 IaaS 资源迁移到 Azure 资源管理器](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [使用 CLI 将从经典 IaaS 资源迁移到 Azure 资源管理器](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [社区工具，可迁移的 IaaS 资源的经典到 Azure 资源管理器帮助](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [查看最常见的迁移错误](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [查看有关迁移 IaaS 资源从经典到 Azure 资源管理器中的最常见问题](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
