---
title: 疑难解答
services: azure-dev-spaces
ms.date: 09/25/2019
ms.topic: troubleshooting
description: 了解如何排查和解决在启用和使用 Azure Dev Spaces 时遇到的常见问题
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes 服务, 容器, Helm, 服务网格, 服务网格路由, kubectl, k8s '
ms.openlocfilehash: 5d8bf69d456bca2a88b8aa2031d5ef0ba20f7c30
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91979113"
---
# <a name="azure-dev-spaces-troubleshooting"></a>Azure Dev Spaces 故障排除

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

本指南介绍使用 Azure Dev Spaces 时可能会碰到的常见问题。

如果在使用 Azure Dev Spaces 时遇到问题，请[在 Azure Dev Spaces GitHub 存储库中创建问题](https://github.com/Azure/dev-spaces/issues)。

## <a name="before-you-begin"></a>开始之前

为了更有效地排除问题故障，创建更详细的日志以供查看可能会有所帮助。

对于 Visual Studio，请将 `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` 环境变量设置为 1。 请务必重新启动 Visual Studio 以使环境变量生效。 启用后，详细日志将写入到 `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` 目录。

在 CLI 中，可以通过使用 `--verbose` 切换在命令执行过程中输出更多信息。 还可以在 `%TEMP%\Azure Dev Spaces` 中浏览更详细的日志。 在 Mac 上，可以通过在终端窗口中运行 `echo $TMPDIR` 来找到 TEMP 目录。 在 Linux 计算机上，TEMP 目录通常为 `/tmp`。 此外，还请验证是否已在 [Azure CLI 配置文件](/cli/azure/azure-cli-configuration?view=azure-cli-latest#cli-configuration-values-and-environment-variables)中启用了日志记录。

Azure Dev Spaces 在调试单个实例或 Pod 时也能发挥最佳效果。 `azds.yaml` 文件包含 replicaCount 设置，它指明了 Kubernetes 针对服务运行的 Pod 数。 如果你通过更改 replicaCount 将应用程序配置为针对给定服务运行多个 Pod，调试程序会附加到按字母顺序列出的第一个 Pod。 如果回收原始 Pod，调试程序会附加到其他 pod，这可能会导致意外行为发生。

## <a name="common-issues-when-enabling-azure-dev-spaces"></a>启用 Azure Dev Spaces 时遇到的常见问题

### <a name="error-failed-to-create-azure-dev-spaces-controller"></a>错误“无法创建 Azure Dev Spaces 控制器”

控制器创建出现问题时，可能会看到此错误。 如果这是暂时性错误，请通过删除并重新创建控制器来修复错误。

也可以尝试删除控制器：

```bash
azds remove -g <resource group name> -n <cluster name>
```

使用 Azure Dev Spaces CLI 删除控制器。 不能从 Visual Studio 中删除控制器。 你也不能在 Azure Cloud Shell 中安装 Azure Dev Spaces CLI，因此不能从 Azure Cloud Shell 中删除控制器。

如果还没有安装 Azure Dev Spaces CLI，可以先使用以下命令来安装它，再删除控制器：

```azurecli
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

### <a name="controller-create-failing-because-of-controller-name-length"></a>由于控制器名称长度问题，无法创建控制器

Azure Dev Spaces 控制器的名称长度不得超过 31 个字符。 当你在 AKS 群集上启用 Dev Spaces 或创建控制器时，如果控制器的名称超过 31 个字符，你就会看到此错误。 例如：

```console
Failed to create a Dev Spaces controller for cluster 'a-controller-name-that-is-way-too-long-aks-east-us': Azure Dev Spaces Controller name 'a-controller-name-that-is-way-too-long-aks-east-us' is invalid. Constraint(s) violated: Azure Dev Spaces Controller names can only be at most 31 characters long*
```

若要修复此问题，请创建具有备用名称的控制器。 例如：

```cmd
azds controller create --name my-controller --target-name MyAKS --resource-group MyResourceGroup
```

### <a name="enabling-dev-spaces-failing-when-windows-node-pools-are-added-to-an-aks-cluster"></a>当 Windows 节点池添加到 AKS 群集时，无法启用 Dev Spaces

目前，Azure Dev Spaces 只能在 Linux Pod 和节点上运行。 如果你有包含 Windows 节点池的 AKS 群集，必须确保 Azure Dev Spaces Pod 只计划在 Linux 节点上运行。 如果 Azure Dev Spaces Pod 计划在 Windows 节点上运行，那么 Pod 将不会启动，而且启用 Dev Spaces 也会失败。

若要修复此问题，请向 AKS 群集[添加污点](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations)，以确保 Linux Pod 未计划在 Windows 节点上运行。

### <a name="error-found-no-untainted-linux-nodes-in-ready-state-on-the-cluster-there-needs-to-be-at-least-one-untainted-linux-node-in-ready-state-to-deploy-pods-in-azds-namespace"></a>错误“在群集上找不到处于‘就绪’状态的没有污点的 Linux 节点。 至少必须有一个处于‘就绪’状态的没有污点的 Linux 节点，才能在 'azds' 命名空间中部署 Pod。”

Azure Dev Spaces 无法在 AKS 群集上创建控制器，因为它找不到处于“就绪”状态的没有污点的节点来计划在其中运行 Pod。 Azure Dev Spaces 要求至少必须有一个处于“就绪”状态的 Linux 节点，可便于在不指定容忍的情况下计划 Pod。

若要修复此问题，请[更新 AKS 群集上的污点配置](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations)，以确保至少有一个 Linux 节点可便于在不指定容忍的情况下计划 Pod。 此外，还请确保至少一个可便于在不指定容忍的情况下计划 Pod 的 Linux 节点处于“就绪”状态。 如果节点需要很长时间才能达到“就绪”状态，可以尝试重启节点。

### <a name="error-azure-dev-spaces-cli-not-installed-properly-when-running-az-aks-use-dev-spaces"></a>在运行 az aks use-dev-spaces 时看到错误“Azure Dev Spaces CLI 未正确安装”

对 Azure Dev Spaces CLI 的更新更改了它的安装路径。 如果使用的是低于版本 2.0.63 的 Azure CLI，可能会看到此错误。 若要查看 Azure CLI 版本，请使用 `az --version`。

```azurecli
az --version
```

```output
azure-cli                         2.0.60 *
...
```

尽管在使用低于版本 2.0.63 的 Azure CLI 运行 `az aks use-dev-spaces` 时看到此错误消息，但安装还是成功了。 可以继续毫无问题地使用 `azds`。

若要修复此问题，请将 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) 安装更新为 2.0.63 或更高版本。 在执行此更新后，就不会在运行 `az aks use-dev-spaces` 时看到这一错误消息了。 或者，也可以继续使用当前版本的 Azure CLI 和 Azure Dev Spaces CLI。

### <a name="error-unable-to-reach-kube-apiserver"></a>错误“无法访问 kube-apiserver”

当 Azure Dev Spaces 无法连接到 AKS 群集的 API 服务器时，你可能会看到此错误。

如果已锁定对 AKS 群集 API 服务器的访问，或者如果已为 AKS 群集启用了 [API 服务器授权的 IP 地址范围](../aks/api-server-authorized-ip-ranges.md) ，则还必须 [创建](../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled) 或 [更新](../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges) 群集，以 [允许基于你的区域的其他范围](configure-networking.md#aks-cluster-network-requirements)

通过运行 kubectl 命令来确保 API 服务器是可用的。 如果 API 服务器不可用，请联系 AKS 支持部门，然后在 API 服务器正常运行时重试。

## <a name="common-issues-when-preparing-your-project-for-azure-dev-spaces"></a>为 Azure Dev Spaces 准备项目时遇到的常见问题

### <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>警告“无法生成 Dockerfile，因为语言不受支持”
Azure Dev Spaces 为 C# 和 Node.js 提供本机支持。 当你在包含用这些语言之一编写的代码的目录中运行 `azds prep` 时，Azure Dev Spaces 会自动为你创建相应的 Dockerfile。

仍可以结合使用 Azure Dev Spaces 与用其他语言编写的代码，但需要在第一次运行 `azds up` 前手动创建 Dockerfile。

如果应用程序是用 Azure Dev Spaces 本身不支持的语言编写的，你需要提供相应的 Dockerfile，以生成运行代码的容器映像。 Docker 提供[关于编写 Dockerfile 的最佳做法列表](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)和 [Dockerfile 参考](https://docs.docker.com/engine/reference/builder/)，有助于编写满足你需求的 Dockerfile。

一旦有了相应的 Dockerfile，就可以运行 `azds up`，以在 Azure Dev Spaces 中运行应用程序。

## <a name="common-issues-when-starting-or-stopping-services-with-azure-dev-spaces"></a>使用 Azure Dev Spaces 启动或停止服务时遇到的常见问题

### <a name="error-config-file-not-found"></a>错误“找不到配置文件:”

在运行 `azds up` 时，可能会看到此错误。 `azds up` 和 `azds prep` 必须从要在开发空间中运行的项目的根目录中运行。

解决此问题：
1. 将当前目录切换到包含服务代码的根文件夹。 
1. 如果代码文件夹中没有 azds.yaml 文件，请运行 `azds prep`，以生成 Docker、Kubernetes 和 Azure Dev Spaces 资产。

### <a name="timeout-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>在对 AKS 虚拟节点执行“正在等待容器映像生成...”步骤时超时

如果你尝试使用 Dev Spaces 运行服务，此服务又配置为在 [AKS 虚拟节点](../aks/virtual-nodes-portal.md)上运行，就会发生这种超时。 Dev Spaces 暂不支持在虚拟节点上生成或调试服务。

如果使用 `--verbose` 开关运行 `azds up`，或在 Visual Studio 中启用详细日志记录，便会看到其他详细信息：

```cmd
azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

上面的命令指明，服务的 Pod 分配到虚拟节点 virtual-node-aci-linux。

若要修复此问题，请更新服务的 Helm 图表，以删除任何允许服务在虚拟节点上运行的 nodeSelector 或 tolerations 值。 这些值通常是在图表的 `values.yaml` 文件中进行定义。

如果要通过 Dev Spaces 生成或调试的服务在 VM 节点上运行，你仍可使用已启用虚拟节点功能的 AKS 群集。 使用 Dev Spaces 在 VM 节点上运行服务是默认配置。

### <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>启动 Dev Spaces 时看到错误“找不到就绪的 Tiller Pod”

如果 Helm 客户端无法再与群集中运行的 Tiller Pod 通信，则会发生此错误。

若要修复此问题，请重启群集中的代理节点。

### <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>错误 "release azds- \<identifier\> - \<spacename\> - \<servicename\> failed：服务" \<servicename\> "已存在" 或 "拒绝请求 \<servicename\> ，存储库不存在，或者可能需要 ' docker login"

如果在同一开发空间内混合运行直接 Helm 命令（如 `helm install`、`helm upgrade` 或 `helm delete`）和 Dev Spaces 命令（如 `azds up` 和 `azds down`），可能会看到这些错误。 出现这种情况是因为，Dev Spaces 有它自己的 Tiller 实例，这与在同一开发空间中运行的你自己的 Tiller 实例冲突。

对同一个 AKS 群集同时使用 Helm 命令和 Dev Spaces 命令是可以的，但每个已启用 Dev Spaces 的命名空间应该使用其中之一。

例如，假设你使用 Helm 命令在父开发空间中运行整个应用程序。 可以在父开发空间之外创建子开发空间，使用 Dev Spaces 在子开发空间内运行各个服务，并一起测试这些服务。 准备好签入更改时，请使用 Helm 命令将更新后的代码部署到父开发空间。 不要使用 `azds up` 在父开发空间中运行更新后的服务，因为它会与最初使用 Helm 运行的服务发生冲突。

### <a name="existing-dockerfile-not-used-to-build-a-container"></a>现有 Dockerfile 没有用于生成容器

Azure Dev Spaces 可以配置为指向项目中的特定 _Dockerfile_。 如果 Azure Dev Spaces 看起来并未使用预期的 Dockerfile 来生成容器，可能需要显式指示 Azure Dev Spaces 要使用哪个 Dockerfile。 

若要修复此问题，请打开 Azure Dev Spaces 在项目中生成的 azds.yaml 文件。 将 configurations: develop: build: dockerfile 更新为指向要使用的 Dockerfile。 例如：

```yaml
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

### <a name="error-unauthorized-authentication-required-when-trying-to-use-a-docker-image-from-a-private-registry"></a>在尝试使用专用注册表中的 Docker 映像时看到错误“未授权:需要身份验证”

你使用的是需要进行身份验证的专用注册表中的 Docker 映像。

若要修复此问题，可以允许 Dev Spaces 进行身份验证，并使用 [imagePullSecrets](https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets) 从此专用注册表中拉取映像。 若要使用 imagePullSecrets，请在要在其中使用映像的命名空间中[创建 Kubernetes 机密](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod)。 然后，提供机密作为 `azds.yaml` 中的 imagePullSecrets。

下面的示例展示了如何在 `azds.yaml` 中指定 imagePullSecrets。

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
> 在 `azds.yaml` 中设置的 imagePullSecrets 会替代在 `values.yaml` 中指定的 imagePullSecrets。

### <a name="error-service-cannot-be-started"></a>错误“无法启动服务”。

当服务代码无法启动时，你可能会看到此错误。 原因通常在用户代码中。 若要获取更多诊断信息，请在启动服务时启用更详细的日志记录。

在命令行中，使用 `--verbose` 来启用更详细的日志记录。 也可以使用 `--output` 指定输出格式。 例如：

```cmd
azds up --verbose --output json
```

在 Visual Studio 中：

1. 打开“工具”>“选项”，在“项目和解决方案”下，选择“生成并运行”。
2. 将“MSBuild 项目生成输出详细级别”的设置更改为“详细”或“诊断”。

    ![“工具选项”对话框的屏幕截图](media/common/VerbositySetting.PNG)

### <a name="rerunning-a-service-after-controller-re-creation"></a>在重新创建控制器后重新运行服务

如果尝试在删除并重新创建与此群集关联的 Azure Dev Spaces 控制器后重新运行服务，看到错误消息“无法启动服务”。 在这种情况下，详细输出包含以下文本：

```output
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

出现此错误是因为，删除 Dev Spaces 控制器不会删除此控制器先前安装的服务。 因为旧服务仍然存在，所以在重新创建控制器后尝试使用新控制器运行服务会失败。

若要解决此问题，请先使用 `kubectl delete` 命令手动从群集中删除旧服务，再重新运行 Dev Spaces 来安装新服务。

### <a name="error-service-cannot-be-started-when-using-multi-stage-dockerfiles"></a>在使用多阶段 Dockerfile 时看到 错误“无法启动服务。”

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

出现此错误是因为，Azure Dev Spaces 暂不支持多阶段生成。 为了避免多阶段生成，请重写 Dockerfile。

### <a name="network-traffic-is-not-forwarded-to-your-aks-cluster-when-connecting-your-development-machine"></a>连接开发计算机时，网络流量不转发到 AKS 群集

在使用 [Azure Dev Spaces 将 AKS 群集连接到开发计算机](https://code.visualstudio.com/docs/containers/bridge-to-kubernetes)时，可能会遇到下面这样的问题：网络流量没有在开发计算机与 AKS 群集之间进行转发。

将开发计算机连接到 AKS 群集时，Azure Dev Spaces 通过修改开发计算机的 `hosts` 文件，在 AKS 群集与开发计算机之间转发网络流量。 Azure Dev Spaces 在 `hosts` 中创建一个条目，其中包含要替换的 Kubernetes 服务的地址作为主机名。 此条目与端口转发结合使用，以在开发计算机与 AKS 群集之间定向网络流量。 如果开发计算机上的服务与要替换的 Kubernetes 服务的端口发生冲突，Azure Dev Spaces 就无法转发 Kubernetes 服务的网络流量。 例如，Windows BranchCache 服务通常绑定到 0.0.0.0:80，这会导致所有本地 IP 上的端口 80 发生冲突。

若要修复此问题，需要停止与你尝试要替换的 Kubernetes 服务的端口发生冲突的任何服务或进程。 可以使用工具（如 netstat）检查开发计算机上的哪些服务或进程发生冲突。

例如，若要停止和禁用 Windows BranchCache 服务，请执行以下操作：
* 通过命令提示符运行 `services.msc`。
* 右键单击“BranchCache”，然后选择“属性”。
* 单击“停止”。
* （可选）可以通过将“启动类型”设置为“已禁用”来禁用它。
* 单击“确定”。

### <a name="error-no-azureassignedidentity-found-for-podazdsazds-webhook-deployment-id-in-assigned-state"></a>错误 "找不到 pod 的 AzureAssignedIdentity： azds/azds- \<id\> 已分配的状态"

在安装了 [托管标识](../aks/use-managed-identity.md) 和 [pod 托管标识](../aks/developer-best-practices-pod-security.md#use-pod-managed-identities) 的 AKS 群集上运行带有 Azure Dev Spaces 的服务时，该进程可能会在 *图表安装* 步骤后停止响应。 如果在 azds 命名空间中检查 azds-injector-webhook，可能会看到此错误。

Azure Dev Spaces 在群集上运行的服务利用群集的托管标识与群集外的 Azure Dev Spaces 后端服务进行通信。 在 Pod 托管标识安装后，会在群集的节点上配置网络规则，以将托管标识凭据的所有调用重定向到[群集上安装的节点托管标识 (NMI) DaemonSet](https://github.com/Azure/aad-pod-identity#node-managed-identity)。 此 NMI DaemonSet 对调用 Pod 进行标识，并确保 Pod 已被正确标记来访问请求获取的托管标识。 Azure Dev Spaces 无法检测群集是否安装了 Pod 托管标识，也无法执行必要的配置来允许 Azure Dev Spaces 服务访问群集的托管标识。 由于尚未将 Azure Dev Spaces 服务配置为访问群集的托管标识，因此，NMI DaemonSet 将不允许它们获取托管标识的 Azure AD 令牌，并且无法与 Azure Dev Spaces 后端服务进行通信。

若要修复此问题，请为 azds-injector-webhook 应用 [AzurePodIdentityException](https://github.com/Azure/aad-pod-identity/blob/master/docs/readmes/README.app-exception.md)，并更新由 Azure Dev Spaces 检测的 Pod 来访问托管标识。

创建名为 webhookException.yaml 的文件，并复制以下 YAML 定义：

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

上面的文件为 azds-injector-webhook 创建 AzurePodIdentityException 对象。 若要部署此对象，请使用 `kubectl`：

```cmd
kubectl apply -f webhookException.yaml
```

若要更新由 Azure Dev Spaces 检测的 Pod 来访问托管标识，请更新以下 YAML 定义中的 namespace，并使用 `kubectl` 将它应用于每个开发空间。

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

或者，也可以创建 AzureIdentity 和 AzureIdentityBinding 对象，并为在由 Azure Dev Spaces 检测的空间中运行的工作负荷更新 Pod 标签，以访问 AKS 群集创建的托管标识。

若要列出托管标识的详细信息，请为 AKS 群集运行以下命令：

```azurecli
az aks show -g <resourcegroup> -n <cluster> -o json --query "{clientId: identityProfile.kubeletidentity.clientId, resourceId: identityProfile.kubeletidentity.resourceId}"
```

上面的命令输出托管标识的 clientId 和 resourceId。 例如：

```json
{
  "clientId": "<clientId>",
  "resourceId": "/subscriptions/<subid>/resourcegroups/<resourcegroup>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<name>"
}
```

若要创建 AzureIdentity 对象，请创建名为 clusteridentity.yaml 的文件，并使用以下 YAML 定义，此定义更新为包含上一命令中的托管标识详细信息：

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

若要创建 AzureIdentityBinding 对象，请创建名为 clusteridentitybinding.yaml 的文件，并使用以下 YAML 定义：

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: my-cluster-mi-binding
spec:
  AzureIdentity: my-cluster-mi
  Selector: my-label-value
```

若要部署 AzureIdentity 和 AzureIdentityBinding 对象，请使用 `kubectl`：

```cmd
kubectl apply -f clusteridentity.yaml
kubectl apply -f clusteridentitybinding.yaml
```

在你部署 AzureIdentity 和 AzureIdentityBinding 对象之后，任何具有 aadpodidbinding: my-label-value 标签的工作负荷都可以访问群集的托管标识。 添加此标签，并重新部署在任何开发空间中运行的所有工作负荷。 例如：

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

## <a name="common-issues-using-visual-studio-and-visual-studio-code-with-azure-dev-spaces"></a>结合使用 Visual Studio 和 Visual Studio Code 与 Azure Dev Spaces 时遇到的常见问题

### <a name="error-required-tools-and-configurations-are-missing"></a>错误“缺少必需的工具和配置”

启动 VS Code 时可能会发生此错误：“[Azure Dev Spaces] 缺少生成和调试 '[项目名称]' 所需的工具和配置。”
此错误意味着 azds.exe 不在 PATH 环境变量中，如 VS Code 中所示。

尝试从已正确设置 PATH 环境变量的命令提示符中启动 VS Code。

### <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>错误“用来生成和调试 ‘projectname’ 的必需工具已过期。”

如果使用的 zure Dev Spaces VS Code 扩展版本较高，但 Azure Dev Spaces CLI 的版本较低，便会在 Visual Studio Code 中看到此错误。

尝试下载并安装最新版 Azure Dev Spaces CLI：

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

### <a name="error-failed-to-find-debugger-extension-for-typecoreclr"></a>错误：“找不到 type:coreclr 的调试程序扩展”

在运行 Visual Studio Code 调试程序时，可能会看到此错误。 可能未在开发计算机上安装适用于 C# 的 VS Code 扩展。 C# 扩展包括对 .NET Core (CoreCLR) 的调试支持。

若要修复此问题，请安装[适用于 C# 的 VS Code 扩展](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)。

### <a name="error-configured-debug-type-coreclr-is-not-supported"></a>错误“已配置的调试类型 'coreclr' 不受支持”

在运行 Visual Studio Code 调试程序时，可能会看到此错误。 可能未在开发计算机上安装适用于 Azure Dev Spaces 的 VS Code 扩展。

若要修复此问题，请安装适用于 Azure Dev Spaces 的 VS Code 扩展。

### <a name="error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>错误“'cwd' 值 '/src' 无效。 系统找不到指定的文件。” 或者“launch: program‘/src/[项目二进制文件的路径]’不存在”

在运行 Visual Studio Code 调试程序时，可能会看到此错误。 默认情况下，VS Code 扩展使用 `src` 作为项目在容器上的工作目录。 如果你已更新了 `Dockerfile` 来指定一个不同的工作目录，则可能会看到此错误。

若要修复此问题，请更新项目文件夹的 `.vscode` 子目录下的 `launch.json` 文件。 更改 `configurations->cwd` 指令，以指向与在项目的 `Dockerfile` 中定义的 `WORKDIR` 相同的目录。 可能还需要更新 `configurations->program` 指令。

### <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>错误“管道程序 'azds' 意外退出，代码为 126。”

在运行 Visual Studio Code 调试程序时，可能会看到此错误。

若要修复此问题，请关闭并重新打开 Visual Studio Code。 重启调试程序。

### <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>在将调试附加到 Node.js 应用程序时看到错误“内部监视失败:监视 ENOSPC”

当使用你尝试要将调试程序附加到的 Node.js 应用程序来运行 Pod 的节点超过了 fs.inotify.max_user_watches 值时，就会发生此错误。 在某些情况下，[fs.inotify.max_user_watches 的默认值可能太小，无法直接将调试程序附加到 Pod](https://github.com/Azure/AKS/issues/772)。

解决此问题的临时方法是，增加群集中每个节点上的 fs.inotify.max_user_watches 值，然后重启相应节点，以便更改生效。

## <a name="other-common-issues"></a>其他常见问题

### <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>错误“'azds' 不能被识别为内部或外部命令、可运行程序或批处理文件”

如果没有安装或正确配置 `azds.exe`，可能会出现此错误。

解决此问题：

1. 检查位置 %ProgramFiles%/Microsoft SDKs\Azure\Azure Dev Spaces CLI 中是否有 `azds.exe`。 如果它在那里，将该位置添加到 PATH 环境变量。
2. 如果没有安装 `azds.exe`，请运行以下命令：

    ```azurecli
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

### <a name="authorization-error-microsoftdevspacesregisteraction"></a>授权错误“Microsoft.DevSpaces/register/action”

必须对 Azure 订阅拥有“所有者”或“参与者”访问权限，才能管理 Azure Dev Spaces。 如果你在尝试管理 Dev Spaces 时没有对相关 Azure 订阅的“所有者”或“参与者”访问权限，可能就会看到授权错误。 例如：

```output
The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.
```

若要修复此问题，请使用对 Azure 订阅拥有“所有者”或“参与者”访问权限的帐户来手动注册 `Microsoft.DevSpaces` 命名空间：

```azurecli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="new-pods-arent-starting"></a>新的 Pod 没有启动

由于对群集中“群集管理员”角色的 RBAC 权限更改，Kubernetes 初始值设定项无法为新的 Pod 应用 PodSpec。 新的 Pod 还可能有无效的 PodSpec（例如，与 Pod 关联的服务帐户已不存在）。 若要查看由于初始值设定项问题而处于“挂起”状态的 Pod，请使用 `kubectl get pods` 命令：

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

此问题可能会影响群集中所有命名空间（包括未启用 Azure Dev Spaces 的命名空间）内的 Pod。

若要修复此问题，请[将 Dev Spaces CLI 更新为最新版本](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools)，然后从 Azure Dev Spaces 控制器中删除 azds InitializerConfiguration：

```azurecli
az aks get-credentials --resource-group <resource group name> --name <cluster name>
```

```bash
kubectl delete InitializerConfiguration azds
```

在从 Azure Dev Spaces 控制器中删除 azds InitializerConfiguration 后，立即使用 `kubectl delete` 删除所有处于“挂起”状态的 Pod。 在删除所有挂起的 Pod 后，重新部署你的 Pod。

如果在重新部署后新的 Pod 仍停滞在“挂起”状态，请使用 `kubectl delete` 删除所有处于“挂起”状态的 Pod。 在删除所有挂起的 Pod 后，从群集中删除控制器并重新安装它：

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

在重新安装控制器后，重新部署你的 Pod。

### <a name="incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>用于调用 Dev Spaces 控制器和 API 的 RBAC 权限不正确

访问 Azure Dev Spaces 控制器的用户必须有权读取 AKS 群集上的管理员 kubeconfig。 例如，[内置的 Azure Kubernetes 服务群集管理员角色](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions)中包含此权限。 访问 Azure Dev Spaces 控制器的用户还必须具有该控制器的 *参与者* 或 *所有者* Azure 角色。 若要详细了解如何更新用户对 AKS 群集的权限，请单击[此处](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group)。

更新控制器的用户的 Azure 角色：

1. 通过 https://portal.azure.com 登录到 Azure 门户。
1. 转到包含控制器的资源组，它通常与 AKS 群集相同。
1. 启用“显示隐藏的类型”复选框。
1. 单击控制器。
1. 打开“访问控制(IAM)”窗格。
1. 单击“角色分配”选项卡。
1. 依次单击“添加”和“添加角色分配”。
    * 对于“角色”，选择“参与者”或“所有者”。
    * 对于“分配访问权限至”，选择“Azure AD 用户、组或服务主体” 。
    * 对于“选择”，搜索要向其授予权限的用户。
1. 单击“ *保存*”。

### <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>对与 Dev Spaces 服务关联的公用 URL 进行 DNS 名称解析失败

可以将 `--enable-ingress` 开关指定为 `azds prep` 命令，或选中 Visual Studio 内的 `Publicly Accessible` 复选框，从而配置服务的公共 URL 终结点。 当你在 Dev Spaces 中运行服务时，公共 DNS 名称自动注册。 如果此 DNS 名称未注册，你在连接到公共 URL 时，在 Web 浏览器中看到“无法显示网页”或“无法访问网站”错误消息。

解决此问题：

* 检查与 Dev Spaces 服务关联的所有 URL 的状态：

  ```console
  azds list-uris
  ```

* 如果 URL 处于“挂起”状态，表明 Dev Spaces 仍在等待 DNS 注册完成。 有时，注册需要几分钟的时间才能完成。 Dev Spaces 还为每个服务打开一个本地主机隧道，在等待 DNS 注册时你可以使用该隧道。
* 如果 URL 保持“挂起”状态超过 5 分钟，可能表明创建公共终结点的外部 DNS Pod 有问题，或获取公共终结点的 nginx 入口控制器 Pod 有问题。 若要删除这些 Pod，并允许 AKS 自动重新创建它们，请使用以下命令：
  ```console
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
  ```

### <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>错误“上游连接错误或在标头之前存在断开连接/重置设置”

可能会在尝试访问服务时看到此错误。 例如，可能会在浏览器中访问服务的 URL 时看到此错误。 此错误表示容器端口不可用。 这可能是由于以下原因所致：

* 容器仍在生成和部署过程中。 如果先运行 `azds up` 或启动调试器，然后在成功部署容器之前尝试访问容器，则会出现此问题。
* 端口配置在 _Dockerfile_、Helm 图表以及任何用于打开端口的服务器代码中不一致。

解决此问题：

1. 如果容器正在生成/部署过程中，则可等待 2-3 秒，然后尝试再次访问服务。 
1. 检查以下资产中的端口配置：
    * **[Helm 图表](https://docs.helm.sh)：** 由 `azds prep` 命令搭建基架的 values.yaml 中的 `service.port` 和 `deployment.containerPort` 指定。
    * 在应用程序代码（例如，Node.js 的 `var server = app.listen(80, function () {...}` ）中打开的任何端口

### <a name="the-type-or-namespace-name-mylibrary-couldnt-be-found"></a>找不到类型或命名空间名称“MyLibrary”

找不到你正在使用的库项目。 对于 Dev Spaces，默认情况下，生成上下文默认位于项目/服务级别。  

解决此问题：

1. 修改 `azds.yaml` 文件，以将生成上下文设置为解决方案级别。
2. 修改 `Dockerfile` 和 `Dockerfile.develop` 文件，以正确地相对于新的生成上下文来引用项目文件（例如 `.csproj`）。
3. 在与 `.sln` 文件相同的目录中添加 `.dockerignore`。
4. 根据需要，将 `.dockerignore` 更新为包含其他条目。

有关示例，可以单击[此处](https://github.com/sgreenmsft/buildcontextsample)。

### <a name="horizontal-pod-autoscaling-not-working-in-a-dev-space"></a>水平 Pod 自动缩放在开发空间中不起作用

当你在开发空间中运行服务时，相应服务的 Pod 被[注入了用于检测的附加容器](how-dev-spaces-works-cluster-setup.md#prepare-your-aks-cluster)，并且 Pod 中的所有容器都需要为水平 Pod 自动缩放设置资源限制和请求。

若要修复此问题，请对注入的 Dev Spaces 容器应用资源请求和限制。 可以通过将 `azds.io/proxy-resources` 注释添加到 Pod 规范，对注入的容器 (devspaces-proxy) 应用资源请求和限制。应将值设置为 JSON 对象，表示代理的容器规范的资源部分。

下面是要应用于 Pod 规范的代理资源注释的示例。
```
azds.io/proxy-resources: "{\"Limits\": {\"cpu\": \"300m\",\"memory\": \"400Mi\"},\"Requests\": {\"cpu\": \"150m\",\"memory\": \"200Mi\"}}"
```

### <a name="enable-azure-dev-spaces-on-an-existing-namespace-with-running-pods"></a>在运行 Pod 的现有命名空间上启用 Azure Dev Spaces

你可能现有运行 Pod 的 AKS 群集和命名空间，你要在其中启用 Azure Dev Spaces。

若要在 AKS 群集中的现有命名空间上启用 Azure Dev Spaces，请运行 `use-dev-spaces` 并使用 `kubectl` 来重启相应命名空间中的所有 Pod。

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space my-namespace --yes
```

```console
kubectl -n my-namespace delete pod --all
```

在 Pod 重启后，可以开始将现有命名空间与 Azure Dev Spaces 配合使用。

### <a name="enable-azure-dev-spaces-on-aks-cluster-with-restricted-egress-traffic-for-cluster-nodes"></a>在限制群集节点的出口流量的 AKS 群集上启用 Azure Dev Spaces

若要在限制群集节点的出口流量的 AKS 群集上启用 Azure Dev Spaces，必须允许以下 FQDN：

| FQDN                                    | 端口      | 用途      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS:443 | 用于拉取 linux alpine 和其他 Azure Dev Spaces 映像 |
| gcr.io | HTTP:443 | 用于拉取 helm/tiller 映像|
| storage.googleapis.com | HTTP:443 | 用于拉取 helm/tiller 映像|

更新防火墙或安全配置，以允许与上述所有 Fqdn 和 [Azure Dev Spaces 基础结构服务](../dev-spaces/configure-networking.md#virtual-network-or-subnet-configurations)的网络流量。

### <a name="error-could-not-find-the-cluster-cluster-in-subscription-subscriptionid"></a>错误 "找不到 \<cluster\> 订阅中的群集 \<subscriptionId\> "

如果 kubeconfig 文件定目标到的群集或订阅与你尝试与 Azure Dev Spaces 客户端工具配合使用的群集或订阅不同，你可能会看到此错误。 Azure Dev Spaces 客户端工具复制 kubectl 的行为，即使用[一个或多个 kubeconfig 文件](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/)选择群集并与群集通信。

解决此问题：

* 使用 `az aks use-dev-spaces -g <resource group name> -n <cluster name>` 更新当前上下文。 此命令还会在 AKS 群集上启用 Azure Dev Spaces（如果尚未启用的话）。 或者，也可以使用 `kubectl config use-context <cluster name>` 更新当前上下文。
* 使用 `az account show` 显示你当前定目标到的 Azure 订阅，并验证是否正确。 可以使用 `az account set` 更改定目标到的订阅。

### <a name="error-using-dev-spaces-after-rotating-aks-certificates"></a>在轮换 AKS 证书后使用 Dev Spaces 时出错

在 [AKS 群集中轮换证书后](../aks/certificate-rotation.md)，将无法执行某些操作（如 `azds space list` 和 `azds up`）。 在群集上轮换证书后，还需要刷新 Azure Dev Spaces 控制器上的证书。

若要修复此问题，请确保 kubeconfig 已使用 `az aks get-credentials` 获取了更新后的证书，然后运行 `azds controller refresh-credentials` 命令。 例如：

```azurecli
az aks get-credentials -g <resource group name> -n <cluster name>
```

```console
azds controller refresh-credentials -g <resource group name> -n <cluster name>
```
