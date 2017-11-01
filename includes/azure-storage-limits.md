| 资源 | 默认限制 |
| --- | --- |
| 每个订阅的存储帐户数 | 200<sup>1</sup> |
| 最大存储帐户容量 | 500 TiB<sup>2</sup> |
| 每个存储帐户的 Blob 容器、Blob、文件共享、表、队列、实体或消息数上限 | 无限制 |
| 每个存储帐户的最大入口 <sup>3</sup>（美国区域） | 如果已启用 GRS/ZRS<sup>4</sup>，则为 10 Gbps；LRS<sup>2</sup> 为 20 Gbps |
| 每个存储帐户的最大出口 <sup>3</sup>（美国区域） | 如果已启用 GRS/GRS/ZRS<sup>4</sup>，则为 20 Gbps；LRS<sup>2</sup> 为 30 Gbps |
| 每个存储帐户的最大入口<sup>3</sup>（非美国区域） | 如果已启用 GRS/ZRS<sup>4</sup>，则为 5 Gbps；LRS<sup>2</sup> 为 10 Gbps |
| 每个存储帐户的最大出口<sup>3</sup>（非美国区域） | 如果已启用 RA-GRS/GRS/ZRS<sup>4</sup>，则为 10 Gbps；LRS<sup>2</sup> 为 15 Gbps |

<sup>1</sup>包括标准和高级存储帐户。 如果需要的存储帐户超过 100 个，请通过 [Azure 支持](https://azure.microsoft.com/support/faq/)提出请求。 Azure 存储团队将评审业务案例，最多可以批准 250 个存储帐户。 

<sup>2</sup> 若要扩大标准存储帐户的容量，使之突破广告中宣传的容量、入口/出口以及请求速率方面的限制，请通过 [Azure 支持](https://azure.microsoft.com/support/faq/)提交请求。 Azure 存储团队会对请求进行审核，根据具体情况批准提高限制的请求。

<sup>3</sup> 仅受帐户的传入/传出限制的限制。 *传入*是指发送到存储帐户的所有数据（请求）。 “传出”是指从存储帐户接收的所有数据（响应）。  

<sup>4</sup>Azure 存储冗余选项包括：
* **RA-GRS**：读取访问异地冗余存储。 如果已启用 RA-GRS，辅助位置的出口目标与主要位置的出口目标相同。
* **GRS**：异地冗余存储。 
* **ZRS**：区域冗余存储。 仅可用于块 Blob。 
* **LRS**：本地冗余存储。 