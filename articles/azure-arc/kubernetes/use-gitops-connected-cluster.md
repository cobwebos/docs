---
title: 在启用了 Arc 的 Kubernetes 群集 () 预览中使用 GitOps 部署配置
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: 将 GitOps 用于启用了 Azure Arc 的群集配置（预览版）
keywords: GitOps, Kubernetes, K8s, Azure, Arc, Azure Kubernetes Service, containers
ms.openlocfilehash: c00ed30c9a7424d083bf076c64cf008e0480bb2b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91714177"
---
# <a name="deploy-configurations-using-gitops-on-arc-enabled-kubernetes-cluster-preview"></a>在启用了 Arc 的 Kubernetes 群集 () 预览中使用 GitOps 部署配置

GitOps 是在 Git 存储库中声明所需状态的 Kubernetes 配置 (部署、命名空间等) ，然后使用运算符将这些配置部署到群集的轮询和请求部署。 本文档介绍如何在启用了 Azure Arc 的 Kubernetes 群集上设置此类工作流。

群集与一个或多个 Git 存储库之间的连接在 Azure 资源管理器中作为 `sourceControlConfiguration` 扩展资源进行跟踪。 `sourceControlConfiguration` 资源属性表示 Kubernetes 资源应从 Git 流向群集的位置和方式。 `sourceControlConfiguration`数据以加密的静态存储在 Azure Cosmos DB 数据库中，以确保数据的保密性。

`config-agent`群集中运行的负责在启用了 Azure Arc 的 Kubernetes 资源上监视新的或更新的 `sourceControlConfiguration` 扩展资源，部署 flux 运算符来查看 Git 存储库，以及传播对的任何更新 `sourceControlConfiguration` 。 甚至可以 `sourceControlConfiguration` `namespace` 在启用了同一个 Azure Arc Kubernetes 群集的范围内创建多个资源，以实现多租户。 在这种情况下，每个运算符只能将配置部署到其各自的命名空间。

Git 存储库可以包含任何有效的 Kubernetes 资源，包括命名空间、ConfigMap、部署、Daemonset 等。它还可能包含用于部署应用程序的 Helm 图表。 一组常见的方案包括为你的组织定义基线配置，其中可能包括常见的 Azure 角色和绑定、监视或日志记录代理或群集范围内的服务。

可以使用相同的模式来管理更大的群集集合，这些群集可能会在异类环境中进行部署。 例如，你可能具有一个存储库，该存储库为组织定义基线配置，并一次性将其应用于数十个 Kubernetes 群集。 [Azure 策略可以](use-azure-policy.md) 在 `sourceControlConfiguration` (订阅或资源组) 的作用域下，使用一组特定的参数在所有启用了 Azure Arc 的 Kubernetes 资源上自动创建。

本入门指南将指导你应用一组具有群集管理作用域的配置。

## <a name="before-you-begin"></a>开始之前

本文假设你已有一个启用了 Azure Arc 的 Kubernetes 已连接群集。 如果你需要一个已连接的群集，请参阅[连接群集快速入门](./connect-cluster.md)。

## <a name="create-a-configuration"></a>创建配置

本文档中使用的 [示例存储库](https://github.com/Azure/arc-k8s-demo) 是围绕群集操作员的角色构建的，该操作员要预配几个命名空间，部署一个常见的工作负荷，并提供一些特定于团队的配置。 使用此存储库将在群集上创建以下资源：

命名空间：`cluster-config`、`team-a`、`team-b`
部署：**** ****`cluster-config/azure-vote`
ConfigMap：`team-a/endpoints`****

`config-agent` `sourceControlConfiguration` 每30秒轮询一次 Azure，这是 `config-agent` 选取新的或更新的配置所需的最长时间。
如果要将专用存储库与相关联 `sourceControlConfiguration` ，请确保还完成了 [从私有 git 存储库应用配置](#apply-configuration-from-a-private-git-repository)中的步骤。

### <a name="using-azure-cli"></a>使用 Azure CLI

使用的 Azure CLI 扩展 `k8sconfiguration` ，让我们将连接的群集链接到 [示例 git 存储库](https://github.com/Azure/arc-k8s-demo)。 将此配置命名为 `cluster-config`，指示代理在 `cluster-config` 命名空间中部署运算符，并授予运算符 `cluster-admin` 权限。

```console
az k8sconfiguration create --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name cluster-config --operator-namespace cluster-config --repository-url https://github.com/Azure/arc-k8s-demo --scope cluster --cluster-type connectedClusters
```

**输出：**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "ComplianceStatus": 1,
    "clientAppliedTime": null,
    "level": 3,
    "message": "{\"OperatorMessage\":null,\"ClusterState\":null}"
  },
  "configKind": "Git",
  "configName": "cluster-config",
  "configOperator": {
    "operatorInstanceName": "cluster-config",
    "operatorNamespace": "cluster-config",
    "operatorParams": "--git-readonly",
    "operatorScope": "cluster",
    "operatorType": "flux"
  },
  "configType": "",
  "id": null,
  "name": null,
  "operatorInstanceName": null,
  "operatorNamespace": null,
  "operatorParams": null,
  "operatorScope": null,
  "operatorType": null,
  "providerName": "ConnectedClusters",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": null,
  "repositoryUrl": null,
  "sourceControlConfiguration": {
    "repositoryPublicKey": "",
    "repositoryUrl": "git://github.com/Azure/arc-k8s-demo.git"
  },
  "type": null
}
```

#### <a name="repository-url-parameter"></a>repository-url 参数

下面是 --repository-url 参数的值支持的场景。

| 场景 | 格式 | 说明 |
| ------------- | ------------- | ------------- |
| 专用 GitHub 存储库 - SSH | git@github.com:username/repo | Flux 生成的 SSH 密钥对。  用户必须将公钥作为部署密钥添加到 GitHub 帐户。 |
| 公共 GitHub 存储库 | `http://github.com/username/repo` 或 git://github.com/username/repo   | 公共 Git 存储库  |

上述场景受 Flux 支持，但不受 sourceControlConfiguration 支持。 

| 场景 | 格式 | 说明 |
| ------------- | ------------- | ------------- |
| 专用 GitHub 存储库 - HTTPS | `https://github.com/username/repo` | Flux 不生成 SSH 密钥对。  [说明](https://docs.fluxcd.io/en/1.17.0/guides/use-git-https.html) |
| 专用 Git 主机 | user@githost:path/to/repo | [说明](https://docs.fluxcd.io/en/1.18.0/guides/use-private-git-host.html) |
| 专用 GitHub 存储库 - SSH（自带密钥） | git@github.com:username/repo | [使用自己的 SSH 密钥对](https://docs.fluxcd.io/en/1.17.0/guides/provide-own-ssh-key.html) |


#### <a name="additional-parameters"></a>附加参数

若要自定义配置的创建，可使用以下几个附加参数：

`--enable-helm-operator`：可选开关，用于启用对 Helm 图表部署的支持。

`--helm-operator-chart-values`：Helm 运算符（若已启用）的可选图表值。  例如“--set helm.versions=v3”。

`--helm-operator-chart-version`：Helm 运算符（若已启用）的可选图表版本。 默认值：“0.6.0”。

`--operator-namespace`：运算符命名空间的可选名称。 默认值：“default”

`--operator-params`：运算符的可选参数。 必须用单引号括起来。 例如： ```--operator-params='--git-readonly --git-path=releases' ```

--operator-params 中支持的选项

| 选项 | 说明 |
| ------------- | ------------- |
| --git-branch  | 用于 Kubernetes 清单的 git 存储库的分支。 默认为“master”。 |
| --git-path  | Git 存储库中供 Flux 查找 Kubernetes 清单的相对路径。 |
| --git-readonly | Git 存储库将被视为只读；Flux 不会尝试向其写入。 |
| --manifest-generation  | 如果启用，Flux 将查找 .flux.yaml 并运行 Kustomize 或其他清单生成器。 |
| --git-poll-interval  | 轮询 Git 存储库以获取新提交的周期。 默认值为“5m”（5 分钟）。 |
| --sync-garbage-collection  | 如果启用，Flux 将删除由它创建但在 Git 中已不再存在的资源。 |
| --git-label  | 用于跟踪同步进度和标记 Git 分支的标签。  默认值为“flux-sync”。 |
| --git-user  | git 提交的用户名。 |
| --git-email  | 用于 git 提交的电子邮件。 |

* 如果未设置“--git-user”或“--git-email”（这意味着你不希望 Flux 向存储库进行写入），则将自动设置 --git-readonly（如果尚未设置）。

* 如果 enableHelmOperator 为 true，则 operatorInstanceName + operatorNamespace 字符串的组合长度不能超过 47 个字符。  如果无法遵守此限制，您将收到以下错误：

   ```console
   {"OperatorMessage":"Error: {failed to install chart from path [helm-operator] for release [<operatorInstanceName>-helm-<operatorNamespace>]: err [release name \"<operatorInstanceName>-helm-<operatorNamespace>\" exceeds max length of 53]} occurred while doing the operation : {Installing the operator} on the config","ClusterState":"Installing the operator"}
   ```

有关详细信息，请参阅 [Flux 文档](https://aka.ms/FluxcdReadme)。

> [!TIP]
> 可以在 Azure 门户上创建 sourceControlConfiguration，也可以在启用了 Azure Arc Kubernetes 资源边栏选项卡的 " **配置** " 选项卡下创建。

## <a name="validate-the-sourcecontrolconfiguration"></a>验证 sourceControlConfiguration

使用 Azure CLI 验证是否已成功创建 `sourceControlConfiguration`。

```console
az k8sconfiguration show --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

请注意，将使用合规状态、消息和调试信息更新 `sourceControlConfiguration` 资源。

**输出：**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2019-12-05T05:34:41.481000",
    "message": "...",
    "messageLevel": "3"
  },
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
  "name": "cluster-config",
  "operatorInstanceName": "cluster-config",
  "operatorNamespace": "cluster-config",
  "operatorParams": "--git-readonly",
  "operatorScope": "cluster",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "...",
  "repositoryUrl": "git://github.com/Azure/arc-k8s-demo.git",
  "resourceGroup": "AzureArcTest",
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

创建 `sourceControlConfiguration` 时，后台将发生以下情况：

1. Azure Arc `config-agent` 监视 Azure 资源管理器，查看是否存在新配置或更新的配置 (`Microsoft.KubernetesConfiguration/sourceControlConfiguration`)
1. `config-agent` 通知新的 `Pending` 配置
1. `config-agent` 读取配置属性并准备部署 `flux` 的托管实例
    * `config-agent` 创建目标命名空间
    * `config-agent` 准备具有适当权限（`cluster` 或 `namespace` 作用域）的 Kubernetes 服务帐户
    * `config-agent` 部署 `flux` 的一个实例
    * `flux` 生成 SSH 密钥并记录公钥
1. `config-agent` 将状态报告回 `sourceControlConfiguration`

在预配过程中，`sourceControlConfiguration` 会经历几次状态更改。 使用上面的 `az k8sconfiguration show ...` 命令监视进度：

1. `complianceStatus` -> `Pending`：表示初始状态和正在进行的状态
1. `complianceStatus` -> `Installed`：`config-agent` 能够成功配置群集并部署 `flux` 且不会出错
1. `complianceStatus` -> `Failed`：`config-agent` 在部署 `flux` 时遇到错误，`complianceStatus.message` 响应正文中应具有详细信息

## <a name="apply-configuration-from-a-private-git-repository"></a>应用专用 git 存储库中的配置

如果你使用的是私有 git 存储库，则需要执行一个以上的任务来关闭循环：将生成的公钥 `flux` 作为 **部署密钥** 添加到存储库中。

使用 Azure CLI 获取公钥

```console
$ az k8sconfiguration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --query 'repositoryPublicKey'
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

从 Azure 门户中获取公钥

1. 在 Azure 门户中，导航到已连接的群集资源。
2. 在“资源”页上，选择“配置”，并查看此群集的配置列表。
3. 选择使用专用 Git 存储库的配置。
4. 在打开的上下文窗口中，在窗口底部复制“存储库公钥”。

将公钥作为部署密钥添加到 git 存储库

1. 打开 GitHub，依次导航到“分支”、“设置”和“部署密钥” 
2. 单击“添加部署密钥”
3. 提供标题
4. 选中“允许写访问”
5. 粘贴公钥（删除任何周围的引号）
6. 单击“添加密钥”

有关如何管理这些密钥的详细信息，请参阅 GitHub 文档。

如果使用 Azure DevOps 存储库，请将密钥添加到 SSH 密钥

1. 在右上方的“用户设置”下（配置文件映像旁），单击“SSH 公钥” 
1. 选择“+ 新建密钥”
1. 提供名称
1. 粘贴不带任何引号的公钥
1. 单击“添加”

## <a name="validate-the-kubernetes-configuration"></a>验证 Kubernetes 配置

当 `config-agent` 安装了 `flux` 实例之后，git 存储库中保存的资源应开始流向群集。 检查是否已创建命名空间、部署和资源：

```console
kubectl get ns --show-labels
```

**输出：**

```console
NAME              STATUS   AGE    LABELS
azure-arc         Active   24h    <none>
cluster-config    Active   177m   <none>
default           Active   29h    <none>
itops             Active   177m   fluxcd.io/sync-gc-mark=sha256.9oYk8yEsRwWkR09n8eJCRNafckASgghAsUWgXWEQ9es,name=itops
kube-node-lease   Active   29h    <none>
kube-public       Active   29h    <none>
kube-system       Active   29h    <none>
team-a            Active   177m   fluxcd.io/sync-gc-mark=sha256.CS5boSi8kg_vyxfAeu7Das5harSy1i0gc2fodD7YDqA,name=team-a
team-b            Active   177m   fluxcd.io/sync-gc-mark=sha256.vF36thDIFnDDI2VEttBp5jgdxvEuaLmm7yT_cuA2UEw,name=team-b
```

我们可以看到已创建 `team-a`、`team-b`、`itops` 和 `cluster-config` 命名空间。

`flux` 运算符已部署到 `cluster-config` 命名空间，如 `sourceControlConfig` 所指示：

```console
kubectl -n cluster-config get deploy  -o wide
```

**输出：**

```console
NAME             READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES                         SELECTOR
cluster-config   1/1     1            1           3h    flux         docker.io/fluxcd/flux:1.16.0   instanceName=cluster-config,name=flux
memcached        1/1     1            1           3h    memcached    memcached:1.5.15               name=memcached
```

## <a name="further-exploration"></a>深入了解

可以了解作为配置存储库的一部分部署的其他资源：

```console
kubectl -n team-a get cm -o yaml
kubectl -n itops get all
```

## <a name="delete-a-configuration"></a>删除配置

`sourceControlConfiguration`使用 Azure CLI 或 Azure 门户删除。  启动删除命令后， `sourceControlConfiguration` 会立即在 Azure 中删除资源，但从群集中完全删除关联的对象可能需要1小时的时间， (我们有一个积压工作（backlog）项来减少) 的此时间延迟。

> [!NOTE]
> 创建具有命名空间作用域的 sourceControlConfiguration 后，具有 `edit` 命名空间上的角色绑定的用户可以在此命名空间上部署工作负荷。 `sourceControlConfiguration`删除具有命名空间作用域的这一命名空间时，命名空间保持不变且不会被删除，以避免破坏这些其他工作负荷。
> 删除后，不会删除对由跟踪的 git 存储库中的部署所做的任何更改 `sourceControlConfiguration` 。

```console
az k8sconfiguration delete --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

**输出：**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
```

## <a name="next-steps"></a>后续步骤

- [结合使用 Helm 和源代码管理配置](./use-gitops-with-helm.md)
- [使用 Azure Policy 控制群集配置](./use-azure-policy.md)
