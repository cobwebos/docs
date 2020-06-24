---
title: 使用示例客户端应用了解基础知识
titleSuffix: Azure Digital Twins
description: 使用示例命令行应用程序了解 Azure 数字孪生 SDK 的教程
author: baanders
ms.author: baanders
ms.date: 5/8/2020
ms.topic: tutorial
ms.service: digital-twins
ROBOTS: NOINDEX, NOFOLLOW
ms.openlocfilehash: 67b476b2ec6ec0c841639f7aa1d94a0d9d3d3304
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2020
ms.locfileid: "85262323"
---
# <a name="explore-azure-digital-twins-with-a-sample-client-app"></a>使用示例客户端应用了解 Azure 数字孪生

[!INCLUDE [Azure Digital Twins current preview status](../../includes/digital-twins-preview-status.md)]

本教程介绍实现了命令行客户端应用程序的示例应用程序，用于与 Azure 数字孪生实例进行交互。 客户端应用与[教程：编写客户端应用](tutorial-code.md)中编写的应用类似。

可以使用此示例执行基本的 Azure 数字孪生操作，例如上传模型、创建和修改孪生以及创建关系。 还可以查看示例的代码，以了解 Azure 数字孪生 API，并通过修改示例项目等各种方式来练习实现你自己的命令。

在本教程中，你将...
1. 设置 Azure 数字孪生实例
2. 配置示例命令行应用以与实例进行交互
3. 使用命令行应用来探索 Azure 数字孪生，包括模型、数字孪生、关系和查询   

[!INCLUDE [Azure Digital Twins tutorial: sample prerequisites](../../includes/digital-twins-tutorial-sample-prereqs.md)]

[!INCLUDE [Azure Digital Twins tutorial: configure the sample project](../../includes/digital-twins-tutorial-sample-configure.md)]

## <a name="explore-with-the-sample-solution"></a>使用示例解决方案进行探索

现在，实例和示例应用已经配置完毕，你将使用示例项目和一些预先编写的示例代码来扩建和探索基本的 Azure 数字孪生解决方案。 解决方案的主要组成部分是模型、数字孪生和关系，生成结果为可查询的环境孪生图   。

### <a name="model-a-physical-environment-with-dtdl"></a>使用 DTDL 为物理环境建模

创建 Azure 数字孪生解决方案的第一步是为环境定义孪生[模型](concepts-models.md)。 

模型类似于面向对象的编程语言中的类；它们为[数字孪生](concepts-twins-graph.md)提供了日后可遵循并实例化的用户定义的模板。 它们用类似于 JSON 的语言（称为数字孪生定义语言 (DTDL)）编写，并且可以定义孪生的属性、遥测、关系和组件   。

> [!NOTE]
> DTDL 还允许在数字孪生上定义命令。 但是，Azure 数字孪生服务当前不支持命令。

在打开了 AdtE2ESample 项目的 Visual Studio 窗口中，使用“解决方案资源管理器”窗格导航到 AdtSampleApp\SampleClientApp\Models 文件夹 。 此文件夹包含示例模型。

选择 Room.json，在编辑窗口中将其打开，并通过以下方式更改：

* 更新版本号，以指出正在提供此模型的更新版本。 为此，请将 `@id` 末尾的 1 更改为 2 。 大于当前版本号的任何数字也有效。
* 编辑属性。 将 `Humidity` 属性的名称更改为 HumidityLevel（或根据需要将其更改为其他名称。 如果使用的名称不是 HumidityLevel，请记住自己使用的名称，并在整个教程中继续使用该名称，而不使用 HumidityLevel） 。
* 添加属性。 在结束于第 15 行的 `HumidityLevel` 属性下方，粘贴以下代码以将 `RoomName` 属性添加到 room 中：

    ```json
    ,
    {
      "@type": "Property",
      "name": "RoomName",
      "schema": "string"
    }
    ```
* 添加关系。 在刚刚添加的 `RoomName` 属性下方，粘贴以下代码，使此类孪生能够与其他孪生形成包含关系：

    ```json
    ,
    {
      "@type": "Relationship",
      "name": "contains",
    }
    ```

完成后，更新后的模型应如下所示：

:::image type="content" source="media/tutorial-command-line-app/room-model.png" alt-text="编辑了更新版本号、HumidityLevel 和 RoomName 属性和包含关系的 Room.json" border="false":::

在继续操作之前，请务必保存该文件。

> [!TIP]
> 如果想尝试创建自己的模型，可以将 Room 模型代码粘贴到新文件中，该文件以 .json 为扩展名，并保存在 AdtSampleApp\SampleClientApp\Models 文件夹中  。 然后，尝试添加属性和关系以表示想要的任何内容。 也可以查看此文件夹中的其他示例模型以获得灵感。

> [!TIP] 
> 有一种与语言无关的 [DTDL 验证程序示例](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator)，可用来检查模型文件，以确保 DTDL 有效。 它是在 DTDL 分析程序库的基础上构建的，详情请参阅[操作方法：分析和验证模型](how-to-use-parser.md)。

### <a name="get-started-with-the-command-line-app"></a>命令行应用入门

现在，你已经定义了模型，剩下的步骤包括使用示例应用与 Azure 数字孪生实例进行交互。 使用工具栏中的以下按钮运行项目：

:::image type="content" source="media/tutorial-command-line-app/start-button-sample.png" alt-text="Visual Studio 开始按钮（SampleClientApp 项目）":::

控制台窗口随即打开，执行身份验证，然后等待命令。 
* 通过浏览器处理身份验证：默认的 Web 浏览器将打开，并显示身份验证提示。 根据此提示使用 Azure 凭据登录。 然后，可以关闭浏览器选项卡或窗口。

下面是项目控制台的外观的屏幕截图：

:::image type="content" source="media/tutorial-command-line-app/command-line-app.png" alt-text="来自命令行应用的欢迎消息":::

> [!TIP]
> 有关在此项目中可以使用的所有可能命令的列表，请在项目控制台中输入 `help`，然后按回车键。
> :::image type="content" source="media/tutorial-command-line-app/command-line-app-help.png" alt-text="help 命令的输出":::

使项目控制台保持运行状态，以完成本教程中的其余步骤。

#### <a name="upload-models-to-azure-digital-twins"></a>将模型上传到 Azure 数字孪生

设计模型后，需要将其上传到 Azure 数字孪生实例。 这将使用你自己的自定义域词汇来配置 Azure 数字孪生服务实例。 上传模型后，可以创建使用这些模型的孪生实例。

在项目控制台窗口中，运行以下命令以上传更新后的 Room 模型和 Floor 模型，你将在下一节中使用这些模型创建不同类型的孪生 。

```cmd/sh
CreateModels Room Floor
```

输出应指示模型已成功创建。

> [!TIP]
> 如果之前设计了自己的模型，则还可以在此处上传它，方法是将其文件名（可以不包含扩展名）添加到上述命令的 `Room Floor` 列表中。

通过运行命令 `GetModels true` 来验证模型是否已创建。 这将在 Azure 数字孪生实例中查询所有已上传的模型，并打印出其完整信息。 在结果中寻找经过编辑的 Room 模型：

:::image type="content" source="media/tutorial-command-line-app/output-get-models.png" alt-text="GetModels 的结果，其中显示了更新后的 Room 模型":::

#### <a name="errors"></a>错误

该示例应用程序还处理服务中的错误。 

重新运行 `CreateModels` 命令，尝试再次重新上传刚刚上传的其中一个相同模型：

```cmd/sh
CreateModels Room
```

由于无法覆盖模型，此操作现在将返回服务错误：
```cmd/sh
Response 409: Service request failed.
Status: 409 (Conflict)

Content:
{"error":{"code":"ModelAlreadyExists","message":"Could not add model dtmi:example:Room;2 as it already exists. Use Model_List API to view models that already exist. See the Swagger example.(http://aka.ms/ModelListSwSmpl)"}}

Headers:
Strict-Transport-Security: REDACTED
Date: Wed, 20 May 2020 00:53:49 GMT
Content-Length: 223
Content-Type: application/json; charset=utf-8
```

### <a name="create-digital-twins"></a>创建数字孪生

现在，一些模型已上传到 Azure 数字孪生实例，你可以根据模型定义创建[数字孪生](concepts-twins-graph.md)。 数字孪生表示业务环境中的实体，这类似于农场中的传感器、大楼中的房间或汽车上的灯。 

要创建数字孪生，请使用 `CreateDigitalTwin` 命令。 必须引用孪生所基于的模型，并且可以选择定义模型中任何属性的初始值。 在此阶段，无需传递任何关系信息。

在先前更新的 Room 模型和另一个 Floor 模型的基础上，在正在运行的项目控制台中运行此代码，以创建多个孪生模型 。 回想一下，Room 具有三个属性，因此可以为这些属性提供具有初始值的参数。

```cmd/sh
CreateDigitalTwin dtmi:example:Room;2 room0 RoomName string Room0 Temperature double 70 HumidityLevel double 30
CreateDigitalTwin dtmi:example:Room;2 room1 RoomName string Room1 Temperature double 80 HumidityLevel double 60
CreateDigitalTwin dtmi:example:Floor;1 floor0
CreateDigitalTwin dtmi:example:Floor;1 floor1
```

> [!TIP]
> 如果之前上传了自己的模型，请尝试在上述命令的基础上创建自己的 `CreateDigitalTwin` 命令，以添加自己的模型类型的孪生。

这些命令的输出应指示孪生已成功创建。 

:::image type="content" source="media/tutorial-command-line-app/output-create-digital-twin.png" alt-text="CreateDigitalTwin 命令的摘录，其中显示了 floor0、floor1、room0 和 room1":::

还可以通过运行 `Query` 命令来验证是否已创建了孪生。 此命令在 Azure 数字孪生实例中查询其包含的所有数字孪生。 在结果中查找 floor0、floor1、room0 和 room1 孪生   。

#### <a name="modify-a-digital-twin"></a>修改数字孪生

还可以修改已创建的孪生的属性。 尝试运行以下命令，将 room0 的 RoomName 从 Room0 更改为 PresidentialSuite  ：

```cmd/sh
UpdateDigitalTwin room0 add /RoomName string PresidentialSuite
```

输出应指示模型已成功更新。

还可以通过运行以下命令进行验证，以查看 room0 的信息：

```cmd/sh
GetDigitalTwin room0
```

输出应反映更新后的名称。

> [!NOTE]
> 基础 REST API 使用 JSON 修补程序定义对孪生的更新。 命令行应用反映了这种格式，因此可以试验基础 API 的实际预期内容。

### <a name="create-a-graph-by-adding-relationships"></a>通过添加关系来创建关系图

接下来，你可以在这些孪生之间创建某种关系，以将它们连接到[孪生图](concepts-twins-graph.md) 。 孪生图用于表示整个环境。 

要添加关系，请使用 `CreateRelationship` 命令。 指定关系来自的孪生、要添加的关系的类型以及关系要连接到的孪生。 最后，提供关系的名称 (ID)。

运行以下代码，将来自之前创建的每个 Floor 孪生的“包含”关系添加到对应的 Room 孪生 。 请注意，必须在 Floor 模型上定义包含关系，才能实现这一点 。

```cmd/sh
CreateRelationship floor0 contains room0 relationship0
CreateRelationship floor1 contains room1 relationship1
```

这些命令的输出确认关系已成功创建：

:::image type="content" source="media/tutorial-command-line-app/output-create-relationship.png" alt-text="CreateRelationship 命令结果的摘录，其中显示了 relationship0 和 relationship1":::

还可以使用以下任何命令来验证关系，这些命令查询 Azure 数字孪生实例中的关系。
* 要查看从每个楼层剥离的所有关系（从一端查看关系），请使用以下命令
    ```cmd/sh
    GetRelationships floor0
    GetRelationships floor1
    ```
* 要查看每个房间获得的所有关系（从“另一”端查看关系），请使用以下命令
    ```cmd/sh
    GetIncomingRelationships room0
    ```
* 要分别查询这些关系，请使用以下命令 
    ```cmd/sh
    GetRelationship floor0 contains relationship0
    GetRelationship floor1 contains relationship1
    ```

本教程中设置的孪生和关系形成以下概念图：

:::image type="content" source="media/tutorial-command-line-app/sample-graph.png" alt-text="该图显示了 floor0 通过 relationship0 连接到 room0，以及 floor1 通过 relationship1 连接到 room1" border="false":::

### <a name="query-the-twin-graph-to-answer-environment-questions"></a>查询孪生图以回答环境问题

Azure 数字孪生的主要功能是能够轻松有效地[查询](concepts-query-language.md)孪生图，以解答有关环境的问题。 在正在运行的项目控制台中运行以下命令，以了解相关情况。

* **在我的环境中，用 Azure 数字孪生表示的所有实体都有哪些？** （查询所有）

    ```cmd/sh
    Query
    ```

    使用此命令可以一目了然地评估环境，确保在 Azure 数字孪生中以你希望的方式表示所有内容。 此命令的输出结果包含每个数字孪生及其详细信息。 下面是结果摘录：

    :::image type="content" source="media/tutorial-command-line-app/output-query-all.png" alt-text="孪生查询的部分结果，其中显示了 room0 和 floor1":::

    >[!NOTE]
    >不带任何其他参数的命令 `Query` 与 `Query SELECT * FROM DIGITALTWINS` 等效。

* **我的环境中的所有房间都有哪些？** （按模型查询）

    ```cmd/sh
    Query SELECT * FROM DIGITALTWINS T WHERE IS_OF_MODEL(T, 'dtmi:example:Room;2')
    ```

    可以将查询限制为某种类型的孪生，以获取有关所表示内容的更多具体信息。 此命令的结果显示 room0 和 room1，但不显示 floor0 或 floor1（因为它们是楼层，而不是房间）  。
    
    :::image type="content" source="media/tutorial-command-line-app/output-query-model.png" alt-text="模型查询的结果，其中仅显示 room0 和 room1":::

* floor0 上的所有房间都有哪些？ （按关系查询）

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0'
    ```

    可以根据图形中的关系进行查询，以获取有关如何连接孪生或如何将查询限制在特定区域的信息。 floor0 上只有 room0，因此它是结果中唯一的房间 。

    :::image type="content" source="media/tutorial-command-line-app/output-query-relationship.png" alt-text="关系查询的结果，其中显示 room0":::

* **环境中温度高于 75 的所有孪生都有哪些？** （按属性查询）

    ```cmd/sh
    Query SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
    ```

    可以根据属性查询图形，以回答各种问题，包括在环境中查找可能需要注意的离群值。 还支持其他比较运算符（<、>、= 或 !=   ）。 room1 出现在此处的结果中，因为它的温度为 80。

    :::image type="content" source="media/tutorial-command-line-app/output-query-property.png" alt-text="属性查询的结果，其中仅显示 room1":::

* floor0 上温度高于 75 的所有房间都有哪些？ （复合查询）

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0' AND IS_OF_MODEL(room, 'dtmi:example:Room;2') AND room.Temperature > 75
    ```

    还可以像在 SQL 一样使用组合运算符（例如 `AND`、`OR` 和 `NOT`）合并之前的查询。 此查询使用 `AND` 使先前有关孪生温度的查询更具体。 现在，结果仅包括 floor0 上温度高于 75 的房间。在本例中，这些房间均不满足条件。 结果集为空。

    :::image type="content" source="media/tutorial-command-line-app/output-query-compound.png" alt-text="复合查询的结果，其中未显示任何结果":::

## <a name="clean-up-resources"></a>清理资源

本教程中的项目为下一个教程（即[教程：连接端到端解决方案](tutorial-end-to-end.md)）奠定基础。 如果打算继续学习下一个教程，则可以保留此处设置的资源，以便继续使用此 Azure 数字孪生实例和已配置的示例应用。
* 在本例中，可以使用示例应用的 `DeleteAllTwins` 和 `DeleteAllModels` 命令分别清除实例中的孪生和模型。 这将为下一个教程奠定基础。

如果不再需要本教程中创建的资源，请按照以下步骤将其删除。

利用 [Azure Cloud Shell](https://shell.azure.com)，你可以使用 [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) 命令删除资源组中的所有 Azure 资源。 这将删除资源组和 Azure 数字孪生实例。

> [!IMPORTANT]
> 删除资源组的操作不可逆。 资源组以及包含在其中的所有资源将被永久删除。 请确保不会意外删除错误的资源组或资源。 

打开 Azure Cloud Shell 并运行以下命令，以删除资源组及其包含的所有内容。

```azurecli-interactive
az group delete --name <your-resource-group>
```

接下来，使用以下命令删除为客户端应用创建的 Azure Active Directory 应用注册：

```azurecli
az ad app delete --id <your-application-ID>
```

最后，删除下载到本地计算机上的项目示例文件夹。

## <a name="next-steps"></a>后续步骤 

在本教程中，你通过设置实例和客户端应用程序开始使用 Azure 数字孪生，以与实例进行了交互。 你使用客户端应用浏览了 Azure 数字孪生，创建了模型、数字孪生和关系。 你还对解决方案运行了一些查询，以了解 Azure 数字孪生可以解答的与环境有关的问题。

继续学习下一个教程，以结合使用示例命令行应用和其他 Azure 服务来完成数据驱动型端到端方案：

> [!div class="nextstepaction"]
> [教程：连接端到端解决方案](tutorial-end-to-end.md)

或者开始查看概念文档，详细了解以下教程中所用的元素：
* [概念：自定义模型](concepts-models.md)

你也可以先查看操作方法文章，更深入的了解以下教程中的过程：
* [操作说明：使用 Azure 数字孪生 CLI](how-to-use-cli.md)
