| 资源 | 默认限制 | 最大限制 |
| --- | --- | --- |
| 每个[订阅](../articles/billing-buy-sign-up-azure-subscription.md)的 VM 数 |每个区域 10,000 个<sup>1</sup> |每个区域 10,000 个 |
| 每个[订阅](../articles/billing-buy-sign-up-azure-subscription.md)的 VM 核心总数 |每个区域 20 个<sup>1</sup> | 联系支持人员 |
| 每个[订阅](../articles/billing-buy-sign-up-azure-subscription.md)的 VM 按系列（Dv2、F 等）核心数 |每个区域 20 个<sup>1</sup> | 联系支持人员 |
| 每个订阅的[共同管理员数](../articles/billing-add-change-azure-subscription-administrator.md) |不受限制 |不受限制 |
| 每个订阅的[存储帐户数](../articles/storage/common/storage-create-storage-account.md) |200 |200<sup>2</sup> |
| 每个订阅的[资源组数](../articles/azure-resource-manager/resource-group-overview.md) |800 |800 |
| 每个订阅的[可用性集数](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) |每个区域 2,000 个 |每个区域 2,000 个 |
| Resource Manager API 读取次数 |每小时 15,000 次 |每小时 15,000 次 |
| Resource Manager API 写入次数 |每小时 1,200 次 |每小时 1,200 次 |
| Resource Manager API 请求大小 |4,194,304 字节 |4,194,304 字节 |
| 每个订阅的标记数<sup>3</sup> |不受限制 |不受限制 |
| 每个订阅的唯一标记计算<sup>3</sup> | 10,000 | 10,000 |
| 每个订阅的[云服务数](../articles/cloud-services/cloud-services-choose-me.md) |不适用<sup>4</sup> |不适用<sup>4</sup> |
| 每个订阅的[地缘组数](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) |不适用<sup>4</sup> |不适用<sup>4</sup> |

<sup>1</sup>默认限制根据产品类别类型（例如免费试用、即用即付，以及系列（例如 Dv2、F、G 等））而有所不同。

<sup>2</sup>这包括标准和高级存储帐户。 如果需要的存储帐户超过 100 个，请通过 [Azure 支持](https://azure.microsoft.com/support/faq/)提出请求。 Azure 存储团队将评审业务案例，最多可以批准 250 个存储帐户。

<sup>3</sup>每个订阅可以应用无限数量的标记。 每个资源或资源组的标记数仅限 15。 当标记数少于或等于 10,000 时，Resource Manager 仅返回订阅中[唯一标记名和值的列表](/rest/api/resources/tags#Tags_List)。 不过，即使数目超过 10,000，也仍可通过标记查找资源。  

<sup>4</sup>使用 Azure 资源组和 Azure Resource Manager 时不再需要这些功能。

> [!NOTE]
> 必须强调的是，虚拟机核心数既有区域总数限制，也有区域按大小系列（Dv2、F 等）限制，这两种限制单独实施。  例如，某个订阅的美国东部 VM 核心总数限制为 30，A 系列核心数限制为 30，D 系列核心数限制为 30。  该订阅可以部署 30 个 A1 VM，或者 30 个 D1 VM，或者同时部署这二者，但其总数不能超过 30 个核心（例如，10 个 A1 VM 和 20 个 D1 VM）。  
> <!-- -->
> 
> 

