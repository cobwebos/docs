---
title: 将开发计算机连接到 AKS 群集的工作原理
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: 描述使用 Azure 开发人员空间将开发计算机连接到 Azure Kubernetes 服务群集的过程
keywords: Azure 开发空间、开发空间、Docker、库伯奈斯、Azure、AKS、Azure 库伯奈斯服务、容器
ms.openlocfilehash: a74a5a623006ccd64441023c2c4bc9ad3dcb517e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241707"
---
# <a name="how-connecting-your-development-computer-to-your-aks-cluster-works"></a>将开发计算机连接到 AKS 群集的工作原理

使用 Azure 开发人员空间，可以将开发计算机连接到 AKS 群集，从而可以在开发计算机上运行和调试代码，就像它在群集上运行一样。 Azure 开发人员空间通过在群集上运行充当远程代理的 Pod 来重定向连接的 AKS 群集之间的流量，从而在开发计算机和群集之间重定向流量。 此流量重定向允许开发计算机上的代码和在 AKS 群集中运行的服务进行通信，就像它们位于同一 AKS 群集中一样。 此连接还允许您在开发计算机上运行和调试具有或不带容器的代码。 将开发计算机连接到群集可帮助您快速开发应用程序并执行端到端测试。

## <a name="connecting-to-your-cluster"></a>连接到群集

使用[可视化工作室代码][vs-code]连接到现有的 AKS 群集，在 MacOS 或 Windows 10 上安装了[Azure 开发空间][azds-vs-code]扩展。 建立连接时，可以选择将所有流量重定向到群集中的新或现有窗格到开发计算机。

> [!NOTE]
> 使用 Visual Studio 代码连接到群集时，Azure 开发人员空间扩展提供了将服务重定向到开发计算机的选项。 此选项是标识用于重定向的窗格的便捷方法。 AKS 群集和开发计算机之间的所有重定向都用于 pod。

连接到群集不需要在群集上启用 Azure 开发空间。 相反，当 Azure 开发人员空间扩展建立到群集的连接时，它会：

* 将 AKS 群集上的窗格中的容器替换为远程代理容器，该容器将流量重定向到开发计算机。 重定向新窗格时，Azure 开发人员空间会使用远程代理在 AKS 群集中创建新的 Pod。
* 在开发计算机上运行[kubectl 端口转发][kubectl-port-forward]，将流量从开发计算机转发到群集中运行的远程代理。
* 使用远程代理从群集收集环境信息。 此环境信息包括环境变量、可见服务、卷装载和机密装载。
* 在 Visual Studio Code 终端中设置环境，以便开发计算机上的服务可以访问与在群集上运行相同的变量。  
* 更新主机文件，将 AKS 群集上的服务映射到开发计算机上的本地 IP 地址。 这些主机文件条目允许开发计算机上运行的代码向群集中运行的其他服务发出请求。 要更新主机文件，Azure 开发人员空间将在连接到群集时请求开发计算机上的管理员访问权限。

如果群集上确实启用了 Azure 开发空间，则还可以选择使用[Azure 开发空间提供的流量重定向][how-it-works-routing]。 Azure 开发空间提供的流量重定向允许您连接到在子开发空间中运行的服务的副本。 使用子开发空间可帮助您避免中断在父开发空间中工作的其他人，因为您只是重定向针对子空间的服务实例的流量，使服务的父空间实例未修改。

连接到群集后，无论开发计算机上是否运行服务，流量都会路由到开发计算机。

## <a name="running-code-on-your-development-computer"></a>在开发计算机上运行代码

建立与 AKS 群集的连接后，可以在计算机上以本机方式运行任何代码，而无需容器化。 远程代理接收的任何网络流量都会重定向到连接期间指定的本地端口，以便本机运行的代码可以接受和处理该流量。 群集中的环境变量、卷和机密可用于在开发计算机上运行的代码。 此外，由于 Azure Dev Space 向开发人员计算机添加了主机文件条目和端口转发，代码可以使用群集中的服务名称将网络流量发送到群集上运行的服务，并且该流量将转发到群集中运行的服务。

由于代码在开发计算机上运行，因此您可以灵活地使用开发中通常使用的任何工具来运行代码并对其进行调试。 在整个连接时间，流量在开发计算机和群集之间路由。 此持久连接允许您根据需要启动、停止和重新启动代码，而无需重新建立连接。

此外，Azure 开发人员空间提供了一种通过*azds-local.env*文件复制开发计算机 AKS 群集中可用于 POD 的环境变量和装载的文件的方法。 您还可以使用此文件创建新的环境变量和卷装载。

## <a name="additional-configuration-with-azds-localenv"></a>附加配置与 azds-local.env

*azds-local.env*文件允许您复制 AKS 群集中可供窗格使用的环境变量和装载的文件。 您可以在*azds-local.env*文件中指定以下操作：

* 下载卷并将该卷的路径设置为环境变量。
* 从卷下载单个文件或文件集，并将其装载到开发计算机上。
* 无论您连接到哪个群集，都能使用服务。

下面是*一个示例 azds-local.env*文件：

```
# This downloads the "whitelist" volume from the container,
# saves it to a temporary directory on your development computer,
# and sets the full path to an environment variable called WHITELIST_PATH.

WHITELIST_PATH=${volumes.whitelist}/whitelist

# This downloads a file from the container's 'default-token-<any>' mount directory 
# to /var/run/secrets/kubernetes.io/serviceaccount on your development computer.

KUBERNETES_IN_CLUSTER_CONFIG_OVERWRITE=${volumes.default-token-*|/var/run/secrets/kubernetes.io/serviceaccount}

# This makes the myapp1 service available to your development computer
# regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP1_SERVICE_HOST.

# If the myapp1 service is made available in this way, 
# you can also access it using "myapp1" and "myapp1.svc.cluster.local"
# in addition to the IP in the MYAPP1_SERVICE_HOST environment variable.

MYAPP1_SERVICE_HOST=${services.myapp1}

# This makes the service myapp2 in namespace mynamespace available to your 
# development computer regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP2_SERVICE_HOST.

MYAPP2_SERVICE_HOST=${services.mynamespace.myapp2}
```

不会自动创建默认*的 azds-local.env*文件，因此您必须在项目的根目录上手动创建该文件。

## <a name="diagnostics-and-logging"></a>诊断和日志记录

连接到 AKS 群集时，群集的诊断日志将记录到开发计算机[的临时目录][azds-tmp-dir]。 使用 Visual Studio 代码，您还可以使用 *"显示诊断信息"* 命令从 AKS 群集打印当前环境变量和 DNS 条目。

## <a name="next-steps"></a>后续步骤

要开始将本地开发计算机连接到 AKS 群集，请参阅[将开发计算机连接到 AKS 群集][connect]。

[azds-tmp-dir]: troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[connect]: how-to/connect.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[vs-code]: https://code.visualstudio.com/download
