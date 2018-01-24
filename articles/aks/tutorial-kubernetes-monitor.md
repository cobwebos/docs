---
title: "Azure 上的 Kubernetes 教程 - 监视 Kubernetes"
description: "AKS 教程 - 使用 Microsoft Operations Management Suite (OMS) 监视 Kubernetes"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: b01aa01df198ce75b2f8b66d28a2db68b1c30b87
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="monitor-azure-container-service-aks"></a>监视 Azure 容器服务 (AKS)

监视 Kubernetes 群集和容器至关重要，特别是在使用多个应用程序大规模地运行生产群集时。

在本教程中，使用 [Log Analytics 的容器解决方案][log-analytics-containers]配置 AKS 群集的监视功能。

本教程第 7 部分（共 8 部分）涵盖以下任务：

> [!div class="checklist"]
> * 配置容器监视解决方案
> * 配置监视代理
> * 在 Azure 门户中访问监视信息

## <a name="before-you-begin"></a>开始之前

在前面的教程中，我们将应用程度打包到容器映像中，将这些映像上传到 Azure 容器注册表，并创建了 Kubernetes 群集。

如果尚未完成这些步骤，并且想要逐一完成，请返回到[教程 1 – 创建容器映像][aks-tutorial-prepare-app]。

## <a name="configure-the-monitoring-solution"></a>配置监视解决方案

在 Azure 门户中，选择“新建”并搜索 `Container Monitoring Solution`。 找到后，选择“创建”。

![添加解决方案](./media/container-service-tutorial-kubernetes-monitor/add-solution.png)

新建一个 OMS 工作区或选择现有 OMS 工作区。 “OMS 工作区”窗体将引导你完成此过程。

创建工作区时，请选择“固定到仪表板”以便于检索。

![OMS 工作区](./media/container-service-tutorial-kubernetes-monitor/oms-workspace.png)

完成后，选择“确定”。 完成验证后，选择“创建”以创建容器监视解决方案。

创建工作区后，将在 Azure 门户中显示该工作区。

## <a name="get-workspace-settings"></a>获取工作区设置

在 Kubernetes 节点上配置解决方案代理时，需要 Log Analytics 工作区 ID 和密钥。

若要检索这些值，请从容器解决方案左侧菜单中选择“OMS 工作区”。 选择“高级设置”并记下**工作区 ID** 和**主密钥**。

## <a name="configure-monitoring-agents"></a>配置监视代理

在 Kubernetes 群集上配置容器监视代理时，可以使用以下 Kubernetes 清单文件。 该文件将创建 Kubernetes [DaemonSet][kubernetes-daemonset]，它可在每个群集节点上运行单个 Pod。

将以下文本保存到名为 `oms-daemonset.yaml` 的文件，并将 `WSID` 和 `KEY` 的占位符值替换为 Log Analytics 工作区 ID 和密钥。

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
    agentVersion: 1.4.0-12
    dockerProviderVersion: 10.0.0-25
  spec:
   containers:
     - name: omsagent
       image: "microsoft/oms"
       imagePullPolicy: Always
       env:
       - name: WSID
         value: <WSID>
       - name: KEY
         value: <KEY>
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
        - mountPath: /var/opt/microsoft/omsagent/state/containerhostname
          name: container-hostname
        - mountPath: /var/log
          name: host-log
        - mountPath: /var/lib/docker/containers/
          name: container-log
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
    - name: container-hostname
      hostPath:
       path: /etc/hostname
    - name: host-log
      hostPath:
       path: /var/log
    - name: container-log
      hostPath:
       path: /var/lib/docker/containers/
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

```
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE-SELECTOR                 AGE
omsagent   3         3         3         3            3           beta.kubernetes.io/os=linux   8m
```

代理运行后，OMS 需要几分钟才能引入和处理该数据。

## <a name="access-monitoring-data"></a>访问监视数据

在 Azure 门户中，选择已固定到门户仪表板的 Log Analytics 工作区。 单击“容器监视解决方案”磁贴。 可在此处找到有关 AKS 群集和群集中的容器的信息。

![仪表板](./media/container-service-tutorial-kubernetes-monitor/oms-containers-dashboard.png)

有关查询和分析监视数据的详细指南，请参阅 [Azure Log Analytics 文档][log-analytics-docs]。

## <a name="next-steps"></a>后续步骤

在本教程中，已使用 OMS 监视 Kubernetes 群集。 涉及的任务包括：

> [!div class="checklist"]
> * 配置容器监视解决方案
> * 配置监视代理
> * 在 Azure 门户中访问监视信息

继续学习下一个教程，了解如何将 Kubernetes 升级到新版本。

> [!div class="nextstepaction"]
> [升级 Kubernetes][aks-tutorial-upgrade]

<!-- LINKS - external -->
[kubernetes-daemonset]: https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/

<!-- LINKS - internal -->
[aks-tutorial-deploy-app]: ./tutorial-kubernetes-deploy-application.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-upgrade]: ./tutorial-kubernetes-upgrade-cluster.md
[log-analytics-containers]: ../log-analytics/log-analytics-containers.md
[log-analytics-docs]: ../log-analytics/index.yml
