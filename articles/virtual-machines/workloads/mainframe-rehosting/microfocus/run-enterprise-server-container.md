---
title: 在 Azure 上的 Docker 容器中运行微聚焦企业服务器 5.0 |Microsoft Docs
description: 使用 Azure 虚拟机（Vm）上的微焦点开发和测试环境 Rehost IBM z/OS 大型机工作负荷。
services: virtual-machines-linux
documentationcenter: ''
author: maggsl
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 06/29/2020
tags: ''
keywords: ''
ms.service: multiple
ms.openlocfilehash: d266dbd0ee908c41a7d29ddbb6d9c73fcfdc7c9e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87083457"
---
# <a name="run-micro-focus-enterprise-server-50-in-a-docker-container-on-azure"></a>在 Azure 上的 Docker 容器中运行微聚焦企业服务器5。0

可以在 Azure 上的 Docker 容器中运行微聚焦企业服务器5.0。 本教程将向您介绍如何操作。 它使用适用于企业服务器的 Windows CICS （客户信息控制系统） acctdemo 演示。

Docker 增加了应用程序的可移植性和隔离性。 例如，可以从一个 Windows 虚拟机（VM）导出一个 Docker 映像，使其在另一个 Windows 虚拟机（VM）上运行，或者从存储库导出到使用 Docker 的 Windows server。 Docker 映像在具有相同配置的新位置运行，无需安装企业服务器。 它是映像的一部分。 许可注意事项仍适用。

本教程通过 Azure Marketplace 安装**带有容器 VM 的 Windows 2016 Datacenter** 。 此 VM 包含**Docker 18.09.0**。 下面的步骤演示了如何部署容器，运行它，然后使用3270模拟器连接到该容器。

## <a name="prerequisites"></a>先决条件

在开始之前，请先查看以下先决条件：

-   Azure 订阅。 如果还没有该订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

-   微聚焦软件和有效许可证（或试用许可证）。 如果你是现有的微聚焦客户，请联系你的微侧重点代表。 否则，[请求试用](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/)。

    > [!Note] 
    > Docker 演示文件包含在企业服务器5.0 中。 本教程使用 ent \_ server \_ dockerfile \_ 5.0 \_windows.zip。 从访问企业服务器安装文件的同一位置访问它，或转到 "*微*" 以开始使用。

-   [企业服务器和企业开发人员](https://www.microfocus.com/documentation/enterprise-developer/#%22)的文档。

## <a name="create-a-vm"></a>创建 VM

1.  保护 ent \_ server \_ dockerfile \_ 5.0 \_windows.zip 文件中的媒体。 保护 mflic 授权文件（构建 Docker 映像所需的文件）。

2.  创建 VM。 为此，请打开 Azure 门户，从左上方菜单中选择 "**创建资源**"，并按 " *windows server 操作系统*" 进行筛选。 在结果中，选择 " **Windows Server"。** 在下一屏幕中，选择 " **Windows Server 2016 Datacenter – With 容器**"。

    ![Azure 门户搜索结果的屏幕截图](./media/run-image-1.png)

3.  若要配置 VM 的属性，请选择 "实例详细信息"：

    1.  选择 VM 大小。 对于本教程，请考虑使用**标准 DS2 \_ v3** VM，其中包含2个个 VCPU 和 16 GB 的内存。

    2.  选择要部署到的**区域**和**资源组**。

    3.  对于**可用性选项**，请使用默认设置。

    4.  对于 "**用户名**"，请键入要使用的管理员帐户和密码。

    5.  请确保已打开**端口 3389 RDP** 。 只需公开此端口，即可登录到 VM。 然后，接受所有默认值，然后单击 "**查看 + 创建**"。

    !["创建虚拟机" 窗格的屏幕截图](./media/run-image-2.png)

4.  等待部署完成（几分钟）。 将出现一条消息，指出已创建你的 VM。

5.  选择 "**中转到资源**" 以前往 VM 的 "**概述**" 边栏选项卡。

6.  在右侧，选择 "**连接**"。 右侧将显示 "**连接到虚拟机**" 选项。

7.  选择 "**下载 RDP 文件**" 按钮，下载允许附加到 VM 的远程桌面协议（RDP）文件。

8.  文件下载完成后，打开它，键入为 VM 创建的用户名和密码。

    > [!Note]    
    > 不要使用企业凭据登录。 （RDP 客户端假设你可能想要使用它们。 不会。）

9.  选择 "**更多**选择"，然后选择 VM 凭据。

此时，VM 正在运行，并通过 RDP 连接。 你已登录并准备好进行下一步。

## <a name="create-a-sandbox-directory-and-upload-the-zip-file"></a>创建沙盒目录并上传 zip 文件

1.  在 VM 上创建一个目录，你可以在其中上载演示和许可证文件。 例如， **C： \\ 沙盒**。

2.  将**ent \_ server \_ dockerfile \_ 5.0 \_windows.zip**和**mflic**文件上传到所创建的目录。

3.  将 zip 文件的内容提取到提取过程创建的**ent \_ server \_ dockerfile \_ 5.0 \_ windows**目录中。 此目录包含一个自述文件（如 .html 和 .txt 文件）和两个子目录（ **EnterpriseServer**和**示例**）。

4.  将**mflic**复制到 C： \\ 沙盒 \\ ent \_ server \_ dockerfile \_ 5.0 \_ windows \\ EnterpriseServer 和 c： \\ 沙盒 \\ ent \_ server \_ dockerfile \_ 5.0 \_ windows \\ 示例 \\ CICS 目录。  
      
    > [!Note]
    > 请确保将许可文件复制到这两个目录。 它们对于 Docker build 步骤是必需的，以确保映像获得正确许可。

## <a name="check-docker-version-and-create-base-image"></a>检查 Docker 版本并创建基础映像

> [!Important]  
> 创建相应的 Docker 映像的过程分为两个步骤。 首先，创建企业服务器5.0 基础映像。 然后为 x64 平台创建另一个映像。 尽管可以创建 x86 （32位）映像，但请使用64位映像。

1.  打开命令提示符。

2.  检查是否已安装 Docker。 在命令提示符下，键入： **docker 版本**  
    例如，当编写此版本时，将18.09.0 版本。

3.  若要更改目录，请键入：  
    **cd \\沙盒 \\ ent \_ server \_ dockerfile \_ 5.0 \_ windows \\ EnterpriseServer**。

4.  键入**bld.bat IacceptEULA** ，开始初始基本映像的生成过程。 等待几分钟以便运行此进程。 在结果中，请注意创建了两个映像：一个用于 x64，一个用于 x86：

    ![显示图像的命令窗口](./media/run-image-3.png)

5.  若要为 CICS 演示创建最终映像，请通过键入**cd \\ 沙箱 \\ ent \_ server \_ dockerfile \_ 5.0 \_ windows \\ 示例 \\ cics**切换到 cics 目录。

6.  若要创建映像，请键入**bld.bat x64**。 请等待几分钟，让进程运行，并显示一条消息，指出已创建该映像。

7.  键入**docker 映像**以显示 VM 上安装的所有 docker 映像的列表。 请确保**microfocus/acctdemo**是其中之一。

    ![显示 acctdemo 映像的命令窗口](./media/run-image-4.png)

## <a name="run-the-image"></a>运行映像

1.  若要启动企业服务器5.0 和 acctdemo 应用程序，请在命令提示符下键入：

    ~~~
    **docker run -p 16002:86/tcp -p 16002:86/udp -p 9040-9050:9040-9050 -p 9000-9010:9000-9010 -ti --network="nat" --rm microfocus/es-acctdemo:win\_5.0\_x64
    ~~~

1.  安装3270终端模拟器（如[x3270](http://x3270.bgp.nu/) ），并使用它将端口9040附加到运行的映像。

2.  获取 acctdemo 容器的 IP 地址，以便 Docker 可充当它所管理的容器的动态主机配置协议（DHCP）服务器：

    1.  获取正在运行的容器的 ID。 在命令提示符下键入**Docker ps** ，并记下 ID （在本示例中为**22a0fe3159d0** ）。 将其保存到下一步。

    2.  若要获取 acctdemo 容器的 IP 地址，请使用上一步中的容器 ID，如下所示：

    ~~~
    docker inspect \<containerID\> --format="{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ~~~

    例如：

    ~~~
    docker inspect 22a0fe3159d0 --format="{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ~~~

4. 请注意 acctdemo 映像的 IP 地址。 例如，以下输出中的地址为172.19.202.52。

    ![显示 IP 地址的命令窗口屏幕截图](./media/run-image-5.png)

5. 使用模拟器装载映像。 将仿真程序配置为使用 acctdemo 映像和端口9040的地址。 下面是**172.19.202.52： 9040**。 你的情况将类似。 "**登录到 CICS** " 屏幕将打开。

    ![登录到 CICS 的屏幕截图](./media/run-image-6.png)

6. 输入**用户 id**的**SYSAD**和**SYSAD**作为**密码**，以登录到 CICS 区域。

7. 使用仿真程序的快捷键映射清除屏幕。 对于 x3270，请选择 "**快捷键映射**" 菜单选项。

8. 若要启动 acctdemo 应用程序，请键入 "**帐户**"。 将显示应用程序的初始屏幕。

     ![帐户演示的屏幕截图](./media/run-image-7.png)

9. 试用显示帐户类型。 例如，键入**D**作为请求，将**11111**键入**帐户**。 要尝试的其他帐号为22222、33333等。

    ![帐户演示的屏幕截图](./media/run-image-8.png)

10. 若要显示 Enterprise Server 管理控制台，请打开命令提示符，然后键入**start http：172.19.202.52： 86**。

    ![企业服务器管理控制台](media/run-image-9.png)

就这么简单！ 现在正在运行并在 Docker 容器中管理 CICS 应用程序。

## <a name="next-steps"></a>后续步骤

-   [在 Azure 上安装微聚焦企业服务器5.0 和企业开发人员5。0](./set-up-micro-focus-azure.md)

-   [大型机应用程序迁移](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
