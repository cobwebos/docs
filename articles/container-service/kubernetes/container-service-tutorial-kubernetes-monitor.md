---
title: "Azure 容器服务教程 - 监视 Kubernetes | Microsoft Docs"
description: "Azure 容器服务教程 - 使用 Microsoft Operations Management Suite (OMS) 监视 Kubernetes"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, 容器, 微服务, Kubernetes, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: danlep
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: 1e1b93c29dc28569e897c57d9b68622c1edbd636
ms.contentlocale: zh-cn
ms.lasthandoff: 07/25/2017

---

# <a name="monitor-a-kubernetes-cluster-with-operations-management-suite"></a>使用 Operations Management Suite 监视 Kubernetes 群集

监视 Kubernetes 群集和容器至关重要，特别是在使用多个应用大规模地管理生产群集时。 

可以利用 Microsoft 或其他提供程序提供的多个 Kubernetes 监视解决方案。 在此教程中，可以通过使用 Microsoft 基于云的 IT 管理解决方案 [Operations Management Suite](../../operations-management-suite/operations-management-suite-overview.md) 来监视 Kubernetes 群集。 （OMS 容器解决方案为预览版。）

本教程第 7 部分（共 7 部分）涵盖以下任务：

> [!div class="checklist"]
> * 获取 OMS 工作区设置
> * 在 Kubernetes 节点上设置 OMS 代理
> * 在 OMS 门户或 Azure 门户中访问监视信息

## <a name="before-you-begin"></a>开始之前

在前面的教程中，我们将应用程度打包到容器映像中，将这些映像上传到 Azure 容器注册表，并创建了 Kubernetes 群集。 如果尚未完成这些步骤，并且想要逐一完成，请返回到[教程 1 – 创建容器映像](./container-service-tutorial-kubernetes-prepare-app.md)。 

本教程至少需要使用 Linux 代理节点的 Kubernetes 群集以及 Operations Management Suite (OMS) 帐户。 必要时，请注册[免费试用版 OMS](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite-trial)。

## <a name="get-workspace-settings"></a>获取工作区设置

如果可以访问 [OMS 门户](https://mms.microsoft.com)，请转到“设置” > “连接的源” > “Linux 服务器”。 你可以在其中找到工作区 ID 和主要或辅助工作区密钥。 记下这些值，在群集上设置 OMS 代理需要这些值。

## <a name="set-up-oms-agents"></a>设置 OMS 代理

以下是用于在 Linux 群集节点上设置 OMS 代理的 YAML 文件。 该文件将创建 Kubernetes [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/)，它可在每个群集节点上运行单个相同 Pod。 DaemonSet 资源非常适合部署监视代理。 

将以下文本保存到名为 `oms-daemonset.yaml` 的文件，并将 myWorkspaceID 和 myWorkspaceKey 的占位符值替换为 OMS 工作区 ID 和密钥。 （在生产中，可将这些值编码为机密。）

```YAML
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
 name: omsagent
spec:
 template:
  metadata:
   labels:
    app: omsagent
    agentVersion: v1.3.4-127
    dockerProviderVersion: 10.0.0-25
  spec:
   containers:
     - name: omsagent 
       image: "microsoft/oms"
       imagePullPolicy: Always
       env:
       - name: WSID
         value: myWorkspaceID
       - name: KEY 
         value: myWorkspaceKey
       - name: DOMAIN
         value: opinsights.azure.com
       securityContext:
         privileged: true
       ports:
       - containerPort: 25225
         protocol: TCP 
       - containerPort: 25224
         protocol: UDP
       volumeMounts:
        - mountPath: /var/run/docker.sock
          name: docker-sock
        - mountPath: /var/log 
          name: host-log
       livenessProbe:
        exec:
         command:
         - /bin/bash
         - -c
         - ps -ef | grep omsagent | grep -v "grep"
        initialDelaySeconds: 60
        periodSeconds: 60
   volumes:
    - name: docker-sock 
      hostPath:
       path: /var/run/docker.sock
    - name: host-log
      hostPath:
       path: /var/log
```

使用以下命令创建 DaemonSet：

```azurecli-interactive
kubectl create -f oms-daemonset.yaml
```

若要查看是否已创建 DaemonSet，请运行：

```azurecli-interactive
kubectl get daemonset
```

输出与下面类似：

```azurecli-interactive
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE-SELECTOR   AGE
omsagent   3         3         3         0            3           <none>          5m
```

代理运行后，OMS 需要几分钟才能引入和处理该数据。

## <a name="access-monitoring-data"></a>访问监视数据

在 OMS 门户或 Azure 门户中，使用[容器解决方案](../../log-analytics/log-analytics-containers.md)查看和分析 OMS 容器监视数据。 

若要使用 [OMS 门户](https://mms.microsoft.com)安装容器解决方案，请转到“解决方案库”。 然后添加“容器解决方案”。 或者，添加 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft.containersoms?tab=Overview) 中的容器解决方案。

在 OMS 门户中，查找 OMS 仪表板中的“容器”摘要磁贴。 单击磁贴获取各种详细信息，包括：容器事件、错误、状态、映像清单以及 CPU 和内存使用率。 有关更详细的信息，请单击任何磁贴上的行，或执行[日志搜索](../../log-analytics/log-analytics-log-searches.md)。

![OMS 门户中的容器仪表板](./media/container-service-tutorial-kubernetes-monitor/oms-containers-dashboard.png)

同样，在 Azure 门户中，转到“Log Analytics”并选择工作区名称。 若要查看“容器”摘要磁贴，请单击“解决方案” > “容器”。 若要查看详细信息，请单击该磁贴。

有关查询和分析监视数据的详细指南，请参阅 [Azure Log Analytics 文档](../../log-analytics/index.md)。

## <a name="next-steps"></a>后续步骤

在本教程中，已使用 OMS 监视 Kubernetes 群集。 涉及的任务包括：

> [!div class="checklist"]
> * 获取 OMS 工作区设置
> * 在 Kubernetes 节点上设置 OMS 代理
> * 在 OMS 门户或 Azure 门户中访问监视信息


请访问以下链接查看预先生成的容器服务脚本示例。

> [!div class="nextstepaction"]
> [Azure 容器服务脚本示例](cli-samples.md)
