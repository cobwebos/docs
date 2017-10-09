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
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: ef5e058d81b64c46e8bad6f85c0bef0c69fb1512
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---

# <a name="azure-machine-learning-workbench---known-issues-and-troubleshooting-guide"></a>Azure Machine Learning Workbench - 已知问题和故障排除指南 
作为使用 Azure Machine Learning Workbench 应用程序的一部分，本文帮助你查找和更正错误或遇到的问题。 

> [!NOTE]
> 通过电子邮件或论坛帖子与支持团队沟通时，拥有内部版本号很有帮助。 可以通过单击“帮助”菜单找到应用的内部版本号。 单击该内部版本号可将其复制到剪贴板中。 可将其粘贴到电子邮件或支持论坛以帮助报告问题。

![检查版本号](media/known-issues-and-troubleshooting-guide/version.png)

## <a name="windows-and-mac"></a>Windows 和 Mac
### <a name="centos-based-azure-data-science-virtual-machine"></a>基于 CentOS 的 Azure 数据科学虚拟机 
* 不支持将作业提交到基于 CentOS 的 Azure 数据科学虚拟机 (DSVM)。 可以锁定[基于 Ubuntu 的 DSVM](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)。

### <a name="docker-error"></a>Docker 错误 
* 在本地 Docker 容器执行时，如果看到以下错误，可以通过将 Docker DNS 服务器从自动更改为固定 8.8.8.8 来修复该问题。 
```
Get https://registry-1.docker.io/v2/: 
dial tcp: 
lookup registry-1.docker.io on [::1]:53: read udp [::1]:49385->[::1]:53: 
read: connection refused
```
![映像](media/known-issues-and-troubleshooting-guide/docker_dns.png)


### <a name="linux-vm-password"></a>Linux VM 密码 
* 如果在 Ubuntu Linux VM 上更改 Azure 门户的密码，可能在其上执行作业时会遇到以下错误：
  ```
  sudo: no tty present and no askpass program specified.
  ``` 

  解决方法是在 _/etc/sudoers.d_ 中添加文件（示例 _myDockerUsers_）并包含以下内容：
  ```
  <your_username> ALL = NOPASSWD: /usr/bin/docker, /usr/bin/nvidia-docker
  ```

### <a name="ssh-keys"></a>SSH 密钥 
* 通过 SSH 连接到远程计算机或 Spark 群集时不支持 SSH 密钥。 仅支持用户名/密码模式。

## <a name="windows-only"></a>仅限 Windows 
### <a name="sharing-c-drive-in-docker"></a>在 Docker 中共享 C 驱动器 
* 使用文件资源管理器检查 C 驱动器上的共享
* 打开网络适配器设置并为 vEthernet 卸载/重新安装“Microsoft Networks 的文件和打印机共享”
* 打开 Docker 设置并从 Docker 设置内共享 C 驱动器
* Windows 密码的更改会影响共享。 打开文件资源管理器，重新共享 C 驱动器，并输入新密码。
* 尝试在 Docker 中共享 C 驱动器时也可能遇到防火墙问题。 此[堆栈溢出文章](http://stackoverflow.com/questions/42203488/settings-to-windows-firewall-to-allow-docker-for-windows-to-share-drive/43904051)很有帮助。
* 使用域凭据共享 C 驱动器时，对无法访问的域控制器所在的网络，共享可能停止（例如，家庭网络和公共 WiFi 等）。 有关详细信息，请参阅[此文章](https://blogs.msdn.microsoft.com/stevelasker/2016/06/14/configuring-docker-for-windows-volumes/)。



## <a name="mac-only"></a>仅限 Mac 
* Mac 上不支持文本群集转换。
* Mac 上不支持 RevoScalePy 库。

## <a name="azure-machine-learning-service-limits"></a>Azure 机器学习服务限制

- 允许的最大项目文件夹大小：25 MB
    >[!Note]
    >该限制不适用于 `.git`、`docs` 和 `outputs` 文件夹。 这些文件夹名称区分大小写。

- 允许的最大试验执行时间：7 天
- 运行后 `outputs` 文件夹中跟踪文件的最大大小：512 MB 

>[!NOTE]
>使用大文件时，请参阅[保存更改并处理大文件](how-to-read-write-files.md)。

## <a name="other-issues"></a>其他问题
如果了解其他未记录的问题，通过_发送笑脸/哭脸_向我们发送反馈。 




