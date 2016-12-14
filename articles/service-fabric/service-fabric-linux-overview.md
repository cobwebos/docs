---
title: "Linux 上的 Azure Service Fabric |Microsoft 文档"
description: "Service Fabric 群集支持 Linux 和 Java，这意味着，可以在 Linux 上部署和托管以 Java 和 C# 编写的 Service Fabric 应用程序。"
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: 459afade-145d-4ee6-b72b-ddf380ccd1bf
ms.service: service-fabric
ms.devlang: Java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/26/2016
ms.author: SubramaR
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f52903801c53a50d149fe8732bf514194aae0478


---
# <a name="service-fabric-on-linux"></a>Linux 上的 Service Fabric
如同在 Windows 上一样，可以使用 Linux 上的 Service Fabric 预览版在 Linux 上构建、部署和管理高可用性、高度可缩放的应用程序。 Service Fabric 框架（Reliable Services 和 Reliable Actors）除了可在 C# (.NET Core) 中使用以外，也能在 Java on Linux 中使用。  还可以使用任何语言或框架来构建[来宾可执行的服务](service-fabric-deploy-existing-app.md)。 此外，预览版还支持协调 Docker 容器。 Docker 容器可以运行使用 Service Fabric 框架的来宾可执行文件或本机 Service Fabric 服务。

Linux 上的 Service Fabric 在概念上等同于 Windows 上的 Service Fabric（OS 细节和编程语言支持除外）。 因此，我们的大部分[现有文档](http://aka.ms/servicefabricdocs)都能帮助你熟悉该技术。

> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/Service-Fabric-Linux-Preview/player]
> 
> 

## <a name="supported-operating-systems-and-programming-languages"></a>支持操作系统和编程语言
受限预览版支持创建单机开发群集，以及在 Azure 中运行 Ubuntu Server 16.04 的多计算机群集。 除了来宾可执行文件和协调 Docker 容器以外，预览版还支持 Java 和 C# 中的 Reliable Actors 和 Reliable Stateless Services 框架。  

> [!NOTE]
> Linux 尚不支持 Reliable 集合。 此外，也不支持独立群集 - 预览版仅支持单机群集和 Azure Linux 多计算机群集。
> 
> 

## <a name="supported-tooling"></a>支持的工具
预览版支持通过 Azure CLI 来与群集交互。 Java 开发人员可以通过 Linux 和 OSX 上支持的 Eclipse 来与 Eclipse 和 Yeoman 集成。 OSX 集成在幕后通过 vagrant 使用 Linux VM。 C# 开发人员可以借助与 Yeoman 的集成来生成应用程序模板。

## <a name="next-steps"></a>后续步骤
1. 熟悉 [Reliable Actors](service-fabric-reliable-actors-introduction.md) 和 [Reliable Services](service-fabric-reliable-services-introduction.md) 编程框架。
2. [在 Linux 上准备开发环境](service-fabric-get-started-linux.md)
3. [在 OSX 上准备开发环境](service-fabric-get-started-mac.md)
4. [在 Linux 上创建第一个 Service Fabric Java 应用程序](service-fabric-create-your-first-linux-application-with-java.md)




<!--HONumber=Nov16_HO3-->


