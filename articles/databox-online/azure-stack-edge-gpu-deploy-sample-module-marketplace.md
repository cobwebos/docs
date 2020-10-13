---
title: 从 Azure Marketplace 部署 Microsoft Azure Stack Edge Pro 设备上的 GPU 模块 |Microsoft Docs
description: 介绍如何在 Azure Stack Edge Pro GPU 设备上部署已启用 GPU 的 IoT 模块。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 09/09/2020
ms.author: alkohli
ms.openlocfilehash: 64d028892298a70e7588863bf9a3f4fc6f4ca609
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91760053"
---
# <a name="deploy-a-gpu-enabled-iot-module-from-azure-marketplace-on-azure-stack-edge-pro-gpu-device"></a>在 Azure Stack Edge Pro GPU 设备上从 Azure Marketplace 部署支持 GPU 的 IoT 模块

本文介绍如何在 Azure Stack Edge Pro 设备上从 Azure Marketplace) 启用 IoT Edge 模块的图形处理单元部署 (GPU。 

在本文中，学习如何：
  - 准备 Azure Stack Edge Pro 以运行 GPU 模块。
  - 从 Azure Marketplace 下载并部署已启用 GPU 的 IoT 模块。
  - 监视模块输出。

## <a name="about-sample-module"></a>关于示例模块

本文中的 GPU 示例模块包括针对 GPU 的 CPU 的 PyTorch 和 TensorFlow 基准测试示例代码。

## <a name="prerequisites"></a>必备条件

在开始之前，请确保：

- 你可以访问已启用 GPU 的1节点 Azure Stack Edge 设备。 此设备已使用 Azure 中的资源激活。 
- 已在此设备上配置计算。 按照 [教程：在 Azure Stack Edge 设备上配置计算](azure-stack-edge-gpu-deploy-configure-compute.md)中的步骤操作。
- Windows 客户端上的以下开发资源：
    - [Visual Studio Code](https://code.visualstudio.com/)  
    - [Visual Studio Code 的 Azure IoT Edge 扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)。   


## <a name="get-module-from-azure-marketplace"></a>从 Azure Marketplace 获取模块

1. 浏览 [Azure Marketplace 中的所有应用](https://azuremarketplace.microsoft.com/marketplace/apps)。

    ![在 Azure Marketplace 中浏览应用](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/browse-apps-marketplace-1.png)

2. 搜索 **gpu**入门。

    ![搜索 GPU 示例模块](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/search-gpu-sample-module-1.png)

3. 选择“立即获取”。

    ![获取示例模块](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/get-sample-module-1.png)

4. 选择 " **继续** " 以确认提供者的使用条款和隐私策略。 

    ![获取示例模块2](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/terms-of-use-1.png)

5. 选择用于部署 Azure Stack Edge Pro 设备的订阅。

    ![选择订阅](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/select-subscription-1.png)

6. 输入在配置 Azure Stack Edge Pro 设备时创建的 IoT 中心服务的名称。 若要查找此 IoT 中心服务名称，请在 Azure 门户中转到与设备关联的 Azure Stack Edge 资源。 

    1. 在左窗格菜单选项中，转到 **Edge 计算 > 开始**。 

    1. 在 " **配置边缘计算** " 磁贴中，选择 " **查看配置**"。 

        ![查看计算配置](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/view-config-1.png)

    1. 在 " **边缘计算配置** " 边栏选项卡中：

        1. 记下在 Azure Stack Edge Pro 设备上配置计算时所创建的 IoT 中心服务。
        2. 记下在配置计算时创建的 IoT Edge 设备的名称。 你将在后续步骤中使用此名称。

        ![边缘计算配置](media/azure-stack-edge-gpu-deploy-sample-module/view-compute-config-1.png)

10. 选择“部署到设备”。

11. 输入 IoT Edge 设备的名称，或选择 " **查找设备**"，在向   中心注册的设备之间浏览。

    ![查找设备](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/find-device-1.png)

12. 选择 " **创建**   " 以继续配置部署清单的标准过程，包括添加其他模块（如果需要）。 新模块的详细信息（例如映像 URI、创建选项以及所需的属性）已预定义，但可以更改。

    ![选择“创建”](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/target-devices-iot-edge-module-1.png)


13. 验证该模块是否已部署在 Azure 门户的 IoT 中心。 选择设备，选择 " **设置模块**"，   模块应在 **IoT Edge 模块**"部分中列出   。

    ![选择创建2](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/running-module-iotres-1.png)

## <a name="monitor-the-module"></a>监视模块  

1. 在 VS Code 命令面板中，运行“Azure IoT 中心: 选择 IoT 中心”。****

2. 选择包含要配置的 IoT Edge 设备的订阅和 IoT 中心。 在这种情况下，请选择用于部署 Azure Stack Edge Pro 设备的订阅，并选择为 Azure Stack Edge Pro 设备创建的 IoT Edge 设备。 在前面的步骤中通过 Azure 门户配置计算时，会发生这种情况。

3. 在 VS Code 资源管理器中，展开 "Azure IoT 中心" 部分。 在 " **设备**" 下，应会看到与 Azure Stack Edge Pro 设备对应的 IoT Edge 设备。 

    1. 选择该设备，右键单击并选择 " **开始监视" "内置事件终结点**"。
  
        ![开始监视](media/azure-stack-edge-gpu-deploy-sample-module/monitor-builtin-event-endpoint-1.png)  

    2. 中转到 " **设备" > 模块** ，你会看到你的 **GPU 模块** 正在运行。

    3. VS Code 终端还应显示 IoT 中心事件作为 Azure Stack Edge Pro 设备的监视输出。

        ![监视输出](media/azure-stack-edge-gpu-deploy-sample-module/monitor-events-output-1.png) 

        你可以看到，执行同一组操作所需的时间 (5000 的形状转换) 迭代的时间比 CPU 的小。

## <a name="next-steps"></a>后续步骤

- 详细了解如何 [将 GPU 配置为使用模块](azure-stack-edge-j-series-configure-gpu-modules.md)。
