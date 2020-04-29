---
title: 如何将开发计算机连接到 AKS 群集
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: 描述使用 Azure Dev Spaces 将开发计算机连接到 Azure Kubernetes Service 群集的过程
keywords: Azure Dev Spaces，Dev 空间，Docker，Kubernetes，Azure，AKS，Azure Kubernetes 服务，容器
ms.openlocfilehash: a74a5a623006ccd64441023c2c4bc9ad3dcb517e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80241707"
---
# <a name="how-connecting-your-development-computer-to-your-aks-cluster-works"></a>如何将开发计算机连接到 AKS 群集

利用 Azure Dev Spaces，你可以将开发计算机连接到 AKS 群集，以便在开发计算机上运行和调试代码，就像它在群集上运行一样。 Azure Dev Spaces 通过在群集上运行一个 pod，将流量重定向到连接的 AKS 群集，该群集充当远程代理，用于重定向开发计算机和群集之间的流量。 此流量重定向允许开发计算机上的代码和在 AKS 群集中运行的服务进行通信，就像它们位于同一个 AKS 群集中一样。 通过此连接，还可以在开发计算机上使用或不使用容器来运行和调试代码。 将开发计算机连接到群集可帮助你快速开发应用程序并执行端到端测试。

## <a name="connecting-to-your-cluster"></a>连接到群集

使用[Visual Studio Code][vs-code]安装在 MacOS 或 Windows 10 上运行的[Azure Dev Spaces][azds-vs-code]扩展连接到现有的 AKS 群集。 建立连接时，可以选择将群集中新的或现有的 pod 重定向到开发计算机。

> [!NOTE]
> 使用 Visual Studio Code 连接到群集时，Azure Dev Spaces 扩展提供了将服务重定向到开发计算机的选项。 使用此选项可以方便地识别用于重定向的 pod。 AKS 群集与开发计算机之间的所有重定向都适用于 pod。

连接到群集时，不需要在群集上启用 Azure Dev Spaces。 相反，当 Azure Dev Spaces 扩展与群集建立连接时，它会执行以下操作：

* 将 AKS 群集上 pod 中的容器替换为将流量重定向到开发计算机的远程代理容器。 重定向新 pod 时，Azure Dev Spaces 使用远程代理在 AKS 群集中创建新的 pod。
* 在开发计算机上运行[kubectl 端口转发][kubectl-port-forward]，将流量从开发计算机转发到群集中运行的远程代理。
* 使用远程代理从群集收集环境信息。 此环境信息包括环境变量、可见服务、卷装载和机密装载。
* 在 Visual Studio Code 终端中设置环境，使开发计算机上的服务可以访问与群集上运行相同的变量。  
* 更新主机文件，将 AKS 群集上的服务映射到开发计算机上的本地 IP 地址。 这些主机文件条目允许在开发计算机上运行的代码向群集中运行的其他服务发出请求。 若要更新主机文件，在连接到群集时，Azure Dev Spaces 会要求在开发计算机上进行管理员访问。

如果在群集上启用了 Azure Dev Spaces，则还可以选择使用[Azure Dev Spaces 提供的流量重定向][how-it-works-routing]。 通过 Azure Dev Spaces 提供的流量重定向，可以连接到在子开发人员空间中运行的服务副本。 使用子开发人员空间有助于避免中断在父开发人员空间中工作的其他人，因为只重定向目标为服务子空间实例的流量，使服务的父空间实例保持不变。

连接到群集后，无论你的服务是否在开发计算机上运行，流量都将路由到你的开发计算机。

## <a name="running-code-on-your-development-computer"></a>在开发计算机上运行代码

建立与 AKS 群集的连接后，可以在计算机上本机运行任何代码，而无需容器化。 远程代理接收的任何网络流量都将重定向到连接期间指定的本地端口，以便本机运行的代码可以接受和处理该流量。 群集中的环境变量、卷和机密可供开发计算机上运行的代码使用。 此外，由于 Azure Dev Spaces 中添加了主机文件条目和端口转发，你的代码可以使用群集中的服务名称向群集上运行的服务发送网络流量，并将该流量转发到群集中运行的服务。

由于你的代码在你的开发计算机上运行，因此你可以灵活地使用通常用于开发的任何工具来运行代码并对其进行调试。 在您连接的整个时间，将在您的开发计算机和群集之间路由流量。 此永久性连接使你可以根据需要启动、停止和重新启动代码，而无需重新建立连接。

此外，Azure Dev Spaces 提供了一种方法，用于通过*azds-local*文件将环境变量和已装载的文件复制到开发计算机的 AKS 群集中的 pod。 你还可以使用此文件创建新的环境变量和卷装入。

## <a name="additional-configuration-with-azds-localenv"></a>具有 azds-local 的附加配置

*Azds-local*文件允许你将环境变量和已装载文件复制到 AKS 群集中的 pod。 可以在*azds-local*文件中指定以下操作：

* 下载卷并将该卷的路径设置为环境变量。
* 从卷下载单个文件或一组文件，并将其装载到您的开发计算机上。
* 无论连接到哪个群集，都可以使用服务。

下面是一个示例*azds-local*文件：

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

不会自动创建默认的*azds-local*文件，因此你必须在项目的根目录中手动创建文件。

## <a name="diagnostics-and-logging"></a>诊断和日志记录

连接到 AKS 群集后，会将群集中的诊断日志记录到开发计算机的[临时目录][azds-tmp-dir]中。 使用 Visual Studio Code，还可以使用 "*显示诊断信息*" 命令从 AKS 群集中打印当前环境变量和 DNS 条目。

## <a name="next-steps"></a>后续步骤

若要开始将本地开发计算机连接到 AKS 群集，请参阅[将开发计算机连接到 AKS 群集][connect]。

[azds-tmp-dir]: troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[connect]: how-to/connect.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[vs-code]: https://code.visualstudio.com/download
