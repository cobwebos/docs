---
title: 在 Azure 红帽 OpenShift 群集中部署独立的 Prometheus 实例 |微软文档
description: 在 Azure 红帽 OpenShift 群集中创建 Prometheus 实例，以监视应用程序的指标。
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 06/17/2019
keywords: 普罗米图斯， aro， 开班， 指标， 红帽子
ms.openlocfilehash: f81a993caa31578e689fb3a90108f3cf0ca81fc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "69875127"
---
# <a name="deploy-a-standalone-prometheus-instance-in-an-azure-red-hat-openshift-cluster"></a>在 Azure 红帽开放Shift 群集中部署独立的 Prometheus 实例

本文介绍如何配置在 Azure 红帽 OpenShift 群集中使用服务发现的独立 Prometheus 实例。

> [!NOTE]
> 客户管理员不需要访问 Azure 红帽 OpenShift 群集。

目标设置：

- 一个项目（普罗米苏斯项目），其中包含普罗米苏斯和警报管理器。
- 两个项目（应用项目1和应用项目2），其中包含要监视的应用程序。

您将在本地准备一些普罗米苏斯配置文件。 创建新文件夹以存储它们。 配置文件作为机密存储在群集中，以防以后将机密令牌添加到群集中。

## <a name="sign-in-to-the-cluster-by-using-the-oc-tool"></a>使用 OC 工具登录到群集

1. 打开 Web 浏览器，然后转到群集的 Web 控制台https://openshift（。*随机 ID*。*区域*.azmosa.io）。
2. 使用 Azure 凭据登录。
3. 在右上角选择您的用户名，然后选择 **"复制登录命令**"。
4. 将用户名粘贴到要使用的终端中。

> [!NOTE]
> 要查看是否登录到正确的群集，请运行该`oc whoami -c`命令。

## <a name="prepare-the-projects"></a>准备项目

要创建项目，请运行以下命令：
```
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2
```


> [!NOTE]
> 可以使用`-n`或`--namespace`参数，或通过运行`oc project`命令选择活动项目。

## <a name="prepare-the-prometheus-configuration-file"></a>准备普罗米图斯配置文件
通过输入以下内容创建 prometheus.yml 文件：
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
通过输入以下配置创建名为 Prom 的机密：
```
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
```

普罗米底斯.yml文件是一个基本的普罗米底斯配置文件。 它设置间隔并在三个项目中配置自动发现（prometheus 项目、应用项目 1、应用项目 2）。 在以前的配置文件中，自动发现的终结点在没有身份验证的情况下通过 HTTP 进行刮擦。

有关刮削端点的详细信息，请参阅[普罗米塞乌斯风景图](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config)。


## <a name="prepare-the-alertmanager-config-file"></a>准备警报管理器配置文件
通过输入以下内容创建警报管理器.yml 文件：
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
通过输入以下配置创建名为 Prom-Alert 的机密：
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

警报管理器.yml 是警报管理器配置文件。

> [!NOTE]
> 要验证前面的两个步骤，请运行该`oc get secret -n prometheus-project`命令。

## <a name="start-prometheus-and-alertmanager"></a>启动普罗米古斯和警报管理器
转到[开班/原发存储库](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus)并下载[prometheus 独立.yaml](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml)模板。 通过输入以下配置，将模板应用于 prometheus 项目：
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
prometheus 独立.yaml 文件是一个 OpenShift 模板。 它将创建一个 Prometheus 实例，其前面有 oauth 代理，并且使用 oauth 代理保护了警报管理器实例。 在此模板中，auth 代理配置为允许任何可以"获取"普罗米图斯项目命名空间的用户（请参阅`-openshift-sar`标志）。

> [!NOTE]
> 要验证 promStatefulSet 是否具有相等的"渴望"和"当前编号`oc get statefulset -n prometheus-project`副本"，请运行该命令。 要检查项目中的所有资源，请运行该`oc get all -n prometheus-project`命令。

## <a name="add-permissions-to-allow-service-discovery"></a>添加允许服务发现的权限

通过输入以下内容创建 prometheus-sdrole.yml 文件：
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
要将模板应用于要从中允许服务发现的所有项目，请运行以下命令：
```
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project2
oc process -f prometheus-sdrole.yml | oc apply -f - -n prometheus-project
```

> [!NOTE]
> 要验证角色和角色绑定是否正确创建，请运行`oc get role`和`oc get rolebinding`命令。

## <a name="optional-deploy-example-application"></a>可选：部署示例应用程序

一切工作，但没有指标源。 转到普罗米苏斯 URL （.https://prom-prometheus-project.apps*随机 ID*。*区域*.azmosa.io/）。 您可以使用以下命令找到它：

```
oc get route prom -n prometheus-project
```
> [!IMPORTANT]
> 请记住将https://前缀添加到主机名的开头。

**状态>服务发现**"页将显示 0/0 活动目标。

要部署在 /metrics 终结点下公开基本 Python 指标的示例应用程序，请运行以下命令：
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1

oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
新应用程序应在部署后 30 秒内在"服务发现"页上显示为有效目标。

有关详细信息，请选择 **"状态** > **目标**"。

> [!NOTE]
> 对于每个成功刮擦的目标，Prometheus 都会在上升指标中添加一个数据点。 选择左上角的**Prometheus，****向上**输入作为表达式，然后选择 **"执行**"。

## <a name="next-steps"></a>后续步骤

您可以将自定义普罗米底斯检测添加到应用程序。 Prometheus 客户端库简化了 Prometheus 指标的编制，可用于不同的编程语言。

有关详细信息，请参阅以下 GitHub 库：

 - [Java](https://github.com/prometheus/client_java)
 - [Python](https://github.com/prometheus/client_python)
 - [开始](https://github.com/prometheus/client_golang)
 - [Ruby](https://github.com/prometheus/client_ruby)
