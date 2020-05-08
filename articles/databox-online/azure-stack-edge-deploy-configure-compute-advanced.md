---
title: 教程：通过 Azure Stack Edge 的计算力筛选和分析用于高级部署的数据 | Microsoft Docs
description: 了解如何在 Azure Stack Edge 中配置计算角色，以及如何在向 Azure 发送数据前使用该角色转换用于高级部署流的数据。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge for advanced deployment flow so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: eb7bb7933303aaf16f320e219ad3673bf1efde72
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82564655"
---
# <a name="tutorial-transform-data-with-azure-stack-edge-for-advanced-deployment-flow"></a>教程：使用 Azure Stack Edge 转换用于高级部署流的数据

本教程介绍如何在 Azure Stack Edge 设备上配置用于高级部署流的计算角色。 配置计算角色后，Azure Stack Edge 可在将数据发送到 Azure 之前转换数据。

可在设备上为简单或高级部署流配置计算。

|                  | 简单部署                                | 高级部署                   |
|------------------|--------------------------------------------------|---------------------------------------|
| 适用对象     | IT 管理员                                | 开发人员                            |
| 类型             | 使用 Azure Stack Edge 服务来部署模块      | 使用 IoT 中心服务部署模块 |
| 部署的模块 | Single                                           | 链接模块或多个模块           |


此过程可能需要大约 20 到 30 分钟才能完成。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 配置计算
> * 添加共享
> * 添加触发器
> * 添加计算模块
> * 验证数据转换和传输

 
## <a name="prerequisites"></a>先决条件

在 Azure Stack Edge 设备上设置计算角色之前，请确保：

- 已按照[连接、设置和激活 Azure Stack Edge](azure-stack-edge-deploy-connect-setup-activate.md) 中所述激活了 Azure Stack Edge 设备。


## <a name="configure-compute"></a>配置计算

若要在 Azure Stack Edge 上配置计算，必须创建一个 IoT 中心资源。

1. 在 Azure Stack Edge 资源的 Azure 门户中，转到“概览”  。 在右窗格中的“计算”磁贴上，选择“开始”。  

    ![开始使用计算](./media/azure-stack-edge-deploy-configure-compute-advanced/configure-compute-1.png)

2. 在“配置 Edge 计算”磁贴上，选择“配置计算”。  

    ![开始使用计算](./media/azure-stack-edge-deploy-configure-compute-advanced/configure-compute-2.png)

3. 在“配置 Edge 计算”边栏选项卡上输入以下内容： 

   
    |字段  |值  |
    |---------|---------|
    |IoT 中心     | 选择“新建”或“现有”。   <br> 默认会使用标准层 (S1) 来创建 IoT 资源。 若要使用免费层 IoT 资源，请创建一个资源，然后选择现有的资源。 <br> 在每种情况下，IoT 中心资源都会使用 Azure Stack Edge 资源所用的同一订阅和资源组。     |
    |名称     |输入 IoT 中心资源的名称。         |

    ![开始使用计算](./media/azure-stack-edge-deploy-configure-compute-advanced/configure-compute-3.png)

4. 选择“创建”  。 创建 IoT 中心资源需要花费几分钟时间。 创建 IoT 中心资源后，“配置 Edge 计算”磁贴会更新，以显示计算配置  。 若要确认已配置 Edge 计算角色，请在“配置计算”磁贴上选择“查看配置”   。
    
    ![开始使用计算](./media/azure-stack-edge-deploy-configure-compute-advanced/configure-compute-4.png)

    如果在 Edge 设备上设置了 Edge 计算角色，则会创建两个设备：一个 IoT 设备，一个 IoT Edge 设备。 可在 IoT 中心资源中查看这两个设备。 某个 IoT Edge 运行时也在此 IoT Edge 设备上运行。

    目前，只有 Linux 平台适用于你的 IoT Edge 设备。


## <a name="add-shares"></a>添加共享

对于本教程中的高级部署，需要添加两个共享：一个 Edge 共享和一个 Edge 本地共享。

1. 执行以下步骤，在设备上添加 Edge 共享：

    1. 在 Azure Stack Edge 资源中，转到“Edge 计算”>“开始”。 
    2. 在“添加共享”磁贴上选择“添加”。  
    3. 在“添加共享”边栏选项卡上提供共享名称，然后选择共享类型。 
    4. 若要装载 Edge 共享，请选中“将该共享用于 Edge 计算”复选框。 
    5. 依次选择“存储帐户”、“存储服务”、某个现有用户、“创建”。   

        ![添加 Edge 共享](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-1.png)

    <!--If you created a local NFS share, use the following remote sync (rsync) command option to copy files onto the share:

    `rsync <source file path> < destination file path>`

    For more information about the rsync command, go to [Rsync documentation](https://www.computerhope.com/unix/rsync.htm).-->

    创建 Edge 共享后，将收到创建成功的通知。 共享列表会更新，以反映新的共享。

2. 重复上述所有步骤并选中“配置为 Edge 本地共享”复选框，在 Edge 设备上添加 Edge 本地共享。  本地共享中的数据将保留在设备上。

    ![添加 Edge 本地共享](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-2.png)

3. 在“共享”边栏选项卡中，可看到更新的共享列表  。

    ![更新的共享列表](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-3.png)

4. 若要查看新创建的本地共享的属性，请从列表中选择该共享。 在“Edge 计算模块的本地装入点”框中，复制对应于此共享的值。 

    部署模块时将要使用此本地装入点。

    ![“Edge 计算模块的本地装入点”框](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-4.png)
 
5. 若要查看创建的 Edge 共享的属性，请从列表中选择该共享。 在“Edge 计算模块的本地装入点”框中，复制对应于此共享的值。 

    部署模块时将要使用此本地装入点。

    ![添加自定义模块](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-5.png)


## <a name="add-a-trigger"></a>添加触发器

1. 转到“Edge 计算”>“触发器”  。 选择“+ 添加触发器”  。

    ![添加触发器](./media/azure-stack-edge-deploy-configure-compute-advanced/add-trigger-1.png)

2. 在“添加触发器”边栏选项卡中，输入以下值  。

    |字段  |值  |
    |---------|---------|
    |触发器名称     | 触发器的唯一名称。         |
    |触发器类型     | 选择“文件”触发器  。 每当发生文件事件（例如，将文件写入输入共享）时，文件触发器就会激发。 另一方面，计划的触发器根据定义的计划激发。 在本示例中，我们需要文件触发器。    |
    |输入共享     | 选择一个输入共享。 在本例中，Edge 本地共享是输入共享。 此处使用的模块将文件从 Edge 本地共享移到 Edge 共享，然后，这些文件将从 Edge 共享上传到云中。        |

    ![添加触发器](./media/azure-stack-edge-deploy-configure-compute-advanced/add-trigger-2.png)

3. 创建触发器后会收到通知。 触发器列表会更新，以显示新创建的触发器。 选择刚才创建的触发器。

    ![添加触发器](./media/azure-stack-edge-deploy-configure-compute-advanced/add-trigger-3.png)

4. 复制并保存示例路由。 此示例路由将进行修改并稍后用于 IoT 中心。

    `"sampleroute": "FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/modulename/inputs/input1\")"`

    ![添加触发器](./media/azure-stack-edge-deploy-configure-compute-advanced/add-trigger-4.png)

## <a name="add-a-module"></a>添加模块

此 Edge 设备上不存在自定义模块。 可以添加自定义的或预生成的模块。 若要了解如何创建自定义模块，请访问[为 Azure Stack Edge 设备开发 C# 模块](azure-stack-edge-create-iot-edge-module.md)。

在此部分中，我们将向[为 Azure Stack Edge 开发 C# 模块](azure-stack-edge-create-iot-edge-module.md)中创建的 IoT Edge 设备添加一个自定义模块。 此自定义模块从 Edge 设备上的 Edge 本地共享提取文件，并将其移到设备上的 Edge（云）共享。 然后，云共享将文件推送到与该云共享相关联的 Azure 存储帐户。

1. 转到“Edge 计算”>“开始”。  在“添加模块”磁贴上，选择“高级”作为方案类型   。 选择“转到 IoT 中心”  。

    ![选择高级部署](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-1.png)

<!--2. In the **Configure and add module** blade, input the following values:  

    |Input share     | Select an input share. The Edge local share is the input share in this case. The module used here moves files from the Edge local share to an Edge share where they are uploaded into the cloud.        |
    |Output share     | Select an output share. The Edge share is the output share in this case.        |
-->

2. 在 IoT 中心资源中，转到“IoT Edge 设备”，然后选择 IoT Edge 设备  。

    ![转到 IoT 中心内的 IoT Edge 设备](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-2.png)

3. 在“设备详细信息”中，选择“设置模块”   。

    ![“设置模块”链接](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-3.png)

4. 在“添加模块”下执行以下操作： 

    1. 输入自定义模块的容器注册表设置的名称、地址、用户名和密码。
    名称、地址和所列的凭据用于检索具有匹配 URL 的模块。 若要部署此模块，请在“部署模块”下选择“IoT Edge 模块”。   此 IoT Edge 模块是一个 Docker 容器，可以部署到与 Azure Stack Edge 设备关联的 IoT Edge 设备。

        ![“设置模块”页](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-4.png) 
 
    2. 指定 IoT Edge 自定义模块的设置。 输入以下值。
     
        |字段  |值  |
        |---------|---------|
        |名称     | 模块的唯一名称。 此模块是一个 Docker 容器，可以部署到与 Azure Stack Edge 关联的 IoT Edge 设备。        |
        |映像 URI     | 模块的对应容器映像的映像 URI。        |
        |需要凭据     | 如果选中此项，则会使用用户名和密码来检索具有匹配 URL 的模块。        |
    
        在“容器创建选项”框中，输入在之前步骤中为 Edge 共享和 Edge 本地共享复制的 Edge 模块本地装入点  。

        > [!IMPORTANT]
        > 此处使用的路径将装入容器中，因此它们必须与容器预期的功能匹配。 如果按照[创建自定义模块](azure-stack-edge-create-iot-edge-module.md#update-the-module-with-custom-code)的说明操作，则该模块中指定的代码需要复制的路径。 不要修改这些路径。
    
        在“容器创建选项”中，可以粘贴以下示例内容： 
    
        ```
        {
          "HostConfig": 
          {
           "Binds": 
            [
             "/home/hcsshares/mydbesmbedgelocalshare1:/home/input",
             "/home/hcsshares/mydbesmbedgeshare1:/home/output"
            ]
           }
        }
        ```

        提供用于模块的任何环境变量。 环境变量提供可选信息，有助于定义模块的运行环境。

        ![“容器创建选项”框](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-5.png) 
 
    4. 根据需要选择配置高级 Edge 运行时设置，然后单击“下一步”。 

        ![添加自定义模块](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-6.png)
 
5.    在“指定路由”下，设置模块之间的路由。   
    
    ![指定路由](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-7.png)

    可以使用之前复制的以下路由字符串替换*路由*。 在本示例中，请输入要将数据推送到云共享的本地共享的名称。 将 `modulename` 替换为模块的名称。 选择“**下一页**”。
        
    ```
    "route": "FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/filemove/inputs/input1\")"
    ```

    ![“指定路由”部分](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-8.png)

6.    在“检查部署”下检查所有设置，然后选择“提交”以提交模块进行部署。  

    ![“设置模块”页](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-9.png)
 
    此操作将启动模块部署。 部署完成后，模块的“运行时状态”为“正在运行”   。

    ![添加自定义模块](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-10.png)

## <a name="verify-data-transform-transfer"></a>验证数据转换并传输

最后一步是确保模块按预期连接并运行。 对于 IoT 中心资源中的 IoT Edge 设备，模块的运行时状态应为正在运行。

执行以下步骤，验证数据转换并传输到 Azure。
 
1.    在文件资源管理器中，连接到前面创建的 Edge 本地共享和 Edge 共享。

    ![验证数据转换](./media/azure-stack-edge-deploy-configure-compute-advanced/verify-data-2.png)
 
1.    将数据添加到本地共享。

    ![验证数据转换](./media/azure-stack-edge-deploy-configure-compute-advanced/verify-data-3.png)
 
    数据将移到云共享。

    ![验证数据转换](./media/azure-stack-edge-deploy-configure-compute-advanced/verify-data-4.png)  

    然后，将数据从云共享推送到存储帐户。 若要查看数据，请转到存储帐户，然后选择“存储资源管理器”  。 可以在存储帐户中查看上传的数据。

    ![验证数据转换](./media/azure-stack-edge-deploy-configure-compute-advanced/verify-data-5.png)
 
现已完成验证过程。

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
> * 配置计算
> * 添加共享
> * 添加触发器
> * 添加计算模块
> * 验证数据转换和传输

若要了解如何管理 Azure Stack Edge 设备，请参阅：

> [!div class="nextstepaction"]
> [使用本地 Web UI 管理 Azure Stack Edge](azure-stack-edge-manage-access-power-connectivity-mode.md)
