---
title: 教程：使用 SQL 模块存储数据 - Azure IoT Edge | Microsoft Docs
description: 了解如何使用 SQL Server 模块将数据存储到本地的 IoT Edge 设备
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/01/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 0193d79dec663b089184099c2a4d275c91380c8b
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2018
ms.locfileid: "53163406"
---
# <a name="tutorial-store-data-at-the-edge-with-sql-server-databases"></a>教程：使用 SQL Server 数据库存储边缘中的数据

使用 Azure IoT Edge 和 SQL Server 在边缘存储和查询数据。 Azure IoT Edge 有基本的存储功能，可以在设备脱机的情况下缓存消息，在重新建立连接后再转发这些消息。 不过，你可能需要更高级的存储功能，例如在本地查询数据的功能。 整合本地数据库以后，IoT Edge 设备就可以执行更复杂的计算而不需维持到 IoT 中心的连接。 例如，计算机上的传感器一月一次将数据上传到云，以便报告和改进计算机学习模块。 不过，如果现场技术人员正在计算机上工作，则该技术人员可以在本地访问过去数天的传感器数据。

本文提供有关将 SQL Server 数据库部署到 IoT Edge 设备的说明。 在 IoT Edge 设备上运行的 Azure Functions 可将传入的数据结构化，然后将其发送到数据库。 本文中的步骤也适用于在容器中运行的其他数据库，例如 MySQL 或 PostgreSQL。

本教程介绍如何执行下列操作： 

> [!div class="checklist"]
> * 使用 Visual Studio Code 创建 Azure Functions
> * 将 SQL 数据库部署到 IoT Edge 设备
> * 使用 Visual Studio Code 生成模块并将其部署到 IoT Edge 设备
> * 查看生成的数据

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

Azure IoT Edge 设备：

* 可以按照适用于 [Linux](quickstart-linux.md) 或 [Windows 设备](quickstart.md)的快速入门中的步骤，将开发计算机或虚拟机用作 Edge 设备。 

云资源：

* Azure 中的免费或标准层 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。 

开发资源：

* [Visual Studio Code](https://code.visualstudio.com/)。 
* [适用于 Visual Studio Code 的 C#（由 OmniSharp 提供支持）扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)。 
* [适用于 Visual Studio Code 的 Azure IoT Edge 扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)。 
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download)。 
* [Docker CE](https://docs.docker.com/install/)。 

## <a name="create-a-container-registry"></a>创建容器注册表

本教程将使用适用于 Visual Studio Code 的 Azure IoT Edge 扩展来生成模块并从文件创建**容器映像**。 然后将该映像推送到用于存储和管理映像的**注册表**。 最后，从注册表部署在 IoT Edge 设备上运行的映像。  

可以使用任意兼容 Docker 的注册表来保存容器映像。 两个常见 Docker 注册表服务分别是 [Azure 容器注册表](https://docs.microsoft.com/azure/container-registry/)和 [Docker 中心](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)。 本教程使用 Azure 容器注册表。 

如果还没有容器注册表，请执行以下步骤，以便在 Azure 中创建一个新的：

1. 在 [Azure 门户](https://portal.azure.com)中，选择“创建资源” > “容器” > “容器注册表”。

2. 提供以下值，以便创建容器注册表：

   | 字段 | 值 | 
   | ----- | ----- |
   | 注册表名称 | 提供唯一名称。 |
   | 订阅 | 从下拉列表中选择“订阅”。 |
   | 资源组 | 建议对在 IoT Edge 快速入门和教程中创建的所有测试资源使用同一资源组。 例如，**IoTEdgeResources**。 |
   | 位置 | 选择靠近你的位置。 |
   | 管理员用户 | 设置为“启用”。 |
   | SKU | 选择“基本”。 | 

5. 选择“创建”。

6. 创建容器注册表后，请浏览到其中，然后选择“访问密钥”。 

7. 复制“登录服务器”、“用户名”和“密码”的值。 本教程后面会用到这些值来访问容器注册表。  

## <a name="create-a-function-project"></a>创建函数项目

若要将数据发送到数据库中，需要通过一个模块将数据进行适当的结构化并存储在表中。 

以下步骤将介绍如何使用 Visual Studio Code 和 Azure IoT Edge 扩展来创建 IoT Edge 函数。

1. 打开 Visual Studio Code。

2. 打开 VS Code 命令面板，方法是选择“视图” > “命令面板”。

3. 在命令面板中，键入并运行“Azure IoT Edge: New IoT Edge solution”命令。 在命令面板中提供以下信息，以便创建解决方案： 

   | 字段 | 值 |
   | ----- | ----- |
   | 选择文件夹 | 在适用于 VS Code 的开发计算机上选择用于创建解决方案文件的位置。 |
   | 提供解决方案名称 | 输入解决方案的描述性名称（例如 **SqlSolution**），或者接受默认名称。 |
   | 选择模块模板 | 选择“Azure Functions - C#”。 |
   | 提供模块名称 | 将模块命名为 **sqlFunction**。 |
   | 为模块提供 Docker 映像存储库 | 映像存储库包含容器注册表的名称和容器映像的名称。 容器映像是在上一步预先填充的。 将 **localhost:5000** 替换为 Azure 容器注册表中的登录服务器值。 可以在 Azure 门户的容器注册表的“概览”页中检索登录服务器。 最终的字符串看起来类似于 \<注册表名称\>.azurecr.io/sqlFunction。 |

   VS Code 窗口将加载你的 IoT Edge 解决方案空间。 
   
4. 在你的 IoT Edge 解决方案中，打开 \.env 文件。 

   只要你创建新的 IoT Edge 解决方案，VS Code 就会提示你在 \.env 文件中提供注册表凭据。 此文件会被 git 忽略，IoT Edge 扩展会在以后使用它，以便通过注册表访问 IoT Edge 设备。 

   如果你在上一步骤中未提供容器注册表而是接受了默认的 localhost:5000，则不会具有 \.env 文件。

5. 在 .env 文件中，为 IoT Edge 运行时提供注册表凭据，使之能够访问模块映像。 找到 **CONTAINER_REGISTRY_USERNAME** 和 **CONTAINER_REGISTRY_PASSWORD** 部分并在等号后插入你的凭据： 

   ```env
   CONTAINER_REGISTRY_USERNAME_yourregistry=<username>
   CONTAINER_REGISTRY_PASSWORD_yourregistry=<password>
   ```

6. 保存 .env 文件。

7. 在 VS Code 资源管理器中，打开“modules” > “sqlFunction” > “sqlFunction.cs”。

8. 将此文件的内容替换为以下代码：

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
                       var filteredMessage = new Message(messageBytes);
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

6. 在第 35 行中，将 **\<sql connection string\>** 字符串替换为以下字符串。 **Data Source** 属性引用 SQL Server 容器名称。将在下一部分使用名称“SQL”来创建该名称。 

   ```csharp
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

7. 保存 **sqlFunction.cs** 文件。 

8. 打开 **sqlFunction.csproj** 文件。

9. 找到包引用所在的组，并为 SqlClient include 添加一个新的引用。 

   ```csproj
   <PackageReference Include="System.Data.SqlClient" Version="4.5.1"/>
   ```

10. 保存 **sqlFunction.csproj** 文件。

## <a name="add-a-sql-server-container"></a>添加 SQL Server 容器

[部署清单](module-composition.md)声明将要由 IoT Edge 运行时安装在 IoT Edge 设备上的具体模块。 已在上一部分提供了生成自定义 Functions 模块所需的代码，但 SQL Server 模块已经生成。 只需要求 IoT Edge 运行时包括它，然后在设备上配置它即可。 

1. 在 Visual Studio Code 资源管理器中打开 **deployment.template.json** 文件。 

2. 找到 **modules** 节。 应该会列出两个模块：**tempSensor** 模块，用于生成模拟数据，以及 **sqlFunction** 模块。

3. 如果使用了 Windows 容器，请修改 **sqlFunction.settings.image** 部分。

   ```json
   "image": "${MODULES.sqlFunction.windows-amd64}"
   ```

4. 添加以下代码来声明第三个模块。 在 sqlFunction 部分后添加一个逗号并插入以下内容：

   ```json
   "sql": {
     "version": "1.0",
     "type": "docker",
     "status": "running",
     "restartPolicy": "always",
     "env":{},
     "settings": {
       "image": "",
       "createOptions": ""
     }
   }
   ```

   ![将 SQL Server 模块添加到清单](./media/tutorial-store-data-sql-server/view_json_sql.png)

5. 根据 IoT Edge 设备的 Docker 容器的类型，使用以下代码更新 **sql** 模块参数：
   * Windows 容器：

      ```json
      "env": {
        "ACCEPT_EULA": {"value": "Y"},
        "SA_PASSWORD": {"value": "Strong!Passw0rd"}
      },
      "settings": {
        "image": "microsoft/mssql-server-windows-developer",
        "createOptions": {
          "HostConfig": {
            "Mounts": [{"Target": "C:\\\\mssql","Source": "sqlVolume","Type": "volume"}],
            "PortBindings": {
              "1433/tcp": [{"HostPort": "1401"}]
            }
          }
        }
      }
      ```

   * Linux 容器：

      ```json
      "env": {
        "ACCEPT_EULA": {"value": "Y"},
        "SA_PASSWORD": {"value": "Strong!Passw0rd"}
      },
      "settings": {
        "image": "mcr.microsoft.com/mssql/server:latest",
        "createOptions": {
          "HostConfig": {
            "Mounts": [{"Target": "/var/opt/mssql","Source": "sqlVolume","Type": "volume"}],
            "PortBindings": {
              "1433/tcp": [{"HostPort": "1401"}]
            }
          }
        }
      }
      ```

   >[!Tip]
   >每当在生产环境中创建 SQL Server 容器时，都应该[更改默认的系统管理员密码](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker#change-the-sa-password)。

6. 保存 **deployment.template.json** 文件。

## <a name="build-your-iot-edge-solution"></a>生成 IoT Edge 解决方案

在前面部分，你创建了一个包含一个模块的解决方案，然后向部署清单模板添加了另一个。 现在需生成该解决方案，创建用于模块的容器映像，然后将映像推送到容器注册表。 

1. 在 Visual Studio Code 中登录到你的容器注册表，以便将映像推送到你的注册表。 使用你添加到 .env 文件中的凭据。 在集成终端中输入以下命令：

    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```
    
    系统会提示输入密码。 将密码粘贴到提示符处（为了安全，密码已隐藏），然后按 **Enter**。 

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

2. 在 VS Code 资源管理器中右键单击“deployment.template.json”文件，然后选择“生成并推送 IoT Edge 解决方案”。 

告知 Visual Studio Code 生成解决方案时，它首先获取部署模板中的信息，然后在名为 **config** 的新文件夹中生成 deployment.json 文件。然后，它在集成终端运行两个命令，即 `docker build` 和 `docker push`。 这两个命令会生成代码，将模块容器化，然后将代码推送到在初始化解决方案时指定的容器注册表。 

## <a name="deploy-the-solution-to-a-device"></a>将解决方案部署到设备

可以通过 IoT 中心设置设备上的模块，但是也可以通过 Visual Studio Code 访问 IoT 中心和设备。 在此部分，请先设置对 IoT 中心的访问权限，然后使用 VS Code 将解决方案部署到 IoT Edge 设备。 

1. 在 VS Code 命令面板中，选择“Azure IoT 中心: 选择 IoT 中心”。

2. 根据提示登录到 Azure 帐户。 

3. 在命令面板中选择 Azure 订阅，然后选择 IoT 中心。 

4. 在 VS Code 资源管理器中，展开“Azure IoT 中心设备”部分。 

5. 右键单击要将其作为部署目标的设备，然后选择“为单个设备创建部署”。 

   ![为单个设备创建部署](./media/tutorial-store-data-sql-server/create-deployment.png)

6. 在文件资源管理器中导航到解决方案中的 **config** 文件夹，然后选择 **deployment.amd64**。 单击“选择 Edge 部署清单”。 

如果部署成功，则会在 VS Code 输出中输出确认消息。 

在 VS Code 的“Azure IoT 中心设备”部分中刷新设备状态。 新模块将会列出，并且在接下来的几分钟内将随着容器的安装和启动开始报告为正在运行。 也可查看设备上的所有模块是否都已启动并运行。 在 IoT Edge 设备上运行以下命令，以便查看模块的状态。 

   ```cmd/sh
   iotedge list
   ```

## <a name="create-the-sql-database"></a>创建 SQL 数据库

对设备应用部署清单时，会运行三个模块。 tempSensor 模块生成模拟环境数据。 sqlFunction 模块会提取数据并针对数据库设置其格式。 本部分介绍如何设置用于存储温度数据的 SQL 数据库。 

在 IoT Edge 设备上运行以下命令。 这些命令连接到在你的设备上运行的 **sql** 模块，并创建数据库和表来存放发送到它的温度数据。 

1. 在 IoT Edge 设备上的命令行工具中，连接到你的数据库。 
   * Windows 容器：
   
      ```cmd
      docker exec -it sql cmd
      ```
    
   * Linux 容器： 

      ```bash
      sudo docker exec -it sql bash
      ```

2. 打开 SQL 命令工具。
   * Windows 容器：

      ```cmd
      sqlcmd -S localhost -U SA -P "Strong!Passw0rd"
      ```

   * Linux 容器： 

      ```bash
      /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
      ```

3. 创建数据库： 

   * Windows 容器
      ```sql
      CREATE DATABASE MeasurementsDB
      ON
      (NAME = MeasurementsDB, FILENAME = 'C:\mssql\measurementsdb.mdf')
      GO
      ```

   * Linux 容器
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

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]



## <a name="next-steps"></a>后续步骤

本教程介绍了如何创建 Azure Functions 模块，其中包含用于筛选 IoT Edge 设备生成的原始数据的代码。 做好生成自己的模块的准备以后，即可详细了解如何[使用用于 Visual Studio Code 的 Azure IoT Edge 开发 Azure Functions](how-to-develop-csharp-function.md)。 

继续阅读后续教程，了解如何使用 Azure IoT Edge 通过其他方式将数据转化为边缘业务见解。

> [!div class="nextstepaction"]
> [使用 C# 代码筛选传感器数据](tutorial-csharp-module.md)
