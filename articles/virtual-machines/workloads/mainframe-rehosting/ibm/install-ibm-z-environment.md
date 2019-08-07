---
title: '& Azure 上的开发/测试环境安装 IBM zD |Microsoft Docs'
description: 在 Azure 虚拟机 (VM) 基础结构即服务 (IaaS) 上部署 IBM Z 开发和测试环境 (zD & T)。
services: virtual-machines-linux
ms.service: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 04/02/2019
tags: ''
keywords: ''
ms.openlocfilehash: 7ba3323f0811f3f9b76d73796264bf17712a1179
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68841341"
---
# <a name="install-ibm-zdt-devtest-environment-on-azure"></a>& Azure 上的开发/测试环境安装 IBM zD

若要为 IBM Z 系统上的大型机工作负荷创建开发/测试环境, 可以在 Azure 虚拟机 (VM) 基础结构即服务 (IaaS) 上部署 IBM Z 开发和测试环境 (zD & T)。

使用 zD & T, 你可以利用 x86 平台的成本节约来实现不太关键的开发和测试环境, 然后将更新推送回 Z 系统生产环境中。 有关详细信息, 请参阅[IBM ZD & 安装说明](https://www-01.ibm.com/support/docview.wss?uid=swg24044565#INSTALL)。

Azure 和 Azure Stack 支持以下版本:

- zD & 个人版
- zD & T Parallel Sysplex
- zD & T Enterprise Edition

所有版本的 zD & 只能在 x86 Linux 系统上运行, 而不是在 Windows Server 上运行。 Red Hat Enterprise Linux (RHEL) 或 Ubuntu/Debian 支持 Enterprise Edition。 RHEL 和 Debian VM 映像都适用于 Azure。

本文介绍如何在 Azure 上设置 zD & T Enterprise Edition, 以便可以使用 zD & T Enterprise Edition web 服务器来创建和管理环境。 安装 zD & T 不会安装任何环境。 您必须将它们分别作为安装包创建。 例如, 应用程序开发人员控制的分布 (ADCD) 是测试环境的卷映像。 它们包含在 IBM 提供的媒体分发上的 zip 映像中。 请参阅如何[在 Azure 上设置 ADCD 环境](demo.md)。

有关详细信息, 请参阅 IBM 知识中心中的[zD & T 概述](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zdt.overview.gs.doc/topics/c_product_overview.html)。

本文介绍如何在 Azure 上设置 Z 开发和测试环境 (zD & T) 企业版。 然后, 可以使用 zD & T Enterprise Edition web 服务器在 Azure 上创建和管理基于 Z 的环境。

## <a name="prerequisites"></a>先决条件

> [!NOTE]
> IBM 只允许在开发/测试环境中安装 zD & T Enterprise Edition,*而不*是在生产环境中安装。

- Azure 订阅。 如果还没有该订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- 你需要访问媒体, 该媒体仅适用于 IBM 客户和合作伙伴。 有关详细信息, 请联系 IBM 代表或查看[zD & T](https://www.ibm.com/us-en/marketplace/z-systems-development-test-environment)网站上的联系信息。

- [授权服务器](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html)。 这对于访问环境是必需的。 创建该方法的方式取决于从 IBM 为软件授权的方式:

     - **基于硬件的许可服务器**需要一个 USB 硬件设备, 其中包含访问软件的所有部分所必需的有理数。 必须从 IBM 获取此。

     - **基于软件的许可服务器**要求你设置集中式服务器来管理许可密钥。 此方法是首选方法, 要求在管理服务器中设置从 IBM 接收的密钥。

## <a name="create-the-base-image-and-connect"></a>创建基本映像并连接

1. 在 Azure 门户中, 使用所需的操作系统配置[创建 VM](/azure/virtual-machines/linux/quick-create-portal) 。 本文假设运行 Ubuntu 16.04 的 B4ms VM (具有4个 vcpu 和 16 GB 内存)。

2. 创建 VM 后, 打开用于 SSH 的入站端口 22, 为 FTP 打开 FTP, 为网络服务器打开9443。

3. 通过 "**连接**" 按钮获取 VM 的 "**概述**" 边栏选项卡上显示的 SSH 凭据。 选择 " **ssh** " 选项卡, 并将 SSH 登录命令复制到剪贴板。

4. 从本地 PC 登录到[Bash shell](/azure/cloud-shell/quickstart)并粘贴命令。 它采用**ssh\<用户 id\>\@IP地址\>格式。\<** 系统提示输入凭据时, 请输入凭据以建立与主目录的连接。

## <a name="copy-the-installation-file-to-the-server"></a>将安装文件复制到服务器

Web 服务器的安装文件为**ZDT\_Install\_\_12.0.0.1. tgz**。 它包含在 IBM 提供的媒体中。 必须将此文件上传到 Ubuntu VM。

1. 在命令行中输入以下命令, 确保新创建的映像中的所有内容都是最新的:

    ```
    sudo apt-get update
    ```

2. 创建要安装到的目录:

    ```
    mkdir ZDT
    ```

3. 将文件从本地计算机复制到 VM:

    ```
    scp ZDT_Install_EE_V12.0.0.1.tgz  your_userid@<IP Address /ZDT>   =>
    ```
    
> [!NOTE]
> 此命令将安装文件复制到主目录中的 ZDT 目录中, 这取决于客户端运行的是 Windows 还是 Linux。

## <a name="install-the-enterprise-edition"></a>安装 Enterprise Edition

1. 使用以下命令, 中转到 ZDT 目录并\_解压缩\_ZDT\_Install EE 12.0.0.1 tgz 文件:

    ```
    cd ZDT
    chmod 755 ZDT\_Install\_EE\_V12.0.0.0.tgz
    ```

2. 运行该安装程序：

    ```
    ./ZDT_Install_EE_V12.0.0.0.x86_64
    ```

3. 选择 " **1** " 以安装企业服务器。

4. 按**enter**并仔细阅读许可协议。 在许可证结束时, 输入 **"是"** 以继续。

5. 当系统提示更改新创建的用户**ibmsys1**的密码时, 请使用命令**sudo passwd ibmsys1** , 并输入新密码。

6. 若要验证安装是否成功, 请输入

    ```
    dpkg -l | grep zdtapp
    ```

7. 验证输出是否包含字符串**zdtapp 12.0.0.0**, 指出已成功安装包气体

### <a name="starting-enterprise-edition"></a>正在启动 Enterprise Edition

请记住, 当 web 服务器启动时, 它将在安装过程中创建的 zD & T 用户 ID 下运行。

1. 若要启动 web 服务器, 请使用 root User ID 运行以下命令:

    ```
    sudo /opt/ibm/zDT/bin/startServer
    ```

2. 复制脚本输出的 URL, 如下所示:

    ```
    https://<your IP address or domain name>:9443/ZDTMC/login.htm
    ```

3. 将 URL 粘贴到 web 浏览器中, 打开 zD & T 安装的管理组件。

## <a name="next-steps"></a>后续步骤

[在 IBM zD & T v1 中设置应用程序开发人员受控分发 (ADCD)](./demo.md)
