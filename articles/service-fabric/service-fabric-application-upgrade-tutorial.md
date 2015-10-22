<properties
    pageTitle="Service Fabric 应用程序升级教程"
    description="本文讲述 Service Fabric 应用程序的升级体验。"
    services="service-fabric"
    documentationCenter=".net"
    authors="mani-ramaswamy"
    manager="samgeo"
    editor=""/>

<tags
    ms.service="service-fabric"
    ms.date="07/17/2015"
    wacn.date=""/>



# 应用程序升级教程

最常用的推荐升级方法是受监控的滚动升级。Service Fabric 基于一组运行状况策略监控正在升级的应用程序的运行状况。当升级域 (UD) 中的应用程序已升级时，Service Fabric 评估应用程序运行状况，并确定是转到下一个升级域还是基于运行状况策略判定升级失败。可使用托管或本机 API、PowerShell 或 REST 执行应用程序的受监控升级。

Service Fabric 监控的滚动升级可让应用程序管理员配置 Service Fabric 用于确定应用程序运行状况是否正常的运行状况评估策略。此外，它还可让管理员配置运行状况评估失败时要执行的操作，如自动回滚。本部分介绍了一个 SDK 示例的受监控升级。

## 步骤 1：构建和部署视觉对象示例

通过在 Visual Studio 中打开项目、右键单击“解决方案”、在 Service Fabric 菜单项中选择部署命令，完成这些步骤。有关详细信息，请参阅[在 Visual Studio 中管理 Service Fabric 应用程序](documentation/articles/service-fabric-manage-application-in-visual-studio)。或者可以使用 PowerShell。

> [AZURE.NOTE]要在 PowerShell 中使用任何 Service Fabric 命令，必须先使用 `Connect-ServiceFabricCluster` cmdlet 连接到群集。同样，假定已在本地计算机上设置了群集。请参阅[设置 Service Fabric 部署环境](documentation/articles/service-fabric-get-started)上的文章。

在 Visual Studio 中生成项目后，可以使用 PowerShell 命令 **Copy-ServiceFabricApplicationPackage** 将应用程序包复制到 ImageStore，然后使用 **Register-ServiceFabricApplicationPackage** cmdlet 将应用程序注册到 Service Fabric 运行时，最后使用 **New-ServiceFabricApplication** cmdlet 启动应用程序的实例。这三个步骤类似于使用 Visual Studio 中的“部署”菜单项。

现在可以使用 [Service Fabric 资源管理器查看群集和应用程序](documentation/articles/service-fabric-visualizing-your-cluster)。应用程序具有一个 Web 服务，可通过在 Internet Explorer 地址栏中键入 [http://localhost:80](http://localhost:80) 导航到该 Web 服务。你应在屏幕上看到一些四处移动的浮动视觉对象。此外，可使用 **Get-ServiceFabricApplication** 检查应用程序状态。

## 步骤 2：更新视觉对象示例

你可能会注意到，使用步骤 1 中部署的版本，视觉对象不会旋转。让我们将此应用程序升级到视觉对象会旋转的版本。

选择 VisualObjects 解决方案中的 VisualObjects.DataService 项目，然后打开该项目中的 VisualObjects.cs 文件。在该文件内部，导航到方法 `CreateMovedObject`，并找到在其中设置旋转视觉对象的行（搜索 `CreateMovedObject` 方法中的以下行）。注释禁止行 `nextObject.Rotation = 1`，并取消注释它下面的一行。更改后的代码应如下所示：

```c#
            //nextObject.Rotation = 1;

            // Once that's deployed, uncomment this line and upgrade the application:

             nextObject.Rotation = (nextObject.Rotation + 10) % 360;
```

我们还需要更新 **VisualObjects.DataService** 项目的 *ServiceManifest.xml* 文件（位于 PackageRoot 下）。将 *CodePackage* 和服务版本更新到 2.0，*ServiceManifest.xml* 文件中的相应行应如下所示（突出显示的部分显示更改）：

```xml
<ServiceManifestName="VisualObjects.DataService"Version="2.0"xmlns="http://schemas.microsoft.com/2011/01/fabric"xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code"Version="2.0">
```

现在，我们需要将 *ApplicationManifest.xml* 文件（位于 **VisualObjects** 解决方案下的 **VisualObjects** 项目下）更新为使用 **VisualObjects.DataService** 项目的 2.0 版，并将应用程序从 1.0.0.0 更新到 2.0.0.0。现在，*ApplicationManifest.xml* 中的相应行应如下所示：

```xml
<ApplicationManifestxmlns:xsd="http://www.w3.org/2001/XMLSchema"xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"ApplicationTypeName="VisualObjects"ApplicationTypeVersion="2.0.0.0"xmlns="http://schemas.microsoft.com/2011/01/fabric">

 <ServiceManifestRefServiceManifestName="VisualObjects.DataService"ServiceManifestVersion="2.0" />
```

现在，通过在 Visual Studio 中选择 **VisualObjects** 项目、右键单击，然后选择“生成”来生成项目（如果选择“全部重新生成”，则可能需要更新 **VisualObjects.WebService** 项目的版本及其 *ServiceManifest.xml* 和 *ApplicationManifest.xml* 中的版本，因为代码已更改）。现在，让我们将更新的应用程序打包，方法是右键单击 *VisualObjects* 项目、选择 Service Fabric 菜单，然后选择“包”。这将创建可部署的应用程序包。更新的应用程序现在准备就绪，可供部署。

## 步骤 3：确定运行状况策略和升级参数

请熟悉[应用程序升级参数](documentation/articles/service-fabric-application-upgrade-parameters)和[升级过程](documentation/articles/service-fabric-application-upgrade)，以充分了解所应用的各种升级参数、超时和运行状况标准。对于本演练，我们将服务运行状况评估标准保留为默认值（即推荐值），这意味着在升级后所有服务和实例均应为_运行状况正常_。但是，我们将 *HealthCheckStableDuration* 增加到 60 秒（这样该服务在进行下一个升级域的升级之前将至少保持 20 秒的运行状况正常）。我们还将 *UpgradeDomainTimeout* 设置为 1200 秒，将 *UpgradeTimeout* 设置为 3000 秒。最后，我们将 *UpgradeFailureAction* 设置为回滚，以便在升级期间遇到任何问题时，请求 Service Fabric 将应用程序回滚到前一版本。因此，我们在启动升级（步骤 4 中）时指定的升级参数将如下所示：

FailureAction = Rollback

HealthCheckStableDurationSec = 60

UpgradeDomainTimeoutSec = 1200

UpgradeTimeout = 3000

## 步骤 4：准备应用程序升级

现在，应用程序已生成并准备好进行升级。如果你作为管理员打开 PowerShell 窗口并键入 **Get ServiceFabricApplication**，则你将看到已部署 **VisualObjects** 的 1.0.0.0 应用程序类型。应用程序包存储在解压 Service Fabric SDK 的位置，其相对路径为 *Samples\\Services\\Stateful\\VisualObjects\\VisualObjects\\obj\\x64\\Debug*。你应在该目录中找到“包”文件夹，这是存储应用程序包的位置。请检查时间戳以确保它是最新版本（可能还需要相应地修改路径）。

现在，让我们将更新的应用程序包复制到 Service Fabric ImageStore（Service Fabric 存储应用程序包的位置）。参数 *ApplicationPackagePathInImageStore* 告知 Service Fabric 可在何处找到应用程序包。我们已使用以下命令将更新的应用程序放入“VisualObjects\_V2”（可能需要再次相应地修改路径）。

```powershell
Copy-ServiceFabricApplicationPackage  -ApplicationPackagePath .\Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug\Package
-ImageStoreConnectionString fabric:ImageStore   -ApplicationPackagePathInImageStore "VisualObjects\_V2"
```

下一步是向 Service Fabric 注册此应用程序，可使用以下命令执行此操作：

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "VisualObjects\_V2"
```

如果上面的命令未成功，则你可能需要重新生成所有服务。如步骤 2 中所述，你可能还需要更新 WebService 版本。

## 步骤 5：启动应用程序升级

现在，我们将使用以下命令启动应用程序升级：

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/VisualObjects -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000   -FailureAction Rollback -Monitored
```


请注意，可在 *ApplicationManifest.xml* 文件中找到应用程序名称。Service Fabric 使用此名称来确定升级的应用程序。如果设置的超时太短，则可能遇到一条说明该问题的失败消息。请参阅故障排除部分，或增加超时值。

现在，应用程序升级继续进行，你可以使用 Service Fabric 资源管理器或以下 PowerShell 命令对其进行监控：**Get-ServiceFabricApplicationUpgrade fabric:/VisualObjects**。

几分钟后，使用上述 PowerShell 命令的状态应说明所有升级域均已升级（完成）。并且你会发现浏览器窗口中的视觉对象现在已开始旋转！

作为一项联系，你可能想要尝试更改版本，并从版本 2 升级到版本 3，甚至从版本 2 回滚到版本 1（是的，你可以从 v2 升到 v1）。尝试使用超时和运行状况策略，以加深对其了解程度。部署到 Azure 群集时，使用的参数将不同于部署到本地群集时使用的参数，建议你保守地设置超时。


## 后续步骤

[升级参数](documentation/articles/service-fabric-application-upgrade-parameters)

[数据序列化](documentation/articles/service-fabric-application-upgrade-data-serialization)

[高级主题](documentation/articles/service-fabric-application-upgrade-advanced)

[应用程序升级故障排除](documentation/articles/service-fabric-application-upgrade-troubleshooting)
 

<!---HONumber=74-->