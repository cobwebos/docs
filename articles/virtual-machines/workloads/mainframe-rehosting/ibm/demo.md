---
title: 在 IBM zD&T v1 中设置应用程序开发人员控制分发 （ADCD） |微软文档
description: 在 Azure 虚拟机 （VM） 上运行 IBM Z 开发和测试环境 （zD&T） 环境。
services: virtual-machines-linux
ms.service: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
ms.author: edprice
editor: edprice
ms.topic: conceptual
ms.date: 02/22/2019
tags: ''
keywords: ''
ms.openlocfilehash: 66f80c79219090c27da37dfc1d9149df5604961f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "68841387"
---
# <a name="set-up-an-application-developers-controlled-distribution-adcd-in-ibm-zdt-v1"></a>在 IBM zD&T v1 中设置应用程序开发人员控制分发 （ADCD）

您可以在 Azure 虚拟机 （VM） 上运行 IBM Z 开发和测试环境 （zD&T）。 此环境模拟 IBM Z 系列体系结构。 它可以承载各种 Z 系列操作系统或安装（也称为 Z 实例或包），这些操作系统或安装可通过称为 IBM 应用程序开发人员控制的分发 （ADCD） 的自定义捆绑包提供。

本文介绍如何在 Azure 上的 zD&T 环境中设置 ADCD 实例。 ADCd 为在 zD&T 中运行的开发和测试环境创建完整的 Z 系列操作系统实现。

与 zD&T 一样，ADC 只提供给 IBM 客户和合作伙伴，仅用于开发和测试目的。 它们不用于生产环境。 许多 IBM 安装软件包可通过[Passport 优势](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.guide.adcd.doc/topics/installation_ps.html)或[IBM 合作伙伴世界](https://www.ibm.com/partnerworld/public)下载。

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 如果还没有该订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- [zD&T 环境][ibm-install-z]以前在 Azure 上设置。 本文假定您使用的是之前创建的同一 Ubuntu 16.04 VM 映像。

- 通过 IBM 合作伙伴世界或护照优势访问 ADCD 媒体。

- [许可服务器](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html)。 这是运行 IBM zD&T 所必需的。 创建软件的方式取决于您从 IBM 许可软件的方式：

  - **基于硬件的许可服务器**需要一个 USB 硬件设备，该设备包含访问软件所有部分所需的 Rational 令牌。 您必须从 IBM 获取此数据。

  - **基于软件的许可服务器**要求您设置一个集中式服务器来管理许可密钥。 此方法是首选，要求您在管理服务器中设置从 IBM 接收的密钥。

## <a name="download-the-installation-packages-from-passport-advantage"></a>从 Passport 优势下载安装包

需要访问 ADCD 介质。 以下步骤假定您是 IBM 客户，可以使用 Passport 优势。 IBM 合作伙伴可以使用[IBM 合作伙伴世界](https://www.ibm.com/partnerworld/public)。

> [!NOTE]
> 本文假定 Windows PC 用于访问 Azure 门户和下载 IBM 媒体。 如果您使用的是 Mac 或 Ubuntu 桌面，则获取 IBM 媒体的命令和过程可能略有不同。

1. 登录[护照优势](https://www.ibm.com/software/howtobuy/passportadvantage/paocustomer)。

2. 选择**软件下载****和媒体访问**。

3. 选择 **"计划"产品/服务及协议编号**，然后单击"**继续**"。

4. 输入零件说明或零件号，然后单击 **"查找器**"。

5. 可选，单击按字母顺序列表按名称显示和查看产品。

6. 选择 **"操作系统"字段中****的所有操作系统**，在 **"语言"字段中**选择 **"所有语言**"。 然后，单击"**转到**"。

7. 单击 **"选择单个文件**"展开列表并显示要下载的各个媒体。

8. 验证要下载的包，选择 **"下载**"，然后将文件下载到所需的目录。

## <a name="upload-the-adcd-packages"></a>上传 ADCD 包

现在，您已经拥有了包，您必须将它们上载到 Azure 上的 VM。

1. 在 Azure 门户中，使用创建的 Ubuntu VM 启动**ssh**会话。 转到 VM，选择 **"概述"** 边栏选项卡，然后选择"**连接**"。

2. 选择**SSH**选项卡，然后将 ssh 命令复制到剪贴板。

3. 使用凭据和选择的[SSH 客户端](/azure/virtual-machines/linux/use-remote-desktop)登录到 VM。 本演示使用 Windows 10 的 Linux 扩展，该扩展将 bash shell 添加到 Windows 命令提示符。 普蒂也工作得很好。

4. 登录后，创建一个目录以上载 IBM 包。 请记住，Linux 对大小写很敏感。 例如，本演示假定包上载到：

        /home/MyUserID/ZDT/adcd/nov2017/volumes

5. 使用 SSH 客户端（如[WinSCP）](https://winscp.net/eng/index.php)上载文件。 由于 SCP 是 SSH 的一部分，它使用端口 22，这是 SSH 使用的端口。 如果本地计算机不是 Windows，则可以在 SSH 会话中键入[scp 命令](http://man7.org/linux/man-pages/man1/scp.1.html)。

6. 启动上载到您创建的 Azure VM 目录，该目录将成为 zD&T 的图像存储。

    > [!NOTE]
    > 确保**ADCDTOOLS。XML**包含在上载到**家庭/MyUserID/ZDT/adcd/nov2017**目录中。 稍后需要用到此值。

7. 等待文件上载，这可能需要一些时间，具体取决于您与 Azure 的连接。

8. 上载完成后，导航到卷目录并解压缩所有**gz**卷：

    ```
        gunzip \*.gz
    ```
    
![显示解压缩 gz 卷的文件资源管理器](media/01-gunzip.png)

## <a name="configure-the-image-storage"></a>配置映像存储

下一步是将 zD&T 配置为使用上载的包。 zD&T 中的图像存储过程允许您装载和使用图像。 它可以使用 SSH 或 FTP。

1. 启动**zDTServer**。 为此，您必须位于根级别。 按顺序输入以下两个命令：
    ```
        sudo su -
        /opt/ibm/zDT/bin/startServer
    ```
2. 请注意命令的 URL 输出，并使用此 URL 访问 Web 服务器。 它类似于：
     > https：//（您的 VM 名称或 IP 地址）：9443/ZDTMC/index.html
     >
     > 请记住，您的 Web 访问使用端口 9443。 使用此选项可登录到 Web 服务器。 ZD&T 的用户 ID 为**zdtadmin，** 密码为**密码**。

    ![IBM zD&T 企业版欢迎屏幕](media/02-welcome.png)

3. 在 **"快速入门"** 页上，在 **"配置**"下，选择**映像存储**。

     ![IBM zD&T 企业版快速入门屏幕](media/03-quickstart.png)

4. 在 **"配置映像存储**"页上，选择**SSH 文件传输协议**。

5. 对于**主机名**，键入**本地主机**并输入上载图像的目录路径。 例如，/home/MyUserID/ZDT/adcd/nov2017/卷。

6. 输入 VM**的用户 ID**和**密码**。 请勿使用 ZD&T 用户 ID 和密码。

7. 测试连接以确保您具有访问权限，然后选择 **"保存"** 以保存配置。

## <a name="configure-the-target-environments"></a>配置目标环境

下一步是配置 zD&T 目标环境。 此模拟托管环境是映像运行的位置。

1. 在 **"快速入门"** 页上，在 **"配置**"下，选择 **"目标环境**"。

2. 在 **"配置目标环境**"页上，选择 **"添加目标**"。

3. 选择**Linux**。 IBM 支持两种类型的环境，Linux 和云（OpenStack），但此演示在 Linux 上运行。

4. 在 **"添加目标环境**"页上，对于**主机名**，请输入**本地主机**。 将**SSH 端口**设置为**22**。

5. 在 **"目标环境"标签**框中，输入 **"MyCICS"** 等标签。

     ![添加目标环境屏幕](media/04-add-target.png)

## <a name="configure-adcd-and-deploy"></a>配置 ADCD 并部署

完成前面的配置步骤后，必须配置 zD&T 才能使用包和目标环境。 同样，在 zd&T 中使用映像存储过程，这允许您装载和使用图像。 它可以使用 SSH 或 FTP。

1. 在 **"快速入门"** 页上，在 **"配置**"下，选择**ADCD**。 将显示一组说明，告诉您需要在装载 ADCD 包之前完成的步骤。 这解释了为什么我们以前面的方式命名目标目录。

2. 假设所有图像都上载到正确的目录，请单击右下角显示的**ADCD**链接的 IMAGE（如下图所示的步骤 7 所示）。

     ![IBM zD&T 企业版 - 配置 ADCD 屏幕](media/05-adcd.png)

## <a name="create-the-image"></a>创建映像

完成上一个配置步骤后，将显示 **"使用 ADCD 组件创建映像"** 页。

1. 选择卷（本例中为 2017 年 11 月）以显示该卷中的不同包。

2. 在本演示中，选择**客户信息控制系统 （CICS） - 5.3**。

3. 在 **"图像名称"** 框中，键入图像的名称，如**MyCICS 图像**。

4. 选择右下角的 **"创建图像**"按钮。

     ![IBM zD&T 企业版 - 使用 ADCD 组件屏幕创建图像](media/06-adcd.png)

5. 在显示的窗口中，告诉您映像已成功部署，请选择 **"部署映像**"。

6. 在 **"将图像部署到目标环境**"页上，选择您在上一页 **（MyCICS 映像**）上创建的图像和之前创建的目标环境 **（MyCICS**）。

7. 在下一个屏幕上，为 VM 提供凭据（即，不是 ztadmin 凭据）。

8. 在"属性"窗格中，输入**中央处理器 （CP）** 的数量、**系统内存量 （GB）** 和正在运行的映像**的部署目录**。 由于这是一个演示，保持它小。

9. 确保选中该框，以便**部署后自动将 IPL 命令发出到 z/OS。**

     ![属性屏幕](media/07-properties.png)

10. 选择 **"完成**"。

11. 选择 **"将映像**从 **"部署到目标环境**"页。

映像现在可以部署，并且可以由 3270 终端仿真器安装。

> [!NOTE]
> 如果收到错误说磁盘空间不足，请注意该区域需要 151 Gb。

祝贺你！ 现在，您正在 Azure 上运行 IBM 大型机环境。

## <a name="learn-more"></a>了解详细信息

- [大型机迁移：神话和事实](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [Azure 上的 IBM DB2 pureScale](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure)
- [疑难解答](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [将大型机与 Azure 迁移解密](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-on-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
