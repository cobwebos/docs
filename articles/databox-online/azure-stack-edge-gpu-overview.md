---
title: Microsoft Azure Stack Edge Pro with GPU 概述 | Microsoft Docs
description: 介绍 Azure Stack Edge Pro with GPU，这是一种使用物理设备进行 Azure 网络传输的存储解决方案。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 09/23/2020
ms.author: alkohli
ms.openlocfilehash: 8ae95518bab5ed9bc100a5902acf1fd1db631dbe
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91617156"
---
# <a name="what-is-azure-stack-edge-pro-with-gpu"></a>什么是 Azure Stack Edge Pro with GPU？

Azure Stack Edge Pro with GPU 是具有网络数据传输功能的支持 AI 的边缘计算设备。 本文概述了 Azure Stack Edge Pro 解决方案、其优势、重要功能以及可以部署此设备的场景。

Azure Stack Edge Pro with GPU 是一种硬件即服务解决方案。 Microsoft 为客户提供了一个云托管设备，该设备充当网络存储网关并具有内置的图形处理单元 (GPU)，支持加速 AI 推理。 

## <a name="use-cases"></a>用例

下面介绍各种方案，其中 Azure Stack Edge Pro 可用于加快边缘的机器学习 (ML) 推理并对数据进行预处理，然后再将该数据发送到 Azure。

- 使用 Azure 机器学习进行推理 - 借助 Azure Stack Edge Pro，可以运行 ML 模型以获得可在将数据发送到云之前执行的快速结果。 可以选择传输完整的数据集以继续重新训练并改进 ML 模型。 有关如何在 Azure Stack Edge Pro 设备上使用 Azure ML 硬件加速模型的详细信息，请参阅[在 Azure Stack Edge Pro 上部署 Azure ML 硬件加速模型](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-fpga-web-service#deploy-to-a-local-edge-server)。

- **预处理数据** - 在将数据发送到 Azure 之前通过计算选项（如容器化工作负载和虚拟机）转换数据，以创建更具操作性的数据集。 使用预处理可以： 

    - 聚合数据。
    - 修改数据，例如删除个人数据。
    - 用于优化存储和带宽，或用于进一步分析的子集数据。
    - 分析和应对 IoT 事件。 

- **通过网络将数据传输到 Azure** - 使用 Azure Stack Edge Pro 可以既快速又轻松地将数据传输到 Azure，以实现其他计算和分析或用于存档。 

## <a name="key-capabilities"></a>关键功能

Azure Stack Edge Pro 具有以下功能：

|功能 |说明  |
|---------|---------|
|加速 AI 推断| 由内置 GPU 启用（一个或两个取决于型号）。|
|边缘计算      |支持 VM 和容器化工作负载，以允许分析、处理和筛选数据。 |
|数据访问     | 使用云 API 从 Azure 存储 Blob 和 Azure 文件中直接访问数据，以便在云中进行其他数据处理。 设备带有本地缓存，以便快速访问最近使用的文件。|
|云托管     |设备和服务通过 Azure 门户进行管理。  |
|离线上传     | 离线模式支持离线上传方案。|
|支持的文件传输协议      | 支持用于数据引入的标准 SMB、NFS 和 REST 协议。 <br> 有关受支持版本的详细信息，请参阅 [Azure Stack Edge Pro 系统要求](azure-stack-edge-system-requirements.md)。|
|数据刷新     | 可以使用云中的最新内容刷新本地文件。|
|加密    | BitLocker 支持本地加密数据，并通过 *http* 安全地将数据传输到云中。|
|带宽限制| 中止以限制在高峰时段使用带宽。|
<!--|ExpressRoute | 通过 ExpressRoute 增加了安全性。 使用对等互连配置，从本地设备到云存储终结点的流量将通过 ExpressRoute 传输。 有关详细信息，请参阅 [ExpressRoute 概述](../expressroute/expressroute-introduction.md)。-->

## <a name="components"></a>组件

Azure Stack Edge Pro 解决方案包括 Azure Stack Edge 资源、Azure Stack Edge Pro 物理设备和本地 Web UI。

* **Azure Stack Edge Pro 物理设备** - Microsoft 提供的 1U 机架安装式服务器，可配置为将数据发送到 Azure。
    
* **Azure Stack Edge 资源** - Azure 门户中的一种资源，使用该资源可以通过 Web 界面（可从不同的地理位置访问该界面）管理 Azure Stack Edge Pro 设备。 使用 Azure Stack Edge 资源可以创建和管理资源、查看和管理设备与警报，以及管理共享。  

    有关详细信息，请转到[为 Azure Stack Edge Pro 设备创建订单](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource)。

* **Azure Stack Edge Pro 本地 Web UI** - Azure Stack Edge Pro 设备上基于浏览器的本地用户界面，主要用于设备的初始配置。 使用本地 Web UI 还可以运行诊断、关闭和重启 Azure Stack Edge Pro 设备、查看复制日志，并联系 Microsoft 支持部门以提出服务请求。

    有关使用基于 Web 的 UI 的信息，请转到[使用基于 Web 的 UI 管理 Azure Stack Edge Pro](azure-stack-edge-manage-access-power-connectivity-mode.md)。

## <a name="region-availability"></a>上市区域

将数据传输到其中的 Azure Stack Edge Pro 物理设备、Azure 资源和目标存储帐户不一定非要位于同一区域。

- **资源可用性** - 对于此预览版，美国东部、欧洲西部和东南亚地区中可使用此资源。

- **设备可用性** - 有关 Azure Stack Edge Pro 设备可用的所有国家/地区的列表，请转到 [Azure Stack Edge Pro 定价](https://azure.microsoft.com/pricing/details/azure-stack/edge/#azureStackEdgePro)的“Azure Stack Edge Pro”选项卡中的“可用性”部分 。
    
- **目标存储帐户** - 存储数据的存储帐户可在所有 Azure 区域中获得。 存储帐户存储 Azure Stack Edge Pro 数据的区域应靠近设备所在位置，以便获得最佳性能。 远离设备的存储帐户会导致长时间的延迟和性能下降。

## <a name="next-steps"></a>后续步骤

- 查看 [Azure Stack Edge Pro 系统要求](azure-stack-edge-gpu-system-requirements.md)。

- 了解 [Azure Stack Edge Pro 限制](azure-stack-edge-limits.md)。
- 在 Azure 门户中部署 [Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md)。
