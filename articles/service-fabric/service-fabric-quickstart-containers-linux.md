---
title: "在 Linux 上创建 Azure Service Fabric 容器应用程序 | Microsoft Docs"
description: "在 Azure Service Fabric 上创建第一个 Linux 容器应用程序。  生成包含应用程序的 Docker 映像，将该映像推送到容器注册表，然后生成并部署 Service Fabric 容器应用程序。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/05/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: eeed445631885093a8e1799a8a5e1bcc69214fe6
ms.openlocfilehash: 78306672e812745fd1902ae264c2adea196ab721
ms.contentlocale: zh-cn
ms.lasthandoff: 09/07/2017

---

# <a name="deploy-a-service-fabric-linux-container-application-on-azure"></a>在 Azure 上部署 Service Fabric Linux 容器应用程序
Azure Service Fabric 是一款分布式系统平台，可用于部署和管理可缩放的可靠微服务和容器。 

在 Service Fabric 群集上运行 Linux 容器中的现有应用程序不需要对应用程序进行任何更改。 本快速入门介绍如何在 Service Fabric 应用程序中部署预建的 Docker 容器映像。 完成后，你会有一个正在运行的 nginx 容器。  本快速入门介绍如何部署 Linux 容器。若要部署 Windows 容器，请阅读[此快速入门](service-fabric-quickstart-containers.md)。

![Nginx][nginx]

此快速入门介绍如何：
> [!div class="checklist"]
> * 打包 Docker 映像容器
> * 配置通信
> * 生成并打包 Service Fabric 应用程序
> * 将容器应用程序部署到 Azure

## <a name="prerequisites"></a>先决条件
安装 [Service Fabric SDK、Service Fabric CLI 和 Service Fabric yeoman 模板生成器](service-fabric-get-started-linux.md)。
  
## <a name="package-a-docker-image-container-with-yeoman"></a>使用 Yeoman 打包 Docker 映像容器
用于 Linux 的 Service Fabric SDK 包括 [Yeoman](http://yeoman.io/) 生成器，利用它可以轻松地创建第一个服务应用程序和添加容器映像。 

若要创建 Service Fabric 容器应用程序，请打开终端窗口并运行 `yo azuresfcontainer`。  

将应用程序命名为“MyFirstContainer”，将应用程序服务命名为“MyContainerService”。

提供容器映像名称“nginx:latest”（Docker 中心的 [nginx 容器映像](https://hub.docker.com/r/_/nginx/)）。 

此映像已定义工作负荷入口点，因此需显式指定输入命令。 

指定实例计数“1”。

![适用于容器的 Service Fabric Yeoman 生成器][sf-yeoman]

## <a name="configure-communication-and-container-port-to-host-port-mapping"></a>配置通信和容器端口到主机端口映射
配置 HTTP 终结点，使客户端能够与服务通信。  打开 ./MyFirstContainer/MyContainerServicePkg/ServiceManifest.xml 文件，在 ServiceManifest 元素中声明终结点资源。  添加协议、端口和名称。 就本快速入门来说，服务会在端口 80 上进行侦听： 

```xml
<Resources>
  <Endpoints>
    <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
    <Endpoint Name="myserviceTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
  </Endpoints>
</Resources>

```
提供 `UriScheme` 即可向 Service Fabric 命名服务自动注册容器终结点，确保其可以被发现。 本文末尾提供完整的 ServiceManifest.xml 示例文件。 

使用 ApplicationManifest.xml 文件的 `ContainerHostPolicies` 中的 `PortBinding` 策略，将容器端口映射到服务 `Endpoint`。  就本快速入门来说，`ContainerPort` 为 80（容器公开端口 80），`EndpointRef` 为“myserviceTypeEndpoint”（以前在服务清单中定义的终结点）。  传入到端口 80 上的服务的请求映射到容器上的端口 80。  

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="80" EndpointRef="myserviceTypeEndpoint"/>
  </ContainerHostPolicies>
</Policies>
```

## <a name="build-and-package-the-service-fabric-application"></a>生成并打包 Service Fabric 应用程序
Service Fabric Yeoman 模板包含 [Gradle](https://gradle.org/) 的生成脚本，可用于从终端生成应用程序。 保存所有更改。  若要生成并打包应用程序，请运行以下命令：

```bash
cd MyFirstContainer
gradle
```
## <a name="create-a-cluster"></a>创建群集
若要将应用程序部署到 Azure 群集，可以创建自己的群集，也可以使用合作群集。

合作群集是在 Azure 上托管的、由 Service Fabric 团队运行的免费限时 Service Fabric 群集，任何人都可以在其中部署应用程序及了解平台的情况。 若要使用合作群集，请[按照说明操作](http://aka.ms/tryservicefabric)。  

若要了解如何创建自己的群集，请参阅[在 Azure 上创建你的第一个 Service Fabric 群集](service-fabric-get-started-azure-cluster.md)。

请记下连接终结点，下面的步骤会使用该终结点。

## <a name="deploy-the-application-to-azure"></a>将应用程序部署到 Azure
生成应用程序后，可以使用 Service Fabric CLI 将其部署到 Azure 群集。

连接到 Azure 中的 Service Fabric 群集。

```bash
sfctl cluster select --endpoint http://lnxt10vkfz6.westus.cloudapp.azure.com:19080
```

使用模板中提供的安装脚本可将应用程序包复制到群集的映像存储、注册应用程序类型和创建应用程序的实例。

```bash
./install.sh
```

打开浏览器并导航到 Service Fabric Explorer，其网址为 http://lnxt10vkfz6.westus.cloudapp.azure.com:19080/Explorer。 展开应用程序节点，注意现在有一个条目是用于应用程序类型，另一个条目用于该类型的第一个实例。

![Service Fabric Explorer][sfx]

连接到正在运行的容器。  打开 Web 浏览器并指向在端口 80 上返回的 IP 地址，例如“lnxt10vkfz6.westus.cloudapp.azure.com:80”。 此时会看到浏览器中显示 nginx 欢迎页。

![Nginx][nginx]

## <a name="clean-up"></a>清理
使用模板中提供的卸载脚本从群集中删除应用程序实例并取消注册应用程序类型。

```bash
./uninstall.sh
```

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Service Fabric 应用程序和服务清单的完整示例
以下是用在本快速入门中的完整服务和应用程序清单。

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="MyContainerServicePkg" Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" >

   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="MyContainerServiceType" UseImplicitHost="true">
   </StatelessServiceType>
   </ServiceTypes>
   
   <CodePackage Name="code" Version="1.0.0">
      <EntryPoint>
         <ContainerHost>
            <ImageName>nginx:latest</ImageName>
            <Commands></Commands>
         </ContainerHost>
      </EntryPoint>
      <EnvironmentVariables> 
      </EnvironmentVariables> 
   </CodePackage>
<Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="myserviceTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
    </Endpoints>
  </Resources>
 </ServiceManifest>

```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest  ApplicationTypeName="MyFirstContainerType" ApplicationTypeVersion="1.0.0"
                      xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
   
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyContainerServicePkg" ServiceManifestVersion="1.0.0" />
   <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <PortBinding ContainerPort="80" EndpointRef="myserviceTypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
</ServiceManifestImport>
   
   <DefaultServices>
      <Service Name="MyContainerService">
        <!-- On a local development cluster, set InstanceCount to 1.  On a multi-node production 
        cluster, set InstanceCount to -1 for the container service to run on every node in 
        the cluster.
        -->
        <StatelessService ServiceTypeName="MyContainerServiceType" InstanceCount="1">
            <SingletonPartition />
        </StatelessService>
      </Service>
   </DefaultServices>
   
</ApplicationManifest>

```

## <a name="next-steps"></a>后续步骤
此快速入门介绍如何：
> [!div class="checklist"]
> * 打包 Docker 映像容器
> * 配置通信
> * 生成并打包 Service Fabric 应用程序
> * 将容器应用程序部署到 Azure

* 详细了解如何运行 [Service Fabric 上的容器](service-fabric-containers-overview.md)。
* 阅读[在容器中部署 .NET 应用程序](service-fabric-host-app-in-a-container.md)教程。
* 了解 Service Fabric [应用程序生命周期](service-fabric-application-lifecycle.md)。
* 查看 GitHub 上的 [Service Fabric 容器代码示例](https://github.com/Azure-Samples/service-fabric-dotnet-containers)。

[sfx]: ./media/service-fabric-quickstart-containers-linux/SFX.png
[nginx]: ./media/service-fabric-quickstart-containers-linux/nginx.png
[sf-yeoman]: ./media/service-fabric-quickstart-containers-linux/YoSF.png

