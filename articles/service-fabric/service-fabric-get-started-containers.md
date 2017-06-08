---
title: "创建 Azure Service Fabric 容器应用 | Microsoft Docs"
description: "在 Azure Service Fabric 上创建第一个容器应用。  通过应用生成 Docker 映像，将该映像推送到容器注册表，然后生成并部署 Service Fabric 容器应用。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/08/2017
ms.author: ryanwi
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: acb68b274228aa647dc7be5d36b2b077bd213c1b
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017


---

# <a name="create-your-first-service-fabric-container-app"></a>创建第一个 Service Fabric 容器应用
在 Service Fabric 群集上运行 Windows 容器中的现有应用程序不需对应用进行任何更改。 本快速入门介绍如何创建包含 Web 应用的 Docker 映像，将这个新的映像推送到 Azure 容器注册表，创建 Service Fabric 容器应用，然后将该容器应用部署到 Service Fabric 群集。  本文假定读者对 Docker 有一个基本的了解。 阅读 [Docker Overview](https://docs.docker.com/engine/understanding-docker/)（Docker 概述）即可了解 Docker。

## <a name="prerequisites"></a>先决条件
一台运行以下软件的开发计算机：
* Visual Studio 2015 或 Visual Studio 2017。
* [Service Fabric SDK 和工具](service-fabric-get-started.md)。
*  适用于 Windows 的 Docker。  [Get Docker CE for Windows (stable)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description)（获取适用于 Windows 的 Docker CE（稳定版））。 安装并启动 Docker 以后，右键单击任务栏图标，然后选择“切换到 Windows 容器”。 这是运行基于 Windows 的 Docker 映像所必需的。

一个 Windows 群集，其中至少有三个节点运行在包含容器的 Windows Server 2016 上 - [创建群集](service-fabric-get-started-azure-cluster.md)或[免费试用 Service Fabric](http://tryazureservicefabric.westus.cloudapp.azure.com/)。 

一个位于 Azure 容器注册表中的注册表 - 在 Azure 订阅中[创建容器注册表](../container-registry/container-registry-get-started-portal.md)。 

## <a name="create-a-simple-web-app"></a>创建简单的 Web 应用
在一个位置收集需加载到 Docker 映像中的所有资产。 就本快速入门来说，请在开发计算机上创建“Hello World”Web 应用。

1. 创建一个目录，例如 *c:\temp\helloworldapp*。
2. 创建子目录 *c:\temp\helloworldapp\content*。
3. 在 *c:\temp\helloworldapp\content* 中创建文件 *index.html*。
4. 编辑 *index.html*，添加以下行：
    ```
    <h1>Hello World!</h1>
    ```
5. 保存对 *index.html* 所做的更改。

## <a name="build-the-docker-image"></a>生成 Docker 映像
生成基于 [microsft/iis 映像](https://hub.docker.com/r/microsoft/iis/)（位于 Docker 中心）的映像。 microsoft/iis 映像派生自 Windows Server Core 基本 OS 映像，其中包含 Internet Information Services (IIS)。  在容器中运行此映像会自动启动 IIS 和安装的网站。

在 Dockerfile 中定义 Docker 映像。 Dockerfile 包含的说明介绍了如何生成映像和加载要运行的应用。 Dockerfile 是 ```docker build``` 命令的输入，该命令用于创建映像。 

1. 在 *c:\temp\helloworldapp* 中创建文件 *Dockerfile*（不带文件扩展名）并添加以下代码：

    ```
    # The `FROM` instruction specifies the base image. You are
    # extending the `microsoft/iis` image.
    FROM microsoft/iis

    # Create a directory to hold the web app in the container.
    RUN mkdir C:\site

    # Create a new IIS site.
    RUN powershell -NoProfile -Command \
        Import-module IISAdministration; \
        New-IISSite -Name "Site" -PhysicalPath C:\site -BindingInformation "*:8000:"

    # Opens port 8000 on the container.
    EXPOSE 8000

    # The final instruction copies the web app you created earlier into the container.
    ADD content/ /site
    ```

    此 Dockerfile 中没有 ```ENTRYPOINT``` 命令。 不需要该命令。 将 Windows Server 与 IIS 一起运行时，IIS 进程为入口点，配置为在基本映像中启动。

    有关详细信息，请阅读 [Dockerfile reference](https://docs.docker.com/engine/reference/builder/)（Dockerfile 参考）。

2. 运行 ```docker build``` 命令，创建运行 Web 应用的映像。 打开 PowerShell 窗口，导航到 *c:\temp\helloworldapp*。 运行以下命令：

    ```
    docker build -t helloworldapp .
    ```
    该命令按 Dockerfile 中的说明生成新映像，并将映像命名（-t 表示标记）为“helloworldapp”。 生成映像时，会从 Docker 中心拉取基本映像，然后创建新映像，以便将应用添加到基本映像之上。  [microsft/iis 映像](https://hub.docker.com/r/microsoft/iis/)和 OS 基本映像为 10.5 GB，需要一定的时间才能下载并提取到开发计算机。  在这段时间内，不妨外出午餐或喝杯咖啡。  如果曾经将基本 OS 映像拉取到开发计算机，则下载时间会缩短。

3. 生成命令执行完以后，请运行 `docker images` 命令，查看有关新映像的信息：

    ```
    docker images
    
    REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
    helloworldapp              latest              86838648aab6        2 minutes ago       10.1 GB
    ```

## <a name="verify-the-image-runs-locally"></a>验证映像是否在本地运行
请先在本地验证映像，然后再将其推送到容器注册表。  

1. 使用 ```docker run``` 启动容器：

    ```
    docker run -d -p 8000:8000 --name my-web-site helloworldapp
    ```

    name 用于为运行的容器（而不是容器 ID）命名。

2. 容器启动以后，查找其 IP 地址，以便通过浏览器连接到正在运行的容器：
    ```
    docker inspect -f "{{ .NetworkSettings.Networks.nat.IPAddress }}" my-web-site
    ```

3. 连接到正在运行的容器。  打开 Web 浏览器，指向端口 8000 上返回的 IP 地址，例如 “ http://172.31.194.61:8000 ” 。 此时会看到标题“Hello World!” 显示在浏览器中。

4. 若要停止容器，请运行：

    ```
    docker stop my-web-site
    ```

5. 从开发计算机中删除该容器：

    ```
    docker rm my-web-site
    ```

## <a name="push-the-image-to-the-container-registry"></a>将映像推送到容器注册表
验证容器是否在开发计算机上运行以后，即可将映像推送到 Azure 容器注册表的注册表中。

1. 运行 ``docker login``，使用[注册表凭据](../container-registry/container-registry-authentication.md)登录到容器注册表。

    以下示例传递了 Azure Active Directory [服务主体](../active-directory/active-directory-application-objects.md)的 ID 和密码。 例如，你可能在自动化方案中向注册表分配了服务主体。

    ```
    docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
    ```

2. 以下命令使用注册表的完全限定路径创建映像的标记或别名。 此示例将映像置于 ```samples``` 命名空间，以免注册表根目录中出现混乱。

    ```
    docker tag helloworldapp myregistry.azurecr.io/samples/helloworldapp
    ```

3.  将映像推送到容器注册表：

    ```
    docker push myregistry.azurecr.io/samples/helloworldapp
    ```

## <a name="create-and-package-the-containerized-service-in-visual-studio"></a>在 Visual Studio 中创建并打包容器化服务
Service Fabric SDK 和工具提供服务模板，用于将容器部署到 Service Fabric 群集。

1. 启动 Visual Studio。  选择“文件” > “新建” > “项目”。
2. 选择“Service Fabric 应用程序”，将其命名为“MyFirstContainer”，然后单击“确定”。
3. 从“服务模板”列表中选择“来宾容器”。
4. 在“映像名称”中输入“myregistry.azurecr.io/samples/helloworldapp”，这是已推送到容器存储库中的映像。 
5. 为服务命名，然后单击“**确定**”。
6. 如果容器化服务需要通信终结点，则现在就可以在 ServiceManifest.xml 文件中向 ```Endpoint``` 添加协议、端口和类型。 就本快速入门来说，容器化服务会在端口 80 上进行侦听： 

    ```xml
    <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
    ```
    提供 ```UriScheme``` 即可向 Service Fabric 命名服务自动注册容器终结点，确保其可以被发现。 本文末尾提供完整的 ServiceManifest.xml 示例文件。 
7. 在 ApplicationManifest.xml 文件的 ```ContainerHostPolicies``` 中指定 ```PortBinding``` 策略，以便配置容器端口到主机端口的映射。  就本快速入门来说，```ContainerPort``` 为 8000（容器根据 Dockerfile 中指定的要求公开端口 8000），```EndpointRef``` 为“Guest1TypeEndpoint”（服务清单中定义的终结点）。  传入到端口 80 上的服务的请求映射到容器上的端口 8000。  如果容器需要通过专用存储库进行身份验证，则添加 ```RepositoryCredentials```。  就本快速入门来说，请为 myregistry.azurecr.io 容器注册表添加帐户名称和密码。 

    ```xml
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
            <PortBinding ContainerPort="8000" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ```

    本文末尾提供完整的 ApplicationManifest.xml 示例文件。
8. 配置群集连接终结点，以便将应用发布到群集。  在 [Azure 门户](https://portal.azure.com)中，可以在群集的“概览”边栏选项卡中查找客户端连接终结点。 在解决方案资源管理器的 **MyFirstContainer**->**PublishProfiles** 下打开 *Cloud.xml*。  将群集名称和连接端口添加到 **ClusterConnectionParameters**。  例如：
    ```xml
    <ClusterConnectionParameters ConnectionEndpoint="containercluster.westus2.cloudapp.azure.com:19000" />
    ```
    
9. 保存所有文件并生成项目。  

10. 若要将应用打包，请右键单击解决方案资源管理器中的“MyFirstContainer”，然后选择“打包”。 

## <a name="deploy-the-container-app"></a>部署容器应用
1. 若要发布应用，请右键单击解决方案资源管理器中的“MyFirstContainer”，然后选择“发布”。

2. [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) 是一项基于 Web 的工具，用于检验和管理 Service Fabric 群集中的应用程序和节点。 打开浏览器，导航到 http://containercluster.westus2.cloudapp.azure.com:19080/Explorer/，然后按应用部署要求操作。  应用会进行部署，但处于错误状态，直至映像下载到群集节点上（这可能需要一段时间，具体取决于映像大小）： ![错误][1]

3. 当应用处于```Ready```状态时，表明应用已准备就绪： ![就绪][2]

4. 打开浏览器，导航到 http://containercluster.westus2.cloudapp.azure.com。 此时会看到标题“Hello World!” 显示在浏览器中。

## <a name="clean-up"></a>清理
只要群集处于运行状态，就会产生费用。若要避免不必要的费用，可考虑[删除群集](service-fabric-get-started-azure-cluster.md#remove-the-cluster)。  [Party 群集](http://tryazureservicefabric.westus.cloudapp.azure.com/)会在数小时后自动删除。

将映像推送到容器注册表以后，即可从开发计算机中删除本地映像：

```
docker rmi helloworldapp
docker rmi myregistry.azurecr.io/samples/helloworldapp
```

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Service Fabric 应用程序和服务清单的完整示例
以下是用在本快速入门中的完整的服务和应用程序清单。

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Guest1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="Guest1Type" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container: -->
    <!--
    <EnvironmentVariables>
      <EnvironmentVariable Name="VariableName" Value="VariableValue"/>
    </EnvironmentVariables>
    -->
  </CodePackage>

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="MyFirstContainerType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Guest1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="8000" EndpointRef="Guest1TypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Guest1">
      <StatelessService ServiceTypeName="Guest1Type" InstanceCount="[Guest1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="next-steps"></a>后续步骤
* 详细了解如何运行 [Service Fabric 上的容器](service-fabric-containers-overview.md)。
* 了解 Service Fabric [应用程序生命周期](service-fabric-application-lifecycle.md)。
* 查看 GitHub 上的 [Service Fabric 容器代码示例](https://github.com/Azure-Samples/service-fabric-dotnet-containers)。

[1]: ./media/service-fabric-get-started-containers/MyFirstContainerError.png
[2]: ./media/service-fabric-get-started-containers/MyFirstContainerReady.png

