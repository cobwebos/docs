---
title: "已知问题和故障排除指南 | Microsoft Docs"
description: "已知问题列表和帮助故障排除指南"
services: machine-learning
author: svankam
ms.author: svankam
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: f79910ba4fea81e88fce90dd67ba7cb4db2e8220
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-machine-learning-workbench---known-issues-and-troubleshooting-guide"></a>Azure Machine Learning Workbench - 已知问题和故障排除指南 
作为使用 Azure Machine Learning Workbench 应用程序的一部分，本文帮助你查找和更正错误或遇到的问题。 

> [!IMPORTANT]
> 与支持团队沟通时，必须准备好内部版本号。 可以通过单击“帮助”菜单找到应用的内部版本号。 单击该内部版本号可将其复制到剪贴板中。 可将其粘贴到电子邮件或支持论坛以帮助报告问题。

![检查版本号](media/known-issues-and-troubleshooting-guide/buildno.png)

## <a name="machine-learning-msdn-forum"></a>机器学习 MSDN 论坛
我们开通了 MSDN 论坛，可以在其上发布问题。 产品团队会主动监视该论坛。 论坛 URL 为 [https://aka.ms/azureml-forum](https://aka.ms/azureml-forum)。 

## <a name="gather-diagnostics-information"></a>收集诊断信息
如果在请求帮助时可以提供诊断信息，有时会很有帮助。 下面是日志文件所在的位置：

### <a name="installer"></a>安装程序
如果在安装过程中遇到问题，可在此处找到安装程序日志文件：

```
# Windows:
%TEMP%\amlinstaller\logs\*

# macOS:
/tmp/amlinstaller/logs/*
```
可以打包这些目录的内容，然后将它发送给我们进行诊断。

### <a name="workbench-desktop-app"></a>Workbench 桌面应用
如果 Workbench 桌面崩溃，可以在此处找到日志文件：
```
# Windows
%APPDATA%\AmlWorkbench

# macOS
~/Library/Application Support/AmlWorkbench
``` 
可以打包这些目录的内容，然后将它发送给我们进行诊断。

### <a name="experiment-execution"></a>执行试验
如果从桌面应用提交的过程中某个特定脚本执行失败，请尝试通过 CLI 使用 `az ml experiment submit` 命令将其重新提交。 此命令应以 JSON 格式提供完整的错误消息，最重要的是它包含**操作 ID** 值。 请向我们发送包含**操作 ID** 的 JSON 文件，我们可以帮助诊断。 

如果某个特定脚本在提交时成功，但在执行时失败，它应输出**运行 ID** 来标识该特定的运行。 可使用以下命令打包相关日志文件：

```azurecli
# Create a ZIP file that contains all the diagnostics information
$ az ml experiment diagnostics -r <run_id> -t <target_name>
```

`az ml experiment diagnostics` 命令在项目根文件夹中生成 `diagnostics.zip` 文件。 此 ZIP 包包含处于执行时状态的整个项目文件夹，以及日志记录信息。 向我们发送诊断文件之前，请务必删除不想要包含的任何敏感信息。

## <a name="send-us-a-frown-or-a-smile"></a>发送哭脸（或笑脸）

在 Azure ML Workbench 中操作时，还可以单击应用程序 shell 左下角的笑脸图标向我们发送哭脸（或笑脸）。 可以选择性地包含自己的电子邮件地址（以方便我们做出回复）和/或当前状态的屏幕截图。 

## <a name="known-service-limits"></a>已知服务限制
- 允许的最大项目文件夹大小：25 MB。
    >[!NOTE]
    >此限制不适用于 `.git`、`docs` 和 `outputs` 文件夹。 这些文件夹名称区分大小写。 使用大文件时，请参阅[保存更改并处理大文件](how-to-read-write-files.md)。

- 允许的最大试验执行时间：7 天
- 运行后 `outputs` 文件夹中跟踪文件的最大大小：512 MB
  - 这意味着，如果脚本在输出文件夹中生成了大于 512 MB 的文件，则不会收集该文件夹中的文件。 使用大文件时，请参阅[保存更改并处理大文件](how-to-read-write-files.md)。

- 通过 SSH 连接到远程计算机或 Spark 群集时不支持 SSH 密钥。 目前仅支持用户名/密码模式。

- Mac 上不支持文本群集转换。

- 只有 Windows 或 Linux 上（Docker 容器中）不支持 RevoScalePy 库。 macOS 不支持此库。

## <a name="docker-error-read-connection-refused"></a>Docker 错误“读取: 连接被拒绝”
针对本地 Docker 容器执行时，有时可能会出现以下错误： 
```
Get https://registry-1.docker.io/v2/: 
dial tcp: 
lookup registry-1.docker.io on [::1]:53: read udp [::1]:49385->[::1]:53: 
read: connection refused
```

可以通过将 Docker DNS 服务器从 `automatic` 更改为固定值 `8.8.8.8` 来修复此错误。

## <a name="remove-vm-execution-error-no-tty-present"></a>消除 VM 执行错误“不存在 tty”
针对远程 Linux 计算机上的 Docker 容器执行时，可能会遇到以下错误消息：
```
sudo: no tty present and no askpass program specified.
``` 
如果使用 Azure 门户更改 Ubuntu Linux VM 的 root 密码，可能会发生此错误。 

Azure Machine Learning Workbench 需要有无密码 sudoers 访问权限才能在远程主机上运行。 最简单的解决方法是使用 _visudo_ 编辑以下文件（如果不存在该文件，可以创建它）：

```
$ sudo visudo -f /etc/sudoers
```

>[!IMPORTANT]
>必须使用 _visudo_ 而不能使用其他命令编辑该文件。 _visudo_ 会自动对所有 sudo 配置文件执行语法检查，如果无法生成语法正确的 sudoers 文件，可能会将你锁定在 sudo 以外。

在该文件的末尾插入以下行：

```
username ALL=(ALL) NOPASSWD:ALL
```

其中，_username_ 是 Azure Machine Learning Workbench 用来登录远程主机的名称。

必须将该行放在 #includedir "/etc/sudoers.d" 的后面，否则其他规则可能会重写它。

如果使用了更复杂的 sudo 配置，可能需要查阅以下网页中适用于 Ubuntu 的 sudo 文档：https://help.ubuntu.com/community/Sudoers

如果未在 Azure 中使用基于 Ubuntu 的 Linux VM 作为执行目标，也可能会发生上述错误。 仅支持将基于 Ubuntu 的 Linux VM 用于远程执行。 

## <a name="vm-disk-is-full"></a>VM 磁盘已满
在 Azure 中创建新 Linux VM 时，默认会获得 30-GB 的操作系统磁盘。 Docker 引擎默认使用相同的磁盘来提取映像和运行容器。 这可能会填满 OS 磁盘。发生这种情况时，会出现“VM 磁盘已满”错误。

快速解决方法是删除不再使用的所有 Docker 映像。 以下 Docker 命令即可解决此问题。 （当然，必须通过 SSH 连接到 VM 才能通过 bash shell 执行该 Docker 命令。）

```
$ docker system prune -a
```

还可以添加一个数据磁盘，并将 Docker 引擎配置为使用该数据磁盘来存储映像。 了解[如何添加数据磁盘](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/add-disk)。 然后，可以[更改 Docker 的映像存储位置](https://forums.docker.com/t/how-do-i-change-the-docker-image-installation-directory/1169)。

或者，可以扩展 OS 磁盘，这样就不需要更改 Docker 引擎配置。 了解[如何扩展 OS 磁盘](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/add-disk)。

## <a name="sharing-c-drive-on-windows"></a>在 Windows 上共享 C 驱动器
如果在 Windows 上的本地 Docker 容器中执行，在 `docker.compute` 文件中的 `aml_config` 下将 `sharedVolumes` 设置为 `true` 可以改善执行性能。 但是，这需要在“适用于 Windows 的 Docker 工具”中共享 C 驱动器。 如果无法共享 C 驱动器，请尝试遵循以下提示操作：

* 使用文件资源管理器检查 C 驱动器上的共享
* 打开网络适配器设置并为 vEthernet 卸载/重新安装“Microsoft Networks 的文件和打印机共享”
* 打开 Docker 设置并从 Docker 设置内共享 C 驱动器
* Windows 密码的更改会影响共享。 打开文件资源管理器，重新共享 C 驱动器，并输入新密码。
* 尝试在 Docker 中共享 C 驱动器时也可能遇到防火墙问题。 此[堆栈溢出文章](http://stackoverflow.com/questions/42203488/settings-to-windows-firewall-to-allow-docker-for-windows-to-share-drive/43904051)很有帮助。
* 使用域凭据共享 C 驱动器时，对无法访问的域控制器所在的网络，共享可能停止（例如，家庭网络和公共 WiFi 等）。 有关详细信息，请参阅[此文章](https://blogs.msdn.microsoft.com/stevelasker/2016/06/14/configuring-docker-for-windows-volumes/)。

还可以通过在 `docker.compute` 文件中将 `sharedVolumne` 设置为 `false`，以较低的性能代价避免共享问题。

## <a name="some-useful-docker-commands"></a>一些有用的 Docker 命令

下面是一些有用的 Docker 命令：

```sh
# display all running containers
$ docker ps

# dislplay all containers (running or stopped)
$ docke ps -a

# display all images
$ docker images

# show Docker logs of a container
$ docker logs <container_id>

# create a new container and launch into a bash shell
$ docker run <image_id> /bin/bash

# launch into a bash shell on a running container
$ docker exec -it <container_id> /bin/bash

# stop an running container
$ docker stop <container_id>

# delete a container
$ docker rm <container_id>

# delete an image
$ docker rmi <image_id>

# delete all unussed Docker images 
$ docker system prune -a

```