---
title: "适用于 Windows 的 Azure Kubernetes 群集 | Microsoft Docs"
description: "在 Azure 容器服务中部署用于 Windows 容器的 Kubernetes 群集并开始使用"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: c43648dae95d90d0ee9f3d6b5bedfad7ab4889ca
ms.lasthandoff: 03/22/2017


---

# <a name="get-started-with-kubernetes-and-windows-containers-in-container-service"></a>容器服务中的 Kubernetes 和 Windows 容器入门


本文介绍如何在包含 Windows 节点的 Azure 容器服务中创建 Kubernetes 群集，以运行 Windows 容器。 

> [!NOTE]
> 对 Azure 容器服务中 Kubernetes 的 Windows 容器的支持处于预览状态。 使用 Azure 门户或 Resource Manager 模板来创建包含 Windows 节点的 Kubernetes 群集。 Azure CLI 2.0 当前不支持此功能。
>



下图显示 Azure 容器服务中 Kubernetes 群集的体系结构，包括一个 Linux 主节点和两个 Windows 代理节点。 

* Linux 主节点为 Kubernetes REST API 提供服务，可由 SSH 通过端口 22 访问，或者由 `kubectl` 通过端口 443 访问。 
* Windows 代理节点分组在 Azure 可用性集中，运行用户的容器。 可以通过主节点经由 RDP SSH 隧道访问 Windows 节点。 Azure 负载均衡器规则根据公开的服务以动态方式添加到群集。


![Azure 上的 Kubernetes 群集映像](media/container-service-kubernetes-windows-walkthrough/kubernetes-windows.png)

所有 VM 在相同的专用虚拟网络中，并完全可以相互访问。 所有 VM 都运行 kubelet、 Docker 和代理。

## <a name="prerequisites"></a>先决条件


* **SSH RSA 公钥**：通过门户或某个 Azure 快速入门模板进行部署时需提供 SSH RSA 公钥，以便针对 Azure 容器服务虚拟机进行身份验证。 若要创建安全外壳 (SSH) RSA 密钥，请参阅 [OS X 和 Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) 或 [Windows](../virtual-machines/virtual-machines-linux-ssh-from-windows.md) 指南。 

* **服务主体客户端 ID 和机密**：有关详细信息和指南，请参阅[关于 Kubernetes 群集的服务主体](container-service-kubernetes-service-principal.md)。




## <a name="create-the-cluster"></a>创建群集

可以使用 Azure 门户[创建 Kubernetes 群集](container-service-deployment.md#create-a-cluster-by-using-the-azure-portal)（含 Windows 代理节点）。 创建群集时，请注意以下设置：

* 在“基本信息”边栏选项卡的“Orchestrator”中，选择“Kubernetes”。 

  ![选择 Kubernetes Orchestrator](media/container-service-kubernetes-windows-walkthrough/portal-select-kubernetes.png)

* 在“主配置”边栏选项卡上，输入 Linux 主节点的用户凭据和服务主体凭据。 选择 1个、3 个或 5 个主节点。

* 在“代理配置”边栏选项卡的“操作系统”中，选择“Windows (预览版)”。 输入 Windows 代理节点的管理员凭据。

  ![选择 Windows 代理](media/container-service-kubernetes-windows-walkthrough/portal-select-windows.png)

有关更多详细信息，请参阅[部署 Azure 容器服务群集](container-service-deployment.md)。

## <a name="connect-to-the-cluster"></a>连接至群集

使用 `kubectl` 命令行工具从本地计算机连接到 Kubernetes 群集的主节点。 有关安装和设置 `kubectl` 的步骤，请参阅[连接到 Azure 容器服务群集](container-service-connect.md#connect-to-a-kubernetes-cluster)。 可以使用 `kubectl` 命令来访问 Kubernetes Web UI，及创建和管理 Windows 容器工作负荷。

## <a name="create-your-first-kubernetes-service"></a>创建第一个 Kubernetes 服务

创建群集并与 `kubectl` 连接后，可以尝试启动基本的 Windows Web 应用，并将其公开到 Internet。 在此示例中，使用 YAML 文件指定容器资源，然后使用 `kubctl apply` 进行创建。

1. 若要查看节点列表，请键入 `kubectl get nodes`。 若要获得节点的完整详细信息，请键入：  

    ```
    kubectl get nodes -o yaml
    ```

2. 创建名为 `simpleweb.yaml` 的文件，并复制以下内容。 此文件使用 [Docker 中心](https://hub.docker.com/r/microsoft/windowsservercore/)的 Windows Server 2016 Server 核心基础 OS 映像来设置 Web 应用。  

```yaml
  apiVersion: v1
  kind: Service
  metadata:
    name: win-webserver
    labels:
      app: win-webserver
  spec:
    ports:
      # the port that this service should serve on
    - port: 80
      targetPort: 80
    selector:
      app: win-webserver
    type: LoadBalancer
  ---
  apiVersion: extensions/v1beta1
  kind: Deployment
  metadata:
    labels:
      app: win-webserver
    name: win-webserver
  spec:
    replicas: 1
    template:
      metadata:
        labels:
          app: win-webserver
        name: win-webserver
      spec:
        containers:
        - name: windowswebserver
          image: microsoft/windowsservercore
          command:
          - powershell.exe
          - -command
          - "<#code used from https://gist.github.com/wagnerandrade/5424431#> ; $$listener = New-Object System.Net.HttpListener ; $$listener.Prefixes.Add('http://*:80/') ; $$listener.Start() ; $$callerCounts = @{} ; Write-Host('Listening at http://*:80/') ; while ($$listener.IsListening) { ;$$context = $$listener.GetContext() ;$$requestUrl = $$context.Request.Url ;$$clientIP = $$context.Request.RemoteEndPoint.Address ;$$response = $$context.Response ;Write-Host '' ;Write-Host('> {0}' -f $$requestUrl) ;  ;$$count = 1 ;$$k=$$callerCounts.Get_Item($$clientIP) ;if ($$k -ne $$null) { $$count += $$k } ;$$callerCounts.Set_Item($$clientIP, $$count) ;$$header='<html><body><H1>Windows Container Web Server</H1>' ;$$callerCountsString='' ;$$callerCounts.Keys | % { $$callerCountsString+='<p>IP {0} callerCount {1} ' -f $$_,$$callerCounts.Item($$_) } ;$$footer='</body></html>' ;$$content='{0}{1}{2}' -f $$header,$$callerCountsString,$$footer ;Write-Output $$content ;$$buffer = [System.Text.Encoding]::UTF8.GetBytes($$content) ;$$response.ContentLength64 = $$buffer.Length ;$$response.OutputStream.Write($$buffer, 0, $$buffer.Length) ;$$response.Close() ;$$responseStatus = $$response.StatusCode ;Write-Host('< {0}' -f $$responseStatus)  } ; "
        nodeSelector:
          beta.kubernetes.io/os: windows
  ```

      
> [!NOTE] 
> 该配置包括 `type: LoadBalancer`。 此设置会让服务通过 Azure 负载均衡器在 Internet 上公开。 有关详细信息，请参阅[对 Azure 容器服务中 Kubernetes 群集内的容器进行负载均衡](container-service-kubernetes-load-balancing.md)。
>

## <a name="start-the-application"></a>启动应用程序

1. 若要启动该应用程序，请键入：  

    ```
    kubectl apply -f simpleweb.yaml
    ```  
  
  
2. 若要验证服务部署（需要大约 30 秒），请键入：  

    ```
    kubectl get pods
    ```

3. 该服务运行后，若要查看该服务的内部和外部 IP 地址，键入：

    ```
    kubectl get svc
    ``` 
  
    ![Windows 服务的 IP 地址](media/container-service-kubernetes-windows-walkthrough/externalipa.png)

    外部 IP 地址的添加需要几分钟时间。 负载均衡器配置外部地址之前，会显示为 `<pending>`。

4. 外部 IP 地址可用后，可以在 Web 浏览器中访问该服务。

    ![浏览器中的 Windows Server 应用](media/container-service-kubernetes-windows-walkthrough/wincontainerwebserver.png)


## <a name="access-the-windows-nodes"></a>访问 Windows 节点
可通过远程桌面连接从本地 Windows 计算机访问 Windows 节点。 建议通过主节点经由 RDP SSH 隧道实现这一目的。 

有多种方法可在 Windows 上创建 SSH 隧道。 本部分介绍如何使用 PuTTY 创建隧道。

1. [将 PuTTY 下载](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)到 Windows 系统。

2. 运行应用程序。

3. 输入主机名 - 由群集管理员用户名和群集中第一个主机的公用 DNS 名组成。 “主机名”类似于 `adminuser@PublicDNSName`。 输入 22 作为“端口”。

  ![PuTTY 配置 1](media/container-service-kubernetes-windows-walkthrough/putty1.png)

4. 选择“SSH”>“身份验证”。 添加用于身份验证的专用密钥文件（.ppk 格式）的路径。 可以使用 [PuTTYgen](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) 等工具，通过用于创建群集的 SSH 密钥生成此文件。

  ![PuTTY 配置 2](media/container-service-kubernetes-windows-walkthrough/putty2.png)

5. 选择“SSH”>“隧道”并配置转发端口。 由于本地 Windows 计算机已使用端口 3389，因此建议使用以下设置来访问 Windows 节点 0 和 Windows 节点 1。 （其他 Windows 节点沿用此模式。）

    **Windows 节点 0**

    * **源端口：**3390
    * **目标：**10.240.245.5:3389

    **Windows 节点 1**

    * **源端口：**3391
    * **目标：**10.240.245.6:3389

    ![Windows RDP 隧道的图像](media/container-service-kubernetes-windows-walkthrough/rdptunnels.png)

6. 完成后，单击“会话”>“保存”保存连接配置。

7. 若要连接到 PuTTY 会话，请单击“打开”。 完成主节点连接。

8. 启动远程桌面连接。 若要连接到第一个 Windows 节点，对于“计算机”，请指定 `localhost:3390`，然后单击“连接”。 （若要连接到第二个 Windows 节点，指定 `localhost:3390`，依次类推。）若要完成连接，需提供在部署过程中配置的本地 Windows 管理员密码。


## <a name="next-steps"></a>后续步骤

以下是有关 Kubernetes 详细信息的一些推荐链接：

* [Kubernetes 训练营](https://kubernetesbootcamp.github.io/kubernetes-bootcamp/index.html) - 介绍如何部署、伸缩、更新和调试容器化应用程序。
* [Kubernetes 用户指南](http://kubernetes.io/docs/user-guide/) - 介绍如何在现有 Kubernetes 群集中运行程序。
* [Kubernetes 示例](https://github.com/kubernetes/kubernetes/tree/master/examples) - 提供有关如何使用 Kubernetes 运行实际应用程序的示例。
