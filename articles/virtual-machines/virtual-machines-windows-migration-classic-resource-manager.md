<!-- not suitable for Mooncake -->

<properties
	pageTitle="平台支持的从经典部署模型到 Azure Resource Manager 的 IaaS 资源迁移 | Azure"
	description="本文逐步讲解如何对资源进行平台支持的从经典部署模型到 Azure Resource Manager 的迁移"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="mahthi"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.date="05/04/2016"
	wacn.date=""/>

# 平台支持的从经典部署模型到 Azure Resource Manager 的 IaaS 资源迁移

自从我们宣布了在 Azure Resource Manager 下面支持虚拟机之后，时间已过了将近一年。可以在此处详细了解这段时间内的进展和它支持的其他功能。此外，我们还提供了指导来告诉你如何进行最佳连接，以及如何使用虚拟网络的站点到站点网关在订阅中并置两个部署模型的资源。在本文中，我们将介绍如何将服务架构 (IaaS) 资源从经典部署模型迁移到 Resource Manager。

## 迁移目标

使用发布的新模型，你可以在资源组中部署、管理和监视相关的服务。Resource Manager 除了可让你通过模板部署复杂的应用程序之外，还可使用 VM 扩展来配置虚拟机，并且纳入了访问管理和标记。它还可以将虚拟机的可缩放并行部署包含在可用性集内。此外，新模型还针对计算、网络和存储单独提供生命周期管理。最后，将重点介绍为了按默认启用安全性而要在虚拟网络中实施虚拟机的做法。

在 Azure Resource Manager 之下，针对来自经典部署模型的几乎所有功能，均提供计算、网络和存储支持。由于 Azure Resource Manager 具有此新功能，并且部署基础也在不断发展，因此我们希望客户能够迁移经典部署模型中的现有部署。

## 自动化与工具在迁移之后的变化

在将资源从经典模型迁移到 Resource Manager 模型的过程中，必须更新现有的自动化或工具，以确保其在迁移之后仍可继续运行。

## 将 IaaS 资源从经典模型迁移到 Resource Manager 的意义

在详细了解详细信息之前，我们想要先简短地说明 IaaS 资源的数据平面和管理平面操作之间的差异。了解这些差异非常重要，因为这可以说明我们打算如何支持迁移。

- 管理平面描述进入管理平面或 API 以修改资源的调用。例如，创建 VM、重启 VM 以及将虚拟网络更新成新子网等操作均可管理正在运行的资源。它们并不直接影响与实例之间的连接。
- 数据平面（应用程序）描述应用程序本身的运行时，并涉及与不通过 Azure API 的实例的交互。访问网站或从运行中的 SQL Server 实例或 MongoDB 服务器拉取数据，全都被视为数据平面或应用程序交互。从存储帐户复制 Blob，以及访问公共 IP 地址以通过 RDP 或 SSH 连接到虚拟机也属于数据平面。这些操作可让应用程序继续跨计算、网络和存储运行。

>[AZURE.NOTE] 在某些迁移方案中，我们会停止、解除分配并重新启动你的虚拟机。这会造成短暂的数据平面停机。

## 支持的迁移范围

有三个迁移范围，其主要目标是计算、网络和存储。

### 迁移（不在虚拟网络中的）虚拟机

在 Resource Manager 部署模型中，我们默认在应用程序中实施安全性。在 Resource Manager 模型中，所有 VM 都必须位于虚拟网络内。因此，在迁移过程中，我们将重新启动（`Stop`、`Deallocate`并`Start`）VM。你有两个虚拟网络选项：

- 你可以请求平台创建新的虚拟网络，并将虚拟机迁移到新的虚拟网络。
- 你可以将虚拟机迁移到 Resource Manager 中的现有虚拟网络。

>[AZURE.NOTE] 在此迁移范围内，迁移期间可能有一段时间不允许进行管理平面操作和数据平面操作。

### 迁移（虚拟网络中的）虚拟机

在此范围内，针对大多数 VM 配置，我们只在经典部署模型和 Resource Manager 部署模型之间迁移元数据。基础 VM 在相同硬件、相同网络上，使用相同的存储来运行。因此，当我们提到将元数据从经典部署模型迁移到 Resource Manager 时，迁移期间可能有一段时间不允许进行管理平面操作。不过，数据平面将继续运行。也就是说，在 VM（经典）之上运行的应用程序不会在迁移期间造成停机。

目前，以下配置还不受支持。如果我们在将来添加对这些配置的支持，可能会造成这一配置中的某些 VM 停机（将经历停止、解除分配并重新启动 VM 操作的过程）。

-	在单个云服务中有一个以上的可用性集。
-	在单个云服务中有一个或多个可用性集和不在可用性集中的 VM。

>[AZURE.NOTE] 在此迁移范围内，迁移期间可能有一段时间不允许进行管理平面操作。针对上述某些配置，这会造成数据平面停机。

### 存储帐户迁移

为了让迁移顺畅进行，我们已启用在经典存储帐户中部署 Resource Manager VM 的功能。通过此功能，就可以并且应该迁移计算和网络资源，而不必受到存储帐户的约束。迁移虚拟机和虚拟网络后，你将需要迁移存储帐户才能完成迁移过程。

>[AZURE.NOTE] Resource Manager 部署模型没有经典映像和磁盘的概念。迁移存储帐户时，这些帐户不在 Resource Manager 堆栈中可见，但后备 VHD 将保留在存储帐户中。

## 不支持的功能和配置

目前我们不支持某些功能和配置。下列各节介绍了我们对这些功能和配置的相关建议。

### 不支持的功能

目前不支持以下功能。你可以选择删除这些设置、迁移 VM，然后在 Resource Manager 部署模型中重新启用这些设置。

资源提供程序 | 功能
---------- | ------------
计算 | 不关联的虚拟机磁盘。
计算 | 虚拟机映像。
网络 | 不关联的保留 IP（如果未附加到 VM）。支持已附加到 VM 的保留 IP。
网络 | 不关联的网络安全组（如果未附加到虚拟网络或网络接口）。支持虚拟网络所引用的 NSG。
网络 | 终结点 ACL。
网络 | 虚拟网络网关（站点到站点、ExpressRoute、点到站点）。

### 不支持的配置

目前不支持以下配置。

服务 | 配置 | 建议
---------- | ------------ | ------------
资源管理器 | 经典资源的基于角色的访问控制 (RBAC) | 由于资源的 URI 在迁移后会进行修改，因此建议你规划需要在迁移后进行的 RBAC 策略更新。
计算 | 与 VM 关联的多个子网 | 应该将子网配置更新为只引用子网。
计算 | 属于虚拟网络，但未分配显式子网的虚拟机 | 你可以选择性地删除 VM。
计算 | 具有警报、自动缩放策略的虚拟机 | 目前，迁移可以进行，但这些设置将被丢弃。因此强烈建议你在进行迁移之前先评估你的环境。或者，也可以在迁移完成之后重新配置警报设置。
计算 | XML VM 扩展（Visual Studio 调试器、Web 部署和远程调试） | 此操作不受支持。建议先从虚拟机中删除这些扩展，然后继续迁移。
计算 | 使用高级存储启动诊断 | 在继续执行迁移之前，请为 VM 禁用启动诊断功能。在迁移完成之后，可以在 Resource Manager 堆栈中重新启用启动诊断。此外，应删除正用于屏幕截图和串行日志的 blob，以便不会再为这些 blob 向你收费。
计算 | 包含 Web 角色/辅助角色的云服务 | 目前不支持。
网络 | 包含虚拟机和 Web 角色/辅助角色的虚拟网络 | 目前不支持。
Azure | 包含 Azure 环境的虚拟网络 | 目前不支持。
Azure HDInsight | 包含 HDInsight 服务的虚拟网络 | 目前不支持。
Microsoft Dynamics Lifecycle Services | 包含由 Dynamics Lifecycle Services 管理的虚拟机的虚拟网络 | 目前不支持。

## 迁移体验

在开始迁移体验之前，强烈建议遵循以下步骤：

- 确保你要迁移的资源未使用任何不受支持的功能或配置。在大多数情况下，平台将检测这些问题，并引发错误。
- 如果你有不在虚拟网络中的 VM，平台将在准备操作期间停止这些 VM 并解除其分配。因此，如果不想丢失公共 IP 地址，请先保留 IP 地址再触发准备操作。但是，如果 VM 位于虚拟网络中，则不会停止和解除分配。
- 规划在非工作时间迁移，以便应对迁移期间可能发生的任何意外失败。
- 使用 PowerShell、命令行接口 (CLI) 命令或 REST API 下载当前的 VM 配置，以便能够在完成准备步骤之后轻松进行验证。
- 先更新用于处理 Resource Manager 部署模型的自动化/操作化脚本，再开始迁移。也可以选择在资源处于准备就绪状态时执行 GET 操作。
- 在迁移完成之后，评估经典 IaaS 资源上配置的 RBAC 策略并准备好计划。

迁移工作流如下所示

![显示迁移工作流的屏幕截图](./media/virtual-machines-windows-migration-classic-resource-manager/migration-workflow.png)

>[AZURE.NOTE] 下列各节描述的所有操作都是幂等的。如果你遇到功能不受支持或配置错误以外的问题，建议你重试准备、中止或提交操作。然后平台将重试操作。

### 验证

验证操作是迁移过程中的第一个步骤。此步骤的目的是在后台对进行迁移的资源执行数据分析，并在资源能够进行迁移时返回成功/失败。

你将选择要验证以进行迁移的虚拟网络或托管服务（如果它不是虚拟网络）。

* 如果资源无法迁移，平台将列出不支持迁移该资源的所有原因。

### 准备

准备操作是迁移过程中的第二个步骤。此步骤的目标是要模拟将 IaaS 资源从经典资源转换为 Resource Manager 资源的过程，并以并排方式让此转换过程直观可见。

你将选择要准备进行迁移的虚拟网络或托管服务（如果它不是虚拟网络）。

* 如果资源无法迁移，平台将列出停止迁移过程，并列出准备操作失败的原因。
* 如果资源可迁移，平台将先锁定进行迁移的资源的管理平面操作。例如，无法将数据磁盘添加到进行迁移的 VM。

然后，平台开始将迁移中资源的元数据从经典模型迁移到 Resource Manager。

准备操作完成之后，你可以选择在经典模型和 Resource Manager 中将资源可视化。对于经典部署模型中的每个云服务，我们将创建模式为 `cloud-service-name>-migrated` 的资源组名称。

>[AZURE.NOTE] 不属于经典虚拟网络的虚拟机将在此迁移阶段中停止解除分配。

### 检查（手动或通过脚本）

在检查步骤中，你可以选择使用前面下载的配置来验证迁移是否正常。或者，你也可以登录到门户并抽查属性和资源，来验证元数据的迁移是否正常。

如果你要迁移虚拟网络，大多数虚拟机配置不会重新启动。对于这些 VM 上的应用程序，你可以验证应用程序是否仍已启动并在运行。

可以测试监视/自动化和操作脚本，以查看 VM 是否按预期运行，以及更新后的脚本是否正常运行。请注意，仅当资源处于准备就绪状态时，才支持 GET 操作。

你可以慢慢考虑是否要提交迁移，因为系统对此并没有时间限制。可以在此状态下停留任何时间。但请注意，这些资源的管理平面将遭到锁定，直到中止或提交为止。

如果你发现任何问题，始终可以中止迁移，并返回到经典部署模型。在你返回后，我们将打开资源的管理平面操作，使你可以继续在经典部署模型中对这些 VM 执行正常操作。

### 中止

中止是可选步骤，可让你将更改还原为经典部署模型，并停止迁移。

>[AZURE.NOTE] 触发提交操作后，就无法执行此操作。

### 提交

完成验证之后，就可以提交迁移。资源将不会再出现在经典部署模型中，而只有在 Resource Manager 部署模型中才能使用这些资源。这也意味着只能在新门户中管理迁移的资源。

>[AZURE.NOTE] 这是幂等操作。如果此操作失败，建议你重试几次。如果一直失败，请创建支持票证，或在我们的 [VM 论坛](https://social.msdn.microsoft.com/Forums/azure/zh-cn/home?forum=WAVirtualMachinesforWindows)上创建标记为 ClassicIaaSMigration 的论坛贴子。

## 常见问题

**此迁移计划是否影响 Azure 虚拟机上运行的任何现有服务或应用程序？**

否。VM（经典）是公开上市的完全受支持的服务。你可以继续使用这些资源来拓展你在 Azure 上的足迹。

**如果我近期不打算迁移，我的 VM 会出现什么情况？**

我们近期不会淘汰现有的经典 API 和资源模型。我们想要通过 Resource Manager 部署模型中提供的高级功能，让迁移变得简单。强烈建议你查看 Resource Manager 下的 IaaS 所包含的[一些改进](/documentation/articles/virtual-machines-windows-compare-deployment-models/)。

**对于我现有的工具而言，此迁移计划有何意义？**

将工具更新为 Resource Manager 部署模型，是必须在迁移计划中考虑的最重要的更改之一。

**管理平面的停机时间持续多久？**

这取决于迁移的资源数量。对于较小型部署（几十个 VM），整个迁移过程应该不超过一小时。如果是大规模部署（数百个 VM），迁移过程可能需要花费几个小时。

**在 Resource Manager 中提交迁移的资源之后，是否还可以回滚？**

只要资源处于准备就绪状态，就可以中止迁移。但是，在通过提交操作成功迁移资源之后，就不支持回滚。

**提交操作失败时，是否可以回滚迁移？**

如果提交操作失败，就无法中止迁移。包括提交操作在内的所有迁移操作都是幂等的。因此，建议在片刻之后重试操作。如果仍遇到错误，请创建支持票证，或在我们的 [VM 论坛](https://social.msdn.microsoft.com/Forums/azure/zh-cn/home?forum=WAVirtualMachinesforWindows)上创建标记为 ClassicIaaSMigration 的论坛贴子。

**如果我必须使用 Resource Manager 下的 IaaS，是否必须购买其他 ExpressRoute 线路？**

不需要。我们最近已启用[跨经典模型和 Resource Manager 共存的 ExpressRoute 线路](/documentation/articles/expressroute-howto-coexist-resource-manager/)。如果你已有 ExpressRoute 线路，则不需要购买新的线路。

**如果我已经为经典 IaaS 资源配置基于角色的访问控制策略，该怎么办？**

在迁移期间，资源从经典资源转换为 Resource Manager 资源。因此，建议计划需要在迁移之后进行的 RBAC 策略更新。

**如果我目前正在使用 Azure Site Recovery 或 Azure 备份，该怎么办？**

Resource Manager 下的 VM 最近已添加对 Azure Site Recovery 和备份的支持。我们正在想办法一并启用支持将 VM 迁移到 Resource Manager 的功能。目前，如果你正在使用这些功能，建议不要运行迁移。

**我是否可以验证订阅或资源，以查看其是否能够迁移？**

是的。在平台支持的迁移选项中，准备迁移的第一个步骤，就是验证资源是否能够进行迁移。如果验证操作失败，你将收到包含无法完成迁移的所有原因的所有消息。

**如果我在准备要迁移的 IaaS 资源时遇到配额错误，会发生什么情况？**

建议你中止迁移，然后记录支持请求，以在要迁移 VM 的区域中增加配额。配额请求经过批准后，可以重新开始执行迁移步骤。

**如何报告问题？**

请在我们的 [VM 论坛](https://social.msdn.microsoft.com/Forums/azure/zh-cn/home?forum=WAVirtualMachinesforWindows)上，使用关键字 ClassicIaaSMigration 发布有关迁移的问题和疑惑。建议你将所有问题都发布在此论坛上。如果你有支持协定，也欢迎你记录支持票证。

**如果我不喜欢平台在迁移期间选择的资源名称，该怎么做？**

在经典部署模型中显式提供名称的所有资源都会在迁移期间得到保留。在某些情况下，会创建新资源。例如：为每个 VM 创建网络接口。目前，我们不支持控制迁移期间所创建的新资源名称。请在 [Azure 反馈论坛](http://feedback.azure.com)上针对此功能进行投票。

**我收到一条消息，指出“VM 报告总体代理状态为‘未就绪’。因此，此 VM 无法迁移。请确保 VM 代理报告总体代理状态为‘就绪’”或“VM 包含 VM未报告其状态的扩展。因此，此 VM 无法迁移。”**

当 VM 未建立到 Internet 的出站连接时，将收到此消息。VM 代理使用出站连接访问 Azure 存储帐户以每隔 5 分钟更新代理状态。

## 后续步骤
你已经了解经典 IaaS 资源到 Resource Manager 的迁移，现在可以开始迁移资源。

- [有关平台支持的从经典部署模型到 Azure Resource Manager 的迁移的技术深入探讨](/documentation/articles/virtual-machines-windows-migration-classic-resource-manager-deep-dive/)
- [使用 PowerShell 将 IaaS 资源从经典部署模型迁移到 Azure Resource Manager](/documentation/articles/virtual-machines-windows-ps-migration-classic-resource-manager/)
- [使用 CLI 将 IaaS 资源从经典部署模型迁移到 Azure Resource Manager](/documentation/articles/virtual-machines-linux-cli-migration-classic-resource-manager/)
- [使用社区 PowerShell 脚本将经典虚拟机克隆到 Azure Resource Manager](/documentation/articles/virtual-machines-windows-migration-scripts/)

<!---HONumber=Mooncake_0808_2016-->