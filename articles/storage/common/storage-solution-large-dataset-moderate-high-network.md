---
title: 适用于大型数据集以及中高速网络带宽的 Azure 数据传输选项 | Microsoft Docs
description: 了解在环境中有中高速网络带宽且正在计划传输大型数据集时，如何选择数据传输的 Azure 解决方案。
services: storage
author: alkohli
ms.service: storage
ms.subservice: blob
ms.topic: article
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: 7243edbe0b51a3cca69bec018d6cbb15e9aa1674
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2018
ms.locfileid: "53263502"
---
# <a name="data-transfer-for-large-datasets-with-moderate-to-high-network-bandwidth"></a>具有中高速网络带宽的大型数据集的数据传输
 
本文概述了在环境中有中高速网络带宽并且正在计划传输大型数据集时的数据传输解决方案。 本文还介绍了针对此情况的推荐数据传输选项和相应的关键功能矩阵。

若要查看所有可用数据传输选项的概述，请转到[选择一个 Azure 数据传输解决方案](storage-choose-data-transfer-solution.md)。

## <a name="scenario-description"></a>方案描述

大型数据集指的是 TB 到 PB 级的数据大小。 中高速网络带宽是指 100 Mbps 到 10 Gbps 的网络带宽。

## <a name="recommended-options"></a>推荐选项

此方案中推荐的选项取决于是否具有中速网络带宽或高速网络带宽。

### <a name="moderate-network-bandwidth-100-mbps---1-gbps"></a>中速网络带宽 (100 Mbps - 1 Gbps)

使用中速网络带宽，需要预测通过该网络传输数据的时间。

使用下表估计时间，并根据此时间，在离线传输或脱机传输之间进行选择。 下表显示各种可用网络带宽（假设利用率为 90%）的网络数据传输的预测时间。  

![网络传输或脱机传输](media/storage-solution-large-dataset-low-network/storage-network-or-offline-transfer.png)

- 如果预测网络传输速度很慢，应使用物理设备。 在这种情况下，推荐的选项是 Azure Data Box 系列的离线传输设备或使用自己的磁盘执行 Azure 导入/导出。

    - **用于脱机传输的 Azure Data Box 系列** – 当受到时间、网络可用性或成本的限制时，使用 Microsoft 提供的 Data Box 设备将大量数据移动到 Azure。 使用工具（例如 Robocopy）复制本地数据。 可从 Data Box Disk、Data Box 或 Data Box Heavy 中进行选择，具体取决于要传输的数据的大小。
    - **Azure 导入/导出** - 通过寄送自己的磁盘驱动器，使用 Azure 导入/导出服务安全地将大量数据导入 Azure Blob 存储和 Azure 文件。 此外，还可以使用此服务将数据从 Azure Blob 存储传输到磁盘驱动器，然后再寄送到本地站点。

- 如果预测出网络传输比较合理，那么可以使用以下在[高速网络带宽](#high-network-bandwidth)中详细介绍的工具。


### <a name="high-network-bandwidth-1-gbps---100-gbps"></a>高速网络带宽 (1 Gbps - 100 Gbps)

如果可用的网络带宽为高速带宽，则使用下面的一种工具。

- AzCopy - 使用此命令行工具在保证最佳性能的同时轻松向/从 Azure Blob、文件和表存储复制数据。 AzCopy 支持并发度和并行度，并且可以在复制操作中断后进行恢复。
- Azure 存储 REST API/SDK - 生成应用程序时，可以对照着 Azure 存储 REST API 开发应用程序，并使用以多种语言提供的 Azure SDK。
- **用于在线传输的 Azure Data Box 系列** – Data Box Edge 和 Data Box Gateway 是可以将数据移入和移出 Azure 的联机网络设备。 在上传之前同时需要持续引入和预处理数据时，请使用 Data Box Edge 物理设备。 Data Box Gateway 是该设备的虚拟版本，具有相同的数据传输功能。 每种情况下，数据传输都是由设备进行管理。
- **Azure 数据工厂** - 如果需要业务流程和企业级监视功能，应使用数据工厂横向扩展传输操作。 使用数据工厂在多个 Azure 服务、本地或两者的组合之间定期传输文件。 使用数据工厂，可以创建和计划数据驱动型工作流（称为管道），以便从不同的数据存储引入数据并自动执行数据移动和数据传输。

## <a name="comparison-of-key-capabilities"></a>关键功能比较

下表总结了推荐选项的主要功能差异。

### <a name="moderate-network-bandwidth"></a>中速网络带宽

如果使用脱机数据传输，请通过下表了解主要功能之间的差异。

|                                     |    Data Box Disk（预览版）    |    Data Box                                      |    Data Box Heavy（预览版）              |    导入/导出                       |
|-------------------------------------|---------------------------------|--------------------------------------------------|------------------------------------------|----------------------------------------|
|    数据大小                        |    最多为 35 TB                 |    每个设备最多 80 TB                       |    每个设备最多 800 TB               |    变量                            |
|    数据类型                        |    Azure Blob                  |    Azure Blob<br>Azure 文件                    |    Azure Blob<br>Azure 文件            |    Azure Blob<br>Azure 文件          |
|    外形规格                      |    每笔订单 5 个 SSD             |    每笔订单 1 X 50 磅 桌面大小的设备    |    每笔订单 1 X ~500 磅 大型设备    |    每笔订单最多 10 个 HDD/SSD        |
|    初始设置时间               |    低 <br>（15 分钟）            |    低等到中等 <br> （<30 分钟）               |    中等<br>（1-2 小时）               |    中等到困难<br>（不定） |
|    将数据发送到 Azure               |    是                          |    是                                           |    是                                   |    是                                 |
|    从 Azure 导出数据           |    否                           |    否                                            |    否                                    |    是                                 |
|    加密                       |    AES 128 位                  |    AES 256 位                                   |    AES 256 位                           |    AES 128 位                         |
|    硬件                         |     Microsoft 提供          |    Microsoft 提供                            |    Microsoft 提供                    |    客户提供                   |
|    网络接口                |    USB 3.1/SATA                 |    RJ 45，SFP+                                   |    RJ45，QSFP+                           |    SATA II/SATA III                    |
|    合作伙伴集成              |    部分                         |    [高](https://azuremarketplace.microsoft.com/campaigns/databox/azure-data-box)                                          |    [高](https://azuremarketplace.microsoft.com/campaigns/databox/azure-data-box)                                  |    部分                                |
|    寄送                         |    Microsoft 托管            |    Microsoft 托管                             |    Microsoft 托管                     |    由客户管理                    |
| 数据移动时使用         |在商务区域内|在商务区域内|在商务区域内|跨地理区域，例如美国到欧洲|
|    定价                          |    [定价](https://azure.microsoft.com/pricing/details/storage/databox/disk/)                    |   [定价](https://azure.microsoft.com/pricing/details/storage/databox/)                                      |  [定价](https://azure.microsoft.com/pricing/details/storage/databox/heavy/)                               |   [定价](https://azure.microsoft.com/pricing/details/storage-import-export/)                            |


如果使用在线数据传输，请使用以下部分中的表格获得高速网络带宽。

### <a name="high-network-bandwidth"></a>高速网络带宽

|                                     |    Tools AzCopy， <br>Azure PowerShell， <br>Azure CLI             |    Azure 存储 REST API，SDK                   |    Data Box Gateway 或 Data Box Edge（预览版）           |    Azure 数据工厂                                            |
|-------------------------------------|------------------------------------|----------------------------------------------|----------------------------------|-----------------------------------------------------------------------|
|    数据类型                  |    Azure Blob、Azure 文件、Azure 表    |    Azure Blob、Azure 文件、Azure 表    |    Azure Blob、Azure 文件                           |   支持 70 多个用于数据存储和格式的数据连接器    |
|    外形规格                |    命令行工具                        |    编程接口                    |    Microsoft 提供虚拟 <br>或物理设备     |    Azure 门户中的服务                                            |
|    初始一次性设置     |    简单               |    中等                       |    简单（< 30 分钟）到中等（1-2 小时）            |    广泛                                                          |
|    数据预处理              |    否                                        |    否                                        |    是（使用 Edge 计算）                               |    是                                                                |
|    从其他云传输       |    否                                        |    否                                        |    否                                                    |    是                                                                |
|    用户类型                        |    IT 专家或开发人员                                       |    Dev                                       |    IT 专家                                                |    IT 专家                                                             |
|    定价                          |    免费，收取数据出口费用         |    免费，收取数据出口费用         |    [定价](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                                               |    [定价](https://azure.microsoft.com/pricing/details/data-factory/)                                                            |

## <a name="next-steps"></a>后续步骤

- [了解如何使用导入/导出转移数据](/azure/storage/common/storage-import-export-data-to-blobs)。
- 了解如何

    - [使用 Data Box Disk 传输数据](https://docs.microsoft.com/azure/databox/data-box-disk-quickstart-portal)。
    - [使用 Data Box 传输数据](https://docs.microsoft.com/azure/databox/data-box-quickstart-portal)。
- [使用 AzCopy 传输数据](/azure/storage/common/storage-use-azcopy-v10)。
- 了解如何：
    - [使用 Data Box Gateway 传输数据](https://docs.microsoft.com/azure/databox-online/data-box-gateway-deploy-add-shares.md)。
    - [在将数据发送到 Azure 之前使用 Data Box Edge 转换数据](https://docs.microsoft.com/azure/databox-online/data-box-edge-deploy-configure-compute)。
- [了解如何使用 Azure 数据工厂传输数据](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal)。
- 使用 REST API 传输数据

    - [在 .NET 中](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
    - [在 Java 中](https://docs.microsoft.com/java/api/overview/azure/storage/client)