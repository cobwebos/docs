---
title: 在 Azure 上安装 IBM zD&T 开发/测试环境 |微软文档
description: 在 Azure 虚拟机 （VM） 基础结构上部署 IBM Z 开发和测试环境 （zD&T） 作为服务 （IaaS）。
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
ms.openlocfilehash: 67af4eae03b773fad9cf38964152c1fb9d623bd5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "72025934"
---
# <a name="install-ibm-zdt-devtest-environment-on-azure"></a>在 Azure 上安装 IBM zD&T 开发/测试环境

要为 IBM Z Systems 上的主机工作负载创建开发/测试环境，可以在 Azure 虚拟机 （VM） 基础结构（IaaS） 上部署 IBM Z 开发和测试环境 （zD&T）。

借助 zD&T，您可以利用 x86 平台的成本节约，为您的不太关键的开发和测试环境，然后将更新推回 Z 系统生产环境。 有关详细信息，请参阅 IBM [ZD&T 安装说明](https://www-01.ibm.com/support/docview.wss?uid=swg24044565#INSTALL)。

Azure 和 Azure 堆栈支持以下版本：

- zD&T 个人版
- zD&T 并行系统
- zD&T 企业版

zD&T 的所有版本仅在 x86 Linux 系统上运行，而不是 Windows 服务器运行。 红帽企业版 （RHEL） 或 Ubuntu/Debian 都支持企业版。 RHEL 和 Debian VM 映像都可用于 Azure。

本文介绍如何在 Azure 上设置 zD&T 企业版，以便可以使用 zD&T 企业版 Web 服务器创建和管理环境。 安装 zD&T 不会安装任何环境。 您必须单独创建这些安装包。 例如，应用程序开发人员控制的分发 （ADCD） 是测试环境的卷映像。 它们包含在 IBM 提供的媒体分发上的 zip 图像中。 了解如何在[Azure 上设置 ADCD 环境](demo.md)。

有关详细信息，请参阅 IBM 知识中心中的[zD&T 概述](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zdt.overview.gs.doc/topics/c_product_overview.html)。

本文介绍如何在 Azure 上设置 Z 开发和测试环境 （zD&T） 企业版。 然后，可以使用 zD&T 企业版 Web 服务器在 Azure 上创建和管理基于 Z 的环境。

## <a name="prerequisites"></a>先决条件

> [!NOTE]
> IBM 允许仅在开发/测试环境中安装 zD&T 企业版，*而不是*生产环境。

- Azure 订阅。 如果还没有该订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- 您需要访问媒体，媒体仅对 IBM 客户和合作伙伴可用。 有关详细信息，请联系您的 IBM 代表或查看[zD&T](https://www.ibm.com/us-en/marketplace/z-systems-development-test-environment)网站上的联系信息。

- [许可服务器](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html)。 这是访问环境所必需的。 创建软件的方式取决于您从 IBM 许可软件的方式：

     - **基于硬件的许可服务器**需要一个 USB 硬件设备，该设备包含访问软件所有部分所需的 Rational 令牌。 您必须从 IBM 获取此数据。

     - **基于软件的许可服务器**要求您设置一个集中式服务器来管理许可密钥。 此方法是首选，要求您在管理服务器中设置从 IBM 接收的密钥。

## <a name="create-the-base-image-and-connect"></a>创建基本映像并连接

1. 在 Azure 门户中，使用所需的操作系统配置[创建 VM。](/azure/virtual-machines/linux/quick-create-portal) 本文假定运行 Ubuntu 16.04 的 B4ms VM（具有 4 个 vCPU 和 16 GB 内存）。

2. 创建 VM 后，打开 SSH 的入站端口 22，为 FTP 打开 21 端口，为 Web 服务器打开 9443。

3. 通过 **"连接**"按钮获取 VM**概述**边栏选项卡上显示的 SSH 凭据。 选择**SSH**选项卡并将 SSH 登录命令复制到剪贴板。

4. 从本地 PC 登录到[Bash 外壳](/azure/cloud-shell/quickstart)并粘贴该命令。 它将在**ssh\<\>\@\<用户 ID IP\>地址**的窗体中。 当提示您输入凭据时，请输入它们以建立与主目录的连接。

## <a name="copy-the-installation-file-to-the-server"></a>将安装文件复制到服务器

Web 服务器的安装文件是**ZDT\_\_安装\_EE V12.0.0.1.tgz**。 它包含在 IBM 提供的介质中。 您必须将此文件上载到 Ubuntu VM。

1. 从命令行中，输入以下命令以确保新创建的映像中的所有内容都是最新的：

    ```
    sudo apt-get update
    ```

2. 创建要安装到的目录：

    ```
    mkdir ZDT
    ```

3. 将文件从本地计算机复制到 VM：

    ```
    scp ZDT_Install_EE_V12.0.0.1.tgz  your_userid@<IP Address /ZDT>   =>
    ```
    
> [!NOTE]
> 此命令将安装文件复制到主目录中的 ZDT 目录，该目录因客户端是否运行 Windows 或 Linux 而异。

## <a name="install-the-enterprise-edition"></a>安装企业版

1. 转到 ZDT 目录并使用以下命令解压缩 ZDT\_安装\_EE\_V12.0.0.1.tgz 文件：

    ```
    cd ZDT
    chmod 755 ZDT\_Install\_EE\_V12.0.0.0.tgz
    ```

2. 运行安装程序：

    ```
    ./ZDT_Install_EE_V12.0.0.0.x86_64
    ```

3. 选择**1**可安装企业服务器。

4. 按**Enter**并仔细阅读许可协议。 在许可证结束时，输入 **"是**"以继续。

5. 当提示更改新创建用户的密码**ibmsys1**时，请使用命令**sudo passwd ibmsys1**并输入新密码。

6. 验证安装是否成功输入

    ```
    dpkg -l | grep zdtapp
    ```

7. 验证输出是否包含字符串**zdtapp 12.0.0.0，** 指示已成功安装封装气体

### <a name="starting-enterprise-edition"></a>启动企业版

请记住，当 Web 服务器启动时，它在安装过程中创建的 zD&T 用户 ID 下运行。

1. 要启动 Web 服务器，请使用根用户 ID 运行以下命令：

    ```
    sudo /opt/ibm/zDT/bin/startServer
    ```

2. 复制脚本的 URL 输出，如下所示：

    ```
    https://<your IP address or domain name>:9443/ZDTMC/login.htm
    ```

3. 将 URL 粘贴到 Web 浏览器中，以打开 zD&T 安装的管理组件。

## <a name="next-steps"></a>后续步骤

[在 IBM zD&T v1 中设置应用程序开发人员控制分发 （ADCD）](./demo.md)
