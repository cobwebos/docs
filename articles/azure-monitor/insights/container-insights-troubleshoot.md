---
title: 如何对用于容器的 Azure Monitor 进行故障排除 | Microsoft Docs
description: 本文介绍如何排查和解决用于容器的 Azure Monitor 存在的问题。
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 17a2817b320599b2aa2c331c354d316b9d864a32
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75403384"
---
# <a name="troubleshooting-azure-monitor-for-containers"></a>对用于容器的 Azure Monitor 进行故障排除

使用用于容器的 Azure Monitor 配置 Azure Kubernetes 服务 (AKS) 群集的监视时，可能会遇到阻止数据收集或报告状态的问题。 本文详细介绍了一些常见问题及其排查步骤。

## <a name="authorization-error-during-onboarding-or-update-operation"></a>载入或更新操作期间授权错误
为容器启用 Azure Monitor 或更新群集以支持收集度量值时，可能会收到类似于以下的错误：*客户端 < 用户的标识 > "，其中对象 id 为" < 用户的 objectId > "无权对作用域执行操作" roleAssignments/write "* 。

在载入或更新过程中，将尝试对群集资源授予**监视指标发布者**角色分配。 为启用容器 Azure Monitor 或更新以支持指标收集的用户必须具有对 AKS 群集资源作用域的**roleAssignments/write**权限的访问权限。 只有 "**所有者**" 和 "**用户访问管理员**" 内置角色的成员才会获得对此权限的访问权限。 如果你的安全策略需要分配粒度级别权限，我们建议你查看[自定义角色](../../role-based-access-control/custom-roles.md)并将其分配给需要该角色的用户。 

还可以通过执行以下步骤，从 Azure 门户中手动授予此角色：

1. 登录 [Azure 门户](https://portal.azure.com)。 
2. 在 Azure 门户中，单击左上角的“所有服务”。 在资源列表中，键入 " **Kubernetes**"。 开始键入时，会根据输入筛选该列表。 选择 " **Azure Kubernetes**"。
3. 在 Kubernetes 群集列表中，从列表中选择一个。
2. 在左侧菜单中，单击 "**访问控制（IAM）** "。
3. 选择 " **+ 添加**" 以添加角色分配，然后选择 "**监视指标发布者**" 角色，并在 "**选择**" 框中键入 " **AKS** "，以仅筛选订阅中定义的群集服务主体上的结果。 从列表中选择特定于该群集的一个。
4. 选择“保存”完成角色分配。 

## <a name="azure-monitor-for-containers-is-enabled-but-not-reporting-any-information"></a>用于容器的 Azure Monitor 已启用，但未报告任何信息
如果已成功启用并配置容器的 Azure Monitor，但无法查看状态信息，或者日志查询未返回任何结果，则可以通过执行以下步骤来诊断问题： 

1. 通过运行以下命令检查代理状态： 

    `kubectl get ds omsagent --namespace=kube-system`

    输出应如下所示，指明其已正确部署：

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```  
2. 使用以下命令检查代理版本 *06072018* 或更高版本的部署状态：

    `kubectl get deployment omsagent-rs -n=kube-system`

    输出应类似于以下示例，指示已正确部署：

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

3. 使用以下命令，检查 Pod 的状态，验证它是否正在运行：`kubectl get pods --namespace=kube-system`

    输出应类似于以下示例，omsagent 状态为“正在运行”：

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system 
    NAME                                READY     STATUS    RESTARTS   AGE 
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d 
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d 
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d 
    omsagent-484hw                      1/1       Running   0          1d 
    omsagent-fkq7g                      1/1       Running   0          1d 
    ```

4. 查看代理日志。 部署容器化代理时，它通过运行 OMI 命令执行快速检查，并显示代理和提供程序的版本。 

5. 若要验证代理是否已成功部署，请运行以下命令： `kubectl logs omsagent-484hw --namespace=kube-system`

    状态应类似于以下示例：

    ```
    User@aksuser:~$ kubectl logs omsagent-484hw --namespace=kube-system
    :
    :
    instance of Container_HostInventory
    {
        [Key] InstanceID=3a4407a5-d840-4c59-b2f0-8d42e07298c2
        Computer=aks-nodepool1-39773055-0
        DockerVersion=1.13.1
        OperatingSystem=Ubuntu 16.04.3 LTS
        Volume=local
        Network=bridge host macvlan null overlay
        NodeRole=Not Orchestrated
        OrchestratorType=Kubernetes
    }
    Primary Workspace: b438b4f6-912a-46d5-9cb1-b44069212abc    Status: Onboarded(OMSAgent Running)
    omi 1.4.2.2
    omsagent 1.6.0.23
    docker-cimprov 1.0.0.31
    ```

## <a name="error-messages"></a>错误消息

下表汇总了使用适用于容器的 Azure Monitor 时可能会遇到的已知错误。

| 错误消息  | 行动 |  
| ---- | --- |  
| 错误消息 `No data for selected filters`  | 为新创建的群集建立监视数据流可能需要花费一些时间。 至少需要10到15分钟的时间，数据才会显示在你的群集中。 |   
| 错误消息 `Error retrieving data` | 当 Azure Kubernetes Service 群集设置为运行状况和性能监视时，将在群集和 Azure Log Analytics 工作区之间建立连接。 Log Analytics 工作区用于存储你的群集的所有监视数据。 删除 Log Analytics 工作区时，可能会发生此错误。 检查工作区是否已删除，如果已删除，你将需要使用容器 Azure Monitor 重新启用对群集的监视，并指定现有工作区或创建新的工作区。 若要重新启用，你将需要为群集[禁用](container-insights-optout.md)监视并再次[启用](container-insights-enable-new-cluster.md)为容器 Azure Monitor。 |  
| 通过 az aks cli 添加适用于容器的 Azure Monitor 后出现 `Error retrieving data` | 使用 `az aks cli`启用监视时，可能无法正确部署容器 Azure Monitor。 检查解决方案是否已部署。 若要执行此操作，请转到你的 Log Analytics 工作区，并从左侧的面板中选择“解决方案”来查看该解决方案是否可用。 若要解决此问题，需要按照[如何部署适用于容器的 Azure Monitor](container-insights-onboard.md) 中的说明重新部署该解决方案。 |  

为了帮助诊断问题，我们在[此处](https://github.com/Microsoft/OMS-docker/tree/ci_feature_prod/Troubleshoot#troubleshooting-script)提供了一个可用的故障排除脚本。

## <a name="azure-monitor-for-containers-agent-replicaset-pods-are-not-scheduled-on-non-azure-kubernetes-cluster"></a>未在非 Azure Kubernetes 群集上计划容器代理 ReplicaSet pod 的 Azure Monitor

容器代理 ReplicaSet pod 的 Azure Monitor 依赖于计划的辅助节点（或代理）节点上的以下节点选择器：

```
nodeSelector:
  beta.kubernetes.io/os: Linux
  kubernetes.io/role: agent
```

如果辅助角色节点未附加节点标签，则将不会计划代理 ReplicaSet pod。 有关如何附加标签的说明，请参阅[Kubernetes 分配标签选择器](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/)。

## <a name="performance-charts-dont-show-cpu-or-memory-of-nodes-and-containers-on-a-non-azure-cluster"></a>性能图表不显示非 Azure 群集上的节点和容器的 CPU 或内存

容器的 Azure Monitor 使用节点代理上的 cAdvisor 终结点来收集性能指标。 验证节点上容器化的代理是否已配置为允许在群集中的所有节点上打开 `cAdvisor port: 10255` 以收集性能指标。

## <a name="non-azure-kubernetes-cluster-are-not-showing-in-azure-monitor-for-containers"></a>容器 Azure Monitor 中未显示非 Azure Kubernetes 群集

若要在容器 Azure Monitor 中查看非 Azure Kubernetes 群集，需要在支持此见解的 Log Analytics 工作区和容器 Insights 解决方案资源**ContainerInsights （*工作区*）** 上进行读取访问。

## <a name="next-steps"></a>后续步骤

启用监视来捕获 AKS 群集节点和 Pod 的运行状况指标后，可在 Azure 门户中找到这些运行状况指标。 要了解如何将 Azure Monitor 用于容器，请参阅[查看 Azure Kubernetes 服务运行状况](container-insights-analyze.md)。
