---
title: 在 Microsoft Azure Stack Edge Pro GPU 设备上运行 GPU 模块 |Microsoft Docs
description: 描述如何通过 Azure 门户在 Azure Stack 边缘 Pro 设备上配置和运行 GPU 上的模块。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/25/2020
ms.author: alkohli
ms.openlocfilehash: 8b9f1180639f638e72fdea2f87958628a2e9e86b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90891461"
---
# <a name="configure-and-run-a-module-on-gpu-on-azure-stack-edge-pro-device"></a>在 Azure Stack Edge Pro 设备上的 GPU 上配置和运行模块

Azure Stack Edge Pro 设备包含一个或多个图形处理单元 (GPU) 。 Gpu 是 AI 计算的常用选项，因为它们提供并行处理功能，并且在图像呈现时速度比中央处理单元 (Cpu) 的速度更快。 有关 Azure Stack Edge Pro 设备中包含的 GPU 的详细信息，请参阅 [Azure Stack Edge pro 设备技术规范](azure-stack-edge-gpu-technical-specifications-compliance.md)。

本文介绍如何在 Azure Stack Edge Pro 设备上配置和运行 GPU 上的模块。 在本文中，你将使用为 Nvidia T4 Gpu 编写的公开可用容器模块 **位数** 。 此过程可用于配置 Nvidia 为这些 Gpu 发布的任何其他模块。


## <a name="prerequisites"></a>先决条件

在开始之前，请确保：

1. 你可以访问已启用 GPU 的1节点 Azure Stack Edge Pro 设备。 此设备已使用 Azure 中的资源激活。  

## <a name="configure-module-to-use-gpu"></a>配置模块以使用 GPU

若要将模块配置为使用 Azure Stack Edge Pro 设备上的 GPU 来运行模块，请执行以下步骤。

1. 在 Azure 门户中，请参阅与设备关联的资源。 

2. 转到“Edge 计算”>“开始”。 在 " **配置边缘计算** " 磁贴中，选择 "配置"。

    ![配置模块以使用 GPU 1](media/azure-stack-edge-j-series-configure-gpu-modules/configure-compute-1.png)

3. 在 " **配置边缘计算** " 边栏选项卡中：

    1. 对于 **IoT 中心**，请选择 " **新建**"。
    2. 提供要为设备创建的 IoT 中心资源的名称。 若要使用免费层，请选择现有资源。 
    3. 记下 IoT Edge 设备和使用 IoT 中心资源创建的 IoT 网关设备。 稍后的步骤中将使用此信息。

    ![配置模块以使用 GPU 2](media/azure-stack-edge-j-series-configure-gpu-modules/configure-compute-2.png)

4. 创建 IoT 中心资源需要几分钟时间。 创建资源后，在 " **配置边缘计算** " 磁贴中，选择 " **查看配置** " 以查看 IoT 中心资源的详细信息。

    ![配置模块以使用 GPU 4](media/azure-stack-edge-j-series-configure-gpu-modules/configure-compute-4.png)

5. 请参阅 **自动设备管理 > IoT Edge**。

    ![配置模块以使用 GPU 6](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-2.png)

    在右侧窗格中，可以看到与 Azure Stack Edge Pro 设备关联的 IoT Edge 设备。 这对应于在创建 IoT 中心资源时在上一个步骤中创建的 IoT Edge 设备。 
    
6. 选择此 IoT Edge 设备。

   ![配置模块以使用 GPU 7](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-3.png)

7.  选择“设置模块”。

    ![配置模块以使用 GPU 8](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-4.png)

8. 选择 " **+ 添加** "，然后选择 " **+ IoT Edge 模块**"。 

    ![配置模块以使用 GPU 9](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-5.png)

9. 在 " **添加 IoT Edge 模块** " 选项卡中：

    1. 提供 **映像 URI**。 你将在此处使用公开提供的 Nvidia 模块 **数字** 。 
    
    2. 将 **重启策略** 设置为 " **始终**"。
    
    3. 将 **所需状态** 设置为 " **正在运行**"。
    
    ![配置模块以使用 GPU 10](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-6.png)

10. 在 " **环境变量** " 选项卡中，提供变量的名称和相应的值。 

    1. 要使当前模块在此设备上使用一个 GPU，请使用 NVIDIA_VISIBLE_DEVICES。 

    2. 将该值设置为 0 或 1。 这可确保设备在此模块中至少使用一个 GPU。 如果将此值设置为0，则表示此模块正在使用设备上的两个 Gpu。

        ![配置模块以使用 GPU 11](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-7.png)

        有关可与 Nvidia GPU 一起使用的环境变量的详细信息，请参阅 [nvidia 容器运行时](https://github.com/NVIDIA/nvidia-container-runtime#environment-variables-oci-spec)。

    > [!NOTE]
    > GPU 只能映射到一个模块。 但是，模块可以使用其中一个，无论是不使用 Gpu。 

11. 输入模块的名称。 此时，你可以选择提供容器 create 选项和修改模块克隆设置，或者，如果已完成，请选择 " **添加**"。 

    ![配置模块以使用 GPU 12](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-8.png)

12. 确保该模块正在运行，然后选择 " **查看 + 创建**"。    

    ![配置模块以使用 GPU 13](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-9.png)

13. 在 " **查看** " 和 "创建" 选项卡中，将显示所选的部署选项。 查看选项，然后选择 " **创建**"。
    
    ![配置模块以使用 GPU 14](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-10.png)

14. 记下模块的 **运行时状态** 。 
    
    ![配置模块以使用 GPU 15](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-11.png)

    部署模块需要几分钟时间。 选择 " **刷新** "，应会看到运行 **时状态** 更新为 " **正在运行**"。

    ![配置模块以使用 GPU 16](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-12.png)


## <a name="next-steps"></a>后续步骤

- 详细了解 [可与 NVIDIA GPU 一起使用的环境变量](https://github.com/NVIDIA/nvidia-container-runtime#environment-variables-oci-spec)。
