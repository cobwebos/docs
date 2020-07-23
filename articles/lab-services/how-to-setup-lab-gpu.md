---
title: 在 Azure 实验室服务中使用 Gpu 设置实验室 |Microsoft Docs
description: 了解如何使用图形处理单元（GPU）虚拟机设置实验室。
author: nicolela
ms.topic: article
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: 63b7a8c58bb6d277233268ed4fc0bc870fdd337f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85443394"
---
# <a name="set-up-a-lab-with-gpu-virtual-machines"></a>使用 GPU 虚拟机设置实验室

本文演示如何完成以下任务：

- 选择*可视化*和*计算*图形处理单元（gpu）。
- 确保安装了相应的 GPU 驱动程序。

## <a name="choose-between-visualization-and-compute-gpu-sizes"></a>选择可视化和计算 GPU 大小
在实验室创建向导的第一页上，在 "**你需要哪种虚拟机大小？** " 下拉列表中，选择你的类所需的 vm 大小。  

![用于选择 VM 大小的 "新建实验室" 窗格的屏幕截图](./media/how-to-setup-gpu/lab-gpu-selection.png)

在此过程中，你可以选择是选择**可视化**或**计算**gpu。  选择基于你的学生将使用的软件的 GPU 类型很重要。  

按照下表中所述，*计算*GPU 大小适用于计算密集型应用程序。  例如，[自然语言处理类类型中的深度学习](./class-type-deep-learning-natural-language-processing.md)使用**小 GPU （计算）** 大小。  计算 GPU 适用于这类类，因为学生使用[Data Science Virtual Machine 图像](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804)提供的深度学习框架和工具，用大量数据来训练深度学习模型。

| 大小 | 核心数 | RAM | 说明 | 
| ---- | ----- | --- | ----------- | 
| 小型 GPU (计算) | -&nbsp;6 &nbsp; 核<br>-&nbsp;56 &nbsp; GB &nbsp; RAM  | [Standard_NC6](https://docs.microsoft.com/azure/virtual-machines/nc-series) |此大小最适用于计算机密集型应用程序，如人工智能（AI）和深度学习。 |

*可视化*GPU 大小适用于图形密集型应用程序。  例如， [SolidWorks 工程类类型](./class-type-solidworks.md)使用**小 GPU （可视化）** 大小显示。  可视化 GPU 适用于这类类，因为学生与用于建模和可视化实体对象的 SolidWorks 3D 计算机辅助设计（CAD）环境交互。

| 大小 | 核心数 | RAM | 说明 | 
| ---- | ----- | --- | ----------- | 
| 小型 GPU (可视化) | -&nbsp;6 &nbsp; 核<br>-&nbsp;56 &nbsp; GB &nbsp; RAM  | [Standard_NV6](https://docs.microsoft.com/azure/virtual-machines/nv-series) | 此大小最适合用于使用框架（如 OpenGL 和 DirectX）的远程可视化、流式处理、游戏和编码。 |
| 中等 GPU (可视化) | -&nbsp;12 &nbsp; 核<br>-&nbsp;112 &nbsp; GB &nbsp; RAM  | [Standard_NV12](https://docs.microsoft.com/azure/virtual-machines/nv-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | 此大小最适合用于使用框架（如 OpenGL 和 DirectX）的远程可视化、流式处理、游戏和编码。 |

## <a name="ensure-that-the-appropriate-gpu-drivers-are-installed"></a>确保安装了相应的 GPU 驱动程序
若要利用实验室 Vm 的 GPU 功能，请确保已安装适当的 GPU 驱动程序。  在实验室创建向导中，选择 GPU VM 大小时，可以选择 "**安装 gpu 驱动程序**" 选项。  

![显示 "安装 GPU 驱动程序" 选项的 "新实验室" 屏幕截图](./media/how-to-setup-gpu/lab-gpu-drivers.png)

如上图所示，此选项在默认情况下处于启用状态，这可确保为所选 GPU 和映像的类型安装*最新*的驱动程序。
- 选择*计算*GPU 大小时，实验室 Vm 由[NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) GPU 提供支持。  在这种情况下，将安装最新的[计算统一设备体系结构（CUDA）](https://www.nvidia.com/object/io_69526.html)驱动程序，这可实现高性能计算。
- 选择*可视化*GPU 大小时，实验室 Vm 由[NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU 和[网格技术](https://www.nvidia.com/content/dam/en-zz/Solutions/design-visualization/solutions/resources/documents1/NVIDIA_GRID_vPC_Solution_Overview.pdf)提供支持。  在这种情况下，将安装最新的网格驱动程序，这将允许使用图形密集型应用程序。

### <a name="install-the-drivers-manually"></a>手动安装驱动程序
可能需要安装最新版本以外的驱动程序版本。  本部分说明如何手动安装适当的驱动程序，具体取决于你使用的是*计算*gpu 还是*可视化*gpu gpu。

#### <a name="install-the-compute-gpu-drivers"></a>安装计算 GPU 驱动程序

若要手动安装用于计算 GPU 大小的驱动程序，请执行以下操作：

1. [创建](./how-to-manage-classroom-labs.md)实验室时，在实验室创建向导中，禁用 "**安装 GPU 驱动程序**" 设置。

1. 创建实验室后，连接到模板 VM 以安装相应的驱动程序。

   ![NVIDIA 驱动程序下载页的屏幕截图](./media/how-to-setup-gpu/nvidia-driver-download.png) 

   a. 在浏览器中，请参阅[NVIDIA 驱动程序下载页](https://www.nvidia.com/Download/index.aspx)。  
   b. 将**产品类型**设置为 " **Tesla**"。  
   c. 将**产品序列**设置为**K 系列**。  
   d. 根据创建实验室时选择的基本映像类型，设置**操作系统**。  
   e. 将**CUDA 工具包**设置为所需的 CUDA 驱动程序版本。  
   f. 选择 "**搜索**" 以查找驱动程序。  
   如， 选择 "**下载**" 以下载安装程序。  
   h.如果该值不存在，请单击“添加行”。 运行安装程序，以便在模板 VM 上安装驱动程序。  
1. 按照[验证安装的驱动程序](how-to-setup-lab-gpu.md#validate-the-installed-drivers)部分中的说明，验证是否正确安装了驱动程序。 
1. 安装了类所需的驱动程序和其他软件之后，选择 "**发布**" 以创建学生的 vm。

> [!NOTE]
> 如果使用的是 Linux 映像，则在下载安装程序后，请按照在[Linux 上安装 CUDA 驱动程序](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup?toc=/azure/virtual-machines/linux/toc.json#install-cuda-drivers-on-n-series-vms)中的说明安装驱动程序。

#### <a name="install-the-visualization-gpu-drivers"></a>安装可视化 GPU 驱动程序

若要手动安装可视化 GPU 大小的驱动程序，请执行以下操作：

1. [创建](./how-to-manage-classroom-labs.md)实验室时，在实验室创建向导中，禁用 "**安装 GPU 驱动程序**" 设置。
1. 创建实验室后，连接到模板 VM 以安装相应的驱动程序。
1. 按照适用于你的操作系统的说明，在模板 VM 上安装 Microsoft 提供的网格驱动程序：
   -  [Windows NVIDIA GRID 驱动程序](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers)
   -  [Linux NVIDIA GRID 驱动程序](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup?toc=/azure/virtual-machines/linux/toc.json#nvidia-grid-drivers)
  
1. 重新启动模板 VM。
1. 按照[验证安装的驱动程序](how-to-setup-lab-gpu.md#validate-the-installed-drivers)部分中的说明，验证是否正确安装了驱动程序。
1. 安装了类所需的驱动程序和其他软件之后，选择 "**发布**" 以创建学生的 vm。

### <a name="validate-the-installed-drivers"></a>验证已安装的驱动程序
本部分介绍如何验证是否正确安装了 GPU 驱动程序。

#### <a name="windows-images"></a>Windows 映像
1.  按照在[运行 Windows 的 N 系列 vm 上安装 NVIDIA GPU 驱动程序](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation)的 "验证驱动程序安装" 一节中的说明进行操作。
1.  如果使用的是*可视化*GPU GPU，还可以执行以下操作：
    - 在 NVIDIA 控制面板中查看并调整 GPU 设置。 为此，请在**Windows 控制面板**中选择 "**硬件**"，然后选择 **"NVIDIA 控制面板**"。

      ![显示 NVIDIA 控制面板链接的 Windows 控制面板屏幕截图](./media/how-to-setup-gpu/control-panel-nvidia-settings.png) 

     - 使用**任务管理器**查看 GPU 性能。  为此，请选择 "**性能**" 选项卡，然后选择 " **GPU** " 选项。

       ![显示 "任务管理器 GPU 性能" 选项卡的屏幕截图](./media/how-to-setup-gpu/task-manager-gpu.png) 

      > [!IMPORTANT]
      > 只能为*可视化*GPU 访问 NVIDIA 控制面板设置。  如果尝试打开用于计算 GPU 的 NVIDIA 控制面板，会出现以下错误： "NVIDIA 显示设置不可用。  当前未使用附加到 NVIDIA GPU 的显示器。  同样，仅为可视化 Gpu 提供任务管理器中的 GPU 性能信息。

#### <a name="linux-images"></a>Linux 映像
按照在[运行 Linux 的 N 系列 vm 上安装 NVIDIA GPU 驱动程序](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup#verify-driver-installation)的 "验证驱动程序安装" 一节中的说明进行操作。

## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [创建和管理教室实验室](how-to-manage-classroom-labs.md)
- [SolidWorks 计算机辅助设计（CAD）类类型](class-type-solidworks.md)
- [MATLAB （matrix 实验室）类类型](class-type-matlab.md)



