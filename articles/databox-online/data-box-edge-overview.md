---
title: Microsoft Azure Data Box Edge 概述 | Microsoft Docs
description: 介绍 Azure Data Box Edge - 一种使用物理设备通过网络将数据传输到 Azure 的存储解决方案。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 03/27/2019
ms.author: alkohli
ms.openlocfilehash: 8e07678604797e7e2090f5fefcdb3f3adff40161
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2019
ms.locfileid: "58521765"
---
# <a name="what-is-azure-data-box-edge"></a>什么是 Azure Data Box Edge？ 

Azure Data Box Edge 是可用于处理数据并通过网络将其发送到 Azure 的存储解决方案。 本文概述 Data Box Edge 解决方案、其优势、重要功能以及可以部署此设备的场合。 

Data Box Edge 使用 Microsoft 提供的物理设备来加速安全数据传输。 物理设备驻留在本地，可以使用 NFS 和 SMB 协议将数据写入其中。 

Data Box Edge 具备 Data Box Gateway 的所有网关功能。 Data Box 额外装备了支持 AI 的边缘计算功能，可帮助分析、处理或筛选移到 Azure 块 Blob、页 Blob 或 Azure 文件的数据。  

## <a name="use-cases"></a>用例

Azure Data Box Edge 是具有网络数据传输功能的支持 AI 的边缘计算设备。 下面是可以使用 Data Box Edge 来传输数据的各种场合。

- **预处理数据** - 分析来自本地或 IoT 设备的数据，以快速获得结果，同时密切关注生成数据的位置。 Data Box Edge 将整个数据集传输到云中，以执行更高级的处理或更深入的分析。  使用预处理可以： 

    - 聚合数据。
    - 修改数据，例如，删除个人身份信息 (PII)。
    - 划分和传输在云中进行更深入分析所需的数据。
    - 分析和应对 IoT 事件。 

- **推理 Azure 机器学习** - 使用 Data Box Edge 可以运行机器学习 (ML) 模型，以快速获取可处理的数据，然后将数据发送到云中。 将传输整个数据集，以继续重新训练和改善机器学习模型。

- **通过网络将数据传输到 Azure** - 使用 Data Box Edge 可以快速轻松地将数据传输到 Azure，以实现其他计算和分析或存档目的。 

## <a name="benefits"></a>优点

Data Box Edge 具有以下优势：

- **轻松传输数据** - 可以轻松地将数据移入和移出 Azure 存储，就像使用本地网络共享一样。  
- **高性能** - 以较高的性能将数据传入和传出 Azure。 
- **快速访问** - 缓存最近的文件，以快速访问本地文件。  
- **带宽用量受限** - 即使在营业高峰期网络用量受限，也能将数据写入 Azure。  
- **转换数据** - 为移到 Azure 的数据启用分析、处理或筛选。

## <a name="key-capabilities"></a>关键功能

Data Box Edge 具有以下功能：

|功能 |说明  |
|---------|---------|
|高性能     | 完全自动化，高度优化的数据传输和带宽。|
|支持的协议     | 支持用于数据引入的标准 SMB 和 NFS 协议。 <br> 有关支持的版本的详细信息，请转到 [Data Box Edge 系统要求](https://aka.ms/dbe-docs)。|
|计算       |允许分析、处理、筛选数据。|
|数据访问     | 使用云 API 从 Azure 存储 Blob 和 Azure 文件中直接访问数据，以便在云中进行其他数据处理。|
|快速访问     | 设备带有本地缓存，以便快速访问最近使用的文件。|
|离线上传     | 离线模式支持离线上传方案。|
|数据刷新     | 可以使用云中的最新内容刷新本地文件。|
|加密    | BitLocker 支持本地加密数据，并通过 *http* 安全地将数据传输到云中。       |
|复原     | 内置网络复原能力。        |


## <a name="components"></a>组件

Data Box Edge 解决方案包括 Data Box Edge 资源、Data Box Edge 物理设备和本地 Web UI。

* **Data Box Edge 物理设备** - 可将 Microsoft 提供的 1U 机架安装式服务器配置为向 Azure 发送数据。 
    
* **Data Box Edge 资源** - Azure 门户中的一个资源，使用该资源可以通过 Web 界面（可从不同的地理位置访问该界面）管理 Data Box Edge 设备。 使用 Data Box Edge 资源可以创建和管理资源、查看和管理设备与警报，以及管理共享。  

    <!--![The Data Box Edge service in Azure portal](media/data-box-overview/data-box-Edge-service1.png)-->

    有关详细信息，请转到[使用 Data Box Edge 服务管理 Data Box Edge 设备](https://aka.ms/dbe-docs)。

* **Data Box 本地 Web UI** - 使用本地 Web UI 可以运行诊断、关闭和重启 Data Box Edge 设备、查看复制日志，并联系 Microsoft 支持部门来提出服务请求。

    <!--![The Data Box Edge local web UI](media/data-box-Edge-overview/data-box-Edge-local-web-ui.png)-->

    有关使用基于 Web 的 UI 的详细信息，请转到[使用基于 Web 的 UI 管理 Data Box](https://aka.ms/dbe-docs)。


## <a name="region-availability"></a>上市区域

将数据传输到的 Data Box Edge 物理设备、Azure 资源和目标存储帐户不一定非要位于同一区域。

- **资源可用性** - 对于此版本，Data Box Edge 资源可以在以下区域获得：
    - **美国** - 美国东部
    - **欧盟** - 西欧
    - **亚太** - 东南亚
    
    Data Box Gateway 也可以部署在 Azure 政府云中。 有关详细信息，请参阅[什么是 Azure 政府？](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)。
    
- **目标存储帐户** - 存储数据的存储帐户可在所有 Azure 区域中获得。 

    存储帐户存储 Data Box 数据的区域应靠近设备所在位置，以获得最佳性能。 远离设备的存储帐户会导致长时间的延迟和性能下降。 


## <a name="next-steps"></a>后续步骤

- 查看 [Data Box Edge 系统要求](data-box-edge-system-requirements.md)。
- 了解 [Data Box Edge 限制](data-box-edge-limits.md)。
- 在 Azure 门户中部署 [Azure Data Box Edge](data-box-edge-deploy-prep.md)。




