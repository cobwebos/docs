# <a name="make-a-remote-connection-to-a-kubernetes-dcos-or-docker-swarm-cluster"></a>建立到 Kubernetes、DC/OS 或 Docker Swarm 群集的远程连接
创建 Azure 容器服务群集后，需要连接到该群集才能部署和管理工作负荷。 本文介绍如何通过远程计算机连接到群集的主 VM。 

Kubernetes、DC/OS 和 Docker Swarm 群集在本地提供 HTTP 终结点。 对于 Kubernetes，此终结点在 Internet 上安全公开，用户可在连接到 Internet 的任意计算机上通过运行 `kubectl` 命令行工具来访问此终结点。 

对于 DC/OS 和 Docker Swarm，我们建议创建一个从本地计算机通往群集管理系统的安全外壳 (SSH) 隧道。 建立隧道后，可以运行使用 HTTP 终结点的命令，通过本地系统查看 Orchestrator 的 Web 界面（如果可用）。 

## <a name="prerequisites"></a>先决条件

* [部署在 Azure 容器服务中](../articles/container-service/dcos-swarm/container-service-deployment.md)的 Kubernetes、DC/OS 或 Docker Swarm 群集。
* SSH RSA 私钥文件，对应于在部署期间添加到群集的公钥。 这些命令假设 SSH 私钥位于计算机上的 `$HOME/.ssh/id_rsa` 中。 有关详细信息，请参阅适用于 [macOS 和 Linux](../articles/virtual-machines/linux/mac-create-ssh-keys.md) 或 [Windows](../articles/virtual-machines/linux/ssh-from-windows.md) 的说明。 如果 SSH 连接不起作用，可能需要[重置 SSH 密钥](../articles/virtual-machines/linux/troubleshoot-ssh-connection.md)。

## <a name="connect-to-a-kubernetes-cluster"></a>连接到 Kubernetes 群集

遵循以下步骤在计算机上安装和配置 `kubectl`。

> [!NOTE] 
> 在 Linux 或 macOS 上，可能需要使用 `sudo` 运行本部分中的命令。
> 

### <a name="install-kubectl"></a>安装 kubectl
安装此工具的方法之一是使用 `az acs kubernetes install-cli` Azure CLI 2.0 命令。 若要运行此命令，请确保[已安装](/cli/azure/install-az-cli2)最新的 Azure CLI 2.0 并已登录到 Azure 帐户 (`az login`)。

```azurecli
# Linux or macOS
az acs kubernetes install-cli [--install-location=/some/directory/kubectl]

# Windows
az acs kubernetes install-cli [--install-location=C:\some\directory\kubectl.exe]
```

或者，也可以直接从 [Kubernetes 版本页面](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG.md)下载最新的 `kubectl` 客户端。 有关详细信息，请参阅 [Installing and Setting up kubectl](https://kubernetes.io/docs/tasks/kubectl/install/)（安装和设置 kubectl）。

### <a name="download-cluster-credentials"></a>下载群集凭据
安装 `kubectl` 后，需要将群集凭据复制到计算机。 获取凭据的方法之一是使用 `az acs kubernetes get-credentials` 命令。 传递资源组的名称和容器服务资源的名称：

```azurecli
az acs kubernetes get-credentials --resource-group=<cluster-resource-group> --name=<cluster-name>
```

此命令将群集凭据下载到 `$HOME/.kube/config`（`kubectl` 预期凭据所在的位置）。

或者，可以使用 `scp` 安全地将该文件从主 VM 中的 `$HOME/.kube/config` 复制到本地计算机。 例如：

```bash
mkdir $HOME/.kube
scp azureuser@<master-dns-name>:.kube/config $HOME/.kube/config
```

如果在 Windows 上操作，可以使用 Windows 上的 Ubuntu Bash、PuTTy 安全文件复制客户端或类似的工具。

### <a name="use-kubectl"></a>使用 kubectl

配置 `kubectl` 后，请通过列出群集中的节点来测试连接：

```bash
kubectl get nodes
```

可以尝试其他 `kubectl` 命令。 例如，查看 Kubernetes 仪表板。 首先，运行一个代理连接到 Kubernetes API 服务器：

```bash
kubectl proxy
```

Kubernetes UI 现已显示在 `http://localhost:8001/ui` 上。

有关详细信息，请参阅 [Kubernetes 快速入门](http://kubernetes.io/docs/user-guide/quick-start/)。

## <a name="connect-to-a-dcos-or-swarm-cluster"></a>连接到 DC/OS 或 Swarm 群集

若要使用 Azure 容器服务部署的 DC/OS 和 Docker Swarm 群集，请遵循以下说明，从本地 Linux、macOS 或 Windows 系统创建 SSH 隧道。 

> [!NOTE]
> 这些说明重点介绍如何通过 SSH 在隧道中传输 TCP 流量。 也可以使用某个内部群集管理系统启动交互式 SSH 会话，但我们不建议这样做。 直接在内部系统中操作会带来配置意外发生更改的风险。  
> 

### <a name="create-an-ssh-tunnel-on-linux-or-macos"></a>在 Linux 或 macOS 上创建 SSH 隧道
在 Linux 或 macOS 上创建 SSH 隧道的首要事项是查找负载均衡主机的公用 DNS 名称。 执行以下步骤:


1. 在 [Azure 门户](https://portal.azure.com)中，浏览到包含容器服务群集的资源组。 展开资源组，显示每个资源。 

2. 单击“容器服务”资源，并单击“概览”。 群集的“主 FQDN”会显示在“概要”下面。 保存此名称以供稍后使用。 

    ![公用 DNS 名称](./media/container-service-connect/pubdns.png)

    或者，在容器服务中运行 `az acs show` 命令。 在命令输出中查看 **Master Profile:fqdn** 属性。

3. 现在，打开 shell 并结合以下值运行 `ssh` 命令： 

    **LOCAL_PORT** 是要连接到的隧道的服务端上的 TCP 端口。 对于 Swarm，请将此属性设置为 2375。 对于 DC/OS，请设置为 80。 
    **REMOTE_PORT** 是要公开的终结点的端口。 对于 Swarm，请使用端口 2375。 对于 DC/OS，请使用端口 80。  
    **USERNAME** 是部署群集时提供的用户名。  
    **DNSPREFIX** 是部署群集时提供的 DNS 前缀。  
    **REGION** 是资源组所在的区域。  
    **PATH_TO_PRIVATE_KEY** [可选] 是创建群集时提供的与公钥相对应的私钥的路径。 请结合 `-i` 标志使用此选项。

    ```bash
    ssh -fNL LOCAL_PORT:localhost:REMOTE_PORT -p 2200 [USERNAME]@[DNSPREFIX]mgmt.[REGION].cloudapp.azure.com
    ```
  
  > [!NOTE]
  > SSH 连接端口为 2200，而不是标准端口 22。 在包含多个主 VM 的群集中，这是第一个主 VM 的连接端口。
  > 

  该命令会在没有输出的情况下返回。

请参阅以下部分中的 DC/OS 和 Swarm 示例。    

### <a name="dcos-tunnel"></a>DC/OS 隧道
若要打开 DC/OS 终结点的隧道，请运行如下所示的命令：

```bash
sudo ssh -fNL 80:localhost:80 -p 2200 azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com 
```

> [!NOTE]
> 确保没有另一个绑定端口 80 的本地进程。 必要时可以指定除端口 80 以外的本地端口，如端口 8080。 但是，如果使用此端口，某些 Web UI 链接可能无法正常工作。
>

现在，可通过以下 URL 从本地系统访问 DC/OS 终结点（假设本地端口为 80）：

* DC/OS： `http://localhost:80/`
* Marathon： `http://localhost:80/marathon`
* Mesos： `http://localhost:80/mesos`

同样，可以通过此隧道到达每个应用程序的剩余 API。

### <a name="swarm-tunnel"></a>Swarm 隧道
若要打开 Swarm 终结点的隧道，请运行如下所示的命令：

```bash
ssh -fNL 2375:localhost:2375 -p 2200 azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com
```
> [!NOTE]
> 确保没有另一个绑定端口 2375 的本地进程。 例如，如果在本地运行 Docker 守护程序，则会按默认设置来使用端口 2375。 必要时可以指定除端口 2375 以外的本地端口。
>

现在，可以在本地系统上使用 Docker 命令行界面 (Docker CLI) 访问 Docker Swarm 群集。 如需安装说明，请参阅 [Install Docker](https://docs.docker.com/engine/installation/)（安装 Docker）。

将 DOCKER_HOST 环境变量设置为针对隧道配置的本地端口。 

```bash
export DOCKER_HOST=:2375
```

运行 Docker 命令，通过隧道连接到 Docker Swarm 群集。 例如：

```bash
docker info
```

### <a name="create-an-ssh-tunnel-on-windows"></a>在 Windows 上创建 SSH 隧道
有多种方法可在 Windows 上创建 SSH 隧道。 如果运行基于 Windows 的 Ubuntu 上的 Bash 或类似工具，可以按照本文前面显示的用于 macOS 和 Linux 的 SSH 隧道说明进行操作。 本部分介绍如何使用 PuTTY（作为 Windows 上的替代方法）创建隧道。

1. [将 PuTTY 下载](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)到 Windows 系统。

2. 运行应用程序。

3. 输入主机名 - 由群集管理员用户名和群集中第一个主机的公用 DNS 名组成。 “主机名”类似于 `azureuser@PublicDNSName`。 输入 2200 作为“端口” 。

    ![PuTTY 配置 1](./media/container-service-connect/putty1.png)

4. 选择“SSH”>“身份验证”。添加用于身份验证的专用密钥文件（.ppk 格式）的路径。 可以使用 [PuTTYgen](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) 等工具，通过用于创建群集的 SSH 密钥生成此文件。

    ![PuTTY 配置 2](./media/container-service-connect/putty2.png)

5. 选择“SSH”>“隧道”并配置以下转发端口：

    * **源端口：**为 DC/OS 使用 80，为 Swarm 使用 2375。
    * **目标：** DC/OS 使用 localhost:80，Swarm 使用 localhost:2375。

    以下示例适用于 DC/OS，但 Docker Swarm 与之类似。

    > [!NOTE]
    > 创建此隧道时，不能使用端口 80。
    > 

    ![PuTTY 配置 3](./media/container-service-connect/putty3.png)

6. 完成后，单击“会话”>“保存”保存连接配置。

7. 若要连接到 PuTTY 会话，请单击“打开”。 连接时，可以在 PuTTY 事件日志中看到端口配置。

    ![PuTTY 事件日志](./media/container-service-connect/putty4.png)

为 DC/OS 配置隧道后，可从以下位置访问相关的终结点：

* DC/OS： `http://localhost/`
* Marathon： `http://localhost/marathon`
* Mesos： `http://localhost/mesos`

为 Docker Swarm 配置隧道后，请打开 Windows 设置，配置一个名为 `DOCKER_HOST`、值为 `:2375` 的系统环境变量。 然后，可以通过 Docker CLI 访问 Swarm 群集。

## <a name="next-steps"></a>后续步骤
在群集中部署和管理容器：

* [Work with Azure Container Service and Kubernetes](../articles/container-service/kubernetes/container-service-kubernetes-ui.md)（使用 Azure 容器服务和 Kubernetes）
* [使用 Azure 容器服务和 DC/OS](../articles/container-service//dcos-swarm/container-service-mesos-marathon-rest.md)
* [使用 Azure 容器服务和 Docker Swarm](../articles//container-service/dcos-swarm/container-service-docker-swarm.md)

