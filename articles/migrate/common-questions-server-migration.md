---
title: 有关 Azure Migrate 服务器迁移的常见问题
description: 获取有关使用 Azure Migrate 服务器迁移迁移计算机的常见问题的解答。
ms.topic: conceptual
ms.date: 08/28/2020
ms.openlocfilehash: 80334bb2f0d6c0284c9031a99c0eb469b348873d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91275534"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Azure Migrate Server 迁移：常见问题

本文解答了有关 Azure Migrate： Server 迁移工具的常见问题。 如果你有其他问题，请查看以下资源：

- 有关 Azure Migrate 的[一般问题](resources-faq.md)
- 关于[Azure Migrate 设备](common-questions-appliance.md)的问题
- 有关[发现、评估和依赖项可视化](common-questions-discovery-assessment.md)的问题
- 在[Azure Migrate 论坛](https://aka.ms/AzureMigrateForum)中获取问题答案

## <a name="does-azure-migrate-convert-uefi-based-machines-to-bios-based-machines-and-migrate-them-to-azure-as-azure-generation-1-vms"></a>Azure Migrate 是否将基于 UEFI 的计算机转换为基于 BIOS 的计算机并将其作为 Azure 第1代 Vm 迁移到 Azure？
Azure Migrate：服务器迁移工具将所有基于 UEFI 的计算机迁移到 azure，作为 Azure 第2代 Vm。 我们不再支持将基于 UEFI 的 Vm 转换为基于 BIOS 的 Vm。 请注意，所有基于 BIOS 的计算机仅迁移到 Azure 作为 Azure 第1代 Vm。

## <a name="how-can-i-migrate-uefi-based-machines-to-azure-as-azure-generation-1-vms"></a>如何将基于 UEFI 的计算机迁移到 Azure 作为 Azure 第1代 Vm？
Azure Migrate：服务器迁移工具将基于 UEFI 的计算机迁移到 Azure，作为 Azure 第2代 Vm。 如果要将其迁移到 Azure 第1代 Vm，请在开始复制前将启动类型转换为 BIOS，然后使用 Azure Migrate： Server 迁移工具迁移到 Azure。
 
## <a name="which-operating-systems-are-supported-for-migration-of-uefi-based-machines-to-azure"></a>哪些操作系统支持将基于 UEFI 的计算机迁移到 Azure？

| **基于 UEFI 的计算机支持的操作系统** | **无代理 VMware 到 Azure**                                                                                                             | **无代理 Hyper-v 到 Azure** | **基于代理的 VMware、物理和其他云到 Azure** |
| ------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------ | ---------------------------------------------------------- |
| Windows Server 2019、2016、2012 R2、201                 | Y                                                                                                                                         | Y                              | Y                                                          |
| Windows 10 专业版、Windows 10 企业版                   | Y                                                                                                                                         | Y                              | Y                                                          |
| SUSE Linux Enterprise Server 15 SP1                     | Y                                                                                                                                         | Y                              | Y                                                          |
| SUSE Linux Enterprise Server 12 SP4                     | Y                                                                                                                                         | Y                              | Y                                                          |
| Ubuntu Server 16.04、18.04、19.04、19.10                | Y                                                                                                                                         | Y                              | Y                                                          |
| RHEL 8.1、8.0、7.8、7.7、7.6、7.5、7.4、7.0、1。x        | Y<br>                 _RHEL 8.x 需要 [手动准备](https://go.microsoft.com/fwlink/?linkid=2143939)_   | Y                              | Y                                                          |
| O o 8.1、8.0、7.7、7.6、7.5、7.4、1。x               | Y<br>_% O o 8. x 需要 [手动准备](https://go.microsoft.com/fwlink/?linkid=2143939)_ | Y                              | Y                                                          |
| Oracle Linux 7.7、7.7-CI                                |  Y                                                                                                                                        | Y                              | Y                                                          |

## <a name="can-i-use-the-recovery-services-vault-created-by-azure-migrate-for-disaster-recovery-scenarios"></a>能否使用由 Azure Migrate 创建的恢复服务保管库用于灾难恢复方案？
建议不要使用由 Azure Migrate 创建的恢复服务保管库来实现灾难恢复方案。 这样做可能会导致在 Azure Migrate 中开始复制失败。 

## <a name="where-should-i-install-the-replication-appliance-for-agent-based-migrations"></a>应将复制设备安装在何处才能进行基于代理的迁移？

应将复制设备安装在专用计算机上。 复制设备不应安装在要复制的源计算机上，也不应安装在以前可能已安装的 Azure Migrate 发现和评估设备上。 请按照 [教程](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines) 了解更多详细信息。

## <a name="how-can-i-migrate-my-aws-ec2-instances-to-azure"></a>如何将 AWS EC2 实例迁移到 Azure？

查看 [本文](https://docs.microsoft.com/azure/migrate/tutorial-migrate-aws-virtual-machines) ，了解如何将 AWS EC2 实例进行评估并将其迁移到 Azure。

## <a name="can-i-migrate-aws-vms-running-amazon-linux-operating-system"></a>能否迁移运行 Amazon Linux 操作系统的 AWS Vm？

运行 Amazon Linux 的 Vm 不能按原样迁移，因为 Amazon Linux OS 仅在 AWS 上受支持。
若要迁移在 Amazon Linux 上运行的工作负载，可以在 Azure 中启动 CentOS/RHEL VM，并使用相关的工作负载迁移方法迁移在 AWS Linux 计算机上运行的工作负载。 例如，根据工作负载，通过特定于工作负载的工具来帮助迁移（例如使用数据库工具迁移数据库，或者使用部署工具迁移 Web 服务器）。

## <a name="what-geographies-are-supported-for-migration-with-azure-migrate"></a>哪些地理区域支持 Azure Migrate 迁移？

查看[公有云](migrate-support-matrix.md#supported-geographies-public-cloud)和[政府云](migrate-support-matrix.md#supported-geographies-azure-government)支持的地理位置。

## <a name="can-we-use-the-same-azure-migrate-project-to-migrate-to-multiple-regions"></a>能否使用同一个 Azure Migrate 项目迁移到多个区域？

虽然您可以为 Azure Migrate 项目中的多个区域创建评估，但一个 Azure Migrate 项目只能用于将服务器迁移到一个 Azure 区域。 你可以为需要迁移到的每个区域创建其他 Azure Migrate 项目。

- 对于无代理 VMware 迁移，一旦启用第一次复制，目标区域就会锁定。
- 对于基于代理的迁移 (VMware、物理服务器和其他云) 的服务器，在设置复制设备时，在门户中单击 "创建资源" 按钮后，将锁定目标区域。
- 对于无代理 Hyper-v 迁移，在设置 Hyper-v 复制提供程序时，一旦单击门户上的 "创建资源" 按钮，就会锁定目标区域。

## <a name="can-we-use-the-same-azure-migrate-project-to-migrate-to-multiple-subscriptions"></a>能否使用同一个 Azure Migrate 项目迁移到多个订阅？ 

是的，可以迁移到 Azure Migrate 项目的同一目标区域中的多个订阅。 为计算机或一组计算机启用复制时，可以选择目标订阅。 对于无代理 VMware 迁移，目标区域是锁定的第一次复制，在复制设备和 Hyper-v 提供程序安装期间，分别用于基于代理的迁移和无代理 Hyper-v 迁移。

## <a name="what-are-the-migration-options-in-azure-migrate-server-migration"></a>Azure Migrate 中的迁移选项：服务器迁移？

Azure Migrate：服务器迁移工具提供了两种方法来执行源服务器/Vm 到 Azure 的迁移-无代理迁移和基于代理的迁移。

无论选择哪种迁移选项，使用 Azure 迁移迁移服务器的第一步：服务器迁移是为服务器启用复制。 这会执行将 VM/服务器数据备份到 Azure 的初始复制。 初始复制完成后，将建立一个正在进行的复制 (正在进行的增量同步) ，以将增量数据迁移到 Azure。 操作达到增量同步阶段后，可以随时选择迁移到 Azure。  

在决定迁移选项时，请注意以下事项。

**无代理迁移** 不需要在要迁移的源 vm/服务器上部署任何软件 (代理) 。 无代理选项通过集成虚拟化提供程序提供的功能来协调复制。
无代理复制选项适用于 [VMware vm](https://docs.microsoft.com/azure/migrate/tutorial-migrate-vmware) 和 [hyper-v vm](https://docs.microsoft.com/azure/migrate/tutorial-migrate-hyper-v)。

**基于代理的迁移** 需要在要迁移的源 vm/计算机上安装 Azure Migrate 软件 (代理) 。 基于代理的选项不依赖于用于复制功能的虚拟化平台，因此可用于运行 x86/x64 体系结构的任何服务器和基于代理的复制方法所支持的操作系统版本。

基于代理的迁移选项可用于 [VMware vm](https://docs.microsoft.com/azure/migrate/tutorial-migrate-vmware-agent)、 [hyper-v vm](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines)、 [物理服务器](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines)、 [运行在 AWS](https://docs.microsoft.com/azure/migrate/tutorial-migrate-aws-virtual-machines)上的 vm、在 GCP 上运行的 vm，或在其他虚拟化提供程序上运行的 vm。 基于代理的迁移将计算机作为物理服务器对待，以便进行迁移。

尽管无代理迁移对 VMWare 和 Hyper-v) 支持 (方案的基于代理的复制选项提供了额外的便利性和简单性，但你可能需要考虑在以下用例中使用基于代理的方案：

- IOPS 限制环境：无代理复制使用快照并使用存储 IOPS/带宽。 如果对环境中的存储/IOPS 有限制，则建议采用基于代理的迁移方法。
- 如果没有 vCenter Server，则可以将 VMware Vm 视为物理服务器，并使用基于代理的迁移工作流。

若要了解详细信息，请参阅此 [文](https://docs.microsoft.com/azure/migrate/server-migrate-overview) ，比较 VMware 迁移的迁移选项。

## <a name="how-does-agentless-migration-work"></a>无代理迁移是如何工作的？

Azure Migrate：服务器迁移为 VMware 虚拟机和运行 Windows 或 Linux 的 Hyper-v 虚拟机的迁移提供了无代理复制选项。 该工具还为 Windows 和 Linux 服务器提供了一个基于代理的附加复制选项，这些选项可用于迁移物理服务器，以及 VMware、Hyper-v、AWS、GCP 等上的 x86/x64 虚拟机。基于代理的复制选项需要在要迁移的服务器/虚拟机上安装代理软件，而在无代理选项中，无需在虚拟机本身上安装任何软件，因此可以在基于代理的复制选项上提供其他便利和简洁性。

无代理复制选项的工作方式是使用虚拟化提供商提供的机制 (VMware、Hyper-v) 。 对于 VMware 虚拟机，无代理复制机制使用 VMware 快照和 VMware 更改的块跟踪技术从虚拟机磁盘复制数据。 此机制类似于许多备份产品使用的机制。 对于 Hyper-v 虚拟机，无代理复制机制使用 VM 快照和 Hyper-v 副本的更改跟踪功能从虚拟机磁盘复制数据。

为虚拟机配置复制时，它首先会经历初始复制阶段。 在初始复制期间，会创建一个 VM 快照，并将快照磁盘中的数据的完整副本复制到订阅中的托管磁盘。 VM 的初始复制完成后，复制过程会转换为增量复制 (增量复制) 阶段。 在增量复制阶段，会定期复制自上次完成的复制周期以来发生的数据更改，并将其应用于副本托管磁盘，从而使复制与 VM 上发生的更改保持同步。 对于 VMware 虚拟机，VMware 更改了阻止跟踪技术，用于跟踪复制循环之间的更改。 在复制周期开始时，将使用 VM 快照并更改块跟踪，以获取当前快照与上次成功复制快照之间的更改。 这样，就只需要复制自上次完成的复制周期以来发生更改的数据，以便保持同步 VM 的复制。在每个复制周期结束时，将释放快照，并对虚拟机执行快照合并。 同样，如果是 Hyper-v 虚拟机，则使用 Hyper-v 副本更改跟踪引擎跟踪连续复制循环之间的更改。
在复制虚拟机上执行迁移操作时，可以选择关闭本地虚拟机并执行一个最终增量复制，以确保零数据丢失。 执行 "迁移" 选项时，会使用与虚拟机对应的副本托管磁盘在 Azure 中创建虚拟机。

若要开始，请参阅 [VMware 无代理迁移](https://docs.microsoft.com/azure/migrate/tutorial-migrate-vmware) 和 [hyper-v 无代理迁移](https://docs.microsoft.com/azure/migrate/tutorial-migrate-hyper-v) 教程。

## <a name="how-does-agent-based-migration-work"></a>基于代理的迁移如何工作？

除了适用于 VMware 虚拟机和 Hyper-v 虚拟机的无代理迁移选项，服务器迁移工具还提供基于代理的迁移选项，以迁移物理服务器上运行的 Windows 和 Linux 服务器，或在 VMware、Hyper-v、AWS、Google Cloud Platform 等上作为 x86/x64 虚拟机运行。

基于代理的迁移方法使用所迁移的服务器上安装的代理软件将服务器数据复制到 Azure。 复制过程使用卸载体系结构，在该体系结构中，代理将复制数据中继到称为复制设备或配置服务器 (的专用复制服务器或) 的横向扩展进程服务器。 [了解](https://docs.microsoft.com/azure/migrate/agent-based-migration-architecture) 有关基于代理的迁移选项如何工作的详细信息。 

注意：复制设备不同于 Azure Migrate 发现设备，必须安装在单独的/专用计算机上。

## <a name="how-do-i-gauge-the-bandwidth-requirement-for-my-migrations"></a>如何实现度量迁移的带宽需求？

将数据复制到 Azure 的带宽取决于一系列因素，是本地 Azure Migrate 设备读取数据和将数据复制到 Azure 的速度。 复制有两个阶段：初始复制和增量复制。

VM 的复制开始时，将发生初始复制循环，其中复制了磁盘的完整副本。 初始复制完成后，将定期计划增量复制循环 (增量周期) ，以传输自上一次复制循环以来发生的任何更改。

### <a name="agentless-vmware-vm-migration"></a>无代理 VMware VM 迁移

你可以根据需要移动的数据量以及要在其中完成初始复制的时间来计算带宽要求 (理想情况下，你希望初始复制在实际迁移窗口之前至少3-4 天完成，使你有足够的时间在窗口) 之前执行测试迁移，并使停机时间保持最小。

你可以使用以下公式估计无代理 VMware VM 迁移所需的带宽或时间：

完成初始复制所用的时间 = { (大小或使用大小（如果可用）) * 0.7 (假设有30% 的压缩平均值–保守估计) }/bandwidth 可用于复制。

### <a name="agent-based-vmware-vm-migration"></a>基于代理的 VMware VM 迁移

对于基于代理的复制方法，部署规划器可以帮助分析数据改动的环境，并帮助预测所需的带宽要求。 若要了解详细信息，请参阅此 [文](https://docs.microsoft.com/azure/migrate/agent-based-migration-architecture#plan-vmware-deployment)。 

## <a name="how-do-i-throttle-replication-in-using-azure-migrate-appliance-for-agentless-vmware-replication"></a>使用 Azure Migrate 设备进行无代理 VMware 复制的如何实现限制复制？  

可以使用 New-netqospolicy 进行限制。 例如：

要在 New-netqospolicy 中使用的 AppNamePrefix 为 "GatewayWindowsService.exe"。 可以通过创建如下策略，在 Azure Migrate 设备上创建策略，以限制设备的复制流量：

New-NetQosPolicy-Name "ThrottleReplication"-AppPathNameMatchCondition "GatewayWindowsService.exe"-ThrottleRateActionBitsPerSecond 1 MB

## <a name="how-is-the-data-transmitted-from-on-prem-environment-to-azure-is-it-encrypted-before-transmission"></a>如何将数据从本地环境传输到 Azure？ 传输前是否对其进行加密？

无代理复制情况下的 Azure Migrate 设备将压缩数据，并在上传之前加密。 数据通过 https 上的安全通信通道传输并使用 TLS 1.2 或更高版本。 此外，在将数据保存到云 (静态静态) 时，Azure 存储会自动加密数据。  

## <a name="how-does-churn-rate-affect-agentless-replication"></a>改动速度如何影响无代理复制？

由于无代理复制会在数据中折叠，因此 *改动模式* 比 *改动速度*更重要。 再次编写文件时，速率不会产生很大的影响。 但是，每个其他扇区的写入模式在下一个周期中会导致较高的变动。 由于我们最大限度地减少了传输的数据量，因此，我们允许数据在计划下一个周期之前折叠尽可能多。

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>复制循环计划的频率如何？

计划下一个复制周期的公式 (上一个周期时间/2) 或一小时，以较大者为准。

例如，如果某个 VM 的增量周期使用四个小时，则将在两个小时内计划下一个周期，而不是在下一个小时。 此过程在初始复制之后（在立即计划第一个增量周期时）是不同的。

## <a name="how-do-i-migrate-windows-server-2003-running-on-vmwarehyper-v-to-azure"></a>如何实现将 VMware/Hyper-v 上运行的 Windows Server 2003 迁移到 Azure？

[Windows Server 2003 扩展支持](https://go.microsoft.com/fwlink/?linkid=2140400) 于2015年7月14日结束。  Azure 支持团队将继续帮助排查在 Azure 上运行 Windows Server 2003 时遇到的问题。 但是，这种支持仅限于不需要操作系统级故障排除或修补程序的问题。
建议将应用程序迁移到运行较新版本 Windows Server 的 Azure 实例，以确保有效地利用 Azure 云的灵活性和可靠性。

但是，如果你仍选择将 Windows Server 2003 迁移到 Azure，则可以使用 Azure Migrate： Server 迁移工具（如果你的 Windows Server 是在 VMware 或 Hyper-v 上运行的 VM）查看本文，为 [迁移准备 Windows server 2003 计算机](https://go.microsoft.com/fwlink/?linkid=2140302)。

## <a name="what-is-the-difference-between-the-test-migration-and-migrate-operations"></a>测试迁移与迁移操作之间有何区别？

通过测试迁移，可在实际迁移之前测试和验证迁移。 测试迁移的工作方式是在 Azure 中创建在沙盒环境中复制 Vm 的测试副本。 沙盒环境由指定的测试虚拟网络 demarcated。 测试迁移操作无中断性，应用程序在源位置继续运行，同时让你在隔离的沙箱环境中对克隆的副本执行测试。 你可以根据需要执行多个测试来验证迁移、执行应用测试，并在实际迁移之前解决任何问题。

## <a name="will-windows-server-2008-and-2008-r2-be-supported-in-azure-after-migration"></a>迁移后，Azure 中是否支持 Windows Server 2008 和 2008 R2？

你可以将本地 Windows Server 2008 和 2008 R2 服务器迁移到 Azure 虚拟机，并在支持日期结束后三年获取扩展安全更新，而不会在运行虚拟机的成本上收取额外费用。 可以使用 Azure Migrate：服务器迁移工具迁移 Windows Server 2008 和 2008 R2 工作负荷。

## <a name="is-there-a-rollback-option-for-azure-migrate"></a>是否有用于 Azure Migrate 的回滚选项？

你可以使用测试迁移选项来验证 Azure 中的应用程序功能和性能。 可以执行任意数量的测试迁移，并能在通过测试迁移操作建立信心后执行最终迁移。 测试迁移并不会影响本地计算机，它仍可正常运行并继续复制，直到执行实际迁移。 如果在测试迁移 UAT 过程中出现任何错误，则可以选择推迟最终迁移，并使源 VM/服务器运行并复制到 Azure。 解决错误后，你可以重新尝试最终迁移。  
注意：一旦你执行了到 Azure 的最终迁移并且本地源计算机已关闭，你将无法从 Azure 回滚到本地环境。

## <a name="can-i-select-the-virtual-network-and-subnet-to-use-for-test-migrations"></a>能否选择用于测试迁移的虚拟网络和子网？

你可以选择一个虚拟网络用于测试迁移。 子网是根据以下逻辑自动选择的：

- 如果在启用复制的同时将目标子网 (指定为输入，则 Azure Migrate 使用在为测试迁移选择的虚拟网络中具有相同名称的子网进行优先级) 设置。
- 如果找不到具有相同名称的子网，则 Azure Migrate 选择不是网关/应用程序网关/防火墙/堡垒子网按字母顺序使用的第一个子网。

## <a name="why-is-the-test-migration-button-disabled-for-my-server"></a>为什么为服务器禁用了 "测试迁移" 按钮？

在以下情况下，"测试迁移" 按钮可能处于禁用状态：

- 直到 VM 的初始复制 (IR) 完成后，才能开始测试迁移。 在完成 IR 进程之前，将禁用 "测试迁移" 按钮。 VM 处于增量同步阶段后，可以执行测试迁移。
- 如果测试迁移已完成，但未对该虚拟机执行测试迁移清理，则可以禁用该按钮。 执行测试迁移清理，然后重试该操作。

## <a name="what-happens-if-i-dont-clean-up-my-test-migration"></a>如果不清理测试迁移，会发生什么情况？

测试迁移通过使用复制的数据创建测试 Azure VM 来模拟实际迁移。 将使用复制的数据的时间点副本部署到目标资源组，并在使用 "-test" 后缀启用复制)  (选中该选项。 测试迁移用于验证服务器功能，以便最大程度地减少迁移后问题。 如果测试迁移未清理后测试，则测试虚拟机将继续在 Azure 中运行并且会产生费用。 若要清除测试迁移，请前往服务器迁移工具中的 "复制计算机" 视图，并在计算机上使用 "清理测试迁移" 操作。

## <a name="can-i-migrate-active-directory-domain-controllers-using-azure-migrate"></a>能否使用 Azure Migrate 迁移 Active Directory 域控制器？

服务器迁移工具与应用程序无关，适用于大多数应用程序。 使用服务器迁移工具迁移服务器时，安装在服务器上的所有应用程序都将随之迁移。 但对于某些应用程序，除服务器迁移以外的其他迁移方法可能更适合于迁移。  对于 Active Directory，在本地站点连接到 Azure 环境的混合环境中，可以通过在 Azure 中添加其他域控制器并设置 Active Directory 复制来将目录扩展到 Azure。 如果要迁移到 Azure 中的隔离环境，该环境需要其自己的域控制器 (或测试沙盒环境中的应用程序) ，则可以使用服务器迁移工具迁移服务器。

## <a name="what-happens-if-i-dont-stop-replication-after-migration"></a>如果在迁移后不停止复制，会发生什么情况？

当你停止复制时，Azure Migrate： Server 迁移工具将清理为复制创建的订阅中的托管磁盘。 如果在迁移后不停止复制，则会继续产生这些磁盘的费用。 停止复制不会影响附加到已迁移计算机的磁盘。

## <a name="do-i-need-vmware-vcenter-to-migrate-vmware-vms"></a>是否需要 VMware vCenter 才能迁移 VMware Vm？

若要使用基于 VMware 代理或无代理迁移迁移 [Vmware vm](server-migrate-overview.md) ，则必须通过 VCenter Server 管理 vm 所在的 ESXi 主机。 如果没有 vCenter Server，则可以通过将 VMware Vm 作为物理服务器进行迁移来将其迁移。 [了解详细信息](migrate-support-matrix-physical-migration.md)。

## <a name="can-i-upgrade-my-os-while-migrating"></a>是否可以在迁移时升级操作系统？

Azure Migrate：服务器迁移工具当前仅支持类似于类似的迁移。 该工具不支持在迁移过程中升级操作系统版本。 已迁移的计算机将具有与源计算机相同的操作系统。

## <a name="i-deployed-two-or-more-appliances-to-discover-vms-in-my-vcenter-server-however-when-i-try-to-migrate-the-vms-i-only-see-vms-corresponding-to-one-of-the-appliances"></a>我部署了两个 (或多个) 设备，以便在我的 vCenter Server 中发现 Vm。 但是，当我尝试迁移 Vm 时，只会看到对应于其中一个设备的 Vm。

如果设置了多个设备，则要求提供的 vCenter 帐户上的 VM 间没有重叠。 具有此类重叠的发现是不受支持的方案。

## <a name="how-do-i-know-if-my-vm-was-successfully-migrated"></a>如何实现知道我的 VM 是否已成功迁移？

成功迁移 VM/服务器后，可以从 "虚拟机" 页查看和管理 VM。 连接到已迁移的 VM 进行验证。
或者，您可以查看操作的 "作业状态" 以检查迁移是否已成功完成。 如果看到任何错误，请解决这些错误，然后重试迁移操作。

## <a name="can-i-consolidate-multiple-source-vms-into-one-vm-while-migrating"></a>是否可以在迁移时将多个源 Vm 合并到一个 VM？

Azure Migrate server 迁移功能，支持类似于目前的迁移。 在迁移过程中，我们不支持合并服务器或升级操作系统。 

## <a name="how-does-agentless-replication-affect-vmware-servers"></a>无代理复制如何影响 VMware 服务器？

无代理复制会导致对 VMware vCenter 服务器和 VMware ESXi 主机的性能影响。 由于无代理复制使用快照，因此它将消耗存储空间，因此需要某些 IOPS 存储带宽。 如果对环境中的存储或 IOPs 有限制，我们不建议使用无代理复制。


## <a name="next-steps"></a>后续步骤

阅读 [Azure Migrate 概述](migrate-services-overview.md)。
