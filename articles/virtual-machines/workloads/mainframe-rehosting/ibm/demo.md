---
title: 设置了应用程序开发人员控制分发 (ADCD) 在 IBM zD & T v1 |Microsoft Docs
description: 在 Azure 虚拟机 (Vm) 运行 IBM Z 开发和测试环境 (zD & T) 环境。
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
keywords: ''
ms.openlocfilehash: d527b08f3610531bef8e98a11998942411651d27
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621353"
---
# <a name="set-up-an-application-developers-controlled-distribution-adcd-in-ibm-zdt-v1"></a>设置了应用程序开发人员控制分发 (ADCD) 在 IBM zD & T v1

您可以运行一个 IBM Z 开发和测试环境 (zD & T) 环境在 Azure 虚拟机 (Vm)。 此环境来模拟 IBM Z 系列体系结构。 它可以承载各种 Z 系列操作系统或安装 （也称为 Z 实例或包），这可通过调用 IBM 应用程序开发人员控制分布区 (ADCDs) 的自定义捆绑包。

本文介绍如何设置 ADCD 实例的 zD T 在 Azure 上的环境中。 ADCDs 创建开发和测试环境中 zD 和 t。 运行完整 Z 系列操作系统实现

ZD （& T），ADCDs 仅适用于 IBM 客户和合作伙伴和一样都专用于开发和测试目的。 它们将不用于生产环境。 许多 IBM 安装包是通过下载[Passport 优势](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.guide.adcd.doc/topics/installation_ps.html)或[IBM PartnerWorld](https://www.ibm.com/partnerworld/public)。

## <a name="prerequisites"></a>系统必备

- Azure 订阅。 如果还没有该订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- [ZD & T 环境][ibm-install-z]以前在 Azure 上设置了。 本文假定使用前面创建的同一个 Ubuntu 16.04 VM 映像。

- 对通过 IBM PartnerWorld 或 Passport 利用 ADCD 媒体的访问。

- 一个[授权服务器](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html)。 这被必需运行 IBM zD 和 t。 创建它的方式取决于你如何获取来自 IBM 软件的许可：

  - **基于硬件的授权服务器**需要包含合理的令牌访问所有部分软件所需的 USB 硬件设备。 必须从 IBM 获取。

  - **基于软件的授权服务器**要求您设置授权密钥管理的一台中央服务器。 此方法是首选，并要求你设置的管理服务器中接收来自 IBM 的密钥。

## <a name="download-the-installation-packages-from-passport-advantage"></a>Passport 利用从下载的安装包

ADCD 媒体访问权限是必需的。 以下步骤假设你是 IBM 客户，并可以使用 Passport 优势。 可以使用 IBM 合作伙伴[IBM PartnerWorld](https://www.ibm.com/partnerworld/public)。

> [!NOTE]
> 本文假定 Windows PC 用于访问 Azure 门户并下载 IBM 媒体。 如果您使用的是 Mac 或 Ubuntu 桌面、 命令和用于获取 IBM 媒体的过程可能会略有不同。

1. 登录到[Passport 利用](https://www.ibm.com/software/howtobuy/passportadvantage/paocustomer)。

2. 选择**下载软件**并**媒体访问**。

3. 选择**程序的产品/服务和协议号**，然后单击**继续**。

4. 输入的一部分描述或部件编号，然后单击**Finder**。

5. 或者，单击要显示和按名称查看产品的字母顺序列表。

6. 选择**所有操作系统**中**操作系统字段**，并**所有语言**中**语言字段**。 然后，单击**转**。

7. 单击**选择单个文件**可展开列表并显示单个的媒体下载。

8. 验证你想要下载，请选择包**下载**，然后将文件下载到所需的目录。

## <a name="upload-the-adcd-packages"></a>上传 ADCD 程序包

现在，你已有包，必须将其上载到你在 Azure 上的 VM。

1. 在 Azure 门户中，启动**ssh**会话创建的 Ubuntu VM。 转到你的 VM，选择**概述**边栏选项卡，并选择**Connect**。

2. 选择**SSH**选项卡，然后将复制的 ssh 命令到剪贴板。

3. 登录到 VM，使用你的凭据并[SSH 客户端](/azure/virtual-machines/linux/use-remote-desktop)的选择。 适用于 Windows 10，将 bash 外壳程序添加到 Windows 命令提示符下，此演示程序使用 Linux 扩展。 PuTTY 也无妨。

4. 当登录时，创建一个目录来上传 IBM 包。 请注意 Linux 是区分大小写。 例如，此演示假设包上载到：

        /home/MyUserID/ZDT/adcd/nov2017/volumes

5. 例如使用 SSH 客户端将文件上传[WinSCP](https://winscp.net/eng/index.php)。 SCP 是 SSH 的一部分，因为它使用端口 22，这是 SSH 的使用。 如果在本地计算机不是 Windows，则可以键入[scp 命令](http://man7.org/linux/man-pages/man1/scp.1.html)SSH 会话中。

6. 启动上传到 Azure VM 目录创建，则将其转换的映像存储 zD 和 t。

    > [!NOTE]
    > 请确保**ADCDTOOLS。XML**包含在上传到**主页/MyUserID/ZDT/adcd/nov2017**目录。 稍后需要用到此值。

7. 等待要上传，这可能需要一些时间，具体取决于连接到 Azure 的文件。

8. 上传完成后，导航到卷目录和解压缩的所有**gz**卷：

    ```
        gunzip \*.gz
    ```
    
![文件资源管理器显示解压缩 gz 卷](media/01-gunzip.png)

## <a name="configure-the-image-storage"></a>将映像存储配置

下一步是配置 zD （& T） 若要使用已上传的程序包。 ZD （& T） 中的映像存储过程可以装载和使用的映像。 它可以使用 SSH 或 FTP。

1. 启动**zDTServer**。 要执行此操作，您必须是在根级别。 按顺序输入以下两个命令：
    ```
        sudo su -
        /opt/ibm/zDT/bin/startServer
    ```
2. 请注意该命令的 URL 输出和使用此 URL 来访问 web 服务器。 它看起来类似于：
     > https://(your VM name or IP address):9443/ZDTMC/index.html
     >
     > 请记住，你的 web 访问使用端口 9443。 用于登录到 web 服务器上。 ZD （& T) 的用户 ID 是**zdtadmin**并且该密码是**密码**。

    ![IBM zD 和 T Enterprise Edition 欢迎屏幕](media/02-welcome.png)

3. 上**快速启动**页面上，在**配置**，选择**映像存储**。

     ![IBM zD 和 T Enterprise Edition 快速启动屏幕](media/03-quickstart.png)

4. 上**配置的映像存储**页上，选择**SSH 文件传输协议**。

5. 有关**主机名**，类型**Localhost**然后输入目录路径上传到映像。 例如，/home/MyUserID/ZDT/adcd/nov2017/volumes。

6. 输入**用户 ID**并**密码**vm。 不允许使用 ZD T 用户 ID 和密码。

7. 测试连接，请确保您具有访问权限，然后选择**保存**保存配置。

## <a name="configure-the-target-environments"></a>配置目标环境

下一步是配置 zD & T 目标环境。 此模拟的托管的环境是运行你的映像的位置。

1. 上**快速启动**页面上，在**配置**，选择**目标环境**。

2. 上**配置目标环境**页上，选择**Add Target**。

3. 选择**Linux**。 IBM 支持两种类型的环境中，Linux 和 Cloud(OpenStack)，但此演示在 Linux 上运行。

4. 上**添加目标环境**页上，对于**主机名**，输入**localhost**。 保持**SSH 端口**设置为**22**。

5. 在中**目标环境标签**框中，输入一个标签，如**MyCICS。**

     ![添加目标环境屏幕](media/04-add-target.png)

## <a name="configure-adcd-and-deploy"></a>配置 ADCD 和部署

完成前面的配置步骤后，必须配置 zD （& T） 若要使用程序包和目标环境。 同样，使用 zD （& T），可用于装载和使用映像中的映像存储过程。 它可以使用 SSH 或 FTP。

1. 上**快速启动**页面上，在**配置**，选择**ADCD**。 将显示一组指令，告诉你需要可以装载 ADCD 包之前完成的步骤。 这解释了为什么我们名为的目标目录我们之前所做的方法。

2. 假设所有映像上都传到正确的目录，请单击**映像从 ADCD**链接显示在右下方 （如以下屏幕截图中的步骤 7 所示）。

     ![IBM zD & T Enterprise Edition-配置 ADCD 屏幕](media/05-adcd.png)

## <a name="create-the-image"></a>创建映像

以前的配置步骤完成后，**使用 ADCD 组件创建映像**页将出现。

1. 选择卷 (2017 年 11 月在此情况下) 显示了该卷中的不同程序包。

2. 对于此演示中，选择**客户信息控制系统 (CICS)-5.3**。

3. 在中**映像名称**框中，键入映像的名称，如**MyCICS 映像**。

4. 选择**创建的映像**在右下角的按钮。

     ![IBM zD & T Enterprise Edition-创建使用 ADCD 组件屏幕的图像](media/06-adcd.png)

5. 在窗口中显示，告诉您该映像已成功部署中，选择**部署映像**。

6. 上**映像部署到目标环境**页上，选择你在前一页创建的映像 (**MyCICS 映像**) 和前面创建的目标环境 (**MyCICS**).

7. 在下一个屏幕上，为 VM （即，不 ztadmin 凭据） 中提供你的凭据。

8. 在属性窗格中，输入的数字**中央处理器 (CPs)** ，量**系统内存 (GB)** ，并**部署目录**正在运行的映像。 由于这是一个演示，保持较小。

9. 请确保为选中此框**问题 IPL 命令到 z/OS 后自动部署**。

     ![属性屏幕](media/07-properties.png)

10. 选择**完整**。

11. 选择**部署映像**从**映像部署到目标环境**页。

你的映像现在可以将部署并已准备好由 3270 终端模拟器进行装载。

> [!NOTE]
> 如果你收到一个错误，指示您没有足够的磁盘空间，请注意在区域需要 151 Gb。

祝贺你！ 现在正在 Azure 上的 IBM 大型机环境。

## <a name="learn-more"></a>了解详细信息

- [大型机迁移： 误解和事实](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [在 Azure 上的 IBM DB2 pureScale](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure)
- [故障排除](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [揭秘大型机到 Azure 的迁移](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-on-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
