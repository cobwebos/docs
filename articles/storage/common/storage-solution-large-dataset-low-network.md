---
title: 适用于大型数据集具有低或无网络带宽的 Azure 数据传输选项 | Microsoft Docs
description: 了解如何在环境中限制到无网络带宽且想要传输大型数据集时，选择数据传输的 Azure 解决方案。
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 6922d49affdde86728b41ef1d3d0c6a2da5c323e
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2019
ms.locfileid: "58805085"
---
# <a name="data-transfer-for-large-datasets-with-low-or-no-network-bandwidth"></a>具有低或无网络宽带的大型数据集的数据传输
 
本文概述了在环境中已限制为无网络宽带并且正在计划传输大型数据集时的数据传输解决方案。 本文还介绍了针对此情况的推荐数据传输选项和相应的关键功能矩阵。

若要查看所有可用数据传输选项的概述，请转到[选择一个 Azure 数据传输解决方案](storage-choose-data-transfer-solution.md)。

## <a name="offline-transfer-or-network-transfer"></a>脱机传输或网络传输

大型数据集意味着具有几个 TB 到几个 PB 的数据。 网络带宽已限制为无、网络速度较慢或不可靠。 此外：

- 受到来自 Internet 服务提供商 (ISP) 的网络传输成本的限制。
- 安全或组织政策不允许在处理敏感数据时使用出站连接。

在所有上述实例中，使用物理设备进行一次性大容量数据传输。 从 Microsoft 提供的 Data Box Disk、Data Box、Data Box Heavy 设备中进行选择，或者使用你自己的磁盘进行导入/导出。

若要确认物理设备是否是正确的选项，请使用下表。 它显示各种可用宽带（假设利用率为 90%）的网络数据传输的预测时间。 如果预测网络传输速度很慢，应使用物理设备。  

![网络传输或脱机传输](media/storage-solution-large-dataset-low-network/storage-network-or-offline-transfer.png)

## <a name="recommended-options"></a>推荐选项

此方案中可用的选项是用于 Azure Data Box 脱机传输或 Azure 导入/导出的设备。

- **用于脱机传输的 Azure Data Box 系列** – 当受到时间、网络可用性或成本的限制时，使用 Microsoft 提供的 Data Box 设备将大量数据移动到 Azure。 使用工具（例如 Robocopy）复制本地数据。 可从 Data Box Disk、Data Box 或 Data Box Heavy 中进行选择，具体取决于要传输的数据的大小。
- **Azure 导入/导出** - 通过寄送自己的磁盘驱动器，使用 Azure 导入/导出服务安全地将大量数据导入 Azure Blob 存储和 Azure 文件。 此外，还可以使用此服务将数据从 Azure Blob 存储传输到磁盘驱动器，然后再寄送到本地站点。

## <a name="comparison-of-key-capabilities"></a>关键功能比较

下表汇总了各项关键功能方面的差异。

|                                     |    Data Box Disk      |    Data Box                                      |    Data Box Heavy              |    导入/导出                       |
|-------------------------------------|---------------------------------|--------------------------------------------------|------------------------------------------|----------------------------------------|
|    数据大小                        |    最多为 35 TB                 |    每个设备最多 80 TB                       |    每个设备最多 800 TB               |    Variable                            |
|    数据类型                        |    Azure Blob                  |    Azure Blob<br>Azure 文件                    |    Azure Blob<br>Azure 文件            |    Azure Blob<br>Azure 文件          |
|    外形规格                      |    每笔订单 5 个 SSD             |    每笔订单 1 X 50 磅 桌面大小的设备    |    每笔订单 1 X ~500 磅 大型设备    |    每笔订单最多 10 个 HDD/SSD        |
|    初始设置时间               |    低 <br>（15 分钟）            |    低等到中等 <br> （<30 分钟）               |    中等<br>（1-2 小时）               |    中等到困难<br>（不定） |
|    将数据发送到 Azure               |    是                          |    是                                           |    是                                   |    是                                 |
|    从 Azure 导出数据           |    否                           |    否                                            |    否                                    |    是                                 |
|    加密                       |    AES 128 位                  |    AES 256 位                                   |    AES 256 位                           |    AES 128 位                         |
|    硬件                         |     Microsoft 提供          |    Microsoft 提供                            |    Microsoft 提供                    |    客户提供                   |
|    网络接口                |    USB 3.1/SATA                 |    RJ 45，SFP+                                   |    RJ45，QSFP+                           |    SATA II/SATA III                    |
|    合作伙伴集成              |    部分                         |    [高](https://azuremarketplace.microsoft.com/campaigns/databox/azure-data-box)                                          |    [高](https://azuremarketplace.microsoft.com/campaigns/databox/azure-data-box)                                  |    部分                                |
|    正在装运                         |    Microsoft 托管            |    Microsoft 托管                             |    Microsoft 托管                     |    由客户管理                    |
| 数据移动时使用         |在商务区域内|在商务区域内|在商务区域内|跨地理区域，例如美国到欧洲|
|    定价                          |    [价格](https://azure.microsoft.com/pricing/details/storage/databox/disk/)                    |   [价格](https://azure.microsoft.com/pricing/details/storage/databox/)                                      |  [价格](https://azure.microsoft.com/pricing/details/storage/databox/heavy/)                               |   [价格](https://azure.microsoft.com/pricing/details/storage-import-export/)                            |


## <a name="next-steps"></a>后续步骤

- 了解如何

    - [使用 Data Box Disk 传输数据](https://docs.microsoft.com/azure/databox/data-box-disk-quickstart-portal)。
    - [使用 Data Box 传输数据](https://docs.microsoft.com/azure/databox/data-box-quickstart-portal)。
    - [通过导入/导出转移数据](/azure/storage/common/storage-import-export-data-to-blobs).
