---
title: "在 Azure Service Fabric 中指定服务的环境变量的操作说明 | Microsoft Docs"
description: "演示如何在 Service Fabric 中使用应用程序的环境变量"
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
ms.openlocfilehash: d487eeadde9f9a45549763863f8fe5b06b2945a4
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2018
---
# <a name="how-to-specify-environment-variables-for-services-in-service-fabric"></a>如何在 Service Fabric 中指定服务的环境变量

本文演示如何在 Service Fabric 中指定服务或容器的环境变量。

## <a name="procedure-for-specifying-environment-variables-for-services"></a>指定服务的环境变量的过程

在此示例中，可以为容器设置环境变量。 本文假设你已有一个应用程序和服务清单。

1. 打开 ServiceManifest.xml 文件。
1. 在 `CodePackage` 元素中，为每个环境变量添加新的 `EnvironmentVariables` 元素和 `EnvironmentVariable` 元素。

    ```xml
      <CodePackage Name="MyCode" Version="CodeVersion1">
      ...
        <EnvironmentVariables>
          <EnvironmentVariable Name="MyEnvVariable" Value="DeafultValue"/>
          <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
        </EnvironmentVariables>
      </CodePackage>
    ```

    可在应用程序清单中重写环境变量。

1. 若要替代应用程序清单中的环境变量，请使用 `EnvironmentOverrides` 元素。

    ```xml
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontEndServicePkg" ServiceManifestVersion="1.0.0" />
        <EnvironmentOverrides CodePackageRef="MyCode">
          <EnvironmentVariable Name="MyEnvVariable" Value="OverrideValue"/>
        </EnvironmentOverrides>
      </ServiceManifestImport>
    ```

## <a name="next-steps"></a>后续步骤
若要详细了解本文中讨论的一些核心概念，请参阅文章[管理多个环境的应用程序](service-fabric-manage-multiple-environment-app-configuration.md)。

有关 Visual Studio 中其他可用应用管理功能的信息，请参阅[在 Visual Studio 中管理 Service Fabric 应用程序](service-fabric-manage-application-in-visual-studio.md)。