---
title: Azure Stack Edge Mini R 概述 | Microsoft Docs
description: 介绍 Azure Stack Edge Mini R，这是一种使用带有电池的便携式物理设备通过 Wi-Fi 传输到 Azure 的存储解决方案，适用于军事应用程序。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 09/22/2020
ms.author: alkohli
ms.openlocfilehash: 20efae411ae4d2fae9bf3b5e69dbfdd98da1603a
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90985634"
---
# <a name="what-is-the-azure-stack-edge-mini-r"></a>什么是 Azure Stack Edge Mini R？

Azure Stack Edge Mini R 是一种硬件即服务解决方案。 Microsoft 为你提供了一款具有内置视觉处理单元 (VPU) 且由云管理的超便携式加固型设备，该设备支持加速 AI 推理并具有网络存储网关的所有功能。 这些设备适合在恶劣环境中使用，并针对 AI、分析和无服务器计算进行了优化。

本文概述了 Azure Stack Edge Mini R 解决方案、重要功能以及可以部署此设备的场景。


## <a name="key-capabilities"></a>关键功能

Azure Stack Edge Mini R 具有以下功能：

|功能 |说明  |
|---------|---------|
|加固型硬件| 专为恶劣环境而设计的加固型硬件。|
|超便携| 超便携、电池供电的外形规格。|
|云托管|设备和服务通过 Azure 门户进行管理。|
|Edge 计算工作负载|允许分析、处理、筛选数据。<br>支持 VM 和容器化工作负载。 |
|加速 AI 推断| 由 Intel Movidius Myriad X VPU 提供支持。|
|有线和无线 | 允许进行有线数据传输和无线数据传输。|
|数据访问     | 使用云 API 从 Azure 存储 Blob 和 Azure 文件中直接访问数据，以便在云中进行其他数据处理。 设备带有本地缓存，以便快速访问最近使用的文件。|
|断开连接模式|  可以选择通过 Azure Stack Hub 管理设备和服务。 在脱机模式下部署、运行和管理应用程序。 <br> 离线模式支持离线上传方案。|
|支持的协议     |支持用于数据引入的标准 SMB、NFS 和 REST 协议。 <br> 有关支持的版本的详细信息，请转到 [Azure Stack Edge Mini R 系统要求](azure-stack-edge-gpu-system-requirements.md)。|
|数据刷新     | 可以使用云中的最新内容刷新本地文件。|
|双重加密    | 使用自加密驱动器提供第一层加密。 VPN 提供第二层加密。 BitLocker 支持本地加密数据，并通过 https 安全地将数据传输到云中。|
|带宽限制| 中止以限制在高峰时段使用带宽。|

## <a name="use-cases"></a>用例

下面介绍各种方案，其中 Azure Stack Edge Mini R 可用于加快边缘的机器学习 (ML) 推理并对数据进行预处理，然后再将该数据发送到 Azure。

- 使用 Azure 机器学习进行推理 - 借助 Azure Stack Edge Mini R，可以运行 ML 模型以获得可在将数据发送到云之前执行的快速结果。 可以选择传输完整的数据集以继续重新训练并改进 ML 模型。 有关如何在 Azure Stack Edge Mini R 设备上使用 Azure ML 硬件加速模型的详细信息，请参阅[在 Azure Stack Edge Mini R 上部署 Azure ML 硬件加速模型](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-fpga-web-service#deploy-to-a-local-edge-server)。

- 预处理数据 - 在将数据发送到 Azure 之前通过计算选项（如容器或虚拟机）转换数据，以创建更具操作性的数据集。 使用预处理可以：

    - 聚合数据。
    - 修改数据，例如删除个人数据。
    - 用于优化存储和带宽，或用于进一步分析的子集数据。
    - 分析和应对 IoT 事件。

- 通过网络将数据传输到 Azure - 使用 Azure Stack Edge Mini R 可以既快速又轻松地将数据传输到 Azure，以实现其他计算和分析或用于存档。

## <a name="components"></a>组件

Azure Stack Edge Mini R 解决方案包括 Azure Stack Edge 资源、Azure Stack Edge Mini R 超便携式加固型物理设备和本地 Web UI。

* Azure Stack Edge Mini R 物理设备 - 一种超便携式设备，具有板载电池和 Microsoft 提供的加固型外壳，可配置该设备以将数据发送到 Azure。 带电池的设备重量不到 7 磅。

    ![Azure Stack Edge Mini R 设备](media/azure-stack-edge-k-series-overview/perspective-view-1.png)

* Azure Stack Edge 资源 - Azure 门户中的一种资源，使用该资源可以通过 Web 界面（可从不同的地理位置访问该界面）管理加固型 Azure Stack Edge Mini R 设备。 使用 Azure Stack Edge 资源可以创建和管理资源、查看和管理设备与警报，以及管理共享。  

* Azure Stack Edge Mini R 本地 Web UI - 使用本地 Web UI 进行初始设备配置，可以运行诊断、关闭和重启 Azure Stack Edge Mini R 设备、查看复制日志，并联系 Microsoft 支持部门以提出服务请求。


## <a name="region-availability"></a>上市区域

将数据传输到其中的 Azure Stack Edge Mini R 物理设备、Azure 资源和目标存储帐户不一定非要位于同一区域。

- **资源可用性** - 有关 Azure Stack Edge 资源可用的所有区域的列表，请转到[各区域提供的 Azure 产品](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)。 

- **目标存储帐户** - 存储数据的存储帐户可在所有 Azure 区域中获得。 存储帐户存储 Azure Stack Edge Mini R 数据的区域应靠近设备所在位置，以便获得最佳性能。 远离设备的存储帐户会导致长时间的延迟和性能下降。


## <a name="next-steps"></a>后续步骤

- 查看 [Azure Stack Edge Mini R 系统要求](azure-stack-edge-gpu-system-requirements.md)。


