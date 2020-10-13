---
title: 在容器实例上设置就绪情况探测
description: 了解如何配置探测，以确保 Azure 容器实例中的容器仅在准备就绪时才接收请求
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: 3e89086d66f284df35e36dc8f1d68bb09264843f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86169657"
---
# <a name="configure-readiness-probes"></a>配置就绪情况探测

对于为流量提供服务的容器化应用程序，你可能想要验证容器是否已准备好处理传入的请求。 Azure 容器实例支持采用就绪情况探测来包括各种配置，使容器在某些情况下无法访问。 就绪情况探测的行为类似于 [Kubernetes 就绪情况探测](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)。 例如，容器应用可能需要在启动过程中加载大型数据集，并且你不希望在这段时间内接收请求。

本文介绍了如何部署包含就绪情况探测的容器组，使容器仅在探测成功时才接收流量。

Azure 容器实例还支持[运行情况探测](container-instances-liveness-probe.md)，你可以配置该探测来使不正常的容器自动重启。

> [!NOTE]
> 目前不能在部署到虚拟网络的容器组中使用准备情况探测。

## <a name="yaml-configuration"></a>YAML 配置

例如，使用以下代码片段创建包含就绪情况探测的 `readiness-probe.yaml` 文件。 此文件定义一个容器组，其中包含运行小型 Web 应用的容器。 此应用是通过公共 `mcr.microsoft.com/azuredocs/aci-helloworld` 映像部署的。 此容器化应用还演示了如何使用 Azure CLI 和其他快速入门在 [Azure 中部署容器实例](container-instances-quickstart.md) 。

```yaml
apiVersion: 2019-12-01
location: eastus
name: readinesstest
properties:
  containers:
  - name: mycontainer
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      command:
        - "/bin/sh"
        - "-c"
        - "node /usr/src/app/index.js & (sleep 240; touch /tmp/ready); wait"
      ports:
      - port: 80
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      readinessProbe:
        exec:
          command:
          - "cat"
          - "/tmp/ready"
        periodSeconds: 5
  osType: Linux
  restartPolicy: Always
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

### <a name="start-command"></a>“启动”命令

部署包含一个 `command` 属性，该属性定义在容器首次开始运行时运行的启动命令。 此属性接受字符串数组。 此命令模拟当 Web 应用运行但容器未准备就绪时的时间。 

首先，它启动一个 shell 会话并运行 `node` 命令来启动 Web 应用。 它还启动一个命令来休眠 240 秒，经过此时间后，它将在 `/tmp` 目录中创建一个名为 `ready` 的文件：

```console
node /usr/src/app/index.js & (sleep 240; touch /tmp/ready); wait
```

### <a name="readiness-command"></a>就绪情况命令

此 YAML 文件定义了一个 `readinessProbe`，它支持执行就绪情况检查的 `exec` 就绪情况命令。 此示例就绪情况命令测试 `/tmp` 目录中是否存在 `ready` 文件。

如果 `ready` 文件不存在，则就绪情况命令将以非零值退出；容器将继续运行，但无法访问。 当命令成功退出且退出代码为 0 时，容器就绪可供访问。 

`periodSeconds` 属性指定就绪情况命令应当每 5 秒执行一次。 就绪情况探测在容器组的生存期内运行。

## <a name="example-deployment"></a>示例部署

运行以下命令来使用前面的 YAML 配置部署一个容器组：

```azurecli-interactive
az container create --resource-group myResourceGroup --file readiness-probe.yaml
```

## <a name="view-readiness-checks"></a>查看就绪情况检查

在此示例中，在第一个 240 秒内，就绪情况命令在检查 `ready` 文件是否存在时失败。 状态代码返回了表明容器未就绪的信号。

可以通过 Azure 门户或 Azure CLI 查看这些事件。 例如，门户显示当就绪情况命令失败时触发了 `Unhealthy` 类型的事件。 

![门户不正常事件][portal-unhealthy]

## <a name="verify-container-readiness"></a>验证容器就绪情况

启动容器后，可以验证它最初是否无法访问。 预配后，获取容器组的 IP 地址：

```azurecli
az container show --resource-group myResourceGroup --name readinesstest --query "ipAddress.ip" --out tsv
```

在就绪情况探测失败时尝试访问该站点：

```bash
wget <ipAddress>
```

输出表明该站点最初无法访问：
```
$ wget 192.0.2.1
--2019-10-15 16:46:02--  http://192.0.2.1/
Connecting to 192.0.2.1... connected.
HTTP request sent, awaiting response... 
```

240 秒后，就绪情况命令成功，表明容器已准备就绪。 现在，运行 `wget` 命令时，该命令将成功：

```
$ wget 192.0.2.1
--2019-10-15 16:46:02--  http://192.0.2.1/
Connecting to 192.0.2.1... connected.
HTTP request sent, awaiting response...200 OK
Length: 1663 (1.6K) [text/html]
Saving to: ‘index.html.1’

index.html.1                       100%[===============================================================>]   1.62K  --.-KB/s    in 0s      

2019-10-15 16:49:38 (113 MB/s) - ‘index.html.1’ saved [1663/1663] 
```

在容器准备就绪后，还可以通过使用 Web 浏览器浏览到 IP 地址来访问 Web 应用。

> [!NOTE]
> 就绪情况探测在容器组的生存期内会继续运行。 如果就绪情况命令稍后失败，则容器将再次变得无法访问。 
> 

## <a name="next-steps"></a>后续步骤

当涉及包含依赖容器的多容器组时，就绪情况探测可能很有用。 有关多容器方案的详细信息，请参阅 [Azure 容器实例中的容器组](container-instances-container-groups.md)。

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-readiness-probe/readiness-probe-failed.png
