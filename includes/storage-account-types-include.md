有两种类型的存储帐户：

### <a name="general-purpose-storage-accounts"></a>通用存储帐户
通用存储帐户有权使用单个帐户访问诸如表、队列、文件、Blob 和 Azure 虚拟机磁盘等 Azure 存储服务。 此类型存储帐户具有两个性能层：

* 标准存储性能层，允许存储表、队列、文件、Blob 和 Azure 虚拟机磁盘。
* 高级存储性能层，当前仅支持 Azure 虚拟机磁盘。 有关高级存储的详细概述，请参阅 [高级存储：适用于 Azure 虚拟机工作负荷的高性能存储](../articles/virtual-machines/windows/premium-storage.md) 。

### <a name="blob-storage-accounts"></a>Blob 存储帐户
Blob 存储帐户是将非结构化数据作为 Blob（对象）存储在 Azure 存储的专用存储帐户。 Blob 存储帐户类似于现有通用存储帐户，并且具有现在使用的所有卓越的耐用性、可用性、伸缩性和性能功能，包括用于块 blob 和追加 blob 的 100% API 一致性。 对于仅需要块 blob 或追加 blob 存储的应用程序，我们建议使用 Blob 存储帐户。

> [!NOTE]
> Blob 存储帐户仅支持块 blob 和追加 blob，不支持页 blob。
> 
> 

Blob 存储帐户公开 **访问层** 属性，该属性可在帐户创建过程中指定，并稍后根据需要进行修改。 根据数据访问模式可以指定两种类型的访问层：

* **热** 访问层，表示被更频繁地访问存储帐户中的对象。 该访问层允许以较低访问成本存储数据。
* **冷** 访问层，表示将不经常访问存储帐户中的对象。 该访问层允许以较低数据存储成本存储数据。

如果数据的使用模式有所更改，也可以随时在这些访问层之间切换。 更改访问层可能会产生额外费用。 有关更多详细信息，请参阅 [Blob 存储帐户的定价和计费](../articles/storage/blobs/storage-blob-storage-tiers.md#pricing-and-billing) 。

有关 Blob 存储帐户的更多详细信息，请参阅 [Azure Blob 存储：冷层和热层](../articles/storage/blobs/storage-blob-storage-tiers.md)。

必须具有 Azure 订阅（这是允许访问各种 Azure 服务的计划），然后才能创建存储帐户。 可通过 [免费帐户](https://azure.microsoft.com/pricing/free-trial/)开始使用 Azure。 一旦决定购买某个订阅计划，可从各种 [购买选项](https://azure.microsoft.com/pricing/purchase-options/)中进行选择。 如果是 [MSDN 订户](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)，则可以获得免费的月度信用，可以将其用于各种 Azure 服务，包括 Azure 存储。 有关批量定价的信息，请参阅 [Azure 存储定价 ](https://azure.microsoft.com/pricing/details/storage/) 。

若要了解如何创建存储帐户，请参阅 [创建存储帐户](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account) 获取更多详细信息。 通过单个订阅，最多可以创建 200 个唯一命名的存储帐户。 有关存储帐户限制的详细信息，请参阅 [Azure 存储可伸缩性和性能目标](../articles/storage/common/storage-scalability-targets.md) 。

