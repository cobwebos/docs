---
title: Azure IoT Edge SQL 模块 | Microsoft Docs
description: 使用 Microsoft SQL 模块存储边缘中的数据，并使用 Azure Functions 设置数据的格式。
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: ebertrams
ms.date: 02/21/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: c755d171b34d59d2746a965ab3511a0df00c98db
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="store-data-at-the-edge-with-sql-server-databases"></a>使用 SQL Server 数据库存储边缘中的数据

使用 Azure IoT Edge 设备存储边缘中生成的数据。 Internet 连接不稳定的设备可以维护自身的数据库，仅在已建立连接时才向云报告更改。 经过适当的编程、只将关键数据发送到云的设备可以保存剩余的数据用于日常批量上传。 进入云后，结构化数据可与其他 Azure 服务共享，例如，用于构建机器学习模型。 

本文提供有关将 SQL Server 数据库部署到 IoT Edge 设备的说明。 在 IoT Edge 设备上运行的 Azure Functions 可将传入的数据结构化，然后将其发送到数据库。 本文中的步骤也适用于在容器中运行的其他数据库，例如 MySQL 或 PostgreSQL。 

## <a name="prerequisites"></a>先决条件 

在遵照本文中的说明之前，请先完成以下教程：
* 在 [Windows](tutorial-simulate-device-windows.md) 或 [Linux](tutorial-simulate-device-linux.md) 中在模拟设备上部署 Azure IoT Edge
* [将 Azure 函数部署为 IoT Edge 模块](tutorial-deploy-function.md)

无需阅读以下文章就能成功完成本教程，但这些文章提供有用的上下文：
* [使用 Docker 运行 SQL Server 2017 容器映像](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker)
* [使用 Visual Studio Code 开发 Azure Functions 并将其部署到 Azure IoT Edge](how-to-vscode-develop-azure-function.md)

完成所需的教程后，计算机上应已准备好全部所需的必备组件： 
* 有效的 Azure IoT 中心。
* 至少有 2-GB RAM 和 2-GB 硬盘空间的 IoT Edge 设备。
* [Visual Studio Code](https://code.visualstudio.com/)。 
* [适用于 Visual Studio Code 的 Azure IoT Edge 扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)。 
* [适用于 Visual Studio Code 的 C# 扩展（由 OmniSharp 提供支持）](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)。 
* [Docker](https://docs.docker.com/engine/installation/)
* [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd)。 
* [Python 2.7](https://www.python.org/downloads/)
* [IoT Edge 控制脚本](https://pypi.python.org/pypi/azure-iot-edge-runtime-ctl)
* AzureIoTEdgeFunction 模板 (`dotnet new -i Microsoft.Azure.IoT.Edge.Function`)
* 包含至少一个 IoT Edge 设备的活动 IoT 中心。

x64 处理器体系结构中的 Windows 和 Linux 容器适用于本教程。 SQL Server 不支持 ARM 处理器。

## <a name="deploy-a-sql-server-container"></a>部署 SQL Server 容器

在此部分，我们将一个 MS-SQL 数据库添加到模拟的 IoT Edge 设备。 使用 SQL Server 2017 docker 容器映像，该映像以 [Windows](https://hub.docker.com/r/microsoft/mssql-server-windows-developer/) 容器和 [Linux](https://hub.docker.com/r/microsoft/mssql-server-linux/) 容器的形式提供。 

### <a name="deploy-sql-server-2017"></a>部署 SQL Server 2017

本部分中的代码默认创建包含免费 SQL Server 2017 Developer Edition 的容器。 若要运行生产版，请参阅[运行生产容器映像](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker#production)了解详细信息。 

在步骤 3 中，请将 create 选项添加到 SQL Server 容器，在建立环境变量和持久性存储时，此选项非常重要。 配置的[环境变量](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-environment-variables)接受最终用户许可协议并定义密码。 使用 [mounts](https://docs.docker.com/storage/volumes/) 配置[持久性存储](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker#persist)。 Mounts 创建附有 *sqlvolume* 卷容器的 SQL Server 2017 容器，这样，即使该了该容器，也能持久保存数据。 

1. 在 Visual Studio Code 中打开 `deployment.json` 文件。 
2. 将该文件的 **modules** 节替换为以下代码： 

   ```json
   "modules": {
          "filterFunction": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "<docker registry address>/filterfunction:latest",
              "createOptions": "{}"
            }
          },
          "tempSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
              "createOptions": "{}"
            }
          },
          "sql": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "",
              "createOptions": ""
             }
          }
        }
   ```

3. 将 `<docker registry address>` 替换为在已完成本教程[将 Azure Functions 作为 IoT Edge 模块进行部署 - 预览版](https://docs.microsoft.com/en-us/azure/iot-edge/tutorial-deploy-function)中填写的地址

   >[!NOTE]
   >容器注册表地址与从注册表复制的登录服务器相同。 它应采用 `<your container registry name>.azurecr.io` 格式

4. 根据正在运行的操作系统，使用以下代码更新 SQL 模块的设置： 

   * Windows:

      ```json
      "image": "microsoft/mssql-server-windows-developer",
      "createOptions": "{\"Env\": [\"ACCEPT_EULA=Y\",\"MSSQL_SA_PASSWORD=Strong!Passw0rd\"],\"HostConfig\": {\"Mounts\": [{\"Target\": \"C:\\\\mssql\",\"Source\": \"sqlVolume\",\"Type\": \"volume\"}],\"PortBindings\": {\"1433/tcp\": [{\"HostPort\": \"1401\"}]}}"
      ```

   * Linux：

      ```json
      "image": "microsoft/mssql-server-linux:2017-latest",
      "createOptions": "{\"Env\": [\"ACCEPT_EULA=Y\",\"MSSQL_SA_PASSWORD=Strong!Passw0rd\"],\"HostConfig\": {\"Mounts\": [{\"Target\": \"/var/opt/mssql\",\"Source\": \"sqlVolume\",\"Type\": \"volume\"}],\"PortBindings\": {\"1433/tcp\": [{\"HostPort\": \"1401\"}]}}}"
      ```

5. 保存文件。 
6. 在 VS Code 命令面板中，选择 **Edge: Create deployment for Edge device**。 
7. 选择 IoT Edge 设备 ID。
8. 选择更新的 `deployment.json` 文件。 在输出窗口中，可以看到部署的相应输出。 
9. 若要启动 Edge 运行时，请在命令面板中选择 **Edge: Start Edge**。

>[!TIP]
>每当在生产环境中创建 SQL Server 容器时，都应该[更改默认的系统管理员密码](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker#change-the-sa-password)。

## <a name="create-the-sql-database"></a>创建 SQL 数据库

本部分引导你设置 SQL 数据库，用于存储从连接到 IoT Edge 设备的传感器接收的温度数据。 如果使用模拟设备，则此数据来自 *tempSensor* 容器。 

在命令行工具中连接到数据库： 

* Windows 容器
   ```cmd
   docker exec -it sql cmd
   ```

* Linux 容器
   ```bash
   docker exec -it sql bash
   ```

打开 SQL 命令工具： 

* Windows 容器
   ```cmd
   sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

* Linux 容器
   ```bash
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

创建数据库： 

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

定义表： 

   ```sql
   CREATE TABLE MeasurementsDB.dbo.TemperatureMeasurements (measurementTime DATETIME2, location NVARCHAR(50), temperature FLOAT)
   GO
   ```

可以自定义 SQL Server docker 文件，以自动设置要在多个 IoT Edge 设备上部署的 SQL Server。 有关详细信息，请参阅 [Microsoft SQL Server 容器演示项目](https://github.com/twright-msft/mssql-node-docker-demo-app)。

## <a name="understand-the-sql-connection"></a>了解 SQL 连接

在其他教程中，我们使用路由来让容器通信，同时保持相互隔离。 不过，在使用 SQL Server 数据库时，必须建立更密切的关系。 

IoT Edge 在启动时，会自动构建网桥 (Linux) 或 NAT (Windows) 网络。 该网络名为 **azure-iot-edge**。 如果需要调试此连接，可以在命令行中查看其属性：

* Windows

   ```cmd
   docker network inspect azure-iot-edge
   ```

* Linux

   ```bash
   sudo docker network inspect azure-iot-edge
   ```

IoT Edge 还能通过 docker 解析容器名称的 DNS，因此你无需按 IP 地址引用 SQL Server 数据库。 

例如，下一部分将使用以下连接字符串： 

   ```csharp
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

可以看到，该连接字符串按名称 **sql** 引用了容器。 如果更改了模块名称，请同时更新此连接字符串。 否则请转到下一部分。 

## <a name="update-your-azure-function"></a>更新 Azure 函数
若要将数据发送到数据库，请更新上一教程中创建的 Azure 函数 FilterFunction。 请更改此文件，使其结构化传感器收到的数据，然后将其存储在 SQL 表中。 

1. 在 Visual Studio Code 中打开 FilterFunction 文件夹。 
2. 将 run.csx 文件替换为以下代码，其中包括上一部分中的 SQL 连接字符串： 

   ```csharp
   #r "Microsoft.Azure.Devices.Client"
   #r "Newtonsoft.Json"
   #r "System.Data.SqlClient"

   using System.IO;
   using Microsoft.Azure.Devices.Client;
   using Newtonsoft.Json;
   using Sql = System.Data.SqlClient;
   using System.Threading.Tasks;

   // Filter messages based on the temperature value in the body of the message and the temperature threshold value.
   public static async Task Run(Message messageReceived, IAsyncCollector<Message> output, TraceWriter log)
   {
       const int temperatureThreshold = 25;
       byte[] messageBytes = messageReceived.GetBytes();
       var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

       if (!string.IsNullOrEmpty(messageString))
       {
           // Get the body of the message and deserialize it
           var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

           //Store the data in SQL db
           const string str = "Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;";
           using (Sql.SqlConnection conn = new Sql.SqlConnection(str))
           {
           conn.Open();
           var insertMachineTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'machine', " + messageBody.machine.temperature + ");";
           var insertAmbientTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'ambient', " + messageBody.ambient.temperature + ");"; 
               using (Sql.SqlCommand cmd = new Sql.SqlCommand(insertMachineTemperature + "\n" + insertAmbientTemperature, conn))
               {
               //Execute the command and log the # rows affected.
               var rows = await cmd.ExecuteNonQueryAsync();
               log.Info($"{rows} rows were updated");
               }
           }

           if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
           {
               // Send the message to the output as the temperature value is greater than the threshold
               var filteredMessage = new Message(messageBytes);
               // Copy the properties of the original message into the new Message object
               foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
               {
                   filteredMessage.Properties.Add(prop.Key, prop.Value);
               }
               // Add a new property to the message to indicate it is an alert
               filteredMessage.Properties.Add("MessageType", "Alert");
               // Send the message        
               await output.AddAsync(filteredMessage);
               log.Info("Received and transferred a message with temperature above the threshold");
           }
       }
   }

   //Define the expected schema for the body of incoming messages
   class MessageBody
   {
       public Machine machine {get;set;}
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
   ```

## <a name="update-your-container-image"></a>更新容器映像

若要应用所做的更改，请更新并发布容器映像，然后重启 IoT Edge。

1. 在 Visual Studio Code 中展开“Docker”文件夹。 
2. 根据所用的平台，展开“windows-nano”或“linux-x64”文件夹。 
3. 右键单击“Dockerfile”文件，然后选择“生成 IoT Edge 模块 Docker 映像”。
4. 导航到“FilterFunction”项目文件夹，然后单击“选择文件夹作为 EXE_DIR”。
5. 在 VS Code 窗口顶部弹出的文本框中，输入映像名称。 例如，`<your container registry address>/filterfunction:latest`。 若要部署到本地注册表，名称应为 `<localhost:5000/filterfunction:latest>`。
6. 在 VS Code 命令面板中，选择 **Edge: Push IoT Edge module Docker image**。 
7. 在弹出的文本框中，输入相同的映像名称。 
8. 在 VS Code 命令面板中，选择 **Edge: Restart Edge**。

## <a name="view-the-local-data"></a>查看本地数据

容器重启后，从温度传感器收到的数据将存储在 IoT Edge 设备上的本地 SQL Server 2017 数据库中。 

在命令行工具中连接到数据库： 

* Windows 容器
   ```cmd
   docker exec -it sql cmd
   ```

* Linux 容器
   ```bash
   docker exec -it sql bash
   ```

打开 SQL 命令工具： 

* Windows 容器
   ```cmd
   sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

* Linux 容器
   ```bash
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

查看数据： 

   ```sql
   SELECT * FROM MeasurementsDB.dbo.TemperatureMeasurements
   GO
   ```

## <a name="next-steps"></a>后续步骤

* 了解如何[在 Docker 上配置 SQL Server 2017 容器映像](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker)。

* 访问 [mssql-docker GitHub 存储库](https://github.com/Microsoft/mssql-docker)了解资源、反馈和已知问题。
