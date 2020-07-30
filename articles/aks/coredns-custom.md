---
title: 自定义 Azure Kubernetes 服务 (AKS) 的 CoreDNS
description: 了解如何使用 Azure Kubernetes 服务 (AKS) 自定义 CoreDNS，以便添加子域或扩展自定义 DNS 终结点
services: container-service
author: jnoller
ms.topic: article
ms.date: 03/15/2019
ms.author: jenoller
ms.openlocfilehash: f58232eac6727f10fdccb32e7795bf12a93b7cbb
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87405511"
---
# <a name="customize-coredns-with-azure-kubernetes-service"></a>使用 Azure Kubernetes 服务自定义 CoreDNS

Azure Kubernetes 服务 (AKS) 可将适用于管理和解决群集 DNS 问题的 [CoreDNS][coredns] 项目与所有 *1.12.x* 及更高版本的群集配合使用。 以前使用 kube-dns 项目。 该 kube-dns 项目现已弃用。 有关 CoreDNS 自定义和 Kubernetes 的详细信息，请参阅[官方的上游文档][corednsk8s]。

由于 AKS 是托管的服务，因此不能修改 CoreDNS 的主要配置 (*CoreFile*)。 可以改用 Kubernetes *ConfigMap* 来替代默认设置。 若要查看默认的 AKS CoreDNS ConfigMap，请使用 `kubectl get configmaps --namespace=kube-system coredns -o yaml` 命令。

本文介绍如何在 AKS 中将 ConfigMap 用于 CoreDNS 的基本自定义选项。 此方法不同于在其他上下文中配置 CoreDNS，例如使用 CoreFile。 验证正在运行的 CoreDNS 的版本，因为配置值可能会在不同版本之间发生变化。

> [!NOTE]
> `kube-dns` 通过 Kubernetes 配置映射提供不同的[自定义选项][kubednsblog]。 CoreDNS **不**后向兼容 kube-dns。 以前使用的任何自定义项都必须在更新后才能与 CoreDNS 配合使用。

## <a name="before-you-begin"></a>开始之前

本文假定你拥有现有的 AKS 群集。 如果需要 AKS 群集，请参阅 AKS 快速入门[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 门户][aks-quickstart-portal]。

创建类似于以下示例的配置时，*数据*节中的名称必须以 *. server*或 *. override*结束。 此命名约定在默认 AKS CoreDNS Configmap 中定义，可以使用 `kubectl get configmaps --namespace=kube-system coredns -o yaml` 命令查看。

## <a name="what-is-supportedunsupported"></a>支持的/不支持的插件

支持所有内置 CoreDNS 插件。 不支持任何附加/第三方插件。

## <a name="rewrite-dns"></a>重写 DNS

一个可以使用的方案是执行 DNS 名称即时重写。 在以下示例中，请将 `<domain to be written>` 替换为你自己的完全限定域名。 创建名为 `corednsms.yaml` 的文件并粘贴以下示例配置：

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    <domain to be rewritten>.com:53 {
        errors
        cache 30
        rewrite name substring <domain to be rewritten>.com default.svc.cluster.local
        forward .  /etc/resolv.conf # you can redirect this to a specific DNS server such as 10.0.0.10, but that server must be able to resolve the rewritten domain name
    }
```

使用 [kubectl apply configmap][kubectl-apply] 命令创建 ConfigMap，并指定 YAML 清单的名称：

```console
kubectl apply -f corednsms.yaml
```

若要验证自定义项是否已得到应用，请使用 [kubectl get configmaps][kubectl-get] 命令并指定 *coredns-custom* ConfigMap：

```
kubectl get configmaps --namespace=kube-system coredns-custom -o yaml
```

现在强制 CoreDNS 重新加载 ConfigMap。 [kubectl delete pod][kubectl delete] 命令不是破坏性的，不会导致停机。 `kube-dns` Pod 会被删除，然后 Kubernetes 计划程序会重新创建它们。 这些新的 Pod 包含 TTL 值中的更改。

```console
kubectl delete pod --namespace kube-system -l k8s-app=kube-dns
```

> [!Note]
> 以上命令是正确的。 当我们更改 `coredns` 时，部署采用 **kube-dns** 名称。

## <a name="custom-forward-server"></a>自定义转发服务器

如果需要为网络流量指定转发服务器，可以创建一个 ConfigMap 来自定义 DNS。 在以下示例中，请将 `forward` 名称和地址更新为你自己的环境的值。 创建名为 `corednsms.yaml` 的文件并粘贴以下示例配置：

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    <domain to be rewritten>.com:53 {
        forward foo.com 1.1.1.1
    }
```

与前面的示例一样，使用 [kubectl apply configmap][kubectl-apply] 命令创建 ConfigMap，并指定 YAML 清单的名称。 然后，使用 [kubectl delete pod][kubectl delete] 强制 CoreDNS 重新加载 ConfigMap，以便 Kubernetes 计划程序重新创建它们：

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="use-custom-domains"></a>使用自定义域

可能需要配置只能在内部进行解析的自定义域。 例如，可能需要解析自定义域 *puglife.local*，该域不是有效的顶级域。 在没有自定义域 ConfigMap 的情况下，AKS 群集无法解析该地址。

在以下示例中，请将用于定向流量的自定义域和 IP 地址更新为你自己的环境的值。 创建名为 `corednsms.yaml` 的文件并粘贴以下示例配置：

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  puglife.server: | # you may select any name here, but it must end with the .server file extension
    puglife.local:53 {
        errors
        cache 30
        forward . 192.11.0.1  # this is my test/dev DNS server
    }
```

与前面的示例一样，使用 [kubectl apply configmap][kubectl-apply] 命令创建 ConfigMap，并指定 YAML 清单的名称。 然后，使用 [kubectl delete pod][kubectl delete] 强制 CoreDNS 重新加载 ConfigMap，以便 Kubernetes 计划程序重新创建它们：

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="stub-domains"></a>存根域

CoreDNS 也可用于配置存根域。 在以下示例中，请将自定义域和 IP 地址更新为你自己的环境的值。 创建名为 `corednsms.yaml` 的文件并粘贴以下示例配置：

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    abc.com:53 {
        errors
        cache 30
        forward . 1.2.3.4
    }
    my.cluster.local:53 {
        errors
        cache 30
        forward . 2.3.4.5
    }

```

与前面的示例一样，使用 [kubectl apply configmap][kubectl-apply] 命令创建 ConfigMap，并指定 YAML 清单的名称。 然后，使用 [kubectl delete pod][kubectl delete] 强制 CoreDNS 重新加载 ConfigMap，以便 Kubernetes 计划程序重新创建它们：

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="hosts-plugin"></a>Hosts 插件

由于所有内置插件都受支持，这意味着 CoreDNS [Hosts][coredns hosts] 插件也可用于自定义：

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom # this is the name of the configmap you can overwrite with your changes
  namespace: kube-system
data:
    test.override: | # you may select any name here, but it must end with the .override file extension
          hosts example.hosts example.org { # example.hosts must be a file
              10.0.0.1 example.org
              fallthrough
          }
```

## <a name="enable-logging-for-dns-query-debugging"></a>为 DNS 查询调试启用日志记录 

若要启用 DNS 查询日志记录，请在 coredns-custom ConfigMap 中应用以下配置：

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  log.override: | # you may select any name here, but it must end with the .override file extension
        log
```

## <a name="next-steps"></a>后续步骤

本文介绍了一些适用于 CoreDNS 自定义的示例方案。 有关 CoreDNS 项目的信息，请参阅 [CoreDNS 上游项目页][coredns]。

若要详细了解核心网络概念，请参阅 [AKS 中应用程序的网络概念][concepts-network]。

<!-- LINKS - external -->
[kubednsblog]: https://www.danielstechblog.io/using-custom-dns-server-for-domain-specific-name-resolution-with-azure-kubernetes-service/
[coredns]: https://coredns.io/
[corednsk8s]: https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/#coredns
[dnscache]: https://coredns.io/plugins/cache/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[coredns hosts]: https://coredns.io/plugins/hosts/

<!-- LINKS - internal -->
[concepts-network]: concepts-network.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
