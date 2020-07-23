---
title: 教程：通过 Azure Stack Edge 的计算力筛选和分析数据 | Microsoft Docs
description: 了解如何在 Azure Stack Edge 中配置计算角色，并在将数据发送到 Azure 之前，使用该角色转换数据。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: ab9c93dc029498c70a828f38f297c1e02dcb29ee
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83774087"
---
# <a name="tutorial-transform-data-with-azure-stack-edge"></a>教程：使用 Azure Stack Edge 转换数据

本教程介绍如何在Azure Stack Edge 设备上配置计算角色。 配置计算角色后，Azure Stack Edge 可在将数据发送到 Azure 之前转换数据。

此过程可能需要大约 10 到 15 分钟才能完成。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 配置计算
> * 添加共享
> * 添加计算模块
> * 验证数据转换和传输

 
## <a name="prerequisites"></a>先决条件

在 Azure Stack Edge 设备上设置计算角色之前，请确保：

- 已按照[连接、设置和激活 Azure Stack Edge](azure-stack-edge-deploy-connect-setup-activate.md) 中所述激活了 Azure Stack Edge 设备。


## <a name="configure-compute"></a>配置计算

若要在 Azure Stack Edge 上配置计算，必须创建一个 IoT 中心资源。

1. 在 Azure Stack Edge 资源的 Azure 门户中，转到“概览”。 在右窗格中的“计算”磁贴上，选择“开始”。  

    ![开始使用计算](./media/azure-stack-edge-deploy-configure-compute/configure-compute-1.png)

2. 在“配置 Edge 计算”磁贴上，选择“配置计算”。  
3. 在“配置 Edge 计算”边栏选项卡上输入以下内容： 

   
    |字段  |值  |
    |---------|---------|
    |IoT 中心     | 选择“新建”或“现有”。   <br> 默认会使用标准层 (S1) 来创建 IoT 资源。 若要使用免费层 IoT 资源，请创建一个资源，然后选择现有的资源。 <br> 在每种情况下，IoT 中心资源都会使用 Azure Stack Edge 资源所用的同一订阅和资源组。     |
    |名称     |输入 IoT 中心资源的名称。         |

    ![开始使用计算](./media/azure-stack-edge-deploy-configure-compute/configure-compute-2.png)

4. 选择“创建”  。 创建 IoT 中心资源需要花费几分钟时间。 创建 IoT 中心资源后，“配置计算”磁贴会更新，以显示计算配置。  若要确认是否已配置 Edge 计算角色，请在“配置计算”磁贴上选择“查看计算”。  
    
    ![开始使用计算](./media/azure-stack-edge-deploy-configure-compute/configure-compute-3.png)

    > [!NOTE]
    > 如果在 IoT 中心与 Azure Stack Edge 设备关联之前关闭了“配置计算”  对话框，则仍然会创建 IoT 中心，但不会在计算配置中显示该 IoT 中心。 
    
    如果在 Edge 设备上设置了 Edge 计算角色，则会创建两个设备：一个 IoT 设备，一个 IoT Edge 设备。 可在 IoT 中心资源中查看这两个设备。 某个 IoT Edge 运行时也在此 IoT Edge 设备上运行。 目前，只有 Linux 平台适用于你的 IoT Edge 设备。


## <a name="add-shares"></a>添加共享

对于本教程中的简单部署，需要添加两个共享：一个 Edge 共享，一个 Edge 本地共享。

1. 执行以下步骤，在设备上添加 Edge 共享：

    1. 在 Azure Stack Edge 资源中，转到“Edge 计算”>“开始”。 
    2. 在“添加共享”磁贴上选择“添加”。  
    3. 在“添加共享”边栏选项卡上提供共享名称，然后选择共享类型。 
    4. 若要装载 Edge 共享，请选中“将该共享用于 Edge 计算”复选框。 
    5. 依次选择“存储帐户”、“存储服务”、某个现有用户、“创建”。   

        ![添加 Edge 共享](./media/azure-stack-edge-deploy-configure-compute/add-edge-share-1.png) 

    如果创建了本地 NFS 共享，请使用以下远程同步 (rsync) 命令选项将文件复制到共享：

    `rsync <source file path> < destination file path>`

    有关 rsync 命令的详细信息，请参阅 [Rsync 文档](https://www.computerhope.com/unix/rsync.htm)。

    现已创建 Edge 共享，并且收到了创建成功的通知。 共享列表可能会更新，但必须等待共享创建完成。

2. 重复上述所有步骤并选中“配置为 Edge 本地共享”复选框，在 Edge 设备上添加 Edge 本地共享。  本地共享中的数据将保留在设备上。

    ![添加 Edge 本地共享](./media/azure-stack-edge-deploy-configure-compute/add-edge-share-2.png)

  
3. 选择“添加共享”以查看更新的共享列表。 

    ![更新的共享列表](./media/azure-stack-edge-deploy-configure-compute/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>添加模块

可以添加自定义的或预生成的模块。 此 Edge 设备上不存在自定义模块。 若要了解如何创建自定义模块，请访问[为 Azure Stack Edge 设备开发 C# 模块](azure-stack-edge-create-iot-edge-module.md)。

在此部分中，我们将向[为 Azure Stack Edge 开发 C# 模块](azure-stack-edge-create-iot-edge-module.md)中创建的 IoT Edge 设备添加一个自定义模块。 此自定义模块从 Edge 设备上的 Edge 本地共享提取文件，并将其移到设备上的 Edge（云）共享。 然后，云共享将文件推送到与该云共享相关联的 Azure 存储帐户。

1. 转到“Edge 计算”>“开始”。  在“添加模块”磁贴上，选择“简单”作为方案类型。   选择 **添加** 。
2. 在“配置和添加模块”边栏选项卡中输入以下值： 

    
    |字段  |值  |
    |---------|---------|
    |名称     | 模块的唯一名称。 此模块是一个 Docker 容器，可以部署到与 Azure Stack Edge 关联的 IoT Edge 设备。        |
    |映像 URI     | 模块的对应容器映像的映像 URI。        |
    |需要凭据     | 如果选中此项，则会使用用户名和密码来检索具有匹配 URL 的模块。        |
    |输入共享     | 选择一个输入共享。 在本例中，Edge 本地共享是输入共享。 此处使用的模块将文件从 Edge 本地共享移到 Edge 共享，然后，这些文件将从 Edge 共享上传到云中。        |
    |输出共享     | 选择一个输出共享。 在本例中，Edge 共享是输出共享。        |
    |触发器类型     | 选择“文件”或“计划”。   每当发生文件事件（例如，将文件写入输入共享）时，文件触发器就会激发。 计划的触发器根据定义的计划激发。         |
    |触发器名称     | 触发器的唯一名称。         |
    |环境变量| 可帮助定义运行模块的环境的可选信息。   |

    ![添加和配置模块](./media/azure-stack-edge-deploy-configure-compute/add-module-1.png)

3. 选择 **添加** 。 随即会添加该模块。 “添加模块”磁贴将会更新，以指示模块已部署。  

    ![已部署模块](./media/azure-stack-edge-deploy-configure-compute/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>验证数据转换和传输

最后一步是确保模块按预期连接并运行。 对于 IoT 中心资源中的 IoT Edge 设备，模块的运行时状态应为正在运行。

若要验证模块是否正在运行，请执行以下操作：

1. 选择“添加模块”磁贴。  随后会转到“模块”边栏选项卡。  在模块列表中，找到已部署的模块。 所添加模块的运行时状态应为“正在运行”。 

    ![验证数据转换](./media/azure-stack-edge-deploy-configure-compute/verify-data-1.png)
 
1. 在文件资源管理器中，连接到前面创建的 Edge 本地共享和 Edge 共享。

    ![验证数据转换](./media/azure-stack-edge-deploy-configure-compute/verify-data-2.png) 
 
1. 将数据添加到本地共享。

    ![验证数据转换](./media/azure-stack-edge-deploy-configure-compute/verify-data-3.png) 
 
    数据将移到云共享。

    ![验证数据转换](./media/azure-stack-edge-deploy-configure-compute/verify-data-4.png)  

    然后，将数据从云共享推送到存储帐户。 若要查看数据，请转到存储资源管理器。

    ![验证数据转换](./media/azure-stack-edge-deploy-configure-compute/verify-data-5.png) 
 
现已完成验证过程。


## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
> * 配置计算
> * 添加共享
> * 添加计算模块
> * 验证数据转换和传输

若要了解如何管理 Azure Stack Edge 设备，请参阅：

> [!div class="nextstepaction"]
> [使用本地 Web UI 管理 Azure Stack Edge](azure-stack-edge-manage-access-power-connectivity-mode.md)
