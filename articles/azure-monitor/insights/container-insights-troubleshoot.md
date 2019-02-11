---
title: 如何对用于容器的 Azure Monitor 进行故障排除 | Microsoft Docs
description: 本文介绍如何排查和解决用于容器的 Azure Monitor 存在的问题。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2018
ms.author: magoedte
ms.openlocfilehash: 5a9211abdbc4c9ea7907dfac00d449317dd13089
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2018
ms.locfileid: "53190741"
---
# <a name="troubleshooting-azure-monitor-for-containers"></a>对用于容器的 Azure Monitor 进行故障排除

使用用于容器的 Azure Monitor 配置 Azure Kubernetes 服务 (AKS) 群集的监视时，可能会遇到阻止数据收集或报告状态的问题。 本文详细介绍了一些常见问题及其排查步骤。

## <a name="azure-monitor-for-containers-is-enabled-but-not-reporting-any-information"></a>用于容器的 Azure Monitor 已启用，但未报告任何信息
如果已成功启用并配置了用于容器的 Azure Monitor，但无法查看状态信息或从 Log Analytics 日志查询未返回任何结果，则可以按照以下步骤诊断问题： 

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

4. 检查代理日志。 部署容器化代理时，它通过运行 OMI 命令执行快速检查，并显示代理和提供程序的版本。 

5. 若要验证代理是否已成功载入，请运行以下命令：`kubectl logs omsagent-484hw --namespace=kube-system`

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

| 错误消息  | 操作 |  
| ---- | --- |  
| 错误消息 `No data for selected filters`  | 为新创建的群集建立监视数据流可能需要花费一些时间。 请等待至少 10 到 15 分钟，以便群集的数据出现。 |   
| 错误消息 `Error retrieving data` | 为 Azure Kubenetes 服务群集设置运行状况和性能监视时，会在群集与 Azure Log Analytics 工作区之间建立连接。 Log Analytics 工作区用于存储你的群集的所有监视数据。 当 Log Analytics 工作区已删除或丢失时，可能会发生此错误。 通过查看 [管理访问权限](../../azure-monitor/platform/manage-access.md?toc=/azure/azure-monitor/toc.json#workspace-information) 来检查你的工作区是否可用。 如果缺少工作区，则需要使用适用于容器的 Azure Monitor 重新载入你的群集。 若要重新载入，需要为群集[禁用](container-insights-optout.md)监视，并再次为容器[启用](container-insights-onboard.md?toc=%2fazure%2fmonitoring%2ftoc.json#enable-monitoring-for-a-new-cluster) Azure Monitor。 |  
| 通过 az aks cli 添加适用于容器的 Azure Monitor 后出现 `Error retrieving data` | 使用 `az aks cli` 进行载入时，适用于容器的 Azure Monitor 可能不会正确载入。 检查该解决方案是否已载入。 若要执行此操作，请转到你的 Log Analytics 工作区，并从左侧的面板中选择“解决方案”来查看该解决方案是否可用。 若要解决此问题，需要按照[如何部署适用于容器的 Azure Monitor](container-insights-onboard.md?toc=%2fazure%2fmonitoring%2ftoc.json) 中的说明重新部署该解决方案。 |  

为了帮助诊断问题，我们在[此处](https://github.com/Microsoft/OMS-docker/tree/ci_feature_prod/Troubleshoot#troubleshooting-script)提供了一个可用的故障排除脚本。  

## <a name="next-steps"></a>后续步骤
启用监视来捕获 AKS 群集节点和 Pod 的运行状况指标后，可在 Azure 门户中找到这些运行状况指标。 要了解如何将 Azure Monitor 用于容器，请参阅[查看 Azure Kubernetes 服务运行状况](container-insights-analyze.md)。