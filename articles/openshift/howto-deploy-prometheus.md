---
title: 部署 Azure Red Hat OpenShift 群集中的独立 Prometheus 实例 |Microsoft Docs
description: 在 Azure Red Hat OpenShift 群集来监视应用程序的度量值中创建的 Prometheus 实例。
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 06/17/2019
keywords: prometheus aro、 openshift、 指标、 red hat
ms.openlocfilehash: a9748932a72106413677b21fe0efd1f69fb02e47
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827020"
---
# <a name="deploy-a-standalone-prometheus-instance-in-an-azure-red-hat-openshift-cluster"></a>部署 Azure Red Hat OpenShift 群集中的独立 Prometheus 实例

本文介绍如何配置 Azure Red Hat OpenShift 群集中使用服务发现的独立 Prometheus 实例。

> [!NOTE]
> 客户向 Azure Red Hat OpenShift 群集的管理员访问权限不是必需的。

目标安装程序：

- 一个项目 （prometheus-项目），其中包含 Prometheus 和 Alertmanager。
- 两个项目 （应用 project1 和应用 project2），其中包含要监视的应用程序。

将准备一些 Prometheus 配置文件在本地。 创建一个新文件夹来存储它们。 配置文件存储在群集中作为机密，以防机密令牌更高版本添加到群集。

## <a name="sign-in-to-the-cluster-by-using-the-oc-tool"></a>使用 OC 工具登录到群集

1. 打开 web 浏览器，然后转到你的群集的 web 控制台 (https://openshift 。*随机 id*。*区域*。 azmosa.io)。
2. 使用 Azure 凭据登录。
3. 在右上角中，选择你的用户名，然后选择**登录名复制命令**。
4. 粘贴到终端，将使用你的用户名。

> [!NOTE]
> 若要查看是否你已登录到正确的群集，运行`oc whoami -c`命令。

## <a name="prepare-the-projects"></a>准备项目

若要创建项目，请运行以下命令：
```
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2
```


> [!NOTE]
> 您可以使用`-n`或`--namespace`参数或选择通过运行某个活动项目`oc project`命令。

## <a name="prepare-the-prometheus-configuration-file"></a>准备 Prometheus 配置文件
输入以下内容创建 prometheus.yml 文件：
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
创建机密擦写存储器调用通过输入以下配置：
```
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
```

Prometheus.yml 文件是一个基本的 Prometheus 配置文件。 它设置的时间间隔，并在三个项目 （prometheus 项目，应用 project1，应用 project2） 配置自动发现。 在以前的配置文件中，已自动发现终结点会擦除通过 HTTP 而无需身份验证。

抓取终结点的详细信息，请参阅[Prometheus scape 配置](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config)。


## <a name="prepare-the-alertmanager-config-file"></a>准备 Alertmanager 配置文件
输入以下内容创建 alertmanager.yml 文件：
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
创建调用擦写存储器警报通过输入下面的配置的机密：
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

Alertmanager.yml 是警报管理器配置文件。

> [!NOTE]
> 若要验证两个前面的步骤，请运行`oc get secret -n prometheus-project`命令。

## <a name="start-prometheus-and-alertmanager"></a>启动 Prometheus 和 Alertmanager
转到[openshift/源存储库](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus)，并下载[prometheus standalone.yaml](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml)模板。 通过输入下面的配置，将模板应用到 prometheus 项目：
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
Prometheus standalone.yaml 文件是一个 OpenShift 模板。 它将使用它，并使用 oauth 代理还保护 Alertmanager 实例的前面的 oauth 代理创建 Prometheus 实例。 在此模板中，oauth 代理配置为允许任何用户都可以"获取"prometheus 项目命名空间 (请参阅`-openshift-sar`标志)。

> [!NOTE]
> 若要验证是否擦写存储器 StatefulSet 具有相等的所需和当前数字副本，请运行`oc get statefulset -n prometheus-project`命令。 若要检查项目中的所有资源，请运行`oc get all -n prometheus-project`命令。

## <a name="add-permissions-to-allow-service-discovery"></a>添加权限以允许服务发现

输入以下内容创建 prometheus sdrole.yml 文件：
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
若要将模板应用于你想允许服务发现的所有项目，运行以下命令：
```
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project2
```
若要从本身收集指标的 Prometheus，应用 prometheus project 中的权限。

> [!NOTE]
> 若要验证角色和 RoleBinding 已正确创建，请运行`oc get role`和`oc get rolebinding`命令。

## <a name="optional-deploy-example-application"></a>可选：将示例应用程序部署

一切正常，但没有任何度量值源。 转到 Prometheus URL (https://prom-prometheus-project.apps 。*随机 id*。*区域*.azmosa.io/)。 您可以使用以下命令进行查找：

```
oc get route prom -n prometheus-project
```
> [!IMPORTANT]
> 请记住将 https:// 前缀添加到主机名的开头。

**状态 > 服务发现**页面将显示 0/0 活动目标。

若要部署示例应用程序，它提供了基本的 Python 指标 /metrics 终结点下，运行以下命令：
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1

oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
在部署后的 30 秒内，新的应用程序应显示为服务发现页面上的有效目标。

对于更多详细信息，请选择**状态** > **目标**。

> [!NOTE]
> 对于每个成功抓取目标，Prometheus 最指标中添加一个数据点。 选择**Prometheus**在左上角中，输入**向上**作为表达式，然后选择**Execute**。

## <a name="next-steps"></a>后续步骤

您可以将自定义 Prometheus 检测添加到你的应用程序。 Prometheus 客户端库，它简化了 Prometheus 指标准备，就可以进行不同的编程语言。

有关详细信息，请参阅以下 GitHub 库：

 - [Java](https://github.com/prometheus/client_java)
 - [Python](https://github.com/prometheus/client_python)
 - [Go](https://github.com/prometheus/client_golang)
 - [Ruby](https://github.com/prometheus/client_ruby)
