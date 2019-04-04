---
title: Azure 虚拟机上的 Docker 容器中运行微焦点 Enterprise Server 4.0
description: 通过 Azure 虚拟机上的 Docker 容器中运行微焦点企业服务器在重新托管在 IBM z/OS 大型机工作负荷。
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: sread
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 30d99c3f4767eb50361f7074c0d508fcf309faca
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2019
ms.locfileid: "58896269"
---
# <a name="run-micro-focus-enterprise-server-40-in-a-docker-container-on-azure"></a>在 Azure 上的 Docker 容器中运行微焦点 Enterprise Server 4.0

在 Azure 上，可以在 Docker 容器中运行微焦点 Enterprise Server 4.0。 本教程演示如何。 它使用 Windows CICS （客户信息控制系统） acctdemo 演示企业服务器。

Docker 将添加到应用程序的可移植性和隔离。 例如，可以导出的 Docker 映像从一个 Windows VM 运行在另一个，或从存储库复制到使用 Docker 的 Windows 服务器。 在具有相同配置的新位置中运行的 Docker 映像，而无需安装企业服务器。 它的映像的一部分。 许可注意事项仍适用。

本教程会安装**Windows 2016 数据中心与容器**从 Azure Marketplace 虚拟机 (VM)。 此 VM 包括**Docker 18.09.0**。 下面的步骤说明如何将容器部署，运行它，以及与 3270 仿真程序，然后连接到它。

## <a name="prerequisites"></a>必备组件

开始之前，请查看这些系统必备组件：

- Azure 订阅。 如果还没有该订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- Micro Focus 软件和有效的许可证 （或试用版许可证）。 如果你是现有的 Micro Focus 客户，请联系 Micro Focus 代表。 否则为[请求试用版](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/)。

     > [!NOTE]
     > Enterprise Server 4.0 包含 Docker 演示文件。 本教程使用 ent\_服务器\_dockerfile\_4.0\_windows.zip。 访问从同一个位置访问企业服务器安装文件，或转至*Micro Focus*若要开始。

- 文档[企业服务器和企业开发人员](https://www.microfocus.com/documentation/enterprise-developer/#")。

## <a name="create-a-vm"></a>创建 VM

1. 从客户端保护媒体\_服务器\_dockerfile\_4.0\_windows.zip 文件。 保护 ES Docker Prod XXXXXXXX.mflic 许可文件 （若要生成 Docker 映像的必需）。

2. 创建 VM。 若要执行此操作，请打开 Azure 门户中，选择**创建资源**从左上方，并按筛选*windows server*。 在结果中，选择**Windows Server 2016 Datacenter – 与容器**。

     ![Azure 门户的搜索结果](media/01-portal.png)

3. 若要配置的 VM 的属性，选择实例详细信息：

    1. 选择 VM 大小。 对于本教程中，请考虑使用**标准 DS2\_v2**具有 2 个 Vcpu 和 7 GB 内存的 VM。

    2. 选择**地区**并**资源组**你想要将部署到。

    3. 有关**可用性选项**，使用默认设置。

    4. 有关**用户名**，键入你想要使用的管理员帐户和密码。

    5. 请确保**端口 3389 RDP**处于打开状态。 仅需要此端口向公众公开，以便你可以登录到 VM。 接受所有默认值，然后单击**查看 + 创建**。

     ![创建虚拟机窗格](media/container-02.png)

4. 等待部署完成 （需要花费几分钟）。 消息，指明已创建你的 VM。

5. 单击**转到资源**若要转到**概述**在 VM 边栏选项卡。

6. 在右侧，单击**Connect**按钮。 **连接到虚拟机**选项显示在右侧。

7. 单击**下载 RDP 文件**按钮以下载 RDP 文件，可用于将附加到 VM。

8. 文件下载完毕后，打开它并键入用户名和密码为 VM 创建中。

     > [!NOTE]
     > 不使用公司凭据登录。 （您可能想要使用这些假设 RDP 客户端。 不这样做。）

9.  选择**其他选择**，然后选择你的虚拟机凭据。

在此情况下，VM 正在运行且通过 RDP 连接。 你的登录中并可供下一步。

## <a name="create-a-sandbox-directory-and-upload-the-zip-file"></a>创建一个沙盒目录并上传 zip 文件

1.  用于上传的演示和许可证文件在 VM 上创建一个目录。 例如， **c:\\沙盒**。

2.  上传**ent\_服务器\_dockerfile\_4.0\_windows.zip**并**ES Docker Prod XXXXXXXX.mflic**为你创建的目录的文件。

3.  提取到该 zip 文件的内容**ent\_服务器\_dockerfile\_4.0\_windows**提取过程创建目录。 此目录包含自述文件 （如.html 和.txt 文件） 和两个子目录**EnterpriseServer**并**示例**。

4.  复制**ES Docker Prod XXXXXXXX.mflic**到 c:\\沙盒\\ent\_server\_dockerfile\_4.0\_windows\\EnterpriseServer 和 c:\\沙盒\\ent\_服务器\_dockerfile\_4.0\_windows\\示例\\CICS 目录。

> [!NOTE]
> 请确保将授权文件复制到这两个目录。 它们所需的 Docker 生成步骤，以确保映像正确获得许可。

## <a name="check-docker-version-and-create-base-image"></a>检查 Docker 版本，并创建基本映像

> [!IMPORTANT]
> 创建适当的 Docker 映像是一个两步过程。 首先，创建企业 Server 4.0 基本映像。 然后，创建另一个映像针对 x64 平台。 虽然您可以创建 x86 （32 位） 映像，使用 64 位映像。

1. 打开命令提示符。

2. 检查安装了 Docker。 在命令提示符处，键入：

    ```
        docker version
    ```

     例如，版本是 18.09.0 时写入。

3. 若要更改目录，键入**cd \Sandbox\ent_server_dockerfiles_4.0_windows\EnterpriseServer**。

4. 类型**bld.bat IacceptEULA**开始初始基本映像的生成过程。 等待几分钟时间来运行此过程。 在结果中，已创建的两个映像，请注意，一个用于 x64 和 x86:

     ![命令窗口，其中显示图像](media/container-04.png)

5. 若要创建此最终图像可查看 CICS 演示，请切换到 CICS 目录通过键入**cd\\沙盒\\ent\_server\_dockerfile\_4.0\_windows\\示例\\CICS**。

6. 若要创建映像，请键入**bld.bat x64**。 等待几分钟时间运行的进程和条消息，指出已创建映像。

7. 类型**docker 映像**以显示所有安装在 VM 上的 Docker 映像的列表。 请确保**microfocus/es acctdemo**是其中之一。

     ![命令窗口，其中显示 es acctdemo 图像](media/container-05.png)

## <a name="run-the-image"></a>运行映像 

1.  若要启动企业 Server 4.0 和 acctdemo 应用程序，在命令提示符下键入：

    ```
         docker run -p 16002:86/tcp -p 16002:86/udp -p 9040-9050:9040-9050 -p 9000-9010:9000-9010 -ti --network="nat" --rm microfocus/es-acctdemo:win_4.0_x64
    ```

2.  安装 3270 终端仿真程序，如[x3270](http://x3270.bgp.nu/)并使用它来附加，通过端口 9040，到正在运行的映像。

3.  获取 acctdemo 容器的 IP 地址，以便 Docker 可以充当容器的 DHCP 服务器管理：

    1.  获取正在运行的容器的 ID。 类型**Docker ps**在命令提示符并记下 ID (**22a0fe3159d0**在此示例中)。 将其保存供下一步。

    2.  若要获取 acctdemo 容器的 IP 地址，请使用上一步中的容器 ID，如下所示：

    ```
       docker inspect <containerID> --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```

       例如：

    ```   
        docker inspect 22a0fe3159d0 --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```
4. 请注意 acctdemo 图像的 IP 地址。 例如，下面的输出中的地址是 172.19.202.52。

     ![命令窗口中显示的 IP 地址](media/container-06.png)

5. 装载映像使用仿真程序。 配置模拟器以使用 acctdemo 映像和端口 9040 的地址。 在这里，它具有**172.19.202.52:9040**。 将会类似。 **登录到 CICS**屏幕随即打开。

    ![登录到 CICS 屏幕](media/container-07.png)

6. 通过输入登录到 CICS 区域**SYSAD**有关**USERID**并**SYSAD**有关**密码**。

7. 清除屏幕上，使用仿真程序的键映射。 对于 x3270，选择**键映射**菜单选项。

8. 若要启动 acctdemo 应用程序，请键入**ACCT**。 显示应用程序的初始屏幕。

     ![帐户演示屏幕](media/container-08.png)

9. 尝试显示帐户类型。 例如，键入**D**请求并**11111**有关**帐户**。 若要尝试其他帐户数字是 22222、 33333，等等。

     ![帐户演示屏幕](media/container-09.png)

10. 若要显示在 Enterprise Server 管理控制台，请转到命令提示符并键入**启动 http:172.19.202.52:86**

     ![企业服务器管理控制台](media/container-010.png)

就这么简单！ 现在你正在运行和管理 Docker 容器中的 CICS 应用程序。

## <a name="next-steps"></a>后续步骤

- [在 Azure 上安装微焦点 Enterprise Server 4.0 和企业级开发版 4.0](./set-up-micro-focus-azure.md)
- [大型机应用程序迁移](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
