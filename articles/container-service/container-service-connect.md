<properties
   pageTitle="连接到 Azure 容器服务群集 | Microsoft Azure"
   description="使用 SSH 隧道连接到 Azure 容器服务群集。"
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, 容器, 微服务, DC/OS, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/13/2016"
   ms.author="rogardle"/>


# 连接到 Azure 容器服务群集

由 Azure 容器服务部署的 DC/OS 和 Docker Swarm 群集将公开 REST 终结点。但是，这些终结点不对外界开放。为了管理这些终结点，必须创建安全外壳 (SSH) 隧道。建立 SSH 隧道后，就可以对群集终结点运行命令，并通过自己系统中的浏览器查看群集 UI。本文档将介绍从 Linux、OS X 和 Windows 创建 SSH 隧道的步骤。

>[AZURE.NOTE] 可以使用群集管理系统来创建 SSH 会话。但不建议这样做。直接在管理系统上操作会引致无意配置更改所带来的风险。

## 在 Linux 或 OS X 上创建 SSH 隧道

在 Linux 或 OS X 上创建 SSH 隧道的首要事项是查找负载平衡主机的公用 DNS 名称。若要执行此操作，请展开资源组，显示每个资源。查找并选择主机的公用 IP 地址。这会打开一个边栏选项卡，提供有关公用 IP 地址的信息，包括 DNS 名称。保存此名称以供稍后使用。<br />


![公用 DNS 名称](media/pubdns.png)

现在打开外壳程序并运行以下命令：

**PORT** 是想要公开的终结点的端口。对于 Swarm，端口为 2375。对于 DC/OS，端口为 80。**USERNAME** 是部署群集时提供的用户名。**DNSPREFIX** 是部署群集时提供的 DNS 前缀。**REGION** 是资源组所在的区域。**PATH\_TO\_PRIVATE\_KEY** [可选] 是创建容器服务群集时提供的与公钥相对应的私钥的路径。使用此带有 -i 标志的选项。

```bash
ssh -L PORT:localhost:PORT -f -N [USERNAME]@[DNSPREFIX]mgmt.[REGION].cloudapp.azure.com -p 2200
```
> SSH 连接端口为 2200，而不是标准端口 22。

## DC/OS 隧道

若要打开通向与 DC/OS 相关的终结点的隧道，请执行如下命令：

```bash
sudo ssh -L 80:localhost:80 -f -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

现在从这些位置可以访问 DC/OS 相关终结点：

- DC/OS：`http://localhost/`
- Marathon：`http://localhost/marathon`
- Mesos：`http://localhost/mesos`

同样，可以通过此隧道到达每个应用程序的剩余 API。

## Swarm 隧道

若要打开通向 Swarm 终结点隧道，请执行如下命令：

```bash
ssh -L 2375:localhost:2375 -f -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

现在可以设置 DOCKER\_HOST 环境变量，如下所示。可以继续像平常一样使用 Docker 命令行接口 (CLI)。

```bash
export DOCKER_HOST=:2375
```

## 在 Windows 上创建 SSH 隧道

有多种方法可在 Windows 上创建 SSH 隧道。本文档将介绍如何使用 PuTTY 进行创建。

将 PuTTY 下载到 Windows 系统中并运行该应用程序。

输入主机名 - 由群集管理员用户名和群集中第一个主机的公用 DNS 名组成。“主机名”将如下所示：`adminuser@PublicDNS`。输入 2200 作为“端口”。

![PuTTY 配置 1](media/putty1.png)

选择“SSH”和“身份验证”。添加私钥文件进行身份验证。

![PuTTY 配置 2](media/putty2.png)

选择**隧道**并配置以下各转发端口：
- **源端口：**对于 DC/OS 优先使用 80，对于 Swarm 优先使用 2375。
- **目标：**DC/OS 使用 localhost:80，Swarm 使用 localhost:2375。

以下示例适用于 DC/OS，但 Docker Swarm 与之类似。

>[AZURE.NOTE] 创建此隧道时，不能使用端口 80。

![PuTTY 配置 3](media/putty3.png)

完成后，保存连接配置，并连接 PuTTY 会话。连接时，可以在 PuTTY 事件日志中看到端口配置。

![PuTTY 事件日志](media/putty4.png)

为 DC/OS 配置隧道后，可以从这些位置访问相关终结点：

- DC/OS：`http://localhost/`
- Marathon：`http://localhost/marathon`
- Mesos：`http://localhost/mesos`

为 Docker Swarm 配置隧道后，可以通过 Docker CLI 访问 Swarm 群集。首先需要将名为 `DOCKER_HOST` 的 Windows 环境变量配置为值 ` :2375`。

## 后续步骤

使用 DC/OS 或 Swarm 部署或管理容器：

- [使用 Azure 容器服务和 DC/OS](container-service-mesos-marathon-rest.md)
- [使用 Azure 容器服务和 Docker Swarm](container-service-docker-swarm.md)

<!---HONumber=AcomDC_0921_2016-->