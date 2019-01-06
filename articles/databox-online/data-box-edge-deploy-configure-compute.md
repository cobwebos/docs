---
title: 使用 Azure Data Box Edge 转换数据 | Microsoft Docs
description: 了解如何在 Data Box Edge 中配置计算角色，并在将数据发送到 Azure 之前，使用该角色转换数据。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 11/27/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: c0901f22e4941fdfaa21138153a06e97c2d6095f
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/19/2018
ms.locfileid: "53630368"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge-preview"></a>教程：使用 Azure Data Box Edge（预览版）转换数据

本教程介绍如何在Azure Data Box Edge 设备上配置计算角色。 配置计算角色后，Data Box Edge 可在将数据发送到 Azure 之前先转换数据。

此过程可能需要大约 30 到 45 分钟才能完成。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建 Azure IoT 中心资源
> * 设置计算角色
> * 添加计算模块
> * 验证数据转换和传输

> [!IMPORTANT]
> Data Box Edge 以预览版提供。 在订购和部署此解决方案之前，请查看 [Azure 预览版服务条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
 
## <a name="prerequisites"></a>先决条件

在 Data Box Edge 设备上设置计算角色之前，请确保：

* 已根据[连接、设置和激活 Azure Data Box Edge](data-box-edge-deploy-connect-setup-activate.md) 中所述激活 Data Box Edge 设备。


## <a name="create-an-iot-hub-resource"></a>创建 IoT 中心资源

在 Data Box Edge 中设置计算角色之前，必须先创建一个 IoT 中心资源。

有关详细说明，请转到[创建 IoT 中心](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal#create-an-iot-hub)。 使用对 Data Box Edge 资源所用的同一个订阅和资源组。

![创建 IoT 中心资源](./media/data-box-edge-deploy-configure-compute/create-iothub-resource-1.png)

如果尚未设置 Edge 计算角色，请注意以下事项：

- IoT 中心资源不包含任何 Azure IoT 设备或 Azure IoT Edge 设备。
- 无法创建 Edge 本地共享。 添加共享时，不会启用用来为 Edge 计算创建本地共享的选项。


## <a name="set-up-compute-role"></a>设置计算角色

如果在 Edge 设备上设置了 Edge 计算角色，则会创建两个设备：一个 IoT 设备，一个 IoT Edge 设备。 可在 IoT 中心资源中查看这两个设备。

若要在设备上设置计算角色，请执行以下操作：

1. 转到 Data Box Edge 资源，依次选择“概述”、“设置计算角色”。 

    ![左窗格中的“概述”链接](./media/data-box-edge-deploy-configure-compute/setup-compute-1.png)
   
    （可选）可以转到“模块”并选择“配置计算”。

    ![“模块”和“配置计算”链接](./media/data-box-edge-deploy-configure-compute/setup-compute-2.png)
 
1. 在下拉列表中，选择在上一步骤中创建的 **IoT 中心资源**。  
    目前，只有 Linux 平台适用于你的 IoT Edge 设备。 
    
1. 单击“创建”。

    ![“创建”按钮](./media/data-box-edge-deploy-configure-compute/setup-compute-3.png)
 
    创建计算角色需要花费几分钟时间。 由于此版本的一个 bug，即使创建了计算角色，屏幕也不会刷新。 若要确认是否已配置 Edge 计算角色，请转到“模块”。  

    ![“配置 Edge 计算”设备列表](./media/data-box-edge-deploy-configure-compute/setup-compute-4.png)

1. 再次转到“概述”。  
    屏幕将会更新，指出计算角色已配置。

    ![设置计算角色](./media/data-box-edge-deploy-configure-compute/setup-compute-5.png)
 
1. 在创建 Edge 计算角色时所用的 IoT 中心，转到“IoT 设备”。  
    现已启用某个 IoT 设备。 

    ![“IoT 设备”页](./media/data-box-edge-deploy-configure-compute/setup-compute-6.png)

1. 在左窗格中，选择“IoT Edge”。  
    此外还启用了某个 IoT Edge 设备。

    ![设置计算角色](./media/data-box-edge-deploy-configure-compute/setup-compute-7.png)
 
1. 选择并单击该 IoT Edge 设备。  
    一个 Edge 代理正在此 IoT Edge 设备上运行。 

    ![“设备详细信息”页](./media/data-box-edge-deploy-configure-compute/setup-compute-8.png) 

    此 Edge 设备不包含任何自定义模块，但你现在可以添加自定义模块。 若要了解如何创建自定义模块，请参阅[为 Data Box Edge 设备开发 C# 模块](data-box-edge-create-iot-edge-module.md)。


## <a name="add-a-custom-module"></a>添加自定义模块

在此部分，请将在[为 Data Box Edge 开发 C# 模块](data-box-edge-create-iot-edge-module.md)中创建的自定义模块添加到 IoT Edge 设备。 

以下过程使用一个示例，其中，自定义模块从 Edge 设备上的本地共享提取文件，并将其移到设备上的云共享。 然后，云共享将文件推送到与该云共享相关联的 Azure 存储帐户。 

1. 执行以下操作，在 Edge 设备上添加本地共享：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在 Data Box Edge 资源中，转到“共享”。 
    
    b. 选择“添加共享”，提供共享名称，然后选择共享类型。 
    
    c. 若要创建本地共享，请选中“配置为 Edge 本地共享”复选框。 
    
    d. 选择“新建”或“使用现有项”，然后选择“创建”。

    ![添加自定义模块](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-1.png) 

    如果创建了本地 NFS 共享，请使用以下远程同步 (rsync) 命令选项将文件复制到共享：

    `rsync --inplace <source file path> < destination file path>`

    有关 rsync 命令的详细信息，请参阅 [Rsync 文档](https://www.computerhope.com/unix/rsync.htm)。 

    现已创建本地共享，并且收到了创建成功的通知。 共享列表可能会更新，但必须等待共享创建完成。
    
1. 转到共享列表。 

    ![添加自定义模块](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-2.png) 
 
1. 若要查看新建的本地共享的属性，请将其选中。 

1. 在“Edge 计算模块的本地装入点”框中，复制对应于此共享的值。  
    部署模块时将要使用此本地装入点。

    ![“Edge 计算模块的本地装入点”框](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-3.png) 
 
1. 在 Data Box Edge 设备上创建的现有云共享中的“Edge 计算模块的本地装入点”框内，复制此云共享的 Edge 计算模块本地装入点。  
    部署模块时将要使用此本地装入点。

    ![添加自定义模块](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-4.png)  

1. 若要将自定义模块添加到 IoT Edge 设备，请转到 IoT 中心资源，然后转到“IoT Edge 设备”。 

1. 选择该设备，然后在“设备详细信息”下选择“设置模块”。 

    ![“设置模块”链接](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-5.png) 

1. 在“添加模块”下执行以下操作：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 输入自定义模块的容器注册表设置的名称、地址、用户名和密码。  
    名称、地址和所列的凭据用于检索具有匹配 URL 的模块。 若要部署此模块，请在“部署模块”下选择“IoT Edge 模块”。 此 IoT Edge 模块是可以部署到与 Data Box Edge 设备相关联的 IoT Edge 设备的 Docker 容器。

    ![“设置模块”页](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-6.png) 
 
    b. 通过输入模块名称和相应容器映像的映像 URI，指定 IoT Edge 自定义模块的设置。 
    
    ![“IoT Edge 自定义模块”页](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-7.png) 

    c. 在“容器创建选项”中，输入在前面步骤中为云共享和本地共享复制的 Edge 模块本地装入点。
    > [!IMPORTANT]
    > 请使用复制的路径，而不要创建新路径。 本地装载点映射到相应的 **InputFolderPath** 和 **OutputFolderPath**，后者是在[使用自定义代码更新模块](data-box-edge-create-iot-edge-module.md#update-the-module-with-custom-code)时在模块中指定的。 
    
    在“容器创建选项”中，可以粘贴以下示例内容： 
    
    ```
    {
        "HostConfig": {
        "Binds": [
        "/home/hcsshares/mysmblocalshare:/home/LocalShare",
        "/home/hcsshares/mysmbshare1:/home/CloudShare"
        ]
        }
    }
    ```

    另外，请在此处为模块提供任何环境变量。

    ![“容器创建选项”框](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-8.png) 
 
    d. 根据需要选择配置高级 Edge 运行时设置，然后单击“下一步”。

    ![添加自定义模块](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-9.png) 
 
1.  在“指定路由”下，设置模块之间的路由。  
    在本示例中，请输入要将数据推送到云共享的本地共享的名称。

    可以将路由替换为以下路由字符串：`"route": "FROM /* WHERE topic = 'mysmblocalshare' INTO BrokeredEndpoint(\"/modules/filemovemodule/inputs/input1\")"`

    ![“指定路由”部分](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-10.png) 

1. 选择“**下一步**”。 

1.  在“检查部署”下检查所有设置，然后选择“提交”以提交模块进行部署。

    ![“设置模块”页](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-11.png) 
 
    此操作会启动模块部署，如下图所示：

    ![添加自定义模块](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-12.png) 

### <a name="verify-data-transform-and-transfer"></a>验证数据转换和传输

最后一步是确保模块按预期连接并运行。 对于 IoT 中心资源中的 IoT Edge 设备，模块的运行时状态应为正在运行。

![验证数据转换](./media/data-box-edge-deploy-configure-compute/verify-data-transform-1.png) 
 
若要验证模块是否正在运行，请执行以下操作：

1. 选择该模块，然后查看“模块标识孪生”。  
    Edge 设备和模块的客户端状态应显示为“已连接”。

    ![验证数据转换](./media/data-box-edge-deploy-configure-compute/verify-data-transform-2.png) 
 
    模块运行后，它还会显示在 Data Box Edge 资源中的 Edge 模块列表下面。 所添加模块的运行时状态为“正在运行”。

    ![验证数据转换](./media/data-box-edge-deploy-configure-compute/verify-data-transform-3.png) 
 
1.  在文件资源管理器中，连接到前面创建的本地共享和云共享。

    ![验证数据转换](./media/data-box-edge-deploy-configure-compute/verify-data-transform-4.png) 
 
1.  将数据添加到本地共享。

    ![验证数据转换](./media/data-box-edge-deploy-configure-compute/verify-data-transform-5.png) 
 
    数据将移到云共享。

    ![验证数据转换](./media/data-box-edge-deploy-configure-compute/verify-data-transform-6.png)  

    然后，将数据从云共享推送到存储帐户。 转到存储资源管理器可查看数据。

    ![验证数据转换](./media/data-box-edge-deploy-configure-compute/verify-data-transform-7.png) 
 
现已完成验证过程。


## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 创建 IoT 中心资源
> * 设置计算角色
> * 添加计算模块
> * 验证数据转换和传输

若要了解如何管理 Data Box Edge 设备，请参阅：

> [!div class="nextstepaction"]
> [使用本地 Web UI 管理 Data Box Edge](https://aka.ms/dbg-docs)


