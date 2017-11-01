---
title: "Azure Service Fabric Docker Compose 预览版 | Microsoft Docs"
description: "Azure Service Fabric 接受 Docker Compose 格式，因此可以更轻松地安排使用 Service Fabric 的现有容器。 这种支持目前处于预览状态。"
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
ms.openlocfilehash: cf7b0dd3a81c35be4907dbba85b72ce4f87e3a9f
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2017
---
# <a name="using-volume-plugins-and-logging-drivers-in-your-container"></a>在容器中使用卷插件和日志记录驱动程序

Service Fabric 支持为容器服务指定 [Docker 卷插件](https://docs.docker.com/engine/extend/plugins_volume/)和 [Docker 日志记录驱动程序](https://docs.docker.com/engine/admin/logging/overview/)。 

## <a name="install-volumelogging-driver"></a>安装卷/日志记录驱动程序

如果计算机上未安装 Docker 卷/日志记录驱动程序，请通过 RDP/SSH 连接到计算机手动安装它或通过 VMSS 启动脚本安装。 例如，若要安装 Docker 卷驱动程序，请通过 SSH 连接到该计算机并执行：

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

## <a name="specify-the-plugin-or-driver-in-the-manifest"></a>在清单中指定插件或驱动程序
如以下清单所示，应用程序清单中指定了这些插件：

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

在前面的示例中，`Volume` 的 `Source` 标记指的是源文件夹。 源文件夹可能是 VM 上托管容器或永久性远程存储的文件夹。 `Destination` 标记是 `Source` 映射到正在运行的容器中的位置。 

指定卷插件时，Service Fabric 使用指定的参数自动创建卷。 `Source` 标记是该卷的名称，`Driver` 标记指定卷驱动程序插件。 可使用 `DriverOption` 标记指定选项，如以下代码片段所示：

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azurefile" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```

如果指定了 Docker 日志记录驱动程序，则有必要部署代理（或容器）以处理群集中的日志。  `DriverOption` 标记还可用于指定日志驱动程序选项。

请参阅以下文章，将容器部署到 Service Fabric 群集：


[在 Service Fabric 上部署容器](service-fabric-deploy-container.md)

