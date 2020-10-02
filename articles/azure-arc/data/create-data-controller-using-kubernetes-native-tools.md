---
title: 使用 Kubernetes 工具创建数据控制器
description: 使用 Kubernetes 工具创建数据控制器
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: bfdda75c0826ed12fbce1eb47680f91abbde4934
ms.sourcegitcommit: 487a9f5272300d60df2622c3d13e794d54680f90
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2020
ms.locfileid: "91661051"
---
# <a name="create-azure-arc-data-controller-using-kubernetes-tools"></a>使用 Kubernetes 工具创建 Azure Arc 数据控制器

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>必备条件

有关概述信息，请参阅 [创建 Azure Arc 数据控制器](create-data-controller.md) 主题。

若要使用 Kubernetes 工具创建 Azure Arc 数据控制器，需要安装 Kubernetes 工具。  本文中的示例将使用 `kubectl` ，但类似的方法可与其他 Kubernetes 工具（如 Kubernetes 仪表板）或使用 `oc` `helm` 这些工具和 Kubernetes yaml/json 一起使用。

[安装 kubectl 工具](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

> [!NOTE]
> 下面所示的创建 Azure Arc 数据控制器的一些步骤需要 Kubernetes 群集管理员权限。  如果您不是 Kubernetes 群集管理员，则需要让 Kubernetes 群集管理员代表您执行这些步骤。

### <a name="cleanup-from-past-installations"></a>清除过去的安装

如果过去安装了 Azure Arc 数据控制器，则在同一群集上使用命令删除 Azure Arc 数据控制器时， `azdata arc dc delete` 可能仍需要删除某些群集级别的对象。 运行以下命令以删除 Azure Arc 数据控制器群集级别对象：

```console
# Cleanup azure arc data service artifacts
kubectl delete crd datacontrollers.arcdata.microsoft.com 
kubectl delete sqlmanagedinstances.sql.arcdata.microsoft.com 
kubectl delete postgresql-11s.arcdata.microsoft.com 
kubectl delete postgresql-12s.arcdata.microsoft.com
kubectl delete clusterroles azure-arc-data:cr-arc-metricsdc-reader
kubectl delete clusterrolebindings azure-arc-data:crb-arc-metricsdc-reader
```

## <a name="overview"></a>概述

创建 Azure Arc 数据控制器具有以下高级步骤：
1. 为 Arc 数据控制器、Azure SQL 托管实例和 PostgreSQL 超大规模创建自定义资源定义。 **[要求 Kubernetes 群集管理员权限]**
2. 创建将在其中创建数据控制器的命名空间。 **[要求 Kubernetes 群集管理员权限]**
3. 创建引导程序服务，包括副本集、服务帐户、角色和角色绑定。
4. 为数据控制器管理员用户名和密码创建机密。
5. 创建数据控制器。
   
## <a name="create-the-custom-resource-definitions"></a>创建自定义资源定义

运行以下命令以创建自定义资源定义。  **[要求 Kubernetes 群集管理员权限]**

```console
kubectl create -f https://raw.githubusercontent.com/microsoft/azure_arc/master/arc_data_services/deploy/yaml/custom-resource-definitions.yaml
```

## <a name="create-a-namespace-in-which-the-data-controller-will-be-created"></a>创建将在其中创建数据控制器的命名空间

运行与下面类似的命令，以创建将在其中创建数据控制器的新的专用命名空间。  在此示例和本文的其余部分中，将使用的命名空间名称 `arc` 。 如果选择使用其他名称，请在整个中使用相同的名称。

```console
kubectl create namespace arc
```

如果其他人将使用不是群集管理员的此命名空间，我们建议创建命名空间管理员角色，并通过角色绑定向这些用户授予该角色。  命名空间管理员应对命名空间具有完全权限。  稍后会提供详细信息，以便为用户提供更细致的基于角色的访问。

## <a name="create-the-bootstrapper-service"></a>创建引导程序服务

引导程序服务处理用于创建、编辑和删除自定义资源（例如数据控制器、SQL 托管实例或 PostgreSQL 超大规模服务器组）的传入请求。

运行以下命令以创建引导程序服务、引导程序服务的服务帐户，以及引导程序服务帐户的角色和角色绑定。

```console
kubectl create --namespace arc -f https://raw.githubusercontent.com/microsoft/azure_arc/master/arc_data_services/deploy/yaml/bootstrapper.yaml
```

使用以下命令验证引导程序 pod 是否正在运行。  在状态更改为之前，你可能需要多次运行该示例 `Running` 。

```console
kubectl get pod --namespace arc
```

Yaml 模板文件默认为从 Microsoft 容器注册表 (MCR) 拉取引导容器映像。  如果你的环境无权直接访问 Microsoft 容器注册表，你可以执行以下操作：
- 按照以下步骤 [从 Microsoft 容器注册表拉取容器映像并将其推送到专用容器注册表](offline-deployment.md)。
- 为专用容器注册表[创建映像请求机密](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-lin)。
- 将映像请求机密添加到引导程序容器。 请参阅以下示例。
- 更改引导程序映像的映像位置。 请参阅以下示例。

下面的示例假设你创建了一个图像请求机密名称， `regcred` 如 Kubernetes 文档中所示。

```yaml
#just showing only the relevant part of the bootstrapper.yaml template file here
containers:
      - env:
        - name: ACCEPT_EULA
          value: "Y"
        #image: mcr.microsoft.com/arcdata/arc-bootstrapper:public-preview-sep-2020 <-- template value to change
        image: <your registry DNS name or IP address>/<your repo>/arc-bootstrapper:<your tag>
        imagePullPolicy: IfNotPresent
        name: bootstrapper
        resources: {}
        securityContext:
          runAsUser: 21006
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      imagePullSecrets:
      - name: regcred
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      serviceAccount: sa-mssql-controller
      serviceAccountName: sa-mssql-controller
      terminationGracePeriodSeconds: 30

```

## <a name="create-a-secret-for-the-data-controller-administrator"></a>为数据控制器管理员创建机密

数据控制器管理员用户名和密码用于向数据控制器 API 进行身份验证，以执行管理功能。  选择安全密码，只与需要具有群集管理员权限的密码共享。

Kubernetes 机密存储为 base64 编码字符串-一个用于用户名，另一个用于密码。

可以使用联机工具对所需的用户名和密码进行 base64 编码，也可以根据平台使用内置 CLI 工具。

PowerShell

```console
[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('<your string to encode here>'))

#Example
#[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('example'))

```

Linux/macOS

```console
echo '<your string to encode here>' | base64

#Example
# echo 'example' | base64
```

对用户名和密码进行编码后，您可以基于 [模板文件](https://raw.githubusercontent.com/microsoft/azure_arc/master/arc_data_services/deploy/yaml/controller-login-secret.yaml) 创建一个文件，并将用户名和密码值替换为您自己的值。

然后运行以下命令以创建机密。

```console
kubectl create --namespace arc -f <path to your data controller secret file>

#Example
kubectl create --namespace arc -f C:\arc-data-services\controller-login-secret.yaml
```

## <a name="create-the-data-controller"></a>创建数据控制器

现在，你已准备好创建数据控制器本身。

首先，在计算机上本地创建 [模板文件](https://raw.githubusercontent.com/microsoft/azure_arc/master/arc_data_services/deploy/yaml/data-controller.yaml) 的副本，以便可以修改某些设置。

根据需要编辑以下内容：

**必填**
- **位置**：将此位置更改为要在其中存储数据控制器的 _元数据_ 的 Azure 位置。  可以在 [创建数据控制器概述](create-data-controller.md) 一文中查看可用 Azure 位置的列表。
- **resourceGroup**：要在 azure 中创建数据控制器 azure 资源的 azure 资源组资源管理器。  通常，此资源组应该已经存在，但在将数据上传到 Azure 之前，不需要此资源组。
- **订阅**：要在其中创建 azure 资源的订阅的 AZURE 订阅 GUID。

**建议查看并可能更改默认值**
- **存储空间。className**：用于数据控制器数据和日志文件的存储类。  如果你不确定 Kubernetes 群集中可用的存储类，可以运行以下命令： `kubectl get storageclass` 。  默认值为， `default` 它假定存在一个存在且命名为 `default` 非默认存储类 _的_ 存储类。  注意：需要将两个 className 设置设置为所需的存储类-一个用于数据，一个用于日志。
- **serviceType**： `NodePort` 如果使用的不是 LoadBalancer，请将服务类型更改为。  注意：需要更改两个 serviceType 设置。

**可有可无**
- **名称**：数据控制器的默认名称为 `arc` ，但你可以根据需要对其进行更改。
- **displayName**：将此值设置为与文件顶部的 name 特性相同的值。
- **注册表**： Microsoft 容器注册表是默认值。  如果要从 Microsoft 容器注册表拉取映像并将 [其推送到专用容器注册表](offline-deployment.md)，请在此处输入注册表的 IP 地址或 DNS 名称。
- **dockerRegistry**：如有必要，要用于从专用容器注册表中提取映像的映像请求密码。
- **存储库**： Microsoft 容器注册表中的默认存储库是 `arcdata` 。  如果使用的是专用容器注册表，请输入文件夹/存储库的路径，该文件夹/存储库包含已启用 Azure Arr 的数据服务容器映像。
- **imageTag**：当前的最新版本标记在模板中是默认的，但是如果要使用较旧的版本，可以对其进行更改。

已完成数据控制器 yaml 文件的示例：
```yaml
apiVersion: arcdata.microsoft.com/v1alpha1
kind: datacontroller
metadata:
  generation: 1
  name: arc
spec:
  credentials:
    controllerAdmin: controller-login-secret
    #dockerRegistry: mssql-private-registry - optional if you are using a private container registry that requires authentication using an image pull secret
    serviceAccount: sa-mssql-controller
  docker:
    imagePullPolicy: Always
    imageTag: public-preview-sep-2020
    registry: mcr.microsoft.com
    repository: arcdata
  security:
    allowDumps: true
    allowNodeMetricsCollection: true
    allowPodMetricsCollection: true
    allowRunAsRoot: false
  services:
  - name: controller
    port: 30080
    serviceType: LoadBalancer
  - name: serviceProxy
    port: 30777
    serviceType: LoadBalancer
  settings:
    ElasticSearch:
      vm.max_map_count: "-1"
    azure:
      connectionMode: Indirect
      location: eastus
      resourceGroup: myresourcegroup
      subscription: c82c901a-129a-435d-86e4-cc6b294590ae
    controller:
      displayName: arc
      enableBilling: "True"
      logs.rotation.days: "7"
      logs.rotation.size: "5000"
  storage:
    data:
      accessMode: ReadWriteOnce
      className: default
      size: 15Gi
    logs:
      accessMode: ReadWriteOnce
      className: default
      size: 10Gi
```

将编辑的文件保存在本地计算机上，并运行以下命令以创建数据控制器：

```console
kubectl create --namespace arc -f <path to your data controller file>

#Example
kubectl create --namespace arc -f C:\arc-data-services\data-controller.yaml
```

## <a name="monitoring-the-creation-status"></a>监视创建状态

创建控制器需要几分钟才能完成。 可以通过以下命令在另一个终端窗口中监视进度：

> [!NOTE]
>  下面的示例命令假设你创建了一个具有名称的数据控制器和 Kubernetes 命名空间 `arc` 。  如果使用了不同的命名空间/数据控制器名称，则可以将替换 `arc` 为您的名称。

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

还可以通过运行如下命令来检查任何特定 pod 的创建状态。  这对于解决任何问题特别有用。

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

适用于 Azure Data Studio 的 azure Arc 扩展提供了一个笔记本，指导你完成如何设置启用了 Azure Arc 的 Kubernetes，并将其配置为监视包含示例 SQL 托管实例 yaml 文件的 git 存储库。 一切连接后，新的 SQL 托管实例将部署到 Kubernetes 群集。

请参阅 Azure Data Studio 的 Azure Arc 扩展中的 **使用启用了 Azure arc 的 Kubernetes 和 Flux 笔记本部署 SQL 托管实例** 。

## <a name="troubleshooting-creation-problems"></a>创建问题疑难解答

如果在创建 troubles 的过程中遇到任何问题，请参阅 [故障排除指南](troubleshoot-guide.md)。

## <a name="next-steps"></a>后续步骤

- [使用 Kubernetes 工具创建 SQL 托管实例](./create-sql-managed-instance-using-kubernetes-native-tools.md)
- [使用 Kubernetes 工具创建 PostgreSQL 超大规模服务器组](./create-postgresql-hyperscale-server-group-kubernetes-native-tools.md)
