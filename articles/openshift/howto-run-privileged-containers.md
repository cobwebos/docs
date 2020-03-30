---
title: 在 Azure 红帽 OpenShift 群集中运行特权容器 |微软文档
description: 运行特权容器以监视安全性和合规性。
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 12/05/2019
keywords: aro， 开移， 水赛， 扭锁， 红帽子
ms.openlocfilehash: e1c1dd9f27a207f78dd22e271f6b070c7f92f622
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78271374"
---
# <a name="run-privileged-containers-in-an-azure-red-hat-openshift-cluster"></a>在 Azure Red Hat OpenShift 群集中运行特权容器

不能在 Azure 红帽 OpenShift 群集上运行任意特权容器。
允许在 ARO 群集上运行两个安全监视和合规性解决方案。
本文档介绍与安全产品供应商的通用 OpenShift 部署文档的差异。


在按照供应商的说明操作之前，请通读这些说明。
以下特定于产品步骤中的节标题直接引用供应商文档中的节标题。

## <a name="before-you-begin"></a>开始之前

大多数安全产品的文档假定您具有群集管理权限。
客户管理员在 Azure 红帽 OpenShift 中没有所有权限。 修改群集范围资源所需的权限是有限的。

首先，通过运行`oc get scc`，确保用户以客户管理员身份登录到群集。 作为客户管理组成员的所有用户都有权查看群集上的安全上下文约束 （SCC）。

接下来，确保`oc`二进制版本为`3.11.154`。
```
oc version
oc v3.11.154
kubernetes v1.11.0+d4cacc0
features: Basic-Auth GSSAPI Kerberos SPNEGO

Server https://openshift.aqua-test.osadev.cloud:443
openshift v3.11.154
kubernetes v1.11.0+d4cacc0
```

## <a name="product-specific-steps-for-aqua-security"></a>针对水安全的产品特定步骤
将要修改的基本说明可以在[Aqua 安全部署文档中](https://docs.aquasec.com/docs/openshift-red-hat)找到。 此处的步骤将与 Aqua 部署文档一起运行。

第一步是对要更新所需的 SCC 进行批号。 这些注释阻止群集的同步 Pod 还原对这些 SSC 的任何更改。

```
oc annotate scc hostaccess openshift.io/reconcile-protect=true
oc annotate scc privileged openshift.io/reconcile-protect=true
```

### <a name="step-1-prepare-prerequisites"></a>第 1 步：准备先决条件
请记住以 ARO 客户管理员而不是群集管理员角色登录到群集。

创建项目和服务帐户。
```
oc new-project aqua-security
oc create serviceaccount aqua-account -n aqua-security
```

使用以下命令将客户管理员群集角色分配给 Aqua 帐户，而不是分配群集读取器角色。
```
oc adm policy add-cluster-role-to-user customer-admin-cluster system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user privileged system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user hostaccess system:serviceaccount:aqua-security:aqua-account
```

继续按照步骤 1 中的剩余说明操作。  这些说明描述了为 Aqua 注册表设置机密。

### <a name="step-2-deploy-the-aqua-server-database-and-gateway"></a>第 2 步：部署水族服务器、数据库和网关
按照 Aqua 文档中提供的步骤安装水控制台.

修改提供的`aqua-console.yaml`。  删除标记为 的前两个对象，`kind: ClusterRole`和`kind: ClusterRoleBinding`。  不会创建这些资源，因为客户管理员此时没有修改`ClusterRole`和`ClusterRoleBinding`对象的权限。

第二个修改将到`kind: Route`的`aqua-console.yaml`部分。 替换`kind: Route``aqua-console.yaml`文件中对象的以下 yaml。
```
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  labels:
    app: aqua-web
  name: aqua-web
  namespace: aqua-security
spec:
  port:
    targetPort: aqua-web
  tls:
    insecureEdgeTerminationPolicy: Redirect
    termination: edge
  to:
    kind: Service
    name: aqua-web
    weight: 100
  wildcardPolicy: None
```

按照其余说明操作。

### <a name="step-3-login-to-the-aqua-server"></a>第 3 步：登录到水族服务器
此部分不会以任何方式修改。  请遵循 Aqua 文档。

使用以下命令获取 Aqua 控制台地址。
```
oc get route aqua-web -n aqua-security
```

### <a name="step-4-deploy-aqua-enforcers"></a>第 4 步：部署水上强制器
部署执行者时设置以下字段：

| 字段          | “值”         |
| -------------- | ------------- |
| 业务流程协调程序   | OpenShift     |
| ServiceAccount | 水帐户  |
| Project        | 水上安全 |

## <a name="product-specific-steps-for-prisma-cloud--twistlock"></a>棱镜云/Twistlock 的产品特定步骤

我们将修改的基本说明可在[Prisma 云部署文档中](https://docs.paloaltonetworks.com/prisma/prisma-cloud/19-11/prisma-cloud-compute-edition-admin/install/install_openshift.html)找到

首先安装"安装`twistcli`棱镜云"和"下载棱镜云软件"部分中所述的工具。

创建新的 OpenShift 项目
```
oc new-project twistlock
```

跳过可选部分"将棱镜云映像推送到专用注册表"。 它不会在 Azure 红帽开放移位上工作。 改用联机注册表。

您可以按照官方文档操作，同时应用下面描述的更正。
从"安装控制台"部分开始。

### <a name="install-console"></a>安装控制台

在`oc create -f twistlock_console.yaml`步骤 2 中，创建命名空间时将收到错误。
您可以安全地忽略它，命名空间以前已使用 命令`oc new-project`创建。

用于`azure-disk`存储类型。

### <a name="create-an-external-route-to-console"></a>创建到控制台的外部路由

如果您更喜欢 oc 命令，您可以按照文档或下面的说明进行操作。
将以下路由定义复制到计算机上的名为 twistlock_route.yaml 的文件
```
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  labels:
    name: console
  name: twistlock-console
  namespace: twistlock
spec:
  port:
    targetPort: mgmt-http
  tls:
    insecureEdgeTerminationPolicy: Redirect
    termination: edge
  to:
    kind: Service
    name: twistlock-console
    weight: 100
  wildcardPolicy: None
```
然后运行：
```
oc create -f twistlock_route.yaml
```

您可以使用以下命令获取分配给 Twistlock 控制台的 URL：`oc get route twistlock-console -n twistlock`

### <a name="configure-console"></a>配置控制台

请遵循 Twistlock 文档。

### <a name="install-defender"></a>安装防御器

在`oc create -f defender.yaml`步骤 2 中，在创建群集角色和群集角色绑定时，您将收到错误。
你可以忽略这些异常。

防御者将只部署在计算节点上。 您不必使用节点选择器来限制它们。
