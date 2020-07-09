---
title: Azure Migrate 服务器评估中的 Azure VM 评估
description: 了解 Azure Migrate 服务器评估中的评估
ms.topic: conceptual
ms.date: 05/27/2020
ms.openlocfilehash: 52cdd6bb9cb062b5c36e10c67524fa4d266ca6e0
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86107995"
---
# <a name="azure-vm-assessments-in-azure-migrate-server-assessment"></a>Azure Migrate 中的 Azure VM 评估：服务器评估

本文概述了[Azure Migrate：服务器评估](migrate-services-overview.md#azure-migrate-server-assessment-tool)工具中的评估。 该工具可评估用于迁移到 Azure 的本地 VMware 虚拟机、Hyper-v Vm 和物理服务器。

## <a name="whats-an-assessment"></a>什么是评估？

使用服务器评估工具的评估可衡量准备情况并估计将本地服务器迁移到 Azure 的影响。

> [!NOTE]
> 在 Azure 政府版中，查看[支持的目标](migrate-support-matrix.md#supported-geographies-azure-government)评估位置。 请注意，评估版中的 VM 大小建议将专用于政府云区域的 VM 系列。 [详细了解](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines)VM 类型。

## <a name="types-of-assessments"></a>评估类型

使用“Azure Migrate: 服务器评估”可以运行两种类型的评估。

**评估类型** | **详细信息**
--- | --- 
Azure VM  | 用于将本地服务器迁移到 Azure 虚拟机的评估。 <br/><br/> 可以使用此评估类型评估本地[VMware vm](how-to-set-up-appliance-vmware.md)、 [hyper-v vm](how-to-set-up-appliance-hyper-v.md)和[物理服务器](how-to-set-up-appliance-physical.md)，以便迁移到 Azure。
**Azure VMware 解决方案 (AVS)** | 用于将本地服务器迁移到[Azure VMware 解决方案（AVS）](../azure-vmware/introduction.md)的评估。 <br/><br/> 可以使用此评估类型评估本地[Vmware vm](how-to-set-up-appliance-vmware.md) ，以便迁移到 Azure VMware 解决方案（AVS）。[了解更多](concepts-azure-vmware-solution-assessment-calculation.md)

使用服务器评估创建的评估是数据的时间点快照。 服务器评估中的 Azure VM 评估提供两个大小调整条件选项：

**评估类型** | **详细信息** | **数据**
--- | --- | ---
**基于性能** | 基于收集的性能数据提出建议的评估 | VM 大小建议基于 CPU 和 RAM 利用率数据。<br/><br/> 磁盘类型建议基于每秒输入/输出操作数（IOPS）和本地磁盘的吞吐量。 磁盘类型为 Azure 标准 HDD、Azure 标准 SSD 和 Azure 高级磁盘。
**按本地** | 不使用性能数据提出建议的评估 | VM 大小建议基于本地 VM 大小。<br/><br> 建议的磁盘类型基于所选的评估存储类型。

## <a name="how-do-i-run-an-assessment"></a>如何实现运行评估？

可以通过多种方式来运行评估。

- 使用轻型 Azure Migrate 设备收集的服务器元数据来评估计算机。 设备会发现本地计算机。 然后，它将计算机元数据和性能数据发送到 Azure Migrate。
- 使用以逗号分隔值（CSV）格式导入的服务器元数据来评估计算机。

## <a name="how-do-i-assess-with-the-appliance"></a>如何实现与设备进行评估？

如果要部署 Azure Migrate 设备来发现本地服务器，请执行以下步骤：

1. 将 Azure 和本地环境设置为使用服务器评估。
1. 对于第一个评估，请创建一个 Azure 项目并向其添加服务器评估工具。
1. 部署轻型 Azure Migrate 设备。 设备持续发现本地计算机，并将计算机元数据和性能数据发送到 Azure Migrate。 将设备部署为 VM 或物理计算机。 无需在要评估的计算机上安装任何内容。

设备开始发现计算机后，你可以将你想要评估的计算机收集到一个组中，并为具有评估类型 " **AZURE VM**" 的组运行评估。

按照我们有关[VMware](tutorial-prepare-vmware.md)、 [hyper-v](tutorial-prepare-hyper-v.md)或[物理服务器](tutorial-prepare-physical.md)的教程操作，尝试这些步骤。

## <a name="how-do-i-assess-with-imported-data"></a>如何实现评估导入的数据？

如果要使用 CSV 文件评估服务器，则无需设备。 请改为执行以下步骤：

1. 设置 Azure 以使用服务器评估。
1. 对于第一个评估，请创建一个 Azure 项目并向其添加服务器评估工具。
1. 下载 CSV 模板并向其中添加服务器数据。
1. 将模板导入到服务器评估中。
1. 发现通过导入添加的服务器，将其收集到一个组中，并运行评估类型为**AZURE VM**的组的评估。

## <a name="what-data-does-the-appliance-collect"></a>设备会收集哪些数据？

如果要使用 Azure Migrate 设备进行评估，请参阅针对[VMware](migrate-appliance.md#collected-data---vmware)和[hyper-v](migrate-appliance.md#collected-data---hyper-v)收集的元数据和性能数据。

## <a name="how-does-the-appliance-calculate-performance-data"></a>设备如何计算性能数据？

如果使用设备进行发现，则它会使用以下步骤收集计算设置的性能数据：

1. 设备将收集实时示例点。

    - **VMware vm**：每隔20秒收集一个示例点。
    - **Hyper-v vm**：每30秒收集一次示例点。
    - **物理服务器**：每五分钟收集一次示例点。

1. 设备将示例点组合起来，每10分钟创建一个数据点。 为了创建数据点，设备从所有示例中选择最大值。 然后，它将数据点发送到 Azure。
1. 服务器评估存储上个月的10分钟数据点。
1. 创建评估时，服务器评估会确定要用于合理精简的相应数据点。 标识基于*性能历史记录*和*百分比利用率*的百分位值。

    - 例如，如果性能历史记录为一周，百分比利用率为95%，则服务器评估会对最后一周的10分钟示例点进行排序。 它将按升序排序，并选取合理精简的第95百分位值。
    - 95% 的值可以确保忽略任何离群值，如果选择了99% 百分点值，则可能会包含这些离群值。
    - 如果要选择该时间段的高峰使用量，而不想错过任何离群值，请选择99% 百分位以实现百分比利用率。

1. 此值与舒适因素相乘，以获取设备收集的这些指标的有效性能利用率数据：

    - CPU 使用率
    - RAM 利用率
    - 磁盘 IOPS （读取和写入）
    - 磁盘吞吐量（读取和写入）
    - 网络吞吐量（传入和传出）

## <a name="how-are-azure-vm-assessments-calculated"></a>如何计算 Azure VM 评估？

服务器评估使用本地计算机的元数据和性能数据来计算评估。 如果部署 Azure Migrate 设备，则评估将使用设备收集的数据。 但如果运行使用 CSV 文件导入的评估，则需要提供用于计算的元数据。

这三个阶段会发生计算：

1. **计算 Azure 就绪**：评估计算机是否适合迁移到 Azure。
1. **计算大小调整建议**：估算计算、存储和网络大小。
1. **计算每月成本**：计算迁移后在 Azure 中运行计算机的估计每月计算和存储成本。

计算按前面的顺序排列。 仅当计算机通过前一个阶段时，计算机才会移动到后面的阶段。 例如，如果服务器未通过 Azure 就绪阶段，则会将其标记为不适用于 Azure。 不会对该服务器执行大小调整和成本计算。

## <a name="whats-in-an-azure-vm-assessment"></a>Azure VM 评估有哪些？

服务器评估中的 Azure VM 评估中包含以下是：

**属性** | **详细信息**
--- | ---
**目标位置** | 要迁移到的位置。 服务器评估目前支持以下目标 Azure 区域：<br/><br/> 澳大利亚东部，澳大利亚东南部、巴西南部、加拿大中部、加拿大东部、美国东部、美国中北部、中国东部、中国北部、东亚、美国东部、美国东部、日本西部、韩国北部、日本东部、日本西部、韩国中部、韩国南部、美国中北部、北欧、美国中南部、英国西部英国南部 US Gov 亚利桑那州、US Gov 德克萨斯州、US Gov 弗吉尼亚州、美国西部、西欧、印度西部、美国西部和美国西部2。
**目标存储磁盘（按原样调整大小）** | 要用于 Azure 中的存储的磁盘类型。 <br/><br/> 将目标存储磁盘指定为高级托管、标准 SSD 管理或标准 HDD 管理。
**目标存储磁盘（基于性能的大小调整）** | 将目标存储磁盘的类型指定为自动、高级托管、标准 HDD 管理或标准 SSD 管理。<br/><br/> **自动**：磁盘建议基于磁盘的性能数据，即 IOPS 和吞吐量。<br/><br/>**高级或标准**：评估建议选定存储类型中的磁盘 SKU。<br/><br/> 如果需要99.9% 的单实例 VM 服务级别协议（SLA），请考虑使用高级托管磁盘。 此使用确保将评估中的所有磁盘作为高级托管磁盘。<br/><br/> Azure Migrate 仅支持用于迁移评估的托管磁盘。
**Azure 保留 VM 实例** | 指定[保留实例](https://azure.microsoft.com/pricing/reserved-vm-instances/)，以便评估评估中的成本。<br/><br/> 选择 "保留实例" 时，"折扣（%）""VM 运行时间" 属性不适用。<br/><br/> Azure Migrate 当前仅支持即用即付产品/服务的 Azure 保留 VM 实例。
**调整大小标准** | 用于将 Azure VM。<br/><br/> 使用按原样调整大小或基于性能的大小调整。
**性能历史记录** | 用于基于性能的大小调整。 性能历史记录指定评估性能数据时使用的持续时间。
**百分位使用率** | 用于基于性能的大小调整。 百分点利用率指定用于合理精简的性能示例的百分位数值。
**VM 系列** | 要用于合理精简的 Azure VM 系列。 例如，如果你没有需要在 Azure 中使用 A 系列 Vm 的生产环境，则可以从序列列表中排除 A 系列。
**舒适因子** | 评估过程中使用的缓冲区。 它适用于 Vm 的 CPU、RAM、磁盘和网络使用率数据。 它可解决季节性使用情况、简短性能历史记录等问题，并可能会在将来的使用量上增加。<br/><br/> 例如，利用率为20% 的10核 VM 通常会产生两核 VM。 如果使用的是2.0，则结果是一个四核 VM。
**产品** | 你注册的[Azure 产品/服务](https://azure.microsoft.com/support/legal/offer-details/)。 服务器评估将估计该产品/服务的成本。
**货币** | 帐户的计费货币。
**折扣 (%)** | 在 Azure 产品/服务上收到的任何特定于订阅的折扣。 默认设置是 0%。
**VM 运行时间** | 不连续运行的 Azure Vm 每月的持续时间（以天为单位）和每天的小时数。 成本估算基于该持续时间。<br/><br/> 默认值为每月31天，每天24小时。
**Azure 混合权益** | 指定您是否具有软件保障并且有资格使用[Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-use-benefit/)。 如果设置的默认值为 "是"，则对于 Windows Vm，将考虑除 Windows 以外的操作系统的 Azure 价格。
**EA 订阅** | 指定企业协议（EA）订阅用于成本估算。 考虑适用于订阅的折扣。 <br/><br/> 保留保留实例的设置，折扣（%）和 VM 运行时间属性及其默认设置。


查看使用服务器评估创建评估的[最佳实践](best-practices-assessment.md)。

## <a name="calculate-readiness"></a>计算准备情况

并非所有计算机都适合在 Azure 中运行。 Azure VM 评估会评估所有本地计算机，并为其分配一个就绪类别。

- **适用于 azure**：计算机可以按原样迁移到 azure，而无需进行任何更改。 它将在 Azure 中从完整的 Azure 支持开始。
- **Azure 有条件的就绪**：计算机可能会在 azure 中启动，但可能没有完整的 azure 支持。 例如，Azure 不支持运行旧版 Windows Server 的计算机。 在将这些计算机迁移到 Azure 之前，必须注意。 若要解决任何准备情况问题，请遵循评估建议的修正指南。
- **未准备好进行 azure**：计算机不会在 azure 中启动。 例如，如果本地计算机的磁盘存储超过 64 TB，Azure 将无法托管计算机。 请按照更正指南进行操作，以在迁移之前解决问题。
- **准备情况未知**：由于元数据不足，Azure Migrate 无法确定计算机的就绪性。

若要计算准备情况，服务器评估会查看下表中汇总的计算机属性和操作系统设置。

### <a name="machine-properties"></a>计算机属性

对于 Azure VM 评估，服务器评估会查看本地 VM 的以下属性，以确定它是否可以在 Azure Vm 上运行。

properties | 详细信息 | Azure 迁移就绪性状态
--- | --- | ---
**启动类型** | Azure 支持启动类型为 BIOS 而不是 UEFI 的 Vm。 | 如果启动类型为 UEFI，则有条件地准备就绪
**核心数** | 每台计算机的核心数不得超过128个，这是 Azure VM 支持的最大数量。<br/><br/> 如果性能历史记录可用，Azure Migrate 会考虑已利用的内核数以进行比较。 如果评估设置指定了舒适的因素，则使用的内核数乘以舒适系数。<br/><br/> 如果没有性能历史记录，Azure Migrate 将使用已分配的内核，而不应用舒适因子。 | 如果内核数在限制范围内，则已准备就绪
**RAM** | 每台计算机都必须具有超过 3892 GB 的 RAM，这是 Azure M 系列 Standard_M128m &nbsp; <sup>2</sup> VM 支持的最大大小。 [了解详细信息](../virtual-machines/windows/sizes.md)。<br/><br/> 如果性能历史记录可用，Azure Migrate 会将使用的 RAM 视为比较。 如果指定了舒适的因素，则使用的 RAM 将乘以舒适系数。<br/><br/> 如果没有历史记录，则使用分配的 RAM，而不应用舒适因子。<br/><br/> | 如果 RAM 量在限制范围内，则已准备就绪
**存储磁盘** | 分配的磁盘大小不得超过 32 TB。 尽管 Azure 支持 64 TB 磁盘和 Azure 超级 SSD 磁盘，Azure Migrate：服务器评估目前检查 32 TB 作为磁盘大小限制，因为它尚不支持超级 SSD。 <br/><br/> 附加到计算机的磁盘数（包括 OS 磁盘）必须是65或更少。 | 如果磁盘大小和数量处于限制范围内，则已准备就绪
**网络** | 计算机上必须附加超过32个网络接口（Nic）。 | 如果 Nic 数在限制范围内，则已准备就绪

### <a name="guest-operating-system"></a>来宾操作系统

对于 Azure VM 评估以及查看 VM 属性，服务器评估会查看计算机的来宾操作系统，以确定它是否可在 Azure 上运行。

> [!NOTE]
> 为了处理 VMware Vm 的来宾分析，服务器评估使用在 vCenter Server 中为 VM 指定的操作系统。 但 vCenter Server 不提供 Linux VM 操作系统的内核版本。 若要发现版本，需要设置[应用程序发现](./how-to-discover-applications.md)。 然后，设备会使用在设置应用发现时指定的来宾凭据来发现版本信息。


服务器评估使用以下逻辑来识别基于操作系统的 Azure 准备情况：

**操作系统** | **详细信息** | **Azure 迁移就绪性状态**
--- | --- | ---
Windows Server 2016 和所有 SP | Azure 提供完全支持。 | 适用于 Azure。
Windows Server 2012 R2 和所有 SP | Azure 提供完全支持。 | 适用于 Azure。
Windows Server 2012 和所有 SP | Azure 提供完全支持。 | 适用于 Azure。
Windows Server 2008 R2 和所有 SP | Azure 提供完全支持。| 适用于 Azure。
Windows Server 2008（32 位和 64 位） | Azure 提供完全支持。 | 适用于 Azure。
Windows Server 2003 和 Windows Server 2003 R2 | 这些操作系统已超过其支持终止日期，需要[自定义支持协议（CSA）](https://aka.ms/WSosstatement)以支持 Azure 中的支持。 | Azure 有条件的就绪。 请考虑在迁移到 Azure 之前升级 OS。
Windows 2000、Windows 98、Windows 95、Windows NT、Windows 3.1 和 MS-DOS | 这些操作系统已超过其支持结束日期。 计算机可能会在 Azure 中启动，但 Azure 不提供 OS 支持。 | Azure 有条件的就绪。 建议在迁移到 Azure 之前升级 OS。
Windows 7、Windows 8 和 Windows 10 | Azure[仅支持 Visual Studio 订阅。](../virtual-machines/windows/client-images.md) | Azure 有条件的就绪。
Windows 10 专业版 | Azure 提供了对[多租户托管权限](../virtual-machines/windows/windows-desktop-multitenant-hosting-deployment.md)的支持。 | Azure 有条件的就绪。
Windows Vista 和 Windows XP Professional | 这些操作系统已超过其支持结束日期。 计算机可能会在 Azure 中启动，但 Azure 不提供 OS 支持。 | Azure 有条件的就绪。 建议在迁移到 Azure 之前升级 OS。
Linux | 请参阅 Azure 予以认可的[Linux 操作系统](../virtual-machines/linux/endorsed-distros.md)。 其他 Linux 操作系统可能会在 Azure 中启动。 但建议你在迁移到 Azure 之前，将 OS 升级到认可的版本。 | 如果版本受到认可，则为 Azure 已就绪。<br/><br/>如果版本未认可，则有条件地准备就绪。
Oracle Solaris、Apple macOS 和 FreeBSD 等其他操作系统 | Azure 不认可这些操作系统。 计算机可能会在 Azure 中启动，但 Azure 不提供 OS 支持。 | Azure 有条件的就绪。 建议在迁移到 Azure 之前安装受支持的操作系统。  
vCenter Server 中指定为“其他”**** 的 OS | 在这种情况下 Azure Migrate 无法识别 OS。 | 就绪性未知。 确保 Azure 支持在 VM 内运行的 OS。
32 位操作系统 | 计算机可能会在 Azure 中启动，但 Azure 可能不提供完全支持。 | Azure 有条件的就绪。 迁移到 Azure 之前，请考虑升级到64位操作系统。

## <a name="calculating-sizing"></a>计算调整大小

将计算机标记为准备好进行 Azure 后，服务器评估将在 Azure VM 评估中提供大小建议。 这些建议用于标识 Azure VM 和磁盘 SKU。 大小调整计算取决于你使用的是本地大小调整还是基于性能的大小调整。

### <a name="calculate-sizing-as-is-on-premises"></a>计算大小（按本地方式）

 如果使用 "按本地大小调整"，则服务器评估不会考虑 Azure VM 评估中 Vm 和磁盘的性能历史记录。

- **计算规模**：服务器评估根据本地分配的大小分配 AZURE VM SKU。
- **存储和磁盘大小调整**：服务器评估查看评估属性中指定的存储类型，并建议相应的磁盘类型。 可能的存储类型为标准 HDD、标准 SSD 和高级。 默认存储类型为 "高级"。
- **网络大小调整**：服务器评估考虑本地计算机上的网络适配器。

### <a name="calculate-sizing-performance-based"></a>计算大小（基于性能）

如果在 Azure VM 评估中使用基于性能的大小调整，服务器评估会按如下所示调整大小建议：

- 服务器评估会考虑计算机的性能历史记录来识别 Azure 中的 VM 大小和磁盘类型。
- 如果使用 CSV 文件导入服务器，则会使用指定的值。 如果你已将本地计算机过度分配，利用率较低，并且你想要将 Azure VM 来节省成本，此方法特别有用。
- 如果不想要使用性能数据，请按照上一节中所述，将大小调整条件重置为 "按本地"。

#### <a name="calculate-storage-sizing"></a>计算存储大小

对于 Azure VM 评估中的存储大小，Azure Migrate 尝试将连接到计算机的每个磁盘映射到 Azure 磁盘。 大小调整工作方式如下：

1. 服务器评估增加了磁盘的读取和写入 IOPS，以获取所需的总 IOPS。 同样，它会添加 "读取" 和 "写入吞吐量" 值，以获取每个磁盘的总吞吐量。 对于基于导入的评估，可以选择提供总 IOPS、总吞吐量和 total no。 导入文件中的磁盘，无需指定单个磁盘设置。 如果执行此操作，则会跳过单个磁盘大小调整，并直接使用提供的数据来计算规模，并选择适当的 VM SKU。

1. 如果已将存储类型指定为 "自动"，则所选类型基于 "有效 IOPS" 和 "吞吐量" 值。 服务器评估确定是否将磁盘映射到 Azure 中的标准 HDD、标准 SSD 或高级磁盘。 如果存储类型设置为这些磁盘类型之一，则服务器评估会尝试在选定存储类型中查找磁盘 SKU。
1. 选择磁盘的方法如下所示：
    - 如果服务器评估找不到具有所需 IOPS 和吞吐量的磁盘，则会将计算机标记为不适用于 Azure。
    - 如果服务器评估查找一组合适的磁盘，则会选择支持在评估设置中指定的位置的磁盘。
    - 如果有多个合格的磁盘，服务器评估会选择成本最低的磁盘。
    - 如果任何磁盘的性能数据不可用，则配置磁盘大小用于在 Azure 中查找标准 SSD 磁盘。

#### <a name="calculate-network-sizing"></a>计算网络大小

对于 Azure VM 评估，服务器评估会尝试查找支持连接到本地计算机的网络适配器的数量和所需性能的 Azure VM。

- 为了获得本地 VM 的有效网络性能，服务器评估将跨所有网络适配器将数据传输速率从计算机传出（网络输出）。 然后，它将应用舒适因子。 它使用结果值查找可支持所需网络性能的 Azure VM。
- 与网络性能一起，服务器评估还会考虑 Azure VM 是否可支持所需的网络适配器数。
- 如果网络性能数据不可用，则服务器评估只考虑 VM 大小的网络适配器计数。

#### <a name="calculate-compute-sizing"></a>计算计算规模

在计算存储和网络需求后，服务器评估会考虑 CPU 和 RAM 要求，以便在 Azure 中查找合适的 VM 大小。

- Azure Migrate 查看有效的已使用内核和 RAM，查找合适的 Azure VM 大小。
- 如果找不到合适的大小，计算机将标记为不适用于 Azure。
- 如果找到了合适的大小，Azure Migrate 将应用存储和网络计算。 然后，它会对最终的 VM 大小建议应用位置和定价层设置。
- 如果有多个合格的 Azure VM 大小，建议选择成本最低的那一个。

## <a name="confidence-ratings-performance-based"></a>置信度评级（基于性能）

Azure Migrate 中的每个基于性能的 Azure VM 评估都与置信度分级相关联。 评级范围从一个（最低）到五（最高）星。 置信度评级有助于您估算 Azure Migrate 提供的大小建议的可靠性。

- 置信度评级分配给评估。 评级基于计算评估所需的数据点的可用性。
- 对于基于性能的大小调整，服务器评估需要：
    - CPU 和 VM RAM 的利用率数据。
    - 附加到 VM 的每个磁盘的磁盘 IOPS 和吞吐量数据。
    - 为附加到 VM 的每个网络适配器处理基于性能的大小调整的网络 i/o。

如果其中的任何利用率均不可用，则大小建议可能不可靠。

> [!NOTE]
> 不会为使用导入的 CSV 文件评估的服务器分配置信度。 评级也不适用于 "按本地评估"。

### <a name="ratings"></a>评级

下表显示了评估置信度评级，它取决于可用数据点的百分比：

   **数据点的可用性** | **置信度分级**
   --- | ---
   0-20% | 1 星
   21-40% | 2 星
   41-60% | 3 星
   61-80% | 4 星
   81-100% | 5 星

### <a name="low-confidence-ratings"></a>低置信度分级

下面是评估降低置信度的几个原因：

- 你未在创建评估的持续时间内分析环境。 例如，如果你创建了 "性能持续时间" 设置为一天的评估，则必须在开始发现所有数据点后等待至少一天。
- 某些 Vm 在计算评估的时间内关闭。 如果任何 Vm 在某个持续时间内关闭，服务器评估将无法收集该时间段的性能数据。
- 某些 Vm 是在计算评估期间创建的。 例如，假设你为上个月的性能历史记录创建了评估，但某些 Vm 只是在一周之前创建的。 新的 Vm 的性能历史记录不会存在于完整的持续时间。

> [!NOTE]
> 如果任何评估的置信度小于五星级，我们建议你等待至少一天，让设备对环境进行配置，然后重新计算评估。 否则，基于性能的大小调整可能不可靠。 在这种情况下，我们建议你将评估切换为本地大小调整。

## <a name="calculate-monthly-costs"></a>计算每月成本

完成规模调整建议后，中的 Azure VM 评估 Azure Migrate 在迁移后计算计算和存储成本。

- **计算成本**： Azure Migrate 使用推荐的 azure VM 大小和 AZURE 计费 API 来计算 VM 每月的费用。

    计算将考虑：
    - 操作系统
    - 软件保障
    - 预留实例
    - VM 运行时间
    - 位置
    - 货币设置

    服务器评估在所有计算机上聚合成本，以计算每月总计算成本。

- **存储成本**：通过聚合附加到计算机的所有磁盘的每月成本来计算计算机的每月存储成本。

    服务器评估通过聚合所有计算机的存储成本来计算每月总存储成本。 目前，计算不会考虑评估设置中指定的产品/服务。

成本以在评估设置中指定的币种显示。

## <a name="next-steps"></a>后续步骤

[查阅](best-practices-assessment.md)关于创建评估的最佳做法。 

- 了解如何为[VMware vm](tutorial-prepare-vmware.md)、 [hyper-v vm](tutorial-prepare-hyper-v.md)和[物理服务器](tutorial-prepare-physical.md)运行评估。
- 了解如何评估[使用 CSV 文件导入](tutorial-assess-import.md)的服务器。
- 了解如何设置[依赖项可视化](concepts-dependency-visualization.md)。
