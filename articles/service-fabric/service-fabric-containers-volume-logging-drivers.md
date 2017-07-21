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
ms.date: 6/28/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 868c3051f60c27f15bfd99f66e50b65595951a00
ms.contentlocale: zh-cn
ms.lasthandoff: 05/25/2017

---

# <a name="specifying-volume-plugins-and-logging-drivers-for-your-container"></a>为容器指定卷插件和日志记录驱动程序

Service Fabric 支持为容器服务指定 [Docker 卷插件](https://docs.docker.com/engine/extend/plugins_volume/)和 [Docker 日志记录驱动程序](https://docs.docker.com/engine/admin/logging/overview/)。 如以下清单所示，应用程序清单中指定了这些插件：


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
        <Volume Source="myexternalvolume" Destination="c:\testmountlocation3" Driver="sf" IsReadOnly="true"></Volume>
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

在前面的示例中，`Volume` 的 `Source` 标记指的是源文件夹。 源文件夹可能是 VM 上托管容器或永久性远程存储的文件夹。 `Destination` 标记是 `Source` 映射到正在运行的容器中的位置。 使用卷插件时，需要制定插件名称（`Driver` 标记），如前面的示例所示。  如果指定了 Docker 日志记录驱动程序，则有必要部署代理（或容器）以处理群集中的日志。 

请参阅以下文章，将容器部署到 Service Fabric 群集：

[将 Windows 容器部署到 Windows Server 2016 上的 Service Fabric](service-fabric-deploy-container.md)

[将 Docker 容器部署到 Linux 上的 Service Fabric](service-fabric-deploy-container-linux.md)


