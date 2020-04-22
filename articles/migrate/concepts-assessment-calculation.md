---
title: Azure 迁移服务器评估中的评估
description: 了解 Azure 迁移服务器评估中的评估
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 2f76ea5f195be2914cdcdb4de9e93af38504d66e
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769923"
---
# <a name="assessments-in-azure-migrate-server-assessment"></a>Azure 迁移中的评估：服务器评估

本文概述了[Azure 迁移：服务器评估](migrate-services-overview.md#azure-migrate-server-assessment-tool)工具中的评估。 该工具可以评估本地 VMware 虚拟机、超 VM 和物理服务器，以便迁移到 Azure。

## <a name="whats-an-assessment"></a>什么是评估？

使用"服务器评估"工具进行评估可测量就绪情况，并估计将本地服务器迁移到 Azure 的效果。

> [!NOTE]
> 在 Azure 政府中，查看[支持的目标](migrate-support-matrix.md#supported-geographies-azure-government)评估位置。 请注意，评估中的 VM 大小建议将专门用于政府云区域的 VM 系列。 [了解有关](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines)VM 类型的信息。

## <a name="types-of-assessments"></a>评估类型

使用服务器评估创建的评估是数据的时间点快照。 服务器评估提供两种类型的评估。

**评估类型** | **详细信息** | **数据**
--- | --- | ---
**基于性能** | 根据收集的绩效数据提出建议的评估 | VM 大小建议基于 CPU 和 RAM 利用率数据。<br/><br/> 磁盘类型建议基于每秒输入/输出操作 （IOPS） 和本地磁盘的吞吐量。 磁盘类型包括 Azure 标准硬盘、Azure 标准 SSD 和 Azure 高级磁盘。
**本地** | 不使用性能数据提出建议的评估 | VM 大小建议基于本地 VM 大小。<br/><br> 建议的磁盘类型基于评估的选定存储类型。

## <a name="how-do-i-run-an-assessment"></a>如何运行评估？

有几种方法可以运行评估。

- 使用轻量级 Azure 迁移设备收集的服务器元数据评估计算机。 设备会发现本地计算机。 然后，它将计算机元数据和性能数据发送到 Azure 迁移。
- 使用以逗号分隔值 （CSV） 格式导入的服务器元数据评估计算机。

## <a name="how-do-i-assess-with-the-appliance"></a>如何使用产品进行评估？

如果要部署 Azure 迁移设备以发现本地服务器，请执行以下步骤：

1. 设置 Azure 和本地环境以使用服务器评估。
1. 对于第一次评估，请创建 Azure 项目，并将服务器评估工具添加到其中。
1. 部署轻量级 Azure 迁移设备。 设备不断发现本地计算机，并将计算机元数据和性能数据发送到 Azure 迁移。 将设备部署为 VM 或物理计算机。 您无需在要评估的计算机上安装任何内容。

设备开始发现机器后，您可以将要评估的计算机收集到组中，并为组运行评估。

请按照[VMware、Hyper-V](tutorial-prepare-vmware.md)或[物理服务器](tutorial-prepare-physical.md)的教程来试用这些步骤。 [Hyper-V](tutorial-prepare-hyper-v.md)

## <a name="how-do-i-assess-with-imported-data"></a>如何使用导入的数据进行评估？

如果使用 CSV 文件评估服务器，则不需要设备。 相反，执行以下步骤：

1. 将 Azure 设置为使用服务器评估。
1. 对于第一次评估，请创建 Azure 项目，并将服务器评估工具添加到其中。
1. 下载 CSV 模板并将其添加到其中。
1. 将模板导入服务器评估。
1. 发现随导入一起添加的服务器，将它们收集到组中，并为组运行评估。

## <a name="what-data-does-the-appliance-collect"></a>产品收集哪些数据？

如果使用 Azure 迁移设备进行评估，请了解为[VMware](migrate-appliance.md#collected-data---vmware)和[Hyper-V](migrate-appliance.md#collected-data---hyper-v)收集的元数据和性能数据。

## <a name="how-does-the-appliance-calculate-performance-data"></a>设备如何计算性能数据？

如果使用设备进行发现，它将通过以下步骤收集计算设置的性能数据：

1. 产品收集实时采样点。

    - **VMware VM**：每 20 秒收集一个采样点。
    - **超 VM**：每 30 秒收集一个采样点。
    - **物理服务器**：每五分钟收集一个采样点。

1. 设备将采样点合并，每 10 分钟创建一个数据点。 要创建数据点，设备将从所有示例中选择峰值。 然后，它将数据点发送到 Azure。
1. 服务器评估存储上个月的所有 10 分钟数据点。
1. 创建评估时，服务器评估将标识用于权限化的相应数据点。 标识基于*性能历史记录*和*百分位数利用率*的百分位值。

    - 例如，如果性能历史记录为一周，百分位数利用率为第 95 百分位，则服务器评估会对上周的 10 分钟采样点进行排序。 它按升序排序，并选择第 95 个百分位值进行权利化。
    - 第 95 个百分位值可确保忽略任何异常值，如果选取第 99 个百分位，可能会包括这些异常值。
    - 如果要选择期间的峰值使用率，并且不想错过任何异常值，请选择第 99 百分位数，了解百分位数利用率。

1. 此值乘以舒适系数，以获得设备收集的这些指标的有效性能利用率数据：

    - CPU 使用率
    - RAM 利用率
    - 磁盘 IOPS（读写）
    - 磁盘吞吐量（读取和写入）
    - 网络吞吐量（进出）

## <a name="how-are-assessments-calculated"></a>如何计算评估？

服务器评估使用本地计算机的元数据和性能数据来计算评估。 如果部署 Azure 迁移设备，评估将使用设备收集的数据。 但是，如果运行使用 CSV 文件导入的评估，则提供计算的元数据。

计算发生在以下三个阶段：

1. **计算 Azure 就绪性**：评估计算机是否适合迁移到 Azure。
1. **计算大小调整建议**：估计计算、存储和网络大小调整。
1. **计算每月成本**：计算迁移后在 Azure 中运行计算机的估计每月计算和存储成本。

计算按上述顺序排列。 仅当计算机服务器通过前一阶段时，计算机服务器才会移动到稍后阶段。 例如，如果服务器在 Azure 就绪阶段失败，则将其标记为不适合 Azure。 未为该服务器执行大小调整和成本计算。

## <a name="whats-in-an-assessment"></a>评估内容有哪些？

以下是服务器评估中评估中包含的内容：

属性 | 详细信息
--- | ---
**目标位置** | 要迁移到的位置。 服务器评估当前支持这些目标 Azure 区域：<br/><br/> 澳大利亚东部、澳大利亚东南部、巴西南部、加拿大中部、加拿大东部、印度中部、美国中部、中国东部、中国北部、东亚、美国东部、美国东部 2、德国中部、德国东北部、日本东部、日本西部、韩国中部、韩国中部、美国中北部、北欧、美国中南部、东南亚、南印度、英国南部、英国西部、美国亚利桑那州州长、美国得克萨斯州州长、美国弗吉尼亚州州长，美国中西部、西欧、西印度、美国西部和美国西部 2.
**目标存储磁盘（大小调整）** | 用于在 Azure 中存储的磁盘类型。 <br/><br/> 将目标存储磁盘指定为高级管理、标准 SSD 管理或标准硬盘管理。
**目标存储磁盘（基于性能的尺寸调整）** | 指定目标存储磁盘的类型为自动、高级管理、标准硬盘管理或标准 SSD 管理。<br/><br/> **自动**：磁盘建议基于磁盘的性能数据，即 IOPS 和吞吐量。<br/><br/>**高级或标准**：评估建议在所选存储类型内使用磁盘 SKU。<br/><br/> 如果希望单实例 VM 服务级别协议 （SLA） 为 99.9%，请考虑使用高级托管磁盘。 此使用可确保建议将评估中的所有磁盘作为高级管理磁盘。<br/><br/> Azure 迁移仅支持用于迁移评估的托管磁盘。
**Azure 预留虚拟机实例** | 指定[预留实例](https://azure.microsoft.com/pricing/reserved-vm-instances/)，以便评估中的成本估计考虑它们。<br/><br/> Azure 迁移当前仅支持"现付即付"服务的 Azure 预留 VM 实例。
**调整标准大小** | 用于调整 Azure VM 的大小。<br/><br/> 使用按"大小调整"或基于性能的尺寸调整。
**性能历史记录** | 与基于性能的尺寸调整一起使用。 性能历史记录指定计算性能数据时使用的持续时间。
**百分位使用率** | 与基于性能的尺寸调整一起使用。 百分位数利用率指定用于权限化的性能示例的百分位数值。
**VM 系列** | 要考虑进行权限调整的 Azure VM 系列。 例如，如果 Azure 中没有需要 A 系列 VM 的生产环境，则可以从系列列表中排除 A 系列。
**舒适因子** | 评估期间使用的缓冲区。 它应用于 VM 的 CPU、RAM、磁盘和网络利用率数据。 它考虑了季节性使用、性能历史记录短以及未来使用量可能增加等问题。<br/><br/> 例如，利用率为 20% 的 10 核 VM 通常会导致双核 VM。 舒适系数为 2.0 时，结果改为四核 VM。
**产品/服务** | 注册的[Azure 产品/服务](https://azure.microsoft.com/support/legal/offer-details/)。 服务器评估估计该产品/服务的成本。
**货币** | 帐户的帐单货币。
**折扣 (%)** | 在 Azure 产品/服务顶部收到的任何特定于订阅的折扣。 默认设置是 0%。
**VM 运行时间** | Azure VM 的持续时间（以每月天数和每天小时数表示）不会连续运行。 成本估算基于该持续时间。<br/><br/> 默认值为每月 31 天和每天 24 小时。
**Azure 混合权益** | 指定您是否具有软件保证并有资格享受[Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-use-benefit/)。 如果该设置的默认值为"是"，则 Windows VM 将考虑 Windows VM 以外的操作系统的 Azure 价格。

[查看](best-practices-assessment.md)使用服务器评估创建评估的最佳做法。

## <a name="calculate-readiness"></a>计算就绪情况

并非所有计算机都适合在 Azure 中运行。 服务器评估评估所有本地计算机，并分配给它们一个就绪类别。

- **已为 Azure 做好准备**：可以根据需要将计算机迁移到 Azure，而无需进行任何更改。 它将在 Azure 中开始，支持 Azure。
- **有条件地为 Azure 做好准备**：计算机可能在 Azure 中启动，但可能没有完整的 Azure 支持。 例如，Azure 不支持运行旧版本的 Windows 服务器的计算机。 在将这些计算机迁移到 Azure 之前，必须小心谨慎。 要修复任何就绪问题，请遵循评估建议的补救指南。
- **尚未准备好使用 Azure**：计算机不会在 Azure 中启动。 例如，如果本地计算机的磁盘存储超过 64 TB，则 Azure 无法承载该计算机。 按照修正指南在迁移之前解决问题。
- **就绪未知**：由于元数据不足，Azure 迁移无法确定计算机的就绪情况。

为了计算就绪情况，服务器评估会审核下表中汇总的计算机属性和操作系统设置。

### <a name="machine-properties"></a>计算机属性

服务器评估会检查本地 VM 的以下属性，以确定是否可以在 Azure 上运行。

属性 | 详细信息 | Azure 迁移就绪性状态
--- | --- | ---
**启动类型** | Azure 支持具有 BIOS 启动类型的 VM，而不是 UEFI。 | 如果引导类型为 UEFI，则有条件就绪
**核心数** | 每台计算机的内核数不得超过 128 个，这是 Azure VM 支持的最大数量。<br/><br/> 如果性能历史记录可用，Azure Migrate 会考虑已利用的内核数以进行比较。 如果评估设置指定舒适系数，则使用的核心数乘以舒适系数。<br/><br/> 如果没有性能历史记录，Azure 迁移将使用分配的内核，而无需应用舒适系数。 | 如果内核数在限制范围内，则准备就绪
**Ram** | 每台计算机的 RAM 必须不超过 3，892 GB，这是 Azure M 系列的最大大小Standard_M128m&nbsp;<sup>2</sup>个 VM 支持。 [了解详细信息](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)。<br/><br/> 如果性能历史记录可用，Azure 迁移将考虑使用的 RAM 进行比较。 如果指定了舒适系数，则使用的 RAM 乘以舒适系数。<br/><br/> 如果没有历史记录，则无需应用舒适系数即可使用分配的 RAM。<br/><br/> | 如果 RAM 量在限制范围内，则准备就绪
**存储磁盘** | 磁盘的分配大小不得超过 32 TB。 尽管 Azure 支持具有 Azure 超 SD 磁盘的 64 TB 磁盘，但 Azure 迁移：服务器评估当前会检查 32 TB 作为磁盘大小限制，因为它还不支持超 SSD。 <br/><br/> 连接到计算机（包括操作系统磁盘）的磁盘数必须为 65 个或更少。 | 如果磁盘大小和数量在限制范围内，则准备就绪
**网络连接** | 计算机必须连接不超过 32 个网络接口 （NIC）。 | 如果 NIC 的数量在限制范围内，则准备就绪

### <a name="guest-operating-system"></a>来宾操作系统

除了查看 VM 属性外，服务器评估还查看计算机的来宾操作系统，以确定是否可以在 Azure 上运行。

> [!NOTE]
> 要处理 VMware VM 的来宾分析，服务器评估使用 vCenter 服务器中为 VM 指定的操作系统。 对于在 VMware 上运行的 Linux VM，服务器评估当前无法标识来宾操作系统的内核版本。

服务器评估使用以下逻辑根据操作系统标识 Azure 就绪：

**操作系统** | **详细信息** | **Azure 迁移就绪性状态**
--- | --- | ---
Windows Server 2016 和所有 SP | Azure 提供完全支持。 | 已为 Azure 做好准备。
Windows Server 2012 R2 和所有 SP | Azure 提供完全支持。 | 已为 Azure 做好准备。
Windows Server 2012 和所有 SP | Azure 提供完全支持。 | 已为 Azure 做好准备。
Windows Server 2008 R2 和所有 SP | Azure 提供完全支持。| 已为 Azure 做好准备。
Windows Server 2008（32 位和 64 位） | Azure 提供完全支持。 | 已为 Azure 做好准备。
Windows 服务器 2003 和 Windows 服务器 2003 R2 | 这些操作系统已过了其终止支持日期，并且需要[自定义支持协议 （CSA）](https://aka.ms/WSosstatement)来在 Azure 中提供支持。 | 有条件地为 Azure 做好准备。 请考虑在迁移到 Azure 之前升级操作系统。
Windows 2000、Windows 98、Windows 95、Windows NT、Windows 3.1 和 MS-DOS | 这些操作系统已超过其支持终止日期。 计算机可能在 Azure 中启动，但 Azure 不提供操作系统支持。 | 有条件地为 Azure 做好准备。 我们建议您在迁移到 Azure 之前升级操作系统。
Windows 7、Windows 8 和 Windows 10 | Azure[仅通过可视化工作室订阅提供支持。](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) | 有条件地为 Azure 做好准备。
Windows 10 专业版 | Azure 提供了对[多租户托管权限](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment)的支持。 | 有条件地为 Azure 做好准备。
视窗Vista和视窗XP专业 | 这些操作系统已超过其支持终止日期。 计算机可能在 Azure 中启动，但 Azure 不提供操作系统支持。 | 有条件地为 Azure 做好准备。 我们建议您在迁移到 Azure 之前升级操作系统。
Linux | 请参阅 Azure 支持的[Linux 操作系统](../virtual-machines/linux/endorsed-distros.md)。 其他 Linux 操作系统可能在 Azure 中启动。 但是，我们建议您在迁移到 Azure 之前将操作系统升级到支持的版本。 | 如果版本受到认可，则为 Azure 已就绪。<br/><br/>如果版本未得到认可，则有条件地准备就绪。
其他操作系统，如甲骨文索拉里斯，苹果macOS和FreeBSD | Azure 不认可这些操作系统。 计算机可能在 Azure 中启动，但 Azure 不提供操作系统支持。 | 有条件地为 Azure 做好准备。 我们建议您在迁移到 Azure 之前安装受支持的操作系统。  
vCenter Server 中指定为“其他”**** 的 OS | 在这种情况下，Azure 迁移无法标识操作系统。 | 就绪性未知。 确保 Azure 支持在 VM 内运行的操作系统。
32 位操作系统 | 计算机可能在 Azure 中启动，但 Azure 可能无法提供完全支持。 | 有条件地为 Azure 做好准备。 在迁移到 Azure 之前，请考虑升级到 64 位操作系统。

## <a name="calculating-sizing"></a>计算大小调整

在计算机标记为已准备好 Azure 后，服务器评估会制定大小调整建议。 这些建议标识 Azure VM 和磁盘 SKU。 大小调整计算取决于您使用的是本地大小调整还是基于性能的尺寸调整。

### <a name="calculate-sizing-as-is-on-premises"></a>计算大小调整（按本地大小调整）

 如果使用 as-is 本地大小调整，服务器评估不考虑 VM 和磁盘的性能历史记录。

- **计算大小调整**：服务器评估根据本地分配的大小分配 Azure VM SKU。
- **存储和磁盘大小调整**：服务器评估查看评估属性中指定的存储类型，并建议适当的磁盘类型。 可能的存储类型包括标准硬盘、标准 SSD 和高级存储。 默认存储类型为"高级"。
- **网络大小调整**：服务器评估考虑本地计算机上的网络适配器。

### <a name="calculate-sizing-performance-based"></a>计算大小调整（基于性能）

如果使用基于性能的尺寸调整，服务器评估会按如下方式调整大小建议：

- 服务器评估会考虑计算机的性能历史记录，以标识 Azure 中的 VM 大小和磁盘类型。
- 如果使用 CSV 文件导入服务器，则使用您指定的值。 如果过度分配了本地计算机，利用率较低，并且希望调整 Azure VM 的大小以节省成本，则此方法特别有用。
- 如果不想使用性能数据，请将大小调整条件重置为"本地原样"，如上一节所述。

#### <a name="calculate-storage-sizing"></a>计算存储大小

对于存储大小调整，Azure 迁移尝试将连接到计算机的每个磁盘映射到 Azure 磁盘。 大小调整的工作原理如下：

1. 服务器评估添加磁盘的读取和写入 IOPS，以获得所需的总 IOPS。 同样，它添加读写吞吐量值，以获得每个磁盘的总吞吐量。
1. 如果将存储类型指定为自动存储类型，则所选类型基于有效的 IOPS 和吞吐量值。 服务器评估确定是将磁盘映射到 Azure 中的标准硬盘、标准 SSD 还是高级磁盘。 如果存储类型设置为这些磁盘类型之一，服务器评估将尝试在所选存储类型中找到磁盘 SKU。
1. 选择磁盘如下：
    - 如果服务器评估找不到具有所需 IOPS 和吞吐量的磁盘，则它将计算机标记为不适合 Azure。
    - 如果服务器评估找到一组合适的磁盘，它将选择支持评估设置中指定位置的磁盘。
    - 如果有多个符合条件的磁盘，服务器评估会选择成本最低的磁盘。
    - 如果任何磁盘的性能数据不可用，则配置磁盘大小用于在 Azure 中查找标准 SSD 磁盘。

#### <a name="calculate-network-sizing"></a>计算网络大小

服务器评估尝试查找支持连接到本地计算机的网络适配器的数量和所需性能的 Azure VM。

- 为了获得本地 VM 的有效网络性能，服务器评估将数据传输速率聚合到计算机（网络外）中，并跨所有网络适配器进行。 然后应用舒适系数。 它使用生成的值查找支持所需网络性能的 Azure VM。
- 除了网络性能外，服务器评估还考虑 Azure VM 是否可以支持所需的网络适配器数。
- 如果网络性能数据不可用，服务器评估将仅考虑 VM 大小调整的网络适配器计数。

#### <a name="calculate-compute-sizing"></a>计算大小调整

计算存储和网络要求后，服务器评估会考虑 CPU 和 RAM 要求，以在 Azure 中找到合适的 VM 大小。

- Azure 迁移查看有效利用的核心和 RAM，以查找合适的 Azure VM 大小。
- 如果找不到合适的大小，计算机将标记为不适用于 Azure。
- 如果找到了合适的大小，Azure Migrate 将应用存储和网络计算。 然后，它将为最终 VM 大小建议应用位置和定价层设置。
- 如果有多个合格的 Azure VM 大小，建议选择成本最低的那一个。

## <a name="confidence-ratings-performance-based"></a>置信度（基于绩效）

Azure 迁移中的每个基于性能的评估都与置信度评级相关联。 额定值范围从一颗（最低）到五颗（最高）星。 置信度可帮助您估计 Azure 迁移提供的大小建议的可靠性。

- 置信度评级分配给评估。 评级基于计算评估所需的数据点的可用性。
- 对于基于性能的尺寸调整，服务器评估需要：
    - CPU 和 VM RAM 的利用率数据。
    - 连接到 VM 的每个磁盘的磁盘 IOPS 和吞吐量数据。
    - 网络 I/O，用于处理连接到 VM 的每个网络适配器的基于性能的尺寸调整。

如果这些利用率编号中的任何一个不可用，则大小建议可能不可靠。

> [!NOTE]
> 对于使用导入的 CSV 文件评估的服务器，不会分配置信度。 评级也不适用于本地评估。

### <a name="ratings"></a>评级

下表显示了评估置信度，该评级取决于可用数据点的百分比：

   **数据点的可用性** | **置信度分级**
   --- | ---
   0-20% | 1 星
   21-40% | 2 星
   41-60% | 3 星
   61-80% | 4 星
   81-100% | 5 星

### <a name="low-confidence-ratings"></a>低置信度

以下是评估可能获得低置信度的几个原因：

- 在创建评估的持续时间内，您没有分析您的环境。 例如，如果创建评估时，性能持续时间设置为一天，则必须在开始发现要收集的所有数据点后至少等待一天。
- 在计算评估期间，某些 VM 被关闭。 如果任何 VM 在一段时间内关闭，服务器评估无法收集该期间的性能数据。
- 在计算评估期间创建了一些 VM。 例如，假设您为上个月的绩效历史记录创建了评估，但某些 VM 是在一周前创建的。 新 VM 的性能历史记录将不存在整个持续时间。

> [!NOTE]
> 如果任何评估的置信度低于五星，我们建议您至少等待一天，让产品分析环境，然后重新计算评估。 否则，基于性能的尺寸调整可能不可靠。 在这种情况下，我们建议您将评估切换到本地大小调整。

## <a name="calculate-monthly-costs"></a>计算每月成本

调整大小建议完成后，Azure 迁移计算迁移后的计算和存储成本。

- **计算成本**：Azure 迁移使用建议的 Azure VM 大小和 Azure 计费 API 来计算 VM 的每月成本。

    计算考虑：
    - 操作系统
    - 软件保证
    - 预留实例
    - VM 运行时间
    - 位置
    - 货币设置

    服务器评估聚合所有计算机的成本，以计算每月计算总成本。

- **存储成本**：机器的每月存储成本是通过聚合连接到计算机的所有磁盘的每月成本来计算的。

    服务器评估通过聚合所有计算机的存储成本来计算每月总存储成本。 目前，计算不考虑评估设置中指定的报价。

成本以在评估设置中指定的币种显示。

## <a name="next-steps"></a>后续步骤

[查看](best-practices-assessment.md)创建评估的最佳做法。 

- 了解[VMware VM、](tutorial-prepare-vmware.md)[超 V VM](tutorial-prepare-hyper-v.md)和[物理服务器](tutorial-prepare-physical.md)的运行评估。
- 了解如何评估[使用 CSV 文件导入的](tutorial-assess-import.md)服务器。
- 了解如何设置[依赖项可视化](concepts-dependency-visualization.md)。
