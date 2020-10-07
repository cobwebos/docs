---
title: 使用 ARM 模板发布 IoT 中心、存储帐户、路由消息
description: 使用 ARM 模板发布 IoT 中心、存储帐户、路由消息
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: quickstart
ms.date: 08/24/2020
ms.author: robinsh
ms.custom: mvc, subject-armqs
ms.openlocfilehash: 4112e8aae485e229beb16d21e90280750e1465e1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "89462660"
---
# <a name="quickstart-deploy-an-azure-iot-hub-and-a-storage-account-using-an-arm-template"></a>快速入门：使用 ARM 模板部署 Azure IoT 中心和存储帐户

在此快速入门中，你将使用 Azure 资源管理器模板（ARM 模板）创建 IoT 中心以将消息路由到 Azure 存储，并创建存储帐户以保存消息。 将虚拟 IoT 设备手动添加到中心以提交消息后，可在名为“arm-read-write”的应用程序中配置该连接信息，以将该设备的消息提交到中心。 该中心进行了配置，发送到该中心的消息将自动路由到存储帐户。 在本快速入门教程时，你可以打开该存储帐户并查看发送的消息。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>先决条件

如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板称为 `101-iothub-auto-route-messages`，来自 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/101-iothub-auto-route-messages)。

:::code language="json" source="~/quickstart-templates/101-iothub-auto-route-messages/azuredeploy.json":::

该模板中定义了两个 Azure 资源： 
* [Microsoft.Devices/Iothubs](/azure/templates/microsoft.devices/iothubs)
* [Microsoft.Storage/](/azure/templates/microsoft.storage/allversions)

## <a name="deploy-the-template-and-run-the-sample-app"></a>部署模板并运行示例应用

本部分提供部署模板、创建虚拟设备和运行 arm-read-write 应用程序以发送消息的步骤。

1. 通过部署 ARM 模板创建资源。

    > [!TIP]
    > 选择下面的按钮以开始部署模板。 在运行时，将 arm-read-write 应用程序设置为运行。

    [![部署到 Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-iothub-auto-route-messages%2Fazuredeploy.json)

1. 下载并解压缩 [IoT C# 示例](https://docs.microsoft.com/samples/azure-samples/azure-iot-samples-csharp/azure-iot-samples-for-csharp-net/)。

1. 打开一个命令窗口并转到你将 IoT C# 示例解压缩到的文件夹。 查找包含 arm-read-write.csproj 文件的文件夹。 可以在此命令窗口中创建环境变量。 登录到 [Azure 门户](https://portal.azure.com ]以获取密钥。 选择“资源组”，然后选择用于此快速入门的资源组。

   ![选择资源组](./media/horizontal-arm-route-messages/01-select-resource-group.png)

1. 你可以看到部署 ARM 模板时创建的 IoT 中心和存储帐户。 等待模板完全部署后再继续。 然后选择资源组以查看资源。

   ![查看资源组中的资源](./media/horizontal-arm-route-messages/02-view-resources-in-group.png)

1. 需要“中心名称”。 在资源列表中选择中心。 将“IoT 中心”部分顶部的中心名称复制到 Windows 剪贴板。 
 
   ![复制中心名称](./media/horizontal-arm-route-messages/03-copy-hub-name.png)

    替换此命令中标记的中心名称，并在命令窗口中执行此命令：
   
    ```cmd
    SET IOT_HUB_URI=<hub name goes here>.azure-devices-net;
    ```

   它将如下所示：

   ```cmd
   SET IOT_HUB_URI=ContosoTestHubdlxlud5h.azure-devices-net;
   ```

1. 下一个环境变量是 IoT 设备密钥。 通过从中心的“IoT 中心”菜单中选择“IOT 设备”，将新设备添加到中心。 

   ![选择 IoT 设备](./media/horizontal-arm-route-messages/04-select-iot-devices.png)

1. 在屏幕右侧，选择“+ 新建”以添加新设备。 

   填写新设备名称。 此快速入门使用以“Contoso-Test-Device”开头的名称。 保存设备，然后再次打开该屏幕以检索设备密钥。 （关闭窗格时将生成密钥。）选择主要或辅助密钥并将其复制到 Windows 剪贴板。 在命令窗口中，设置要执行的命令，然后按 Enter。 该命令应类似于此命令，但在其中粘贴了设备密钥：

   ```cmd
   SET IOT_DEVICE_KEY=<device-key-goes-here>
   ```

1. 最后一个环境变量是“设备 ID”。 在命令窗口中，设置并执行命令。 
   
   ```cms
   SET IOT_DEVICE_ID=<device-id-goes-here> 
   ```

   如以下示例所示：

   ```cmd
   SET IOT_DEVICE_ID=Contoso-Test-Device
   ```

1. 若要查看你定义的环境变量，请在命令行中键入 SET 并按 Enter，然后查找以“IoT”开头的变量 。

   ![查看环境变量](./media/horizontal-arm-route-messages/06-environment-variables.png)

现在已经设置好环境变量，请从同一命令窗口运行应用程序。 因为使用的是同一个窗口，所以在运行应用程序时，可以在内存中访问这些变量。

1. 若要运行应用程序，请在命令窗口中键入以下命令并按 Enter。

    `dotnet run arm-read-write`

   应用程序在将每条消息发送到 IoT 中心时在控制台上生成并显示消息。 该中心在 ARM 模板中配置为具有自动路由。 包含文本“level = storage”的消息将自动路由到存储帐户。 让应用运行 10 到 15 分钟，然后按 Enter 一次或两次，直到它停止运行。

## <a name="review-deployed-resources"></a>查看已部署的资源

1. 登录到 [Azure 门户](https://portal.azure.com)，选择资源组，然后选择存储帐户。

1. 向下钻取到存储帐户，直到找到文件。

   ![查看存储帐户文件](./media/horizontal-arm-route-messages/07-see-storage.png)

1. 选择其中一个文件并选择“下载”，将文件下载到你稍后可以找到的位置。 它会有一个数值名称，比如 47。 在末尾添加“.txt”，然后双击文件打开它。

1. 打开文件时，每一行对应一条不同的消息；每条消息的正文都进行了加密。 必须这样才能对消息正文执行查询。

   ![查看已发送的消息](./media/horizontal-arm-route-messages/08-messages.png)

   > [!NOTE]
   > 这些消息以 UTF-32 和 base64 编码。 如果读回消息，必须由 base64 和 utf-32 对其进行解码，以便以 ASCII 格式进行读取。 如果你感兴趣，可以使用路由教程中的 ReadOneRowFromFile 方法从其中某个消息文件对其进行读取，并将其解码为 ASCII。 ReadOneRowFromFile 位于你为此快速入门解压缩的 IoT C# 示例存储库中。 从该文件夹顶层开始的路径为：./iot-hub/Tutorials/Routing/SimulatedDevice/Program.cs。 将布尔值 `readTheFile` 设置为 true，并将路径硬编码到磁盘上的文件中，它将打开并转换文件中的第一行。

已部署 ARM 模板来创建 IoT 中心和存储帐户，并运行程序来向中心发送消息。 然后消息会自动存储在存储帐户中，以便查看。

## <a name="clean-up-resources"></a>清理资源

若要删除此快速入门期间添加的资源，请登录到 [Azure 门户](https://portal.azure.com)。 选择“资源组”，然后查找用于此快速入门的资源组。 选择资源组，然后选择“删除”。 它将删除组中的所有资源。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：创建和部署你的第一个 ARM 模板](/azure/azure-resource-manager/templates/template-tutorial-create-first-template)
