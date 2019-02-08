---
title: 将现有的 MongoDB 应用程序与用于 MongoDB 的 Azure Cosmos DB API 和用于 Azure 的 Open Service Broker (OSBA) 集成
description: 本文介绍如何使用用于 Azure 的 Open Service Broker (OSBA) 将现有的 Java 和 MongoDB 应用程序与用于 MongoDB 的 Azure Cosmos DB API 集成。
services: azure-dev-spaces
author: zr-msft
manager: jeconnoc
ms.service: azure-dev-spaces
ms.topic: article
ms.date: 01/25/2019
ms.author: zarhoads
ms.custom: mvc
keywords: Cosmos DB, Open Service Broker, 用于 Azure 的 Open Service Broker
ms.openlocfilehash: 04b513de1d47749bb87b7aaf79839389ab4d7290
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2019
ms.locfileid: "55082593"
---
# <a name="integrate-existing-mongodb-application-with-azure-cosmos-db-api-for-mongodb-and-open-service-broker-for-azure-osba"></a>将现有的 MongoDB 应用程序与用于 MongoDB 的 Azure Cosmos DB API 和用于 Azure 的 Open Service Broker (OSBA) 集成

Azure Cosmos DB 是一种全球分布式多模型数据库服务。 它还提供网络协议兼容性，兼容多个 NoSQL API（包括用于 MongoDB 的 API）。 可以通过用于 MongoDB 的 Cosmos DB API 将 Cosmos DB 与现有的 MongoDB 应用程序配合使用，不需更改应用程序的数据库驱动程序或实现。 也可使用 Open Service Broker for Azure 来预配 Cosmos DB 服务。

本文使用现有的 Java 应用程序，该应用程序使用 MongoDB 数据库并对其进行更新，以便使用 Cosmos DB 数据库，而后者又使用 Open Service Broker for Azure。

## <a name="prerequisites"></a>先决条件

在继续操作之前，必须满足以下条件：
    
* 创建 [Azure Kubernetes 服务群集](kubernetes-walkthrough.md)。
* [在 AKS 群集上安装并配置 Open Service Broker for Azure](integrate-azure.md)。 
* 安装并配置运行 `svcat` 命令所需的[服务目录 CLI](https://svc-cat.io/docs/install/)。
* 有一个现有的 [MongoDB](https://www.mongodb.com/) 数据库。 例如，可以让 MongoDB 在[开发计算机](https://docs.mongodb.com/manual/administration/install-community/)上或 [Azure VM](../virtual-machines/linux/install-mongodb.md) 中运行。
* 通过某种方式（例如 [mongo shell](https://docs.mongodb.com/manual/mongo/)）连接到 MongoDB 数据库并对其进行查询。

## <a name="get-application-code"></a>获取应用程序代码
    
在本文中，请使用 [Cloud Foundry 提供的 Spring Music 示例应用程序](https://github.com/cloudfoundry-samples/spring-music)来演示一个使用 MongoDB 数据库的应用程序。
    
从 GitHub 克隆此应用程序，然后导航到其目录中：
    
```cmd
git clone https://github.com/cloudfoundry-samples/spring-music
cd spring-music
```

## <a name="prepare-the-application-to-use-your-mongodb-database"></a>准备使用 MongoDB 数据库所需的应用程序

Spring Music 示例应用程序为数据源提供许多选项。 在本文中，请将它配置为使用现有的 MongoDB 数据库。 

将以下 YAML 添加到 *src/main/resources/application.yml* 末尾。 该添加操作创建名为 *mongodb* 的配置文件并配置 URI 和数据库名称。 将 URI 替换为连接到现有 MongoDB 数据库所需的信息。 将包含用户名和密码的 URI 直接添加到此文件的操作**仅限开发用途**，**不应将其添加到版本控制**。

```yaml
---
spring:
  profiles: mongodb
  data:
    mongodb:
      uri: "mongodb://user:password@serverAddress:port/musicdb"
      database: musicdb
```



启动应用程序并告知其使用 *mongodb* 配置文件以后，该应用程序会连接到 MongoDB 数据库并用它来存储应用程序的数据。

若要生成应用程序，请执行以下操作：

```cmd
./gradlew clean assemble

Starting a Gradle Daemon (subsequent builds will be faster)

BUILD SUCCESSFUL in 10s
4 actionable tasks: 4 executed
```

启动应用程序并告知其使用 *mongodb* 配置文件：

```cmd
java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

在浏览器中，导航到 http://localhost:8080。

![使用默认数据的 Spring Music 应用](media/music-app.png)

请注意，应用程序已填充了一些[默认数据](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/albums.json)。 与之交互：删除一些现有的专辑，并创建一些新的。

可以验证应用程序是否使用 MongoDB 数据库，方法是在连接到该数据库后查询 *musicdb* 数据库：

```cmd
mongo serverAddress:port/musicdb -u user -p password
use musicdb
db.album.find()

{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446d"), "title" : "Nevermind", "artist" : "Nirvana", "releaseYear" : "1991", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446e"), "title" : "Pet Sounds", "artist" : "The Beach Boys", "releaseYear" : "1966", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446f"), "title" : "What's Going On", "artist" : "Marvin Gaye", "releaseYear" : "1971", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
...
```

上一示例使用 [mongo shell](https://docs.mongodb.com/manual/mongo/) 连接到 MongoDB 数据库并对其进行查询。 也可通过以下方式验证所做的更改是否已持久保存：停止并重启应用程序，然后在浏览器中导航回到该应用程序。 注意所做的更改仍在该处。


## <a name="create-a-cosmos-db-database"></a>创建 Cosmos DB 数据库

若要通过 Open Service Broker 在 Azure 中创建 Cosmos DB 数据库，请使用 `svcat provision` 命令：

```cmd
svcat provision musicdb --class azure-cosmosdb-mongo-account --plan account  --params-json '{
  "location": "eastus",
  "resourceGroup": "MyResourceGroup",
  "ipFilters" : {
    "allowedIPRanges" : ["0.0.0.0/0"]
  }
}'
```

上一命令在 Azure 中预配 Cosmos DB 数据库，所在的资源组为 *MyResourceGroup*，所在区域为 *eastus*。 有关 *resourceGroup*、*location* 以及其他特定于 Azure 的 JSON 参数的详细信息，请参阅 [Cosmos DB 模块参考文档](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/cosmosdb.md#provision-3)。

若要验证数据库是否已完成预配，请使用 `svcat get instance` 命令：

```cmd
$ svcat get instance musicdb

   NAME     NAMESPACE              CLASS                PLAN     STATUS
+---------+-----------+------------------------------+---------+--------+
  musicdb   default     azure-cosmosdb-mongo-account   account   Ready
```

如果在“状态”下看到“就绪”，则表明数据库已准备就绪。

数据库完成预配以后，需将其元数据绑定到 [Kubernetes 机密](https://kubernetes.io/docs/concepts/configuration/secret/)。 在将该数据绑定到机密以后，其他应用程序就可以访问该数据。 若要将数据库的元数据绑定到某个机密，请使用 `svcat bind` 命令：

```cmd
$ svcat bind musicdb

  Name:        musicdb
  Namespace:   default
  Status:
  Secret:      musicdb
  Instance:    musicdb

Parameters:
  No parameters defined
```


## <a name="use-the-cosmos-db-database-with-your-application"></a>将 Cosmos DB 数据库与应用程序配合使用

若要将 Cosmos DB 数据库与应用程序配合使用，需要知道连接到该数据库所需的 URI。 若要获取该信息，请使用 `kubectl get secret` 命令：

```cmd
$ kubectl get secret musicdb -o=jsonpath='{.data.uri}' | base64 --decode

mongodb://12345678-90ab-cdef-1234-567890abcdef:aaaabbbbccccddddeeeeffffgggghhhhiiiijjjjkkkkllllmmmmnnnnooooppppqqqqrrrrssssttttuuuuvvvv@098765432-aaaa-bbbb-cccc-1234567890ab.documents.azure.com:10255/?ssl=true&replicaSet=globaldb
```

上一命令获取 *musicdb* 机密，仅显示 URI。 机密以 base64 格式存储，因此上一命令也会将其解码。

使用 Cosmos DB 数据库的 URI 更新 *src/main/resources/application.yml*，以方便使用它：

```yaml
...
---
spring:
  profiles: mongodb
  data:
    mongodb:
      uri: "mongodb://12345678-90ab-cdef-1234-567890abcdef:aaaabbbbccccddddeeeeffffgggghhhhiiiijjjjkkkkllllmmmmnnnnooooppppqqqqrrrrssssttttuuuuvvvv@098765432-aaaa-bbbb-cccc-1234567890ab.documents.azure.com:10255/?ssl=true&replicaSet=globaldb"
      database: musicdb
```

使用包含用户名和密码的 URI 更新此文件的操作**仅限开发用途**，**不应将其添加到版本控制**。

重新生成并启动应用程序即可开始使用 Cosmos DB 数据库：

```cmd
./gradlew clean assemble

java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

注意，应用程序仍使用 *mongodb* 配置文件以及以 *mongodb://* 开头的 URI 来连接到 Cosmos DB 数据库。 [用于 MongoDB 的 Azure Cosmos DB API](../cosmos-db/mongodb-introduction.md) 提供此兼容性。 使用它，应用程序就可以继续运行，就像使用 MongoDB 数据库一样，但实际上它使用的是 Cosmos DB。

在浏览器中，导航到 http://localhost:8080。 请注意，默认数据已还原。 与之交互：删除一些现有的专辑，并创建一些新的。 可通过以下方式验证所做的更改是否已持久保存：停止并重启应用程序，然后在浏览器中导航回到该应用程序。 注意所做的更改仍在该处。 所做的更改保存到使用 Open Service Broker for Azure 创建的 Cosmos DB。


## <a name="run-your-application-on-your-aks-cluster"></a>在 AKS 群集上运行应用程序

可以使用 [Azure Dev Spaces](../dev-spaces/azure-dev-spaces.md) 将应用程序部署到 AKS 群集。 Azure Dev Spaces 可以用来在 AKS 中生成项目（例如 Dockefile 和 Helm 图表），以及部署并运行应用程序。

若要在 AKS 群集中启用 Azure Dev Spaces，请执行以下操作：

```cmd
az aks enable-addons --addons http_application_routing -g MyResourceGroup -n MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

使用 Azure Dev Spaces 工具准备要在 AKS 中运行的应用程序：

```cmd
azds prep --public
```

此命令生成多个项目，包括位于项目根目录中的 *charts/* 文件夹，即 Helm 图表。 此命令不能为此特定的项目生成 *Dockerfile*，因此你必须创建它。

在项目的根目录中创建名为 *Dockerfile* 且包含以下内容的文件：

```dockerfile
FROM openjdk:8-jdk-alpine
EXPOSE 8080
WORKDIR /app
COPY build/libs/spring-music-1.0.jar .
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-Dspring.profiles.active=mongodb","-jar","/app/spring-music-1.0.jar"]
```

另外，需将 *azds.yaml* 中的 *configurations.develop.build* 属性更新为 *false*：
```yaml
...
configurations:
  develop:
    build:
      useGitIgnore: false
```

还需在 *charts/spring-music/templates/deployment.yaml* 中将 *containerPort* 属性更新为 *8080*：

```yaml
...
spec:
  ...
  template:
    ...
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          imagePullPolicy: {{ .Values.image.pullPolicy }}
          ports:
            - name: http
              containerPort: 8080
              protocol: TCP
```

若要将应用程序部署到 AKS，请执行以下操作：

```cmd
$ azds up

Using dev space 'default' with target 'MyAKS'
Synchronizing files...1m 18s
Installing Helm chart...5s
Waiting for container image build...23s
Building container image...
Step 1/5 : FROM openjdk:8-jdk-alpine
Step 2/5 : EXPOSE 8080
Step 3/5 : WORKDIR /app
Step 4/5 : COPY build/libs/spring-music-1.0.jar .
Step 5/5 : ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-Dspring.profiles.active=mongodb","-jar","/app/spring-music-1.0.jar"]
Built container image in 21s
Waiting for container...8s
Service 'spring-music' port 'http' is available at http://spring-music.1234567890abcdef1234.eastus.aksapp.io/
Service 'spring-music' port 8080 (TCP) is available at http://localhost:57892
press Ctrl+C to detach
...
```

导航到日志中显示的 URL。 在上一示例中，应使用 *http://spring-music.1234567890abcdef1234.eastus.aksapp.io/*。 

验证是否可以看到应用程序以及你所做的更改。

## <a name="next-steps"></a>后续步骤

本文介绍了如何将使用 MongoDB 的现有应用程序更新为使用用于 MongoDB 的 Cosmos DB API。 本文还介绍了如何使用 Open Service Broker for Azure 来预配 Cosmos DB 服务，以及如何使用 Azure Dev Spaces 将该应用程序部署到 AKS。

有关 Cosmos DB、Open Service Broker for Azure 和 Azure Dev Spaces 的详细信息，请参阅：
* [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/)
* [Open Service Broker for Azure](https://osba.sh)
* [使用 Dev Spaces 进行开发](../dev-spaces/azure-dev-spaces.md)
