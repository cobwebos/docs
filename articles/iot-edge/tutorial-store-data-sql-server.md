---
title: 教程 - 使用 SQL 模块存储数据 - Azure IoT Edge
description: 本教程介绍如何使用 SQL Server 模块将数据存储到本地的 IoT Edge 设备
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: c239c16103dc0c1f847c5d4354aed89a143a28c6
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745509"
---
# <a name="tutorial-store-data-at-the-edge-with-sql-server-databases"></a>教程：使用 SQL Server 数据库存储边缘中的数据

部署 SQL Server 模块以便在运行 Azure IoT Edge 的 Linux 设备上存储数据。

使用 Azure IoT Edge 和 SQL Server 在边缘存储和查询数据。 Azure IoT Edge 有基本的存储功能，可以在设备脱机的情况下缓存消息，在重新建立连接后再转发这些消息。 不过，你可能需要更高级的存储功能，例如在本地查询数据的功能。 IoT Edge 设备可以使用本地数据库来执行更复杂的计算，而不需要与 IoT 中心保持连接。

本文提供有关将 SQL Server 数据库部署到 IoT Edge 设备的说明。 在 IoT Edge 设备上运行的 Azure Functions 可将传入的数据结构化，然后将其发送到数据库。 本文中的步骤也适用于在容器中运行的其他数据库，例如 MySQL 或 PostgreSQL。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
>
> * 使用 Visual Studio Code 创建 Azure Functions
> * 将 SQL 数据库部署到 IoT Edge 设备
> * 使用 Visual Studio Code 生成模块并将其部署到 IoT Edge 设备
> * 查看生成的数据

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

在开始学习本教程之前，应已完成上一篇教程，了解如何设置用于开发 Linux 容器的开发环境：[开发适用于 Linux 设备的 IoT Edge 模块](tutorial-develop-for-linux.md)。 完成该教程后，应已准备好以下必备组件：

* Azure 中的免费或标准层 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。
* 一个[运行 Azure IoT Edge 的 AMD64 Linux 设备](quickstart-linux.md)。
  * ARM 设备（如 Raspberry Pi）无法运行 SQL Server。 如果要在 ARM 设备上使用 SQL，可以注册试用 [Azure SQL Edge](https://azure.microsoft.com/services/sql-edge/) 预览版。
* 一个容器注册表，例如 [Azure 容器注册表](https://docs.microsoft.com/azure/container-registry/)。
* 配置了 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 的 [Visual Studio Code](https://code.visualstudio.com/)。
* 配置为运行 Linux 容器的 [Docker CE](https://docs.docker.com/install/)。

本教程使用 Azure Functions 模块向 SQL Server 发送数据。 要使用 Azure Functions 开发 IoT Edge 模块，请在开发计算机上安装下述额外的必备组件：

* [适用于 Visual Studio Code 的 C#（由 OmniSharp 提供支持）扩展](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)。
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download)。

## <a name="create-a-function-project"></a>创建函数项目

若要将数据发送到数据库中，需要通过一个模块将数据进行适当的结构化并存储在表中。

### <a name="create-a-new-project"></a>创建新项目

以下步骤将介绍如何使用 Visual Studio Code 和 Azure IoT 工具来创建 IoT Edge 函数。

1. 打开 Visual Studio Code。

2. 打开 VS Code 命令面板，方法是选择“视图” > “命令面板”。

3. 在命令面板中，键入并运行 **Azure IoT Edge:New IoT Edge solution** 命令。 在命令面板中提供以下信息，以便创建解决方案：

   | 字段 | 值 |
   | ----- | ----- |
   | 选择文件夹 | 在适用于 VS Code 的开发计算机上选择用于创建解决方案文件的位置。 |
   | 提供解决方案名称 | 输入解决方案的描述性名称（例如 **SqlSolution**），或者接受默认名称。 |
   | 选择模块模板 | 选择“Azure Functions - C#”。 |
   | 提供模块名称 | 将模块命名为 **sqlFunction**。 |
   | 为模块提供 Docker 映像存储库 | 映像存储库包含容器注册表的名称和容器映像的名称。 容器映像是在上一步预先填充的。 将 **localhost:5000** 替换为 Azure 容器注册表中的登录服务器值。 可以在 Azure 门户的容器注册表的“概览”页中检索登录服务器。 <br><br>最终的字符串看起来类似于 \<注册表名称\>.azurecr.io/sqlfunction。 |

   VS Code 窗口将加载你的 IoT Edge 解决方案空间。

### <a name="add-your-registry-credentials"></a>添加注册表凭据

环境文件存储容器注册表的凭据，并将其与 IoT Edge 运行时共享。 此运行时需要这些凭据才能将专用映像拉取到 IoT Edge 设备中。

1. 在 VS Code 资源管理器中，打开 .env 文件。
2. 使用从 Azure 容器注册表复制的 **username** 和 **password** 值更新相关字段。
3. 保存此文件。

### <a name="select-your-target-architecture"></a>选择目标体系结构

目前，Visual Studio Code 可以为 Linux AMD64 和 Linux ARM32v7 设备开发 C 模块。 需要选择面向每个解决方案的体系结构，因为每种体系结构类型的容器的生成和运行方式均不相同。 默认值为 Linux AMD64。

1. 打开命令面板并搜索 **Azure IoT Edge:Set Default Target Platform for Edge Solution**，或选择窗口底部侧栏中的快捷方式图标。

2. 在命令面板中，从选项列表中选择目标体系结构。 在本教程中，我们使用 Ubuntu 虚拟机作为 IoT Edge 设备，因此将保留默认的“amd64”。

### <a name="update-the-module-with-custom-code"></a>使用自定义代码更新模块

1. 在 VS Code 资源管理器中，打开“modules” > “sqlFunction” > “sqlFunction.cs”。

2. 将文件的全部内容替换为以下代码：

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.IO;
   using System.Text;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Client;
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Extensions.EdgeHub;
   using Microsoft.Azure.WebJobs.Host;
   using Microsoft.Extensions.Logging;
   using Newtonsoft.Json;
   using Sql = System.Data.SqlClient;

   namespace Functions.Samples
   {
       public static class sqlFunction
       {
           [FunctionName("sqlFunction")]
           public static async Task FilterMessageAndSendMessage(
               [EdgeHubTrigger("input1")] Message messageReceived,
               [EdgeHub(OutputName = "output1")] IAsyncCollector<Message> output,
               ILogger logger)
           {
               const int temperatureThreshold = 20;
               byte[] messageBytes = messageReceived.GetBytes();
               var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

               if (!string.IsNullOrEmpty(messageString))
               {
                   logger.LogInformation("Info: Received one non-empty message");
                   // Get the body of the message and deserialize it.
                   var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

                   //Store the data in SQL db
                   const string str = "<sql connection string>";
                   using (Sql.SqlConnection conn = new Sql.SqlConnection(str))
                   {
                       conn.Open();
                       var insertMachineTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'machine', " + messageBody.machine.temperature + ");";
                       var insertAmbientTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'ambient', " + messageBody.ambient.temperature + ");";
                       using (Sql.SqlCommand cmd = new Sql.SqlCommand(insertMachineTemperature + "\n" + insertAmbientTemperature, conn))
                       {
                           //Execute the command and log the # rows affected.
                           var rows = await cmd.ExecuteNonQueryAsync();
                           logger.LogInformation($"{rows} rows were updated");
                       }
                   }

                   if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
                   {
                       // Send the message to the output as the temperature value is greater than the threashold.
                       using (var filteredMessage = new Message(messageBytes))
                       {
                            // Copy the properties of the original message into the new Message object.
                            foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                            {filteredMessage.Properties.Add(prop.Key, prop.Value);}
                            // Add a new property to the message to indicate it is an alert.
                            filteredMessage.Properties.Add("MessageType", "Alert");
                            // Send the message.
                            await output.AddAsync(filteredMessage);
                            logger.LogInformation("Info: Received and transferred a message with temperature above the threshold");
                       }
                   }
               }
           }
       }
       //Define the expected schema for the body of incoming messages.
       class MessageBody
       {
           public Machine machine {get; set;}
           public Ambient ambient {get; set;}
           public string timeCreated {get; set;}
       }
       class Machine
       {
           public double temperature {get; set;}
           public double pressure {get; set;}
       }
       class Ambient
       {
           public double temperature {get; set;}
           public int humidity {get; set;}
       }
   }
   ```

3. 在第 35 行中，将 **\<sql connection string\>** 字符串替换为以下字符串。 **Data Source** 属性引用 SQL Server 容器，该容器尚不存在。 在下一部分，我们将使用名称 **SQL** 来创建该容器。

   ```csharp
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

4. 保存 **sqlFunction.cs** 文件。

5. 打开 **sqlFunction.csproj** 文件。

6. 找到包引用所在的组，并添加一个新组来包含 SqlClient。

   ```csproj
   <PackageReference Include="System.Data.SqlClient" Version="4.5.1"/>
   ```

7. 保存 **sqlFunction.csproj** 文件。

## <a name="add-the-sql-server-container"></a>添加 SQL Server 容器

[部署清单](module-composition.md)声明将要由 IoT Edge 运行时安装在 IoT Edge 设备上的具体模块。 在上一部分提供的代码可生成自定义的函数模块，但是，Azure 市场中已生成并提供了 SQL Server 模块。 只需要求 IoT Edge 运行时包括它，然后在设备上配置它即可。

1. 在 Visual Studio Code 中，选择“视图” > “命令面板”打开命令面板。 

2. 在命令面板中，键入并运行 **Azure IoT Edge:** Add IoT Edge module”。 在命令面板中，提供以下信息以添加新模块：

   | 字段 | 值 |
   | ----- | ----- |
   | 选择部署模板文件 | 命令面板会突出显示当前解决方案文件夹中的 deployment.template.json 文件。 选择该文件。  |
   | 选择模块模板 | 选择“Azure 市场中的模块”。 |

3. 在 Azure IoT Edge 模块市场中，搜索并选择“SQL Server 模块”。

4. 将模块名称更改为 **sql**（全小写）。 此名称与 sqlFunction.cs 文件中的连接字符串内声明的容器名称相匹配。

5. 选择“导入”，将模块添加到解决方案。

6. 在解决方案文件夹中，打开 **deployment.template.json** 文件。

7. 找到 **modules** 节。 应会看到三个模块。 模块 *SimulatedTemperatureSensor* 默认已包含在新解决方案中，并提供与其他模块配合使用的测试数据。 模块 *sqlFunction* 是最初使用新代码创建和更新的模块。 最后，模块 *sql* 是从 Azure 市场导入的。

   >[!Tip]
   >在部署清单的环境变量中，为 SQL Server 模块设置了默认密码。 每当在生产环境中创建 SQL Server 容器时，都应该[更改默认的系统管理员密码](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker)。

8. 关闭 **deployment.template.json** 文件。

## <a name="build-your-iot-edge-solution"></a>生成 IoT Edge 解决方案

在前面部分，你创建了一个包含一个模块的解决方案，然后向部署清单模板添加了另一个。 SQL Server 模块由 Microsoft 公开托管，但你需要容器化 Functions 模块中的代码。 在本部分，你将生成解决方案，创建 sqlFunction 模块的容器映像，然后将映像推送到容器注册表。

1. 在 Visual Studio Code 中选择“视图” > “终端”，打开集成终端 。  

1. 在 Visual Studio Code 中登录到你的容器注册表，以便将映像推送到你的注册表。 使用已添加到 .env 文件中的相同 Azure 容器注册表 (ACR) 凭据。 在集成终端中输入以下命令：

    ```csh/sh
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

    此时可能会出现一条安全警告，建议使用 --password-stdin 参数。 虽然本文中未介绍它的用法，但我们建议按照此最佳做法进行操作。 有关详细信息，请参阅 [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) 命令参考。

1. 在 VS Code 资源管理器中右键单击“deployment.template.json”文件，然后选择“生成并推送 IoT Edge 解决方案”。 

告知 Visual Studio Code 生成解决方案时，它首先获取部署模板中的信息，然后在名为 **config** 的新文件夹中生成 deployment.json 文件。然后，它在集成终端运行两个命令，即 `docker build` 和 `docker push`。 build 命令生成你的代码并将模块容器化。 然后，push 命令会将代码推送到在初始化解决方案时指定的容器注册表。

可以验证 sqlFunction 模块是否已成功推送到容器注册表。 在 Azure 门户中，导航到容器注册表。 选择“存储库”并搜索 **sqlFunction**。 另外两个模块 SimulatedTemperatureSensor 和 sql 不会推送到容器注册表，因为其存储库已经在 Microsoft 注册表中。

## <a name="deploy-the-solution-to-a-device"></a>将解决方案部署到设备

可以通过 IoT 中心设置设备上的模块，但是也可以通过 Visual Studio Code 访问 IoT 中心和设备。 在此部分，请先设置对 IoT 中心的访问权限，然后使用 VS Code 将解决方案部署到 IoT Edge 设备。

1. 在 VS Code 资源管理器中，展开“Azure IoT 中心设备”部分。

2. 右键单击要将其作为部署目标的设备，然后选择“为单个设备创建部署”。

3. 在文件资源管理器中导航到解决方案中的 **config** 文件夹，然后选择 **deployment.amd64**。 单击“选择 Edge 部署清单”。

   请不要使用 deployment.template.json 文件作为部署清单。

如果部署成功，则会在 VS Code 输出中输出确认消息。

在 VS Code 的“Azure IoT 中心设备”部分中刷新设备状态。 新模块将会列出，并且在接下来的几分钟内将随着容器的安装和启动开始报告为正在运行。 也可查看设备上的所有模块是否都已启动并运行。 在 IoT Edge 设备上运行以下命令，以便查看模块的状态。

   ```cmd/sh
   iotedge list
   ```

## <a name="create-the-sql-database"></a>创建 SQL 数据库

对设备应用部署清单时，会运行三个模块。 SimulatedTemperatureSensor 模块生成模拟环境数据。 sqlFunction 模块会提取数据并针对数据库设置其格式。 本部分介绍如何设置用于存储温度数据的 SQL 数据库。

在 IoT Edge 设备上运行以下命令。 这些命令连接到在你的设备上运行的 **sql** 模块，并创建数据库和表来存放发送到它的温度数据。

1. 在 IoT Edge 设备上的命令行工具中，连接到你的数据库。

      ```bash
      sudo docker exec -it sql bash
      ```

2. 打开 SQL 命令工具。

      ```bash
      /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
      ```

3. 创建数据库：

      ```sql
      CREATE DATABASE MeasurementsDB
      ON
      (NAME = MeasurementsDB, FILENAME = '/var/opt/mssql/measurementsdb.mdf')
      GO
      ```

4. 定义表。

   ```sql
   CREATE TABLE MeasurementsDB.dbo.TemperatureMeasurements (measurementTime DATETIME2, location NVARCHAR(50), temperature FLOAT)
   GO
   ```

可以自定义 SQL Server docker 文件，以自动设置要在多个 IoT Edge 设备上部署的 SQL Server。 有关详细信息，请参阅 [Microsoft SQL Server 容器演示项目](https://github.com/twright-msft/mssql-node-docker-demo-app)。

## <a name="view-the-local-data"></a>查看本地数据

表创建好以后，sqlFunction 模块就会开始在 IoT Edge 设备的本地 SQL Server 2017 数据库中存储数据。

在 SQL 命令工具中运行以下命令，以便查看格式化的表数据：

   ```sql
   SELECT * FROM MeasurementsDB.dbo.TemperatureMeasurements
   GO
   ```

   ![查看本地数据库的内容](./media/tutorial-store-data-sql-server/view-data.png)

## <a name="clean-up-resources"></a>清理资源

如果打算继续学习下一篇建议的文章，可以保留已创建的资源和配置，以便重复使用。 还可以继续使用相同的 IoT Edge 设备作为测试设备。

否则，可以删除本文中创建的本地配置和 Azure 资源，以避免收费。

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>后续步骤

本教程介绍了如何创建 Azure Functions 模块，其中包含用于筛选 IoT Edge 设备生成的原始数据的代码。 做好生成自己的模块的准备以后，即可详细了解如何[使用用于 Visual Studio Code 的 Azure IoT Edge 开发 Azure Functions](how-to-develop-csharp-function.md)。

如果想尝试 Edge 上的另一种存储方法，请阅读关于如何使用 IoT Edge 上的 Azure Blob 存储。

> [!div class="nextstepaction"]
> [通过 IoT Edge 上的 Azure Blob 存储在边缘存储数据](how-to-store-data-blob.md)
