---
title: 为 Azure Kubernetes 服务（AKS）自定义 CoreDNS
description: 了解如何自定义 CoreDNS，以使用 Azure Kubernetes 服务（AKS）添加子域或扩展自定义 DNS 终结点
services: container-service
author: jnoller
ms.topic: article
ms.date: 03/15/2019
ms.author: jenoller
ms.openlocfilehash: 78132a53313f4a8ee5c10af340c8dab08c3e42c2
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77595818"
---
# <a name="customize-coredns-with-azure-kubernetes-service"></a>通过 Azure Kubernetes 服务自定义 CoreDNS

Azure Kubernetes Service （AKS）使用[CoreDNS][coredns]项目通过所有*1.12. x*和更高版本的群集进行群集 DNS 管理和解析。 以前，使用了 kube-dns 项目。 此 kube-dns 项目现已弃用。 有关 CoreDNS 自定义和 Kubernetes 的详细信息，请参阅[官方上游文档][corednsk8s]。

由于 AKS 是托管的服务，因此无法修改 CoreDNS （ *CoreFile*）的主要配置。 相反，可以使用 Kubernetes *ConfigMap*替代默认设置。 若要查看默认 AKS CoreDNS ConfigMaps，请使用 `kubectl get configmaps --namespace=kube-system coredns -o yaml` 命令。

本文说明如何使用 ConfigMaps 在 AKS 中使用 CoreDNS 的基本自定义选项。 此方法不同于在其他上下文（如使用 CoreFile）中配置 CoreDNS。 验证正在运行的 CoreDNS 的版本，因为配置值可能会在版本之间发生更改。

> [!NOTE]
> `kube-dns` 通过 Kubernetes 配置映射提供不同的[自定义选项][kubednsblog]。 CoreDNS**不**与 kube 向后兼容。 你以前使用的任何自定义都必须更新为可与 CoreDNS 配合使用。

## <a name="before-you-begin"></a>开始之前

本文假定你拥有现有的 AKS 群集。 如果需要 AKS 群集，请参阅 AKS 快速入门，并[使用 Azure CLI][aks-quickstart-cli]或[使用 Azure 门户][aks-quickstart-portal]。

## <a name="what-is-supportedunsupported"></a>支持/不支持的功能

支持所有内置 CoreDNS 插件。 不支持外接程序/第三方插件。

## <a name="rewrite-dns"></a>重写 DNS

一种方案是执行动态 DNS 名称重写。 在下面的示例中，将 `<domain to be written>` 替换为你自己的完全限定的域名。 创建名为 `corednsms.yaml` 的文件并粘贴以下示例配置：

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: |
    <domain to be rewritten>.com:53 {
        errors
        cache 30
        rewrite name substring <domain to be rewritten>.com default.svc.cluster.local
        forward .  /etc/resolv.conf # you can redirect this to a specific DNS server such as 10.0.0.10
    }
```

使用[kubectl apply ConfigMap][kubectl-apply]命令创建 ConfigMap，并指定 YAML 清单的名称：

```console
kubectl apply -f corednsms.yaml
```

若要验证是否已应用自定义项，请使用[kubectl get configmaps][kubectl-get]并指定*coredns* ConfigMap：

```
kubectl get configmaps --namespace=kube-system coredns-custom -o yaml
```

现在强制 CoreDNS 重新加载 ConfigMap。 [Kubectl delete pod][kubectl delete]命令没有破坏性，不会导致停机。 将删除 `kube-dns` pod，然后 Kubernetes 计划程序将重新创建它们。 这些新 pod 包含 TTL 值的更改。

```console
kubectl delete pod --namespace kube-system -l k8s-app=kube-dns
```

> [!Note]
> 上面的命令是正确的。 更改 `coredns`时，部署位于**kube**的名称下。

## <a name="custom-forward-server"></a>自定义转发服务器

如果需要为网络流量指定转发服务器，可以创建一个 ConfigMap 来自定义 DNS。 在下面的示例中，用您自己的环境的值更新 `forward` 名称和地址。 创建名为 `corednsms.yaml` 的文件并粘贴以下示例配置：

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

如前面的示例所示，使用[kubectl apply ConfigMap][kubectl-apply]命令创建 ConfigMap，并指定 YAML 清单的名称。 然后，使用 Kubernetes 计划程序的[kubectl delete pod][kubectl delete]强制 CoreDNS 重新加载 ConfigMap，以重新创建它们：

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="use-custom-domains"></a>使用自定义域

你可能想要配置只能在内部解析的自定义域。 例如，你可能需要解析自定义域*puglife*，它不是有效的顶级域。 如果没有自定义域 ConfigMap，AKS 群集将无法解析该地址。

在下面的示例中，更新自定义域和 IP 地址，以将流量定向到你自己的环境的值。 创建名为 `corednsms.yaml` 的文件并粘贴以下示例配置：

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  puglife.server: |
    puglife.local:53 {
        errors
        cache 30
        forward . 192.11.0.1  # this is my test/dev DNS server
    }
```

如前面的示例所示，使用[kubectl apply ConfigMap][kubectl-apply]命令创建 ConfigMap，并指定 YAML 清单的名称。 然后，使用 Kubernetes 计划程序的[kubectl delete pod][kubectl delete]强制 CoreDNS 重新加载 ConfigMap，以重新创建它们：

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="stub-domains"></a>存根域

CoreDNS 也可用于配置存根域。 在下面的示例中，用您自己的环境的值更新自定义域和 IP 地址。 创建名为 `corednsms.yaml` 的文件并粘贴以下示例配置：

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: |
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

如前面的示例所示，使用[kubectl apply ConfigMap][kubectl-apply]命令创建 ConfigMap，并指定 YAML 清单的名称。 然后，使用 Kubernetes 计划程序的[kubectl delete pod][kubectl delete]强制 CoreDNS 重新加载 ConfigMap，以重新创建它们：

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="hosts-plugin"></a>主机插件

由于支持所有内置插件，因此也可以自定义 CoreDNS[主机][coredns hosts]插件：

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom # this is the name of the configmap you can overwrite with your changes
  namespace: kube-system
data:
    test.override: |
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
  log.override: |
        log
```

## <a name="next-steps"></a>后续步骤

本文介绍 CoreDNS 自定义的一些示例方案。 有关 CoreDNS 项目的信息，请参阅[CoreDNS 上游项目页][coredns]。

若要详细了解核心网络概念，请参阅[AKS 中应用程序的网络概念][concepts-network]。

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
