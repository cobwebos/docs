使用 Azure 资源管理器中，你定义的值你想要在部署模板时指定的参数。 该模板包含一个包含所有参数值的节称为参数。
应定义要部署的项目的基础或您要部署到的环境将会变化这些值的参数。 未定义的将始终保持不变的值的参数。 每个参数值用于在模板中定义部署的资源。 

在定义参数时，使用**allowedValues**字段来指定哪些值用户可以在部署过程中提供。 使用**defaultValue**字段将值分配给该参数，如果在部署过程不提供任何值。

我们将介绍模板中的每个参数。

### <a name="sitename"></a>siteName
你想要创建 web 应用的名称。

    "siteName":{
      "type":"string"
    }

### <a name="hostingplanname"></a>hostingPlanName
要使用来托管 web 应用的 App Service 计划名称。

    "hostingPlanName":{
      "type":"string"
    }

### <a name="sku"></a>Sku
宿主计划的定价层。

    "sku": {
      "type": "string",
      "allowedValues": [
        "F1",
        "D1",
        "B1",
        "B2",
        "B3",
        "S1",
        "S2",
        "S3",
        "P1",
        "P2",
        "P3",
        "P4"
      ],
      "defaultValue": "S1",
      "metadata": {
        "description": "The pricing tier for the hosting plan."
      }
    }

模板将定义此参数允许的值，如果未不指定任何值，则分配默认值 (S1)。

### <a name="workersize"></a>workerSize
托管计划 （小型、 中型或大型） 实例大小。

    "workerSize":{
      "type":"string",
      "allowedValues":[
        "0",
        "1",
        "2"
      ],
      "defaultValue":"0"
    }

模板将定义为 （0、 1 或 2），此参数允许的值，如果未不指定任何值，则分配默认值 (0)。 值对应对小型、 中型和大型。

