<properties
   pageTitle="安装 DC/OS CLI | Microsoft Azure"
   description="安装 DC/OS CLI。"
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="容器, 微服务, DC/OS, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/10/2016"
   ms.author="rogardle"/>

>[AZURE.NOTE] 这适用于基于 DC/OS 的 ACS 群集。无需为基于 Swarm 的 ACS 群集执行此操作。

首先，[连接到基于 DC/OS 的 ACS 群集](../articles/container-service/container-service-connect.md)。完成连接后，可以使用以下命令在客户端计算机上安装 DC/OS CLI。

```bash
sudo pip install virtualenv
mkdir dcos && cd dcos
wget https://raw.githubusercontent.com/mesosphere/dcos-cli/master/bin/install/install-optout-dcos-cli.sh
chmod +x install-optout-dcos-cli.sh
./install-optout-dcos-cli.sh . http://localhost --add-path yes
```

如果使用的是旧版本的 Python，可能会看到某些“InsecurePlatformWarnings”。可以放心地忽略这些警告。

若要在无需重新启动 shell 的情况下开始安装，请运行：

```bash
source ~/.bashrc
```

启动新的 shell 时，无需执行此步骤。

现在可以确认 CLI 已安装：

```bash
dcos --help
```

<!---HONumber=AcomDC_0921_2016-->