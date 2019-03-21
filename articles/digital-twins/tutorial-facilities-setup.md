---
title: 教程：部署 Azure 数字孪生 | Microsoft Docs
description: 了解如何使用本教程中的步骤部署 Azure 数字孪生的实例并配置空间资源。
services: digital-twins
author: dsk-2015
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 12/17/2018
ms.author: dkshir
ms.openlocfilehash: 096df62305af91ac85ce9ddbcff5b0160aaa4e8a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2019
ms.locfileid: "57537450"
---
# <a name="tutorial-deploy-azure-digital-twins-and-configure-a-spatial-graph"></a>教程：部署 Azure 数字孪生并配置空间图

可以使用 Azure 数字孪生服务将人员、地点和设备规整到一个一致的空间系统中。 本系列教程演示如何使用 Azure 数字孪生来检测温度和空气质量达到理想化条件时的房间占用情况。 

这些教程演示如何通过 .NET 控制台应用程序生成一个涉及一栋办公大楼的方案， 该楼有多个楼层，每个楼层有多个房间。 房间包含设备，通过附加的传感器来检测移动、环境温度和空气质量。 

本系列教程会介绍如何通过 Azure 数字孪生服务将大楼中的物理区域和实体复制为数字对象。 可以使用另一控制台应用程序模拟设备事件。 然后，教程会介绍如何以近实时的方式监视来自这些物理区域和实体的事件。 

办公室主任可以根据此信息帮助在此大楼工作的员工预订理想化条件的会议室。 办公设备管理员可以根据你的设置了解房间的使用趋势，并监视进行维护所需的工作条件。

本系列的第一个教程介绍如何执行以下操作：

> [!div class="checklist"]
> * 部署数字孪生。
> * 向应用授权。
> * 修改数字孪生示例应用。
> * 预配大楼。

这些教程使用和修改[介绍如何查找可用房间的快速入门](quickstart-view-occupancy-dotnet.md)所使用的示例，目的是更详细且更深入地探讨相关概念。

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 如果没有 Azure 帐户，请创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- .NET Core SDK。 在这些教程中使用的 Azure 数字孪生示例以 C# 编写。 请确保在用于生成和运行示例的开发计算机上安装 [.NET Core SDK 2.1.403 或更高版本](https://www.microsoft.com/net/download)。 在命令窗口中运行 `dotnet --version` 即可检查计算机上是否已安装正确的版本。

- [Visual Studio Code](https://code.visualstudio.com/)，用于探索示例代码。 

<a id="deploy"></a>

## <a name="deploy-digital-twins"></a>部署数字孪生

请使用此部分的步骤创建 Azure 数字孪生服务的新实例。 每个订阅只能创建一个实例。 如果已经有一个实例在运行，请跳至下一部分。 

[!INCLUDE [create-digital-twins-portal](../../includes/digital-twins-create-portal.md)]

<a id="permissions"></a>

## <a name="grant-permissions-to-your-app"></a>向应用授权

数字孪生使用 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) 来控制对服务的[读/写访问权限](../active-directory/develop/v1-permissions-and-consent.md)。 任何需要与数字孪生实例连接的应用程序都必须注册到 Azure AD。 本部分的步骤介绍如何注册示例应用。

如果已经进行了应用注册，可以将其重用于示例。 但是，请浏览本部分，确保应用注册已正确配置。

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]

## <a name="configure-the-digital-twins-sample"></a>配置数字孪生示例

本部分详细介绍一个可以与[数字孪生 REST API](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index) 通信的 Azure 数字孪生应用程序。 

### <a name="download-the-sample"></a>下载示例

如果已下载适用于[介绍如何查找可用房间的快速入门](quickstart-view-occupancy-dotnet.md)的示例，可以跳过这些步骤。

1. 下载[数字孪生 .NET 示例](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip)。
2. 解压缩计算机上的 zip 文件夹的内容。

### <a name="explore-the-sample"></a>探索示例

在解压缩后的示例文件夹中，通过 Visual Studio Code 打开 **digital-twins-samples-csharp\digital-twins-samples.code-workspace** 文件。 该文件包含两个项目：

* 可以通过预配示例 **occupancy-quickstart** 配置和预配[空间智能图](concepts-objectmodel-spatialgraph.md#graph)。 该图是你的物理空间以及其中的资源的数字化图像。 它使用[对象模型](concepts-objectmodel-spatialgraph.md#model)来定义智能大楼的对象。 如需数字孪生对象和 REST API 的完整列表，请访问[此 REST API 文档](https://docs.westcentralus.azuresmartspaces.net/management/swagger)或者为[你的实例](#deploy)创建的管理 API URL。

   若要通过示例来了解它与数字孪生实例的通信方式，可以从 **src\actions** 文件夹开始。 该文件夹中的文件实现的命令是将要在这些教程中使用的命令：
    - **provisionSample.cs** 文件说明如何预配空间图。
    - **getSpaces.cs** 文件获取已预配空间的相关信息。
    - **getAvailableAndFreshSpaces.cs** 文件获取名为“用户定义函数”的自定义函数的结果。
    - **createEndpoints.cs** 文件创建与其他服务交互的终结点。

* 模拟示例 **device-connectivity** 模拟传感器数据并将其发送到为数字孪生实例预配的 IoT 中心。 将在[预配空间图后的下一教程](tutorial-facilities-udf.md#simulate)中使用此示例。 用于配置此示例的传感器和设备标识符应该与用于预配图形的相同。

### <a name="configure-the-provisioning-sample"></a>配置预配示例

1. 打开一个命令窗口，转到已下载的示例。 运行以下命令：

    ```cmd/sh
    cd occupancy-quickstart/src
    ```

1. 通过运行以下命令将依赖项还原到示例项目：

    ```cmd/sh
    dotnet restore
    ```

1. 在 Visual Studio Code 中打开 **occupancy-quickstart** 项目中的 [appSettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/appSettings.json) 文件。 更新以下值：
   * **ClientId**：输入 Azure AD 应用注册的应用程序 ID。 已在[设置应用权限](#permissions)的部分记下此 ID。
   * **租户**：输入 [Azure AD 租户](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)的目录 ID。 也已在[设置应用权限](#permissions)的部分记下此 ID。
   * **BaseUrl**：输入数字孪生实例的 URL。 若要获取此 URL，请将其中的占位符替换为适用于你的实例的值：`https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`。 也可通过修改[部署部分](#deploy)的管理 API URL 来获取此 URL。 将 **swagger/** 替换为 **api/v1.0/**。

1. 查看数字孪生功能的列表，这些功能可以通过示例来探索。 运行以下命令：

    ```cmd/sh
    dotnet run
    ```

<a id="provision-spaces"></a>

## <a name="understand-the-provisioning-process"></a>了解预配过程

此部分介绍示例如何预配大楼的空间图。

在 Visual Studio Code 中浏览到 **occupancy-quickstart\src\actions** 文件夹，打开 **provisionSample.cs** 文件。 注意以下函数：

```csharp
public static async Task<IEnumerable<ProvisionResults.Space>> ProvisionSample(HttpClient httpClient, ILogger logger)
{
    IEnumerable<SpaceDescription> spaceCreateDescriptions;
    using (var r = new StreamReader("actions/provisionSample.yaml"))
    {
        spaceCreateDescriptions = await GetProvisionSampleTopology(r);
    }

    var results = await CreateSpaces(httpClient, logger, spaceCreateDescriptions, Guid.Empty);

    Console.WriteLine($"Completed Provisioning: {JsonConvert.SerializeObject(results, Formatting.Indented)}");

    return results;
}

```

此函数使用同一文件夹中的 [provisionSample.yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml)。 打开此文件，注意办公大楼的层次结构：地点、楼层、区域和房间。 任何一个这样的物理空间都可能包含设备和传感器。 每个条目都有预定义的 `type`&mdash;例如楼层和房间。

示例 **yaml** 文件显示一个使用 `Default` 数字孪生对象模型的空间图。 该模型提供适用于大多数类型的通用名称。 通用名称对于大楼来说已足够。 例如，使用“温度”作为 SensorDataType，使用“地图”作为 SpaceBlobType。 例如，子类型为 FocusRoom、ConferenceRoom 等的房间为空间类型。 

如果必须为另一类型的地点（例如工厂）创建空间图，则可能需要另一对象模型。 若要找出可供使用的具体模型，可在适用于预配示例的命令行中运行 `dotnet run GetOntologies` 命令。 

若要详细了解空间图和对象模型，请阅读[了解数字孪生对象模型和空间智能图](concepts-objectmodel-spatialgraph.md)。

### <a name="modify-the-sample-spatial-graph"></a>修改示例空间图

**provisionSample.yaml** 文件包含以下节点：

- **resources**：`resources` 节点创建一个 Azure IoT 中心资源，用于与设置中的设备通信。 在图形根节点处的 IoT 中心可以与图形中的所有设备和传感器通信。  

- **spaces**：在数字孪生对象模型中，`spaces` 表示物理位置。 每个空间都有一个 `Type`（例如区域、地点或客户），以及一个友好 `Name`。 一些空间可能属于另一些空间，形成一个层次结构。 provisionSample.yaml 文件有一个虚构大楼的空间图。 注意 `Venue` 中有 `Floor` 类型，楼层中有 `Area`，区域中有 `Room` 节点，这样就形成了空间的逻辑嵌套。 

- **devices**：空间可能包含 `devices`，这是物理或虚拟实体，用于管理大量传感器。 例如，设备可能是用户的手机、Raspberry Pi 传感器 Pod 或网关。 在示例的虚构大楼中，注意名为“专注室”的房间包含一个 **Raspberry Pi 3 A1** 设备。 每个设备节点均由已硬编码到示例中的唯一 `hardwareId` 进行标识。 若要针对实际生产来配置此示例，请将这些项替换为你设置中的值。  

- **sensors**：一个设备可能包含多个 `sensors`， 用于检测和记录物理变化，例如温度、移动和电池剩余电量的变化。 每个传感器节点都通过此处硬编码的 `hardwareId` 进行唯一标识。 对于实际的应用程序，请将这些项替换为设置中传感器的唯一标识符。 provisionSample.yaml 文件有两个传感器，用于记录移动和二氧化碳情况。 请添加另一传感器来记录温度，方法是将以下行添加到二氧化碳传感器对应的行下面。 请注意，这些都是在 provisionSample.yaml 中作为注释掉的行提供的。 可以取消注释这些行，方法是删除每一行前面的 `#` 字符。 

    ```yaml
            - dataType: Temperature
              hardwareId: SAMPLE_SENSOR_TEMPERATURE
    ```
    > [!NOTE]
    > 请确保 `dataType` 和 `hardwareId` 键与此代码片段上面的语句对齐。 另请确保编辑器不将空格替换为制表符。 

保存并关闭 provisionSample.yaml 文件。 在下一教程中，将向此文件添加更多的信息，然后预配 Azure 数字孪生示例大楼。

> [!TIP]
> 可以使用 [Azure 数字孪生图形查看器](https://github.com/Azure/azure-digital-twins-graph-viewer)查看和修改空间图。

## <a name="clean-up-resources"></a>清理资源

如果不希望继续探索 Azure 数字孪生，可以删除本教程中创建的资源：

1. 在 [Azure 门户](https://portal.azure.com)的左菜单中依次选择“所有资源”、数字孪生资源组、“删除”。

    > [!TIP]
    > 如果在删除数字孪生实例时遇到麻烦，请使用已推出的包含修补程序的服务更新。 请重新尝试删除实例。

1. 可以根据需要删除工作计算机上的示例应用程序。

## <a name="next-steps"></a>后续步骤

若要了解如何实现一个用于监视示例大楼中条件的自定义逻辑，请继续学习此系列中的下一教程： 
> [!div class="nextstepaction"]
> [教程：预配大楼并监视工作条件](tutorial-facilities-udf.md)
