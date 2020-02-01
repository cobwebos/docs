---
title: 在容器实例上设置活动探测
description: 了解如何配置活动探测以重新启动 Azure 容器实例中的不正常容器
ms.topic: article
ms.date: 06/08/2018
ms.openlocfilehash: 566f7952aff1cf460272fbb418a2a0efff411881
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901898"
---
# <a name="configure-liveness-probes"></a>配置活动探测

容器化应用程序可能会长时间运行，从而导致损坏的状态，这些状态可能需要通过重新启动容器进行修复。 Azure 容器实例支持活动探测，以便在关键功能不工作时，将容器组内的容器配置为重新启动。 活动探测的行为类似于[Kubernetes 活动探测](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)。

本文介绍如何部署包含活动探测的容器组，并演示模拟的不正常容器的自动重新启动。

Azure 容器实例还支持[准备情况探测](container-instances-readiness-probe.md)，你可以将其配置为确保流量仅在准备就绪时才到达容器。

> [!NOTE]
> 目前不能在部署到虚拟网络的容器组中使用活动探测。

## <a name="yaml-deployment"></a>YAML 部署

使用以下代码段创建 `liveness-probe.yaml` 文件。 此文件定义包含 NGNIX 容器的容器组，该容器最终会变得不正常。

```yaml
apiVersion: 2018-10-01
location: eastus
name: livenesstest
properties:
  containers:
  - name: mycontainer
    properties:
      image: nginx
      command:
        - "/bin/sh"
        - "-c"
        - "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      livenessProbe:
        exec:
            command:
                - "cat"
                - "/tmp/healthy"
        periodSeconds: 5
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

运行以下命令以通过上述 YAML 配置部署此容器组：

```azurecli-interactive
az container create --resource-group myResourceGroup --name livenesstest -f liveness-probe.yaml
```

### <a name="start-command"></a>开始命令

部署定义一个启动命令，该命令将在容器第一次开始运行时运行，由 `command` 属性定义，该属性接受字符串数组。 在此示例中，它将启动 bash 会话，并通过传递此命令在 `/tmp` 目录中创建名为 `healthy` 的文件：

```bash
/bin/sh -c "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
```

 然后，它将休眠30秒，然后再删除该文件，然后进入10分钟睡眠状态。

### <a name="liveness-command"></a>活动命令

此部署定义一个 `livenessProbe`，该支持作为活动检查的 `exec` 活动命令。 如果此命令以非零值退出，则将终止并重新启动容器，并通知 `healthy` 文件找不到。 如果此命令成功退出，并退出代码为0，则不会执行任何操作。

`periodSeconds` 属性指定活动命令每5秒执行一次。

## <a name="verify-liveness-output"></a>验证活动输出

在前30秒内，"启动" 命令创建的 `healthy` 文件存在。 当活动命令检查是否存在 `healthy` 文件时，状态代码返回零，表示成功，因此不会重新启动。

30秒后，`cat /tmp/healthy` 将开始失败，从而导致不正常的事件和中止的事件发生。

可以从 Azure 门户或 Azure CLI 查看这些事件。

![门户不正常事件][portal-unhealthy]

通过查看 Azure 门户中的事件，将在活动命令失败的情况下触发 `Unhealthy` 类型的事件。 后续事件的类型为 `Killing`，表示删除容器，因此可以开始重新启动。 每次发生此事件时，容器的重启计数都会增加。

重新启动已完成，因此将保留公共 IP 地址和特定于节点的内容等资源。

![门户重启计数器][portal-restart]

如果活动探查连续失败并触发太多重新启动，则容器将进入指数后关闭延迟。

## <a name="liveness-probes-and-restart-policies"></a>活动探测和重新启动策略

重新启动策略取代了活动探测触发的重启行为。 例如，如果您设置了一个 `restartPolicy = Never`*和*一个活动探测，则由于活动检查失败，容器组将不会重新启动。 容器组将改为遵循容器组的 "重新启动" 策略 `Never`。

## <a name="next-steps"></a>后续步骤

基于任务的方案可能需要一个活动探测，以便在先决条件函数不能正常工作时启用自动重新启动。 有关运行基于任务的容器的详细信息，请参阅[在 Azure 容器实例中运行容器化任务](container-instances-restart-policy.md)。

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-liveness-probe/unhealthy-killing.png
[portal-restart]: ./media/container-instances-liveness-probe/portal-restart.png
