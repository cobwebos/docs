---
title: 故障排除
services: azure-dev-spaces
ms.date: 09/25/2019
ms.topic: troubleshooting
description: 了解如何在启用和使用时对常见问题进行故障排除和解决 Azure Dev Spaces
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes 服务, 容器, Helm, 服务网格, 服务网格路由, kubectl, k8s '
ms.openlocfilehash: 2b5a6f14899ec41b1740563f4e8174f65aa679c7
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197991"
---
# <a name="azure-dev-spaces-troubleshooting"></a>Azure Dev Spaces 疑难解答

本指南介绍使用 Azure Dev Spaces 时可能会碰到的常见问题。

如果使用 Azure Dev Spaces 时遇到问题，请[在 Azure Dev Spaces GitHub 存储库中创建问题](https://github.com/Azure/dev-spaces/issues)。

## <a name="before-you-begin"></a>开始之前

为了更有效地排除问题故障，创建更详细的日志以供查看可能会有所帮助。

对于 Visual Studio 扩展，请将 `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` 环境变量设置为 1。 请务必重新启动 Visual Studio 以使环境变量生效。 启用后，详细日志将写入到 `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` 目录。

在 CLI 中，可以通过使用 `--verbose` 切换在命令执行过程中输出更多信息。 还可以在 `%TEMP%\Azure Dev Spaces` 中浏览更详细的日志。 在 Mac 上，可以通过在终端窗口中运行 `echo $TMPDIR` 来找到*TEMP*目录。 在 Linux 计算机上， *TEMP*目录通常 `/tmp`。 此外，请验证[Azure CLI 配置文件](/cli/azure/azure-cli-configuration?view=azure-cli-latest#cli-configuration-values-and-environment-variables)中是否已启用日志记录。

调试单个实例或 pod 时，Azure Dev Spaces 也最适合。 `azds.yaml` 文件包含一个设置*replicaCount*，用于指示 Kubernetes 为服务运行的 pod 数。 如果更改*replicaCount*以将应用程序配置为针对给定服务运行多个 pod，则调试器将在按字母顺序列出时附加到第一个 pod。 如果回收原始 Pod，调试程序会附加到其他 pod，这可能会导致意外行为发生。

## <a name="common-issues-when-enabling-azure-dev-spaces"></a>启用 Azure Dev Spaces 时遇到的常见问题

### <a name="error-failed-to-create-azure-dev-spaces-controller"></a>"无法创建 Azure Dev Spaces 控制器" 错误

控制器创建出现问题时，可能会看到此错误。 如果这是暂时性错误，请通过删除并重新创建控制器来修复错误。

你还可以尝试删除控制器：

```bash
azds remove -g <resource group name> -n <cluster name>
```

使用 Azure Dev Spaces CLI 删除控制器。 不能从 Visual Studio 中删除控制器。 你也无法在 Azure Cloud Shell 中安装 Azure Dev Spaces CLI，因此无法从 Azure Cloud Shell 中删除控制器。

如果尚未安装 Azure Dev Spaces CLI，可以先使用以下命令安装它，然后删除控制器：

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

可以从 CLI 或 Visual Studio 重新创建该控制器。 有关示例，请参阅[团队开发](quickstart-team-development.md)或[通过 .net Core](quickstart-netcore-visualstudio.md)快速入门进行开发。

### <a name="controller-create-failing-because-of-controller-name-length"></a>由于控制器名称长度，控制器创建失败

Azure Dev Spaces 控制器的名称长度不能超过31个字符。 如果在 AKS 群集上启用 Dev 空间或创建控制器时，控制器的名称超过31个字符，则会收到错误。 例如：

```console
Failed to create a Dev Spaces controller for cluster 'a-controller-name-that-is-way-too-long-aks-east-us': Azure Dev Spaces Controller name 'a-controller-name-that-is-way-too-long-aks-east-us' is invalid. Constraint(s) violated: Azure Dev Spaces Controller names can only be at most 31 characters long*
```

若要解决此问题，请使用备用名称创建控制器。 例如：

```cmd
azds controller create --name my-controller --target-name MyAKS --resource-group MyResourceGroup
```

### <a name="enabling-dev-spaces-failing-when-windows-node-pools-are-added-to-an-aks-cluster"></a>将 Windows 节点池添加到 AKS 群集时启用 Dev 空间失败

目前，Azure Dev Spaces 仅适用于 Linux pod 和节点。 如果你有一个具有 Windows 节点池的 AKS 群集，则必须确保仅在 Linux 节点上计划 Azure Dev Spaces pod。 如果 Azure Dev Spaces pod 计划在 Windows 节点上运行，则该 pod 将无法启动，并且将无法启用 Dev 空间。

若要解决此问题，请[将破坏添加](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations)到 AKS 群集，以确保不会计划在 Windows 节点上运行 Linux pod。

### <a name="error-found-no-untainted-linux-nodes-in-ready-state-on-the-cluster-there-needs-to-be-at-least-one-untainted-linux-node-in-ready-state-to-deploy-pods-in-azds-namespace"></a>错误：在群集上找不到就绪状态的原始 Linux 节点。 至少需要有一个原始 Linux 节点处于就绪状态，才能在 "azds" 命名空间中部署 pod。

Azure Dev Spaces 无法在 AKS 群集上创建控制器，因为它找不到*就绪*状态的原始节点来计划盒。 Azure Dev Spaces 要求至少一个处于 "*就绪*" 状态的 Linux 节点，以便在不指定 tolerations 的情况下计划 pod。

若要解决此问题，请更新 AKS 群集上的[破坏配置](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations)，以确保至少有一个 Linux 节点在没有指定 tolerations 的情况下计划 pod。 另外，请确保至少有一个允许计划在不指定 tolerations 的情况下进行计划的 Linux 节点处于 "*就绪*" 状态。 如果节点需要很长时间才能达到 "*就绪*" 状态，则可以尝试重启节点。

### <a name="error-azure-dev-spaces-cli-not-installed-properly-when-running-az-aks-use-dev-spaces"></a>运行 `az aks use-dev-spaces` 时出现 "Azure Dev Spaces CLI 未正确安装" 错误

Azure Dev Spaces CLI 的更新更改了它的安装路径。 如果你使用的 Azure CLI 版本早于2.0.63，则可能会看到此错误。 若要显示 Azure CLI 的版本，请使用 `az --version`。

```bash
$ az --version
azure-cli                         2.0.60 *
...
```

尽管使用2.0.63 之前的 Azure CLI 版本运行 `az aks use-dev-spaces` 时出现错误消息，但安装会成功。 您可以继续使用 `azds`，而不会出现任何问题。

若要解决此问题，请将[Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)安装更新到2.0.63 或更高版本。 此更新将解决在 `az aks use-dev-spaces`运行时收到的错误消息。 或者，你可以继续使用当前版本的 Azure CLI 和 Azure Dev Spaces CLI。

### <a name="error-unable-to-reach-kube-apiserver"></a>"无法访问 kube-apiserver" 错误

当 Azure Dev Spaces 无法连接到 AKS 群集的 API 服务器时，可能会看到此错误。 

如果已锁定对 AKS 群集 API 服务器的访问，或者如果已为 AKS 群集启用[API 服务器授权的 IP 地址范围](../aks/api-server-authorized-ip-ranges.md)，则还必须[创建](../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled)或[更新](../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges)群集，以[允许基于你的区域的其他范围](https://github.com/Azure/dev-spaces/tree/master/public-ips)。

通过运行 kubectl 命令确保 API 服务器可用。 如果 API 服务器不可用，请联系 AKS 支持部门，然后在 API 服务器正常工作时重试。

## <a name="common-issues-when-preparing-your-project-for-azure-dev-spaces"></a>为 Azure Dev Spaces 准备项目时遇到的常见问题

### <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>警告 "由于语言不受支持，无法生成 Dockerfile"
Azure Dev Spaces 为 C# 和 Node.js 提供本机支持。 当使用以这些语言之一编写的代码在目录中运行 `azds prep` 时，Azure Dev Spaces 会自动为你创建适当的 Dockerfile。

你仍可以将 Azure Dev Spaces 与用其他语言编写的代码配合使用，但你需要在首次运行 `azds up` 之前手动创建 Dockerfile。

如果你的应用程序以 Azure Dev Spaces 不能以本机方式支持的语言编写，则需要提供适当的 Dockerfile，以生成运行代码的容器映像。 Docker 提供[关于编写 Dockerfile 的最佳做法列表](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)和 [Dockerfile 参考](https://docs.docker.com/engine/reference/builder/)，有助于编写满足你需求的 Dockerfile。

准备好适当的 Dockerfile 后，运行 `azds up` 在 Azure Dev Spaces 中运行应用程序。

## <a name="common-issues-when-starting-or-stopping-services-with-azure-dev-spaces"></a>用 Azure Dev Spaces 启动或停止服务时遇到的常见问题

### <a name="error-config-file-not-found"></a>"找不到配置文件：" 错误

运行 `azds up`时，可能会看到此错误。 `azds up` 和 `azds prep` 必须从要在开发环境中运行的项目的根目录中运行。

解决此问题：
1. 将当前目录切换到包含服务代码的根文件夹。 
1. 如果代码文件夹中没有_azds_文件，则运行 `azds prep` 以生成 Docker、Kubernetes 和 Azure Dev Spaces 资产。

### <a name="timeout-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>"正在等待容器映像生成 ..." 处的超时AKS 虚拟节点的步骤

当你尝试使用 Dev 空间运行配置为在[AKS 虚拟节点](https://docs.microsoft.com/azure/aks/virtual-nodes-portal)上运行的服务时，将发生此超时。 开发人员空间目前不支持在虚拟节点上构建或调试服务。

如果使用 `azds up` 开关运行 `--verbose`，或在 Visual Studio 中启用详细日志记录，便会看到其他详细信息：

```cmd
$ azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

上面的命令显示，将服务的 pod 分配给*虚拟节点*，即虚拟节点。

若要解决此问题，请更新服务的 Helm 图表，以删除允许服务在虚拟节点上运行的任何*nodeSelector*或*tolerations*值。 这些值通常是在图表的 `values.yaml` 文件中进行定义。

你仍可以使用启用了虚拟节点功能的 AKS 群集，前提是你希望通过 Dev 空间生成或调试的服务在 VM 节点上运行。 在 VM 节点上运行具有开发人员空间的服务是默认配置。

### <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>启动 Dev 空间时出现 "找不到就绪的 tiller pod" 错误

如果 Helm 客户端无法再与群集中运行的 Tiller Pod 通信，则会发生此错误。

若要解决此问题，请重新启动群集中的代理节点。

### <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>错误 "release azds-\<标识符\>-\<spacename\>-\<servicename\> 失败：服务"\<servicename\>"已存在" 或 "拒绝请求 \<servicename\>，存储库不存在，或者可能需要" docker login "

如果在同一开发人员空间内混合运行直接 Helm 命令（例如 `helm install`、`helm upgrade`或 `helm delete`）和 Dev Spaces 命令（如 `azds up` 和 `azds down`），则可能会发生这些错误。 之所以发生这种情况，是因为 Dev 空间有自己的 Tiller 实例，这与您在同一 Dev 空间中运行的自己的 Tiller 实例冲突。

对于同一个 AKS 群集，可以同时使用 Helm 命令和 Dev Spaces 命令，但每个启用了空间的启用空间的命名空间应使用其中一种。

例如，假设你使用 Helm 命令在父开发人员空间中运行整个应用程序。 您可以创建该父级的子开发人员空间，使用 Dev 空间在子开发人员空间内运行单个服务，并同时测试这些服务。 准备好签入更改时，请使用 Helm 命令将更新的代码部署到父开发人员空间。 不要使用 `azds up` 在父开发人员空间中运行更新的服务，因为它将与使用 Helm 的服务最初运行的服务发生冲突。

### <a name="existing-dockerfile-not-used-to-build-a-container"></a>现有 Dockerfile 不用于构建容器

Azure Dev Spaces 可以配置为指向项目中的特定 _Dockerfile_。 如果 Azure Dev Spaces 看起来并未使用预期的 Dockerfile 来生成容器，可能需要显式指示 Azure Dev Spaces 要使用哪个 Dockerfile。 

若要解决此问题，请打开 Azure Dev Spaces 在项目中生成的_yaml_文件。 更新*配置：开发：生成： dockerfile*指向要使用的 dockerfile。 例如：

```yaml
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

### <a name="error-unauthorized-authentication-required-when-trying-to-use-a-docker-image-from-a-private-registry"></a>尝试使用专用注册表中的 Docker 映像时出现 "未授权：需要身份验证" 错误

你使用的是需要进行身份验证的专用注册表中的 Docker 映像。

若要解决此问题，可以使用[imagePullSecrets](https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets)通过此专用注册表允许开发人员共享空间，并请求映像。 若要使用 imagePullSecrets，请在使用映像的命名空间中[创建 Kubernetes 机密](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod)。 然后提供机密作为 `azds.yaml`中的 imagePullSecret。

下面是在 `azds.yaml`中指定 imagePullSecrets 的示例。

```yaml
kind: helm-release
apiVersion: 1.1
build:
  context: $BUILD_CONTEXT$
  dockerfile: Dockerfile
install:
  chart: $CHART_DIR$
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    # Optional, specify an array of imagePullSecrets. These secrets must be manually created in the namespace.
    # This will override the imagePullSecrets array in values.yaml file.
    # If the dockerfile specifies any private registry, the imagePullSecret for the registry must be added here.
    # ref: https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod
    #
    # This uses credentials from secret "myRegistryKeySecretName".
    imagePullSecrets:
      - name: myRegistryKeySecretName
```

> [!IMPORTANT]
> 在 `azds.yaml` 中设置 imagePullSecrets 将覆盖 `values.yaml`中指定的 imagePullSecrets。

### <a name="error-service-cannot-be-started"></a>错误 "服务无法启动"。

当服务代码无法启动时，你可能会看到此错误。 原因通常在用户代码中。 若要获取更多诊断信息，请在启动服务时启用更详细的日志记录。

在命令行中，使用 `--verbose` 来启用更详细的日志记录。 你还可以使用 `--output`指定输出格式。 例如：

```cmd
azds up --verbose --output json
```

在 Visual Studio 中：

1. 打开“工具”>“选项”，在“项目和解决方案”下，选择“生成并运行”。
2. 将“MSBuild 项目生成输出详细级别”的设置更改为“详细”或“诊断”。

    ![“工具选项”对话框的屏幕截图](media/common/VerbositySetting.PNG)

### <a name="rerunning-a-service-after-controller-re-creation"></a>在重新创建控制器后重新运行服务

如果尝试在删除并重新创建与此群集关联的 Azure Dev Spaces 控制器后重新运行服务，看到错误消息“无法启动服务”。 在这种情况下，详细输出包含以下文本：

```cmd
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

发生此错误的原因是，删除 Dev 空间控制器不会删除该控制器先前安装的服务。 因为旧服务仍然存在，所以在重新创建控制器后尝试使用新控制器运行服务会失败。

若要解决此问题，请先使用 `kubectl delete` 命令手动从群集中删除旧服务，再重新运行 Dev Spaces 来安装新服务。

### <a name="error-service-cannot-be-started-when-using-multi-stage-dockerfiles"></a>错误 "服务无法启动"。 使用多阶段 Dockerfile 时

使用多阶段 Dockerfile 时，看到错误消息“无法启动服务”。 在这种情况下，详细输出包含以下文本：

```cmd
$ azds up -v
Using dev space 'default' with target 'AksClusterName'
Synchronizing files...6s
Installing Helm chart...2s
Waiting for container image build...10s
Building container image...
Step 1/12 : FROM [imagename:tag] AS base
Error parsing reference: "[imagename:tag] AS base" is not a valid repository/tag: invalid reference format
Failed to build container image.
Service cannot be started.
```

之所以发生此错误，是因为 Azure Dev Spaces 当前不支持多阶段生成。 为了避免多阶段生成，请重写 Dockerfile。

### <a name="network-traffic-is-not-forwarded-to-your-aks-cluster-when-connecting-your-development-machine"></a>连接开发计算机时，不会将网络流量转发到 AKS 群集

使用[Azure Dev Spaces 将 AKS 群集连接到您的开发计算机](how-to/connect.md)时，您可能会遇到这样的问题：不会将网络流量转发到您的开发计算机与 AKS 群集之间。

将开发计算机连接到 AKS 群集时，Azure Dev Spaces 通过修改开发计算机的 `hosts` 文件，在 AKS 群集与开发计算机之间转发网络流量。 Azure Dev Spaces 使用要替换为主机名的 Kubernetes 服务的地址在 `hosts` 中创建一个条目。 此条目与端口转发一起用于定向开发计算机与 AKS 群集之间的网络流量。 如果开发计算机上的服务与要替换的 Kubernetes 服务的端口发生冲突，Azure Dev Spaces 无法转发 Kubernetes 服务的网络流量。 例如， *Windows BranchCache*服务通常绑定到*0.0.0.0： 80*，这种冲突会导致所有本地 ip 上的端口80发生冲突。

若要解决此问题，需要停止与要替换的 Kubernetes 服务的端口发生冲突的任何服务或进程。 您可以使用一些工具（例如*netstat*）检查您的开发计算机上的哪些服务或进程发生冲突。

例如，若要停止并禁用*Windows BranchCache*服务，请执行以下操作：
* 在命令提示符下运行 `services.msc`。
* 右键单击*BranchCache* ，然后选择 "*属性*"。
* 单击 "*停止*"。
* 或者，你可以通过将*启动类型*设置为 "*已禁用*" 来禁用它。
* 单击“确定”。

## <a name="common-issues-using-visual-studio-and-visual-studio-code-with-azure-dev-spaces"></a>使用 Visual Studio 和 Visual Studio Code 与 Azure Dev Spaces 时遇到的常见问题

### <a name="error-required-tools-and-configurations-are-missing"></a>错误 "缺少所需的工具和配置"

启动 VS Code 时可能会发生此错误：“[Azure Dev Spaces] 缺少生成和调试 '[项目名称]' 所需的工具和配置。”
此错误意味着 azds.exe 不在 PATH 环境变量中，如 VS Code 中所示。

尝试从正确设置路径环境变量的命令提示符启动 VS Code。

### <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>错误“用来生成和调试 ‘projectname’ 的必需工具已过期。”

如果使用的 zure Dev Spaces VS Code 扩展版本较高，但 Azure Dev Spaces CLI 的版本较低，便会在 Visual Studio Code 中看到此错误。

尝试下载并安装最新版本的 Azure Dev Spaces CLI：

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

### <a name="error-failed-to-find-debugger-extension-for-typecoreclr"></a>错误： "找不到类型的调试器扩展： coreclr"

运行 Visual Studio Code 调试程序时，可能会看到此错误。 你的开发计算机上可能没有C#安装 VS Code 扩展。 此C#扩展包括对 .net Core （CoreCLR）的调试支持。

若要解决此问题，请安装的[VS Code C#扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)。

### <a name="error-configured-debug-type-coreclr-is-not-supported"></a>错误 "不支持配置的调试类型 ' coreclr '"

运行 Visual Studio Code 调试程序时，可能会看到此错误。 你可能没有在开发计算机上安装 Azure Dev Spaces 的 VS Code 扩展。

若要解决此问题，请安装[Azure Dev Spaces 的 VS Code 扩展](get-started-netcore.md)。

### <a name="error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>错误 "cwd" 值 "/src" 无效。 系统找不到指定的文件。” 或者“launch: program‘/src/[项目二进制文件的路径]’不存在”

运行 Visual Studio Code 调试程序时，可能会看到此错误。 默认情况下，VS Code 扩展使用 `src` 作为项目在容器上的工作目录。 如果你已更新了 `Dockerfile` 来指定一个不同的工作目录，则可能会看到此错误。

若要解决此问题，请更新项目文件夹的 `.vscode` 子目录下的 `launch.json` 文件。 更改 `configurations->cwd` 指令，以指向与在项目的 `WORKDIR` 中定义的 `Dockerfile` 相同的目录。 可能还需要更新 `configurations->program` 指令。

### <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>错误： "管道程序 ' azds ' 意外退出，代码为126。"

运行 Visual Studio Code 调试程序时，可能会看到此错误。

若要解决此问题，请关闭并重新打开 Visual Studio Code。 重新启动调试器。

### <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>将调试附加到 node.js 应用程序时出现错误 "内部监视失败：监视 ENOSPC"

如果正在使用调试程序尝试附加到的 node.js 应用程序的节点的节点已超过*max_user_watches inotify* ，则会出现此错误。 在某些情况下， [ *max_user_watches inotify*的默认值可能太小，无法直接将调试器附加到 pod](https://github.com/Azure/AKS/issues/772)。

此问题的一种临时解决方法是增加群集中每个节点上的*max_user_watches inotify*的值，然后重新启动该节点以使更改生效。

## <a name="other-common-issues"></a>其他常见问题

### <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>错误 "azds" 未被识别为内部或外部命令、可使用的程序或批处理文件

如果 `azds.exe` 未安装或未正确配置，则可能发生此错误。

解决此问题：

1. 检查位置% ProgramFiles%/Microsoft SDKs\Azure\Azure Dev Spaces CLI for `azds.exe`。 如果它在那里，将该位置添加到 PATH 环境变量。
2. 如果未安装 `azds.exe`，请运行以下命令：

    ```cmd
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

### <a name="authorization-error-microsoftdevspacesregisteraction"></a>授权错误 "DevSpaces/register/action"

必须对 Azure 订阅拥有“所有者”或“参与者”访问权限，才能管理 Azure Dev Spaces。 如果尝试管理开发共享空间，并且没有*所有者*或*参与者*访问关联的 Azure 订阅，可能会出现授权错误。 例如：

```console
The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.
```

若要解决此问题，请使用具有*所有者*或*参与者*访问 Azure 订阅的帐户，手动注册 `Microsoft.DevSpaces` 命名空间：

```console
az provider register --namespace Microsoft.DevSpaces
```

### <a name="new-pods-arent-starting"></a>无法启动新的 pod

由于在群集中对*群集管理员*角色进行了 RBAC 权限更改，Kubernetes 初始值设定项无法将 PodSpec 应用于新的 pod。 新的 pod 还可能具有无效的 PodSpec，例如，与 pod 关联的服务帐户不再存在。 若要查看由于初始值设定项问题而处于*挂起*状态的 pod，请使用 `kubectl get pods` 命令：

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

此问题可能会影响群集中*所有命名空间*中的 pod，包括未启用 Azure Dev Spaces 的命名空间。

若要解决此问题，请将[Dev SPACES CLI 更新到最新版本](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools)，然后从 Azure Dev Spaces 控制器中删除*azds InitializerConfiguration* ：

```bash
az aks get-credentials --resource-group <resource group name> --name <cluster name>
kubectl delete InitializerConfiguration azds
```

删除 Azure Dev Spaces 控制器中的*Azds InitializerConfiguration*后，请使用 `kubectl delete` 删除处于*挂起*状态的所有 pod。 删除所有挂起的 pod 后，请重新部署你的 pod。

重新部署后，如果新的 pod 仍处于*挂起*状态，请使用 `kubectl delete` 删除处于*挂起*状态的所有 pod。 删除所有挂起的 pod 后，将其从群集中删除，然后重新安装它：

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

重新安装控制器后，请重新部署你的 pod。

### <a name="incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>调用 Dev 空间控制器和 Api 的 RBAC 权限不正确

访问 Azure Dev Spaces 控制器的用户必须具有读取 AKS 群集上的管理*kubeconfig*的访问权限。 例如，[内置的 Azure Kubernetes Service 群集管理角色](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions)中提供了此权限。 访问 Azure Dev Spaces 控制器的用户还必须具有该控制器的*参与者*或*所有者*RBAC 角色。 [此处](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group)提供了有关更新 AKS 群集的用户权限的详细信息。

更新控制器的用户 RBAC 角色：

1. 通过 https://portal.azure.com 登录到 Azure 门户。
1. 导航至包含控制器的资源组，该控制器通常与 AKS 群集相同。
1. 启用 "*显示隐藏的类型*" 复选框。
1. 单击控制器。
1. 打开 "*访问控制（IAM）* " 窗格。
1. 单击 "*角色分配*" 选项卡。
1. 单击 "*添加*"，然后*添加角色分配*。
    * 对于 "*角色*"，请选择 "*参与者*" 或 "*所有者*"。
    * 对于“分配访问权限至”，选择“Azure AD 用户、组或服务主体”。
    * 对于 "*选择*"，请搜索要为其授予权限的用户。
1. 单击“ *保存*”。

### <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>对与 Dev Spaces 服务关联的公用 URL 进行 DNS 名称解析失败

可以将 `--enable-ingress` 开关指定为 `azds prep` 命令，或选中 Visual Studio 内的 `Publicly Accessible` 复选框，从而配置服务的公共 URL 终结点。 当你在 Dev Spaces 中运行服务时，公共 DNS 名称自动注册。 如果此 DNS 名称未注册，你在连接到公共 URL 时，在 Web 浏览器中看到“无法显示网页”或“无法访问网站”错误消息。

解决此问题：

* 检查与开发共享空间服务关联的所有 Url 的状态：

  ```console
  azds list-uris
  ```

* 如果 URL 处于*挂起*状态，则开发人员空间仍在等待 DNS 注册完成。 有时，注册需要几分钟的时间才能完成。 Dev Spaces 还为每个服务打开一个本地主机隧道，在等待 DNS 注册时你可以使用该隧道。
* 如果 URL 保持“挂起”状态超过 5 分钟，可能表明创建公共终结点的外部 DNS Pod 有问题，或获取公共终结点的 nginx 入口控制器 Pod 有问题。 使用以下命令删除这些 pod，并允许 AKS 自动重新创建它们：
  ```console
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
  ```

### <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>错误 "标头之前上游连接错误或断开/重置"

可能会在尝试访问服务时看到此错误。 例如，可能会在浏览器中访问服务的 URL 时看到此错误。 此错误表示容器端口不可用。 这可能是由于以下原因：

* 容器仍在生成和部署过程中。 如果先运行 `azds up` 或启动调试器，然后在成功部署容器之前尝试访问容器，则会出现此问题。
* 端口配置在 _Dockerfile_、Helm 图表以及任何用于打开端口的服务器代码中不一致。

解决此问题：

1. 如果容器正在生成/部署过程中，则可等待 2-3 秒，然后尝试再次访问服务。 
1. 检查以下资产中的端口配置：
    * **[Helm 图](https://docs.helm.sh)：** 由 `service.port` 通过 `azds prep` 命令的 yaml 基架中的 `deployment.containerPort` 指定。
    * 在应用程序代码（例如，Node.js 的 `var server = app.listen(80, function () {...}` ）中打开的任何端口

### <a name="the-type-or-namespace-name-mylibrary-couldnt-be-found"></a>找不到类型或命名空间名称 "Mylibrary.dll"

找不到您正在使用的库项目。 对于 Dev 空间，默认情况下，生成上下文在项目/服务级别。  

解决此问题：

1. 修改 `azds.yaml` 文件，将生成上下文设置为解决方案级别。
2. 修改 `Dockerfile` 和 `Dockerfile.develop` 文件以引用项目文件，例如 `.csproj`，与新的生成上下文正确关联。
3. 将 `.dockerignore` 添加到 `.sln` 文件所在的目录中。
4. 根据需要，用其他项更新 `.dockerignore`。

可在[此处](https://github.com/sgreenmsft/buildcontextsample)找到示例。

### <a name="horizontal-pod-autoscaling-not-working-in-a-dev-space"></a>横向 pod 自动缩放不能在开发环境中工作

当你在开发环境中运行服务时，该服务的 pod 会[注入额外的用于检测的容器](how-dev-spaces-works.md#prepare-your-aks-cluster)，并且 pod 中的所有容器都需要为水平 Pod 自动缩放设置资源限制和请求。

若要解决此问题，请对注入的 Dev Spaces 容器应用资源请求和限制。 可以通过将 `azds.io/proxy-resources` 批注添加到 pod 规范来对注入的容器（devspaces）应用资源请求和限制。应将该值设置为一个 JSON 对象，该对象表示代理的容器规范的资源部分。

下面是要应用于 pod 规范的代理资源批注的示例。
```
azds.io/proxy-resources: "{\"Limits\": {\"cpu\": \"300m\",\"memory\": \"400Mi\"},\"Requests\": {\"cpu\": \"150m\",\"memory\": \"200Mi\"}}"
```

### <a name="enable-azure-dev-spaces-on-an-existing-namespace-with-running-pods"></a>在具有运行的 pod 的现有命名空间上启用 Azure Dev Spaces

你可能会有一个现有的 AKS 群集和命名空间，其中包含要在其中启用 Azure Dev Spaces 的运行中的 pod。

若要在 AKS 群集中的现有命名空间上启用 Azure Dev Spaces，请运行 `use-dev-spaces` 并使用 `kubectl` 重新启动该命名空间中的所有 pod。

```console
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space my-namespace --yes
kubectl -n my-namespace delete pod --all
```

在 pod 重新启动后，可以开始将现有命名空间用于 Azure Dev Spaces。

### <a name="enable-azure-dev-spaces-on-aks-cluster-with-restricted-egress-traffic-for-cluster-nodes"></a>启用群集节点的受限制传出流量的 AKS 群集上的 Azure Dev Spaces

若要在 AKS 群集上启用 Azure Dev Spaces，而这些群集节点的传出流量受到限制，则必须允许以下 Fqdn：

| FQDN                                    | 端口      | 用途      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS:443 | 请求 linux alpine 和其他 Azure Dev Spaces 映像 |
| gcr.io | HTTP：443 | 请求 helm/tiller 映像|
| storage.googleapis.com | HTTP：443 | 请求 helm/tiller 映像|
| azds-<guid>。<location>. azds.io | HTTPS:443 | 与控制器的 Azure Dev Spaces 后端服务进行通信。 可以在% USERPROFILE%\.azds\settings.json 的 "dataplaneFqdn" 中找到准确的 FQDN。|

### <a name="error-could-not-find-the-cluster-cluster-in-subscription-subscriptionid"></a>"找不到群集 \<群集\> 订阅中 \<subscriptionId"\>"

如果你的 kubeconfig 文件的目标不同于你尝试用于 Azure Dev Spaces 客户端工具的不同群集或订阅，你可能会看到此错误。 Azure Dev Spaces 客户端工具将复制*kubectl*的行为，该行为使用[一个或多个 kubeconfig 文件](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/)选择群集并与之进行通信。

解决此问题：

* 使用 `az aks use-dev-spaces -g <resource group name> -n <cluster name>` 更新当前上下文。 此命令还会启用 AKS 群集上的 Azure Dev Spaces （如果尚未启用）。 或者，您可以使用 `kubectl config use-context <cluster name>` 更新当前上下文。
* 使用 `az account show` 显示目标为当前 Azure 订阅，并验证此是否正确。 您可以使用 `az account set`更改目标订阅。

### <a name="error-using-dev-spaces-after-rotating-aks-certificates"></a>旋转 AKS 证书后使用 Dev 空格时出错

在[AKS 群集中旋转证书](../aks/certificate-rotation.md)后，某些操作（如 `azds space list` 和 `azds up`）将会失败。 在群集上循环证书后，还需要刷新 Azure Dev Spaces 控制器上的证书。

若要解决此问题，请确保你的*kubeconfig*已使用 `az aks get-credentials` 的证书，然后运行 `azds controller refresh-credentials` 命令。 例如：

```azurecli
az aks get-credentials -g <resource group name> -n <cluster name>
azds controller refresh-credentials -g <resource group name> -n <cluster name>
```
