---
title: 使用 Azure Data Box Edge 转换数据 | Microsoft Docs
description: 了解如何在 Data Box Edge 中配置计算角色，并在将数据发送到 Azure 之前，使用该角色转换数据。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/19/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: ba77fc4596d9bb245b3cea2538804b1816e9ad14
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/19/2018
ms.locfileid: "49466964"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge-preview"></a>教程：使用 Azure Data Box Edge（预览版）转换数据

本教程介绍如何在 Data Box Edge 中配置计算角色。 配置计算角色后，Data Box Edge 可在将数据发送到 Azure 之前先转换数据。

此过程可能需要大约 30-45 分钟才能完成。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建 IoT 中心资源
> * 设置计算角色
> * 添加计算模块
> * 验证数据转换和传输

> [!IMPORTANT]
> Data Box Edge 以预览版提供。 在订购和部署此解决方案之前，请查看 [Azure 预览版服务的条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
 
## <a name="prerequisites"></a>先决条件

在 Data Box Edge 中设置计算之前，请确保：

* 已根据[连接和激活 Azure Data Box Edge](data-box-edge-deploy-connect-setup-activate.md) 中的详述激活 Data Box Edge 设备。


## <a name="create-an-iot-hub-resource"></a>创建 IoT 中心资源

在 Data Box Edge 中设置计算角色之前，必须先创建一个 IoT 中心资源。

有关详细说明，请转到[创建 IoT 中心](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal#create-an-iot-hub)。 使用对 Data Box Edge 资源所用的同一个订阅和资源组。

![创建 IoT 中心资源](./media/data-box-edge-deploy-configure-compute/create-iothub-resource-1.png)

未设置 Edge 计算角色时，请注意：

- IoT 中心资源不包含任何 IoT 设备或 IoT Edge 设备。
- 无法创建 Edge 本地共享。 添加共享时，不会启用用来为 Edge 计算创建本地共享的选项。


## <a name="set-up-compute-role"></a>设置计算角色

如果在 Edge 设备上设置了 Edge 计算角色，则会创建两个设备 – 一个是 IoT 设备，另一个是 IoT Edge 设备。 可在 IoT 中心资源中查看这两个设备。

若要在设备上设置计算角色，请执行以下步骤。

1. 转到 Data Box Edge 资源，然后转到“概述”并单击“设置计算角色”。 

    ![设置计算角色](./media/data-box-edge-deploy-configure-compute/setup-compute-1.png)
   
    也可以转到“模块”并单击“配置计算”。

    ![设置计算角色](./media/data-box-edge-deploy-configure-compute/setup-compute-2.png)
 
2. 从下拉列表中，选择在上一步骤中创建的 **IoT 中心资源**。 目前，只有 Linux 平台适用于你的 IoT Edge 设备。 单击“创建”。

    ![设置计算角色](./media/data-box-edge-deploy-configure-compute/setup-compute-3.png)
 
3. 创建计算角色需要花费几分钟时间。 由于此版本的一个 bug，即使创建了计算角色，屏幕也不会刷新。 转到“模块”，可以看到 Edge 计算已配置。  

    ![设置计算角色](./media/data-box-edge-deploy-configure-compute/setup-compute-4.png)

4. 再次转到“概述”，现在，屏幕将会更新，指出计算角色已配置。

    ![设置计算角色](./media/data-box-edge-deploy-configure-compute/setup-compute-5.png)
 
5. 转到创建 Edge 计算角色时所用的 IoT 中心。 转到“IoT 设备”。 可以看到，IoT 设备现已启用。 

    ![设置计算角色](./media/data-box-edge-deploy-configure-compute/setup-compute-6.png)

6. 转到“IoT Edge”，将会看到，同时还启用了 IoT Edge 设备。

    ![设置计算角色](./media/data-box-edge-deploy-configure-compute/setup-compute-7.png)
 
7. 选择并单击该 IoT Edge 设备。 一个 Edge 代理正在此 IoT Edge 设备上运行。 

    ![设置计算角色](./media/data-box-edge-deploy-configure-compute/setup-compute-8.png) 

但是，此 Edge 设备不包含任何自定义模块。 现在，可将自定义模块添加到此设备。 若要了解如何创建自定义模块，请访问[为 Data Box Edge 开发 C# 模块](data-box-edge-create-iot-edge-module.md)。


## <a name="add-a-custom-module"></a>添加自定义模块

在此部分，请将在[为 Data Box Edge 开发 C# 模块](data-box-edge-create-iot-edge-module.md)中创建的自定义模块添加到 IoT Edge 设备。 

此过程使用一个示例，其中，所用的自定义模块从 Edge 设备上的本地共享提取文件，并将其移到设备上的云共享。 然后，云共享将文件推送到与该云共享相关联的 Azure 存储帐户。 

1. 第一步是在 Edge 设备上添加本地共享。 在 Data Box Edge 资源中，转到“共享”。 单击“+ 添加共享”。 提供共享名称，并选择共享类型。 若要创建本地共享，请选中“配置为 Edge 本地共享”选项。 选择**现有用户**或**新建用户**。 单击“创建”。

    ![添加自定义模块](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-1.png) 

    如果创建了本地 NFS 共享，请使用以下 rsync 命令选项将文件复制到共享：

    `rsync --inplace <source file path> < destination file path>`

     有关 rsync 命令的详细信息，请参阅 [Rsync 文档](https://www.computerhope.com/unix/rsync.htm)。 

 
2. 创建本地共享并收到成功创建的通知后（共享列表可能会提前更新，但必须等待共享创建完成），请转到共享列表。 

    ![添加自定义模块](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-2.png) 
 
3. 选择并单击新建的本地共享，并查看该共享的属性。 复制并保存对应于此共享的 **Edge 模块本地装入点**。

    ![添加自定义模块](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-3.png) 
 
    转到在 Data Box Edge 设备上创建的现有云共享。 同样，请复制并保存此云共享的 Edge 计算模块本地装入点。 部署模块时将使用这些本地装入点。

    ![添加自定义模块](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-4.png)  

4. 若要将自定义模块添加到 IoT Edge 设备，请转到 IoT 中心资源，然后转到“IoT Edge 设备”。 选择并单击该设备。 在“设备详细信息”中，单击顶部命令栏中的“设置模块”。 

    ![添加自定义模块](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-5.png) 

5. 在“添加模块”下执行以下步骤：

    1. 提供自定义模块的“容器注册表设置”的**名称**、**地址**、**用户名**和**密码**。 名称、地址和所列的凭据用于检索具有匹配 URL 的模块。 若要部署此模块，请在“部署模块”下选择“IoT Edge 模块”。 此 IoT Edge 模块是可以部署到与 Data Box Edge 设备相关联的 IoT Edge 设备的 Docker 容器。

        ![添加自定义模块](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-6.png) 
 
    2. 指定 IoT Edge 自定义模块的设置。 提供模块的**名称**，并为相应的容器映像提供**映像 URI**。 
    
        ![添加自定义模块](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-7.png) 

    3. 在“容器创建选项”中，提供在前面步骤中为云共享和本地共享复制的 Edge 模块本地装入点（必须使用这些路径，而不要创建新路径）。 本地装载点映射到相应的 **InputFolderPath** 和 **OutputFolderPath**，后者是在[使用自定义代码更新模块](data-box-edge-create-iot-edge-module.md#update-the-module-with-custom-code)时在模块中指定的。 
    
        可以复制下面显示的示例并将其粘贴到**容器创建选项**中： 
        
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

        ![添加自定义模块](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-8.png) 
 
    4. 根据需要选择“配置高级 Edge 运行时设置”，然后单击“下一步”。

        ![添加自定义模块](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-9.png) 
 
6.  在“指定路由”下，设置模块之间的路由。 在本例中，请提供要将数据推送到云共享的本地共享的名称。 单击“下一步”。

    可以将路由替换为以下路由字符串："route": "FROM /* WHERE topic = 'mysmblocalshare' INTO BrokeredEndpoint(\"/modules/filemovemodule/inputs/input1\")"

    ![添加自定义模块](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-10.png) 
 
7.  在“检查部署”下检查所有设置，如果对设置感到满意，请**提交**该模块进行部署。

    ![添加自定义模块](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-11.png) 
 
随后会启动模块部署，如“模块”下的“IoT Edge 自定义模块”所示。

![添加自定义模块](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-12.png) 

### <a name="verify-data-transform-and-transfer"></a>验证数据转换和传输

最后一步是确保模块按预期连接并运行。 执行以下步骤，验证模块是否正在运行。

1. 对于 IoT 中心资源中的 IoT Edge 设备，模块的运行时状态应为正在运行。

    ![验证数据转换](./media/data-box-edge-deploy-configure-compute/verify-data-transform-1.png) 
 
2. 选择并单击该模块，然后查看“模块标识孪生”。 Edge 设备和模块的客户端状态应显示为“已连接”。

    ![验证数据转换](./media/data-box-edge-deploy-configure-compute/verify-data-transform-2.png) 
 
3.  模块运行后，它还会显示在 Data Box Edge 资源中的 Edge 模块列表下面。 所添加模块的“运行时状态”为“正在运行”。

    ![验证数据转换](./media/data-box-edge-deploy-configure-compute/verify-data-transform-3.png) 
 
4.  通过文件资源管理器连接到创建的本地共享和云共享。

    ![验证数据转换](./media/data-box-edge-deploy-configure-compute/verify-data-transform-4.png) 
 
5.  将数据添加到本地共享。

    ![验证数据转换](./media/data-box-edge-deploy-configure-compute/verify-data-transform-5.png) 
 
6.  数据将移到云共享。

    ![验证数据转换](./media/data-box-edge-deploy-configure-compute/verify-data-transform-6.png)  

7.  然后，将数据从云共享推送到存储帐户。 转到存储资源管理器以查看数据。

    ![验证数据转换](./media/data-box-edge-deploy-configure-compute/verify-data-transform-7.png) 
 
验证过程到此结束。


## <a name="next-steps"></a>后续步骤

在本教程中，我们已了解有关 Data Box Edge 的主题，例如：

> [!div class="checklist"]
> * 创建 IoT 中心资源
> * 设置计算角色
> * 添加计算模块
> * 验证数据转换和传输

请继续学习下一教程，了解如何管理 Data Box Edge。

> [!div class="nextstepaction"]
> [使用本地 Web UI 管理 Data Box Edge](http://aka.ms/dbg-docs)


