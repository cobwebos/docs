---
title: 针对 Kubernetes 工作负荷的工作负荷保护
description: 了解如何使用 Azure 安全中心的一组 Kubernetes 工作负荷保护安全建议
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 09/12/2020
ms.author: memildin
ms.openlocfilehash: 332c33ce46298e5338587cd4d001466d30c1a9e6
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90933370"
---
# <a name="protect-your-kubernetes-workloads"></a>保护 Kubernetes 工作负载

本页介绍如何使用 Azure 安全中心的一组安全建议，这些建议专用于 Kubernetes 工作负荷保护。

在工作负荷保护中了解有关这些功能的详细信息 [使用 Kubernetes 许可控制的最佳做法](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)

如果启用 Azure Defender，安全中心会提供更多的容器安全功能。 尤其是在下列情况下：

- 针对[容器注册表的 Azure Defender](defender-for-container-registries-introduction.md) ，扫描容器注册表中的漏洞
- 获取 K8s 群集的实时威胁检测警报 [Azure Defender For Kubernetes](defender-for-kubernetes-introduction.md)

> [!TIP]
> 有关可能会为 Kubernetes 群集和节点显示的 *所有* 安全建议的列表，请参阅 "建议参考" 表的 " [容器" 部分](recommendations-reference.md#recs-containers) 。



## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|预览|
|计价|免费|
|必需的角色和权限：|用于编辑分配的**所有者**或**安全管理员**<br>查看建议的**读者**|
|支持的群集|需要 Kubernetes v 1.14 (或更高版本) <br>群集上没有 PodSecurityPolicy 资源 (旧的 PSP 模型) <br>不支持 Windows 节点|
|云：|![是](./media/icons/yes-icon.png) 商业云<br>![否](./media/icons/no-icon.png) 国家/主权 (US Gov、中国 Gov、其他 Gov) |
|||


## <a name="set-up-your-workload-protection"></a>设置工作负荷保护

Azure 安全中心包含一系列建议，这些建议可在安装 **适用于 Kubernetes 的 Azure 策略外接程序**时获得。

1. 若要配置建议，首先必须安装 "添加到"：

    1. 在 "建议" 页上，搜索 **应该在群集上安装和启用的、用于 Kubernetes 的名为 Azure Policy 外接程序**的建议。

        :::image type="content" source="./media/defender-for-kubernetes-usage/recommendation-to-install-policy-add-on-for-kubernetes.png" alt-text="建议 * * 应在群集上安装和启用适用于 Kubernetes 的 Azure 策略外接程序 * *":::

        > [!TIP]
        > 建议包括在五个不同的安全控件中，在下一步中选择哪一种都不重要。

    1. 从任何安全控件中，选择 "建议" 以查看可在其上安装 "添加" 的资源，并选择 " **更正**"。 

        :::image type="content" source="./media/defender-for-kubernetes-usage/recommendation-to-install-policy-add-on-for-kubernetes-details.png" alt-text="* * 的建议详细信息页应在群集上安装和启用适用于 Kubernetes 的 Azure 策略外接程序 * *":::

1. 外接程序安装完成后大约30分钟，安全中心会显示以下建议的群集运行状况状态，每种状态都在相关安全控件中显示，如下所示：

    > [!TIP]
    > 某些建议包含必须通过 Azure 策略自定义的参数，才能有效地使用它们。 例如，若要从建议的容器映像中获益， **只应从受信任的注册表进行部署**，你必须定义受信任的注册表。
    > 
    > 如果没有为需要配置的建议输入所需的参数，则工作负荷将显示为 "不正常"。

    | 建议名称                                                                   | 安全控制                         | 需要配置 |
    |---------------------------------------------------------------------------------------|------------------------------------------|------------------------|
    | 应 (预览中强制容器 CPU 和内存限制)                           | 保护应用程序免受 DDoS 攻击 | 否                     |
    | 应避免 (预览的特权容器)                                      | 管理访问权限和权限            | 否                     |
    |  (只读) 应为容器 () 预览的根文件系统强制执行     | 管理访问权限和权限            | 否                     |
    | 应避免 (预览中进行特权升级的容器)                        | 管理访问权限和权限            | 否                     |
    | 应避免 (预览，以根用户身份运行容器)                            | 管理访问权限和权限            | 否                     |
    | 应避免 (预览中共享敏感主机命名空间的容器)               | 管理访问和权限            | 否                     |
    | 应为容器 (预览提供最低权限的 Linux 功能)        | 管理访问权限和权限            | **是**                |
    | 应将 pod HostPath 卷装载的使用限制为已知列表 (预览)     | 管理访问和权限            | **是**                |
    | 容器只应侦听 (预览中允许的端口)                               | 限制未经授权的网络访问     | **是**                |
    | 服务应仅在 (预览中侦听允许的端口)                                 | 限制未经授权的网络访问     | **是**                |
    | 应 (预览版限制主机网络和端口的使用)                      | 限制未经授权的网络访问     | **是**                |
    | 重写或禁用容器 AppArmor 配置文件应 (预览进行限制)  | 修正安全配置        | **是**                |
    | 容器映像只应从受信任的注册表中部署 (预览)             | 修正漏洞                | **是**                |


1. 对于带有参数的建议，必须自定义参数：

    1. 从安全中心的菜单中，选择 " **安全策略**"。
    1. 选择相关订阅。
    1. 从 **安全中心的默认策略** 部分，选择 " **查看有效策略**"。
    1. 选择 "ASC 默认值"。
    1. 打开 " **参数** " 选项卡，并根据需要修改这些值。
    1. 选择“查看 + 保存”。
    1. 选择“保存”。


1. 若要强制实施任何建议，请在安全中心的安全策略**参数**选项卡中将其设置为 "**拒绝**"：

    :::image type="content" source="./media/defender-for-kubernetes-usage/enforce-workload-protection-example.png" alt-text="Azure 策略参数的拒绝选项":::

    这会拒绝 AKS 群集的任何不符合请求

1. 查看适用于群集的建议：

    1. 打开安全中心的 " [资产清单](asset-inventory.md) " 页，并使用资源类型筛选器 **Kubernetes 服务**。

    1. 选择要调查的群集，并查看可用于该群集的可用建议。 

1. 查看工作负荷保护集的建议时，会看到受影响的 pod 数 ( "Kubernetes components" ) 与群集一起列出。 若要查看特定 pod 的列表，请选择该群集，然后选择 " **采取操作**"。

    :::image type="content" source="./media/defender-for-kubernetes-usage/view-affected-pods-for-recommendation.gif" alt-text="查看受影响的 K8s 建议"::: 

1. 若要测试强制，请使用下面两个 Kubernetes 部署：

    - 一种用于正常部署，符合工作负荷保护建议的捆绑。
    - 另一种方式适用于不正常的部署，不符合 *任何* 建议。

    按原样部署 yaml 文件，或将其用作参考来更正你自己的工作负荷 (步骤 VIII)   


## <a name="healthy-deployment-example-yaml-file"></a>正常的部署示例。 yaml 文件

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-healthy-deployment
  labels:
    app: redis
spec:
  replicas: 3
  selector:
    matchLabels:
      app: redis
  template:
    metadata:
      labels:
        app: redis
      annotations:
        apparmor.security.beta.kubernetes.io/pod: runtime/default
        container.apparmor.security.beta.kubernetes.io/redis: runtime/default
    spec:
      containers:
      - name: redis
        image: healthyClusterRegistry.azurecr.io/redis:latest
        ports:
        - containerPort: 80
        resources:
          limits:
            cpu: 100m
            memory: 250Mi
        securityContext:
          privileged: false
          readOnlyRootFilesystem: true
          allowPrivilegeEscalation: false
          runAsNonRoot: true
          runAsUser: 1000
---
apiVersion: v1
kind: Service
metadata:
  name: redis-healthy-service
spec:
  type: LoadBalancer
  selector:
    app: redis
  ports:
  - port: 80
    targetPort: 80
```

## <a name="unhealthy-deployment-example-yaml-file"></a>不正常的部署示例。 yaml 文件

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-unhealthy-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:      
      labels:
        app: nginx
    spec:
      hostNetwork: true
      hostPID: true 
      hostIPC: true
      containers:
      - name: nginx
        image: nginx:1.15.2
        ports:
        - containerPort: 9001
          hostPort: 9001
        securityContext:
          privileged: true
          readOnlyRootFilesystem: false
          allowPrivilegeEscalation: true
          runAsUser: 0
          capabilities:
            add:
              - NET_ADMIN
        volumeMounts:
        - mountPath: /test-pd
          name: test-volume
          readOnly: true
      volumes:
      - name: test-volume
        hostPath:
          # directory location on host
          path: /tmp
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-unhealthy-service
spec:
  type: LoadBalancer
  selector:
    app: nginx
  ports:
  - port: 6001
    targetPort: 9001
```



## <a name="next-steps"></a>后续步骤

本文介绍了如何配置 Kubernetes 工作负荷保护。 

有关其他相关材料，请参阅以下页面： 

- [容器的安全中心建议](recommendations-reference.md#recs-containers)
- [AKS 群集级别的警报](alerts-reference.md#alerts-akscluster)
- [容器主机级别的警报](alerts-reference.md#alerts-containerhost)