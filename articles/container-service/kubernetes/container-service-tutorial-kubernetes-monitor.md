---
title: （已弃用）Azure 容器服务教程 - 监视 Kubernetes
description: Azure 容器服务教程 - 使用 Log Analytics 监视 Kubernetes
author: iainfoulds
ms.service: container-service
ms.topic: tutorial
ms.date: 04/05/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 84c2438a8c25b1b64f46e12923212812beac687d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "78273327"
---
# <a name="deprecated-monitor-a-kubernetes-cluster-with-log-analytics"></a>（已弃用）使用 Log Analytics 监视 Kubernetes 群集

> [!TIP]
> 有关使用 Azure Kubernetes 服务的此教程的更新版本，请参阅[用于容器的 Azure Monitor（预览版）概述](../../azure-monitor/insights/container-insights-overview.md)。

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

监视 Kubernetes 群集和容器至关重要，特别是在使用多个应用大规模地管理生产群集时。

可以利用 Microsoft 或其他提供程序提供的多个 Kubernetes 监视解决方案。 在本教程中，我们将使用 Microsoft 基于云的 IT 管理解决方案 [Log Analytics](../../operations-management-suite/operations-management-suite-overview.md) 中的容器解决方案来监视 Kubernetes 群集。 （容器解决方案为预览版。）

本教程第 7 部分（共 7 部分）涵盖以下任务：

> [!div class="checklist"]
> * 获取 Log Analytics 工作区设置
> * 在 Kubernetes 节点上设置 Log Analytics 代理
> * 在 Log Analytics 门户或 Azure 门户中访问监视信息

## <a name="before-you-begin"></a>开始之前

在前面的教程中，我们将应用程度打包到容器映像中，将这些映像上传到 Azure 容器注册表，并创建了 Kubernetes 群集。

如果尚未完成这些步骤，并且想要逐一完成，请返回到[教程 1 – 创建容器映像](./container-service-tutorial-kubernetes-prepare-app.md)。

## <a name="get-workspace-settings"></a>获取工作区设置

如果可以访问 [Log Analytics 门户](https://mms.microsoft.com)，请转到“设置” > “连接的源” > “Linux 服务器”。 你可以在其中找到工作区 ID 和主要或辅助工作区密钥。   记下这些值，在群集上设置 Log Analytics 代理需要这些值。

## <a name="create-kubernetes-secret"></a>创建 Kubernetes 机密

使用 [kubectl create secret][kubectl-create-secret] 命令在名为 `omsagent-secret` 的 Kubernetes 机密中存储 Log Analytics 工作区设置。 将 `WORKSPACE_ID` 更新为 Log Analytics 工作区 ID，将 `WORKSPACE_KEY` 更新为工作区密钥。

```console
kubectl create secret generic omsagent-secret --from-literal=WSID=WORKSPACE_ID --from-literal=KEY=WORKSPACE_KEY
```

## <a name="set-up-log-analytics-agents"></a>设置 Log Analytics 代理

在 Kubernetes 群集上配置容器监视代理时，可以使用以下 Kubernetes 清单文件。 该文件将创建 Kubernetes [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/)，它可在每个群集节点上运行单个相同 Pod。

将以下文本保存到名为 `oms-daemonset.yaml` 的文件。

```yaml
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
 name: omsagent
spec:
 template:
  metadata:
   labels:
    app: omsagent
    agentVersion: 1.4.3-174
    dockerProviderVersion: 1.0.0-30
  spec:
   containers:
     - name: omsagent
       image: "microsoft/oms"
       imagePullPolicy: Always
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
        - mountPath: /etc/omsagent-secret
          name: omsagent-secret
          readOnly: true
        - mountPath: /var/lib/docker/containers
          name: containerlog-path
       livenessProbe:
        exec:
         command:
         - /bin/bash
         - -c
         - ps -ef | grep omsagent | grep -v "grep"
        initialDelaySeconds: 60
        periodSeconds: 60
   nodeSelector:
    beta.kubernetes.io/os: linux
   # Tolerate a NoSchedule taint on master that ACS Engine sets.
   tolerations:
    - key: "node-role.kubernetes.io/master"
      operator: "Equal"
      value: "true"
      effect: "NoSchedule"
   volumes:
    - name: docker-sock
      hostPath:
       path: /var/run/docker.sock
    - name: host-log
      hostPath:
       path: /var/log
    - name: omsagent-secret
      secret:
       secretName: omsagent-secret
    - name: containerlog-path
      hostPath:
       path: /var/lib/docker/containers
```

使用以下命令创建 DaemonSet：

```console
kubectl create -f oms-daemonset.yaml
```

若要查看是否已创建 DaemonSet，请运行：

```console
kubectl get daemonset
```

输出与下面类似：

```output
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE-SELECTOR   AGE
omsagent   3         3         3         0            3           <none>          5m
```

代理运行后，Log Analytics 需要几分钟才能引入和处理该数据。

## <a name="access-monitoring-data"></a>访问监视数据

在 Log Analytics 门户或 Azure 门户中，使用[容器解决方案](../../azure-monitor/insights/containers.md)查看和分析容器监视数据。

若要使用 [Log Analytics 门户](https://mms.microsoft.com)安装容器解决方案，请转到“解决方案库”  。 然后添加“容器解决方案”  。 或者，添加 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft.containersoms?tab=Overview)中的容器解决方案。

在 Log Analytics 门户中，找到仪表板中的“容器”  摘要磁贴。 单击磁贴获取各种详细信息，包括：容器事件、错误、状态、映像清单以及 CPU 和内存使用率。 有关更详细的信息，请单击任何磁贴上的行，或执行[日志搜索](../../log-analytics/log-analytics-log-searches.md)。

![Azure 门户中的容器仪表板](./media/container-service-tutorial-kubernetes-monitor/oms-containers-dashboard.png)

同样，在 Azure 门户中，转到“Log Analytics”  并选择工作区名称。 若要查看“容器”  摘要磁贴，请单击“解决方案”   > “容器”  。 若要查看详细信息，请单击该磁贴。

有关查询和分析监视数据的详细指南，请参阅 [Azure Log Analytics 文档](../../azure-monitor/log-query/log-query-overview.md)。

## <a name="next-steps"></a>后续步骤

在本教程中，我们已使用 Log Analytics 监视 Kubernetes 群集。 涉及的任务包括：

> [!div class="checklist"]
> * 获取 Log Analytics 工作区设置
> * 在 Kubernetes 节点上设置 Log Analytics 代理
> * 在 Log Analytics 门户或 Azure 门户中访问监视信息


请访问以下链接查看预先生成的容器服务脚本示例。

> [!div class="nextstepaction"]
> [Azure 容器服务脚本示例](cli-samples.md)
