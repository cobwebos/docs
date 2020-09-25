---
title: 使用 Kubernetes 工具创建 SQL 托管实例
description: 使用 Kubernetes 工具创建 SQL 托管实例
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 80aa323dfb08ec1e8156099bd5d04e790732b3b0
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91341525"
---
# <a name="create-azure-sql-managed-instance-using-kubernetes-tools"></a>使用 Kubernetes 工具创建 Azure SQL 托管实例

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>先决条件

应该已经创建了一个 [Azure Arc 数据控制器](./create-data-controller.md)。

若要使用 Kubernetes 工具创建 SQL 托管实例，需要安装 Kubernetes 工具。  本文中的示例将使用 `kubectl` ，但类似的方法可与其他 Kubernetes 工具（如 Kubernetes 仪表板）或使用 `oc` `helm` 这些工具和 Kubernetes yaml/json 一起使用。

[安装 kubectl 工具](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

## <a name="overview"></a>概述

若要创建 SQL 托管实例，需要创建 Kubernetes 机密，以便安全地存储系统管理员登录名和密码，以及基于 sqlmanagedinstance 自定义资源定义的 SQL 托管实例自定义资源。

## <a name="create-a-yaml-file"></a>创建 yaml 文件

您可以使用 [模板 yaml](https://raw.githubusercontent.com/microsoft/azure_arc/master/arc_data_services/deploy/yaml/sqlmi.yaml) 文件作为起点来创建您自己的自定义 SQL 托管实例 yaml 文件。  将此文件下载到本地计算机，并在文本编辑器中将其打开。  使用文本编辑器（如支持语法突出显示的 [VS Code](https://code.visualstudio.com/download) ）和 yaml 文件的 linting 很有用。

下面是一个示例 yaml 文件：

```yaml
apiVersion: v1
data:
  password: <your base64 encoded password>
  username: <your base64 encoded user name. 'sa' is not allowed>
kind: Secret
metadata:
  name: example-login-secret
type: Opaque
---
apiVersion: sql.arcdata.microsoft.com/v1alpha1
kind: sqlmanagedinstance
metadata:
  name: example
spec:
  limits:
    memory: 4Gi
    vcores: "4"
  requests:
    memory: 2Gi
    vcores: "1"
  service:
    type: LoadBalancer
  storage:
    backups:
      className: default
      size: 5Gi
    data:
      className: default
      size: 5Gi
    datalogs:
      className: default
      size: 5Gi
    logs:
      className: default
      size: 1Gi
```

### <a name="customizing-the-login-and-password"></a>自定义登录名和密码

Kubernetes 机密存储为 base64 编码字符串-一个用于用户名，另一个用于密码。  你需要对系统管理员登录名和密码进行 base64 编码，并将其放在和的占位符位置 `data.password` `data.username` 。  不要包含 `<` `>` 模板中提供的和符号。

> [!NOTE]
> 为了获得最佳安全性，登录名不允许使用值 "sa"。
> 遵循 [密码复杂性策略](/sql/relational-databases/security/password-policy#password-complexity)。

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

### <a name="customizing-the-name"></a>自定义名称

对于 name 属性，模板的值为 "example"。  您可以更改它，但它必须是遵循 DNS 命名标准的字符。  还必须更改要匹配的机密名称。  例如，如果将 SQL 托管实例的名称更改为 "sql1"，则必须将机密名称从 "" 更改为 "sql1"。

### <a name="customizing-the-resource-requirements"></a>自定义资源要求

你可以根据需要更改资源需求-RAM 和核心限制和请求。  

> [!NOTE]
> 可以详细了解 [Kubernetes 资源调控](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-units-in-kubernetes)。

资源限制和请求的要求：
- 要进行计费， **需要** 内核限制值。
- 资源请求和限制的其余部分是可选的。
- 内核限制和请求必须是正整数值（如果已指定）。
- 如果指定，核心请求需要至少2个核心。
- 内存值格式遵循 Kubernetes 表示法。  
- 如果指定内存请求，则至少需要2Gi。
- 一般原则是，对于生产用例，每1个核心应有4GB 的 RAM。

### <a name="customizing-service-type"></a>自定义服务类型

如果需要，可以将服务类型更改为 NodePort。  将分配一个随机端口号。

### <a name="customizing-storage"></a>自定义存储

你可以自定义存储类，以便与你的环境匹配。  如果你不确定哪些存储类可用，可以运行命令 `kubectl get storageclass` 来查看它们。  模板的默认值为 "default"。  这意味着，存在一个 _名为_ "default" 的存储类，但存储 _类是默认_ 值。  还可以选择更改存储大小。  有关 [存储配置](./storage-configuration.md)的详细信息，请参阅。

## <a name="creating-the-sql-managed-instance"></a>创建 SQL 托管实例

自定义 SQL 托管实例 yaml 文件后，可以通过运行以下命令来创建 SQL 托管实例：

```console
kubectl create -n <your target namespace> -f <path to your yaml file>

#Example
#kubectl create -n arc -f C:\arc-data-services\sqlmi.yaml
```


## <a name="monitoring-the-creation-status"></a>监视创建状态

创建 SQL 托管实例需要几分钟才能完成。 可以通过以下命令在另一个终端窗口中监视进度：

> [!NOTE]
>  下面的示例命令假设您创建了名为 "sql1" 的 SQL 托管实例和名为 "arc" 的 Kubernetes 命名空间。  如果使用了不同的命名空间/SQL 托管实例名称，则可以将 "arc" 和 "sqlmi" 替换为你的名称。

```console
kubectl get sqlmi/sql1 --namespace arc
```

```console
kubectl get pods --namespace arc
```

还可以通过运行如下命令来检查任何特定 pod 的创建状态。  这对于解决任何问题特别有用。

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/sql1-0 --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>创建问题疑难解答

如果在创建 troubles 的过程中遇到任何问题，请参阅 [故障排除指南](troubleshoot-guide.md)。

## <a name="next-steps"></a>后续步骤

[连接到启用了 Azure Arc 的 SQL 托管实例](connect-managed-instance.md)
