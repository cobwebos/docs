---
title: 疑难解答
services: azure-dev-spaces
ms.date: 09/25/2019
ms.topic: troubleshooting
description: 了解如何在启用和使用 Azure 开发人员空间时排除故障并解决常见问题
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes 服务, 容器, Helm, 服务网格, 服务网格路由, kubectl, k8s '
ms.openlocfilehash: 9fcf14bf42fc843a126fea269038087ee7fb0c6c
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382047"
---
# <a name="azure-dev-spaces-troubleshooting"></a>Azure 开发空间故障排除

本指南介绍使用 Azure Dev Spaces 时可能会碰到的常见问题。

如果在使用 Azure 开发空间时遇到问题，请[在 Azure 开发空间 GitHub 存储库中创建问题](https://github.com/Azure/dev-spaces/issues)。

## <a name="before-you-begin"></a>开始之前

为了更有效地排除问题故障，创建更详细的日志以供查看可能会有所帮助。

对于 Visual Studio 扩展，请将 `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` 环境变量设置为 1。 请务必重新启动 Visual Studio 以使环境变量生效。 启用后，详细日志将写入到 `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` 目录。

在 CLI 中，可以通过使用 `--verbose` 切换在命令执行过程中输出更多信息。 还可以在 `%TEMP%\Azure Dev Spaces` 中浏览更详细的日志。 在 Mac 上*TEMP*，可以从终端窗口运行`echo $TMPDIR`找到 TEMP 目录。 在 Linux 计算机上 *，TEMP*目录通常是`/tmp`。 此外，请验证[Azure CLI 配置文件](/cli/azure/azure-cli-configuration?view=azure-cli-latest#cli-configuration-values-and-environment-variables)中是否启用了日志记录。

在调试单个实例或窗格时，Azure 开发人员空间也效果最佳。 该文件`azds.yaml`包含一个设置，*副本Count*，指示 Kubernetes 为您的服务运行的窗格数。 如果更改*副本Count*以将应用程序配置为为给定服务运行多个 pod，则调试器按字母顺序列出时附加到第一个 Pod。 如果回收原始 Pod，调试程序会附加到其他 pod，这可能会导致意外行为发生。

## <a name="common-issues-when-enabling-azure-dev-spaces"></a>启用 Azure 开发空间时的常见问题

### <a name="error-failed-to-create-azure-dev-spaces-controller"></a>错误"无法创建 Azure 开发空间控制器"

控制器创建出现问题时，可能会看到此错误。 如果这是暂时性错误，请通过删除并重新创建控制器来修复错误。

您还可以尝试删除控制器：

```bash
azds remove -g <resource group name> -n <cluster name>
```

使用 Azure 开发空间 CLI 删除控制器。 无法从可视化工作室中删除控制器。 也不能在 Azure 云外壳中安装 Azure 开发空间 CLI，因此无法从 Azure 云外壳中删除控制器。

如果未安装 Azure 开发空间 CLI，则可以首先使用以下命令安装它，然后删除控制器：

```azurecli
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

可以从 CLI 或 Visual Studio 重新创建该控制器。 有关示例，请参阅[团队开发](quickstart-team-development.md)或使用[.NET 核心快速入门进行开发](quickstart-netcore-visualstudio.md)。

### <a name="controller-create-failing-because-of-controller-name-length"></a>控制器创建失败，因为控制器名称长度

Azure 开发人员空间控制器的名称不能超过 31 个字符。 如果在 AKS 群集上启用开发人员空间或创建控制器时，控制器的名称超过 31 个字符，您将收到错误。 例如：

```console
Failed to create a Dev Spaces controller for cluster 'a-controller-name-that-is-way-too-long-aks-east-us': Azure Dev Spaces Controller name 'a-controller-name-that-is-way-too-long-aks-east-us' is invalid. Constraint(s) violated: Azure Dev Spaces Controller names can only be at most 31 characters long*
```

要解决此问题，请创建一个具有备用名称的控制器。 例如：

```cmd
azds controller create --name my-controller --target-name MyAKS --resource-group MyResourceGroup
```

### <a name="enabling-dev-spaces-failing-when-windows-node-pools-are-added-to-an-aks-cluster"></a>将 Windows 节点池添加到 AKS 群集时启用虚拟空间失败

目前，Azure 开发人员空间仅用于 Linux pod 和节点。 当您具有具有 Windows 节点池的 AKS 群集时，必须确保 Azure 开发人员空间窗格仅安排在 Linux 节点上。 如果 Azure 开发人员空间窗格计划在 Windows 节点上运行，则该窗格不会启动，启用开发人员空间将失败。

要解决此问题，请向 AKS 群集[添加污点](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations)，以确保 Linux pod 不会计划在 Windows 节点上运行。

### <a name="error-found-no-untainted-linux-nodes-in-ready-state-on-the-cluster-there-needs-to-be-at-least-one-untainted-linux-node-in-ready-state-to-deploy-pods-in-azds-namespace"></a>错误"未在群集上找到未受污染的 Linux 节点状态。 需要至少有一个未受污染的 Linux 节点处于"就绪"状态，才能在"azds"命名空间中部署 pod。

Azure 开发人员空间无法在 AKS 群集上创建控制器，因为它无法找到处于 *"就绪"* 状态的未受污染的节点来安排 pod。 Azure 开发人员空间至少需要一个*处于"就绪"* 状态的 Linux 节点，该节点允许在不指定节肢的情况下安排窗格。

要解决此问题，请更新 AKS 群集上的[污点配置](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations)，以确保至少有一个 Linux 节点允许在不指定节肢的情况下调度 pod。 此外，请确保至少有一个 Linux 节点允许在不指定节肢的情况下调度 pod 处于 *"就绪"* 状态。 如果节点需要很长时间才能达到 *"就绪"* 状态，则可以尝试重新启动节点。

### <a name="error-azure-dev-spaces-cli-not-installed-properly-when-running-az-aks-use-dev-spaces"></a>运行 az aks 使用开发空间时出错"Azure 开发空间 CLI 未正确安装"

对 Azure 开发空间 CLI 的更新更改了其安装路径。 如果您使用的 Azure CLI 版本早于 2.0.63，您可能会看到此错误。 要显示 Azure CLI 的版本，请使用`az --version`。

```azurecli
az --version
```

```output
azure-cli                         2.0.60 *
...
```

尽管在 2.0.63 之前使用 Azure CLI 版本运行时`az aks use-dev-spaces`出现错误消息，但安装确实成功。 您可以继续使用`azds`，没有任何问题。

要解决此问题，请将[Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)的安装更新为 2.0.63 或更高版本。 此更新将解决您在运行`az aks use-dev-spaces`时收到的错误消息。 或者，可以继续使用当前版本的 Azure CLI 和 Azure 开发空间 CLI。

### <a name="error-unable-to-reach-kube-apiserver"></a>错误"无法访问库贝-apiserver"

当 Azure 开发人员空间无法连接到 AKS 群集的 API 服务器时，您可能会看到此错误。

如果对 AKS 群集 API 服务器的访问被锁定，或者如果您为 AKS 群集启用了[API 服务器授权的 IP 地址范围](../aks/api-server-authorized-ip-ranges.md)，则还必须[创建](../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled)或[更新](../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges)群集，[以允许基于区域的其他范围](https://github.com/Azure/dev-spaces/tree/master/public-ips)。

通过运行 kubectl 命令确保 API 服务器可用。 如果 API 服务器不可用，请与 AKS 支持部门联系，并在 API 服务器工作时重试。

## <a name="common-issues-when-preparing-your-project-for-azure-dev-spaces"></a>为 Azure 开发空间准备项目时的常见问题

### <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>警告"由于不支持的语言，无法生成 Dockerfile"
Azure Dev Spaces 为 C# 和 Node.js 提供本机支持。 当您在包含`azds prep`以这些语言之一编写的代码的目录中运行时，Azure 开发人员空间会自动为您创建适当的 Dockerfile。

您仍然可以将 Azure 开发人员空间与其他语言编写的代码一起使用，但需要在第一次运行`azds up`之前手动创建 Dockerfile。

如果应用程序是用 Azure 开发人员空间不支持的语言编写的，则需要提供适当的 Dockerfile 来生成运行代码的容器映像。 Docker 提供[关于编写 Dockerfile 的最佳做法列表](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)和 [Dockerfile 参考](https://docs.docker.com/engine/reference/builder/)，有助于编写满足你需求的 Dockerfile。

获得适当的 Dockerfile 后，将运行`azds up`以在 Azure 开发空间中运行应用程序。

## <a name="common-issues-when-starting-or-stopping-services-with-azure-dev-spaces"></a>使用 Azure 开发空间启动或停止服务时的常见问题

### <a name="error-config-file-not-found"></a>错误"未找到"配置文件"：

运行`azds up`时，您可能会看到此错误。 `azds up`和`azds prep`都必须从要在开发空间中运行的项目的根目录运行。

解决此问题：
1. 将当前目录切换到包含服务代码的根文件夹。 
1. 如果代码文件夹中没有_azds.yaml_文件，请运行`azds prep`以生成 Docker、Kubernetes 和 Azure 开发人员空间资源。

### <a name="timeout-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>超时在"等待容器映像生成..."使用 AKS 虚拟节点的步骤

当您尝试使用开发人员空间运行配置为在[AKS 虚拟节点](https://docs.microsoft.com/azure/aks/virtual-nodes-portal)上运行的服务时，将发生此超时。 开发空间目前不支持在虚拟节点上构建或调试服务。

如果使用 `--verbose` 开关运行 `azds up`，或在 Visual Studio 中启用详细日志记录，便会看到其他详细信息：

```cmd
azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

上述命令显示服务的 pod 已分配给*虚拟节点 aci-linux，* 这是一个虚拟节点。

要解决此问题，请更新服务的 Helm 图表，以删除允许服务在虚拟节点上运行的任何*节点选择器*或*折服*值。 这些值通常是在图表的 `values.yaml` 文件中进行定义。

如果希望通过开发人员空间构建或调试的服务在 VM 节点上运行，您仍可以使用启用虚拟节点功能的 AKS 群集。 在 VM 节点上运行具有开发人员空间的服务是默认配置。

### <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>启动开发空间时出错"找不到现成的收银台"

如果 Helm 客户端无法再与群集中运行的 Tiller Pod 通信，则会发生此错误。

要解决此问题，请重新启动群集中的代理节点。

### <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>错误\<"释放 azds-\>-\<标识符\>-\<空间名称\>服务名称失败：\<服务'\>服务名称'已存在"或"服务\<名称\>的拉取访问被拒绝，存储库不存在或可能需要'docker 登录'"

如果将运行的直接 Helm`helm install`命令（如 、`helm upgrade`或`helm delete`） 与同一开发空间中的开发人员空间命令（`azds up`如`azds down`和 ）混合使用，则可能发生这些错误。 它们之所以发生，是因为开发人员空间有自己的 Tiller 实例，该实例与在同一开发空间中运行自己的 Tiller 实例冲突。

对同一 AKS 群集同时使用 Helm 命令和开发人员空间命令可以很好，但每个启用开发人员空间的命名空间都应使用其中一个或另一个。

例如，假设您使用 Helm 命令在父开发空间中运行整个应用程序。 可以从该父级创建子开发空间，使用开发人员空间在子开发空间内运行单个服务，并一起测试服务。 准备好签入更改后，请使用 Helm 命令将更新的代码部署到父开发空间。 不要使用 在`azds up`父开发空间中运行更新的服务，因为它将与最初使用 Helm 运行的服务冲突。

### <a name="existing-dockerfile-not-used-to-build-a-container"></a>不用于构建容器的现有 Docker 文件

可以将 Azure 开发人员空间配置为指向项目中的特定_Dockerfile。_ 如果 Azure Dev Spaces 看起来并未使用预期的 Dockerfile__ 来生成容器，可能需要显式指示 Azure Dev Spaces 要使用哪个 Dockerfile。 

要解决此问题，请打开 Azure 开发人员空间在项目中生成的_azds.yaml_文件。 更新*配置：开发：生成：dockerfile*指向要使用的 Dockerfile。 例如：

```yaml
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

### <a name="error-unauthorized-authentication-required-when-trying-to-use-a-docker-image-from-a-private-registry"></a>尝试从专用注册表使用 Docker 映像时出错"未授权：需要身份验证"

您使用的是来自需要身份验证的专用注册表的 Docker 映像。

要解决此问题，您可以允许开发人员空间使用[imagePullSecrets](https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets)进行身份验证并从该私有注册表中提取图像。 要使用图像PullSecrets，请在使用图像的命名空间中[创建 Kubernets 机密](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod)。 然后提供秘密作为图像拉秘密在`azds.yaml`。

下面是 在 中`azds.yaml`指定图像 PullSecrets 的示例。

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
> 在 中`azds.yaml`设置图像拉图秘密将覆盖 在`values.yaml`中指定的图像拉图秘密。

### <a name="error-service-cannot-be-started"></a>错误"无法启动服务"。

当服务代码无法启动时，你可能会看到此错误。 原因通常在用户代码中。 要获取更多诊断信息，请在启动服务时启用更详细的日志记录。

从命令行中，使用`--verbose`启用更详细的日志记录。 您还可以使用 指定输出格式`--output`。 例如：

```cmd
azds up --verbose --output json
```

在 Visual Studio 中：

1. 打开“工具”>“选项”****，在“项目和解决方案”**** 下，选择“生成并运行”****。
2. 将“MSBuild 项目生成输出详细级别”**** 的设置更改为“详细”**** 或“诊断”****。

    ![“工具选项”对话框的屏幕截图](media/common/VerbositySetting.PNG)

### <a name="rerunning-a-service-after-controller-re-creation"></a>在重新创建控制器后重新运行服务

如果尝试在删除并重新创建与此群集关联的 Azure Dev Spaces 控制器后重新运行服务，看到错误消息“无法启动服务”**。 在这种情况下，详细输出包含以下文本：

```output
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

出现此错误的原因是删除开发人员空间控制器不会删除该控制器以前安装的服务。 因为旧服务仍然存在，所以在重新创建控制器后尝试使用新控制器运行服务会失败。

若要解决此问题，请先使用 `kubectl delete` 命令手动从群集中删除旧服务，再重新运行 Dev Spaces 来安装新服务。

### <a name="error-service-cannot-be-started-when-using-multi-stage-dockerfiles"></a>错误"无法启动服务"。 使用多级 Dockerfile 时

使用多阶段 Dockerfile 时，看到错误消息“无法启动服务”**。 在这种情况下，详细输出包含以下文本：

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

出现此错误的原因是 Azure 开发空间当前不支持多阶段生成。 为了避免多阶段生成，请重写 Dockerfile。

### <a name="network-traffic-is-not-forwarded-to-your-aks-cluster-when-connecting-your-development-machine"></a>连接开发机器时，网络流量不会转发到 AKS 群集

当使用[Azure 开发人员空间将 AKS 群集连接到开发计算机时](how-to/connect.md)，可能会遇到开发计算机和 AKS 群集之间未转发网络流量的问题。

将开发计算机连接到 AKS 群集时，Azure 开发人员空间通过修改开发计算机`hosts`的文件，转发 AKS 群集和开发计算机之间的网络流量。 Azure 开发人员空间在 中`hosts`创建一个条目，该条目将替换为主机名的 Kubernetes 服务的地址。 此条目与端口转发一起使用，用于开发计算机和 AKS 群集之间的直接网络流量。 如果开发计算机上的服务与要替换的 Kubernetes 服务的端口冲突，则 Azure 开发人员空间无法转发 Kubernetes 服务的网络流量。 例如 *，Windows BranchCache*服务通常绑定到*0.0.0.0：80，* 这冲突将导致所有本地 IP 上的端口 80 冲突。

要解决此问题，您需要停止与您尝试替换的 Kubernetes 服务端口冲突的任何服务或进程。 您可以使用*netstat*等工具来检查开发计算机上的哪些服务或进程发生冲突。

例如，要停止和禁用*Windows 分支缓存*服务：
* 从`services.msc`命令提示符运行。
* 右键单击*分支缓存*并选择*属性*。
* 单击“停止”**。
* 或者，您可以通过将 *"启动"类型*设置为 *"已禁用*"来禁用它。
* 单击“确定”。 

### <a name="error-no-azureassignedidentity-found-for-podazdsazds-webhook-deployment-id-in-assigned-state"></a>错误"未找到为 pod 找到 Azure 分配标识：在分配状态下的 azds/azds-webhook-部署-ID"\<\>

在安装了[托管标识](../aks/use-managed-identity.md)和[pod 托管标识](../aks/developer-best-practices-pod-security.md#use-pod-managed-identities)的 AKS 群集上运行具有 Azure 开发人员空间的服务时，该过程可能会在*图表安装*步骤后挂起。 如果检查*azds*名称空间中的*阿兹-注入器-webhook，* 您可能会看到此错误。

群集上运行的 Azure 开发人员空间服务利用群集的托管标识与群集外部的 Azure 开发空间后端服务进行交谈。 安装 Pod 托管标识后，在群集的节点上配置网络规则，以将托管标识凭据的所有调用重定向到[群集上安装的节点托管标识 （NMI） 守护进程](https://github.com/Azure/aad-pod-identity#node-managed-identity)。 此 NMI 守护进程集标识调用窗格，并确保已正确标记 pod 以访问请求的托管标识。 Azure 开发人员空间无法检测群集是否安装了 Pod 托管标识，并且无法执行必要的配置以允许 Azure 开发人员空间服务访问群集的托管标识。 由于 Azure 开发人员空间服务尚未配置为访问群集的托管标识，因此 NMI 守护进程将不允许它们获取托管标识的 AAD 令牌，并且无法与 Azure 开发人员空间后端服务通信。

要解决此问题，请对*阿兹德-注入器-webhook*应用[AzurePodIdentityException，](https://github.com/Azure/aad-pod-identity/blob/master/docs/readmes/README.app-exception.md)并更新 Azure 开发人员空间检测的窗格以访问托管标识。

创建名为*webhookException.yaml*的文件并复制以下 YAML 定义：

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: azds-infrastructure-exception
  namespace: azds
spec:
  PodLabels:
    azds.io/uses-cluster-identity: "true"
```

上述文件为*azds-injector-webhook*创建一个*AzurePod 身份异常*对象。 要部署此对象，请使用`kubectl`：

```cmd
kubectl apply -f webhookException.yaml
```

要更新 Azure 开发人员空间检测的窗格以访问托管标识，请更新以下 YAML 定义中的*命名空间*，并`kubectl`用于为每个开发空间应用它。

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: azds-infrastructure-exception
  namespace: myNamespace
spec:
  PodLabels:
    azds.io/instrumented: "true"
```

或者，您可以创建*Azure 标识*和*Azure 标识绑定*对象，并更新在 Azure 开发人员空间检测的空间中运行的工作负载的窗格标签，以访问 AKS 群集创建的托管标识。

要列出托管标识的详细信息，请为 AKS 群集运行以下命令：

```azurecli
az aks show -g <resourcegroup> -n <cluster> -o json --query "{clientId: identityProfile.kubeletidentity.clientId, resourceId: identityProfile.kubeletidentity.resourceId}"
```

上述命令输出托管标识的*客户端 Id*和资源*Id。* 例如：

```json
{
  "clientId": "<clientId>",
  "resourceId": "/subscriptions/<subid>/resourcegroups/<resourcegroup>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<name>"
}
```

要创建*Azure 标识*对象，请创建名为*群集标识.yaml*的文件，并使用与上一命令中的托管标识的详细信息更新的以下 YAML 定义：

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentity
metadata:
  name: my-cluster-mi
spec:
  type: 0
  ResourceID: /subscriptions/<subid>/resourcegroups/<resourcegroup>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<name>
  ClientID: <clientId>
```

要创建*Azure 标识绑定*对象，请创建名为*群集标识绑定.yaml*的文件，并使用以下 YAML 定义：

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: my-cluster-mi-binding
spec:
  AzureIdentity: my-cluster-mi
  Selector: my-label-value
```

要部署*Azure 标识*和*Azure 标识绑定*对象，请使用 ： `kubectl`

```cmd
kubectl apply -f clusteridentity.yaml
kubectl apply -f clusteridentitybinding.yaml
```

部署 Azure*标识*和*Azure 标识绑定*对象后，具有*adapodid 绑定：我的标签值*标签的任何工作负荷都可以访问群集的托管标识。 添加此标签并重新部署在任何开发空间中运行的所有工作负载。 例如：

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: sample
        aadpodidbinding: my-label-value
    spec:
      [...]
```

## <a name="common-issues-using-visual-studio-and-visual-studio-code-with-azure-dev-spaces"></a>使用具有 Azure 开发空间的可视化工作室和可视化工作室代码的常见问题

### <a name="error-required-tools-and-configurations-are-missing"></a>错误"缺少所需的工具和配置"

启动 VS Code 时可能会发生此错误：“[Azure Dev Spaces] 缺少生成和调试 '[项目名称]' 所需的工具和配置。”
此错误意味着 azds.exe 不在 PATH 环境变量中，如 VS Code 中所示。

尝试从正确设置 PATH 环境变量的命令提示符启动 VS 代码。

### <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>错误“用来生成和调试 ‘projectname’ 的必需工具已过期。”

如果使用的 zure Dev Spaces VS Code 扩展版本较高，但 Azure Dev Spaces CLI 的版本较低，便会在 Visual Studio Code 中看到此错误。

尝试下载并安装最新版本的 Azure 开发空间 CLI：

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

### <a name="error-failed-to-find-debugger-extension-for-typecoreclr"></a>错误："找不到类型：coreclr 的调试器扩展失败"

运行可视化工作室代码调试器时，您可能会看到此错误。 您可能没有在开发计算机上安装 C# 的 VS 代码扩展。 C# 扩展包括对 .NET Core （CoreCLR） 的调试支持。

要解决此问题，请安装[C# 的 VS 代码扩展](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)。

### <a name="error-configured-debug-type-coreclr-is-not-supported"></a>错误"不支持配置的调试类型"coreclr"

运行可视化工作室代码调试器时，您可能会看到此错误。 您可能没有在开发计算机上安装 Azure 开发空间的 VS 代码扩展。

要解决此问题，请[安装 Azure 开发空间的 VS 代码扩展](get-started-netcore.md)。

### <a name="error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>错误"无效的"cwd"值"/src"。 系统找不到指定的文件。” 或者“launch: program‘/src/[项目二进制文件的路径]’不存在”

运行可视化工作室代码调试器时，您可能会看到此错误。 默认情况下，VS Code 扩展使用 `src` 作为项目在容器上的工作目录。 如果你已更新了 `Dockerfile` 来指定一个不同的工作目录，则可能会看到此错误。

要解决此问题，`launch.json`请更新项目文件夹子`.vscode`目录下的文件。 更改 `configurations->cwd` 指令，以指向与在项目的 `Dockerfile` 中定义的 `WORKDIR` 相同的目录。 可能还需要更新 `configurations->program` 指令。

### <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>错误"管道程序'azds'意外退出与代码126。

运行可视化工作室代码调试器时，您可能会看到此错误。

要解决此问题，请关闭并重新打开可视化工作室代码。 重新启动调试器。

### <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>将调试附加到 Node.js 应用程序时出错"内部监视失败：监视 ENOSPC"

当使用使用调试器尝试附加到的 Node.js 应用程序运行 pod 的节点超过*fs.inotify.max_user_watches*值时，将发生此错误。 在某些情况下[*，fs.inotify.max_user_watches*的默认值可能太小，无法处理将调试器直接附加到 pod](https://github.com/Azure/AKS/issues/772)。

此问题的临时解决方法是增加群集中每个节点上的*fs.inotify.max_user_watches*的值，并重新启动该节点，使更改生效。

## <a name="other-common-issues"></a>其他常见问题

### <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>错误"azds"不识别为内部或外部命令、可操作程序或批处理文件

如果未`azds.exe`正确安装或配置，则可能发生此错误。

解决此问题：

1. 检查位置 %程序文件%%/微软 SDK\Azure_Azure 开发空间 CLI。 `azds.exe` 如果它在那里，将该位置添加到 PATH 环境变量。
2. 如果未`azds.exe`安装，请运行以下命令：

    ```azurecli
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

### <a name="authorization-error-microsoftdevspacesregisteraction"></a>授权错误"微软.Devspace/注册/操作"

必须对 Azure 订阅拥有“所有者”** 或“参与者”** 访问权限，才能管理 Azure Dev Spaces。 如果您尝试管理开发空间，并且没有*所有者*或*参与者*对关联的 Azure 订阅的访问权限，则可能会看到授权错误。 例如：

```output
The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.
```

要解决此问题，请使用具有对 Azure 订阅*的所有者*或*参与者访问权限的*帐户，请手动注册`Microsoft.DevSpaces`命名空间：

```azurecli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="new-pods-arent-starting"></a>新窗格未启动

由于 RBAC 权限更改为群集中的*群集管理员*角色，Kubernetes 初始化器无法为新 pod 应用 PodSpec。 新窗格可能还有无效的 PodSpec，例如与该窗格关联的服务帐户不再存在。 要查看由于初始化问题而处于*挂起*状态的 pod，请使用以下`kubectl get pods`命令：

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

此问题可能会影响群集*中所有命名空间*中的窗格，包括未启用 Azure 开发空间的命名空间。

要解决此问题，请[将开发空间 CLI 更新到最新版本，](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools)然后从 Azure 开发人员空间控制器中删除*azds 初始化器配置*：

```azurecli
az aks get-credentials --resource-group <resource group name> --name <cluster name>
```

```bash
kubectl delete InitializerConfiguration azds
```

从 Azure 开发人员空间控制器中删除*azds 初始化器配置*后，`kubectl delete`使用 来删除处于*挂起*状态的任何窗格。 删除所有挂起的窗格后，重新部署您的窗格。

如果重新部署后新窗格仍停留在 *"挂起*"状态，请使用`kubectl delete`来删除处于*挂起*状态的任何窗格。 删除所有挂起的窗格后，从群集中删除控制器并重新安装它：

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

重新安装控制器后，重新部署窗格。

### <a name="incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>调用开发人员空间控制器和 API 的 RBAC 权限不正确

访问 Azure 开发人员空间控制器的用户必须有权访问 AKS 群集上的管理员*库贝康格*。 例如，此权限在内置 Azure[库伯奈斯服务群集管理员角色](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions)中可用。 访问 Azure 开发人员空间控制器的用户还必须具有控制器*的参与者*或*所有者*RBAC 角色。 有关更新用户对 AKS 群集的权限的更多详细信息[，请在此处](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group)获取。

要更新控制器的用户 RBAC 角色，请执行以下操作：

1. 通过 https://portal.azure.com 登录到 Azure 门户。
1. 导航到包含控制器的资源组，该控制器通常与您的 AKS 群集相同。
1. 启用"*显示隐藏类型*"复选框。
1. 单击控制器。
1. 打开*访问控制 （IAM）* 窗格。
1. 单击"*角色分配"* 选项卡。
1. 单击"*添加*"然后*添加角色分配*。
    * 对于*角色*，请选择 *"参与者*"或"*所有者*"。
    * 对于“分配访问权限至”，选择“Azure AD 用户、组或服务主体”****。
    * 对于 *"选择*"，搜索要授予权限的用户。
1. 单击“ *保存*”。

### <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>对与 Dev Spaces 服务关联的公用 URL 进行 DNS 名称解析失败

可以将 `--enable-ingress` 开关指定为 `azds prep` 命令，或选中 Visual Studio 内的 `Publicly Accessible` 复选框，从而配置服务的公共 URL 终结点。 当你在 Dev Spaces 中运行服务时，公共 DNS 名称自动注册。 如果此 DNS 名称未注册，你在连接到公共 URL 时，在 Web 浏览器中看到“无法显示网页”** 或“无法访问网站”** 错误消息。

解决此问题：

* 检查与您的开发空间服务关联的所有 URL 的状态：

  ```console
  azds list-uris
  ```

* 如果 URL 处于*挂起*状态，则开发人员空间仍在等待 DNS 注册完成。 有时，注册需要几分钟的时间才能完成。 Dev Spaces 还为每个服务打开一个本地主机隧道，在等待 DNS 注册时你可以使用该隧道。
* 如果 URL 保持“挂起”** 状态超过 5 分钟，可能表明创建公共终结点的外部 DNS Pod 有问题，或获取公共终结点的 nginx 入口控制器 Pod 有问题。 使用以下命令删除这些窗格并允许 AKS 自动重新创建它们：
  ```console
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
  ```

### <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>错误"上行连接错误或标头前断开连接/重置"

可能会在尝试访问服务时看到此错误。 例如，可能会在浏览器中访问服务的 URL 时看到此错误。 此错误表示容器端口不可用。 这可能是出于以下原因：

* 容器仍在生成和部署过程中。 如果先运行 `azds up` 或启动调试器，然后在成功部署容器之前尝试访问容器，则会出现此问题。
* 端口配置在 _Dockerfile_、Helm 图表以及任何用于打开端口的服务器代码中不一致。

解决此问题：

1. 如果容器正在生成/部署过程中，则可等待 2-3 秒，然后尝试再次访问服务。 
1. 检查端口配置（以下资产）：
    * **[赫尔姆图](https://docs.helm.sh)：** `service.port`由`deployment.containerPort`和 值 指定.yaml 脚手架，由`azds prep`命令指定。
    * 在应用程序代码（例如，Node.js 的 `var server = app.listen(80, function () {...}` ）中打开的任何端口

### <a name="the-type-or-namespace-name-mylibrary-couldnt-be-found"></a>找不到类型或命名空间名称"我的库"

找不到正在使用的库项目。 使用开发人员空间时，默认情况下生成上下文位于项目/服务级别。  

解决此问题：

1. 修改文件`azds.yaml`以将生成上下文设置为解决方案级别。
2. 修改`Dockerfile`和`Dockerfile.develop`文件以引用项目文件，例如`.csproj`，相对于新的生成上下文正确。
3. 在同一`.dockerignore`目录中添加 与文件相同的`.sln`目录中的 。
4. 根据需要`.dockerignore`更新 其他条目。

你可以[在这里](https://github.com/sgreenmsft/buildcontextsample)找到一个例子。

### <a name="horizontal-pod-autoscaling-not-working-in-a-dev-space"></a>水平窗格自动缩放在开发空间中不起作用

在开发空间中运行服务时，该服务的窗格将[注入用于检测的其他容器](how-dev-spaces-works-cluster-setup.md#prepare-your-aks-cluster)，并且 Pod 中的所有容器都需要为水平 Pod 自动缩放设置资源限制和请求。

要解决此问题，请应用资源请求并限制注入的开发人员空间容器。 通过将`azds.io/proxy-resources`注释添加到 pod 规范，可以针对注入的容器（开发空间代理）应用资源请求和限制。该值应设置为表示代理容器规范的资源部分的 JSON 对象。

下面是要应用于 pod 规范的代理资源注释的示例。
```
azds.io/proxy-resources: "{\"Limits\": {\"cpu\": \"300m\",\"memory\": \"400Mi\"},\"Requests\": {\"cpu\": \"150m\",\"memory\": \"200Mi\"}}"
```

### <a name="enable-azure-dev-spaces-on-an-existing-namespace-with-running-pods"></a>在现有命名空间上启用 Azure 开发人员空间，并运行窗格

您可能有一个现有的 AKS 群集和命名空间，其中具有正在运行的窗格，您希望在其中启用 Azure 开发空间。

要在 AKS 群集的现有命名空间上启用 Azure 开发空间`use-dev-spaces`，请`kubectl`运行并用于重新启动该命名空间中的所有窗格。

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space my-namespace --yes
```

```console
kubectl -n my-namespace delete pod --all
```

重新启动窗格后，可以开始使用现有命名空间和 Azure 开发空间。

### <a name="enable-azure-dev-spaces-on-aks-cluster-with-restricted-egress-traffic-for-cluster-nodes"></a>在 AKS 群集上启用 Azure 开发空间，群集节点的传出流量受限

要在限制群集节点出口流量的 AKS 群集上启用 Azure 开发空间，必须允许以下 FQDN：

| FQDN                                    | 端口      | 使用      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS：443 | 拉 linux 高山和其他 Azure 开发人员空间映像 |
| gcr.io | HTTP：443 | 拉舵/牵引器图像|
| storage.googleapis.com | HTTP：443 | 拉舵/牵引器图像|
| 阿兹兹-.<guid><location>.azds.io | HTTPS：443 | 与控制器的 Azure 开发空间后端服务进行通信。 确切的 FQDN 可在 %USERPROFILE%\.azds_s_json 中的"数据平面Fqdn"中找到。|

### <a name="error-could-not-find-the-cluster-cluster-in-subscription-subscriptionid"></a>错误\<"在订阅\>\<Id\>中找不到群集"

如果 kubeconfig 文件的目标与尝试与 Azure Dev Spaces 客户端工具一起使用不同的群集或订阅，则可能会看到此错误。 Azure 开发人员空间客户端工具复制*kubectl*的行为，该函数使用[一个或多个库贝康菲克文件](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/)来选择群集并与之通信。

解决此问题：

* 用于`az aks use-dev-spaces -g <resource group name> -n <cluster name>`更新当前上下文。 如果尚未启用，此命令还会在 AKS 群集上启用 Azure 开发空间。 或者，您可以使用`kubectl config use-context <cluster name>`更新当前上下文。
* 用于`az account show`显示要定位的当前 Azure 订阅并验证这是否正确。 您可以使用 更改要定位的订阅`az account set`。

### <a name="error-using-dev-spaces-after-rotating-aks-certificates"></a>旋转 AKS 证书后使用开发空间的错误

[旋转 AKS 群集中的证书](../aks/certificate-rotation.md)后，某些操作（如`azds space list`和`azds up`将失败）。 在旋转群集上的证书后，还需要刷新 Azure 开发人员空间控制器上的证书。

要解决此问题，请确保*kubeconfig*具有更新的证书，然后`az aks get-credentials`运行该`azds controller refresh-credentials`命令。 例如：

```azurecli
az aks get-credentials -g <resource group name> -n <cluster name>
```

```console
azds controller refresh-credentials -g <resource group name> -n <cluster name>
```
