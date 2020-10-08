---
title: Microsoft Azure Stack Edge Pro R 概述 | Microsoft Docs
description: 介绍 Microsoft Azure Stack Edge Pro R 设备，这是一种使用物理设备进行 Azure 网络传输的存储解决方案，适用于军事应用程序。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 09/23/2020
ms.author: alkohli
ms.openlocfilehash: 11ed87f8cf5aabb86f709d938acc4c31b737ca91
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91318580"
---
# <a name="what-is-the-azure-stack-edge-pro-r"></a>什么是 Azure Stack Edge Pro R？

Azure Stack Edge Pro R 是一种专为在恶劣环境中使用而设计的加固型边缘计算设备。 Azure Stack Edge Pro R 以“硬件即服务”解决方案的形式提供。 Microsoft 为客户提供了一个云托管设备，该设备充当网络存储网关并具有内置的图形处理单元 (GPU)，支持加速 AI 推理。

本文概述了 Azure Stack Edge Pro R 解决方案、重要功能以及可以部署此设备的场景。


## <a name="key-capabilities"></a>关键功能

Azure Stack Edge Pro R 具有以下功能：

|功能 |说明  |
|---------|---------|
|加固型硬件| 专为恶劣环境设计的加固型服务器类硬件。 便携传输案例中包含的设备。 |
|云托管     |设备和服务通过 Azure 门户进行管理。|
|Edge 计算工作负载   |允许分析、处理、筛选数据。 支持 VM 和容器化工作负载。|
|加速 AI 推断| 由 Nvidia T4 GPU 提供支持。|
|数据访问     | 使用云 API 从 Azure 存储 Blob 和 Azure 文件中直接访问数据，以便在云中进行其他数据处理。 设备带有本地缓存，以便快速访问最近使用的文件。|
|断开连接模式| 可以选择通过 Azure Stack Hub 管理设备和服务。 在脱机模式下部署、运行和管理应用程序。 <br> 离线模式支持离线上传方案。|
|支持的文件传输协议     |支持用于数据引入的标准 SMB、NFS 和 REST 协议。 <br> 有关支持的版本的详细信息，请转到 [Azure Stack Edge Pro R 系统需求](azure-stack-edge-gpu-system-requirements.md)。|
|数据刷新     | 可以使用云中的最新内容刷新本地文件。|
|双重加密    | 使用自加密驱动器提供第一层加密。 VPN 提供第二层加密。 BitLocker 支持本地加密数据，并通过 https 安全地将数据传输到云中。|
|带宽限制| 中止以限制在高峰时段使用带宽。|

<!--|Scale out file server| Available as 1-node and 4-node cluster configurations|-->

## <a name="use-cases"></a>用例

下面介绍各种方案，其中 Azure Stack Edge Pro R 可用于加快边缘的机器学习 (ML) 推理并对数据进行预处理，然后再将该数据发送到 Azure。

- 使用 Azure 机器学习进行推理 - 借助 Azure Stack Edge Pro R，可以运行 ML 模型以获得可在将数据发送到云之前执行的快速结果。 可以选择传输完整的数据集以继续重新训练并改进 ML 模型。 有关如何在 Azure Stack Edge Pro R 设备上使用 Azure ML 硬件加速模型的详细信息，请参阅[在 Azure Stack Edge Pro R 上部署 Azure ML 硬件加速模型](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-fpga-web-service#deploy-to-a-local-edge-server)。

- **预处理数据** - 转换数据，然后将数据发送到 Azure，以创建更具操作的数据集。 使用预处理可以：

    - 聚合数据。
    - 修改数据，例如删除个人数据。
    - 用于优化存储和带宽，或用于进一步分析的子集数据。
    - 分析和应对 IoT 事件。

- 通过网络将数据传输到 Azure - 使用 Azure Stack Edge Pro R 可以既快速又轻松地将数据传输到 Azure，以实现其他计算和分析或用于存档。

## <a name="components"></a>组件

Azure Stack Edge Pro R 解决方案包括 Azure Stack Edge 资源、Azure Stack Edge Pro R 加固型物理设备和本地 Web UI。

- **Azure Stack Edge Pro R 物理设备** - 加固型传输案例中包含的 1 节点计算和存储设备。 还提供了可选的不间断电源 (UPS)。

    ![Azure Stack Edge Pro R 单节点设备](media/azure-stack-edge-j-series-overview/device-image-1.png)

- Azure Stack Edge 资源 - Azure 门户中的一种资源，使用该资源可以通过 Web 界面（可从不同的地理位置访问该界面）管理加固型 Azure Stack Edge Pro R 设备。 使用 Azure Stack Edge 资源可以创建和管理资源、查看和管理设备与警报，以及管理共享。  

- **Azure Stack Edge Pro R 本地 Web UI** - Azure Stack Edge Pro R 设备上基于浏览器的本地用户界面，主要用于设备的初始配置。 使用本地 Web UI 还可以运行诊断、关闭和重启 Azure Stack Edge Pro 设备、查看复制日志，并联系 Microsoft 支持部门以提出服务请求。


## <a name="region-availability"></a>上市区域

将数据传输到其中的 Azure Stack Edge Pro R 物理设备、Azure 资源和目标存储帐户不一定非要位于同一区域。

- **资源可用性** - 有关 Azure Stack Edge 资源可用的所有区域的列表，请转到[各区域提供的 Azure 产品](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)。 

- **设备可用性** - 有关 Azure Stack Edge Pro R 设备可供使用的所有国家/地区的列表，请转到 [Azure Stack Edge Pro R 定价](https://azure.microsoft.com/pricing/details/azure-stack/edge/#azureStackEdgeProR)的“Azure Stack Edge Pro R”选项卡中的“可用性”部分。

- **目标存储帐户** - 存储数据的存储帐户可在所有 Azure 区域中获得。 存储帐户存储 Azure Stack Edge Pro R 数据的区域应靠近设备所在位置，以便获得最佳性能。 远离设备的存储帐户会导致长时间的延迟和性能下降。

## <a name="next-steps"></a>后续步骤

- 查看 [Azure Stack Edge Pro R 系统需求](azure-stack-edge-gpu-system-requirements.md)。
<!--- Understand the [Azure Stack Edge Pro R limits](azure-stack-edge-limits.md).-->

