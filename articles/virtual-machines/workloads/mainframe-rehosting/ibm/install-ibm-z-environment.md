---
title: 在 Azure 上安装 IBM zD & T 开发/测试环境 |Microsoft Docs
description: 即服务 (IaaS) 在 Azure 虚拟机 (VM) 基础结构上部署 IBM Z 开发和测试环境 (zD & T)。
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 04/02/2019
tags: ''
keywords: ''
ms.openlocfilehash: ad02ec75dab4cb6971d0467899d80f5f745fd94b
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621295"
---
# <a name="install-ibm-zdt-devtest-environment-on-azure"></a>在 Azure 上安装 IBM zD & T 开发/测试环境

若要创建 IBM Z 系统的大型机工作负荷的开发/测试环境，可以部署 IBM Z 开发和测试环境 (zD & T) 在 Azure 虚拟机 (VM) 基础结构即服务 (IaaS)。

与 zD （& T），您可以充分利用在成本方面 x86 平台，用于你不太重要的开发和测试环境，然后推送更新回 Z System 生产环境。 有关详细信息，请参阅[IBM ZD （& T） 的安装说明](https://www-01.ibm.com/support/docview.wss?uid=swg24044565#INSTALL)。

Azure 和 Azure Stack 支持以下版本：

- zD & T Personal Edition
- zD & T 并行 Sysplex
- zD & T Enterprise Edition

ZD （& T） 的所有版本仅在 x86 上都运行 Linux 系统，不是 Windows Server。 Enterprise Edition 支持 Ubuntu/Debian 或 Red Hat Enterprise Linux (RHEL) 上。 RHEL 和 Debian VM 映像是适用于 Azure。

本文介绍如何设置 zD & T 在 Azure 上的 Enterprise Edition，因此可以用于创建和管理的环境中使用 zD & T Enterprise Edition web 服务器。 安装 zD （& T） 不会安装任何环境。 您必须创建单独为安装包。 例如，应用程序开发人员控制分发版 (ADCD) 是测试环境的卷映像。 它们包含在可从 IBM 的媒体分发上的 zip 映像。 请参阅如何[建立在 Azure 上的 ADCD 环境](demo.md)。

有关详细信息，请参阅[zD & T 概述](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zdt.overview.gs.doc/topics/c_product_overview.html)IBM 知识中心中。

本文介绍如何设置 Z 开发和测试环境 (zD & T) 在 Azure 上的 Enterprise Edition。 然后可以使用 zD & T Enterprise Edition web 服务器来创建和管理在 Azure 上基于 Z 的环境。

## <a name="prerequisites"></a>先决条件

> [!NOTE]
> IBM 允许 zD 和 T Enterprise Edition 在仅限开发/测试环境中安装 —*不*生产环境。

- Azure 订阅。 如果还没有该订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- 需要对媒体，仅向 IBM 客户和合作伙伴提供的访问。 有关详细信息，请联系 IBM 代表或上看到的联系信息[zD & T](https://www.ibm.com/us-en/marketplace/z-systems-development-test-environment)网站。

- 一个[授权服务器](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html)。 这是必需的环境的访问权限。 创建它的方式取决于你如何获取来自 IBM 软件的许可：

     - **基于硬件的授权服务器**需要包含合理的令牌访问所有部分软件所需的 USB 硬件设备。 必须从 IBM 获取。

     - **基于软件的授权服务器**要求您设置授权密钥管理的一台中央服务器。 此方法是首选，并要求你设置的管理服务器中接收来自 IBM 的密钥。

## <a name="create-the-base-image-and-connect"></a>创建基本映像并将连接

1. 在 Azure 门户中，[创建 VM](/azure/virtual-machines/linux/quick-create-portal)与所需的操作系统配置。 本文假定 （具有 4 个 Vcpu 和 16 GB 内存） 的 B4ms VM 运行 Ubuntu 16.04。

2. 创建 VM 后，打开 22 SSH、 21 的 FTP 和 web 服务器的 9443 入站的端口。

3. 获取上显示的 SSH 凭据**概述**通过 VM 的边栏选项卡**Connect**按钮。 选择**SSH**选项卡上，并将 SSH 登录命令复制到剪贴板。

4. 登录到[Bash shell](/azure/cloud-shell/quickstart)从本地 PC 和粘贴命令。 它将在窗体**ssh\<用户 id\>\@\<IP 地址\>** 。 当系统提示输入凭据，请输入它们来连接到的主目录。

## <a name="copy-the-installation-file-to-the-server"></a>将安装文件复制到服务器

Web 服务器的安装文件是**ZDT\_安装\_EE\_V12.0.0.1.tgz**。 它包含在由 IBM 提供的媒体。 必须将此文件上传到 Ubuntu VM。

1. 从命令行中，输入以下命令以确保所有内容都是最新中新创建的映像：

    ```
    sudo apt-get update
    ```

2. 创建要安装到的目录：

    ```
    mkdir ZDT
    ```

3. 将文件从本地计算机复制到 VM:

    ```
    scp ZDT_Install_EE_V12.0.0.1.tgz  your_userid@<IP Address /ZDT>   =>
    ```
    
> [!NOTE]
> 此命令将安装文件复制到主目录，而有所差异，具体取决于客户端运行 Windows 或 Linux 中的 ZDT 目录。

## <a name="install-the-enterprise-edition"></a>安装 Enterprise Edition

1. 转到 ZDT 目录和解压缩 ZDT\_安装\_EE\_V12.0.0.1.tgz 文件使用以下命令：

    ```
    cd ZDT
    chmod 755 ZDT\_Install\_EE\_V12.0.0.0.tgz
    ```

2. 运行该安装程序：

    ```
    ./ZDT_Install_EE_V12.0.0.0.x86_64
    ```

3. 选择**1** 安装企业服务器。

4. 按**Enter**和仔细阅读许可协议。 在许可证结束时，输入**是**以继续。

5. 当系统提示你更改新创建的用户的密码**ibmsys1**，使用命令**sudo passwd ibmsys1**并输入新密码。

6. 若要验证安装是否成功输入

    ```
    dpkg -l | grep zdtapp
    ```

7. 验证输出中包含字符串**zdtapp 12.0.0.0**，以指示已成功安装包而言，汽油的

### <a name="starting-enterprise-edition"></a>从企业版

请记住，web 服务器启动时，它在运行安装过程中创建的 zD （& T) 用户 ID 下。

1. 若要启动 web 服务器，请使用根用户 ID 运行以下命令：

    ```
    sudo /opt/ibm/zDT/bin/startServer
    ```

2. 复制脚本，如下所示 URL 输出：

    ```
    https://<your IP address or domain name>:9443/ZDTMC/login.htm
    ```

3. 将 URL 粘贴到 web 浏览器中，打开 zD & T 安装管理组件。

## <a name="next-steps"></a>后续步骤

[设置了应用程序开发人员控制分发 (ADCD) 在 IBM zD & T v1](./demo.md)
