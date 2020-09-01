---
title: 通过 Windows PowerShell 界面连接和管理 Microsoft Azure Stack Edge 设备 |Microsoft Docs
description: 描述如何通过 Windows PowerShell 界面连接到 Azure Stack 边缘并进行管理。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 85e95dc4138fd638c8db9f5c98a7064153c7ef17
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2020
ms.locfileid: "89181640"
---
# <a name="manage-an-azure-stack-edge-gpu-device-via-windows-powershell"></a>通过 Windows PowerShell 管理 Azure Stack Edge GPU 设备

Azure Stack Edge 解决方案使你能够处理数据，并通过网络将数据发送到 Azure。 本文介绍 Azure Stack Edge 设备的一些配置和管理任务。 你可以使用 Azure 门户、本地 web UI 或 Windows PowerShell 界面来管理你的设备。

本文重点介绍如何连接到设备的 PowerShell 接口，以及使用此接口可以执行的任务。 


## <a name="connect-to-the-powershell-interface"></a>连接到 PowerShell 接口

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>创建支持包

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>上传证书

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

你还可以上传 IoT Edge 证书，以在你的 IoT Edge 设备与可能连接到该设备的下游设备之间启用安全连接。 需要安装以下三个 IoT Edge *证书 () * 格式：

- 根 CA 证书或所有者 CA
- 设备 CA 证书
- 设备密钥证书

下面的示例演示如何使用此 cmdlet 安装 IoT Edge 证书：

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username"
```
运行此 cmdlet 时，系统将提示你提供网络共享的密码。

有关证书的详细信息，请参阅 [Azure IoT Edge 证书](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs) "或 [" 在网关上安装证书 "](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway)。

## <a name="view-device-information"></a>查看设备信息
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="view-gpu-driver-information"></a>查看 GPU 驱动程序信息

如果在设备上配置了计算角色，还可以通过 PowerShell 界面获取 GPU 驱动程序信息。 

1. [连接到 PowerShell 接口](#connect-to-the-powershell-interface)。
2. 使用 `Get-HcsGpuNvidiaSmi` 获取设备的 GPU 驱动程序信息。

    以下示例显示了此 cmdlet 的用法：

    ```powershell
    Get-HcsGpuNvidiaSmi
    ```
    记下此 cmdlet 的示例输出中的驱动程序信息。

    ```powershell    
    +-----------------------------------------------------------------------------+    
    | NVIDIA-SMI 440.64.00    Driver Version: 440.64.00    CUDA Version: 10.2     |    
    |-------------------------------+----------------------+----------------------+    
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |    
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |    
    |===============================+======================+======================|    
    |   0  Tesla T4            On   | 000029CE:00:00.0 Off |                    0 |    
    | N/A   60C    P0    29W /  70W |   1539MiB / 15109MiB |      0%      Default |    
    +-------------------------------+----------------------+----------------------+    
    |   1  Tesla T4           On  | 0000AD50:00:00.0 Off |                    0 |
    | N/A   58C    P0    29W /  70W |    330MiB / 15109MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
    ```

## <a name="enable-multi-process-service-mps"></a>启用多进程服务 (MP) 

在 Nvidia Gpu 上，多进程服务 (MPS) 提供一种机制，可通过多个作业来共享 Gpu，其中每个作业分配了一定百分比的 GPU 资源。 若要在 Azure Stack Edge 设备上启用 MP，请执行以下步骤：

1. 在开始之前，请确保： 

    1. 已使用 Azure 中的 Azure Stack Edge/Data Box Gateway 资源配置和 [激活 Azure Stack edge 设备](azure-stack-edge-gpu-deploy-activate.md) 。
    1. 已在 [Azure 门户中的此设备上配置计算](azure-stack-edge-deploy-configure-compute.md#configure-compute)。
    
1. [连接到 PowerShell 接口](#connect-to-the-powershell-interface)。
1. 使用以下命令在设备上启用 MP。

    ```powershell
    Start-HcsGpuMPS
    ```

## <a name="reset-your-device"></a>重置设备

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>获取计算日志

如果在设备上配置了计算角色，则还可以通过 PowerShell 界面获取计算日志。

1. [连接到 PowerShell 接口](#connect-to-the-powershell-interface)。
2. 使用 `Get-AzureDataBoxEdgeComputeRoleLogs` 获取设备的计算日志。

    以下示例显示了此 cmdlet 的用法：

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection    
    ```

    下面是用于 cmdlet 的参数的说明：
    - `Path`：提供要在其中创建计算日志包的共享的网络路径。
    - `Credential`：提供网络共享的用户名。 运行此 cmdlet 时，需要提供共享密码。
    - `FullLogCollection`：此参数可确保日志包将包含所有计算日志。 默认情况下，日志包仅包含一小部分的日志。



## <a name="debug-kubernetes-issues-related-to-iot-edge"></a>调试与 IoT Edge 相关的 Kubernetes 问题

创建 Kubernetes 群集时， `aseuser` 还会创建与系统命名空间相关联的默认用户 `iotedge` 。 若要调试与 IoT Edge 相关的任何问题，可以使用此用户和系统命名空间。  

### <a name="create-config-file-for-system-namespace"></a>为系统命名空间创建配置文件

若要进行故障排除，请首先创建 `config` `iotedge` 与命名空间对应的文件 `aseuser` 。

运行 `Get-HcsKubernetesUserConfig -AseUser` 命令，并将输出保存为 `config` 文件 (没有文件扩展名) 。 将该文件保存到 `.kube` 本地计算机上的用户配置文件的文件夹中。

下面是该命令的示例输出 `Get-HcsKubernetesUserConfig` 。

```PowerShell
[10.100.10.10]: PS>Get-HcsKubernetesUserConfig -AseUser
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJd01EVXhNekl4TkRRME5sb1hEVE13TURVeE1USXhORFEwTmxvd0ZURVRNQkVHQTFVRQpBeE1LYTNWaVpYSnVaWFJsY3pDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBS0M1CjlJbzRSU2hudG90QUdxdjNTYmRjOVd4UmJDYlRzWXU5S0RQeU9xanVoZE1UUE9PcmROOGNoa0x4NEFyZkZaU1AKZithUmhpdWZqSE56bWhucnkvZlprRGdqQzQzRmV5UHZzcTZXeVVDV0FEK2JBdi9wSkJDbkg2MldoWGNLZ1BVMApqU1k0ZkpXenNFbzBaREhoeUszSGN3MkxkbmdmaEpEanBQRFJBNkRWb2pIaktPb29OT1J1dURvUHpiOTg2dGhUCkZaQXJMZjRvZXRzTEk1ZzFYRTNzZzM1YVhyU0g3N2JPYVVsTGpYTzFYSnpFZlZWZ3BMWE5xR1ZqTXhBMVU2b1MKMXVJL0d1K1ArY
===========CUT=========================================CUT===================
    server: https://compute.myasegpu1.wdshcsso.com:6443
    name: kubernetes
contexts:
- context:
    cluster: kubernetes
    user: aseuser
    name: aseuser@kubernetes
current-context: aseuser@kubernetes
kind: Config
preferences: {}
users:
- name: aseuser
    user:
    client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMwRENDQWJpZ0F3SUJBZ0lJY1hOTXRPU2VwbG93RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TURBMU1UTXlNVFEwTkRaYUZ3MHlNVEExTVRNeU1UVXhNVEphTUJJeApFREFPQmdOVkJBTVRCMkZ6WlhWelpYSXdnZ0VpTUEwR0NTcUdTSWIzRFFFQkFRVUFBNElCRHdBd2dnRUtBb0lCCkFRRHVjQ1pKdm9qNFIrc0U3a1EyYmVjNEJkTXdpUEhmU2R2WnNDVVY0aTRRZGY1Yzd0dkE3OVRSZkRLQTY1d08Kd0h0QWdlK3lLK0hIQ1Qyd09RbWtNek1RNjZwVFEzUlE0eVdtRDZHR1cWZWMExBR1hFUUxWWHRuTUdGCi0tLS0tRU5EIFJTQSBQUklWQVRFIEtFWS0tLS0tCg==

[10.100.10.10]: PS>
```

在配置了计算角色的 Azure Stack 边缘设备上， `kubectl` 可以使用所有命令来监视模块或对其进行故障排除。 若要查看可用命令的列表，请 `kubectl --help` 在命令窗口中运行。

```PowerShell
C:\Users\myuser>kubectl --help
kubectl controls the Kubernetes cluster manager.

Find more information at: https://kubernetes.io/docs/reference/kubectl/overview/

Basic Commands (Beginner):
    create         Create a resource from a file or from stdin.
    expose         Take a replication controller, service, deployment or pod and expose it as a new Kubernetes Service
    run            Run a particular image on the cluster
    set            Set specific features on objects
    run-container  Run a particular image on the cluster. This command is deprecated, use "run" instead
==============CUT=============CUT============CUT========================

Usage:
    kubectl [flags] [options]

Use "kubectl <command> --help" for more information about a given command.
Use "kubectl options" for a list of global command-line options (applies to all commands).

C:\Users\myuser>
```

有关命令的完整列表 `kubectl` ，请参阅[ `kubectl` 速查表](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)。


### <a name="to-get-ip-of-service-or-module-exposed-outside-of-kubernetes-cluster"></a>获取在 Kubernetes 群集外部公开的服务或模块的 IP

若要获取在 Kubernetes 外部公开的负载均衡服务的 IP，请运行以下命令：

`kubectl get svc -n iotedge`

下面是在 Kubernetes 群集外公开的所有服务或模块的示例输出。 


```powershell
C:\Users\user>kubectl get svc -n iotedge
NAME           TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)                                       AGE
edgehub        LoadBalancer   10.103.52.225   10.128.44.243   443:31987/TCP,5671:32336/TCP,8883:30618/TCP   34h
iotedged       ClusterIP      10.107.236.20   <none>          35000/TCP,35001/TCP                           3d8h
webserverapp   LoadBalancer   10.105.186.35   10.128.44.244   8080:30976/TCP                                16h

C:\Users\user>
```
"外部 IP" 列中的 IP 地址对应于服务或模块的外部终结点。 还可以 [在 Kubernetes 仪表板中获取外部 IP](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#get-ip-address-for-services-or-modules)。


### <a name="to-check-if-module-deployed-successfully"></a>检查模块是否已成功部署

计算模块是已实现业务逻辑的容器。 Kubernetes pod 可以运行多个容器。 若要检查是否成功部署了计算模块，请运行 `get pods` 命令，并检查容器 (与计算模块) 是否正在运行。

若要获取在特定命名空间中运行的所有 pod 的列表，请运行以下命令：

`get pods -n <namespace>`

下面是在命名空间中运行的所有 pod 的示例输出 `iotedge` 。

```
C:\Users\myuser>kubectl get pods -n iotedge
NAME                        READY   STATUS    RESTARTS   AGE
edgeagent-cf6d4ffd4-q5l2k   2/2     Running   0          20h
edgehub-8c9dc8788-2mvwv     2/2     Running   0          56m
filemove-66c49984b7-h8lxc   2/2     Running   0          56m
iotedged-675d7f4b5f-9nml4   1/1     Running   0          20h

C:\Users\myuser>
```

状态 **状态** 表明命名空间中的所有 pod 都正在运行，并且 " **就绪** " 表示在 pod 中部署的容器数。 在上面的示例中，所有 pod 都在运行，并且每个 pod 中部署的所有模块都在运行。 

对于给定命名空间的特定 pod 更详细的输出，你可以运行以下命令：

`kubectl describe pod <pod name> -n <namespace>` 

示例输出显示在此处。

```
C:\Users\myuser>kubectl describe pod filemove-66c49984b7 -n iotedge
Name:           filemove-66c49984b7-h8lxc
Namespace:      iotedge
Priority:       0
Node:           k8s-1hwf613cl-1hwf613/10.139.218.12
Start Time:     Thu, 14 May 2020 12:46:28 -0700
Labels:         net.azure-devices.edge.deviceid=myasegpu-edge
                net.azure-devices.edge.hub=myasegpu2iothub.azure-devices.net
                net.azure-devices.edge.module=filemove
                pod-template-hash=66c49984b7
Annotations:    net.azure-devices.edge.original-moduleid: filemove
Status:         Running
IP:             172.17.75.81
IPs:            <none>
Controlled By:  ReplicaSet/filemove-66c49984b7
Containers:
    proxy:
    Container ID:   docker://fd7975ca78209a633a1f314631042a0892a833b7e942db2e7708b41f03e8daaf
    Image:          azureiotedge/azureiotedge-proxy:0.1.0-beta8
    Image ID:       docker://sha256:5efbf6238f13d24bab9a2b499e5e05bc0c33ab1587d6cf6f289cdbe7aa667563
    Port:           <none>
    Host Port:      <none>
    State:          Running
        Started:      Thu, 14 May 2020 12:46:30 -0700
    Ready:          True
    Restart Count:  0
    Environment:
        PROXY_LOG:  Debug
=============CUT===============================CUT===========================
Volumes:
    config-volume:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      iotedged-proxy-config
    Optional:  false
    trust-bundle-volume:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      iotedged-proxy-trust-bundle
    Optional:  false
    myasesmb1local:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  myasesmb1local
    ReadOnly:   false
    myasesmb1:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  myasesmb1
    ReadOnly:   false
    filemove-token-pzvw8:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  filemove-token-pzvw8
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                    node.kubernetes.io/unreachable:NoExecute for 300s
Events:          <none>


C:\Users\myuser>
```

### <a name="to-get-container-logs"></a>获取容器日志

若要获取模块的日志，请运行以下命令：

`kubectl logs <pod_name> -n <namespace> --all-containers` 

由于 `all-containers` 标志将转储所有容器的所有日志，因此查看最近的错误的一种好方法是使用选项 `--tail 10` 。

下面是一个示例输出。 

```
C:\Users\myuser>kubectl logs filemove-66c49984b7-h8lxc -n iotedge --all-containers --tail 10
DEBUG 2020-05-14T20:40:42Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 1 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:12Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 1 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
05/14/2020 19:46:44: Info: Opening module client connection.
05/14/2020 19:46:45: Info: Open done.
05/14/2020 19:46:45: Info: Initializing with input: /home/input, output: /home/output, protocol: Amqp.
05/14/2020 19:46:45: Info: IoT Hub module client initialized.

C:\Users\myuser>
```

## <a name="exit-the-remote-session"></a>退出远程会话

若要退出远程 PowerShell 会话，请关闭 PowerShell 窗口。

## <a name="next-steps"></a>后续步骤

- 将 [Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md) 部署 Azure 门户。
