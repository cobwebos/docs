---
title: "如何容器化 Azure Service Fabric 微服务（预览版）"
description: "Azure Service Fabric 已添加用于容器化 Service Fabric 微服务的新功能。 此功能目前处于预览状态。"
services: service-fabric
documentationcenter: .net
author: anmolah
manager: anmolah
editor: anmolah
ms.assetid: 0b41efb3-4063-4600-89f5-b077ea81fa3a
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/04/2017
ms.author: anmola
ms.translationtype: HT
ms.sourcegitcommit: a9cfd6052b58fe7a800f1b58113aec47a74095e3
ms.openlocfilehash: 6f8ad0bad8d1ae861e6b72f7e1a32ab0675813c2
ms.contentlocale: zh-cn
ms.lasthandoff: 08/12/2017

---
# <a name="how-to-containerize-your-service-fabric-reliable-services-and-reliable-actors-preview"></a>如何容器化 Service Fabric Reliable Services 和 Reliable Actors（预览版）

Service Fabric 支持容器化 Service Fabric 微服务（基于 Reliable Services 和 Reliable Actor 的服务）。 有关详细信息，请参阅 [Service Fabric 容器](service-fabric-containers-overview.md)。


 此功能处于预览状态，本文提供各种步骤获取在容器内运行的服务。  

> [!NOTE]
> 此功能处于预览状态，在生产环境中不受支持。 当前此功能仅适用于 Windows。

## <a name="steps-to-containerize-your-service-fabric-application"></a>容器化 Service Fabric 应用程序的步骤

1. 在 Visual Studio 中打开 Service Fabric 应用程序。

2. 将 [SFBinaryLoader.cs](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/code/SFBinaryLoaderForContainers/SFBinaryLoader.cs) 类添加到项目。 此类中的代码在容器中运行时，是用于在应用程序中正确加载 Service Fabric 运行时二进制文件的帮助程序。

3. 对于要容器化的每个代码包，在程序入口点初始化加载程序。 将以下代码片段中所示的静态构造函数添加到程序入口点文件中。

  ```csharp
  namespace MyApplication
  {
      internal static class Program
      {
          static Program()
          {
              SFBinaryLoader.Initialize();
          }

          /// <summary>
          /// This is the entry point of the service host process.
          /// </summary>
          private static void Main()
          {
  ```

4. 生成并[打包](service-fabric-package-apps.md#Package-App)项目。 若要生成并创建包，请在解决方案资源管理器中右键单击应用程序项目，选择“包”命令。

5. 对于每个需要容器化的代码包，运行 PowerShell 脚本 [CreateDockerPackage.ps1](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/scripts/CodePackageToDockerPackage/CreateDockerPackage.ps1)。 用法如下：
  ```powershell
    $codePackagePath = 'Path to the code package to containerize.'
    $dockerPackageOutputDirectoryPath = 'Output path for the generated docker folder.'
    $applicationExeName = 'Name of the ode package executable.'
    CreateDockerPackage.ps1 -CodePackageDirectoryPath $codePackagePath -DockerPackageOutputDirectoryPath $dockerPackageOutputDirectoryPath -ApplicationExeName $applicationExeName
 ```
  该脚本使用位于 $dockerPackageOutputDirectoryPath 的 Docker 项目创建文件夹。 修改生成的 Dockerfile 公开所有端口，根据需要运行安装程序脚本等。

6. 接下来需要[生成](service-fabric-get-started-containers.md#Build-Containers) Docker 容器包并将其[推送](service-fabric-get-started-containers.md#Push-Containers)到存储库。

7. 修改 ApplicationManifest.xml 和 ServiceManifest.xml，添加容器映像、存储库信息、注册表身份验证和端口到主机映射。 有关修改清单的信息，请参阅[创建 Azure Service Fabric 容器应用程序](service-fabric-get-started-containers.md)。 服务清单中的代码包定义需要替换为相应的容器映像。 请确保将入口点更改为 ContainerHost 类型。

  ```xml
<!-- Code package is your service executable. -->
<CodePackage Name="Code" Version="1.0.0">
  <EntryPoint>
    <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
    <ContainerHost>
      <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
    </ContainerHost>
  </EntryPoint>
  <!-- Pass environment variables to your container: -->    
</CodePackage>
  ```

8. 为复制器和服务终结点添加端口到主机映射。 由于 Service Fabric 在运行时分配这两个端口，因此 ContainerPort 会设置为零，将分配的端口用于映射。

 ```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="0" EndpointRef="ServiceEndpoint"/>
    <PortBinding ContainerPort="0" EndpointRef="ReplicatorEndpoint"/>
  </ContainerHostPolicies>
</Policies>
 ```

9. 若要测试此应用程序，需要将其部署到正在运行版本 5.7 或更高版本的群集。 此外，需要编辑和更新群集设置，启用此预览版功能。 请按照[本文](service-fabric-cluster-fabric-settings.md)中的步骤操作，添加下一步所示的设置。
```
      {
        "name": "Hosting",
        "parameters": [
          {
            "name": "FabricContainerAppsEnabled",
            "value": "true"
          }
        ]
      }
```
10. 接下来，将已编辑的应用程序包[部署](service-fabric-deploy-remove-applications.md)到此群集。

现在即具有运行群集的容器化 Service Fabric 应用程序。

## <a name="next-steps"></a>后续步骤
* 详细了解如何运行 [Service Fabric 上的容器](service-fabric-get-started-containers.md)。
* 了解 Service Fabric [应用程序生命周期](service-fabric-application-lifecycle.md)。

