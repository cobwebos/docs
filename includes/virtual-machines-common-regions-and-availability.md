# <a name="regions-and-availability-for-virtual-machines-in-azure"></a>Azure 中虚拟机的区域和可用性
请务必了解 Azure 中虚拟机 (VM) 运行的方式和位置，以及最大化性能、可用性和冗余的选项。 Azure 在世界各地的多个数据中心运行。 这些数据中心分组到地理区域，让用户可灵活选择构建应用程序的位置。 本文提供了 Azure 的可用性和冗余功能的概述。

## <a name="what-are-azure-regions"></a>什么是 Azure 区域？
Azure 允许用户在规定的地理区域（例如“美国西部”、“北欧”或“东南亚”）创建资源（例如 VM）。 目前，全球有 30 个 Azure 区域。 可查看[区域及其位置的列表](https://azure.microsoft.com/regions/)。 为了提供冗余和可用性，每个区域都设有多个数据中心。 这样，便可灵活构建应用程序，创建距离用户最近的 VM，满足任何法律、法规或税务要求。

## <a name="special-azure-regions"></a>特殊 Azure 区域
在构建应用程序时，出于合规性或法律方面的考虑，可能需要使用某些特殊的 Azure 区域。 这些特殊区域包括：

* **美国弗吉尼亚州政府**和**美国爱荷华州政府**
  * 适用于美国政府代理和合作伙伴的 Azure 物理和逻辑网络隔离实例，由甄选出来的美国公民操作。 包括其他合规认证，例如 [FedRAMP](https://www.microsoft.com/en-us/TrustCenter/Compliance/FedRAMP) 和 [DISA](https://www.microsoft.com/en-us/TrustCenter/Compliance/DISA)。 详细了解 [Azure Government](https://azure.microsoft.com/features/gov/)。
* **印度中部**、**印度南部**和**印度西部**
  * 这些区域目前可供在印度拥有本地注册的批量许可客户和合作伙伴使用。 在 2016 年，如果用户购买了直接在线订阅，则可以访问这些区域。
* **中国东部**和**中国北部**
  * 这些区域在 Microsoft 和 21Vianet 达成唯一合作关系之后可供用户使用，有了这种关系，Microsoft 就不需直接维护相关数据中心。 详细了解 [Microsoft Azure（中国）](http://www.windowsazure.cn/)。
* **德国中部**和**德国东北部**
  * 目前，用户可以通过数据信托模式来使用这些区域。这种方式将客户数据保留在德国，由 T-Systems（一家德国电信公司）充当德国的数据受托方来控制数据。

## <a name="region-pairs"></a>区域对
每个 Azure 区域都在同一地理位置（例如美国、欧洲或亚洲）配有另一区域。 此方法适用于跨地域复制资源（例如 VM 存储），降低因自然灾害、社会动乱、电力中断或物理网络中断而同时影响两个区域的可能性。 区域对的其他优点包括：

* 出现范围较广的 Azure 区域中断时，每个区域对中有一个区域优先级更高，这样可以缩短应用程序的还原时间。 
* 将逐一对配对的区域进行计划内 Azure 更新，尽量减少停机时间并降低应用程序中断风险。
* 出于税务和执法管辖权方面的考虑，数据仍将以配对的形式驻留在同一地域内（巴西南部除外）。

区域对的示例包括：

| 主要 | 辅助 |
|:--- |:--- |
| 美国西部 |美国东部 |
| 欧洲北部 |欧洲西部 |
| 亚洲东南部 |东亚 |

可以在[此处](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions)查看完整的区域对列表。

## <a name="feature-availability"></a>功能可用性
某些服务或 VM 功能（例如特定的 VM 大小或存储类型）仅在特定区域提供。 也有一些全球性 Azure 服务不需要用户选择特定的区域，例如 [Azure Active Directory](../articles/active-directory/active-directory-whatis.md)、[流量管理器](../articles/traffic-manager/traffic-manager-overview.md)或 [Azure DNS](../articles/dns/dns-overview.md)。 若要更好地设计应用程序环境，可查看 [Azure 服务在每个区域的可用性](https://azure.microsoft.com/regions/#services)。 

## <a name="storage-availability"></a>存储可用性
在考虑可用的存储复制选项时，了解 Azure 区域和地理位置变得相当重要。 根据存储类型，可以使用不同的复制选项。

**Azure 托管磁盘**
* 本地冗余存储 (LRS)
  * 在你创建存储帐户时所在的区域复制数据三次。

**基于存储帐户的磁盘**
* 本地冗余存储 (LRS)
  * 在你创建存储帐户时所在的区域复制数据三次。
* 区域冗余存储 (ZRS)
  * 在两到三个设施之间复制数据三次（在单个区域内或两个区域之间）。
* 异地冗余存储 (GRS)
  * 将数据复制到距主要区域数百英里以外的次要区域。
* 读取访问异地冗余存储 (RA-GRS)
  * 与 GRS 一样，可将数据复制到次要区域，但此外还提供对次要位置中数据的只读访问权限。

下表提供存储复制类型之间差异的简要概述：

| 复制策略 | LRS | ZRS | GRS | RA-GRS |
|:--- |:--- |:--- |:--- |:--- |
| 数据在多个设施之间进行复制。 |否 |是 |是 |是 |
| 可以从辅助位置和主位置读取数据。 |否 |否 |否 |是 |
| 在单独的节点上维护的数据副本数。 |3 |3 |6 |6 |

可以在[此处](../articles/storage/storage-redundancy.md)详细了解 Azure 存储复制选项。 有关托管磁盘的详细信息，请参阅 [Azure 托管磁盘概述](../articles/storage/storage-managed-disks-overview.md)。

### <a name="storage-costs"></a>存储成本
价格根据所选存储类型和可用性的不同而异。

**Azure 托管磁盘**
* 高级托管磁盘受固态硬盘 (SSD) 支持，标准托管磁盘受常规旋转磁盘支持。 高级和标准托管磁盘均按预配的磁盘容量计费。

**非托管磁盘**
* 高级存储以固态硬盘 (SSD) 为基础，收费方式根据磁盘容量而定。
* 标准存储以普通的旋转式磁盘为基础，收费方式根据所用容量和所需的存储可用性而定。
  * 对于 RA-GRS，有一个额外的异地复制数据传输费用，它是将该数据复制到另一个 Azure 区域收取的带宽费用。

有关不同存储类型和可用性选项的定价信息，请参阅 [Azure 存储器定价](https://azure.microsoft.com/pricing/details/storage/)。

## <a name="azure-images"></a>Azure 映像
在 Azure 中，VM 是基于映像创建的。 通常，映像来自 [Azure 应用商店](https://azure.microsoft.com/marketplace/)，合作伙伴可在 Azure 应用商店提供预配置的完整 OS 映像或应用程序映像。

从 Azure 应用商店中的映像创建 VM 时，实际上是在使用模板。 Azure Resource Manager 模板是声明性的 JavaScript 对象表示法 (JSON) 文件，可用于创建包含 VM、存储、虚拟网络等的复杂应用程序环境。详细了解如何使用 [Azure Resource Manager 模板](../articles/azure-resource-manager/resource-group-overview.md)，包括如何[构建自己的模板](../articles/resource-group-authoring-templates.md)。

也可以使用 [Azure CLI](../articles/virtual-machines/linux/upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 或 [Azure PowerShell](../articles/virtual-machines/windows/upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 创建自己的自定义映像并将其上载，快速创建符合特定构建要求的自定义 VM。

## <a name="availability-sets"></a>可用性集
可用性集是 VM 的逻辑分组，可让 Azure 了解应用程序的构建方式，以便提供冗余和可用性。 建议在可用性集内创建两个或多个 VM，提供高度可用的应用程序，并满足 [99.95% Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) 的要求。 当单个 VM 使用 [Azure 高级存储](../articles/storage/storage-premium-storage.md)时，Azure SLA 适用于计划外维护事件。 可用性集由可防止硬件故障以及允许安全应用更新的两个额外分组构成 - 容错域 (FD) 和更新域 (UD)。

![更新域和容错域配置的概念图](./media/virtual-machines-common-regions-and-availability/ud-fd-configuration.png)

详细了解如何管理 [Linux VM](../articles/virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 或 [Windows VM](../articles/virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 的可用性。

### <a name="fault-domains"></a>容错域
容错域是共享公用电源和网络交换机的基础硬件逻辑组，类似于本地数据中心内的机架。 在可用性集内创建 VM 时，Azure 平台会将 VM 自动分布到这些容错域。 此方法可限制潜在物理硬件故障、网络中断或断电的影响。

#### <a name="managed-disk-fault-domains-and-availability-sets"></a>托管磁盘容错域和可用性集
对于使用 [Azure 托管磁盘](../articles/storage/storage-faq-for-disks.md)的 VM，在使用托管可用性集时，VM 与托管磁盘容错域一致。 该一致性可确保附加到 VM 的所有托管磁盘都在同一托管磁盘容错域内。 在托管可用性集中，只能创建带托管磁盘的 VM。 托管磁盘容错域的数目因区域而异 - 每个区域两个或三个托管磁盘容错域。

### <a name="update-domains"></a>更新域
更新域是可以同时维护或重新启动的基础硬件逻辑组。 在可用性集内创建 VM 时，Azure 平台会自动将 VM 分布到这些更新域。 Azure 平台进行定期维护时，此方法可确保至少有一个应用程序实例始终保持运行状态。 在计划内维护期间，更新域的重启顺序可能不会按序进行，但一次只重启一个更新域。

## <a name="next-steps"></a>后续步骤
现在即可开始使用这些可用性和冗余功能构建 Azure 环境。 有关最佳实践的信息，请参阅 [Azure 可用性的最佳实践](../articles/best-practices-availability-checklist.md)。

