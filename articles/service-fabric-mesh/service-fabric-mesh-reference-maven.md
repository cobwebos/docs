---
title: Azure Service Fabric 网格 Maven 参考 | Microsoft Docs
description: 包含如何使用适用于 Service Fabric 网格的 Maven 插件的参考
services: service-fabric-mesh
keywords: maven, java, cli
author: suhuruli
ms.author: suhuruli
ms.date: 11/26/2018
ms.topic: reference
ms.service: service-fabric-mesh
manager: subramar
ms.openlocfilehash: 08e842f5b91bd0ca5f8e8b2a7866f3f9a689ac28
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60811620"
---
# <a name="maven-plugin-for-service-fabric-mesh"></a>适用于 Service Fabric 网格的 Maven 插件

## <a name="prerequisites"></a>必备组件

- Java SDK
- Maven
- 带有网格扩展的 Azure CLI
- Service Fabric CLI

## <a name="goals"></a>目标

### `azure-sfmesh:init`
- 创建一个 `servicefabric` 文件夹，其中包含具有 `application.yaml` 文件的 `appresources` 文件夹。 

### `azure-sfmesh:addservice`
- 使用服务名称在 `servicefabric` 文件夹中创建一个文件夹，并创建服务的 YAML 文件。 

### `azure-sfmesh:addnetwork`
- 使用 `appresources` 文件夹中提供的网络名称生成 `network` YAML 

### `azure-sfmesh:addgateway`
- 使用 `appresources` 文件夹中提供的网关名称生成 `gateway` YAML 

### `azure-sfmesh:addsecret`
- 使用 `appresources` 文件夹中提供的密钥名称生成 `secret` YAML 

### `azure-sfmesh:addsecretvalue`
- 使用 `appresources` 文件夹中提供的密钥和密钥值名称生成 `secretvalue` YAML 

### `azure-sfmesh:deploy`
- 合并 `servicefabric` 文件夹中的 yaml，并在当前文件夹中创建 Azure Resource Manager 模板 JSON。
- 将所有资源部署到 Azure Service Fabric 网格环境 

### `azure-sfmesh:deploytocluster`
- 创建一个文件夹 (`meshDeploy`)，其中包含从 yaml 生成的部署 JSON，这些 JSON 适用于 Service Fabric 群集
- 将所有资源部署到 Service Fabric 群集
 

## <a name="usage"></a>使用情况

若要在 Maven Java 应用中使用 Maven 插件，请将以下代码片段添加到 pom.xml 文件：

```XML
<project>
  ...
  <build>
    ...
    <plugins>
      ...
      <plugin>
        <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-sfmesh-maven-plugin</artifactId>
          <version>0.1.0</version>
      </plugin>
    </plugins>
  </build>
</project>
```

## <a name="common-configuration"></a>常见配置

Maven 插件目前不支持适用于 Azure 的 Maven 插件的常见配置。

## <a name="how-to"></a>操作说明

### <a name="initialize-maven-project-for-azure-service-fabric-mesh"></a>初始化 Azure Service Fabric 网格的 Maven 项目
运行以下命令以创建应用程序资源 YAML 文件。

```cmd
mvn azure-sfmesh:init -DapplicationName=helloworldserver
```

- 在根文件夹中创建一个名为 `servicefabric->appresources` 的文件夹，其中包含名为 `app_helloworldserver` 的应用程序 YAML

### <a name="add-resource-to-your-application"></a>向应用程序添加资源

#### <a name="add-a-new-network-to-your-application"></a>向应用程序添加新网络
运行以下命令以创建网络资源 yaml。 

```cmd
mvn azure-sfmesh:addnetwork -DnetworkName=helloworldservicenetwork -DnetworkAddressPrefix=10.0.0.4/22
```

- 在名为 `network_helloworldservicenetwork` 的文件夹 `servicefabric->appresources` 中创建网络 YAML

#### <a name="add-a-new-service-to-your-application"></a>向应用程序添加新服务
运行以下命令以创建服务 yaml。 

```cmd
mvn azure-sfmesh:addservice -DapplicationName=helloworldserver -DserviceName=helloworldservice -DimageName=helloworldserver:latest -DlistenerPort=8080 -DnetworkRef=helloworldservicenetwork
```

- 在名为 `service_helloworldservice` 的文件夹 `servicefabric->helloworldservice` 中创建一个引用 `helloworldservicenetwork` 和 `helloworldserver` 应用的服务 YAML
- 该服务将侦听端口 8080
- 该服务将使用 helloworldserver:latest 作为其容器映像。

#### <a name="add-a-new-gateway-resource-to-your-application"></a>向应用程序添加新网关资源
运行以下命令以创建网关资源 yaml。 

```cmd
mvn azure-sfmesh:addgateway -DapplicationName=helloworldserver -DdestinationNetwork=helloworldservicenetwork -DgatewayName=helloworldgateway -DlistenerName=helloworldserviceListener -DserviceName=helloworldservice -DsourceNetwork=open -DtcpPort=80
```

- 在名为 `gateway_helloworldgateway` 的文件夹 `servicefabric->appresources` 中创建新网关 YAML
- 引用 `helloworldservicelistener` 作为侦听来自此网关的调用的服务侦听器。 此外，引用 `helloworldservice` 作为服务，引用 `helloworldservicenetwork` 作为网络，引用 `helloworldserver` 作为应用程序。 
- 在端口 80 上侦听请求

#### <a name="add-a-new-volume-to-your-application"></a>向应用程序添加新卷
运行以下命令以创建卷资源 yaml。 

```cmd
mvn azure-sfmesh:addvolume -DvolumeAccountKey=key -DvolumeAccountName=name -DvolumeName=vol1 -DvolumeShareName=share
```

- 在名为 `volume_vol1` 的文件夹 `servicefabric->appresources` 中创建卷 YAML
- 如上所述设置所需参数 `volumeAccountKey` 和 `volumeShareName` 的属性
- 有关如何引用此创建的卷的详细信息，请访问以下内容：[使用 Azure 文件卷部署应用](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md)

#### <a name="add-a-new-secret-resource-to-your-application"></a>向应用程序添加新密钥资源
运行以下命令以创建密钥资源 yaml。 

```cmd
mvn azure-sfmesh:addsecret -DsecretName=secret1
```

- 在名为 `secret_secret1` 的文件夹 `servicefabric->appresources` 中创建密钥 YAML
- 有关如何引用此创建的密钥的详细信息，请访问以下内容：[管理密钥](service-fabric-mesh-howto-manage-secrets.md)

#### <a name="add-a-new-secretvalue-resource-to-your-application"></a>向应用程序添加新密钥值资源
运行以下命令以创建密钥值资源 yaml。 

```cmd
mvn azure-sfmesh:addsecretvalue -DsecretValue=someVal -DsecretValueName=secret1/v1
```

- 在名为 `secretvalue_secret1_v1` 的文件夹 `servicefabric->appresources` 中创建一个密钥值 YAML

### <a name="run-the-application-locally"></a>在本地运行应用程序

在目标 `azure-sfmesh:deploytocluster` 的帮助下，可以使用以下命令在本地运行应用程序：

```cmd
mvn azure-sfmesh:deploytocluster
```

默认情况下，此目标将资源部署到本地群集。 如果要部署到本地群集，则假定已启动并运行本地 Service Fabric 群集。 当前仅在 [Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md) 上支持资源的本地 Service Fabric 群集。

- 从 yaml 创建适用于 Service Fabric 群集的 JSON
- 然后，部署到群集终结点

### <a name="deploy-application-to-azure-service-fabric-mesh"></a>对 Azure Service Fabric 网格部署应用程序

在目标 `azure-sfmesh:deploy` 的帮助下，可以通过运行以下命令部署到 Service Fabric 网格环境：

```cmd
mvn azure-sfmesh:deploy -DresourceGroup=todoapprg -Dlocation=eastus
```

- 如果不存在，则创建名为 `todoapprg` 的资源组。
- 通过合并 YAML 创建 Azure 资源管理器模板 JSON。 
- 将 JSON 部署到 Azure Service Fabric 网格环境。