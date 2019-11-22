---
title: 在 Azure IoT Central 中定义新的 Azure IoT Edge 设备类型 | Microsoft Docs
description: 本教程向构建人员介绍如何在 Azure IoT Central 应用程序中创建新的 Azure IoT Edge 设备。 其中介绍了如何为类型定义遥测、状态、属性和命令。
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: c205b4dd4871ed53e32dce72f12cc2dcfb3baf41
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/09/2019
ms.locfileid: "73892023"
---
# <a name="tutorial-define-a-new-azure-iot-edge-device-type-in-your-azure-iot-central-application-preview-features"></a>教程：在 Azure IoT Central 应用程序中定义新的 Azure IoT Edge 设备类型（预览版功能）

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

本教程向构建人员介绍如何使用设备模板在 Azure IoT Central 应用程序中定义新的 Azure IoT Edge 设备类型。 

若要获取 Azure IoT Edge 的概述，请[参阅此文章](overview-iot-central.md)。 

Azure IoT Edge 包含三个组件：
* IoT Edge 模块是容器，可以运行 Azure 服务、第三方服务或者你自己的代码  。 这些模块部署到 IoT Edge 设备，在设备上以本地方式执行。
* IoT Edge 运行时在每个 IoT Edge 设备上运行，并管理部署到每个设备的模块  。
* 可以通过基于云的界面远程监视和管理 IoT Edge 设备  。 IoT Central 将是云接口。

Azure IoT Edge 设备可以是具有连接到 Azure IoT Edge 设备的下游设备的网关设备  。 本教程将讨论下游设备连接模式。

设备模板定义设备和 IoT Edge 模块的功能  。 功能包括模块发送的遥测数据、模块属性以及模块响应的命令。

本教程将创建一个“环境传感器”设备模板。  环境传感器设备：

* 发送温度等遥测数据。
* 在云中更新时响应可写属性，例如遥测数据发送间隔。
* 响应重置温度等命令。

本教程还将创建一个“环境网关”设备模板  。 环境网关设备：

* 发送温度等遥测数据。
* 在云中更新时响应可写属性，例如遥测数据发送间隔。
* 响应重置温度等命令。
* 允许与其他设备功能模型建立关系


本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建新的 Azure IoT Edge 设备设备模板。
> * 上传部署清单。
> * 为每个模块创建功能，包括遥测、属性和命令
> * 定义模块遥测的可视化效果。
> * 向下游设备模板添加关系
> * 发布设备模板。

## <a name="prerequisites"></a>先决条件

若要完成本教程，需要一个 Azure IoT Central 应用程序。 按照此快速入门[创建 Azure IoT Central 应用程序](quick-deploy-iot-central.md)。


## <a name="downstream-device-relationships-with-gateway--modules"></a>下游设备与网关和模块的关系

下游设备可通过 $edgeHub 模块连接到 Azure IoT Edge 网关设备。 在此方案中，此 Azure IoT Edge 设备将成为透明网关

![Central 应用程序页](./media/tutorial-define-edge-device-type/gateway-transparent.png)

下游设备可通过自定义模块连接到 Azure IoT Edge 网关设备。 在下面的方案中，下游设备通过 Modbus 自定义模块进行连接，并且下游设备可通过 $edgeHub 模块连接到 Azure IoT Edge 网关设备。

![Central 应用程序页](./media/tutorial-define-edge-device-type/gateway-module.png)

下游设备可通过自定义模块连接到 Azure IoT Edge 网关设备。 在下面的方案中，下游设备通过 Modbus 自定义模块进行连接。 

![Central 应用程序页](./media/tutorial-define-edge-device-type/gateway-module-transparent.png)

下游设备可通过多个自定义模块连接到 Azure IoT Edge 网关设备。 在下面的方案中，下游设备通过 Modbus 自定义模块进行连接，并且 BLE 自定义模块和下游设备可通过 $edgeHub 模块连接到 Azure IoT Edge 网关设备。 

![Central 应用程序页](./media/tutorial-define-edge-device-type/gateway-module2-transparent.png)


## <a name="create-a-template"></a>创建模板

构建人员可在应用程序中创建和编辑 Azure IoT Edge 设备模板。 发布设备模板后，可连接用于实施该设备模板的实际设备。

### <a name="select-device-template-type"></a>选择设备模板类型 

若要将新设备模板添加到应用程序，请转到“设备模板”页。  为此，请在左窗格中选择“设备模板”选项卡  。

![Central 应用程序页](./media/tutorial-define-edge-device-type/edgedevicetemplate.png)

单击“+ 新建”，开始创建新的设备模板  。

![设备模板 - 新建](./media/tutorial-define-edge-device-type/edgedevicetemplatenew.png)

你将登陆设备模板类型选择页。 选择“Azure IoT Edge”磁贴并单击底部的“下一步:   自定义”按钮

![设备模板选择 - Azure IoT Edge](./media/tutorial-define-edge-device-type/selectiotedge.png)

### <a name="customize-device-template"></a>自定义设备模板

通过 Azure IoT Edge，可让你以模块的形式来部署和管理业务逻辑。 Azure IoT Edge 模块是由 IoT Edge 托管的最小计算单位，可以包含 Azure 服务（如 Azure 流分析）或你自己特定于解决方案的代码  。 如需了解如何开发、部署和维护模块，请阅读 [IoT Edge 模块](../../iot-edge/iot-edge-modules.md)。

从较高层面讲，部署清单是配置了所需属性的模块孪生的列表。 部署清单告知某个 IoT Edge 设备（或一组设备）要安装哪些模块，以及如何配置这些模块。 部署清单包含每个模块孪生的所需属性。 IoT Edge 设备将报告每个模块的报告属性。

使用 Visual Studio Code 创建部署清单。 请按照有关如何创建[部署清单](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)的文档进行操作。

下面是一个基本的部署清单，其中一个模块作为本教程要使用的示例。 复制以下 JSON，并将其另存为 .json 文件。 

   ```JSON
   {
     "modulesContent": {
       "$edgeAgent": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "runtime": {
             "type": "docker",
             "settings": {
               "minDockerVersion": "v1.25",
               "loggingOptions": "",
               "registryCredentials": {}
             }
           },
           "systemModules": {
             "edgeAgent": {
               "type": "docker",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                 "createOptions": "{}"
               }
             }
           },
           "modules": {
             "SimulatedTemperatureSensor": {
               "version": "1.0",
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                 "createOptions": "{}"
               }
             }
           }
         }
       },
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "routes": {
               "route": "FROM /* INTO $upstream"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       },
       "SimulatedTemperatureSensor": {
         "properties.desired": {
              "SendData": true,
              "SendInterval": 10
         }
       }
     }
   }
   ```

上传 Azure IoT Edge 部署清单 

单击“浏览”  按钮 

![设备模板 - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgedevicetemplateuploadmanifest.png)

如果计划创建 Azure IoT Edge 网关设备模板，请确保选中“具有下游设备的网关设备”  复选框

![设备模板 - Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-upload.png)

系统将显示一个文件选择对话框。 选择部署清单文件，然后单击“打开”按钮  。

部署清单文件将根据架构进行验证。 成功验证后，单击“查看”  按钮

![设备模板 - Azure IoT Edge](./media/tutorial-define-edge-device-type/deploymentmanifestvalidate.png)

以下是 IoT Central 中的部署清单生命周期流。

![设备模板 - Azure IoT Edge](./media/tutorial-define-edge-device-type/dmflow.png)

将显示包含部署清单详细信息的查看页。 部署清单中的模块列表将显示在查看页上。 在本教程中，你将看到列出的 SimulatedTemperatureSensor 模块。 单击“创建”  按钮。

![设备模板 - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgedevicetemplatereview.png)

如果选择了网关设备，则会看到此查看页

![设备模板 - Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-review.png)


将显示“创建新的设备模板”旋转图标，表示正在 IoT Central 中创建设备模板。

使用模块功能模型创建设备模板。 在本教程中，你将看到 SimulatedTemperatureSensor 模块功能模型创建。 

将设备模板的标题更改为环境传感器设备模板。

![设备模板 - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgedevicetemplatelanding.png)

Azure IoT Edge 设备即插即用建模按如下所示执行
* 每个 Azure IoT Edge 设备模板都将具有设备功能模型 
* 对于部署清单中列出的每个自定义模块，将生成模块功能模型 
* 将在每个模块功能模型和设备功能模型之间建立关系 
* 模块功能模型可实现模块接口 
* 每个模块接口都包含
   - 遥测
   - 属性
   - 命令

![设备模板 - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgemodelling.png)

向模块功能模型添加功能 

下面是 SimulatedTemperatureSensor 模块的示例输出
```json
{

    "machine": {

        "temperature": 75.0,
        "pressure": 40.2
    },
    "ambient": {
        "temperature": 23.0,
        "humidity": 30.0
    },
    "timeCreated": ""
}
```

向将反映上述 JSON 的 SimulatedTemperatureSensor 模块添加功能。 

* 单击“管理”SimulatedTemperatureSensor 模块功能模型接口  。 单击“添加功能”  。 

    ![设备模板 - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplateaddcapability.png)
  
* 由于计算机为复杂类型，因此将其添加为对象类型
  
    ![设备模板 - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatemachineobject.png)

    单击“定义”  。 在弹出窗口模式中，将对象名称更改为计算机，创建属性温度和压力，并单击“应用” 
  
    ![设备模板 - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatemachineattributes.png)
  
* 由于环境为复杂类型，因此将其添加为对象类型

    单击“定义”  。 在弹出窗口模式中，将对象名称更改为环境，创建属性温度和湿度，并单击“应用” 
  
    ![设备模板 - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplateambientattributes.png)

  
* 将 timeCreated 添加为 DateTime 类型，并单击“保存” 
  
    ![设备模板 - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplateallattributes.png)


### <a name="add-relationships"></a>添加关系

如果选择 Azure IoT Edge 设备作为网关设备，则可以将下游关系添加到将连接到网关设备的设备的设备功能模型。
  
  ![设备模板 - Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-add-relationship.png)

可在设备或模块中添加关系。
  
  ![设备模板 - Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-relationship-types.png)


可以选择下游设备功能模型或选择星号。 
  
  ![设备模板 - Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-downstream-rel.png)

  在本教程中，我们将选择星号，这意味着将允许任何下游关系。 单击“保存” 

  ![设备模板 - Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-add-relationship-asterix.png)


### <a name="add-cloud-properties"></a>添加云属性

设备模板可以包含云属性。 云属性仅存在于 IoT Central 应用程序中，永远不会与设备相互发送和接收信息。

1. 依次选择“云属性”、“+ 添加云属性”。   使用下表中的信息将云属性添加到设备模板。

    | 显示名称      | 语义类型 | 架构 |
    | ----------------- | ------------- | ------ |
    | 最后维修日期 | 无          | Date   |
    | 客户名称     | 无          | String |

2. 选择“保存”以保存更改： 

  
    ![云属性 - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatecloudproperties.png)

### <a name="add-customizations"></a>添加自定义项

需要修改接口，或者将 IoT Central 特定的功能添加到不需要对设备功能模型进行版本控制的功能时，可以使用自定义项。 当功能模型处于草稿或已发布状态时，可以自定义字段。 只能自定义不会破坏接口兼容性的字段。 例如，可以：

- 自定义功能的显示名称和单位。
- 添加在图表上显示值时要使用的默认颜色。
- 指定属性的初始值、最小值和最大值。

无法自定义功能名称或功能类型。 单击“保存” 
  
![自定义 - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatecustomize.png)


### <a name="create-views"></a>创建视图

构建人员可将应用程序自定义为向操作员显示有关环境传感器设备的信息。 完成自定义后，操作员可以管理连接到应用程序的环境传感器设备。 可为操作员创建两种类型的视图来让他们与设备交互：

* 用于查看和编辑设备与云属性的窗体。
* 用于可视化设备的仪表板。

### <a name="configure-a-view-to-visualize-devices"></a>配置用于可视化设备的视图

设备仪表板可让操作员使用图表和指标来可视化设备。 构建人员可以定义要在设备仪表板上显示的信息。 可为设备定义多个仪表板。 若要创建一个仪表板来可视化环境传感器遥测数据，请依次选择“视图”、“可视化设备”：  

  
![视图 - Azure IoT Edge](./media/tutorial-define-edge-device-type/visualizingthedevice.png)


环境遥测和计算机遥测属于复杂对象，若要创建图表，请执行以下操作

拖动“环境遥测”并选择折线图。 
  
![视图 - Azure IoT Edge](./media/tutorial-define-edge-device-type/sensorambientchart.png)

单击“配置”图标，选择温度和湿度以可视化数据，然后单击“更新配置”按钮  。 
  
![视图 - Azure IoT Edge](./media/tutorial-define-edge-device-type/sensorambienttelemetrychart.png)

选择“保存”以保存视图： 

可以添加更多的磁贴用于显示其他属性或遥测值。 还可以添加静态文本、链接和图像。 若要移动仪表板上的某个磁贴或调整其大小，请将鼠标指针移到该磁贴上，然后将该磁贴拖放到新位置或调整其大小。
  
![视图 - Azure IoT Edge](./media/tutorial-define-edge-device-type/viewsdashboard.png)

### <a name="add-a-device-form"></a>添加设备窗体

设备窗体可让操作员编辑可写的设备属性和云属性。 构建人员可以定义多个窗体，并选择要在每个窗体中显示的设备属性和云属性。 还可以在窗体中显示只读的设备属性。

若要创建窗体用于查看和编辑环境传感器属性：

导航到“环境传感器”  模板中的“视图”  。 选择“编辑设备和云数据”  磁贴以添加新视图。
  
![视图 - Azure IoT Edge](./media/tutorial-define-edge-device-type/editingdeviceandclouddata.png)

输入窗体名称“环境传感器属性”。 

将“客户名称”和“上次检修日期”云属性拖放到窗体中的现有部分。  
  
![视图 - Azure IoT Edge](./media/tutorial-define-edge-device-type/views-properties.png)

选择“保存”以保存视图。 

### <a name="generate-default-views"></a>生成默认视图

Azure IoT Edge 模板不支持生成默认视图功能 

## <a name="publish-device-template"></a>发布设备模板

在创建模拟环境传感器或连接实际环境传感器之前，需要发布设备模板。

若要发布设备模板：

1. 从“设备模板”页转到你的设备模板。 

2. 选择“发布”  。
  
    ![视图 - 发布](./media/tutorial-define-edge-device-type/edgetemplatepublish.png)

1. 在“发布设备模板”对话框中，选择“发布”：  
  
    ![视图 - 发布](./media/tutorial-define-edge-device-type/edgepublishtemplate.png)

发布设备模板后，它将显示在“设备”页上，操作员可以看到它。  在发布的设备模板中，除非创建新的版本，否则无法编辑设备功能模型。 但是，在不创建新版本的情况下，可以在发布的设备模板中更新云属性、自定义项和视图。 进行任何更改后，选择“发布”将这些更改推送给操作员。 
  
![视图 - 发布](./media/tutorial-define-edge-device-type/publishedtemplate.png)

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

* 创建新的边缘作为叶设备模板
* 从上传的部署清单中生成模块
* 添加复杂类型遥测和属性
* 创建云属性。
* 创建自定义项。
* 定义设备遥测的可视化效果。
* 发布边缘设备模板。

在 Azure IoT Central 应用程序中创建设备模板后，建议接下来执行以下后续步骤：

> [!div class="nextstepaction"]
> [连接设备](./tutorial-connect-pnp-device.md)
