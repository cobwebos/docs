<properties
	pageTitle="使用 PowerShell 自动管理 Service Fabric 应用程序 | Microsoft Azure"
	description="使用 PowerShell 部署、升级、测试和删除 Service Fabric 应用程序。"
	services="service-fabric"
	documentationCenter=".net"
	authors="rwike77"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-fabric"
	ms.date="12/14/2015"
	wacn.date=""/>

# 使用 PowerShell 部署、升级、测试和删除 Service Fabric 应用程序

本文说明如何使用 PowerShell 来自动完成部署、升级、删除和测试 Azure Service Fabric 应用程序的常见任务。

## 先决条件

在继续进行本文中的任务之前，请务必先[安装运行时、SDK 和工具](/documentation/articles/service-fabric-get-started)，这同时会安装 **ServiceFabric** 和 **ServiceFabricTestability** PowerShell 模块。[启用 PowerShell 脚本执行](/documentation/articles/service-fabric-get-started#enable-powershell-script-execution)，同时[安装并启动本地群集](/documentation/articles/service-fabric-get-started#install-and-start-a-local-cluster)，以运行本文中的示例。



在运行本文中的任何 PowerShell 命令之前，请先使用 [**Connect-ServiceFabricCluster**](https://msdn.microsoft.com/zh-cn/library/azure/mt125938.aspx) 连接到本地 Service Fabric 群集：

```powershell
Connect-ServiceFabricCluster localhost:19000
```

## 任务：部署 Service Fabric 应用程序

在构建应用程序并打包应用程序类型之后，可以将应用程序部署到本地 Service Fabric 群集。首先，在 Visual Studio 中打包 WordCount 应用程序，方法是右键单击解决方案资源管理器中的“WordCount”，然后选择“打包”。有关服务、应用程序清单与包布局的详细信息，请参阅[在 Service Fabric 中对应用程序建模](/documentation/articles/service-fabric-application-model)。部署过程包括上载应用程序包、注册应用程序类型，以及创建应用程序实例。使用本部分中的说明将新应用程序部署到群集。

### 步骤 1：上载应用程序包
将应用程序包上载到映像存储会将其放在一个可由内部 Service Fabric 组件访问的位置。应用程序包包含所需的应用程序清单、服务清单，以及用于创建应用程序和服务实例的代码/配置/数据包。[**Copy-ServiceFabricApplicationPackage**](https://msdn.microsoft.com/zh-cn/library/azure/mt125905.aspx) 命令会上载包。例如：

```powershell
Copy-ServiceFabricApplicationPackage C:\ServiceFabricSamples\Services\WordCount\WordCount\pkg\Debug -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCount
```

### 步骤 2：注册应用程序类型
注册应用程序包，使应用程序清单中声明的应用程序类型和版本可供使用。系统将读取上一步中上载的程序包，验证此包（等效于本地运行 [**Test-ServiceFabricApplicationPackage**](https://msdn.microsoft.com/zh-cn/library/azure/mt125950.aspx)），处理包的内容，并将已处理的包复制到内部系统位置。运行 [**Register-ServiceFabricApplicationType**](https://msdn.microsoft.com/zh-cn/library/azure/mt125958.aspx) cmdlet：

```powershell
Register-ServiceFabricApplicationType WordCount
```
若要查看在群集中注册的应用程序类型，运行 cmdlet：

```powershell
Get-ServiceFabricApplicationType
```

### 步骤 3：创建应用程序实例
可以使用已使用 [**New-ServiceFabricApplication**](https://msdn.microsoft.com/zh-cn/library/azure/mt125913.aspx) 命令成功注册的任何应用程序类型版本实例化应用程序。每个应用程序的名称必须以 **fabric:** 方案开头，并且对每个应用程序实例是唯一的。应用程序类型名称和应用程序类型版本在 **ApplicationManifest.xml** 文件中声明。如果目标应用程序类型的应用程序清单中定义有任何默认服务，则此时将还创建那些服务。

```powershell
New-ServiceFabricApplication fabric:/WordCount WordCount 1.0.0
```

[**Get-ServiceFabricApplication**](https://msdn.microsoft.com/zh-cn/library/azure/mt163515.aspx) 命令将列出已成功创建的所有应用程序实例以及其整体状态。[**Get-ServiceFabricService**](https://msdn.microsoft.com/zh-cn/library/azure/mt125889.aspx) 命令将列出在给定的应用程序实例中成功创建的所有服务实例。将列出默认服务（如果有）。

```powershell
Get-ServiceFabricApplication

Get-ServiceFabricApplication | Get-ServiceFabricService
```

## 任务：升级 Service Fabric 应用程序

可以升级以前部署的 Service Fabric 应用程序。此任务将升级以前在“任务：部署 Service Fabric 应用程序”中部署的 WordCount 应用程序。 有关详细信息，请参阅 [Service Fabric 应用程序升级](/documentation/articles/service-fabric-application-upgrade)。

### 步骤 1：更新应用程序

更改 WordCount 服务中的代码。

更新服务代码后，需要递增 **ServiceManifest.xml** 文件（位于 WordCount 项目的 **PackageRoot** 目录）中的服务版本号。查找清单的 **CodePackage** 元素，并将服务版本更改为 2.0.0。ServiceManifest.xml 文件中的相应代码行应如下所示：

```xml
<ServiceManifest Name="WordCountServicePkg" Version="2.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <StatefulServiceType ServiceTypeName="WordCountServiceType" HasPersistedState="true" />
  </ServiceTypes>
  <CodePackage Name="Code" Version="2.0.0">
	  ...
```

现在，需要更新 ApplicationManifest.xml 文件（可于 WordCount 解决方案下的 WordCount 应用程序项目中找到）。更新 **ServiceManifestRef** 元素以使用 2.0.0.0 版 **WordCountServicePkg** 项目。同时将 **ApplicationTypeVersion** 从 1.0.0.0 更新为 2.0.0.0。ApplicationManifest.xml 中的相应代码行应如下所示：

```xml
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="WordCount" ApplicationTypeVersion="2.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
...
<ServiceManifestRef ServiceManifestName="WordCountServicePkg" ServiceManifestVersion="2.0.0" />
```

进行这些更改之后，保存文件并重建 WordCount 项目。现在，请右键单击 WordCount 项目，然后选择“打包”，以打包更新的应用程序。这将创建可部署的应用程序包。更新的应用程序现在准备就绪，可供部署。

### 步骤 2：复制并注册已更新的应用程序包

现已构建并打包应用程序，可以将它升级。如果你以管理员身分打开 PowerShell 窗口并键入 [**Get-ServiceFabricApplication**](https://msdn.microsoft.com/zh-cn/library/azure/mt163515.aspx)，应会看到已部署 1.0.0 版的 WordCount 应用程序类型。对于 WordCount 示例，应用程序包可在以下位置找到：*C:\\ServiceFabricSamples\\Services\\WordCount\\WordCount\\pkg\\Debug*。

现在，将更新的应用程序包复制到 Service Fabric 映像存储（Service Fabric 存储应用程序包的位置）。参数 **ApplicationPackagePathInImageStore** 告知 Service Fabric 可在何处找到应用程序包。以下命令将应用程序包复制到映像存储中的 **WordCountV2**：

```powershell
Copy-ServiceFabricApplicationPackage C:\ServiceFabricSamples\Services\WordCount\WordCount\pkg\Debug -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCountV2
```

下一步是将新版本的应用程序注册到 Service Fabric，这可以使用 [**Register-ServiceFabricApplicationType**](https://msdn.microsoft.com/zh-cn/library/azure/mt125958.aspx) cmdlet 来执行：

```powershell
Register-ServiceFabricApplicationType WordCountV2
```

如果此命令不成功，你可能需要如步骤 1 中所述重建服务。

### 步骤 3：开始升级
可将各种升级参数、超时和运行状况标准应用到应用程序升级。若要了解详细信息，请参阅[应用程序升级参数](/documentation/articles/service-fabric-application-upgrade-parameters)和[升级过程](/documentation/articles/service-fabric-application-upgrade)文档。在本演练中，请将服务运行状况评估标准设置为默认值（和建议值）。所有服务和实例在升级之后都应该_运行正常_。但是，你应该将 **HealthCheckStableDuration** 增加到 60 秒（这样该服务在进行下一个升级域的升级之前将至少保持 20 秒的运行状况正常）。同时，请将 **UpgradeDomainTimeout** 设置为 1200 秒，将 **UpgradeTimeout** 设置为 3000 秒。最后，将 **UpgradeFailureAction** 设置为 **rollback**，以便在升级期间遇到任何错误时，请求 Service Fabric 将应用程序回滚到前一版本。

现在可以使用 [**Start-ServiceFabricApplicationUpgrade**](https://msdn.microsoft.com/zh-cn/library/azure/mt125975.aspx) cmdlet 开始应用程序升级：

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/WordCount -ApplicationTypeVersion 2.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000  -FailureAction Rollback -Monitored
```

请注意，应用程序名称与以前部署的 v1.0.0 应用程序名称相同 (fabric:/WordCount)。Service Fabric 使用此名称来确定升级的应用程序。如果设置的超时太短，则可能遇到一条说明该问题的超时失败消息。请参阅[应用程序升级故障排除](/documentation/articles/service-fabric-application-upgrade-troubleshooting)，或增大超时。

可以使用 [Service Fabric 资源管理器](/documentation/articles/service-fabric-visualizing-your-cluster)或 [**Get-ServiceFabricApplicationUpgrade**](https://msdn.microsoft.com/zh-cn/library/azure/mt125988.aspx) cmdlet 来监视应用程序升级的进度：

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/WordCount
```

几分钟后，[Get-ServiceFabricApplicationUpgrade](https://msdn.microsoft.com/zh-cn/library/azure/mt125988.aspx) cmdlet 应会显示已升级所有升级域（已完成）。

## 任务：测试 Service Fabric 应用程序

若要编写高质量的服务，开发人员需要能够引入不可靠的基础结构故障来测试其服务的稳定性。Service Fabric 使开发人员能够引入故障操作，并使用混沌和故障转移测试方案来测试故障情况下服务的运行情况。请参阅[可测试性概述](/documentation/articles/service-fabric-testability-overview)以获取详细信息。

### 步骤 1：运行混沌测试方案
混沌测试方案跨整个 Service Fabric 群集生成故障。一般而言，该方案将几个月或几年经历的故障压缩到几小时。各种交叉故障的组合并具有高故障率，能够找出很有可能被忽视的极端状况。以下示例运行了 60 分钟的混沌测试方案。

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```

### 步骤 2：运行故障转移测试方案
故障转移测试方案针对特定的服务分区，而不是整个群集，其他服务不受影响。该方案将遍历一系列的模块故障和服务验证，同时你的业务逻辑将保持运行。服务验证失败指出存在需要进一步调查的问题。故障转移测试一次只引入一个故障，而不像混沌测试方案那样引入多个故障。以下示例针对 fabric:/WordCount/WordCountService 服务运行故障转移测试 60 分钟。

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/WordCount/WordCountService"

Connect-ServiceFabricCluster

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindUniformInt64 -PartitionKey 1
```

## 任务：删除 Service Fabric 应用程序
你可以删除已部署的应用程序的实例、从群集中删除预配的应用程序类型，以及从 ImageStore 中删除应用程序包。

### 步骤 1：删除应用程序实例
当你不再需要某个应用程序实例时，可以使用 [**Remove-ServiceFabricApplication**](https://msdn.microsoft.com/zh-cn/library/azure/mt125914.aspx) cmdlet 将其永久删除。这也会将自动删除属于该应用程序的所有服务，永久删除所有服务状态。此操作无法撤消，并且无法恢复应用程序状态。

```powershell
Remove-ServiceFabricApplication fabric:/WordCount
```

### 步骤 2：注销应用程序类型
当你不再需要特定的应用程序类型版本时，可以使用 [**Unregister-ServiceFabricApplicationType**](https://msdn.microsoft.com/zh-cn/library/azure/mt125885.aspx) cmdlet 将它注销。注销未使用的类型将在映像存储中释放该应用程序包使用的存储空间。只要没有针对其实例化的应用程序或引用它的挂起应用程序升级，就可以注销应用程序类型。

```powershell
Unregister-ServiceFabricApplicationType WordCount 1.0.0
```

### 步骤 3：删除应用程序包
在注销应用程序类型后，可以使用 [**Remove-ServiceFabricApplicationPackage**](https://msdn.microsoft.com/zh-cn/library/azure/mt163532.aspx) cmdlet 从映像存储中删除应用程序包。

```powershell
Remove-ServiceFabricApplicationPackage -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCount
```

## 后续步骤
[Service Fabric 应用程序生命周期](/documentation/articles/service-fabric-application-lifecycle)

[部署应用程序](/documentation/articles/service-fabric-deploy-remove-applications)

[应用程序升级](/documentation/articles/service-fabric-application-upgrade)

[Azure Service Fabric cmdlet](https://msdn.microsoft.com/zh-cn/library/azure/mt125965.aspx)

[Azure Service Fabric 可测试性 cmdlet](https://msdn.microsoft.com/zh-cn/library/azure/mt125844.aspx)

<!---HONumber=Mooncake_0307_2016-->