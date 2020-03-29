---
title: 在 Azure 虚拟机上的 Docker 容器中运行微焦点企业服务器 4.0
description: 通过在 Azure 虚拟机上的 Docker 容器中运行微焦点企业服务器，重新托管 IBM z/OS 主机工作负载。
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: sread
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 30d99c3f4767eb50361f7074c0d508fcf309faca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "61488272"
---
# <a name="run-micro-focus-enterprise-server-40-in-a-docker-container-on-azure"></a>在 Azure 上的 Docker 容器中运行微焦点企业服务器 4.0

您可以在 Azure 上的 Docker 容器中运行微焦点企业服务器 4.0。 本教程将介绍如何。 它使用企业服务器的 Windows CICS（客户信息控制系统）acctdemo 演示。

Docker 为应用程序添加了可移植性和隔离性。 例如，可以从一个 Windows VM 导出 Docker 映像以在另一个 Windows VM 上运行，或者从存储库导出到使用 Docker 的 Windows 服务器。 Docker 映像以相同的配置在新位置运行，而无需安装企业服务器。 它是图像的一部分。 许可注意事项仍然适用。

本教程使用 Azure 应用商店中的容器虚拟机 （VM） 安装**Windows 2016 数据中心**。 此 VM 包括**Docker 18.09.0**。 以下步骤演示如何部署容器、运行容器，然后使用 3270 仿真器连接到该容器。

## <a name="prerequisites"></a>先决条件

在开始之前，请查看以下先决条件：

- Azure 订阅。 如果还没有该订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- 微焦点软件和有效的许可证（或试用许可证）。 如果您是现有的微焦点客户，请联系您的微焦点代表。 否则，[请请求试用](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/)。

     > [!NOTE]
     > Docker 演示文件包含在企业服务器 4.0 中。 本教程使用 ent\_\_服务器\_dockerfile\_4.0 windows.zip。 从访问企业服务器安装文件的同一位置访问它，或转到*Micro Focus*以开始。

- [企业服务器和企业开发人员](https://www.microfocus.com/documentation/enterprise-developer/#")的文档。

## <a name="create-a-vm"></a>创建 VM

1. \_从 ent\_服务器 dockerfile\_4.0\_windows.zip 文件保护媒体。 保护 ES-Docker-Prod-XXXXXXXX.mflic 许可文件（构建 Docker 映像所需的）。

2. 创建 VM。 为此，请打开 Azure 门户，选择从左上角**创建资源**，然后按*窗口服务器*进行筛选。 在结果中，选择**Windows 服务器 2016 数据中心 + 带容器**。

     ![Azure 门户搜索结果](media/01-portal.png)

3. 要配置 VM 的属性，请选择实例详细信息：

    1. 选择 VM 大小。 在本教程中，请考虑使用具有 2 个 vCPU 和 7 GB 内存**的标准\_DS2 v2** VM。

    2. 选择要部署到**的区域****和资源组**。

    3. 对于**可用性选项**，请使用默认设置。

    4. 对于**用户名**，键入要使用的管理员帐户和密码。

    5. 确保**端口 3389 RDP**已打开。 只有此端口需要公开公开，因此您可以登录到 VM。 然后接受所有默认值，然后单击 **"审阅+创建**"。

     ![创建虚拟机窗格](media/container-02.png)

4. 等待部署完成（几分钟）。 一条消息指出您的 VM 已创建。

5. 单击"**转到资源**"转到 VM 的 **"概述"** 边栏选项卡。

6. 在右侧，单击"**连接**"按钮。 右侧显示 **"连接到虚拟机**"选项。

7. 单击 **"下载 RDP 文件"** 按钮下载 RDP 文件，该文件允许您附加到 VM。

8. 文件下载完成后，打开该文件并键入您为 VM 创建的用户名和密码。

     > [!NOTE]
     > 请勿使用公司凭据登录。 （RDP 客户端假定您可能需要使用这些。 您没有。

9.  选择**更多选项**，然后选择 VM 凭据。

此时，VM 正在通过 RDP 运行和连接。 您已登录，并准备好执行下一步。

## <a name="create-a-sandbox-directory-and-upload-the-zip-file"></a>创建沙盒目录并上传 zip 文件

1.  在 VM 上创建一个目录，您可以在其中上载演示和许可证文件。 例如 **，C：\\沙盒**。

2.  将**\_ent\_服务器\_dockerfile\_4.0 windows.zip**和**ES-Docker-Prod-XXXXXXXX.mflic**文件上载到您创建的目录。

3.  将 zip 文件的内容提取到由提取过程创建的**ent\_服务器\_\_dockerfile 4.0\_窗口**目录。 此目录包括一个 readme 文件（作为 .html 和 .txt 文件）和两个子目录，**企业服务器**和**示例**。

4.  将**ES-Docker-Prod-XXXXXXXX.mflic 复制到**\\C：沙\\盒\_\_ent\_服务器 Dockerfile 4.0\_窗口\\企业服务器和\_\_C：\_\\沙盒\\ent\\服务器 Dockerfile 4.0\_窗口\\示例 CICS 目录。

> [!NOTE]
> 请确保将许可文件复制到两个目录。 它们是必需的 Docker 生成步骤，以确保映像已正确获得许可。

## <a name="check-docker-version-and-create-base-image"></a>检查 Docker 版本并创建基本映像

> [!IMPORTANT]
> 创建适当的 Docker 映像是一个两步过程。 首先，创建企业服务器 4.0 基本映像。然后为 x64 平台创建另一个映像。 尽管您可以创建 x86（32 位）图像，但请使用 64 位图像。

1. 打开命令提示符。

2. 检查是否安装了 Docker。 在命令提示符处，键入：

    ```
        docker version
    ```

     例如，编写此版本时的版本为 18.09.0。

3. 要更改目录，请键入**cd [沙盒]ent_server_dockerfiles_4.0_windows\企业服务器**。

4. 键入**bld.bat IacceptEULA**以开始初始基本映像的生成过程。 等待几分钟，此过程运行。 在结果中，请注意已创建的两个图像 - 一个用于 x64，一个用于 x86：

     ![显示图像的命令窗口](media/container-04.png)

5. 要为 CICS 演示创建最终映像，请通过键入**\\cd 沙\\盒 ent\_服务器\_dockerfile\_4.0\_窗口\\示例\\CICS**切换到 CICS 目录。

6. 要创建图像，请键入**bld.bat x64**。 等待几分钟，等待进程运行，并显示已创建映像的消息。

7. 键入**Docker 映像**以显示安装在 VM 上的所有 Docker 映像的列表。 确保**微聚焦/es-acctdemo**是其中之一。

     ![显示 es-acctdemo 图像的命令窗口](media/container-05.png)

## <a name="run-the-image"></a>运行映像 

1.  要启动企业服务器 4.0 和 acctdemo 应用程序，请执行命令提示符类型：

    ```
         docker run -p 16002:86/tcp -p 16002:86/udp -p 9040-9050:9040-9050 -p 9000-9010:9000-9010 -ti --network="nat" --rm microfocus/es-acctdemo:win_4.0_x64
    ```

2.  安装 3270 终端仿真器（如[x3270），](http://x3270.bgp.nu/)并用它来通过端口 9040 连接到正在运行的映像。

3.  获取 acctdemo 容器的 IP 地址，因此 Docker 可以充当其管理的容器的 DHCP 服务器：

    1.  获取正在运行的容器的 ID。 在命令提示符处键入**Docker ps**并记下 ID（在此示例中为**22a0fe3159d0）。** 将其保存为下一步。

    2.  要获取 acctdemo 容器的 IP 地址，请使用上一步的容器 ID，如下所示：

    ```
       docker inspect <containerID> --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```

       例如：

    ```   
        docker inspect 22a0fe3159d0 --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```
4. 请注意 acctdemo 映像的 IP 地址。 例如，以下输出中的地址为 172.19.202.52。

     ![显示 IP 地址的命令窗口](media/container-06.png)

5. 使用仿真器安装图像。 将仿真器配置为使用 acctdemo 映像和端口 9040 的地址。 这里是**172.19.202.52：9040**。 你的是相似的。 将打开 **"登录到 CICS"** 屏幕。

    ![登录到 CICS 屏幕](media/container-07.png)

6. 登录CICS区域，输入**SYSAD**的**USERID**和**SYSAD****的密码**。

7. 使用仿真器的键映射清除屏幕。 对于 x3270，选择 **"键映射"** 菜单选项。

8. 要启动 acctdemo 应用程序，请键入**ACCT**。 将显示应用程序的初始屏幕。

     ![帐户演示屏幕](media/container-08.png)

9. 试验显示帐户类型。 例如，为请求键入**D，** 为**帐户**键入**11111。** 其他要尝试的帐号是 22222、33333 等。

     ![帐户演示屏幕](media/container-09.png)

10. 要显示企业服务器管理控制台，请转到命令提示符并键入 **"开始"http：172.19.202.52：86**

     ![企业服务器管理控制台](media/container-010.png)

就这么简单！ 现在，您正在 Docker 容器中运行和管理 CICS 应用程序。

## <a name="next-steps"></a>后续步骤

- [在 Azure 上安装微焦点企业服务器 4.0 和企业开发人员 4.0](./set-up-micro-focus-azure.md)
- [大型机应用程序迁移](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
