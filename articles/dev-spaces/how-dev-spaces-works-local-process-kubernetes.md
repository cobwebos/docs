---
title: 如何通过本地进程使用 Kubernetes
services: azure-dev-spaces
ms.date: 06/02/2020
ms.topic: conceptual
description: 描述使用本地进程和 Kubernetes 将开发计算机连接到 Kubernetes 群集的过程
keywords: 本地过程，其中包含 Kubernetes、Azure Dev Spaces、Dev Spaces、Docker、Kubernetes、Azure、AKS、Azure Kubernetes Service、容器
ms.openlocfilehash: dd126fc55a86b1de115239a31e5adb7b1d264846
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84974392"
---
# <a name="how-local-process-with-kubernetes-works"></a>如何通过本地进程使用 Kubernetes

借助 Kubernetes 的本地进程，你可以在开发计算机上运行和调试代码，同时仍与应用程序或服务的其余部分连接到 Kubernetes 群集。 例如，如果你有一个具有许多相互依赖的服务和数据库的大型微服务体系结构，则在开发计算机上复制这些依赖项可能会很困难。 此外，对于在内部循环开发期间每个代码更改，生成和部署代码到 Kubernetes 群集可能会很慢、耗时且难以与调试程序一起使用。

使用 Kubernetes 的本地进程可以通过直接在开发计算机与群集之间创建连接，来避免生成代码并将其部署到群集。 通过在调试时将开发计算机连接到群集，可以在完整应用程序的上下文中快速测试和开发服务，而无需创建任何 Docker 或 Kubernetes 配置。

具有 Kubernetes 的本地进程将重定向已连接的 Kubernetes 群集与开发计算机之间的流量。 此流量重定向允许开发计算机上的代码和在 Kubernetes 群集中运行的服务进行通信，就像它们位于同一个 Kubernetes 群集中一样。 使用 Kubernetes 的本地进程还提供了一种方法，用于将环境变量和已装载的卷复制到开发计算机的 Kubernetes 群集中的 pod。 通过提供对开发计算机上的环境变量和已装入卷的访问，可以快速处理代码，而无需手动复制这些依赖项。

## <a name="using-local-process-with-kubernetes"></a>使用 Kubernetes 的本地进程

若要将本地进程用于 Kubernetes， [Visual Studio Code][vs-code]需要在 MacOS 或 Windows 10 上安装并运行[Azure Dev Spaces][azds-vs-code]和[Azure Kubernetes 服务][az-aks-vs-code]扩展，以及[安装 Azure Dev Spaces CLI][azds-cli]。 你还可以使用在 Windows 10 上运行的[Visual Studio 2019][visual-studio] ，其中安装了*ASP.NET 和 Web 开发*和*Azure 开发*工作负载，并且启用了*LocalKubernetesDebugging*预览版功能标志，并[安装了 Azure Dev Spaces CLI][azds-cli]。 使用 Kubernetes 的本地进程建立与 Kubernetes 群集的连接时，可以选择将群集中现有 pod 的所有流量重定向到你的开发计算机。

> [!NOTE]
> 使用 Kubernetes 的本地进程时，系统将提示您输入要重定向到开发计算机的服务的名称。 使用此选项可以方便地识别用于重定向的 pod。 Kubernetes 群集与开发计算机之间的所有重定向都适用于 pod。

当使用 Kubernetes 的本地进程建立与群集的连接时，它会执行以下操作：

* 会提示你将服务配置为在群集上进行替换，将你的开发计算机上的端口用于你的代码，并将代码的启动任务配置为一次性操作。
* 将群集上 pod 中的容器替换为将流量重定向到开发计算机的远程代理容器。
* 在开发计算机上运行[kubectl 端口转发][kubectl-port-forward]，将流量从开发计算机转发到群集中运行的远程代理。
* 使用远程代理从群集收集环境信息。 此环境信息包括环境变量、可见服务、卷装载和机密装载。
* 在 Visual Studio 或 Visual Studio Code 中设置环境，使开发计算机上的服务可以访问与群集上运行相同的变量。  
* 更新主机文件，以将群集上的服务映射到开发计算机上的本地 IP 地址。 这些主机文件条目允许在开发计算机上运行的代码向群集中运行的其他服务发出请求。 若要更新主机文件，使用 Kubernetes 的本地进程将在连接到群集时要求在开发计算机上进行管理员访问。
* 开始在开发计算机上运行和调试代码。 如有必要，使用 Kubernetes 的本地进程将停止开发计算机上的所需端口，方法是停止当前正在使用这些端口的服务或进程。

建立与群集的连接后，可以在计算机上本地运行和调试代码，而无需容器化，代码可以直接与群集的其余部分交互。 远程代理接收的任何网络流量都将重定向到连接期间指定的本地端口，以便本机运行的代码可以接受和处理该流量。 群集中的环境变量、卷和机密可供开发计算机上运行的代码使用。 此外，由于本地进程使用 Kubernetes 将主机文件条目和端口转发添加到了你的开发人员计算机，你的代码可以使用群集中的服务名称向群集上运行的服务发送网络流量，并将该流量转发到群集中正在运行的服务。 在您连接的整个时间，将在您的开发计算机和群集之间路由流量。

## <a name="diagnostics-and-logging"></a>诊断和日志记录

使用带有 Kubernetes 的本地进程连接到群集时，会将群集中的诊断日志记录到开发计算机的[临时目录][azds-tmp-dir]中。 使用 Visual Studio Code，还可以使用 "*显示诊断信息*" 命令从群集中打印当前环境变量和 DNS 条目。

## <a name="limitations"></a>限制

具有 Kubernetes 的本地进程具有以下限制：

* 使用 Kubernetes 的本地进程将单个服务的流量重定向到你的开发计算机。 不能同时使用带有 Kubernetes 的本地进程来重定向多个服务。
* 若要连接到该服务，服务必须由一个 pod 支持。 无法使用多个 pod 连接到服务，例如使用副本的服务。
* 一个 pod 只能在该 pod 中运行一个容器，以便与 Kubernetes 成功连接。 使用 Kubernetes 的本地进程无法使用具有附加容器（如服务网格注入的挎斗容器）的 pod 连接到服务。
* 具有 Kubernetes 的本地进程需要提升的权限才能在开发计算机上运行，以便编辑主机文件。

## <a name="next-steps"></a>后续步骤

若要开始使用 Kubernetes 的本地进程连接到你的本地开发计算机到你的群集，请参阅[将本地进程与 Kubernetes 配合使用 Visual Studio Code][local-process-kubernetes-vs-code] ，并[将本地进程与 Visual Studio 配合使用][local-process-kubernetes-vs]。

[azds-cli]: how-to/install-dev-spaces.md#install-the-client-side-tools
[azds-tmp-dir]: troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-aks-vs-code]: https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-aks-tools
[local-process-kubernetes-vs-code]: how-to/local-process-kubernetes-vs-code.md
[local-process-kubernetes-vs]: how-to/local-process-kubernetes-visual-studio.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[visual-studio]: https://www.visualstudio.com/vs/
[vs-code]: https://code.visualstudio.com/download