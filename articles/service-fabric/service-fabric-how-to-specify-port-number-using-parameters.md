---
title: "如何在 Azure Service Fabric 中使用参数来指定服务的端口号 | Microsoft Docs"
description: "演示如何在 Service Fabric 中使用参数来指定应用程序的端口"
documentationcenter: .net
author: mikkelhegn
manager: markfuss
editor: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: mikhegn
ms.openlocfilehash: aca5b6a476e9526498a5e4834aaa28eb73750562
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2018
---
# <a name="how-to-specify-the-port-number-of-a-service-using-parameters-in-service-fabric"></a>如何在 Service Fabric 中使用参数来指定服务的端口号

本文演示如何使用 Visual Studio 在 Service Fabric 中通过参数来指定服务的端口号。

## <a name="procedure-for-specifying-the-port-number-of-a-service-using-parameters"></a>使用参数来指定服务的端口号的过程

在此示例中，使用参数来设置 ASP.NET Core Web API 的端口号。

1. 打开 Visual Studio 并创建新的 Service Fabric 应用程序。
1. 选择无状态 ASP.NET Core 模板。
1. 选择 Web API。
1. 打开 ServiceManifest.xml 文件。
1. 记下为你的服务指定的终结点的名称。 默认为 `ServiceEndpoint`。
1. 打开 ApplicationManifest.xml 文件
1. 在 `ServiceManifestImport` 元素中，添加新的 `RessourceOverrides` 元素，其中包括对 ServiceManifest.xml 文件中终结点的引用。

    ```xml
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
        <ResourceOverrides>
          <Endpoints>
            <Endpoint Name="ServiceEndpoint"/>
          </Endpoints>
        </ResourceOverrides>
        <ConfigOverrides />
      </ServiceManifestImport>
    ```

1. 在 `Endpoint` 元素中，现在可使用参数替换任何属性。 本示例中，使用方括号指定 `Port` 并将其设置为参数名称 - 例如，`[MyWebAPI_PortNumber]`

    ```xml
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
        <ResourceOverrides>
          <Endpoints>
            <Endpoint Name="ServiceEndpoint" Port="[MyWebAPI_PortNumber]"/>
          </Endpoints>
        </ResourceOverrides>
        <ConfigOverrides />
      </ServiceManifestImport>
    ```

1. 仍在 ApplicationManifest.xml 文件中，然后在 `Parameters` 元素中指定参数

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" />
      </Parameters>
    ```

1. 并定义 `DefaultValue`

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" DefaultValue="8080" />
      </Parameters>
    ```

1. 打开 ApplicationParameters 文件夹和 `Cloud.xml` 文件
1. 若要指定在发布到远程群集时要使用的其他端口，请将带有端口号的参数添加到此文件。

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" DefaultValue="80" />
      </Parameters>
    ```

使用 Cloud.xml 发布配置文件从 Visual Studio 发布应用程序时，服务将配置为使用端口 80。 如果在未指定 MyWebAPI_PortNumber 参数的情况下部署应用程序，则服务使用端口 8080。

## <a name="next-steps"></a>后续步骤
若要详细了解本文中讨论的一些核心概念，请参阅文章[管理多个环境的应用程序](service-fabric-manage-multiple-environment-app-configuration.md)。

有关 Visual Studio 中其他可用应用管理功能的信息，请参阅[在 Visual Studio 中管理 Service Fabric 应用程序](service-fabric-manage-application-in-visual-studio.md)。