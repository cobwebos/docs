<properties
	pageTitle="安装 Azure 命令行界面 | Azure"
	description="安装适用于 Mac、Linux 和 Windows 的 Azure 命令行接口 (CLI) 即可使用 Azure 服务"
	editor=""
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services="virtual-machines-linux,virtual-network,storage,azure-resource-manager"
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/17/2016"
	ms.author="danlep"/>
    
# 安装 Azure CLI

> [AZURE.SELECTOR]
- [PowerShell](/documentation/articles/powershell-install-configure/)
- [Azure CLI](/documentation/articles/xplat-cli-install/)

快速安装 Azure 命令行界面 (Azure CLI)，以便使用一组基于 shell 的开源命令在 Microsoft Azure 中创建和管理资源。可以使用多种选项安装最新版本：

* 从 npm 包安装（需要 Node.js 和 npm）
* 为不同的操作系统使用提供的安装程序包之一
* 在 Docker 主机中以容器形式安装 Azure CLI
    
有关更多选项和背景信息，请参阅 [GitHub](https://github.com/azure/azure-xplat-cli) 上的项目存储库。

安装 Azure CLI 之后，[将它连接到 Azure 订阅](xplat-cli-connect.md)，从命令行接口（Bash、终端、命令提示符等）运行 **azure** 命令来使用 Azure 资源。



## 选项 1.安装 npm 包

若要从 npm 包安装 CLI，需要在系统上安装最新的 Node.js 和 npm。然后，运行以下命令安装 Azure CLI 包。（在 Linux 分发版中，可能需要使用 **sudo** 才能成功运行 __npm__ 命令。）

	npm install azure-cli -g

> [AZURE.NOTE]如果需要为操作系统安装或更新 Node.js 和 npm，请参阅 [Nodejs.org](https://nodejs.org/en/download/package-manager/) 上的文档。我们建议安装最新的 Node.js LTS 版本 (4.x)。如果你使用旧版本，可能会遇到安装错误。有关 npm 的详细信息，请访问 [npmjs.com](https://www.npmjs.com/)。

## 选项 2.使用安装程序

以下 CLI 安装程序包也可供下载：


* [Mac OS X 安装程序][mac-installer]

* [Windows 安装程序][windows-installer]

* [Linux tar 文件][linux-installer]（需要 Node.js 和 npm）- 通过运行 `sudo npm install -g <path to downloaded tar file>` 安装


## 选项 3.使用 Docker 容器

如果已设置 Docker 主机，可以在 Docker 容器中运行 Azure CLI。运行：

```
docker run -it microsoft/azure-cli
```

有关 Docker 的详细信息，请访问 [docker.com](https://docs.docker.com/engine/understanding-docker/)。

## 运行 Azure CLI 命令
安装 Azure CLI 之后，请从命令行用户界面（Bash、终端、命令提示符等）运行 **azure** 命令。例如，若要运行帮助命令，请键入以下命令：

```
azure help
```
> [AZURE.NOTE]在某些 Linux 分发版中，可能会收到类似于“`/usr/bin/env: ‘node’: No such file or directory`”的错误。此错误消息来自最近安装在 /usr/bin/nodejs 中的 nodejs。若要解决此错误，请运行以下命令创建 /usr/bin/node 的符号链接：

```
sudo ln -s /usr/bin/nodejs /usr/bin/node
```

若要查看安装的 Azure CLI 版本，请键入以下命令：

```
azure --version
```

你现在已准备就绪！ 若要访问所有 CLI 命令来使用自己的资源，请[从 Azure CLI 连接到 Azure 订阅](xplat-cli-connect.md)。

>[AZURE.NOTE] 首次使用 Azure CLI 时，会看到一条消息，询问是否允许 Microsoft 收集有关如何使用 CLI 的信息。参与为自愿性质。如果选择参与，通过运行 `azure telemetry --disable` 即可随时停止参与。若要随时启用参与，请运行 `azure telemetry --enable`。


## 更新 CLI

Microsoft 会频繁发布 Azure CLI 的更新版本。使用适用于操作系统的安装程序来重新安装 CLI，或运行最新的 Docker 容器。如果已安装最新的 Node.js 和 npm，请键入以下命令（在 Linux 分发版上可能需要使用 **sudo**）进行更新。

```
npm update -g azure-cli
```

## 启用 tab 自动补全

Mac 和 Linux 支持 tab 自动补全 CLI 命令。

如要在 zsh 中启用，运行：

```
echo '. <(azure --completion)' >> .zshrc
```

若要在 bash 中启用，运行：

```
azure --completion >> ~/azure.completion.sh
echo 'source ~/azure.completion.sh' >> ~/.bash_profile
```


## 后续步骤 

* [从 CLI 连接到 Azure 订阅](/documentation/articles/xplat-cli-connect/)以创建和管理 Azure 资源。

* 若要了解有关 Azure CLI、下载源代码、报告问题或贡献项目的详细信息，请访问[适用于 Azure CLI 的 GitHub 存储库](https://github.com/azure/azure-xplat-cli)。

* 如果在使用 Azure CLI 或 Azure 方面有疑问，请访问 [Azure 论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurescripting)。

* 对于 Linux 系统，你还可以通过从[源](http://aka.ms/linux-azure-cli)进行构建的方式来安装 Azure CLI。有关从源代码生成的详细信息，请参阅源存档中随附的 INSTALL 文件。

[mac-installer]: http://aka.ms/mac-azure-cli
[windows-installer]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=windowsazurexplatcli&mode=new
[linux-installer]: http://aka.ms/linux-azure-cli
[cliasm]: /documentation/articles/virtual-machines-command-line-tools/
[cliarm]: /documentation/articles/azure-cli-arm-commands/

<!---HONumber=Mooncake_0926_2016-->