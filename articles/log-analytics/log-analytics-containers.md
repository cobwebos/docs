---
title: "Azure Log Analytics 中的容器监视解决方案 | Microsoft Docs"
description: "Log Analytics 中的容器监视解决方案可帮助用户在单个位置查看和管理 Docker 和 Windows 容器主机。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: e1e4b52b-92d5-4bfa-8a09-ff8c6b5a9f78
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.author: magoedte;banders
ms.openlocfilehash: 562a7a73e2d440c0c3e3e8ab9e94ffd6c1fba7d9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="container-monitoring-solution-in-log-analytics"></a>Log Analytics 中的容器监视解决方案

![容器符号](./media/log-analytics-containers/containers-symbol.png)

本文介绍如何设置和使用 Log Analytics 中的容器监视解决方案，它可以帮助用户在单个位置查看和管理 Docker 和 Windows 容器主机。 Docker 是一种软件虚拟化系统，用于创建自动将软件部署到其 IT 基础结构的容器。

解决方案显示哪些容器正在运行，它们正在运行哪些容器映像以及正在运行容器的位置。 可以查看详细审核信息，它显示了与容器一起使用的命令。 并且，用户可以通过查看和搜索集中式日志来排查容器问题，而无需远程查看 Docker 或 Windows 主机。 可以在主机上找到可能具有干扰性并且占用过多资源的容器。 并且，可以查看容器的集中式 CPU、内存、存储器、网络使用情况和性能信息。 在运行 Windows 的计算机上，可以集中比较 Windows Server、Hyper-V 和 Docker 容器中的日志。 解决方案支持以下容器 Orchestrator：

- Docker Swarm
- DC/OS
- Kubernetes
- Service Fabric
- Red Hat OpenShift


下图显示了 OMS 中各种容器主机和代理之间的关系。

![容器关系图](./media/log-analytics-containers/containers-diagram.png)

## <a name="system-requirements-and-supported-platforms"></a>系统要求和支持的平台

开始之前，请查看以下详细信息来验证是否满足先决条件。

### <a name="container-monitoring-solution-support-for-docker-orchestrator-and-os-platform"></a>Docker Orchestrator 和 OS 平台的容器监视解决方案支持
下表概括了 Log Analytics 中容器清单、性能和日志的 Docker 业务流程和操作系统监视支持。   

| | ACS | Linux | Windows | 容器<br>清单 | 映像<br>清单 | 节点<br>清单 | 容器<br>性能 | 容器<br>事件 | 事件<br>日志 | 容器<br>日志 |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Kubernetes | &#8226; | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Mesosphere<br>DC/OS | &#8226; | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; |
| Docker<br>Swarm | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| 服务<br>Fabric | | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Red Hat Open<br>Shift | | &#8226; | | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; | | &#8226; |
| Windows Server<br>（独立） | | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Linux Server<br>（独立） | | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |


### <a name="docker-versions-supported-on-linux"></a>Linux 上支持的 Docker 版本

- Docker 1.11 到 Docker 1.13
- Docker CE 和 EE v17.06

### <a name="x64-linux-distributions-supported-as-container-hosts"></a>支持用作容器主机的 x64 Linux 分发

- Ubuntu 14.04 LTS 和 16.04 LTS
- CoreOS(stable)
- Amazon Linux 2016.09.0
- openSUSE 13.2
- openSUSE LEAP 42.2
- CentOS 7.2 和 7.3
- SLES 12
- RHEL 7.2 和 7.3
- Red Hat OpenShift 容器平台 (OCP) 3.4 和 3.5
- ACS Mesosphere DC/OS 1.7.3 到 1.8.8
- ACS Kubernetes 1.4.5 到 1.6
    - 仅版本 1.4.1-45 和更高版本适用于 Linux 的 OMS 代理支持 Kubernetes 事件、Kubernetes 清单和容器进程
- ACS Docker Swarm

### <a name="supported-windows-operating-system"></a>支持的 Windows 操作系统

- Windows Server 2016
- Windows 10 周年版（专业版或企业版）

### <a name="docker-versions-supported-on-windows"></a>Windows 上支持的 Docker 版本

- Docker 1.12 和 1.13
- Docker 17.03.0 及更高版本

## <a name="installing-and-configuring-the-solution"></a>安装和配置解决方案
使用以下信息安装和配置解决方案。

1. 从 [Azure 应用商店](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ContainersOMS?tab=Overview)或使用[从解决方案库中添加 Log Analytics 解决方案](log-analytics-add-solutions.md)中所述的过程，将容器监视解决方案添加到 OMS 工作区。

2. 通过 OMS 代理安装和使用 Docker。 根据所用操作系统和 Docker Ochestrator，可使用下列方法来配置代理。
  - 对于独立主机：
    - 在支持的 Linux 操作系统上，安装并运行 Docker，然后安装和配置 [OMS Agent for Linux](log-analytics-agent-linux.md)。  
    - 在 CoreOS 上，无法运行 OMS Agent for Linux。 取而代之，将运行 OMS Agent for Linux 的容器化版本。 如果使用 Azure 政府云中的容器，请查看[包括 CoreOS 在内的 Linux 容器主机](#for-all-linux-container-hosts-including-coreos)或[包括 CoreOS 在内的 Azure 政府 Linux 容器主机](#for-all-azure-government-linux-container-hosts-including-coreos)。
    - 在 Windows Server 2016 和 Windows 10 上安装 Docker 引擎和客户端，然后连接到代理以收集信息并将其发送到 Log Analytics。 在 Windows 环境下，请参阅[安装并配置 Windows 容器主机](#install-and-configure-windows-container-hosts)。
  - 对于 Docker 多主机业务流程：
    - 如果有 Red Hat OpenShift 环境，请查看 [为 Red Hat OpenShift 配置 OMS 代理](#configure-an-oms-agent-for-red-hat-openshift)。
    - 如果有 Kubernetes 群集正使用 Azure 容器服务，请参阅[为 Kubernetes 配置 OMS 代理](#configure-an-oms-agent-for-kubernetes)。
    - 如果拥有 Azure 容器服务 DC/OS 群集，请前往[通过 Operations Management Suite 监视 Azure 容器服务 DC/OS 群集](../container-service/dcos-swarm/container-service-monitoring-oms.md)了解详细信息。
    - 如果拥有 Docker Swarm 模式环境，请访问[配置适用于 Docker Swarm 的 OMS 代理](#configure-an-oms-agent-for-docker-swarm)了解更多信息。
    - 如果在 Service Fabric 中使用容器，请在 [Azure Service Fabric 概述](../service-fabric/service-fabric-overview.md)中了解详细信息。

请参阅 [Windows 上的 Docker 引擎](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon)一文，详细了解如何在运行 Windows 的计算机上安装和配置 Docker 引擎。

> [!IMPORTANT]
> 在容器主机上安装 [OMS Agent for Linux](log-analytics-agent-linux.md)**之前**，主机上必须运行 Docker。 如果在安装 Docker 之前已经安装了代理，则需要重新安装 OMS Agent for Linux。 有关 Docker 的详细信息，请参阅 [Docker 网站](https://www.docker.com)。


### <a name="install-and-configure-linux-container-hosts"></a>安装和配置 Linux 容器主机

安装 Docker 之后，请使用以下容器主机设置来配置代理以供 Docker 使用。 首先，需要 OMS 工作区 ID 和密钥，可在 Azure 门户中找到它们。 在工作区中，单击“快速启动” > “计算机”，查看工作区 ID和主键。  将它们复制并粘贴到喜爱的编辑器中。

对于除了 CoreOS 之外的所有 Linux 容器主机：

- 有关如何安装 OMS Agent for Linux 的详细信息和步骤，请参阅[将 Linux 计算机连接到 Operations Management Suite (OMS)](log-analytics-agent-linux.md)。

对于包括 CoreOS 在内的所有 Linux 容器主机：

启动想要监视的 OMS 容器。 修改并使用以下示例：

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25225:25225 --name="omsagent" --restart=always microsoft/oms
```

对于包括 CoreOS 在内的所有 Azure 政府 Linux 容器主机：

启动想要监视的 OMS 容器。 修改并使用以下示例：

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/log:/var/log -e WSID="your workspace id" -e KEY="your key" -e DOMAIN="opinsights.azure.us" -p 127.0.0.1:25225:25225 -p 127.0.0.1:25224:25224/udp --name="omsagent" -h=`hostname` --restart=always microsoft/oms
```

从使用已安装的 Linux 代理切换为使用容器中的 Linux 代理

如果以前使用直接安装的代理，并且想要改为使用容器中运行的代理，则必须首先删除 OMS Agent for Linux。 请参阅[卸载 OMS Agent for Linux](log-analytics-agent-linux.md#uninstalling-the-oms-agent-for-linux)，了解如何成功卸载代理。  

#### <a name="configure-an-oms-agent-for-docker-swarm"></a>配置适用于 Docker Swarm 的 OMS 代理

可以在 Docker Swarm 中运行 OMS 代理作为全局服务。 使用以下信息创建 OMS 代理服务。 需要插入 OMS 工作区 ID 和主键。

- 在主节点上运行以下命令。

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock  -e WSID="<WORKSPACE ID>" -e KEY="<PRIMARY KEY>" -p 25225:25225 -p 25224:25224/udp  --restart-condition=on-failure microsoft/oms
    ```

##### <a name="secure-secrets-for-docker-swarm"></a>保护 Docker Swarm 的机密

对于 Docker Swarm，一旦创建工作区 ID 的机密和主键，请使用下列信息来创建机密信息。

1. 在主节点上运行以下命令。

    ```
    echo "WSID" | docker secret create WSID -
    echo "KEY" | docker secret create KEY -
    ```

2. 验证机密是否已正确创建。

    ```
    keiko@swarmm-master-13957614-0:/run# sudo docker secret ls
    ```

    ```
    ID                          NAME                CREATED             UPDATED
    j2fj153zxy91j8zbcitnjxjiv   WSID                43 minutes ago      43 minutes ago
    l9rh3n987g9c45zffuxdxetd9   KEY                 38 minutes ago      38 minutes ago
    ```

3. 运行以下命令将机密装载到容器化 OMS 代理。

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --secret source=WSID,target=WSID --secret source=KEY,target=KEY  -p 25225:25225 -p 25224:25224/udp --restart-condition=on-failure microsoft/oms
    ```

#### <a name="configure-an-oms-agent-for-red-hat-openshift"></a>配置适用于 Red Hat OpenShift 的 OMS 代理
可通过三种方法将 OMS 代理添加到 Red Hat OpenShift，以开始收集容器监视数据。

* 在每个 OpenShift 节点上直接[安装 OMS Agent for Linux](log-analytics-agent-linux.md)  
* 在每个位于 Azure 中的 OpenShift 节点上[启用 Log Analytics VM 扩展](log-analytics-azure-vm-extension.md)  
* 安装 OMS 代理作为 OpenShift daemon-set  

本部分介绍安装 OMS 代理作为 OpenShift daemon-set 需要执行的步骤。  

1. 登录到 OpenShift 主节点，从 GitHub 将 yaml 文件 [ocp-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-omsagent.yaml)复制到主节点，并使用 OMS 工作区 ID 和主键修改值。
2. 运行以下命令，为 OMS 创建项目并设置用户帐户。

    ```
    oadm new-project omslogging --node-selector='zone=default'
    oc project omslogging  
    oc create serviceaccount omsagent  
    oadm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oadm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

4. 若要部署 daemon-set，请运行以下命令：

    `oc create -f ocp-omsagent.yaml`

5. 若要验证其配置和运行是否正确，请键入以下命令：

    `oc describe daemonset omsagent`  

    输出应类似于：

    ```
    [ocpadmin@khm-0 ~]$ oc describe ds oms  
    Name:           oms  
    Image(s):       microsoft/oms  
    Selector:       name=omsagent  
    Node-Selector:  zone=default  
    Labels:         agentVersion=1.4.0-12  
                    dockerProviderVersion=10.0.0-25  
                    name=omsagent  
    Desired Number of Nodes Scheduled: 3  
    Current Number of Nodes Scheduled: 3  
    Number of Nodes Misscheduled: 0  
    Pods Status:    3 Running / 0 Waiting / 0 Succeeded / 0 Failed  
    No events.  
    ```

在使用 OMS 代理 daemon-set yaml 文件时，若要使用机密来保护 OMS 工作区 ID 和主键，请执行以下步骤。

1. 登录到 OpenShift 主节点并从 GitHub 复制 yaml 文件 [ocp-ds-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-ds-omsagent.yaml) 和机密生成脚本 [ocp-secretgen.sh](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-secretgen.sh)。  该脚本将为 OMS 工作区 ID 和主键生成机密 yaml 文件，用于保护机密信息。  
2. 运行以下命令，为 OMS 创建项目并设置用户帐户。 机密生成脚本需要 OMS 工作区 ID <WSID> 和主键 <KEY>，并且会在完成时创建 ocp-secret.yaml 文件。  

    ```
    oadm new-project omslogging --node-selector='zone=default'  
    oc project omslogging  
    oc create serviceaccount omsagent  
    oadm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oadm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

4. 通过运行以下命令部署机密文件：

    `oc create -f ocp-secret.yaml`

5. 通过运行以下命令验证部署：

    `oc describe secret omsagent-secret`  

    输出应类似于：  

    ```
    [ocpadmin@khocp-master-0 ~]$ oc describe ds oms  
    Name:           oms  
    Image(s):       microsoft/oms  
    Selector:       name=omsagent  
    Node-Selector:  zone=default  
    Labels:         agentVersion=1.4.0-12  
                    dockerProviderVersion=10.0.0-25  
                    name=omsagent  
    Desired Number of Nodes Scheduled: 3  
    Current Number of Nodes Scheduled: 3  
    Number of Nodes Misscheduled: 0  
    Pods Status:    3 Running / 0 Waiting / 0 Succeeded / 0 Failed  
    No events.  
    ```

6. 通过运行以下命令部署 OMS 代理 daemon-set yaml 文件：

    `oc create -f ocp-ds-omsagent.yaml`  

7. 通过运行以下命令验证部署：

    `oc describe ds oms`

    输出应类似于：

    ```
    [ocpadmin@khocp-master-0 ~]$ oc describe secret omsagent-secret  
    Name:           omsagent-secret  
    Namespace:      omslogging  
    Labels:         <none>  
    Annotations:    <none>  

    Type:   Opaque  

     Data  
     ====  
     KEY:    89 bytes  
     WSID:   37 bytes  
    ```

#### <a name="configure-an-oms-linux-agent-for-kubernetes"></a>配置适用于 Kubernetes 的 OMS Linux 代理

对于 Kubernetes，使用脚本为工作区 ID 和主密钥生成机密 yaml 文件，以便安装适用于 Linux 的 OMS 代理。 在 [OMS Docker Kubernetes GitHub](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes) 页上，存在你可以对其使用或不使用机密信息的文件。

- 默认适用于 Linux 的 OMS 代理 DaemonSet 没有机密信息 (omsagent.yaml)
- 适用于 Linux 的 OMS 代理 DaemonSet yaml 文件使用机密信息 (omsagent-ds-secrets.yaml) 与机密生成脚本生成机密 yaml (omsagentsecret.yaml) 文件。

可以选择创建包含或不包含机密的 omsagent DaemonSet。

不包含机密的默认 OMSagent DaemonSet yaml 文件

- 对于默认 OMS 代理 DaemonSet yaml 文件，将 `<WSID>` 和 `<KEY>` 分别替换为 WSID 和 KEY。 将文件复制到主节点并运行以下命令：

    ```
    sudo kubectl create -f omsagent.yaml
    ```

包含机密的默认 OMSagent DaemonSet yaml 文件

1. 若要使用包含机密信息的 OMS 代理 DaemonSet，请先创建机密。
    1. 复制脚本和机密模板文件，并确保它们位于同一目录中。
        - 生成机密的脚本 - secret-gen.sh
        - 机密模板 - secret-template.yaml
    2. 运行脚本，如下例所示。 脚本要求输入 OMS 工作区 ID 和主密钥，在输入这些值后，脚本将创建一个可运行的机密 .yaml 文件。   

        ```
        #> sudo bash ./secret-gen.sh
        ```

    3. 通过运行以下命令创建机密 Pod：
        ```
        sudo kubectl create -f omsagentsecret.yaml
        ```

    4. 若要验证，请运行以下命令：

        ```
        keiko@ubuntu16-13db:~# sudo kubectl get secrets
        ```

        输出应类似于：

        ```
        NAME                  TYPE                                  DATA      AGE
        default-token-gvl91   kubernetes.io/service-account-token   3         50d
        omsagent-secret       Opaque                                2         1d
        ```

        ```
        keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
        ```

        输出应类似于：

        ```
        Name:           omsagent-secret
        Namespace:      default
        Labels:         <none>
        Annotations:    <none>

        Type:   Opaque

        Data
        ====
        WSID:   36 bytes
        KEY:    88 bytes
        ```

    5. 通过运行 ``` sudo kubectl create -f omsagent-ds-secrets.yaml ``` 创建 omsagent daemon-set

2. 验证 OMS 代理 DaemonSet 是否正在运行，命令如下：

    ```
    keiko@ubuntu16-13db:~# sudo kubectl get ds omsagent
    ```

    ```
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   3         3         <none>          1h
    ```


对于 Kubernetes，使用脚本为适用于 Linux 的 OMS 代理的工作区 ID 和主密钥生成机密 yaml 文件。 将以下示例信息与 [omsagent yaml 文件](https://github.com/Microsoft/OMS-docker/blob/master/Kubernetes/omsagent.yaml)配合使用来保护机密信息。

```
keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
Name:           omsagent-secret
Namespace:      default
Labels:         <none>
Annotations:    <none>

Type:   Opaque

Data
====
WSID:   36 bytes
KEY:    88 bytes
```

#### <a name="configure-an-oms-agent-for-windows-kubernetes"></a>配置适用于 Windows Kubernetes 的 OMS 代理
对于 Windows Kubernetes，使用脚本为工作区 ID 和主密钥生成机密 yaml 文件，以便安装 OMS 代理。 在 [OMS Docker Kubernetes GitHub](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes/windows) 页上，存在你可以对其使用机密信息的文件。  需要分别为主节点和代理节点安装 OMS 代理。  

1. 若要在主节点上使用包含机密信息的 OMS 代理 DaemonSet，请先登录并创建机密。
    1. 复制脚本和机密模板文件，并确保它们位于同一目录中。
        - 生成机密的脚本 - secret-gen.sh
        - 机密模板 - secret-template.yaml

    2. 运行脚本，如下例所示。 脚本要求输入 OMS 工作区 ID 和主密钥，在输入这些值后，脚本将创建一个可运行的机密 .yaml 文件。   

        ```
        #> sudo bash ./secret-gen.sh
        ```
    3. 通过运行 ``` kubectl create -f omsagentsecret.yaml ``` 创建 omsagent daemon-set
    4. 若要检查，请运行以下命令：
    
        ``` 
        root@ubuntu16-13db:~# kubectl get secrets
        ```

        输出应类似于：

        ```
        NAME                  TYPE                                  DATA      AGE
        default-token-gvl91   kubernetes.io/service-account-token   3         50d
        omsagent-secret       Opaque                                2         1d
        root@ubuntu16-13db:~# kubectl describe secrets omsagent-secret
        Name:           omsagent-secret
        Namespace:      default
        Labels:         <none>
        Annotations:    <none>
    
        Type:   Opaque
    
        Data
        ====
        WSID:   36 bytes
        KEY:    88 bytes 
        ```

    5. 通过运行 ```kubectl create -f ws-omsagent-de-secrets.yaml``` 创建 omsagent daemon-set

2. 验证 OMS 代理 DaemonSet 是否正在运行，命令如下：

    ```
    root@ubuntu16-13db:~# kubectl get deployment omsagent
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   1         1         <none>          1h
    ```

3. 若要在运行 Windows 的工作节点上安装代理，请按照[安装和配置 Windows 容器主机](#install-and-configure-windows-container-hosts)部分中的步骤进行操作。 

#### <a name="use-helm-to-deploy-oms-agent-on-linux-kubernetes"></a>使用 Helm 在 Linux Kubernetes 上部署 OMS 代理 
若要使用 helm 在 Linux Kubernetes 环境上部署 OMS 代理，请执行以下步骤。

1. 通过运行 ```helm install --name omsagent --set omsagent.secret.wsid=<WSID>,omsagent.secret.key=<KEY> stable/msoms``` 创建 omsagent daemon-set
2. 结果将与以下内容类似：

    ```
    NAME:   omsagent
    LAST DEPLOYED: Tue Sep 19 20:37:46 2017
    NAMESPACE: default
    STATUS: DEPLOYED

    RESOURCES:
    ==> v1/Secret
    NAME            TYPE    DATA  AGE
    omsagent-msoms  Opaque  3     3s

    ==> v1beta1/DaemonSet
    NAME            DESIRED  CURRENT  READY  UP-TO-DATE  AVAILABLE  NODE-SELECTOR  AGE
    omsagent-msoms  3        3        3      3           3          <none>         3s
    ```
3. 可以通过运行 ```helm status "omsagent"``` 来查看 omsagent 的状态，输出将与以下内容类似：

    ```
    keiko@k8s-master-3814F33-0:~$ helm status omsagent
    LAST DEPLOYED: Tue Sep 19 20:37:46 2017
    NAMESPACE: default
    STATUS: DEPLOYED
 
    RESOURCES:
    ==> v1/Secret
    NAME            TYPE    DATA  AGE
    omsagent-msoms  Opaque  3     17m
 
    ==> v1beta1/DaemonSet
    NAME            DESIRED  CURRENT  READY  UP-TO-DATE  AVAILABLE  NODE-SELECTOR  AGE
    omsagent-msoms  3        3        3      3           3          <none>         17m
    ```
有关更多信息，请访问[容器解决方案 Helm 图表](https://aka.ms/omscontainerhelm)。

### <a name="install-and-configure-windows-container-hosts"></a>安装并配置 Windows 容器主机

使用本部分中信息来安装并配置 Windows 容器主机。

#### <a name="preparation-before-installing-windows-agents"></a>安装 Windows 代理之前的准备

在运行 Windows 的计算机上安装代理之前，需配置 Docker 服务。 配置允许 Windows 代理或 Log Analytics 虚拟机扩展使用 Docker TCP 套接字，因此代理能够远程访问 Docker 守护程序并捕获用于监视的数据。

##### <a name="to-start-docker-and-verify-its-configuration"></a>启动 Docker 并验证其配置

为 Windows Server 设置 TCP 命名管道需要执行以下步骤：

1. 在 Windows PowerShell 中，启用 TCP 管道和命名的管道。

    ```
    Stop-Service docker
    dockerd --unregister-service
    dockerd --register-service -H npipe:// -H 0.0.0.0:2375  
    Start-Service docker
    ```

2. 使用 TCP 管道和命名管道的配置文件配置 Docker。 该配置文件位于 C:\ProgramData\docker\config\daemon.json。

    在 daemon.json 文件中，需要以下项：

    ```
    {
    "hosts": ["tcp://0.0.0.0:2375", "npipe://"]
    }
    ```

若要详细了解用于 Windows 容器的 Docker 守护程序配置，请参阅 [Windows 上的 Docker 引擎](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon)。


#### <a name="install-windows-agents"></a>安装 Windows 代理

若要启用 Windows 和 Hyper-V 容器监视，请在属于容器主机的 Windows 计算机上安装 Microsoft Monitoring Agent (MMA)。 要了解在本地环境中运行 Windows 的计算机，请参阅[将 Windows 计算机连接到 Log Analytics](log-analytics-windows-agents.md)。 为使虚拟机在 Azure 中运行，请使用[虚拟机扩展](log-analytics-azure-vm-extension.md)将其连接到 Log Analytics。

可以监视在 Service Fabric 上运行的 Windows 容器。 但是，目前 Service Fabric 仅支持[在 Azure 中运行的虚拟机](log-analytics-azure-vm-extension.md)和[在本地环境中运行 Windows 的计算机](log-analytics-windows-agents.md)。

可以验证已为 Windows 正确设置容器监视解决方案。 要检查是否已正确下载管理包，请查找 ContainerManagement.xxx。 文件应位于 C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs 文件夹中。


## <a name="solution-components"></a>解决方案组件

如果使用的是 Windows 代理，添加此解决方案后，将使用代理在每台计算机上安装以下管理包。 无需对管理包进行任何配置或维护。

- ContainerManagement.xxx 安装在 C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs 中

## <a name="container-data-collection-details"></a>容器数据收集详细信息
容器监视解决方案使用用户启用的代理从容器主机和容器中收集各种性能指标和日志数据。

以下代理类型每 3 分钟收集一次数据。

- [适用于 Linux 的 OMS 代理](log-analytics-linux-agents.md)
- [Windows 代理](log-analytics-windows-agents.md)
- [Log Analytics VM 扩展](log-analytics-azure-vm-extension.md)


### <a name="container-records"></a>容器记录

下表显示了容器监视解决方案收集的记录以及日志搜索结果中显示的数据类型的示例。

| 数据类型 | 日志搜索中的数据类型 | 字段 |
| --- | --- | --- |
| 主机和容器的性能 | `Type=Perf` | 计算机、ObjectName、CounterName、处理器时间百分比、磁盘读取 MB、磁盘写入 MB、内存使用 MB、网络接收字节数、网络发送字节数、处理器使用秒数、网络、CounterValue、TimeGenerated、CounterPath、SourceSystem |
| 容器库存 | `Type=ContainerInventory` | TimeGenerated、计算机、容器名称、ContainerHostname、映像、ImageTag、ContainerState、ExitCode、EnvironmentVar、命令、CreatedTime、StartedTime、FinishedTime、SourceSystem、ContainerID、ImageID |
| 容器映像库存 | `Type=ContainerImageInventory` | TimeGenerated、计算机、映像、ImageTag、ImageSize、VirtualSize、正在运行、暂停、停止、失败、SourceSystem、ImageID、TotalContainer |
| 容器日志 | `Type=ContainerLog` | TimeGenerated、计算机、映像 ID、容器名称、LogEntrySource、LogEntry、SourceSystem、ContainerID |
| 容器服务日志 | `Type=ContainerServiceLog`  | TimeGenerated、计算机、TimeOfCommand、映像、命令、SourceSystem、ContainerID |
| 容器节点清单 | `Type=ContainerNodeInventory_CL`| TimeGenerated、计算机、ClassName_s、DockerVersion_s、OperatingSystem_s、Volume_s、Network_s、NodeRole_s、OrchestratorType_s、InstanceID_g、SourceSystem|
| Kubernetes 清单 | `Type=KubePodInventory_CL` | TimeGenerated、计算机、PodLabel_deployment_s、PodLabel_deploymentconfig_s、PodLabel_docker_registry_s、Name_s、Namespace_s、PodStatus_s、PodIp_s、PodUid_g、PodCreationTimeStamp_t、SourceSystem |
| 容器进程 | `Type=ContainerProcess_CL` | TimeGenerated、计算机、Pod_s、Namespace_s、ClassName_s、InstanceID_s、Uid_s、PID_s、PPID_s、C_s、STIME_s、Tty_s、TIME_s、Cmd_s、Id_s、Name_s、SourceSystem |
| Kubernetes 事件 | `Type=KubeEvents_CL` | TimeGenerated、计算机、Name_s、ObjectKind_s、Namespace_s、Reason_s、Type_s、SourceComponent_s、SourceSystem、消息 |

追加到 PodLabel 数据类型的标签是你自己的自定义标签。 表中显示的追加的 PodLabel 标签是示例。 因此，`PodLabel_deployment_s`、`PodLabel_deploymentconfig_s` 和 `PodLabel_docker_registry_s` 在你环境的数据集中存在差异，但通常类似于 `PodLabel_yourlabel_s`。


## <a name="monitor-containers"></a>监视容器
在 OMS 门户中启用解决方案后，“容器”磁贴显示有关容器主机和在主机中运行的容器的摘要信息。

![容器磁贴](./media/log-analytics-containers/containers-title.png)

该磁贴概述了你在环境中拥有多少个容器以及它们是失败、正在运行还是已停止。

### <a name="using-the-containers-dashboard"></a>使用容器仪表板
单击“容器”磁贴。 将在磁贴中看到按以下内容组织的视图：

- 容器事件 - 显示容器状态和包含失败的容器的计算机。
- 容器日志 - 显示随时间生成的容器日志文件图表，以及具有最大数量日志文件的计算机列表。
- Kubernetes 事件 - 显示随时间生成的 Kubernetes 事件图表，以及 Pod 生成事件的原因列表。 仅在 Linux 环境中使用此数据集。
- Kubernetes 命名空间清单 - 显示命名空间和 Pod 的数量，并显示其层次结构。 仅在 Linux 环境中使用此数据集。
- 容器节点清单 - 显示容器节点/主机上使用的业务流程类型的数量。 此计算机节点/主机还会按容器数列出。 仅在 Linux 环境中使用此数据集。
- 容器映像清单 - 显示所用的容器映像总数以及映像类型的数量。 映像数量还按映像标记列出。
- 容器状态 - 显示包含正在运行的容器的容器节点/主机计算机的总数。 计算机还按正在运行的主机数列出。
- 容器进程 - 显示随着时间推移运行的容器进程的折线图。 容器还会按容器内正在运行的命令/进程列出。 仅在 Linux 环境中使用此数据集。
- 容器 CPU 性能 - 显示计算机节点/主机随时间推移的平均 CPU 利用率的折线图。 还根据 CPU 平均使用率列出计算机节点/主机。
- 容器内存性能 - 显示随时间推移的内存使用率的折线图。 还根据实例名称列出计算机内存利用率。
- 计算机性能 - 显示随时间推移的 CPU 性能百分比、随时间推移的内存使用率百分比和随着时间推移的可用磁盘空间的兆字节数的折线图。 可以将鼠标悬停在图表中的任意行，查看更多详细信息。


仪表板中的每个区域都是以可视化形式表示的对收集的数据执行的搜索。

![容器仪表板](./media/log-analytics-containers/containers-dash01.png)

![容器仪表板](./media/log-analytics-containers/containers-dash02.png)

在“容器状态”区域中，单击顶部区域，如下所示。

![容器状态](./media/log-analytics-containers/containers-status.png)

日志搜索将打开，显示有关容器状态的信息。

![容器的日志搜索](./media/log-analytics-containers/containers-log-search.png)

可以在此处编辑搜索查询以对其进行修改，以便查找你感兴趣的特定信息。 有关日志搜索的详细信息，请参阅 [Log Analytics 中的日志搜索](log-analytics-log-searches.md)。

## <a name="troubleshoot-by-finding-a-failed-container"></a>通过查找失败的容器进行故障排除

如果容器退出时带有非零退出代码，则 Log Analytics 会将其标记为“失败”。 可以在“失败容器”区域中查看环境中的错误和故障的概述。

### <a name="to-find-failed-containers"></a>查找失败的容器
1. 单击“容器状态”区域。  
   ![容器状态](./media/log-analytics-containers/containers-status.png)
2. 日志搜索将打开并显示容器状态，如下所示。  
   ![容器状态](./media/log-analytics-containers/containers-log-search.png)
3. 接下来，单击失败容器的聚合值查看详细信息。 展开“**显示更多**”以查看映像 ID。  
   ![失败的容器](./media/log-analytics-containers/containers-state-failed.png)  
4. 接下来，在搜索查询中键入以下内容。 `Type=ContainerInventory <ImageID>` 可查看有关映像的详细信息，如映像大小以及已停止和失败映像的数量。  
   ![失败的容器](./media/log-analytics-containers/containers-failed04.png)

## <a name="search-logs-for-container-data"></a>在日志中搜索容器数据
解决特定错误时，它可以帮助你查看环境中发生错误的位置。 以下日志类型将帮助你创建查询以返回所需的信息。


- **ContainerImageInventory** – 尝试查找按映像组织的信息并查看映像 ID 或大小等映像信息时，请使用此类型。
- **ContainerInventory** – 当需要有关容器位置、容器名称和容器中运行的映像的信息时，请使用此类型。
- **ContainerLog** – 想要查找特定的错误日志信息和条目时，请使用此类型。
- ContainerNodeInventory_CL - 如果需要容器所在主机/节点的信息时，可使用此类型。 它可提供 Docker 版本、业务流程类型、存储和网络信息。
- ContainerProcess_CL - 使用此类型可快速查看容器内正在运行的进程。
- **ContainerServiceLog** – 尝试查找 Docker 守护程序的审核线索信息（如 start、stop、delete 或 pull 命令）时，请使用此类型。
- KubeEvents_CL- 使用此类型可查看 Kubernetes 事件。
- KubePodInventory_CL- 如果想要了解群集层次结构信息，请使用此类型。


### <a name="to-search-logs-for-container-data"></a>搜索容器数据的日志
* 选择一个最近失败的映像，并找到它的错误日志。 首先，通过使用 **ContainerInventory** 搜索查找运行该映像的容器名称。 例如，搜索 `Type=ContainerInventory ubuntu Failed`  
    ![搜索 Ubuntu 容器](./media/log-analytics-containers/search-ubuntu.png)

  名称旁边的容器的名称，并搜索这些日志。 在此示例中，它是 `Type=ContainerLog cranky_stonebreaker`。

**查看性能信息**

开始构造查询时，最好首先了解哪些查询可以实现。 例如，若要查看所有性能数据，请输入以下搜索查询，以尝试广泛的查询。

```
Type=Perf
```

![容器性能](./media/log-analytics-containers/containers-perf01.png)

你可以通过在查询右侧键入特定容器的名称，将性能数据的显示范围限定到特定容器。

```
Type=Perf <containerName>
```

它列出了为单个容器收集的性能指标。

![容器性能](./media/log-analytics-containers/containers-perf03.png)

## <a name="example-log-search-queries"></a>日志搜索查询示例
从一或两个示例开始生成查询，并修改它们以适应环境，这通常很有用。 可以首先尝试使用“示例查询”区域，它可以帮助你构建更高级的查询。

[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

![容器查询](./media/log-analytics-containers/containers-queries.png)


## <a name="saving-log-search-queries"></a>保存日志搜索查询
保存查询是 Log Analytics 中的一项标准功能。 通过保存这些查询，你日后可以方便地使用你觉得有用的查询。

创建一个对你有用的查询后，单击“日志搜索”页面底部的“收藏夹”将其保存。 稍后可以从“**我的仪表板**”页轻松访问它。

## <a name="next-steps"></a>后续步骤
* [搜索日志](log-analytics-log-searches.md)以查看详细的容器数据记录。
