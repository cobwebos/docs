---
title: 如何通过 PHP 使用 Azure 存储表服务或 Azure Cosmos DB 表 API | Microsoft Docs
description: 了解如何通过 PHP 使用表服务 API 来创建和删除表以及插入、删除和查询表。
services: cosmos-db
documentationcenter: php
author: mimig1
manager: jhubbard
editor: tysonn
ms.assetid: 1e57f371-6208-4753-b2a0-05db4aede8e3
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: article
ms.date: 02/22/2018
ms.author: mimig
ms.openlocfilehash: 69fa1f8f9717d1ea4ca5081a45317cff8d752809
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-use-azure-storage-table-service-or-cosmos-db-table-api-from-php"></a>如何通过 PHP 使用 Azure 存储表服务或 Cosmos DB 表 API
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

## <a name="overview"></a>概述
本指南介绍了如何使用 Azure 存储表服务和 Azure Cosmos DB 表 API 执行常见方案。 示例是采用 PHP 编写的，并使用了 [Azure 存储表 PHP 客户端库][download]。 涉及的方案包括**创建和删除表**以及**在表中插入、删除和查询实体**。 有关 Azure 表服务的详细信息，请参阅[后续步骤](#next-steps)部分。

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

## <a name="create-an-azure-service-account"></a>创建 Azure 服务帐户

可以通过 Azure 表存储或 Azure Cosmos DB 表 API 来处理表。 可以阅读[表产品](table-introduction.md#table-offerings)来详细了解服务之间的差别。 需要为所要使用的服务创建一个帐户。 

### <a name="create-an-azure-storage-account"></a>创建 Azure 存储帐户

创建第一个存储帐户的最简单方法是使用 [Azure 门户](https://portal.azure.com)。 若要了解更多信息，请参阅 [创建存储帐户](../storage/common/storage-create-storage-account.md#create-a-storage-account)。

也可以使用 [Azure PowerShell](../storage/common/storage-powershell-guide-full.md) 或 [Azure CLI](../storage/common/storage-azure-cli.md) 创建存储帐户。

如果暂时不想创建存储帐户，也可以使用 Azure 存储模拟器在本地环境中运行和测试代码。 有关详细信息，请参阅[使用 Azure 存储模拟器进行开发和测试](../storage/common/storage-use-emulator.md)。

### <a name="create-an-azure-cosmos-db-account"></a>创建 Azure Cosmos DB 帐户

有关创建 Azure Cosmos DB 帐户的说明，请参阅[创建表 API 帐户](create-table-dotnet.md#create-a-database-account)。

## <a name="create-a-php-application"></a>创建 PHP 应用程序

创建 PHP 应用程序来访问存储表服务或 Azure Cosmos DB 表 API 的唯一要求是在代码中引用 PHP 的 azure-storage-table SDK 中的类。 可以使用任何开发工具（包括“记事本”）创建应用程序。

在本指南中，将使用存储表服务或 Azure Cosmos DB 功能，这些功能可在 PHP 应用程序中本地调用，或通过在 Azure 的 Web 角色、辅助角色或网站中运行的代码调用。

## <a name="get-the-client-library"></a>获取客户端库

1. 在项目的根目录中创建名为 composer.json 的文件并向其添加以下代码：
```json
{
  "require": {
    "microsoft/azure-storage-table": "*"
  }
}
```
2. 将 [composer.phar](http://getcomposer.org/composer.phar) 下载到根目录中。 
3. 打开命令提示符并在项目根目录中执行以下命令：
```
php composer.phar install
```
或者，转到 GitHub 上的 [Azure 存储表 PHP 客户端库](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table)，然后克隆源代码。


## <a name="add-required-references"></a>添加所需引用
若要使用存储表服务或 Azure Cosmos DB API，必须：

* 使用 [require_once][require_once]语句引用 autoloader 文件，并
* 引用所使用的所有类。

以下示例展示了如何添加 autoloader 文件并引用 **TableRestProxy** 类。

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Table\TableRestProxy;
```

在下面的示例中，`require_once` 语句将始终显示，但只会引用执行该示例所需的类。

## <a name="add-a-storage-table-service-connection"></a>添加存储表服务连接
若要实例化存储表服务客户端，必须首先具有有效的连接字符串。 表存储服务连接字符串的格式为：

```php
$connectionString = "DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]"
```

## <a name="add-an-azure-cosmos-db-connection"></a>添加 Azure Cosmos DB 连接
若要实例化 Azure Cosmos DB 表客户端，必须首先具有有效的连接字符串。 Azure Cosmos DB 连接字符串的格式为：

```php
$connectionString = "DefaultEndpointsProtocol=[https];AccountName=[myaccount];AccountKey=[myaccountkey];TableEndpoint=[https://myendpoint/]";
```

## <a name="add-a-storage-emulator-connection"></a>添加存储模拟器连接
若要访问模拟器存储，请执行以下操作：

```php
UseDevelopmentStorage = true
```

若要创建 Azure 表服务客户端或 Azure Cosmos DB 客户端，需要使用 **TableRestProxy** 类。 可以：

* 将连接字符串直接传递给此类或
* 使用 **CloudConfigurationManager (CCM)** 检查多个外部源以获取连接字符串：
  * 默认情况下，它附带了对一个外部源的支持 - 环境变量。
  * 可通过扩展 `ConnectionStringSource` 类来添加新源。

在此处列出的示例中，将直接传递连接字符串。

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;

$tableClient = TableRestProxy::createTableService($connectionString);
```

## <a name="create-a-table"></a>创建表
利用 **TableRestProxy** 对象，可以使用 **createTable** 方法创建表。 创建表时，可以设置表服务超时。 （有关表服务超时的详细信息，请参阅[为表服务操作超时的设置][table-service-timeouts]。）

```php
require_once 'vendor\autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create Table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    // Create table.
    $tableClient->createTable("mytable");
}
catch(ServiceException $e){
    $code = $e->getCode();
    $error_message = $e->getMessage();
    // Handle exception based on error codes and messages.
    // Error codes and messages can be found here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
}
```

有关表名称限制的信息，请参阅[了解表服务数据模型][table-data-model]。

## <a name="add-an-entity-to-a-table"></a>将实体添加到表
若要将实体添加到表，请创建新的 **Entity** 对象并将其传递到 **TableRestProxy->insertEntity**。 请注意，在创建实体时，必须指定 `PartitionKey` 和 `RowKey`。 这些值是实体的唯一标识符，并且其查询速度比其他实体属性的查询速度快得多。 系统使用 `PartitionKey` 自动将表的实体分发到许多存储节点上。 具有相同 `PartitionKey` 的实体存储在同一个节点上。 （对存储在同一节点上的多个实体执行操作要将比对存储在不同节点上的实体执行的操作的效果更佳。）`RowKey` 是实体在分区中的唯一 ID。

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$entity = new Entity();
$entity->setPartitionKey("tasksSeattle");
$entity->setRowKey("1");
$entity->addProperty("Description", null, "Take out the trash.");
$entity->addProperty("DueDate",
                        EdmType::DATETIME,
                        new DateTime("2012-11-05T08:15:00-08:00"));
$entity->addProperty("Location", EdmType::STRING, "Home");

try{
    $tableClient->insertEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
}
```

有关表属性和类型的信息，请参阅[了解表服务数据模型][table-data-model]。

**TableRestProxy** 类提供了用于插入实体的两个替代方法：**insertOrMergeEntity** 和 **insertOrReplaceEntity**。 要使用这些方法，请创建新的 **Entity**，并将其作为参数传递到上述任一方法。 如果实体不存在，则每种方法都将插入实体。 在实体已存在的情况下，如果属性已存在，则 **insertOrMergeEntity** 更新属性值；如果属性不存在，则该方法添加新属性，而 **insertOrReplaceEntity** 会完全替换现有实体。 下面的示例演示如何使用 **insertOrMergeEntity**。 如果 `PartitionKey` 为“tasksSeattle”且 `RowKey` 为“1”的实体不存在，则将插入该实体。 但是，如果之前已插入该实体（如上面的示例所示），则将更新 `DueDate` 属性并添加 `Status` 属性。 系统还将更新 `Description` 和 `Location` 属性，但使用的值实际上会使其保持不变。 如果后面两个属性不是如示例中所示添加的，但已存在于目标实体上，则其现有值将保持不变。

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

//Create new entity.
$entity = new Entity();

// PartitionKey and RowKey are required.
$entity->setPartitionKey("tasksSeattle");
$entity->setRowKey("1");

// If entity exists, existing properties are updated with new values and
// new properties are added. Missing properties are unchanged.
$entity->addProperty("Description", null, "Take out the trash.");
$entity->addProperty("DueDate", EdmType::DATETIME, new DateTime()); // Modified the DueDate field.
$entity->addProperty("Location", EdmType::STRING, "Home");
$entity->addProperty("Status", EdmType::STRING, "Complete"); // Added Status field.

try    {
    // Calling insertOrReplaceEntity, instead of insertOrMergeEntity as shown,
    // would simply replace the entity with PartitionKey "tasksSeattle" and RowKey "1".
    $tableClient->insertOrMergeEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="retrieve-a-single-entity"></a>检索单个实体
利用 **TableRestProxy->getEntity** 方法，可以通过查询实体的 `PartitionKey` 和 `RowKey` 来检索它。 在以下示例中，分区键 `tasksSeattle` 和行键 `1` 传递给 **getEntity** 方法。

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    $result = $tableClient->getEntity("mytable", "tasksSeattle", 1);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entity = $result->getEntity();

echo $entity->getPartitionKey().":".$entity->getRowKey();
```

## <a name="retrieve-all-entities-in-a-partition"></a>检索分区中的所有实体
使用筛选器来构造实体查询（有关详细信息，请参阅[查询表和实体][filters]）。 若要检索分区中的所有实体，请使用筛选器“PartitionKey eq *partition_name*”。 下面的示例演示如何通过将筛选器传递到 **queryEntities** 方法来检索 `tasksSeattle` 分区中的所有实体。

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$filter = "PartitionKey eq 'tasksSeattle'";

try    {
    $result = $tableClient->queryEntities("mytable", $filter);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entities = $result->getEntities();

foreach($entities as $entity){
    echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
}
```

## <a name="retrieve-a-subset-of-entities-in-a-partition"></a>检索分区中的一部分实体
可以使用上一示例中使用的同一模式来检索分区中的部分实体。 检索的实体子集由所使用的筛选器确定（有关详细信息，请参阅[查询表和实体][filters]）。下面的示例演示如何使用筛选器检索具有特定的 `Location` 和早于 `DueDate` 的所有实体。

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$filter = "Location eq 'Office' and DueDate lt '2012-11-5'";

try    {
    $result = $tableClient->queryEntities("mytable", $filter);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entities = $result->getEntities();

foreach($entities as $entity){
    echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
}
```

## <a name="retrieve-a-subset-of-entity-properties"></a>检索一部分实体属性
查询可检索一部分实体属性。 此方法称为投影，可减少带宽并提高查询性能，尤其适用于大型实体。 若要指定要检索的属性，请将该属性的名称传递到 **Query->addSelectField** 方法。 可以多次调用此方法来添加更多属性。 执行 **TableRestProxy->queryEntities** 后，返回的实体将仅具有选定的属性。 （若要返回一部分表实体，请使用上述查询中所示的筛选器。）

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\QueryEntitiesOptions;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$options = new QueryEntitiesOptions();
$options->addSelectField("Description");

try    {
    $result = $tableClient->queryEntities("mytable", $options);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

// All entities in the table are returned, regardless of whether
// they have the Description field.
// To limit the results returned, use a filter.
$entities = $result->getEntities();

foreach($entities as $entity){
    $description = $entity->getProperty("Description")->getValue();
    echo $description."<br />";
}
```

## <a name="update-an-entity"></a>更新实体
可通过对现有实体使用 **Entity->setProperty** 和 **Entity->addProperty** 方法并调用 **TableRestProxy->updateEntity** 来更新该实体。 下面的示例将检索一个实体、修改一个属性、删除另一个属性并添加一个新属性。 请注意，将属性值设为 **null** 可删除该属性。

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$result = $tableClient->getEntity("mytable", "tasksSeattle", 1);

$entity = $result->getEntity();
$entity->setPropertyValue("DueDate", new DateTime()); //Modified DueDate.
$entity->setPropertyValue("Location", null); //Removed Location.
$entity->addProperty("Status", EdmType::STRING, "In progress"); //Added Status.

try    {
    $tableClient->updateEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="delete-an-entity"></a>删除实体
若要删除实体，请将表名称以及实体的 `PartitionKey` 和 `RowKey` 传递到 **TableRestProxy->deleteEntity** 方法。

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    // Delete entity.
    $tableClient->deleteEntity("mytable", "tasksSeattle", "2");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

为了进行并发检查，可以使用 **DeleteEntityOptions->setEtag** 方法并将 **DeleteEntityOptions** 对象作为第四个参数传递到 **deleteEntity**，来为要删除的实体设置 Etag。

## <a name="batch-table-operations"></a>对表操作进行批处理
利用 **TableRestProxy->batch** 方法，可以通过一个请求执行多个操作。 此处的模式涉及将操作添加到 **BatchRequest**对象，并将 **BatchRequest** 对象传递到 **TableRestProxy->batch** 方法。 要将操作添加到 **BatchRequest** 对象，可以多次调用以下任一方法：

* **addInsertEntity**（添加 insertEntity 操作）
* **addUpdateEntity**（添加 updateEntity 操作）
* **addMergeEntity**（添加 mergeEntity 操作）
* **addInsertOrReplaceEntity**（添加 insertOrReplaceEntity 操作）
* **addInsertOrMergeEntity**（添加 insertOrMergeEntity 操作）
* **addDeleteEntity**（添加 deleteEntity 操作）

下面的示例演示了如何通过单个请求执行 **insertEntity** 和 **deleteEntity** 操作。 

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;
use MicrosoftAzure\Storage\Table\Models\BatchOperations;

// Configure a connection string for Storage Table service.
$connectionString = "DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]"

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

// Create list of batch operation.
$operations = new BatchOperations();

$entity1 = new Entity();
$entity1->setPartitionKey("tasksSeattle");
$entity1->setRowKey("2");
$entity1->addProperty("Description", null, "Clean roof gutters.");
$entity1->addProperty("DueDate",
                        EdmType::DATETIME,
                        new DateTime("2012-11-05T08:15:00-08:00"));
$entity1->addProperty("Location", EdmType::STRING, "Home");

// Add operation to list of batch operations.
$operations->addInsertEntity("mytable", $entity1);

// Add operation to list of batch operations.
$operations->addDeleteEntity("mytable", "tasksSeattle", "1");

try    {
    $tableClient->batch($operations);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

有关对表操作进行批处理的详细信息，请参阅[执行实体组事务][entity-group-transactions]。

## <a name="delete-a-table"></a>删除表
最后，若要删除表，请将表名传递到 **TableRestProxy->deleteTable** 方法。

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    // Delete table.
    $tableClient->deleteTable("mytable");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>后续步骤
现在，你已了解了 Azure 表服务和 Azure Cosmos DB 的基础知识，请单击下面的链接来了解更多信息。

* [Microsoft Azure 存储资源管理器](../vs-azure-tools-storage-manage-with-storage-explorer.md)是 Microsoft 免费提供的独立应用，适用于在 Windows、macOS 和 Linux 上以可视方式处理 Azure 存储数据。

* [PHP 开发人员中心](/develop/php/)。

[download]: https://packagist.org/packages/microsoft/azure-storage-table
[require_once]: http://php.net/require_once
[table-service-timeouts]: https://docs.microsoft.com/rest/api/storageservices/setting-timeouts-for-table-service-operations

[table-data-model]: https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model
[filters]: https://docs.microsoft.com/rest/api/storageservices/Querying-Tables-and-Entities
[entity-group-transactions]: https://docs.microsoft.com/rest/api/storageservices/Performing-Entity-Group-Transactions
