---
title: 在 Azure Red Hat OpenShift 群集中运行特权容器 |Microsoft Docs
description: 运行特权容器来监视安全性和符合性。
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 12/05/2019
keywords: aro、openshift、aquasec、twistlock、red hat
ms.openlocfilehash: e1c1dd9f27a207f78dd22e271f6b070c7f92f622
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2020
ms.locfileid: "78271374"
---
# <a name="run-privileged-containers-in-an-azure-red-hat-openshift-cluster"></a>在 Azure Red Hat OpenShift 群集中运行特权容器

无法在 Azure Red Hat OpenShift 群集上运行任意特权容器。
允许在 ARO 群集上运行两种安全监视和符合性解决方案。
本文档介绍了安全产品供应商的一般 OpenShift 部署文档的不同之处。


请在遵循供应商说明之前通读这些说明。
以下特定于产品的步骤中的章节标题直接引用供应商文档中的章节标题。

## <a name="before-you-begin"></a>开始之前

大多数安全产品的文档假定你具有群集管理员权限。
客户管理员不具备 Azure Red Hat OpenShift 的所有权限。 修改群集范围内的资源所需的权限受到限制。

首先，通过运行 `oc get scc`确保用户以客户管理员身份登录到群集。 作为 customer 管理员组成员的所有用户都有权查看群集上的安全上下文约束（Scc）。

接下来，确保 `3.11.154``oc` 二进制版本。
```
oc version
oc v3.11.154
kubernetes v1.11.0+d4cacc0
features: Basic-Auth GSSAPI Kerberos SPNEGO

Server https://openshift.aqua-test.osadev.cloud:443
openshift v3.11.154
kubernetes v1.11.0+d4cacc0
```

## <a name="product-specific-steps-for-aqua-security"></a>绿色安全的产品特定步骤
即将修改的基本说明可在 "[浅绿安全部署" 文档](https://docs.aquasec.com/docs/openshift-red-hat)中找到。 此处的步骤将与水绿色部署文档一起运行。

第一步是对要更新的所需 Scc 添加批注。 这些批注阻止群集的同步 Pod 恢复对这些 SSCs 的任何更改。

```
oc annotate scc hostaccess openshift.io/reconcile-protect=true
oc annotate scc privileged openshift.io/reconcile-protect=true
```

### <a name="step-1-prepare-prerequisites"></a>步骤1：准备必备组件
请记住以 ARO 客户管理员而不是群集管理员角色登录到群集。

创建项目和服务帐户。
```
oc new-project aqua-security
oc create serviceaccount aqua-account -n aqua-security
```

可以通过以下命令将客户管理员群集角色分配给浅绿帐户，而不是分配群集读取器角色。
```
oc adm policy add-cluster-role-to-user customer-admin-cluster system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user privileged system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user hostaccess system:serviceaccount:aqua-security:aqua-account
```

继续按照步骤1中的其余说明进行操作。  这些说明描述了如何设置水绿色注册表的机密。

### <a name="step-2-deploy-the-aqua-server-database-and-gateway"></a>步骤2：部署浅绿色服务器、数据库和网关
按照水绿色文档中提供的步骤来安装 yaml。

修改提供的 `aqua-console.yaml`。  删除标记为 "`kind: ClusterRole`" 和 "`kind: ClusterRoleBinding`" 的前两个对象。  由于客户管理员此时无权修改 `ClusterRole` 和 `ClusterRoleBinding` 对象，因此不会创建这些资源。

第二个修改将针对 `aqua-console.yaml`的 `kind: Route` 部分。 替换 `aqua-console.yaml` 文件中 `kind: Route` 对象的以下 yaml。
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

请按照其余说明进行操作。

### <a name="step-3-login-to-the-aqua-server"></a>步骤3：登录到浅绿色服务器
此部分不会以任何方式进行修改。  按照水绿色的文档进行操作。

使用以下命令获取浅绿控制台地址。
```
oc get route aqua-web -n aqua-security
```

### <a name="step-4-deploy-aqua-enforcers"></a>步骤4：部署浅绿 Enforcers
部署 enforcers 时设置以下字段：

| 字段          | 值         |
| -------------- | ------------- |
| 业务流程协调程序   | OpenShift     |
| ServiceAccount | 浅绿-帐户  |
| Project        | 浅绿色-安全性 |

## <a name="product-specific-steps-for-prisma-cloud--twistlock"></a>Prisma Cloud/Twistlock 的特定于产品的步骤

我们要修改的基本说明可在[Prisma Cloud 部署文档](https://docs.paloaltonetworks.com/prisma/prisma-cloud/19-11/prisma-cloud-compute-edition-admin/install/install_openshift.html)中找到

首先安装 `twistcli` 工具，如 "安装 Prisma Cloud" 和 "下载 Prisma Cloud software" 部分所述。

创建新的 OpenShift 项目
```
oc new-project twistlock
```

跳过可选部分 "将 Prisma 云映像推送到专用注册表"。 它不适用于 Azure Red Hat Openshift。 改为使用联机注册表。

在应用以下所述的更正时，可以遵循官方文档。
从 "安装控制台" 部分开始。

### <a name="install-console"></a>安装控制台

在步骤2中 `oc create -f twistlock_console.yaml` 期间，你将在创建命名空间时收到错误。
您可以放心地忽略该命名空间，先前已通过 `oc new-project` 命令创建了该命名空间。

将 `azure-disk` 用于存储类型。

### <a name="create-an-external-route-to-console"></a>创建到控制台的外部路由

如果喜欢 oc 命令，你可以按照文档或下面的说明进行操作。
将以下路由定义复制到计算机上 twistlock_route 名为 yaml 的文件中
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

可以通过以下命令获取分配给 Twistlock 控制台的 URL： `oc get route twistlock-console -n twistlock`

### <a name="configure-console"></a>配置控制台

请参阅 Twistlock 文档。

### <a name="install-defender"></a>安装 Defender

在步骤2中 `oc create -f defender.yaml` 期间，你将在创建群集角色和群集角色绑定时收到错误。
您可以忽略它们。

将仅在计算节点上部署防守。 不必使用节点选择器来限制它们。
