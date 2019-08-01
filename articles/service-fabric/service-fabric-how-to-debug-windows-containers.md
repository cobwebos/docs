---
title: 使用 Service Fabric 和 VS 调试 Windows 容器 | Microsoft Docs
description: 了解如何使用 Visual Studio 2019 在 Azure Service Fabric 中调试 Windows 容器。
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: msfussell
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/14/2019
ms.author: mikhegn
ms.openlocfilehash: 043623869163a27cdbbdd7115fca43486577ca7d
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599443"
---
# <a name="how-to-debug-windows-containers-in-azure-service-fabric-using-visual-studio-2019"></a>如何：使用 Visual Studio 2019 在 Azure Service Fabric 中调试 Windows 容器

借助 Visual Studio 2019，可以将容器中的 .NET 应用程序作为 Service Fabric 服务进行调试。 本文介绍如何配置环境，然后调试在本地 Service Fabric 群集中运行的容器中的 .NET 应用程序。

## <a name="prerequisites"></a>先决条件

* 在 Windows 10 上，按照此快速入门[配置 Windows 10 以运行 Windows 容器](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10)
* 在 Windows Server 2016 上，按照此快速入门[配置 Windows 2016 以运行 Windows 容器](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-server)
* 按照[在 Windows 上准备开发环境](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)来设置本地 Service Fabric 环境

## <a name="configure-your-developer-environment-to-debug-containers"></a>配置开发人员环境以调试容器

1. 确保 Docker for Window 服务正在运行，然后再继续进行下一步。

1. 为了支持容器之间的 DNS 解析，必须使用计算机名称来设置本地开发群集。 如果要通过反向代理寻址服务，则还需要执行这些步骤。
   1. 以管理员身份打开 PowerShell
   2. 导航到 SDK 群集安装文件夹，通常为 `C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup`。
   3. 运行脚本 `DevClusterSetup.ps1`

      ``` PowerShell
        C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1
      ```

      > [!NOTE]
      > 可以使用 `-CreateOneNodeCluster` 来设置单节点群集。 默认情况下将创建一个本地五节点群集。
      >

      若要详细了解 Service Fabric 中的 DNS 服务，请参阅 [Azure Service Fabric 中的 DNS 服务](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice)。 如需深入了解从容器中运行的服务使用 Service Fabric 反向代理，请参阅[对容器中运行的服务的反向代理特殊处理](service-fabric-reverseproxy.md#special-handling-for-services-running-in-containers)。

### <a name="known-limitations-when-debugging-containers-in-service-fabric"></a>在 Service Fabric 中调试容器时的已知限制

下面列出了在 Service Fabric 中调试容器时的已知限制以及可能的解决方法：

* 针对 ClusterFQDNorIP 使用 localhost 时不支持容器中的 DNS 解析。
    * 解决方法：使用计算机名称设置本地群集（请参阅上文）
* 在虚拟机中运行 Windows10 时不会向容器返回 DNS 回复。
    * 解决方法：在虚拟机 NIC 上为 IPv4 禁用 UDP 校验和卸载
    * 运行 Windows 10 会降低计算机上的网络性能。
    * https://github.com/Azure/service-fabric-issues/issues/1061
* 如果使用 Docker Compose 部署应用程序，则无法在 Windows10 中使用 DNS 服务名称解析相同应用程序中的服务
    * 解决方法：使用 servicename.applicationname 解析服务终结点
    * https://github.com/Azure/service-fabric-issues/issues/1062
* 如果针对 ClusterFQDNorIP 使用 IP-address，则更改主机上的主 IP 将破坏 DNS 功能。
    * 解决方法：使用主机上新的主 IP 重新创建群集或使用计算机名称。 此破坏是设计使然。
* 如果无法在网络上解析创建群集时使用的 FQDN，则 DNS 将失败。
    * 解决方法：使用主机的主 IP 重新创建本地群集。 此失败是设计使然。
* 调试容器时，docker 日志只能在 Visual Studio 输出窗口中使用，而不能通过 Service Fabric API（包括 Service Fabric Explorer）使用

## <a name="debug-a-net-application-running-in-docker-containers-on-service-fabric"></a>调试在 Service Fabric 上的 docker 容器中运行的 .NET 应用程序

1. 以管理员身份运行 Visual Studio。

1. 打开现有的 .NET 应用程序或新建一个 .NET 应用程序。

1. 右键单击项目并选择“添加”->“容器业务流程协调程序支持”->“Service Fabric”

1. 按 **F5** 开始调试应用程序。

    Visual Studio 支持 .NET 和 .NET Core 的控制台和 ASP.NET 项目类型。

## <a name="next-steps"></a>后续步骤
若要详细了解 Service Fabric 和容器的功能，请参阅 Service Fabric 容器概述](service-fabric-containers-overview.md)。
