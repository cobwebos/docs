---
title: "Azure Service Fabric 应用程序部署 | Microsoft Docs"
description: "使用 FabricClient API 部署和删除 Service Fabric 中的应用程序。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: b120ffbf-f1e3-4b26-a492-347c29f8f66b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/07/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: ebe8b9f0cace419125bde84a9ff2a912af061156
ms.contentlocale: zh-cn
ms.lasthandoff: 07/08/2017

---
# <a name="deploy-and-remove-applications-using-fabricclient"></a>使用 FabricClient 部署和删除应用程序
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Visual Studio](service-fabric-publish-app-remote-cluster.md)
> * [FabricClient API](service-fabric-deploy-remove-applications-fabricclient.md)
> 
> 

<br/>

[打包应用程序类型][10]后，即可部署到 Azure Service Fabric 群集中。 部署涉及以下三个步骤：

1. 将应用程序包上传到映像存储
2. 注册应用程序类型
3. 创建应用程序实例

在部署应用程序并且实例在群集中运行后，可以删除应用程序实例及其应用程序类型。 从群集中完全删除某个应用程序涉及以下步骤：

1. 删除正在运行的应用程序实例
2. 如果不再需要该应用程序类型，则将其取消注册
3. 从映像存储中删除应用程序包

如果使用 [Visual Studio 来部署和调试](service-fabric-publish-app-remote-cluster.md) 本地开发群集上的应用程序，则将通过 PowerShell 脚本自动处理上述所有步骤。  可在应用程序项目的 *Scripts* 文件夹中找到此脚本。 本文提供了有关这些脚本正在执行什么操作的背景，以便可以在 Visual Studio 外部执行相同的操作。 
 
## <a name="connect-to-the-cluster"></a>连接至群集
在运行本文中的任何代码示例之前，通过创建 [FabricClient](/dotnet/api/system.fabric.fabricclient) 实例连接到群集。 有关连接到本地开发群集、远程群集，或使用 Azure Active Directory、X509 证书或 Windows Active Directory 保护的群集的示例，请参阅[连接到安全群集](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-the-fabricclient-apis)。 若要连接到本地部署群集，请运行以下命令：

```csharp
// Connect to the local cluster.
FabricClient fabricClient = new FabricClient();
```

## <a name="upload-the-application-package"></a>上传应用程序包
假设在 Visual Studio 中生成并打包名为 *MyApplication* 的应用程序。 默认情况下，ApplicationManifest.xml 中列出的应用程序类型名称为“MyApplicationType”。  应用程序包（其中包含必需的应用程序清单、服务清单以及代码/配置/数据包）位于 *C:\Users\&lt;username&gt;\Documents\Visual Studio 2017\Projects\MyApplication\MyApplication\pkg\Debug* 中。

上传应用程序包会将其放在一个可由内部 Service Fabric 组件访问的位置。 Service Fabric 在注册应用程序包期间会对应用程序包进行验证。 但是，如果要在本地验证应用程序包（例如，在上传之前），请使用 [Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) cmdlet。

[CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) API 可将应用程序包上传到群集映像存储。 

如果应用程序包很大和/或包含多个文件，可使用 PowerShell [将其压缩](service-fabric-package-apps.md#compress-a-package)并复制到映像存储。 压缩可以减小文件大小，减少文件数量。

有关映像存储和映像存储连接字符串的补充信息，请参阅[了解映像存储连接字符串](service-fabric-image-store-connection-string.md)。

## <a name="register-the-application-package"></a>注册应用程序包
应用程序清单中声明的应用程序类型和版本会在注册应用程序包时可供使用。 系统将读取上一步中上传的程序包，验证此包，处理包的内容，并将已处理的包复制到内部系统位置。  

[ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) API 可在群集中注册应用程序并使其可供部署。

[GetApplicationTypeListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationtypelistasync) API 提供有关所有已成功注册的应用程序类型的信息。 可以使用此 API 来确定注册的完成时间。

## <a name="create-an-application-instance"></a>创建应用程序实例
可以使用 [CreateApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.createapplicationasync) API 通过已成功注册的任何应用程序类型来实例化应用程序。 每个应用程序的名称必须以“fabric:”方案开头，并且必须对每个应用程序实例是唯一的（在群集中）。 还会创建目标应用程序类型的应用程序清单中定义的任何默认服务。

可以为已注册应用程序类型的任何给定版本创建多个应用程序实例。 每个应用程序实例都将隔离运行，具有其自己的工作目录和进程集。

若要查看哪些已命名应用和服务正在群集中运行，请运行 [GetApplicationListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync) 和 [GetServiceListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync) API。

## <a name="create-a-service-instance"></a>创建服务实例
可以使用 [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) API 通过服务类型实例化服务。  如果该服务被声明为应用程序清单中的默认服务，则该服务在应用程序实例化时实例化。  针对已实例化的服务调用 [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) API 将返回类型为 FabricException 的异常，其中包含值为 FabricErrorCode.ServiceAlreadyExists 的错误代码。

## <a name="remove-a-service-instance"></a>删除服务实例
当不再需要某个服务实例时，可以通过调用 [DeleteServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync) API 从正在运行的应用程序实例中将其删除。  

> [!WARNING]
> 此操作无法撤消，并且无法恢复服务状态。

## <a name="remove-an-application-instance"></a>删除应用程序实例
当不再需要某个应用程序实例时，可以使用 [DeleteApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync) API 通过指定其名称将其永久删除。 [DeleteApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync) 也会自动删除属于该应用程序的所有服务，永久删除所有服务状态。

> [!WARNING]
> 此操作无法撤消，并且无法恢复应用程序状态。

## <a name="unregister-an-application-type"></a>取消注册应用程序类型
当不再需要应用程序类型的某个特定版本时，应使用 [Unregister-ServiceFabricApplicationType](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.unprovisionapplicationasync) API 取消注册该应用程序类型的该特定版本。 取消注册未使用的应用程序类型版本将释放映像存储使用的存储空间。 只要不存在已根据应用程序类型的某个版本进行实例化的应用程序，并且没有挂起的应用程序升级在引用应用程序类型的该版本，就可以取消注册应用程序类型的该版本。

## <a name="remove-an-application-package-from-the-image-store"></a>从映像存储中删除应用程序包
当不再需要某个应用程序包时，可以使用 [RemoveApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.removeapplicationpackage) API 将其从映像存储中删除，释放系统资源。

## <a name="troubleshooting"></a>故障排除
### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Copy-ServiceFabricApplicationPackage 请求 ImageStoreConnectionString
Service Fabric SDK 环境应该已经设置了正确的默认设置。 若有需要，所有命令的 ImageStoreConnectionString 都应匹配 Service Fabric 群集正在使用的值。 可以在使用 [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest?view=azureservicefabricps) 和 Get-ImageStoreConnectionStringFromClusterManifest 命令检索到的群集清单中找到 ImageStoreConnectionString：

```powershell
PS C:\> Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)
```

Service Fabric SDK PowerShell 模块中包含的 **Get-ImageStoreConnectionStringFromClusterManifest** cmdlet，用于获取映像存储连接字符串。  要导入 SDK 模块，请运行：

```powershell
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```


ImageStoreConnectionString 可在群集清单中找到：

```xml
<ClusterManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]
```

有关映像存储和映像存储连接字符串的补充信息，请参阅[了解映像存储连接字符串](service-fabric-image-store-connection-string.md)。

### <a name="deploy-large-application-package"></a>部署大型应用程序包
问题：对于大型（GB 级别的）应用程序包，[CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) API 方法超时。
请尝试：
- 通过 `timeout` 参数为 [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) 方法指定更长的超时时间。 此超时默认为 30 分钟。
- 检查源计算机和群集之间的网络连接。 如果连接缓慢，请考虑使用一台网络连接状况更好的计算机。
如果客户端计算机位于另一个区域，而不在此群集中，请考虑使用此群集的邻近区域或同区域中的客户端计算机。
- 检查是否已达到外部限制。 例如，将映像存储配置为使用 Azure 存储时，可能会限制上传。

问题：上传包已成功完成，但 [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) API 超时。请尝试：
- 复制到映像存储之前[对包进行压缩](service-fabric-package-apps.md#compress-a-package)。
压缩可减小文件大小，减少文件数量，这反过来会减少通信流量和 Service Fabric 必须执行的工作量。 上传操作可能会变慢（尤其是包括压缩时间时），但注册和注销应用程序类型会加快。
- 通过 `timeout` 参数为 [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) API 指定更长的超时时间。

### <a name="deploy-application-package-with-many-files"></a>部署包含多个文件的应用程序包
问题：具有多个文件（上千个）的应用程序包的[ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) 超时。
请尝试：
- 复制到映像存储之前[对包进行压缩](service-fabric-package-apps.md#compress-a-package)。 压缩可以减少文件数量。
- 通过 `timeout` 参数为 [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) 指定更长的超时时间。

## <a name="code-example"></a>代码示例
下面的示例将应用程序包复制到映像存储中、预配应用程序类型、创建应用程序实例、创建服务实例、删除应用程序实例、取消预配应用程序类型，以及从映像存储中删除应用程序包。

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Reflection;
using System.Threading.Tasks;

using System.Fabric;
using System.Fabric.Description;
using System.Threading;

namespace ServiceFabricAppLifecycle
{
class Program
{
static void Main(string[] args)
{

    string clusterConnection = "localhost:19000";
    string appName = "fabric:/MyApplication";
    string appType = "MyApplicationType";
    string appVersion = "1.0.0";
    string serviceName = "fabric:/MyApplication/Stateless1";
    string imageStoreConnectionString = "file:C:\\SfDevCluster\\Data\\ImageStoreShare";
    string packagePathInImageStore = "MyApplication";
    string packagePath = "C:\\Users\\username\\Documents\\Visual Studio 2017\\Projects\\MyApplication\\MyApplication\\pkg\\Debug";
    string serviceType = "Stateless1Type";

    // Connect to the cluster.
    FabricClient fabricClient = new FabricClient(clusterConnection);

    // Copy the application package to a location in the image store
    try
    {
        fabricClient.ApplicationManager.CopyApplicationPackage(imageStoreConnectionString, packagePath, packagePathInImageStore);
        Console.WriteLine("Application package copied to {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Application package copy to Image Store failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Provision the application.  "MyApplicationV1" is the folder in the image store where the application package is located. 
    // The application type with name "MyApplicationType" and version "1.0.0" (both are found in the application manifest) 
    // is now registered in the cluster.            
    try
    {
        fabricClient.ApplicationManager.ProvisionApplicationAsync(packagePathInImageStore).Wait();

        Console.WriteLine("Provisioned application type {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Provision Application Type failed:");

        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    //  Create the application instance.
    try
    {
        ApplicationDescription appDesc = new ApplicationDescription(new Uri(appName), appType, appVersion);
        fabricClient.ApplicationManager.CreateApplicationAsync(appDesc).Wait();
        Console.WriteLine("Created application instance of type {0}, version {1}", appType, appVersion);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("CreateApplication failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Create the stateless service description.  For stateful services, use a StatefulServiceDescription object.
    StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
    serviceDescription.ApplicationName = new Uri(appName);
    serviceDescription.InstanceCount = 1;
    serviceDescription.PartitionSchemeDescription = new SingletonPartitionSchemeDescription();
    serviceDescription.ServiceName = new Uri(serviceName);
    serviceDescription.ServiceTypeName = serviceType;

    // Create the service instance.  If the service is declared as a default service in the ApplicationManifest.xml,
    // the service instance is already running and this call will fail.
    try
    {
        fabricClient.ServiceManager.CreateServiceAsync(serviceDescription).Wait();
        Console.WriteLine("Created service instance {0}", serviceName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("CreateService failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete a service instance.
    try
    {
        DeleteServiceDescription deleteServiceDescription = new DeleteServiceDescription(new Uri(serviceName));

        fabricClient.ServiceManager.DeleteServiceAsync(deleteServiceDescription);
        Console.WriteLine("Deleted service instance {0}", serviceName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("DeleteService failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete an application instance from the application type.
    try
    {
        DeleteApplicationDescription deleteApplicationDescription = new DeleteApplicationDescription(new Uri(appName));

        fabricClient.ApplicationManager.DeleteApplicationAsync(deleteApplicationDescription).Wait();
        Console.WriteLine("Deleted application instance {0}", appName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("DeleteApplication failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Un-provision the application type.
    try
    {
        fabricClient.ApplicationManager.UnprovisionApplicationAsync(appType, appVersion).Wait();
        Console.WriteLine("Un-provisioned application type {0}, version {1}", appType, appVersion);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Un-provision application type failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete the application package from a location in the image store.
    try
    {
        fabricClient.ApplicationManager.RemoveApplicationPackage(imageStoreConnectionString, packagePathInImageStore);
        Console.WriteLine("Application package removed from {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Application package removal from Image Store failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    Console.WriteLine("Hit enter...");
    Console.Read();
}        
}
}

```

## <a name="next-steps"></a>后续步骤
[Service Fabric 应用程序升级](service-fabric-application-upgrade.md)

[Service Fabric 运行状况简介](service-fabric-health-introduction.md)

[对 Service Fabric 进行诊断和故障排除](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[在 Service Fabric 中对应用程序建模](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-application-model.md
[11]: service-fabric-application-upgrade.md

