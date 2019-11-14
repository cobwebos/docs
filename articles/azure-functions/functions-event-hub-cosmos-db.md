---
title: 教程：将 Java 函数与 Azure Cosmos DB 和事件中心配合使用
description: 本教程介绍如何使用事件中心的事件，通过以 Java 编写的函数在 Azure Cosmos DB 中进行更新。
author: KarlErickson
manager: barbkess
ms.service: azure-functions
ms.devlang: java
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: karler
ms.openlocfilehash: 172dda0a79620431d41a5be7e872b6b1507a6c81
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2019
ms.locfileid: "73608300"
---
# <a name="tutorial-create-a-function-in-java-with-an-event-hub-trigger-and-an-azure-cosmos-db-output-binding"></a>教程：使用事件中心触发器和 Azure Cosmos DB 输出绑定在 Java 中创建函数

本教程介绍如何使用 Azure Functions 来创建一个 Java 函数，该函数分析持续的温度和压力数据流。 表示传感器读数的事件中心事件会触发该函数。 该函数处理事件数据，然后将状态条目添加到 Azure Cosmos DB。

在本教程中，你将：

> [!div class="checklist"]
> * 使用 Azure CLI 创建和配置 Azure 资源。
> * 创建和测试与这些资源交互的 Java 函数。
> * 将函数部署到 Azure，并使用 Application Insights 进行监视。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

若要完成本教程，必须安装以下各项：

* [Java 开发人员工具包](https://aka.ms/azure-jdks)版本 8
* [Apache Maven](https://maven.apache.org) 版本 3.0 或更高版本
* [Azure CLI](/cli/azure/install-azure-cli)（如果不想使用 Cloud Shell）
* [Azure Functions Core Tools](https://www.npmjs.com/package/azure-functions-core-tools) 版本 2.6.666 或更高版本

> [!IMPORTANT]
> `JAVA_HOME` 环境变量必须设置为 JDK 的安装位置才能完成本教程。

如果你更愿意直接使用本教程的代码，请参阅 [java-functions-eventhub-cosmosdb](https://github.com/Azure-Samples/java-functions-eventhub-cosmosdb) 示例存储库。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-azure-resources"></a>创建 Azure 资源

在本教程中，需要以下资源：

* 资源组，用于包含其他资源
* 事件中心命名空间、事件中心和授权规则
* Cosmos DB 帐户、数据库和集合
* 函数应用和用于托管它的存储帐户

以下部分说明了如何使用 Azure CLI 创建这些资源。

### <a name="log-in-to-azure"></a>登录 Azure

如果你使用的不是 Cloud Shell，则需要在本地使用 Azure CLI 来访问你的帐户。 使用 Bash 提示符下的 `az login` 命令启动基于浏览器的登录体验。 如果有权访问多个 Azure 订阅，则使用 `az account set --subscription` 后跟订阅 ID 来设置默认值。

### <a name="set-environment-variables"></a>设置环境变量。

接下来，为要创建的资源的名称和位置创建一些环境变量。 使用以下命令，将 `<value>` 占位符替换为你选择的值。 值应当符合 [Azure 资源的命名规则和限制](/azure/architecture/best-practices/resource-naming)。 对于 `LOCATION` 变量，使用 `az functionapp list-consumption-locations` 命令生成的值之一。

```azurecli-interactive
RESOURCE_GROUP=<value>
EVENT_HUB_NAMESPACE=<value>
EVENT_HUB_NAME=<value>
EVENT_HUB_AUTHORIZATION_RULE=<value>
COSMOS_DB_ACCOUNT=<value>
STORAGE_ACCOUNT=<value>
FUNCTION_APP=<value>
LOCATION=<value>
```

本教程的其余部分使用这些变量。 请注意，这些变量仅在当前 Azure CLI 或 Cloud Shell 会话的持续时间内保持不变。 如果使用不同的本地终端窗口或 Cloud Shell 会话超时，将需要再次运行这些命令。

### <a name="create-a-resource-group"></a>创建资源组

Azure 使用资源组收集帐户中的所有相关资源。 这样一来，你可以将它们作为一个单元进行查看，并在完成后使用单一命令将其删除。

使用以下命令来创建资源组：

```azurecli-interactive
az group create \
    --name $RESOURCE_GROUP \
    --location $LOCATION
```

### <a name="create-an-event-hub"></a>创建事件中心

接下来，使用以下命令创建 Azure 事件中心命名空间、事件中心和授权规则：

```azurecli-interactive
az eventhubs namespace create \
    --resource-group $RESOURCE_GROUP \
    --name $EVENT_HUB_NAMESPACE
az eventhubs eventhub create \
    --resource-group $RESOURCE_GROUP \
    --name $EVENT_HUB_NAME \
    --namespace-name $EVENT_HUB_NAMESPACE \
    --message-retention 1
az eventhubs eventhub authorization-rule create \
    --resource-group $RESOURCE_GROUP \
    --name $EVENT_HUB_AUTHORIZATION_RULE \
    --eventhub-name $EVENT_HUB_NAME \
    --namespace-name $EVENT_HUB_NAMESPACE \
    --rights Listen Send
```

事件中心命名空间包含实际的事件中心及其授权规则。 授权规则可让你的函数向中心发送消息并侦听相应的事件。 一个函数发送表示遥测数据的消息。 另一个函数侦听事件，分析事件数据，并将结果存储在 Azure Cosmos DB 中。

### <a name="create-an-azure-cosmos-db"></a>创建 Azure Cosmos DB

接下来，使用以下命令创建 Azure Cosmos DB 帐户、数据库和集合：

```azurecli-interactive
az cosmosdb create \
    --resource-group $RESOURCE_GROUP \
    --name $COSMOS_DB_ACCOUNT
az cosmosdb database create \
    --resource-group-name $RESOURCE_GROUP \
    --name $COSMOS_DB_ACCOUNT \
    --db-name TelemetryDb
az cosmosdb collection create \
    --resource-group-name $RESOURCE_GROUP \
    --name $COSMOS_DB_ACCOUNT \
    --collection-name TelemetryInfo \
    --db-name TelemetryDb \
    --partition-key-path '/temperatureStatus'
```

`partition-key-path` 值基于每个项的 `temperatureStatus` 值对你的数据进行分区。 使用分区键，Cosmos DB 可以通过将你的数据划分为可独立访问的不同子集来提高性能。

### <a name="create-a-storage-account-and-function-app"></a>创建存储帐户和函数应用

接下来，创建 Azure Functions 所需的 Azure 存储帐户，然后创建函数应用。 使用以下命令：

```azurecli-interactive
az storage account create \
    --resource-group $RESOURCE_GROUP \
    --name $STORAGE_ACCOUNT \
    --sku Standard_LRS
az functionapp create \
    --resource-group $RESOURCE_GROUP \
    --name $FUNCTION_APP \
    --storage-account $STORAGE_ACCOUNT \
    --consumption-plan-location $LOCATION \
    --runtime java
```

当 `az functionapp create` 命令创建函数应用时，它还会创建一个具有相同名称的 Application Insights 资源。 函数应用自动使用将其连接到 Application Insights 的名为 `APPINSIGHTS_INSTRUMENTATIONKEY` 的设置来进行配置。 将函数部署到 Azure 后，可以查看应用遥测，如本教程后面所述。

## <a name="configure-your-function-app"></a>配置函数应用

函数应用需要访问其他资源才能正常工作。 以下部分说明如何配置函数应用，使其可以在本地计算机上运行。

### <a name="retrieve-resource-connection-strings"></a>检索资源连接字符串

使用以下命令检索存储、事件中心和 Cosmos DB 连接字符串，并将其保存在环境变量中：

```azurecli-interactive
AZURE_WEB_JOBS_STORAGE=$( \
    az storage account show-connection-string \
        --name $STORAGE_ACCOUNT \
        --query connectionString \
        --output tsv)
echo $AZURE_WEB_JOBS_STORAGE
EVENT_HUB_CONNECTION_STRING=$( \
    az eventhubs eventhub authorization-rule keys list \
        --resource-group $RESOURCE_GROUP \
        --name $EVENT_HUB_AUTHORIZATION_RULE \
        --eventhub-name $EVENT_HUB_NAME \
        --namespace-name $EVENT_HUB_NAMESPACE \
        --query primaryConnectionString \
        --output tsv)
echo $EVENT_HUB_CONNECTION_STRING
COSMOS_DB_CONNECTION_STRING=$( \
    az cosmosdb keys list \
        --resource-group $RESOURCE_GROUP \
        --name $COSMOS_DB_ACCOUNT \
        --type connection-strings \
        --query connectionStrings[0].connectionString \
        --output tsv)
echo $COSMOS_DB_CONNECTION_STRING
```

这些变量设置为从 Azure CLI 命令检索到的值。 每个命令都使用 JMESPath 查询从返回的 JSON 有效负载中提取连接字符串。 还会使用 `echo` 显示连接字符串，以便你可以确认它们已成功检索。

### <a name="update-your-function-app-settings"></a>更新函数应用设置

接下来，使用以下命令将连接字符串值传输到 Azure Functions 帐户中的应用设置：

```azurecli-interactive
az functionapp config appsettings set \
    --resource-group $RESOURCE_GROUP \
    --name $FUNCTION_APP \
    --settings \
        AzureWebJobsStorage=$AZURE_WEB_JOBS_STORAGE \
        EventHubConnectionString=$EVENT_HUB_CONNECTION_STRING \
        CosmosDBConnectionString=$COSMOS_DB_CONNECTION_STRING
```

现已创建 Azure 资源并将其配置为一起正常工作。

## <a name="create-and-test-your-functions"></a>创建和测试函数

接下来，你将在本地计算机上创建一个项目，添加 Java 代码并对其进行测试。 你将使用与适用于 Azure Functions 的 Maven 插件和 Azure Functions Core Tools 配合工作的命令。 你的函数将在本地运行，但将使用你创建的基于云的资源。 在本地运行函数后，可以使用 Maven 将其部署到云中，并监视数据和分析累积。

如果使用 Cloud Shell 创建资源，则不会在本地连接到 Azure。 在这种情况下，请使用 `az login` 命令启动基于浏览器的登录过程。 然后，如有必要，使用 `az account set --subscription` 后跟订阅 ID 来设置默认订阅。 最后，运行以下命令，在本地计算机上重新创建一些环境变量。 将 `<value>` 占位符替换为之前使用的相同值。

```bash
RESOURCE_GROUP=<value>
FUNCTION_APP=<value>
```

### <a name="create-a-local-functions-project"></a>创建本地函数项目

使用以下 Maven 命令创建函数项目并添加所需的依赖项。

```bash
mvn archetype:generate --batch-mode \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype \
    -DappName=$FUNCTION_APP \
    -DresourceGroup=$RESOURCE_GROUP \
    -DgroupId=com.example \
    -DartifactId=telemetry-functions
```

此命令在 `telemetry-functions` 文件夹内生成多个文件：

* 用于 Maven 的 `pom.xml` 文件
* 用于保存本地测试的应用设置的 `local.settings.json` 文件
* 启用 Azure Functions 扩展捆绑的 `host.json` 文件，数据分析函数中 Cosmos DB 输出绑定需要该文件
* 包含默认函数实现的 `Function.java` 文件
* 本教程不需要的一些测试文件

若要避免编译错误，需要删除测试文件。 使用以下命令导航到新的项目文件夹并删除测试文件夹：

```bash
cd telemetry-functions
rm -r src/test
```

### <a name="retrieve-your-function-app-settings-for-local-use"></a>检索函数应用设置以供本地使用

对于本地测试，函数项目将需要在本教程前面部分中添加到函数应用的连接字符串。 使用以下 Azure Functions Core Tools 命令检索云中存储的所有函数应用设置并将其添加到 `local.settings.json` 文件：

```bash
func azure functionapp fetch-app-settings $FUNCTION_APP
```

### <a name="add-java-code"></a>添加 Java 代码

接下来，打开 `Function.java` 文件并将其内容替换为以下代码。

```java
package com.example;

import com.example.TelemetryItem.status;
import com.microsoft.azure.functions.annotation.Cardinality;
import com.microsoft.azure.functions.annotation.CosmosDBOutput;
import com.microsoft.azure.functions.annotation.EventHubOutput;
import com.microsoft.azure.functions.annotation.EventHubTrigger;
import com.microsoft.azure.functions.annotation.FunctionName;
import com.microsoft.azure.functions.annotation.TimerTrigger;
import com.microsoft.azure.functions.ExecutionContext;
import com.microsoft.azure.functions.OutputBinding;

public class Function {

    @FunctionName("generateSensorData")
    @EventHubOutput(
        name = "event",
        eventHubName = "", // blank because the value is included in the connection string
        connection = "EventHubConnectionString")
    public TelemetryItem generateSensorData(
        @TimerTrigger(
            name = "timerInfo",
            schedule = "*/10 * * * * *") // every 10 seconds
            String timerInfo,
        final ExecutionContext context) {

        context.getLogger().info("Java Timer trigger function executed at: "
            + java.time.LocalDateTime.now());
        double temperature = Math.random() * 100;
        double pressure = Math.random() * 50;
        return new TelemetryItem(temperature, pressure);
    }

    @FunctionName("processSensorData")
    public void processSensorData(
        @EventHubTrigger(
            name = "msg",
            eventHubName = "", // blank because the value is included in the connection string
            cardinality = Cardinality.ONE,
            connection = "EventHubConnectionString")
            TelemetryItem item,
        @CosmosDBOutput(
            name = "databaseOutput",
            databaseName = "TelemetryDb",
            collectionName = "TelemetryInfo",
            connectionStringSetting = "CosmosDBConnectionString")
            OutputBinding<TelemetryItem> document,
        final ExecutionContext context) {

        context.getLogger().info("Event hub message received: " + item.toString());

        if (item.getPressure() > 30) {
            item.setNormalPressure(false);
        } else {
            item.setNormalPressure(true);
        }

        if (item.getTemperature() < 40) {
            item.setTemperatureStatus(status.COOL);
        } else if (item.getTemperature() > 90) {
            item.setTemperatureStatus(status.HOT);
        } else {
            item.setTemperatureStatus(status.WARM);
        }

        document.setValue(item);
    }
}
```

如你所见，此文件包含两个函数：`generateSensorData` 和 `processSensorData`。 `generateSensorData` 函数模拟向事件中心发送温度和压力读数的传感器。 计时器触发器每 10 秒运行一次该函数，事件中心输出绑定将返回值发送到事件中心。

当事件中心收到消息时，它会生成一个事件。 `processSensorData` 函数在收到事件时运行。 然后，它会处理事件数据，并使用 Azure Cosmos DB 输出绑定将结果发送到 Azure Cosmos DB。

这些函数所使用的数据是使用名为 `TelemetryItem` 的你需要实现的类存储的。 在 `Function.java` 的同一位置创建名为 `TelemetryItem.java` 的新文件，并添加以下代码：

```java
package com.example;

public class TelemetryItem {

    private String id;
    private double temperature;
    private double pressure;
    private boolean isNormalPressure;
    private status temperatureStatus;
    static enum status {
        COOL,
        WARM,
        HOT
    }

    public TelemetryItem(double temperature, double pressure) {
        this.temperature = temperature;
        this.pressure = pressure;
    }

    public String getId() {
        return id;
    }

    public double getTemperature() {
        return temperature;
    }

    public double getPressure() {
        return pressure;
    }

    @Override
    public String toString() {
        return "TelemetryItem={id=" + id + ",temperature="
            + temperature + ",pressure=" + pressure + "}";
    }

    public boolean isNormalPressure() {
        return isNormalPressure;
    }

    public void setNormalPressure(boolean isNormal) {
        this.isNormalPressure = isNormal;
    }

    public status getTemperatureStatus() {
        return temperatureStatus;
    }

    public void setTemperatureStatus(status temperatureStatus) {
        this.temperatureStatus = temperatureStatus;
    }
}
```

### <a name="run-locally"></a>在本地运行

现在，你可以在本地生成并运行函数，然后查看 Azure Cosmos DB 中显示的数据。

执行以下 Maven 命令来生成并运行应用：

```bash
mvn clean package
mvn azure-functions:run
```

在某些生成和启动消息后，每次运行函数时，将看到类似于以下示例的输出：

```output
[10/22/19 4:01:30 AM] Executing 'Functions.generateSensorData' (Reason='Timer fired at 2019-10-21T21:01:30.0016769-07:00', Id=c1927c7f-4f70-4a78-83eb-bc077d838410)
[10/22/19 4:01:30 AM] Java Timer trigger function executed at: 2019-10-21T21:01:30.015
[10/22/19 4:01:30 AM] Function "generateSensorData" (Id: c1927c7f-4f70-4a78-83eb-bc077d838410) invoked by Java Worker
[10/22/19 4:01:30 AM] Executed 'Functions.generateSensorData' (Succeeded, Id=c1927c7f-4f70-4a78-83eb-bc077d838410)
[10/22/19 4:01:30 AM] Executing 'Functions.processSensorData' (Reason='', Id=f4c3b4d7-9576-45d0-9c6e-85646bb52122)
[10/22/19 4:01:30 AM] Event hub message received: TelemetryItem={id=null,temperature=32.728691307527015,pressure=10.122563042388165}
[10/22/19 4:01:30 AM] Function "processSensorData" (Id: f4c3b4d7-9576-45d0-9c6e-85646bb52122) invoked by Java Worker
[10/22/19 4:01:38 AM] Executed 'Functions.processSensorData' (Succeeded, Id=1cf0382b-0c98-4cc8-9240-ee2a2f71800d)
```

你可以转到 [Azure 门户](https://portal.azure.com)，导航到 Azure Cosmos DB 帐户。 选择“数据资源管理器”  ，展开“TelemetryInfo”  ，然后选择“项”  在数据到达时查看数据。

![Cosmos DB 数据资源管理器](media/functions-event-hub-cosmos-db/data-explorer.png)

## <a name="deploy-to-azure-and-view-app-telemetry"></a>部署到 Azure 并查看应用遥测

最后，你可以将应用部署到 Azure，并验证它是否继续以本地方式运行。

使用以下命令将项目部署到 Azure：

```bash
mvn azure-functions:deploy
```

现在，函数在 Azure 中运行，并继续在 Azure Cosmos DB 中累积数据。 可以在 Azure 门户中查看已部署的函数应用，并通过连接的 Application Insights 资源查看应用遥测，如以下屏幕截图所示：

**实时指标流：**

![Application Insights 实时指标流](media/functions-event-hub-cosmos-db/application-insights-live-metrics-stream.png)

**性能：**

![Application Insights 性能边栏选项卡](media/functions-event-hub-cosmos-db/application-insights-performance.png)

## <a name="clean-up-resources"></a>清理资源

使用完本教程中创建的 Azure 资源后，可以用以下命令将其删除：

```azurecli-interactive
az group delete --name $RESOURCE_GROUP
```

## <a name="next-steps"></a>后续步骤

在本教程中，学习了如何创建一个 Azure 函数来处理事件中心事件和更新 Cosmos DB。 有关详细信息，请参阅 [Azure Functions Java 开发人员指南](/azure/azure-functions/functions-reference-java)。 有关所使用的注释的信息，请参阅 [com.microsoft.azure.functions.annotation](/java/api/com.microsoft.azure.functions.annotation) 引用。

本教程使用环境变量和应用程序设置来存储机密（如连接字符串）。 有关将这些机密存储在 Azure Key Vault 中的信息，请参阅[将 Key Vault 引用用于应用服务和 Azure Functions](/azure/app-service/app-service-key-vault-references)。

接下来，了解如何使用 Azure Pipelines CI/CD 进行自动部署：

> [!div class="nextstepaction"]
> [生成 Java 并将其部署到 Azure Functions](/azure/devops/pipelines/ecosystems/java-function)
