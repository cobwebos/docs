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
ms.openlocfilehash: 0a293d74c9e37a6771c5bb246b74bda38db3b7c3
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406467"
---
# <a name="tutorial-define-a-new-azure-iot-edge-device-type-in-your-azure-iot-central-application-preview-features"></a>教程：在 Azure IoT Central 应用程序中定义新的 Azure IoT Edge 设备类型（预览功能）

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

本教程向构建人员介绍如何使用设备模板在 Azure IoT Central 应用程序中定义新的 Azure IoT Edge 设备类型。 

有关概述，请参阅 [Azure IoT Central（预览版功能）是什么？](overview-iot-central.md)。 

IoT Edge 包含三个组件：
* IoT Edge 模块是容器，可以运行 Azure 服务、合作伙伴服务或者你自己的代码  。 这些模块部署到 IoT Edge 设备，在设备上以本地方式运行。
* IoT Edge 运行时在每个 IoT Edge 设备上运行，并管理部署到每个设备的模块  。
* 可以通过基于云的界面远程监视和管理 IoT Edge 设备  。 IoT Central 是云接口。

Azure IoT Edge 设备可以是具有连接到 IoT Edge 设备的下游设备的网关设备  。 本教程共享有关下游设备连接模式的详细信息。

设备模板定义设备和 IoT Edge 模块的功能  。 功能包括模块发送的遥测数据、模块属性以及模块响应的命令。

本教程将创建一个“环境传感器”设备模板。 环境传感器设备：

* 发送温度等遥测数据。
* 在云中更新时响应可写属性，例如遥测数据发送间隔。
* 响应重置温度等命令。

本教程还将创建一个“环境网关”设备模板。 环境网关设备：

* 发送温度等遥测数据。
* 在云中更新时响应可写属性，例如遥测数据发送间隔。
* 响应重置温度等命令。
* 允许与其他设备功能模型建立关系。


本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建新的 Azure IoT Edge 设备设备模板。
> * 上传部署清单。
> * 为每个模块创建功能，包括遥测、属性和命令。
> * 定义模块遥测的可视化效果。
> * 向下游设备模板添加关系。
> * 发布设备模板。

## <a name="prerequisites"></a>先决条件

若要完成本教程，需要[创建 Azure IoT Central 应用程序](quick-deploy-iot-central.md)。


## <a name="downstream-device-relationships-with-a-gateway-and-modules"></a>下游设备与网关和模块的关系

下游设备可通过 `$edgeHub` 模块连接到 IoT Edge 网关设备。 在此方案中，此 IoT Edge 设备将成为透明网关。

![透明网关关系图](./media/tutorial-define-edge-device-type/gateway-transparent.png)

下游设备也可通过自定义模块连接到 IoT Edge 网关设备。 在以下方案中，下游设备通过 Modbus 自定义模块进行连接。

![自定义模块连接关系图](./media/tutorial-define-edge-device-type/gateway-module.png)

下图显示了通过两种类型的模块（自定义和 `$edgeHub`）与 IoT Edge 网关设备的连接。  

![通过两种连接模块连接的关系图](./media/tutorial-define-edge-device-type/gateway-module-transparent.png)

最后，下游设备可通过多个自定义模块连接到 IoT Edge 网关设备。 下图显示了通过 Modbus 自定义模块、BLE 自定义模块和 `$edgeHub` 模块连接的下游设备。 

![通过多个自定义模块连接的关系图](./media/tutorial-define-edge-device-type/gateway-module2-transparent.png)


## <a name="create-a-template"></a>创建模板

构建人员可在应用程序中创建和编辑 IoT Edge 设备模板。 发布设备模板后，可连接用于实现该设备模板的实际设备。

### <a name="select-device-template-type"></a>选择设备模板类型 

若要将新设备模板添加到应用程序，请在左侧窗格中选择“设备模板”  。

![预览应用程序的屏幕截图，其中突出显示了“设备模板”](./media/tutorial-define-edge-device-type/edgedevicetemplate.png)

选择“+ 新建”，开始创建新的设备模板  。

![“设备模板”页的屏幕截图，其中突出显示了“新建”](./media/tutorial-define-edge-device-type/edgedevicetemplatenew.png)

在“选择模板类型”页上，选择“Azure IoT Edge”，然后选择“下一步：    自定义”。

![“选择模板类型”页的屏幕截图](./media/tutorial-define-edge-device-type/selectiotedge.png)

### <a name="customize-device-template"></a>自定义设备模板

在 IoT Edge 中，你能以模块的形式来部署和管理业务逻辑。 IoT Edge 模块是由 IoT Edge 托管的最小计算单位，可以包含 Azure 服务（如 Azure 流分析）或你自己特定于解决方案的代码。 若要了解如何开发、部署和维护模块，请参阅 [IoT Edge 模块](../../iot-edge/iot-edge-modules.md)。

从较高层面讲，部署清单是配置了所需属性的模块孪生的列表。 部署清单告知某个 IoT Edge 设备（或一组设备）要安装哪些模块，以及如何配置这些模块。 部署清单包含每个模块孪生的所需属性。 IoT Edge 设备将报告每个模块的报告属性。

使用 Visual Studio Code 创建部署清单。 若要了解详细信息，请参阅[适用于 Visual Studio Code 的 Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)。

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

#### <a name="upload-an-iot-edge-deployment-manifest"></a>上传 IoT Edge 部署清单

在“自定义设备”页的“上传 Azure IoT Edge 部署清单”下，选择“浏览”    。 

![“自定义设备”页的屏幕截图，其中突出显示了“浏览”](./media/tutorial-define-edge-device-type/edgedevicetemplateuploadmanifest.png)

如果计划创建 IoT Edge 网关设备模板，请确保选中“具有下游设备的网关设备”  。

![“自定义设备”页的屏幕截图，其中突出显示了“具有下游设备的网关设备”](./media/tutorial-define-edge-device-type/gateway-upload.png)

在文件选择对话框中，选择部署清单文件，然后选择“打开”  。

IoT Edge 根据架构验证部署清单文件。 如果验证成功，请选择“查看”  。

![“自定义设备”页的屏幕截图，其中突出显示了“部署清单”和“查看”](./media/tutorial-define-edge-device-type/deploymentmanifestvalidate.png)

以下流程图显示了 IoT Central 中的部署清单生命周期。

![部署清单生命周期的流程图](./media/tutorial-define-edge-device-type/dmflow.png)

接下来，将显示包含部署清单详细信息的查看页。 此页显示部署清单中的模块列表。 在本教程中，请注意列出了 `SimulatedTemperatureSensor` 模块。 选择“创建”  。

![“查看”页的屏幕截图，其中突出显示了“模块”和“创建”](./media/tutorial-define-edge-device-type/edgedevicetemplatereview.png)

如果选择了网关设备，则会看到以下查看页。

![“查看”页的屏幕截图，其中突出显示了“Azure IoT Edge 网关”](./media/tutorial-define-edge-device-type/gateway-review.png)


使用模块功能模型创建设备模板。 在本教程中，使用 `SimulatedTemperatureSensor` 模块功能模型创建设备模板。 

将设备模板的标题更改为“环境传感器设备模板”  。

![设备模板的屏幕截图，其中突出显示了更新标题](./media/tutorial-define-edge-device-type/edgedevicetemplatelanding.png)

在 IoT Edge 设备中，IoT 即插即用模型如下：
* 每个 IoT Edge 设备模板都具有设备功能模型。
* 对于部署清单中列出的每个自定义模块，都会生成模块功能模型。
* 在每个模块功能模型和设备功能模型之间建立关系。
* 模块功能模型可实现模块接口。
* 每个模块接口都包含遥测、属性和命令。

![IoT Edge 建模关系图](./media/tutorial-define-edge-device-type/edgemodelling.png)

#### <a name="add-capabilities-to-a-module-capability-model"></a>向模块功能模型添加功能

下面是 `SimulatedTemperatureSensor` 模块的示例输出：
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

可以向 `SimulatedTemperatureSensor` 模块添加功能，该模块将反映前面的输出。 

1. 若要管理 `SimulatedTemperatureSensor` 模块功能模型的接口，请选择“管理” > “添加功能”   。 

    ![“环境传感器模板”的屏幕截图，其中突出显示了“添加功能”](./media/tutorial-define-edge-device-type/edgetemplateaddcapability.png)
  
1. 将计算机添加为对象类型。
  
    ![“环境传感器模板功能”页的屏幕截图，其中突出显示了“架构”](./media/tutorial-define-edge-device-type/edgetemplatemachineobject.png)

1. 选择“定义”  。 在出现的对话框中，将对象名称更改为“计算机”  。 创建温度和压力属性，然后选择“应用”  。
  
    ![属性对话框的屏幕截图，其中突出显示了各种选项](./media/tutorial-define-edge-device-type/edgetemplatemachineattributes.png)
  
1. 将“环境”添加为对象类型  。

1. 选择“定义”  。 在出现的对话框中，将对象名称更改为“环境”  。 创建温度和湿度属性，然后选择“应用”  。
  
    ![属性对话框的屏幕截图，其中突出显示了各种选项](./media/tutorial-define-edge-device-type/edgetemplateambientattributes.png)

  
1. 将 `timeCreated` 添加为 `DateTime` 类型，然后选择“保存”  。
  
    ![“环境传感器模板”的屏幕截图，其中突出显示了“保存”](./media/tutorial-define-edge-device-type/edgetemplateallattributes.png)


### <a name="add-relationships"></a>添加关系

如果选择 IoT Edge 设备作为网关设备，则可以将下游关系添加到将连接到网关设备的设备的设备功能模型。
  
  ![“环境网关模板”的屏幕截图，其中突出显示了“添加关系”](./media/tutorial-define-edge-device-type/gateway-add-relationship.png)

可在设备或模块中添加关系。
  
  ![“环境网关模板”的屏幕截图，其中突出显示了设备和模块级关系](./media/tutorial-define-edge-device-type/gateway-relationship-types.png)


可以选择下游设备功能模型，也可以选择星号符号。 
  
  ![“环境网关模板”的屏幕截图，其中突出显示了“目标”](./media/tutorial-define-edge-device-type/gateway-downstream-rel.png)

  对于本教程，请选择星号。 此选项允许任何下游关系。 再选择“保存”  。

  ![“环境网关模板”的屏幕截图，其中突出显示了“目标”](./media/tutorial-define-edge-device-type/gateway-add-relationship-asterix.png)


### <a name="add-cloud-properties"></a>添加云属性

设备模板可以包含云属性。 云属性仅存在于 IoT Central 应用程序中，永远不会与设备相互发送和接收信息。

1. 选择“云属性” > “+ 添加云属性”   。 使用下表中的信息将云属性添加到设备模板。

    | 显示名称      | 语义类型 | 架构 |
    | ----------------- | ------------- | ------ |
    | 最后维修日期 | 无          | Date   |
    | 客户名称     | 无          | String |

2. 选择“保存”。 

  
    ![“环境传感器模板”的屏幕截图，其中突出显示了“保存”](./media/tutorial-define-edge-device-type/edgetemplatecloudproperties.png)

### <a name="add-customizations"></a>添加自定义项

使用自定义项来修改接口，或者将 IoT Central 特定的功能添加到不需要对设备功能模型进行版本控制的功能中。 当功能模型处于草稿或已发布状态时，可以自定义字段。 只能自定义不会破坏接口兼容性的字段。 例如，可以：

- 自定义功能的显示名称和单位。
- 添加在图表上显示值时要使用的默认颜色。
- 指定属性的初始值、最小值和最大值。

无法自定义功能名称或功能类型。

完成自定义后，选择“保存”  。
  
![“环境传感器模板自定义”页的屏幕截图](./media/tutorial-define-edge-device-type/edgetemplatecustomize.png)


### <a name="create-views"></a>创建视图

构建人员可将应用程序自定义为向操作员显示有关环境传感器设备的信息。 完成自定义后，操作员可以管理连接到应用程序的环境传感器设备。 可为操作员创建两种类型的视图来让他们与设备交互：

* 用于查看和编辑设备与云属性的窗体。
* 用于可视化设备的仪表板。

### <a name="configure-a-view-to-visualize-devices"></a>配置用于可视化设备的视图

设备仪表板可让操作员使用图表和指标来可视化设备。 构建人员可以定义要在设备仪表板上显示的信息。 可为设备定义多个仪表板。 若要创建一个仪表板来可视化环境传感器遥测数据，请选择“视图” > “可视化设备”   ：

  
![“环境传感器模板视图”页的屏幕截图，其中突出显示了“可视化设备”](./media/tutorial-define-edge-device-type/visualizingthedevice.png)


“环境遥测”和“计算机遥测”属于复杂对象。 若要创建图表：

1. 拖动“环境遥测”并选择“折线图”   。 
  
   ![“环境传感器模板”的屏幕截图，其中突出显示了“环境遥测”和“折线图”](./media/tutorial-define-edge-device-type/sensorambientchart.png)

1. 选择“配置”图标。 选择“温度”和“湿度”以可视化数据，然后选择“更新配置”    。 
  
   ![“环境传感器模板”的屏幕截图，其中突出显示了各种选项](./media/tutorial-define-edge-device-type/sensorambienttelemetrychart.png)

1. 选择“保存”。 

可以添加更多的磁贴用于显示其他属性或遥测值。 还可以添加静态文本、链接和图像。 若要移动仪表板上的某个磁贴或调整其大小，请将鼠标指针移到该磁贴上，然后将该磁贴拖放到新位置或调整其大小。
  
![“环境传感器模板仪表板”视图的屏幕截图](./media/tutorial-define-edge-device-type/viewsdashboard.png)

### <a name="add-a-device-form"></a>添加设备窗体

设备窗体可让操作员编辑可写的设备属性和云属性。 构建人员可以定义多个窗体，并选择要在每个窗体中显示的设备属性和云属性。 还可以在窗体中显示只读的设备属性。

若要创建窗体用于查看和编辑环境传感器属性：

1. 在“环境传感器模板”中，转到“视图”   。 选择“编辑设备和云数据”  磁贴以添加新视图。
  
   ![“环境传感器模板视图”页的屏幕截图，其中突出显示了“编辑设备和云数据”](./media/tutorial-define-edge-device-type/editingdeviceandclouddata.png)

1. 输入窗体名称“环境传感器属性”。 

1. 将“客户名称”和“上次检修日期”云属性拖放到窗体中的现有部分。  
  
   ![“环境传感器模板视图”页的屏幕截图，其中突出显示了各种选项](./media/tutorial-define-edge-device-type/views-properties.png)

1. 选择“保存”。 

## <a name="publish-a-device-template"></a>发布设备模板

在创建模拟环境传感器或连接实际环境传感器之前，需要发布设备模板。

若要发布设备模板：

1. 从“设备模板”页转到你的设备模板。 

2. 选择“发布”  。
  
    ![“环境传感器模板”的屏幕截图，其中突出显示了“发布”](./media/tutorial-define-edge-device-type/edgetemplatepublish.png)

1. 在“发布设备模板”对话框中，选择“发布”   。
  
    ![“发布设备模板”对话框的屏幕截图，其中突出显示了“发布”](./media/tutorial-define-edge-device-type/edgepublishtemplate.png)

发布设备模板后，它将显示在“设备”页上，操作员可以看到它。  在发布的设备模板中，除非创建新的版本，否则无法编辑设备功能模型。 但是，可以在发布的设备模板中更新云属性、自定义项和视图。 这些更新不会导致创建新版本。 进行任何更改后，选择“发布”将这些更改推送给操作员  。
  
![已发布模板的“设备模板”列表的屏幕截图](./media/tutorial-define-edge-device-type/publishedtemplate.png)

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

* 创建新的边缘作为叶设备模板。
* 从上传的部署清单中生成模块。
* 添加复杂类型遥测和属性。
* 创建云属性。
* 创建自定义项。
* 定义设备遥测的可视化效果。
* 发布边缘设备模板。

在 Azure IoT Central 应用程序中创建设备模板后，接下来可以执行此操作：

> [!div class="nextstepaction"]
> [连接设备](./tutorial-connect-pnp-device.md)
