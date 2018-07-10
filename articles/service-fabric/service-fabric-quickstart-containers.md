---
title: 在 Azure 中的 Service Fabric 上创建 Windows 容器应用 |Microsoft Docs
description: 在本快速入门中，请在 Azure Service Fabric 上创建第一个 Windows 容器应用程序。
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/30/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 95877f8b81641dd70434fc167003cfcce07d9e84
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110804"
---
# <a name="quickstart-deploy-windows-containers-to-service-fabric"></a>快速入门：将 Windows 容器部署到 Service Fabric

Azure Service Fabric 是一款分布式系统平台，可用于部署和管理可缩放的可靠微服务和容器。

在 Service Fabric 群集上运行 Windows 容器中的现有应用程序不需要对应用程序进行任何更改。 本快速入门介绍如何在 Service Fabric 应用程序中部署预建的 Docker 容器映像。 完成后，你会有一个正在运行的 Windows Server 2016 Nano Server 和 IIS 容器。 本快速入门介绍如何部署 Windows 容器。若要部署 Linux 容器，请阅读[此快速入门](service-fabric-quickstart-containers-linux.md)。

![IIS 默认网页][iis-default]

此快速入门介绍如何：

* 打包 Docker 映像容器
* 配置通信
* 生成并打包 Service Fabric 应用程序
* 将容器应用程序部署到 Azure

## <a name="prerequisites"></a>先决条件

* 一个 Azure 订阅（可以创建[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)）。
* 一台运行以下软件的开发计算机：
  * Visual Studio 2015 或 Visual Studio 2017。
  * [Service Fabric SDK 和工具](service-fabric-get-started.md)。

## <a name="package-a-docker-image-container-with-visual-studio"></a>使用 Visual Studio 打包 Docker 映像容器

Service Fabric SDK 和工具提供服务模板，用于将容器部署到 Service Fabric 群集。

以“管理员”身份启动 Visual Studio。  选择“文件” > “新建” > “项目”。

选择“Service Fabric 应用程序”，将其命名为“MyFirstContainer”，并单击“确定”。

从“托管的容器和应用程序”模板中选择“容器”。

在“映像名称”中输入“microsoft/iis:nanoserver”，即 [Windows Server Nano Server 和 IIS 基映像](https://hub.docker.com/r/microsoft/iis/)。

配置容器的“端口到主机”端口映射，使端口 80 上针对服务的传入请求映射到容器上的端口 80。  将“容器端口”设置为“80”并将“主机端口”设置为“80”。  

将服务命名为“MyContainerService”，然后单击“确定”。

<<<<<<< 已更新上游![“新建服务”对话框][new-service]
=======
## <a name="configure-communication-and-container-port-to-host-port-mapping"></a>配置通信和容器端口到主机端口映射

此服务需要使用终结点进行通信。  就本快速入门来说，容器化服务会在端口 80 上进行侦听。  在解决方案资源管理器中，打开 *MyFirstContainer/ApplicationPackageRoot/MyContainerServicePkg/ServiceManifest.xml*。  在 ServiceManifest.xml 文件中更新现有的 `Endpoint`，然后添加协议、端口和 URI 方案：

```xml
<Resources>
    <Endpoints>
        <Endpoint Name="MyContainerServiceTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
   </Endpoints>
</Resources>
```

提供 `UriScheme` 即可向 Service Fabric 命名服务自动注册容器终结点，确保其可以被发现。 本文末尾提供完整的 ServiceManifest.xml 示例文件。

配置容器的“端口到主机”端口映射，使端口 80 上针对服务的传入请求映射到容器上的端口 80。  在解决方案资源管理器中打开 *MyFirstContainer/ApplicationPackageRoot/ApplicationManifest.xml*，然后在 `ContainerHostPolicies` 中指定 `PortBinding` 策略。  就本快速入门来说，`ContainerPort` 为 80，`EndpointRef` 为“MyContainerServiceTypeEndpoint”（在服务清单中定义的终结点）。

```xml
<ServiceManifestImport>
...
  <ConfigOverrides />
  <Policies>
    <ContainerHostPolicies CodePackageRef="Code">
      <PortBinding ContainerPort="80" EndpointRef="MyContainerServiceTypeEndpoint"/>
    </ContainerHostPolicies>
  </Policies>
</ServiceManifestImport>
```

本文末尾提供完整的 ApplicationManifest.xml 示例文件。
>>>>>>> 隐藏的更改

## <a name="specify-the-os-build-for-your-container-image"></a>为容器映像指定 OS 版本
使用特定 Windows Server 版本生成的容器可能无法在运行不同 Windows Server 版本的主机上运行。 例如，使用 Windows Server 版本 1709 生成的容器不会在运行 Windows Server 2016 的主机上运行。 若要了解更多信息，请参阅 [Windows Server 容器 OS 与主机 OS 的兼容性](service-fabric-get-started-containers.md#windows-server-container-os-and-host-os-compatibility)。 

使用 Service Fabric 运行时版本 6.1 及更新版本，可以为每个容器指定多个 OS 映像，并使用它应该部署到的 OS 的内部版本来标记每个 OS 映像。 这有助于确保应用程序能够在运行不同版本 Windows 操作系统的主机上运行。 若要了解详细信息，请参阅[指定特定于 OS 内部版本的容器映像](service-fabric-get-started-containers.md#specify-os-build-specific-container-images)。 

Microsoft 为不同版本的 Windows Server 上生成的 IIS 版本发布不同的映像。 若要确保 Service Fabric 部署的容器与在部署应用程序的群集节点上运行的 Windows Server 版本兼容，请将以下行添加到 *ApplicationManifest.xml* 文件。 Windows Server 2016 的内部版本为 14393，Windows Server 版本 1709 的内部版本为 16299。 

```xml
    <ContainerHostPolicies CodePackageRef="Code"> 
      <ImageOverrides> 
        ...
          <Image Name="microsoft/iis:nanoserverDefault" /> 
          <Image Name= "microsoft/iis:nanoserver" Os="14393" /> 
          <Image Name="microsoft/iis:windowsservercore-1709" Os="16299" /> 
      </ImageOverrides> 
    </ContainerHostPolicies> 
```

服务清单继续为 nanoserver (`microsoft/iis:nanoserver`) 只指定一个映像。 

## <a name="create-a-cluster"></a>创建群集

若要将应用程序部署到 Azure 中的群集，可以加入合作群集。 合作群集是在 Azure 上托管的、由 Service Fabric 团队运行的免费限时 Service Fabric 群集，任何人都可以在其中部署应用程序及了解平台的情况。  该群集使用单个自签名证书来确保节点到节点和客户端到节点的安全。 合作群集支持容器。 如果决定设置并使用自己的群集，该群集必须在支持容器的 SKU（例如，包含容器的 Windows Server 2016 Datacenter）上运行。

登录并[加入 Windows 群集](http://aka.ms/tryservicefabric)。 通过单击 **PFX** 链接，将 PFX 证书下载到计算机。 单击“如何连接到安全合作群集?”链接并复制证书密码。 后续步骤中需要使用证书、证书密码和“连接终结点”值。

![PFX 和连接终结点](./media/service-fabric-quickstart-containers/party-cluster-cert.png)

> [!Note]
> 每小时可用的合作群集数目有限。 如果在尝试注册合作群集时出错，可以等待一段时间再重试，或者遵循[部署 .NET 应用](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-app-to-party-cluster#deploy-the-sample-application)教程中的步骤，在 Azure 订阅中创建一个 Service Fabric 群集并在其中部署应用程序。 通过 Visual Studio 创建的群集支持容器。 在群集中部署并验证应用程序后，可以直接跳到本快速入门中的 [Service Fabric 应用程序和服务清单的完整示例](#complete-example-service-fabric-application-and-service-manifests)。
>

在 Windows 计算机上，将 PFX 安装到 *CurrentUser\My* 证书存储中。

```powershell
PS C:\mycertificates> Import-PfxCertificate -FilePath .\party-cluster-873689604-client-cert.pfx -CertStoreLocation Cert:\CurrentUser\My -Password (ConvertTo-SecureString 873689604 -AsPlainText -Force)


  PSParentPath: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

Thumbprint                                Subject
----------                                -------
3B138D84C077C292579BA35E4410634E164075CD  CN=zwin7fh14scd.westus.cloudapp.azure.com
<<<<<<< Updated upstream
``` 
=======
```

Remember the thumbprint for the following step.
>>>>>>> Stashed changes

## Deploy the application to Azure using Visual Studio

Now that the application is ready, you can deploy it to a cluster directly from Visual Studio.

Right-click **MyFirstContainer** in the Solution Explorer and choose **Publish**. The Publish dialog appears.

<<<<<<< Updated upstream
Copy the **Connection Endpoint** from the Party cluster page into the **Connection Endpoint** field. For example, `zwin7fh14scd.westus.cloudapp.azure.com:19000`. 
=======
Copy the **Connection Endpoint** from the Party cluster page into the **Connection Endpoint** field. For example, `zwin7fh14scd.westus.cloudapp.azure.com:19000`. Click **Advanced Connection Parameters** and verify the connection parameter information.  *FindValue* and *ServerCertThumbprint* values must match the thumbprint of the certificate installed in the previous step.

![Publish Dialog](./media/service-fabric-quickstart-containers/publish-app.png)
>>>>>>> Stashed changes

Click **Publish**.

Each application in the cluster must have a unique name.  Party clusters are a public, shared environment however and there may be a conflict with an existing application.  If there is a name conflict, rename the Visual Studio project and deploy again.

Open a browser and navigate to the **Connection endpoint** specified in the Party cluster page. You can optionally prepend the scheme identifier, `http://`, and append the port, `:80`, to the URL. For example, http://zwin7fh14scd.westus.cloudapp.azure.com:80. You should see the IIS default web page:
![IIS default web page][iis-default]

<<<<<<< Updated upstream
=======
## Complete example Service Fabric application and service manifests

Here are the complete service and application manifests used in this quickstart.

### ServiceManifest.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="MyContainerServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="MyContainerServiceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>microsoft/iis:nanoserver</ImageName>
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
      <Endpoint Name="MyContainerServiceTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
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
    <Parameter Name="MyContainerService_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyContainerServicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <PortBinding ContainerPort="80" EndpointRef="MyContainerServiceTypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="MyContainerService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="MyContainerServiceType" InstanceCount="[MyContainerService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

>>>>>>> 隐藏的更改
## <a name="next-steps"></a>后续步骤

在此快速入门中，读者学习了如何：

* 打包 Docker 映像容器
* 配置通信
* 生成并打包 Service Fabric 应用程序
* 将容器应用程序部署到 Azure

若要详细了解如何在 Service Fabric 中使用 Windows 容器，请继续学习适用于 Windows 容器应用的教程。

> [!div class="nextstepaction"]
> [创建 Windows 容器应用](./service-fabric-host-app-in-a-container.md)

[iis-default]: ./media/service-fabric-quickstart-containers/iis-default.png
[publish-dialog]: ./media/service-fabric-quickstart-containers/publish-dialog.png
[new-service]: ./media/service-fabric-quickstart-containers/NewService.png
