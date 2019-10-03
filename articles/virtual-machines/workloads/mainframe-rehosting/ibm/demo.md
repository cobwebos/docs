---
title: 在 IBM zD & T v1 中设置应用程序开发人员受控分发 (ADCD) |Microsoft Docs
description: 在 Azure 虚拟机 (Vm) 上运行 IBM Z 开发和测试环境 (zD & T) 环境。
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
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68841387"
---
# <a name="set-up-an-application-developers-controlled-distribution-adcd-in-ibm-zdt-v1"></a>在 IBM zD & T v1 中设置应用程序开发人员受控分发 (ADCD)

可以在 Azure 虚拟机 (Vm) 上运行 IBM Z 开发和测试环境 (zD & T) 环境。 此环境模拟 IBM Z 系列体系结构。 它可以承载各种 Z 系列操作系统或安装 (也称为 Z 实例或包), 它们可通过称为 IBM 应用程序开发人员控制分发 (ADCDs) 的自定义捆绑包获得。

本文介绍如何在 Azure 上的 zD & T 环境中设置 ADCD 实例。 ADCDs 为在 zD & T 中运行的开发和测试环境创建完整的 Z 系列操作系统实现。

与 zD & T 一样, ADCDs 仅适用于 IBM 客户和合作伙伴, 专用于开发和测试目的。 它们不用于生产环境。 许多 IBM 安装包均可通过[Passport 优势](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.guide.adcd.doc/topics/installation_ps.html)或[IBM PartnerWorld](https://www.ibm.com/partnerworld/public)下载。

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 如果还没有该订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- 之前在 Azure 上设置的[zD & T 环境][ibm-install-z]。 本文假设你使用的是前面创建的相同 Ubuntu 16.04 VM 映像。

- 通过 IBM PartnerWorld 或 Passport 优势访问 ADCD 媒体。

- [授权服务器](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html)。 这对于运行 IBM zD & T 是必需的。 创建该方法的方式取决于从 IBM 为软件授权的方式:

  - **基于硬件的许可服务器**需要一个 USB 硬件设备, 其中包含访问软件的所有部分所必需的有理数。 必须从 IBM 获取此。

  - **基于软件的许可服务器**要求你设置集中式服务器来管理许可密钥。 此方法是首选方法, 要求在管理服务器中设置从 IBM 接收的密钥。

## <a name="download-the-installation-packages-from-passport-advantage"></a>从 Passport 优势下载安装包

需要访问 ADCD 介质。 以下步骤假定你是 IBM 客户, 并可以使用 Passport 优势。 IBM 合作伙伴可以使用[Ibm PartnerWorld](https://www.ibm.com/partnerworld/public)。

> [!NOTE]
> 本文假设使用 Windows 电脑访问 Azure 门户和下载 IBM 媒体。 如果使用的是 Mac 或 Ubuntu 桌面, 用于获取 IBM 媒体的命令和过程可能会略有不同。

1. 登录到[Passport 优势](https://www.ibm.com/software/howtobuy/passportadvantage/paocustomer)。

2. 选择**软件下载**和**媒体访问**。

3. 选择 "**程序产品和协议编号**", 然后单击 "**继续**"。

4. 输入部件说明或部件号, 然后单击 "**查找**器"。

5. 也可以单击 "按字母顺序排序" 列表, 按名称显示和查看产品。

6. 在 "**操作系统" 字段**中选择 "**所有操作系统**", 并在 "**语言" 字段**中选择**所有语言**。 然后单击 "**开始**"。

7. 单击 "**选择单个文件**" 以展开列表, 并显示要下载的单个媒体。

8. 验证要下载的包, 选择 "**下载**", 然后将文件下载到所需的目录。

## <a name="upload-the-adcd-packages"></a>上传 ADCD 包

现在, 你已有了包, 你必须将其上传到 Azure 中的 VM。

1. 在 Azure 门户中, 使用你创建的 Ubuntu VM 启动**ssh**会话。 前往 VM, 选择 "**概述**" 边栏选项卡, 然后选择 "**连接**"。

2. 选择 " **ssh** " 选项卡, 然后将 SSH 命令复制到剪贴板。

3. 使用你的凭据和所选的[SSH 客户端](/azure/virtual-machines/linux/use-remote-desktop)登录到你的 VM。 此演示使用适用于 Windows 10 的 Linux 扩展, 将 bash shell 添加到 Windows 命令提示符。 PuTTY 也是如此。

4. 登录时, 创建一个目录来上传 IBM 包。 请记住, Linux 区分大小写。 例如, 此演示假定将包上传到:

        /home/MyUserID/ZDT/adcd/nov2017/volumes

5. 使用 SSH 客户端 (如[WinSCP](https://winscp.net/eng/index.php)) 上传文件。 由于 SCP 是 SSH 的一部分, 因此它使用 SSH 使用的端口22。 如果本地计算机不是 Windows, 则可以在 SSH 会话中键入[scp 命令](http://man7.org/linux/man-pages/man1/scp.1.html)。

6. 开始上传到所创建的 Azure VM 目录, 该目录将成为 zD & T 的映像存储。

    > [!NOTE]
    > 请确保**ADCDTOOLS。XML**包含在上传到**Home/MYUSERID/ZDT/adcd/nov2017**目录中。 稍后需要用到此值。

7. 等待文件上传, 这可能需要一些时间, 具体取决于与 Azure 的连接。

8. 上载完成后, 导航到 "卷" 目录并解压缩所有**gz**卷:

    ```
        gunzip \*.gz
    ```
    
![显示解压缩的 gz 卷的文件资源管理器](media/01-gunzip.png)

## <a name="configure-the-image-storage"></a>配置映像存储

下一步是将 & zD 配置为使用上传的包。 ZD 中的映像存储进程 & T 允许装入和使用映像。 它可以使用 SSH 或 FTP。

1. 启动**zDTServer**。 为此, 必须在根级别。 按顺序输入以下两个命令:
    ```
        sudo su -
        /opt/ibm/zDT/bin/startServer
    ```
2. 记下命令的 URL 输出, 并使用此 URL 来访问 web 服务器。 如下所示:
     > https://(你的 VM 名称或 IP 地址): 9443/ZDTMC/index
     >
     > 请记住, web 访问使用端口9443。 使用此登录到 web 服务器。 ZD & T 的用户 ID 是**zdtadmin** , 密码为**密码**。

    ![IBM zD & T Enterprise Edition 欢迎屏幕](media/02-welcome.png)

3. 在**快速入门**"页上的"**配置**"下, 选择"**映像存储**"。

     ![IBM zD & T Enterprise Edition 快速入门屏幕](media/03-quickstart.png)

4. 在 "**配置映像存储**" 页上, 选择 " **SSH 文件传输协议**"。

5. 对于 "**主机名**", 键入**Localhost** , 并输入你将映像上传到的目录路径。 例如,/home/MyUserID/ZDT/adcd/nov2017/volumes。

6. 输入 VM 的**用户 ID**和**密码**。 请勿使用 ZD & T 用户 ID 和密码。

7. 测试连接以确保具有访问权限, 然后选择 "**保存**" 以保存配置。

## <a name="configure-the-target-environments"></a>配置目标环境

下一步是配置 zD & T 目标环境。 此仿真托管环境是指运行映像的位置。

1. 在 "**快速入门**" 页上的 "**配置**" 下, 选择 "**目标环境**"。

2. 在 "**配置目标环境**" 页上, 选择 "**添加目标**"。

3. 选择 " **Linux**"。 IBM 支持两种类型的环境: Linux 和云 (OpenStack), 但此演示在 Linux 上运行。

4. 在 "**添加目标环境**" 页上, 为 "**主机名**" 输入**localhost**。 将**SSH 端口**设置为**22**。

5. 在 "**目标环境标签**" 框中, 输入一个标签, 例如 " **MyCICS"。**

     ![添加目标环境屏幕](media/04-add-target.png)

## <a name="configure-adcd-and-deploy"></a>配置 ADCD 并部署

完成前面的配置步骤后, 你必须将 & zD 配置为使用包和目标环境。 同样, 使用 zD & T 中的映像存储过程, 这样可以装载和使用映像。 它可以使用 SSH 或 FTP。

1. 在**快速入门**"页上的"**配置**"下, 选择**ADCD**。 将显示一组说明, 告诉你需要完成的步骤, 然后才能装载 ADCD 包。 这说明了我们以前的目标目录的命名方式。

2. 假设所有映像都已上传到正确的目录, 请单击右下角显示的 ADCD 链接中的**图像**(如以下屏幕截图中的步骤7所示)。

     ![IBM zD & T 企业版-配置 ADCD 屏幕](media/05-adcd.png)

## <a name="create-the-image"></a>创建映像

上一配置步骤完成后, 将显示 "**使用 ADCD 组件创建映像**" 页。

1. 选择卷 (在本例中为2017年11月) 以显示该卷中的不同包。

2. 对于此演示, 请选择 "**客户信息控制系统 (CICS)-5.3**"。

3. 在 "**映像名称**" 框中, 键入映像的名称, 如**MyCICS image**。

4. 选择右下角的 "**创建映像**" 按钮。

     ![IBM zD & T 企业版-使用 ADCD 组件创建映像屏幕](media/06-adcd.png)

5. 在出现的窗口中, 告诉你映像已成功部署, 请选择 "**部署映像**"。

6. 在 "将**映像部署到目标环境**" 页上, 选择你在前一页 (**MyCICS**) 上创建的映像和前面创建的目标环境 (**MyCICS**)。

7. 在下一个屏幕上, 提供 VM 的凭据 (即, 不是 ztadmin 凭据)。

8. 在 "属性" 窗格中, 输入**中央处理器 (CPs)** 的数量、**系统内存量 (GB)** 和正在运行的映像的**部署目录**。 由于这是一个演示, 因此请将其保持为小。

9. 请确保已选中此框, 以便**在部署后自动向 z/OS 发出 IPL 命令**。

     ![属性屏幕](media/07-properties.png)

10. 选择 "**完成**"。

11. 从 "将**映像部署到目标环境**" 页中选择 "**部署映像**"。

映像现在可以部署并准备就绪, 可由3270终端模拟器装载。

> [!NOTE]
> 如果收到一条错误消息, 指出没有足够的磁盘空间, 请注意, 区域需要 151 Gb。

祝贺你！ 你现在正在 Azure 上运行 IBM 主机环境。

## <a name="learn-more"></a>了解详情

- [大型机迁移: 误解和事实](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [Azure 上的 IBM DB2 pureScale](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure)
- [故障排除](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [揭密大型机到 Azure 的迁移](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-on-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
