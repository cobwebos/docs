---
title: 将现有可执行文件打包到 Azure Service Fabric |Microsoft Docs
description: 了解如何将现有应用程序打包为来宾可执行文件，以便部署到 Service Fabric 群集。
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: d799c1c6-75eb-4b8a-9f94-bf4f3dadf4c3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 03/15/2018
ms.author: atsenthi
ms.openlocfilehash: bd6984db67a8a7b9c38988558ada51e12d337f52
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013261"
---
# <a name="deploy-an-existing-executable-to-service-fabric"></a>将现有可执行文件部署到 Service Fabric
可以在 Azure Service Fabric 中运行任何类型的代码（如 Node.js、Java 或 C++）作为服务。 Service Fabric 将这些类型的服务称为来宾可执行文件。

来宾可执行文件由 Service Fabric 如同无状态服务一样进行处理。 因此，它们会基于可用性和其他指标放在群集中的节点上。 本文介绍如何使用 Visual Studio 或命令行实用工具打包来宾可执行文件并将其部署到 Service Fabric 群集。

## <a name="benefits-of-running-a-guest-executable-in-service-fabric"></a>在 Service Fabric 中运行来宾可执行文件的优势
在 Service Fabric 群集中运行来宾可执行文件有几个优势：

* 高可用性。 Service Fabric 中运行的应用程序具有高可用性。 Service Fabric 可确保应用程序的实例保持运行。
* 运行状况监视。 Service Fabric 运行状况监视会检测应用程序是否正在运行，在发生故障时可提供诊断信息。   
* 应用程序生命周期管理。 除了提供无需停机的升级，如果升级期间报告了运行不正常事件，Service Fabric 还支持回滚到以前的版本。    
* 密度。 可以在群集中运行多个应用程序，这样便无需使每个应用程序在自己的硬件上运行。
* 可发现性：使用 REST，可以调用要在群集中查找其他服务的 Service Fabric 命名服务。 

## <a name="samples"></a>示例
* [打包和部署来宾可执行文件的示例](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [使用 REST 通过命名服务进行通信的两种来宾可执行文件（C# 和 nodejs）示例](https://github.com/Azure-Samples/service-fabric-dotnet-containers)

## <a name="overview-of-application-and-service-manifest-files"></a>应用程序和服务清单文件概述
在部署来宾可执行文件期间，最好先了解 Service Fabric 打包和部署模型（如[应用程序模型](service-fabric-application-model.md)中所述）。 Service Fabric 打包模型依赖两个 XML 文件：应用程序清单和服务清单。 ApplicationManifest.xml 和 ServiceManifest.xml 文件的架构定义随 Service Fabric SDK 一起安装到 *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*。

* **应用程序清单**：应用程序清单用于描述应用程序。 它列出自身的构成服务，以及用于定义应如何部署一个或多个服务的其他参数（如实例数）。

  在 Service Fabric 中，应用程序是部署和升级的单位。 可将应用程序作为一个单位进行升级，其中潜在的失败和潜在回滚受到管理。 Service Fabric 保证升级过程成功，一旦升级失败，它不会使应用程序保持未知或不稳定状态。
* **服务清单**：服务清单描述服务的组件。 其中包含服务的名称和类型、其代码以及配置等数据。 服务清单还包含一些可以用于在部署之后配置服务的其他参数。

## <a name="application-package-file-structure"></a>应用程序包文件结构
要将应用程序部署到 Service Fabric，应用程序应遵循预定义的目录结构。 下面是该结构的示例。

```
|-- ApplicationPackageRoot
    |-- GuestService1Pkg
        |-- Code
            |-- existingapp.exe
        |-- Config
            |-- Settings.xml
        |-- Data
        |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```

ApplicationPackageRoot 包含定义应用程序的 ApplicationManifest.xml 文件。 应用程序中包含的每个服务的子目录用于包含该服务需要的所有项目。 这些子目录为 servicemanifest.xml 以及以下内容（通常）：

* *Code*。 此目录包含服务代码。
* *Config*。此目录包含一个 Settings .xml 文件（以及其他必要的文件），服务可以在运行时访问该文件以检索特定的配置设置。
* *Data*。 这是用于存储服务可能需要的其他本地数据的其他目录。 数据应仅用于存储临时数据。 如果需要对服务进行重定位（例如在故障转移期间），则 Service Fabric 不会复制对数据目录所做的更改。

> [!NOTE]
> 如果不需要 `config` 和 `data` 目录，则不必创建它们。
>
>

## <a name="next-steps"></a>后续步骤
有关相关信息和任务，请参阅以下文章。
* [部署来宾可执行文件](service-fabric-deploy-existing-app.md)
* [部署多个来宾可执行文件](service-fabric-deploy-multiple-apps.md)
* [使用 Visual Studio 创建第一个来宾可执行应用程序](quickstart-guest-app.md)
* [打包和部署来宾可执行文件的示例](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)，包括打包工具的预发行版本的链接
* [使用 REST 通过命名服务进行通信的两种来宾可执行文件（C# 和 nodejs）示例](https://github.com/Azure-Samples/service-fabric-containers)

