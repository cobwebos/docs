---
title: Service Fabric Azure 文件卷驱动程序（预览版）| Microsoft Docs
description: Service Fabric 支持使用 Azure 文件备份容器中的卷。 此功能目前处于预览状态。
services: service-fabric
documentationcenter: other
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: other
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/10/2018
ms.author: subramar
ms.openlocfilehash: a5b75a7069375f503cbe25554eb7c04cba868413
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38969599"
---
# <a name="service-fabric-azure-files-volume-driver-preview"></a>Service Fabric Azure 文件卷驱动程序（预览版）
Azure 文件卷插件是一个 [Docker 卷插件](https://docs.docker.com/engine/extend/plugins_volume/)，可为 Docker 容器提供基于 [Azure 文件](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)的卷。 该 Docker 卷插件可在打包为 Service Fabric 应用程序后部署到 Service Fabric 群集。 该插件用于为部署到群集的其他 Service Fabric 容器应用程序提供基于 Azure 文件的卷。

> [!NOTE]
> Azure 文件卷插件的版本 6.255.389.9494 是预览版，可用于本文档。 作为预览版本，不支持在生产环境中使用。
>

## <a name="prerequisites"></a>先决条件
* Windows 版 Azure 文件卷插件仅适用于 [Windows Server 1709 版](https://docs.microsoft.com/windows-server/get-started/whats-new-in-windows-server-1709)、[Windows 10 1709 版](https://docs.microsoft.com/windows/whats-new/whats-new-windows-10-version-1709)或更高版本的操作系统。 Linux 版 Azure 文件卷插件适用于 Service Fabric 支持的所有操作系统版本。

* Azure 文件卷插件仅适用于 Service Fabric 6.2 和更高版本。

* 按照 [Azure 文件文档](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share)中的说明，为要用作卷的 Service Fabric 容器应用程序创建文件共享。

* 需要[具有 Service Fabric 模块的 Powershell](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started) 或安装 [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli)。

## <a name="deploy-the-service-fabric-azure-files-application"></a>部署 Service Fabric Azure 文件应用程序

可以从以下[链接](https://aka.ms/sfvolume)下载为容器提供卷的 Service Fabric 应用程序。 可以通过 [PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications)、[CLI](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-lifecycle-sfctl) 或 [FabricClient API](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications-fabricclient) 将应用程序部署到群集。

1. 使用命令行，将目录更改为已下载的应用程序包的根目录。

    ```powershell
    cd .\AzureFilesVolume\
    ```

    ```bash
    cd ~/AzureFilesVolume
    ```

2. 将应用程序包复制到映像存储区 使用 [ApplicationPackagePath] 和 [ImageStoreConnectionString] 的相应值运行以下命令：

    ```powershell
    Copy-ServiceFabricApplicationPackage -ApplicationPackagePath [ApplicationPackagePath] -ImageStoreConnectionString [ImageStoreConnectionString] -ApplicationPackagePathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl cluster select --endpoint https://testcluster.westus.cloudapp.azure.com:19080 --pem test.pem --no-verify
    sfctl application upload --path [ApplicationPackagePath] --show-progress
    ```

3. 注册应用程序类型

    ```powershell
    Register-ServiceFabricApplicationType -ApplicationPathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl application provision --application-type-build-path [ApplicationPackagePath]
    ```

4. 创建应用程序 在下方创建应用程序的命令中，请注意 ListenPort 应用程序参数。 为此应用程序参数指定的该值是 Azure 文件卷插件从 Docker 守护程序侦听请求的端口。 务必确保为应用程序提供的端口不与任何其他群集或应用程序使用的端口存在冲突。

    ```powershell
    New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.255.389.9494 -ApplicationParameter @{ListenPort='19100'}
    ```

    ```bash
    sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.255.389.9494 --parameter '{"ListenPort":"19100"}'
    ```

> [!NOTE]

> Windows Server 2016 Datacenter 不支持向容器装载映射 SMB （[仅 Windows Server 1709 版支持](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/container-storage)）。 这样可以阻止网络卷映射和 Azure 文件卷驱动程序出现在早于 1709 的版本上。
>   

### <a name="deploy-the-application-on-a-local-development-cluster"></a>在本地开发群集上部署应用程序
Azure 文件卷插件应用程序的默认服务实例计数为 -1，这表示有一个服务实例会部署到群集中的每个节点。 但在本地开发群集上部署 Azure 文件卷插件应用程序时，服务实例计数应指定为 1。 可以通过 InstanceCount 应用程序参数完成此操作。 因此，在本地开发群集上部署 Azure 文件卷插件应用程序的命令为：

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.255.389.9494 -ApplicationParameter @{ListenPort='19100';InstanceCount='1'}
```

```bash
sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.255.389.9494 --parameter '{"ListenPort": "19100","InstanceCount": "1"}'
```
## <a name="configure-your-applications-to-use-the-volume"></a>配置应用程序以使用卷
以下代码段演示如何在应用程序清单中指定基于 Azure 文件的卷。 相关特定元素为 Volume 标记：

```xml
?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WinNodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <Description>Calculator Application</Description>
    <Parameters>
      <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
      <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
      <Parameter Name="MyStorageVar" DefaultValue="c:\tmp"></Parameter>
    </Parameters>
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
     <Policies>
       <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv">
            <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
            <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
            <Volume Source="azfiles" Destination="c:\VolumeTest\Data" Driver="sfazurefile">
                <DriverOption Name="shareName" Value="" />
                <DriverOption Name="storageAccountName" Value="" />
                <DriverOption Name="storageAccountKey" Value="" />
            </Volume>
       </ContainerHostPolicies>
   </Policies>
    </ServiceManifestImport>
    <ServiceTemplates>
        <StatelessService ServiceTypeName="StatelessNodeService" InstanceCount="5">
            <SingletonPartition></SingletonPartition>
        </StatelessService>
    </ServiceTemplates>
</ApplicationManifest>
```

Azure 文件卷插件的驱动程序名称为 sfazurefile。 此值为应用程序清单中 Volume 元素的 Driver 属性而设置。

在上文代码段的 Volume 元素中，Azure 文件卷插件需要以下标记：
- **Source** - 这是卷的名称。 用户可以为其卷选取任何名称。
- **Destination** - 此标记是卷在运行的容器中映射到的位置。 因此，目标不能为容器中的现有位置

如上文代码段中的 DriverOption 元素所示，Azure 文件卷插件支持以下驱动程序选项：

- **shareName** - 为容器提供卷的“Azure 文件”文件共享的名称
- **storageAccountName** - 包含“Azure 文件”文件共享的 Azure 存储帐户的名称
- **storageAccountKey** - 包含“Azure 文件”文件共享的 Azure 存储帐户的访问密钥

以上所有驱动程序选项都是必需的。

## <a name="using-your-own-volume-or-logging-driver"></a>使用自己的卷或日志记录驱动程序
Service Fabric 还允许使用自己的自定义[卷](https://docs.docker.com/engine/extend/plugins_volume/)或[日志记录](https://docs.docker.com/engine/admin/logging/overview/)驱动程序。 如果群集上未安装 Docker 卷/日志记录驱动程序，可使用 RDP/SSH 协议手动安装。 还可使用这些协议，通过[虚拟机规模集启动脚本](https://azure.microsoft.com/resources/templates/201-vmss-custom-script-windows/)或 [SetupEntryPoint 脚本](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-model#describe-a-service)执行安装操作。

以下是安装 [Azure 的 Docker 卷驱动程序](https://docs.docker.com/docker-for-azure/persistent-data-volumes/)的一个脚本实例：

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

在应用程序中，要使用已安装的卷或日志记录驱动程序，则必须在应用程序清单中 ContainerHostPolicies 下方的 Volume 和 LogConfig 元素中指定相应的值。

```xml
<ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv">
    <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
    <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
    <LogConfig Driver="[YOUR_LOG_DRIVER]" >
        <DriverOption Name="test" Value="vale"/>
    </LogConfig>
    <Volume Source="c:\workspace" Destination="c:\testmountlocation1" IsReadOnly="false"></Volume>
    <Volume Source="[MyStorageVar]" Destination="c:\testmountlocation2" IsReadOnly="true"> </Volume>
    <Volume Source="myvolume1" Destination="c:\testmountlocation2" Driver="[YOUR_VOLUME_DRIVER]" IsReadOnly="true">
        <DriverOption Name="[name]" Value="[value]"/>
    </Volume>
</ContainerHostPolicies>
```

指定卷插件时，Service Fabric 使用指定的参数自动创建卷。 “Volume”元素的“Source”标记是卷的名称，“Driver”标记指定卷驱动程序插件。 “Destination”标记是“Source”在运行的容器中映射到的位置。 因此，目标不能为容器中的现有位置。 使用 DriverOption 标记可指定选项，如下所示：

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```

应用程序支持参数卷中前面的清单代码段所示（查找 `MyStorageVar` 有关用法示例）。

如果指定了 Docker 日志记录驱动程序，则需要部署代理（或容器）以处理群集中的日志。 DriverOption 标记可用于指定日志记录驱动程序的选项。

## <a name="next-steps"></a>后续步骤
* 要查看容器示例（包括卷驱动程序），请访问 [Service Fabric 容器示例](https://github.com/Azure-Samples/service-fabric-containers)
* 要将容器部署到 Service Fabric 群集，请参阅[在 Service Fabric 上部署容器](service-fabric-deploy-container.md)一文
