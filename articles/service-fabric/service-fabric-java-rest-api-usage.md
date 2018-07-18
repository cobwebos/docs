---
title: Azure Service Fabric Java 客户端 API | Microsoft Docs
description: 按照 Service Fabric 客户端 REST API 规范生成和使用 Service Fabric Java 客户端 API
services: service-fabric
documentationcenter: java
author: rapatchi
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/27/2017
ms.author: rapatchi
ms.openlocfilehash: 987959742335940dca8eb57c54d593aea90dec15
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37111178"
---
# <a name="azure-service-fabric-java-client-apis"></a>Azure Service Fabric Java 客户端 API

可以通过 Service Fabric 客户端 API 在 Service Fabric 群集中部署和管理基于微服务的应用程序和容器，不管该群集的位置是 Azure、本地、本地开发计算机还是其他云。 本文介绍如何在 Service Fabric 客户端 REST API 基础上生成和使用 Service Fabric Java 客户端 API

## <a name="generate-the-client-code-using-autorest"></a>使用 AutoRest 生成客户端代码

[AutoRest](https://github.com/Azure/autorest) 是一项工具，可生成用于访问 RESTful Web 服务的客户端库。 “AutoRest 输入”是一项使用 OpenAPI 规范格式描述 REST API 的规范。 [Service Fabric 客户端 REST API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/servicefabric/data-plane) 遵循此规范。

请执行下述步骤，使用 AutoRest 工具生成 Service Fabric Java 客户端代码。

1. 在计算机上安装 nodejs 和 NPM

    如果使用 Linux，则执行以下命令：
    ```bash
    sudo apt-get install npm
    sudo apt install nodejs
    ```
    如果使用 Mac OS X，则执行以下命令：
    ```bash
    brew install node
    ```

2. 使用 NPM 安装 AutoRest。
    ```bash
    npm install -g autorest
    ```

3. 在本地计算机中创建 [azure-rest-api-specs](https://github.com/Azure/azure-rest-api-specs) 存储库的分库并进行克隆，然后从计算机终端转到克隆的位置。


4. 转到已克隆存储库中的下述位置。
    ```bash
    cd specification\servicefabric\data-plane\Microsoft.ServiceFabric\stable\6.0
    ```

    > [!NOTE]
    > 如果群集版本不是 6.0.*，则转到稳定文件夹中的相应目录。
    >   

5. 运行以下 AutoRest 命令，生成 Java 客户端代码。
    
    ```bash
    autorest --input-file= servicefabric.json --java --output-folder=[output-folder-name] --namespace=[namespace-of-generated-client]
    ```
   下面是一个示例，演示了如何使用 AutoRest。
   
    ```bash
    autorest --input-file=servicefabric.json --java --output-folder=java-rest-api-code --namespace=servicefabricrest
    ```
   
   以下命令使用 ``servicefabric.json`` 规范文件作为输入，在 ``java-rest-api-     code`` 文件夹中生成 Java 客户端代码，然后将代码封装在 ``servicefabricrest`` 命名空间中。 执行此步骤后，会生成 ``models`` 和 ``implemenation`` 两个文件夹，并会在 ``java-rest-api-code`` 文件夹中生成 ``ServiceFabricClientAPIs.java`` 和 ``package-info.java`` 两个文件。


## <a name="include-and-use-the-generated-client-in-your-project"></a>在项目中包括和使用生成的客户端

1. 将生成的代码正确地添加到项目中。 建议使用生成的代码创建一个库，然后将该库包括到项目中。
2. 如果创建库，则请在库的项目中包括以下依赖项。 如果使用其他方法，则请采用相应的格式包括依赖项。

    ```
        GroupId:  com.microsoft.rest
        Artifactid: client-runtime
        Version: 1.2.1
    ```
    例如，如果使用 Maven 生成系统，则请在 ``pom.xml`` 文件中包括以下内容：

    ```xml
        <dependency>
          <groupId>com.microsoft.rest</groupId>
          <artifactId>client-runtime</artifactId>
          <version>1.2.1</version>
        </dependency>
    ```

3. 使用以下代码创建 RestClient：

    ```java
        RestClient simpleClient = new RestClient.Builder()
            .withBaseUrl("http://<cluster-ip or name:port>")
            .withResponseBuilderFactory(new ServiceResponseBuilder.Factory())
            .withSerializerAdapter(new JacksonAdapter())
            .build();
        ServiceFabricClientAPIs client = new ServiceFabricClientAPIsImpl(simpleClient);
    ```
4. 使用此客户端对象，根据需要进行相应的调用。 下面是一些演示如何使用客户端对象的示例。 假设在使用下面的 API 之前，应用程序包已生成并上传到映像存储区。
    * 预配应用程序
    
        ```java
            ApplicationTypeImageStorePath imageStorePath = new ApplicationTypeImageStorePath();
            imageStorePath.withApplicationTypeBuildPath("<application-path-in-image-store>");
            client.provisionApplicationType(imageStorePath);
        ```
    * 创建应用程序

        ```java
            ApplicationDescription applicationDescription = new ApplicationDescription();
            applicationDescription.withName("<application-uri>");
            applicationDescription.withTypeName("<application-type>");
            applicationDescription.withTypeVersion("<application-version>");
            client.createApplication(applicationDescription);
        ```

## <a name="understanding-the-generated-code"></a>了解生成的代码
你会发现每个 API 有四个实现重载。 如果有可选参数，你会发现另外还有四个包含这些可选参数的变体。 这里以 API ``removeReplica`` 为例。
 1. **public void removeReplica(String nodeName, UUID partitionId, String replicaId, Boolean forceRemove, Long timeout)**
    * 这是 removeReplica API 调用的同步变体
 2. **public ServiceFuture<Void> removeReplicaAsync(String nodeName, UUID partitionId, String replicaId, Boolean forceRemove, Long timeout, final ServiceCallback<Void> serviceCallback)**
    * 若要使用基于未来的异步编程并使用回调，则可使用 API 调用的此变体
 3. **public Observable<Void> removeReplicaAsync(String nodeName, UUID partitionId, String replicaId)**
    * 若要使用反应式异步编程，则可使用 API 调用的此变体
 4. **public Observable<ServiceResponse<Void>> removeReplicaWithServiceResponseAsync(String nodeName, UUID partitionId, String replicaId)**
    * 若要使用反应式异步编程并处理原始 REST 响应，则可使用 API 调用的此变体

## <a name="next-steps"></a>后续步骤
* 了解 [Service Fabric REST APIs](https://docs.microsoft.com/rest/api/servicefabric/)（Service Fabric REST API）

