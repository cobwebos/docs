---
title: 在 Azure Red Hat OpenShift 群集中部署独立的 Prometheus 实例 |Microsoft Docs
description: 在 Azure Red Hat OpenShift 群集中创建 Prometheus 实例, 以监视应用程序的指标。
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 06/17/2019
keywords: prometheus, aro, openshift, 指标, red hat
ms.openlocfilehash: f81a993caa31578e689fb3a90108f3cf0ca81fc2
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2019
ms.locfileid: "69875127"
---
# <a name="deploy-a-standalone-prometheus-instance-in-an-azure-red-hat-openshift-cluster"></a>在 Azure Red Hat OpenShift 群集中部署独立的 Prometheus 实例

本文介绍如何配置一个独立的 Prometheus 实例, 该实例使用 Azure Red Hat OpenShift 群集中的服务发现。

> [!NOTE]
> 不需要客户对 Azure Red Hat OpenShift 群集的访问权限。

目标设置:

- 一个项目 (prometheus), 其中包含 Prometheus 和 Alertmanager。
- 包含要监视的应用程序的两个项目 (应用程序 project1 和 project2)。

你将在本地准备一些 Prometheus 配置文件。 创建新文件夹以存储它们。 配置文件在群集中存储为机密, 以防机密令牌稍后添加到群集中。

## <a name="sign-in-to-the-cluster-by-using-the-oc-tool"></a>使用 OC 工具登录到群集

1. 打开 web 浏览器, 然后前往群集的 web 控制台 (https://openshift 。*随机 id*。azmosa.io)。
2. 使用 Azure 凭据登录。
3. 在右上角选择你的用户名, 然后选择 "**复制登录命令**"。
4. 将用户名粘贴到将使用的终端。

> [!NOTE]
> 若要查看是否登录到了正确的群集, 请运行`oc whoami -c`命令。

## <a name="prepare-the-projects"></a>准备项目

若要创建项目, 请运行以下命令:
```
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2
```


> [!NOTE]
> 您可以使用`-n`或`--namespace`参数, 也可以通过运行`oc project`命令来选择活动项目。

## <a name="prepare-the-prometheus-configuration-file"></a>准备 Prometheus 配置文件
通过输入以下内容来创建 prometheus docker-compose.override.yml 文件:
```
global:
  scrape_interval: 30s
  evaluation_interval: 5s

scrape_configs:
    - job_name: prom-sd
      scrape_interval: 30s
      scrape_timeout: 10s
      metrics_path: /metrics
      scheme: http
      kubernetes_sd_configs:
      - api_server: null
        role: endpoints
        namespaces:
          names:
          - prometheus-project
          - app-project1
          - app-project2
```
输入以下配置, 创建名为 Prom 的机密:
```
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
```

Prometheus. docker-compose.override.yml 文件是一个基本的 Prometheus 配置文件。 它设置间隔, 并在三个项目中配置自动发现 (prometheus、project1、project2)。 在前面的配置文件中, 自动发现的终结点通过 HTTP 擦除, 无需身份验证。

有关抓取终结点的详细信息, 请参阅[Prometheus 局势 config](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config)。


## <a name="prepare-the-alertmanager-config-file"></a>准备 Alertmanager config 文件
通过输入以下内容来创建 alertmanager docker-compose.override.yml 文件:
```
global:
  resolve_timeout: 5m
route:
  group_wait: 30s
  group_interval: 5m
  repeat_interval: 12h
  receiver: default
  routes:
  - match:
      alertname: DeadMansSwitch
    repeat_interval: 5m
    receiver: deadmansswitch
receivers:
- name: default
- name: deadmansswitch
```
通过输入以下配置创建名为 Prom-警报的机密:
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

Alertmanager. docker-compose.override.yml 是警报管理器配置文件。

> [!NOTE]
> 若要验证前面的两个步骤, `oc get secret -n prometheus-project`请运行命令。

## <a name="start-prometheus-and-alertmanager"></a>启动 Prometheus 和 Alertmanager
请参阅[openshift/源存储库](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus)并下载[prometheus yaml](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml)模板。 通过输入以下配置将模板应用到 prometheus-项目:
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
Prometheus yaml 文件是一个 OpenShift 模板。 它将创建一个 Prometheus 实例, 并在其前面创建一个具有 oauth 代理的实例和一个 Alertmanager 实例, 该实例也使用 oauth 代理进行保护。 在此模板中, oauth 代理配置为允许任何可 "获取" prometheus 命名空间的用户 (请参阅`-openshift-sar`标志)。

> [!NOTE]
> 若要验证 prom StatefulSet 是否具有相同的所需和当前数量的副本`oc get statefulset -n prometheus-project` , 请运行命令。 若要检查项目中的所有资源, 请`oc get all -n prometheus-project`运行命令。

## <a name="add-permissions-to-allow-service-discovery"></a>添加权限以允许服务发现

通过输入以下内容来创建 prometheus-sdrole docker-compose.override.yml 文件:
```
apiVersion: template.openshift.io/v1
kind: Template
metadata:
  name: prometheus-sdrole
  annotations:
    "openshift.io/display-name": Prometheus service discovery role
    description: |
      Role and rolebinding added permissions required for service discovery in a given project.
    iconClass: fa fa-cogs
    tags: "monitoring,prometheus,alertmanager,time-series"
parameters:
- description: The project name, where a standalone Prometheus is deployed
  name: PROMETHEUS_PROJECT
  value: prometheus-project
objects:
- apiVersion: rbac.authorization.k8s.io/v1
  kind: Role
  metadata:
    name: prometheus-sd
  rules:
  - apiGroups:
    - ""
    resources:
    - services
    - endpoints
    - pods
    verbs:
    - list
    - get
    - watch
- apiVersion: rbac.authorization.k8s.io/v1
  kind: RoleBinding
  metadata:
    name: prometheus-sd
  roleRef:
    apiGroup: rbac.authorization.k8s.io
    kind: Role
    name: prometheus-sd
  subjects:
  - kind: ServiceAccount
    name: prom
    namespace: ${PROMETHEUS_PROJECT}
```
若要将模板应用于要从中允许服务发现的所有项目, 请运行以下命令:
```
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project2
oc process -f prometheus-sdrole.yml | oc apply -f - -n prometheus-project
```

> [!NOTE]
> 若要验证是否正确创建了 Role 和 RoleBinding, 请`oc get role`运行`oc get rolebinding`和命令。

## <a name="optional-deploy-example-application"></a>可选：部署示例应用程序

一切正常, 但没有度量值源。 转到 Prometheus URL (https://prom-prometheus-project.apps.*随机 id*.*区域*.azmosa.io/)。 可以使用以下命令找到它:

```
oc get route prom -n prometheus-project
```
> [!IMPORTANT]
> 请记得将 https://前缀添加到主机名的开头。

"**状态 > 服务发现**" 页将显示0/0 活动目标。

若要部署示例应用程序, 该应用程序在/metrics 终结点下公开基本 Python 指标, 请运行以下命令:
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1

oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
部署后, 新应用程序应在30秒内出现在服务发现页上的有效目标。

有关更多详细信息, 请选择 "**状态** > **目标**"。

> [!NOTE]
> 对于每个成功的擦除目标, Prometheus 将在 up 度量值中添加一个数据点。 选择左上角的 " **Prometheus** ", 输入作为表达式, 然后选择 "**执行**"。

## <a name="next-steps"></a>后续步骤

可以向应用程序添加自定义 Prometheus 检测。 Prometheus 客户端库简化了 Prometheus 指标准备, 可用于不同的编程语言。

有关详细信息, 请参阅以下 GitHub 库:

 - [Java](https://github.com/prometheus/client_java)
 - [Python](https://github.com/prometheus/client_python)
 - [Go](https://github.com/prometheus/client_golang)
 - [Ruby](https://github.com/prometheus/client_ruby)
