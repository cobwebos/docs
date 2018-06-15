---
title: 在 Linux 上创建 Azure Service Fabric 容器应用程序 | Microsoft Docs
description: 在 Azure Service Fabric 上创建第一个 Linux 容器应用程序。 生成包含应用程序的 Docker 映像，将该映像推送到容器注册表，然后生成并部署 Service Fabric 容器应用程序。
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/09/2018
ms.author: ryanwi
ms.openlocfilehash: a38eb1f291d00d942ff0a1579b20bca7e012991a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34642931"
---
# <a name="create-your-first-service-fabric-container-application-on-linux"></a>在 Linux 上创建第一个 Service Fabric 容器应用程序
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started-containers.md)
> * [Linux](service-fabric-get-started-containers-linux.md)

在 Service Fabric 群集上运行 Linux 容器中的现有应用程序不需要对应用程序进行任何更改。 本文逐步讲解如何创建包含 Python [Flask](http://flask.pocoo.org/) Web 应用程序的 Docker 映像并将其部署到 Service Fabric 群集。 此外，将通过 [Azure 容器注册表](/azure/container-registry/)共享容器化的应用程序。 本文假定读者对 Docker 有一个基本的了解。 阅读 [Docker Overview](https://docs.docker.com/engine/understanding-docker/)（Docker 概述）即可了解 Docker。

## <a name="prerequisites"></a>先决条件
* 一台运行以下软件的开发计算机：
  * [Service Fabric SDK 和工具](service-fabric-get-started-linux.md)。
  * [适用于 Linux 的 Docker CE](https://docs.docker.com/engine/installation/#prior-releases)。 
  * [Service Fabric CLI](service-fabric-cli.md)

* 一个位于 Azure 容器注册表中的注册表 - 在 Azure 订阅中[创建容器注册表](../container-registry/container-registry-get-started-portal.md)。 

## <a name="define-the-docker-container"></a>定义 Docker 容器
基于 Docker 中心内的 [Python 映像](https://hub.docker.com/_/python/)生成一个映像。 

在 Dockerfile 中指定 Docker 容器。 Dockerfile 包含有关在容器中设置环境、加载要运行的应用程序以及映射端口的说明。 Dockerfile 是 `docker build` 命令的输入，该命令用于创建映像。 

创建一个空目录并创建文件 *Dockerfile*（不带文件扩展名）。 将以下内容添加到 *Dockerfile* 并保存所做的更改：

```
# Use an official Python runtime as a base image
FROM python:2.7-slim

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Install any needed packages specified in requirements.txt
RUN pip install -r requirements.txt

# Make port 80 available outside this container
EXPOSE 80

# Define environment variable
ENV NAME World

# Run app.py when the container launches
CMD ["python", "app.py"]
```

有关详细信息，请阅读 [Dockerfile reference](https://docs.docker.com/engine/reference/builder/)（Dockerfile 参考）。

## <a name="create-a-basic-web-application"></a>创建基本 Web 应用程序
创建一个侦听端口 80 并返回“Hello World!”的 Flask Web 应用程序。 在同一个目录中，创建文件 *requirements.txt*。 添加以下内容并保存所做的更改：
```
Flask
```

此外，创建 *app.py* 文件并添加以下代码片段：

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def hello():
    
    return 'Hello World!'

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=80)
```

## <a name="build-the-image"></a>生成映像
运行 `docker build` 命令，创建运行上述 Web 应用程序的映像。 打开 PowerShell 窗口，导航到 *c:\temp\helloworldapp*。 运行以下命令：

```bash
docker build -t helloworldapp .
```

该命令按 Dockerfile 中的说明生成新映像，并将映像命名为（-t 表示标记）`helloworldapp`。 若要生成容器映像，首先从 Docker Hub 下载基础映像并在其上添加应用程序。 

生成命令执行完以后，请运行 `docker images` 命令，查看有关新映像的信息：

```bash
$ docker images
    
REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
helloworldapp                 latest              86838648aab6        2 minutes ago       194 MB
```

## <a name="run-the-application-locally"></a>在本地运行应用程序
先验证容器化应用程序是否在本地运行，然后将它推送到容器注册表。 

运行应用程序，并将计算机的端口 4000 映射到容器的公开端口 80：

```bash
docker run -d -p 4000:80 --name my-web-site helloworldapp
```

name 用于为运行的容器（而不是容器 ID）命名。

连接到正在运行的容器。 打开 Web 浏览器，指向端口 4000 上返回的 IP 地址，例如 http://localhost:4000。 此时会看到标题“Hello World!” 显示在浏览器中。

![Hello World!][hello-world]

若要停止容器，请运行：

```bash
docker stop my-web-site
```

从开发计算机中删除该容器：

```bash
docker rm my-web-site
```

## <a name="push-the-image-to-the-container-registry"></a>将映像推送到容器注册表
确认应用程序在 Docker 中运行后，请将映像推送到 Azure 容器注册表中的注册表。

运行 `docker login`，使用[注册表凭据](../container-registry/container-registry-authentication.md)登录到容器注册表。

以下示例传递了 Azure Active Directory [服务主体](../active-directory/active-directory-application-objects.md)的 ID 和密码。 例如，你可能在自动化方案中向注册表分配了服务主体。 或者，可以使用注册表用户名和密码登录。

```bash
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

以下命令使用注册表的完全限定路径创建映像的标记或别名。 此示例将映像置于 `samples` 命名空间，以免注册表根目录中出现混乱。

```bash
docker tag helloworldapp myregistry.azurecr.io/samples/helloworldapp
```

将映像推送到容器注册表：

```bash
docker push myregistry.azurecr.io/samples/helloworldapp
```

## <a name="package-the-docker-image-with-yeoman"></a>使用 Yeoman 打包 Docker 映像
用于 Linux 的 Service Fabric SDK 包括 [Yeoman](http://yeoman.io/) 生成器，利用它可以轻松地创建第一个服务应用程序和添加容器映像。 让我们使用 Yeoman 创建具有单个 Docker 容器（名为 *SimpleContainerApp*）的应用程序。

若要创建 Service Fabric 容器应用程序，请打开终端窗口并运行 `yo azuresfcontainer`。 

为应用程序命名（例如 `mycontainer`），以及为应用程序服务命名（例如 `myservice`）。

对于映像名称，请提供容器映像在容器注册表中的 URL（例如“myregistry.azurecr.io/samples/helloworldapp”）。 

此映像中定义了一个工作负荷入口点，因此不需显式指定输入命令（命令在容器中运行，这可以在启动后使容器保持运行）。 

指定实例计数“1”。

![适用于容器的 Service Fabric Yeoman 生成器][sf-yeoman]

## <a name="configure-port-mapping-and-container-repository-authentication"></a>配置端口映射和容器存储库身份验证
容器化服务需要使用一个终结点进行通信。 现在，将协议、端口和类型添加到 ServiceManifest.xml 文件中“Resources”标记下面的 `Endpoint`。 本文所述的容器化服务在端口 4000 上侦听： 

```xml

<Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="myServiceTypeEndpoint" UriScheme="http" Port="4000" Protocol="http"/>
    </Endpoints>
  </Resources>
 ```
 
提供 `UriScheme` 即可向 Service Fabric 命名服务自动注册容器终结点，确保其可以被发现。 本文末尾提供完整的 ServiceManifest.xml 示例文件。 

在 ApplicationManifest.xml 文件的 `ContainerHostPolicies` 中指定 `PortBinding` 策略，以便配置容器端口到主机端口的映射。 在本文中，`ContainerPort` 为 80（容器根据 Dockerfile 中的指定值公开端口 80），`EndpointRef` 为“myServiceTypeEndpoint”（服务清单中定义的终结点）。 传入到端口 4000 上的服务的请求映射到容器上的端口 80。 如果容器需要通过专用存储库进行身份验证，则添加 `RepositoryCredentials`。 在本文中，请为 myregistry.azurecr.io 容器注册表添加帐户名和密码。 确保将策略添加到对应于适当服务包的“ServiceManifestImport”标记下面。

```xml
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServicePkg" ServiceManifestVersion="1.0.0" />
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="80" EndpointRef="myServiceTypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
   </ServiceManifestImport>
``` 
## <a name="configure-docker-healthcheck"></a>配置 docker HEALTHCHECK 
从 v6.1 开始，Service Fabric 自动将 [docker HEALTHCHECK](https://docs.docker.com/engine/reference/builder/#healthcheck) 事件集成到其系统运行状况报告。 这意味着，如果容器启用了 **HEALTHCHECK**，则只要容器的运行状况状态如 Docker 所报告的那样更改，Service Fabric 就会报告运行状况。 当 *health_status* 为“正常”时，会在 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) 中显示运行状况报告“正常”；当 *health_status* 为“不正常”时，会显示“警告”。 生成容器映像时使用的 Dockerfile 中必须存在 **HEALTHCHECK** 指令，该指令指向监视容器运行状况时执行的实际检查。 

![HealthCheckHealthy][1]

![HealthCheckUnealthyApp][2]

![HealthCheckUnhealthyDsp][3]

可以为每个容器配置 **HEALTHCHECK** 行为，方法是在 ApplicationManifest 中将 **HealthConfig** 选项指定为 **ContainerHostPolicies** 的一部分。

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="ContainerServicePkg" ServiceManifestVersion="2.0.0" />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <HealthConfig IncludeDockerHealthStatusInSystemHealthReport="true" RestartContainerOnUnhealthyDockerHealthStatus="false" />
      </ContainerHostPolicies>
    </Policies>
</ServiceManifestImport>
```
默认情况下，*IncludeDockerHealthStatusInSystemHealthReport* 设置为 **true**，*RestartContainerOnUnhealthyDockerHealthStatus* 设置为 **false**。 如果 *RestartContainerOnUnhealthyDockerHealthStatus* 设置为 **true**，则会重启（可能在其他节点上进行）反复报告“不正常”的容器。

若要禁用整个 Service Fabric 群集的 **HEALTHCHECK** 集成，则需将 [EnableDockerHealthCheckIntegration](service-fabric-cluster-fabric-settings.md) 设置为 **false**。

## <a name="build-and-package-the-service-fabric-application"></a>生成并打包 Service Fabric 应用程序
Service Fabric Yeoman 模板包含 [Gradle](https://gradle.org/) 的生成脚本，可用于从终端生成应用程序。 若要生成并打包应用程序，请运行以下命令：

```bash
cd mycontainer
gradle
```

## <a name="deploy-the-application"></a>部署应用程序
生成应用程序后，可以使用 Service Fabric CLI 将其部署到本地群集。

连接到本地 Service Fabric 群集。

```bash
sfctl cluster select --endpoint http://localhost:19080
```

使用模板中提供的安装脚本可将应用程序包复制到群集的映像存储、注册应用程序类型和创建应用程序的实例。

```bash
./install.sh
```

打开浏览器并导航到位于 http://localhost:19080/Explorer 的 Service Fabric Explorer（如果在 Mac OS X 上使用 Vagrant，则使用 VM 的专用 IP 替换 localhost）。 展开应用程序节点，注意现在有一个条目是用于应用程序类型，另一个条目用于该类型的第一个实例。

连接到正在运行的容器。 打开 Web 浏览器，指向端口 4000 上返回的 IP 地址，例如 http://localhost:4000。 此时会看到标题“Hello World!” 显示在浏览器中。

![Hello World!][hello-world]


## <a name="clean-up"></a>清理
使用模板中提供的卸载脚本从本地开发群集中删除应用程序实例并取消注册应用程序类型。

```bash
./uninstall.sh
```

将映像推送到容器注册表以后，即可从开发计算机中删除本地映像：

```
docker rmi helloworldapp
docker rmi myregistry.azurecr.io/samples/helloworldapp
```

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Service Fabric 应用程序和服务清单的完整示例
下面本文中使用的服务和应用程序完整清单。

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="myservicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="myserviceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying containers 
      to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
        <!-- Pass comma delimited commands to your container: dotnet, myproc.dll, 5" -->
        <!--Commands> dotnet, myproc.dll, 5 </Commands-->
        <Commands></Commands>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container: -->
    
    <EnvironmentVariables>
      <!--
      <EnvironmentVariable Name="VariableName" Value="VariableValue"/>
      -->
    </EnvironmentVariables>
    
  </CodePackage>

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="myServiceTypeEndpoint" UriScheme="http" Port="4000" Protocol="http"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="mycontainerType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="myservicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="80" EndpointRef="myServiceTypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="myservice">
      <!-- On a local development cluster, set InstanceCount to 1. On a multi-node production 
      cluster, set InstanceCount to -1 for the container service to run on every node in 
      the cluster.
      -->
      <StatelessService ServiceTypeName="myserviceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
## <a name="adding-more-services-to-an-existing-application"></a>将更多服务添加到现有应用程序

若要将其他容器服务添加到使用 yeoman 创建的应用程序，请执行以下步骤：

1. 将目录更改为现有应用程序的根目录。 例如，如果 `MyApplication` 是 Yeoman 创建的应用程序，则使用 `cd ~/YeomanSamples/MyApplication`。
2. 运行 `yo azuresfcontainer:AddService`

<a id="manually"></a>


## <a name="configure-time-interval-before-container-is-force-terminated"></a>配置在强制终止容器之前需经历的时间间隔

可以配置一个时间间隔，目的是在启动服务删除操作（或移动到另一个节点的操作）之后，要求运行时在删除容器之前等待特定的时间。 配置时间间隔时，会向容器发送 `docker stop <time in seconds>` 命令。  有关更多详细信息，请参阅 [docker stop](https://docs.docker.com/engine/reference/commandline/stop/)。 等待时间间隔在 `Hosting` 节指定。 以下群集清单代码片段显示了如何设置等待时间间隔：


```json
{
        "name": "Hosting",
        "parameters": [
          {
                "name": "ContainerDeactivationTimeout",
                "value" : "10"
          },
          ...
        ]
}
```

默认时间间隔设置为 10 秒。 由于此配置是动态的，因此对群集进行仅限配置的升级即可更新超时。 

## <a name="configure-the-runtime-to-remove-unused-container-images"></a>将运行时配置为删除未使用的容器映像

可以将 Service Fabric 群集配置为从节点删除未使用的容器映像。 如果节点上存在过多容器映像，则可通过此配置回收磁盘空间。 若要启用此功能，请更新群集清单中的 `Hosting` 节，如以下代码片段所示： 


```json
{
        "name": "Hosting",
        "parameters": [
          {
                "name": "PruneContainerImages",
                "value": "True"
          },
          {
                "name": "ContainerImagesToSkip",
                "value": "microsoft/windowsservercore|microsoft/nanoserver|microsoft/dotnet-frameworku|..."
          }
          ...
          }
        ]
} 
```

对于不应删除的映像，可以在 `ContainerImagesToSkip` 参数下进行指定。 

## <a name="configure-container-image-download-time"></a>配置容器映像下载时间

Service Fabric 运行时为下载和解压缩容器映像分配了 20 分钟的时间，这适用于大多数容器映像。 如果是大型映像，或者网络连接速度较慢，则可能必须延长中止映像下载和解压缩之前的等待时间。 此超时可以使用群集清单的 **Hosting** 节的 **ContainerImageDownloadTimeout** 属性来设置，如以下代码片段所示：

```json
{
"name": "Hosting",
        "parameters": [
          {
              "name": " ContainerImageDownloadTimeout ",
              "value": "1200"
          }
]
}
```


## <a name="set-container-retention-policy"></a>设置容器保留策略

为了帮助诊断容器启动故障，Service Fabric（6.1 或更高版本）支持保留终止的或无法启动的容器。 此策略可以在 **ApplicationManifest.xml** 文件中设置，如以下代码片段所示：

```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
```

**ContainersRetentionCount** 设置指定在容器故障时需保留的容器数。 如果指定一个负值，则会保留所有故障容器。 如果不指定 **ContainersRetentionCount** 属性，则不会保留任何容器。 **ContainersRetentionCount** 属性还支持应用程序参数，因此用户可以为测试性群集和生产群集指定不同的值。 使用此功能时可使用放置约束，将容器服务的目标设置为特定的节点，防止将容器服务移至其他节点。 使用此功能保留的容器必须手动删除。

## <a name="start-the-docker-daemon-with-custom-arguments"></a>使用自定义参数启动 Docker 守护程序

有了 6.2 版和更高版本的 Service Fabric 运行时，可以使用自定义参数启动 Docker 守护程序。 指定自定义参数时，Service Fabric 不会将 `--pidfile` 参数以外的任何其他参数传递给 docker 引擎。 因此，`--pidfile` 不应作为参数传递。 此外，参数应继续让 docker 守护程序侦听 Windows 上的默认名称管道（或 Linux 上的 unix 域套接字），以便 Service Fabric 与该守护程序通信。 自定义参数在 **ContainerServiceArguments** 的 **Hosting** 节下的群集清单中指定。 以下代码片段显示了一个示例： 
 

```json
{ 
   "name": "Hosting", 
        "parameters": [ 
          { 
            "name": "ContainerServiceArguments", 
            "value": "-H localhost:1234 -H unix:///var/run/docker.sock" 
          } 
        ] 
} 

```

## <a name="next-steps"></a>后续步骤
* 详细了解如何运行 [Service Fabric 上的容器](service-fabric-containers-overview.md)。
* 阅读[在容器中部署 .NET 应用程序](service-fabric-host-app-in-a-container.md)教程。
* 了解 Service Fabric [应用程序生命周期](service-fabric-application-lifecycle.md)。
* 查看 GitHub 上的 [Service Fabric 容器代码示例](https://github.com/Azure-Samples/service-fabric-containers)。

[hello-world]: ./media/service-fabric-get-started-containers-linux/HelloWorld.png
[sf-yeoman]: ./media/service-fabric-get-started-containers-linux/YoSF.png

[1]: ./media/service-fabric-get-started-containers/HealthCheckHealthy.png
[2]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_App.png
[3]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_Dsp.png
