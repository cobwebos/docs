---
title: 部署 Azure Red Hat OpenShift 群集中的独立 Prometheus |Microsoft Docs
description: 下面介绍了如何监视应用程序的指标的 Azure Red Hat OpenShift 群集上创建 Prometheus 的实例。
author: Makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 06/17/2019
keywords: prometheus aro、 openshift、 指标、 red hat
ms.openlocfilehash: e66658151361edd43f61d398274c88c047928028
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342502"
---
# <a name="deploy-a-standalone-prometheus-in-an-azure-red-hat-openshift-cluster"></a>部署独立的 Prometheus Azure Red Hat OpenShift 群集中

本指南将介绍如何配置独立的 Prometheus Azure Red Hat OpenShift 群集中的服务发现。  不需要访问群集的"客户管理员"权限。

目标安装程序是按如下所示：

- 一个项目 （prometheus-项目），其中包含 Prometheus 和 Alertmanager
- 两个项目 （应用 project1 和应用 project2），其中包含要监视的应用程序

将准备一些 Prometheus 配置文件在本地。 创建一个新文件夹来存储它们。
这些配置文件将存储在群集中以机密形式以防机密令牌添加到这些更高版本。

## <a name="step-1-sign-in-to-the-cluster-using-the-oc-tool"></a>步骤 1：登录到群集使用`oc`工具
使用 web 浏览器中，导航到群集的 web 控制台 (https://openshift 。*随机 id*。*区域*。 azmosa.io)。
使用 Azure 凭据登录。
单击右上角中的用户名，然后选择"复制登录命令"。 将其粘贴到你将使用的终端。

你可以验证是否你已登录到正确的群集与`oc whoami -c`命令。

## <a name="step-2-prepare-the-projects"></a>步骤 2：准备项目

创建项目。
```
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2
```


> [!NOTE]
> 您可以使用`-n`或`--namespace`参数或选择活动项目与`oc project`命令

## <a name="step-3-prepare-prometheus-config"></a>步骤 3：准备 Prometheus 配置
创建名为 prometheus.yml 包含以下内容的文件。
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
创建名为"擦写存储器"与配置的机密。
```
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
```

上面列出的文件是一个基本的 Prometheus 配置文件。
它设置的时间间隔，并在三个项目 （prometheus 项目，应用 project1，应用 project2） 配置自动发现。
在此示例中，自动发现终结点将通过 HTTP 擦除未经身份验证。
抓取终结点的详细信息，请参阅 https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config 。


## <a name="step-4-prepare-alertmanager-config"></a>步骤 4：准备 Alertmanager 配置
创建名为 alertmanager.yml 包含以下内容的文件。
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
创建名为"擦写存储器-警报"与配置的机密。
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

上面列出的文件是警报管理器配置文件。

> [!NOTE]
> 你可以验证与两个前面的步骤 `oc get secret -n prometheus-project`

## <a name="step-5-start-prometheus-and-alertmanager"></a>步骤 5：启动 Prometheus 和 Alertmanager
下载[prometheus standalone.yaml](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml)从模板[openshift/源存储库](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus)并将其应用 prometheus 项目中
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
Prometheus standalone.yaml 文件是一个 OpenShift 模板，这将使用它，并使用 oauth 代理还保护 Alertmanager 实例的前面 oauth 代理创建 Prometheus 实例。  此模板中 oauth 代理配置为允许任何用户都可以"获取""prometheus 项目"命名空间 (请参阅`-openshift-sar`标志)。

> [!NOTE]
> 你可以验证是否有等于"擦写存储器"StatefulSet*所需*和*当前*编号与副本`oc get statefulset -n prometheus-project`命令。
> 你还可以查看与项目中的所有资源`oc get all -n prometheus-project`。

## <a name="step-6-add-permissions-to-allow-service-discovery"></a>步骤 6：添加权限以允许服务发现
使用以下内容创建 prometheus sdrole.yml。
```
apiVersion: template.openshift.io/v1
kind: Template
metadata:
  name: prometheus-sdrole
  annotations:
    "openshift.io/display-name": Prometheus Service Discovery Role
    description: |
      A role and rolebinding adding permissions required to perform Service Discovery in a given project.
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
将模板应用到想要允许服务发现的所有项目。
```
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project2
```
如果你想要能够从本身收集指标的 Prometheus，记住过应用 prometheus project 中的权限。

> [!NOTE]
> 你可以验证是否的角色和 RoleBinding 正确创建了具有`oc get role`和`oc get rolebinding`分别命令

## <a name="optional-deploy-example-application"></a>可选：将示例应用程序部署
一切正常，但没有任何度量值源。 转到 Prometheus URL (https://prom-prometheus-project.apps 。*随机 id*。*区域*.azmosa.io/)，可以使用以下命令找到。
```
oc get route prom -n prometheus-project
```
请记住前缀 https:// 开头的主机名。

**状态 > 服务发现**页面将显示 0/0 活动目标。

若要部署示例应用程序，它提供了基本的 python 运行以下命令在 /metrics 终结点下的度量值。
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
在部署后，新的应用程序将显示为 30 秒内的服务发现页上的有效目标。 你可以获取更多详细信息**状态 > 目标**页。

> [!NOTE]
> 对于每个成功抓取目标 Prometheus 在"已启动"度量值添加 datapoint。 单击**Prometheus**在左上角并作为表达式，然后单击"up"输入**Execute**。

## <a name="next-steps"></a>后续步骤

您可以将自定义 Prometheus 检测添加到你的应用程序。

Prometheus 客户端库，它简化了准备 Prometheus 度量值，就可以进行不同的编程语言。

 - Java https://github.com/prometheus/client_java
 - Python https://github.com/prometheus/client_python
 - 转到 https://github.com/prometheus/client_golang
 - Ruby https://github.com/prometheus/client_ruby
