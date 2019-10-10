---
title: 教程：通过 Azure 数字孪生对空间进行监视 | Microsoft Docs
description: 了解如何使用 Azure 数字孪生根据本教程中的步骤来预配空间资源并监视工作条件。
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 09/20/2019
ms.openlocfilehash: 74e3c46b2b1427c27923ed91846755797b8da690
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949084"
---
# <a name="tutorial-provision-your-building-and-monitor-working-conditions-with-azure-digital-twins-preview"></a>教程：使用 Azure 数字孪生预览版预配大楼并监视工作条件

本教程演示如何使用 Azure 数字孪生预览版来监视空间是否达到理想的温度条件和舒适度。 [配置示例大楼](tutorial-facilities-setup.md)以后，即可根据本教程中的步骤预配大楼并针对传感器数据运行自定义函数。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 定义要监视的条件。
> * 创建用户定义函数 (UDF)。
> * 模拟传感器数据。
> * 获取用户定义函数的结果。

## <a name="prerequisites"></a>先决条件

本教程假定你已[完成 Azure 数字孪生设置](tutorial-facilities-setup.md)。 在继续操作之前，请确保已具备以下条件：

- 一个 [Azure 帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 一个正在运行的数字孪生实例。 
- 在工作计算机上下载并解压缩的[数字孪生 C# 示例](https://github.com/Azure-Samples/digital-twins-samples-csharp)。 
- 在用于生成和运行示例的开发计算机上安装的 [.NET Core SDK 2.1.403 或更高版本](https://www.microsoft.com/net/download)。 请运行 `dotnet --version` 以验证是否安装了正确的版本。 
- [Visual Studio Code](https://code.visualstudio.com/)，用于探索示例代码。 

> [!TIP]
> 如果要预配新实例，请使用唯一的数字孪生实例名称。

## <a name="define-conditions-to-monitor"></a>定义要监视的条件

可以定义一组需要在设备或传感器数据中监视的具体条件，称为“匹配程序”。  然后可以定义名为“用户定义函数”的函数。  用户定义函数用于在匹配程序所指定的条件符合的时候对来自空间和设备的数据执行自定义逻辑。 有关详细信息，请阅读[数据处理和用户定义的函数](concepts-user-defined-functions.md)。 

在 **occupancy-quickstart** 示例项目中，通过 Visual Studio Code 打开 **src\actions\provisionSample.yaml** 文件。 请注意以类型 **matchers** 开头的节。 此类型下的每个条目使用指定的**名称**创建一个匹配程序。 该匹配程序用于监视类型为 **dataTypeValue** 的传感器。 请注意它与名为“专注室 A1”的空间的关系，该专注室有一个“设备”节点，   ，节点中包含一些传感器。 若要预配一个匹配程序，用于跟踪这其中的一个传感器，则请确保其 **dataTypeValue** 与该传感器的 **dataType** 匹配。 

在现有匹配程序下面添加以下匹配程序。 确保各个键对齐且空格不被制表符替换。 这些行也存在 *provisionSample.yaml* 文件中，属于已注释掉的行。 可以取消注释这些行，方法是删除每一行前面的 `#` 字符。

```yaml
      - name: Matcher Temperature
        dataTypeValue: Temperature
```

此匹配程序会跟踪已在[第一个教程](tutorial-facilities-setup.md)中添加的 `SAMPLE_SENSOR_TEMPERATURE` 传感器。 

## <a name="create-a-user-defined-function"></a>创建用户定义函数

可以通过用户定义的函数来自定义传感器数据的处理。 这些函数是自定义 JavaScript 代码，在符合匹配程序所述的具体条件时，可以在 Azure 数字孪生实例中运行。 可以为每个需要监视的传感器创建匹配程序和用户定义的函数。 有关详细信息，请阅读[数据处理和用户定义的函数](concepts-user-defined-functions.md)。 

在示例性的 provisionSample.yaml  文件中，查找以 **userdefinedfunctions** 类型开头的节。 该节预配具有给定**名称**的用户定义函数。 该 UDF 作用于 **matcherNames** 下的匹配程序的列表。 请注意如何以**脚本**形式提供自己的适用于 UDF 的 JavaScript 文件。

另请注意名为 **roleassignments** 的节。 它向用户定义的函数分配“空间管理员”角色。 该函数可以通过此角色访问来自任何已预配空间的事件。 

1. 配置 UDF，使之包含温度匹配程序，方法是在 *provisionSample.yaml* 文件的 `matcherNames` 节点中添加或取消注释以下行：

    ```yaml
            - Matcher Temperature
    ```

1. 在编辑器中打开 **src\actions\userDefinedFunctions\availability.js** 文件。 这是在 provisionSample.yaml  的 **script** 元素中引用的文件。 此文件中的用户定义函数查找符合以下情况的条件：在房间中检测不到移动，以及二氧化碳水平低于 1,000 ppm。 

   请修改 JavaScript 文件，以便监视温度和其他条件。 另请添加以下代码行，查找符合以下情况的条件：在房间中检测不到移动，二氧化碳水平低于 1,000 ppm，且温度低于 78 华氏度。

   > [!NOTE]
   > 此节修改 *src\actions\userDefinedFunctions\availability.js* 文件，因此你可以详细了解如何编写用户定义的函数。 但是，可以选择在设置中直接使用 [src\actions\userDefinedFunctions\availabilityForTutorial.js](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availabilityForTutorial.js) 文件。 该文件包含本教程所需的所有更改。 如果改用此文件，请确保使用对 [src\actions\provisionSample.yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml) 中的 **script** 键来说正确的文件名。

    a. 在文件顶部 `// Add your sensor type here` 注释下方，添加下述适用于温度的行：

    ```JavaScript
        var temperatureType = "Temperature";
        var temperatureThreshold = 78;
    ```

    b. 在注释 `// Add your sensor variable here` 下，在用于定义 `var motionSensor` 的语句后面添加以下行：

     ```JavaScript
        var temperatureSensor = otherSensors.find(function(element) {
            return element.DataType === temperatureType;
        });
    ```

    c. 在注释 `// Add your sensor latest value here` 下，在用于定义 `var carbonDioxideValue` 的语句后面添加以下行：

    ```JavaScript
        var temperatureValue = getFloatValue(temperatureSensor.Value().Value);
    ```

    d. 在注释 `// Modify this line to monitor your sensor value` 下删除以下代码行：

     ```JavaScript
        if(carbonDioxideValue === null || motionValue === null) {
            sendNotification(telemetry.SensorId, "Sensor", "Error: Carbon dioxide or motion are null, returning");
            return;
        }
    ```

    将它们替换为以下行：

    ```JavaScript
        if(carbonDioxideValue === null || motionValue === null || temperatureValue === null){
            sendNotification(telemetry.SensorId, "Sensor", "Error: Carbon dioxide, motion, or temperature are null, returning");
            return;
        }
    ```

    e. 在注释 `// Modify these lines as per your sensor` 下删除以下代码行：

    ```JavaScript
        var availableFresh = "Room is available and air is fresh";
        var noAvailableOrFresh = "Room is not available or air quality is poor";
    ```

    将它们替换为以下行：

    ```JavaScript
        var alert = "Room with fresh air and comfortable temperature is available.";
        var noAlert = "Either room is occupied, or working conditions are not right.";
    ```

    f. 在注释 `// Modify this code block for your sensor` 后面删除以下 *if-else* 代码块：

    ```JavaScript
        // If carbonDioxide less than threshold and no presence in the room => log, notify and set parent space computed value
        if(carbonDioxideValue < carbonDioxideThreshold && !presence) {
            log(`${availableFresh}. Carbon Dioxide: ${carbonDioxideValue}. Presence: ${presence}.`);
            setSpaceValue(parentSpace.Id, spaceAvailFresh, availableFresh);
        }
        else {
            log(`${noAvailableOrFresh}. Carbon Dioxide: ${carbonDioxideValue}. Presence: ${presence}.`);
            setSpaceValue(parentSpace.Id, spaceAvailFresh, noAvailableOrFresh);

            // Set up custom notification for poor air quality
            parentSpace.Notify(JSON.stringify(noAvailableOrFresh));
        }
    ```

    将它替换为以下 *if-else* 块：

    ```JavaScript
        // If sensor values are within range and room is available
        if(carbonDioxideValue < carbonDioxideThreshold && temperatureValue < temperatureThreshold && !presence) {
            log(`${alert}. Carbon Dioxide: ${carbonDioxideValue}. Temperature: ${temperatureValue}. Presence: ${presence}.`);

            // log, notify and set parent space computed value
            setSpaceValue(parentSpace.Id, spaceAvailFresh, alert);

            // Set up notification for this alert
            parentSpace.Notify(JSON.stringify(alert));
        }
        else {
            log(`${noAlert}. Carbon Dioxide: ${carbonDioxideValue}. Temperature: ${temperatureValue}. Presence: ${presence}.`);

            // log, notify and set parent space computed value
            setSpaceValue(parentSpace.Id, spaceAvailFresh, noAlert);
        }
    ```

    修改后的 UDF 会查找这样一个条件：房间变得可用且其二氧化碳和温度处于可以容忍的范围内。 在此条件得到满足的情况下，它会使用语句 `parentSpace.Notify(JSON.stringify(alert));` 生成一个通知。 不管此条件是否得到满足，它都会设置受监视空间的值，并提供相应的消息。

    g. 保存文件。

1. 打开命令窗口，转到 **occupancy-quickstart\src** 文件夹。 运行以下命令，预配空间智能图和用户定义的函数：

    ```cmd/sh
    dotnet run ProvisionSample
    ```

   > [!IMPORTANT]
   > 为了防止对数字孪生管理 API 进行未经授权的访问，**occupancy-quickstart** 应用程序要求使用 Azure 帐户凭据登录。 它会将凭据保存短暂的时间，因此不需要每次运行它时都登录。 第一次运行此程序时，以及在此之后保存的凭据过期时，应用程序会将你定向到一个登录页并提供一个可在该页上输入的特定于会话的代码。 请根据提示通过 Azure 帐户登录。

1. 帐户进行身份验证以后，应用程序就会开始根据 provisionSample.yaml  中的配置创建一个示例空间图。 等待预配完成。 将需要几分钟时间。 此后，请观察命令窗口中的消息，注意空间图是如何创建的。 注意应用程序如何在根节点或 `Venue` 处创建 IoT 中心。

1. 将 `Devices` 节下 `ConnectionString` 的值从命令窗口中的输出复制到剪贴板。 在下一部分，需要此值来模拟设备连接。

    [![预配示例](./media/tutorial-facilities-udf/run-provision-sample.png)](./media/tutorial-facilities-udf/run-provision-sample.png#lightbox)

> [!TIP]
> 如果在预配过程中出现类似于“由于线程退出或应用程序请求，I/O 操作已中止”的错误消息，请尝试再次运行该命令。 如果 HTTP 客户端因网络问题而超时，则可能出现这种情况。

## <a name="simulate-sensor-data"></a>模拟传感器数据

在此部分，需使用示例中名为 *device-connectivity* 的项目。 需模拟检测移动、温度和二氧化碳所需的传感器数据。 此项目为传感器生成随机值，然后通过设备连接字符串将其发送到 IoT 中心。

1. 在单独的命令窗口中，转到 Azure 数字孪生示例，然后导航到 **device-connectivity** 文件夹。

1. 运行以下命令，确保项目的依赖项正确：

    ```cmd/sh
    dotnet restore
    ```

1. 在编辑器中打开 [appsettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/device-connectivity/appsettings.json) 文件，编辑以下值：

   a. **DeviceConnectionString**：在上一部分的输出窗口中分配 `ConnectionString` 的值。 完整地复制引号中的这个字符串，以便模拟器能够正确连接 IoT 中心。

   b. **Sensors** 数组中的 **HardwareId**：由于是模拟已预配到 Azure 数字孪生实例的传感器中的事件，因此此文件中的硬件 ID 和传感器的名称应该与 provisionSample.yaml  文件的 `sensors` 节点匹配。

      为温度传感器添加新条目。 appsettings.json  中的 **Sensors** 节点应该如下所示：

      ```JSON
      "Sensors": [{
        "DataType": "Motion",
        "HardwareId": "SAMPLE_SENSOR_MOTION"
      },{
        "DataType": "CarbonDioxide",
        "HardwareId": "SAMPLE_SENSOR_CARBONDIOXIDE"
      },{
        "DataType": "Temperature",
        "HardwareId": "SAMPLE_SENSOR_TEMPERATURE"
      }]
      ```

1. 运行以下命令，开始针对温度、移动和二氧化碳模拟设备事件：

    ```cmd/sh
    dotnet run
    ```

   > [!NOTE]
   > 由于模拟示例不直接与数字孪生实例通信，因此不需要你进行身份验证。

## <a name="get-results-of-the-user-defined-function"></a>获取用户定义函数的结果

每次实例收到设备和传感器数据时，用户定义的函数就会运行。 此部分查询 Azure 数字孪生实例，以便获取用户定义函数的结果。 可以以近实时的方式查看何时房间可用、空气清新且温度合适。 

1. 打开用于预配示例的命令窗口或新的命令窗口，再次转到示例的 **occupancy-quickstart\src** 文件夹。

1. 出现提示时，运行以下命令并登录：

    ```cmd/sh
    dotnet run GetAvailableAndFreshSpaces
    ```

输出窗口会显示用户定义的函数如何运行，以及如何截获来自设备模拟的事件。 

   [![UDF 的输出](./media/tutorial-facilities-udf/udf-running.png)](./media/tutorial-facilities-udf/udf-running.png#lightbox)

如果满足受监视的条件，用户定义的函数会将空间的值设置为我们[此前](#create-a-user-defined-function)看到的的相关消息。 该消息由 `GetAvailableAndFreshSpaces` 函数输出到控制台。

## <a name="clean-up-resources"></a>清理资源

如果不希望继续探索 Azure 数字孪生，可以删除本教程中创建的资源：

1. 在 [Azure 门户](https://portal.azure.com)的左菜单中依次选择“所有资源”、  数字孪生资源组、“删除”。 

    > [!TIP]
    > 如果在删除数字孪生实例时遇到麻烦，请使用已推出的包含修补程序的服务更新。 请重新尝试删除实例。

2. 可以根据需要删除工作计算机上的示例应用程序。

## <a name="next-steps"></a>后续步骤

预配空间并创建用于触发自定义通知的框架以后，即可继续阅读下述教程之一：

> [!div class="nextstepaction"]
> [教程：使用逻辑应用从 Azure 数字孪生空间接收通知](tutorial-facilities-events.md)

> [!div class="nextstepaction"]
> [教程：使用时序见解直观显示和分析 Azure 数字孪生空间中的事件](tutorial-facilities-analyze.md)
