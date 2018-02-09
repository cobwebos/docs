---
title: "Azure Service Fabric Docker Compose（预览版）| Microsoft Docs"
description: "Azure Service Fabric 接受 Docker Compose 格式，因此使用 Service Fabric 可更轻松地安排现有容器。 对 Docker Compose 支持当前处于预览阶段。"
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: 8918d6d53d7dd04e2a685707979526230ebfbc42
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
# <a name="use-docker-volume-plug-ins-and-logging-drivers-in-your-container"></a>在容器中使用 Docker 卷插件和日志记录驱动程序
Azure Service Fabric 支持为容器服务指定 [Docker 卷插件](https://docs.docker.com/engine/extend/plugins_volume/)和 [Docker 日志记录驱动程序](https://docs.docker.com/engine/admin/logging/overview/)。 因此，可以在 [Azure 文件](https://azure.microsoft.com/services/storage/files/)中持久保存数据，即使容器已移动或已在另一台主机上重启，也是如此。

当前只支持用于 Linux 容器的卷驱动程序。 如果使用 Windows 容器，即使没有卷驱动程序，也可将卷映射到 Azure 文件 [SMB3 共享](https://blogs.msdn.microsoft.com/clustering/2017/08/10/container-storage-support-with-cluster-shared-volumes-csv-storage-spaces-direct-s2d-smb-global-mapping/)。 要进行此映射，需要将群集中的虚拟机 (VMs) 更新到 Windows Server 1709 最新版本。


## <a name="install-the-docker-volumelogging-driver"></a>安装 Docker 卷/日志记录驱动程序

如果计算机上未安装 Docker 卷/日志记录驱动程序，则可通过 RDP/SSH 协议手动安装。 还可使用这些协议，通过[虚拟机规模集启动脚本](https://azure.microsoft.com/resources/templates/201-vmss-custom-script-windows/)或 [SetupEntryPoint 脚本](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-model#describe-a-service)执行安装操作。

以下是安装 [Azure 的 Docker 卷驱动程序](https://docs.docker.com/docker-for-azure/persistent-data-volumes/)的一个脚本实例：

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

> [!NOTE]
> Windows Server 2016 Datacenter 不支持向容器装载映射 SMB （[仅 Windows Server 1709 版支持](https://docs.microsoft.com/en-us/virtualization/windowscontainers/manage-containers/container-storage)）。 这样可以阻止网络卷映射和 Azure 文件卷驱动程序出现在早于 1709 的版本上。 
>   


## <a name="specify-the-plug-in-or-driver-in-the-manifest"></a>在清单中指定插件或驱动程序
应用程序清单指定了以下插件：

```xml
?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WinNodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <Description>Calculator Application</Description>
    <Parameters>
        <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
      <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
    </Parameters>
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
     <Policies>
       <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv"> 
        <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
        <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
        <LogConfig Driver="etwlogs" >
          <DriverOption Name="test" Value="vale"/>
        </LogConfig>
        <Volume Source="c:\workspace" Destination="c:\testmountlocation1" IsReadOnly="false"></Volume>
        <Volume Source="d:\myfolder" Destination="c:\testmountlocation2" IsReadOnly="true"> </Volume>
        <Volume Source="myvolume1" Destination="c:\testmountlocation2" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
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

Volume 元素的“Source”标记指的是源文件夹。 源文件夹可作为 VM 上托管容器或永久性远程存储的文件夹。 “Destination”标记是“Source”在运行的容器中映射到的位置。 因此，目标不能为容器中的现有位置。

指定卷插件时，Service Fabric 使用指定的参数自动创建卷。 “Source”标记是卷的名称，“Driver”标记指定卷驱动程序插件。 使用 DriverOption 标记可指定选项，如下所示：

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```
如果指定了 Docker 日志记录驱动程序，则需要部署代理（或容器）以处理群集中的日志。 DriverOption 标记可用于指定日志记录驱动程序的选项。

## <a name="next-steps"></a>后续步骤
要将容器部署到 Service Fabric 群集，请参阅[在 Service Fabric 上部署容器](service-fabric-deploy-container.md)。
