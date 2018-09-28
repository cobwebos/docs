---
title: Microsoft Azure Data Box Gateway 概述 | Microsoft Docs
description: 介绍 Azure Data Box Gateway - 一种可以将数据传输到 Azure 的虚拟设备存储解决方案
services: databox-edge-gateway
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox-edge-gateway
ms.devlang: NA
ms.topic: overview
ms.custom: ''
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: 9c12674a66582ede04b4cf9d311238d61816afec
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46969535"
---
# <a name="what-is-azure-data-box-gateway-preview"></a>什么是 Data Box Gateway（预览版）？ 

Azure Data Box Gateway 是一种可以将数据无缝地发送到 Azure 的存储解决方案。 本文概述了 Azure Data Box Gateway 解决方案、优势、关键功能以及可以部署此设备的场景。 

Data Box Gateway 是一种虚拟设备，基于在虚拟化环境或虚拟机监控程序中预配的虚拟机。 该虚拟设备驻留在本地，可以使用 NFS 和 SMB 协议将数据写入其中。 然后，设备会将你的数据传输到 Azure 块 blob、页 blob 或 Azure 文件。 

> [!IMPORTANT]
> Data Box Gateway 以预览版提供。 在部署此解决方案之前，请查看[预览版的使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="use-cases"></a>用例

可利用 Data Box Gateway 将数据传输到云，例如云存档、灾难恢复，或根据需要以云规模处理数据。 下面是可以使用 Data Box Gateway 来传输数据的各种场景。

- **云存档** - 使用 Data Box Gateway 以安全有效的方式将数百 TB 的数据复制到 Azure 存储。 对于存档场景，可以一次性或持续地引入数据。

- **数据聚合** - 将来自多个源的数据聚合到 Azure 存储中的单个位置进行数据处理和分析。  

- **与本地工作负载集成** - 与使用云存储并需要对常用文件进行本地访问的本地工作负载（如备份和还原）集成。 

## <a name="benefits"></a>优点

Data Box Gateway 具有以下优势：

- **轻松传输数据** - 可以轻松地将数据移入和移出 Azure 存储，就像使用本地网络共享一样简单。  
- **高性能** - 通过与 Azure 之间的高性能传输，避免了网络数据传输的麻烦。 
- **快速访问** - 缓存最近的文件，以快速访问本地文件。  
- **带宽用量受限** - 即使在营业高峰期网络用量受限，也能将数据写入 Azure。  

## <a name="key-capabilities"></a>关键功能

Data Box Gateway 具有以下功能：

|功能 |Description  |
|---------|---------|
|Speed     | 完全自动化，高度优化的数据传输和带宽。|
|支持的协议     | 支持用于数据引入的标准 SMB 和 NFS 协议。 <br> 有关支持的版本的详细信息，请转到 [Data Box Gateway 系统要求](data-box-gateway-system-requirements.md)。|
|数据访问     | 使用云 API 从 Azure 存储 Blob 和 Azure 文件中直接访问数据，以便在云中进行其他数据处理。|
|快速访问     | 设备带有本地缓存，以便快速访问最近使用的文件。|
|离线上传     | 离线模式支持离线上传方案。|
|数据刷新     | 可以使用云中的最新内容刷新本地文件。|
|加密    | BitLocker 支持本地加密数据，并通过 http 安全地将数据传输到云中       |
|复原     | 内置网络复原能力        |


## <a name="specifications"></a>规范

Data Box Gateway 虚拟设备具有以下规范：

| 规范                                          | Description              |
|---------------------------------------------------------|--------------------------|
| 虚拟处理器（核心数）   | 最低为 4 核 |            
| 内存  | 最低为 8 GB|
| 可用性|单节点|
| 磁盘| OS 磁盘：250 GB <br> 数据磁盘：最低为 2 TB、配置精简，并且必须由 SSD 提供支持|
| 网络接口|1 个或多个虚拟网络接口|
| 本机文件共享协议|SMB 和 NFS  |
| 安全| 必须进行身份验证才能访问设备和数据 <br> 使用 AES-256 位加密动态数据进行加密|
| 管理| 本地 Web UI - 设备的初始设置、诊断和电源管理 <br> Azure 门户 - Data Box Gateway 设备的日常管理       |


## <a name="components"></a>组件

Data Box Gateway 解决方案包括 Data Box Gateway 资源、Data Box Gateway 虚拟设备和本地 Web UI。

* **Data Box Gateway 虚拟设备** - 一种基于在虚拟化环境或虚拟机监控程序中预配的虚拟机的设备，允许将数据发送到 Azure。 
    
* **Data Box Gateway 资源** - Azure 门户的资源，使用该资源可以通过 Web 界面（可从不同的地理位置访问该界面）管理 Data Box Gateway 设备。 使用 Data Box Gateway 资源可以创建和管理资源、查看和管理设备与警报，以及管理共享。  

    <!--![The Data Box Gateway service in Azure portal](media/data-box-overview/data-box-Gateway-service1.png)-->

    <!--For more information, go to [Use the Data Box Gateway service to administer your Data Box Gateway device](data-box-gateway-portal-ui-admin.md).-->

* **Data Box 本地 Web UI** - 使用本地 Web UI 可以运行诊断、关闭和重启 Data Box Gateway 设备、查看复制日志，并联系 Microsoft 支持部门来提出服务请求。

    <!--![The Data Box Gateway local web UI](media/data-box-gateway-overview/data-box-gateway-local-web-ui.png)-->

    <!-- For information about using the web-based UI, go to [Use the web-based UI to administer your Data Box](data-box-gateway-portal-ui-admin.md).-->


## <a name="region-availability"></a>上市区域

将数据传输到的 Data Box Edge 物理设备、Azure 资源和目标存储帐户不一定非要位于同一区域。

- **资源可用性** - 对于此版本，Data Box Edge 资源可以在以下区域获得：
    - **美国** - 美国西部 2 区和美国东部
    - **欧盟** - 西欧
    - **亚太** - 东南亚

- **目标存储帐户** - 存储数据的存储帐户可在所有 Azure 区域中获得。 

    存储帐户存储 Data Box 数据的区域应靠近设备所在位置，以获得最佳性能。 远离设备的存储帐户会导致长时间的延迟和性能下降。 


## <a name="next-steps"></a>后续步骤

- 查看 [Data Box Gateway 系统要求](data-box-gateway-system-requirements.md)。
- 了解 [Data Box Gateway 限制](data-box-gateway-limits.md)。
- 在 Azure 门户中部署 [Azure Data Box Gateway](data-box-gateway-deploy-prep.md)。




