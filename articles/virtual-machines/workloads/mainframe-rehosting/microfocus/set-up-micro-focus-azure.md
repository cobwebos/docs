---
title: 在 Azure 上安装微焦点企业服务器 4.0 和企业开发人员 4.0 |微软文档
description: 使用 Azure 虚拟机 （VM） 上的微焦点开发和测试环境重新托管 IBM z/OS 大型机工作负载。
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 05/29/2019
tags: ''
keywords: ''
ms.service: multiple
ms.openlocfilehash: a5426c3cd7552b24739f9a20e01d5a4b42bd383c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "68834571"
---
# <a name="install-micro-focus-enterprise-server-40-and-enterprise-developer-40-on-azure"></a>在 Azure 上安装微焦点企业服务器 4.0 和企业开发人员 4.0

本文演示如何在 Azure 上设置[微焦点企业服务器 4.0](https://www.microfocus.com/documentation/enterprise-developer/es30/)和[微焦点企业开发人员 4.0。](https://www.microfocus.com/documentation/enterprise-developer/ed_30/)

Azure 上的常见工作负荷是开发和测试环境。 此方案很常见，因为它非常经济高效且易于部署和拆除。 借助企业服务器，Micro Focus 创建了最大的大型主机重新托管平台之一。 您可以使用 Windows 或 Linux 虚拟机 （VM） 在 Azure 上较便宜的 x86 平台上运行 z/OS 工作负荷。

此设置使用 Azure 服务器 2016 映像从 Azure 应用商店运行的 Azure VM，并且已安装 Microsoft SQL Server 2017。 此设置也适用于 Azure 堆栈。

企业服务器的相应开发环境是企业开发人员，它运行在 Microsoft Visual Studio 2017 或更高版本，Visual Studio 社区（免费下载）或 Eclipse。 本文演示如何使用 Visual Studio 2017 或更高版本附带的 Windows Server 2016 虚拟机进行部署。

## <a name="prerequisites"></a>先决条件

在开始之前，请查看以下先决条件：

- Azure 订阅。 如果还没有该订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- 微焦点软件和有效的许可证（或试用许可证）。 如果您是现有的微焦点客户，请联系您的微焦点代表。 否则，[请请求试用](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/)。

- 获取[企业服务器和企业开发人员](https://www.microfocus.com/documentation/enterprise-developer/#")的文档。

> [!NOTE]
> 最佳做法是设置站点到站点的虚拟专用网络 （VPN） 隧道或跳转盒，以便您可以控制对 Azure VM 的访问。

## <a name="install-enterprise-server"></a>安装 Enterprise Server

1. 为了更好的安全性和可管理性，请考虑只为此项目创建新的资源组，例如 **，RGMicroFocusEntServer。** 使用 Azure 中名称的第一部分选择资源类型，以便更轻松地在列表中发现。

2. 创建虚拟机。 在 Azure 应用商店中，选择所需的虚拟机和操作系统。 下面是推荐的设置：

    - **企业服务器**： 选择 ES2 v3 VM（具有 2 个 vCPU 和 16 GB 内存），安装 Windows 服务器 2016 和 SQL Server 2017。 此映像可从 Azure 应用商店获得。 企业服务器也可以使用 Azure SQL 数据库。

    - **企业开发人员**：选择安装了 Windows 10 和可视化工作室的 B2ms VM（具有 2 个 vCPU 和 8 GB 内存）。 此映像可从 Azure 应用商店获得。

3. 在 **"基础知识"** 部分中，输入您的用户名和密码。 选择要用于 VM 的**订阅**和**位置/区域**。 为资源组选择**RGMicro 焦点服务器**。

4. 将两个 VM 放入同一虚拟网络中，以便它们能够相互通信。

5. 接受其余设置的默认值。 请记住您为这些 VM 的管理员创建的用户名和密码。

6. 创建虚拟机后，在企业服务器计算机上打开用于 HTTP 的入站端口 9003、86 和 80，在开发人员计算机上打开 3389，在开发人员计算机上打开 3389。

7. 要登录到企业服务器虚拟机，请在 Azure 门户中选择 ES2 v3 VM。 转到 **"概述"** 部分并选择"**连接"** 以启动 RDP 会话。 使用为 VM 创建的凭据登录。

8. 从 RDP 会话中加载以下两个文件。 由于您使用的是 Windows，因此您可以将文件拖放到 RDP 会话中：

    - **es\_40.exe，** 企业服务器安装文件。

    - **，** 相应的许可证文件 — 企业服务器没有它就不会加载。

9. 双击文件以启动安装。 在第一个窗口中，选择安装位置并接受最终用户许可协议。

     ![微焦点企业服务器设置屏幕](media/01-enterprise-server.png)

     安装程序完成后，将显示以下消息：

     ![微焦点企业服务器设置屏幕](media/02-enterprise-server.png)

### <a name="check-for-updates"></a>检查更新

安装后，请务必检查任何其他更新，因为许多先决条件（如 Microsoft C++可再分发和 .NET 框架）与企业服务器一起安装。

### <a name="upload-the-license"></a>上传许可证

1. 启动微焦点许可证管理。

2. 单击 **"开始**\>**微焦点许可证管理器**\>**许可证管理**"，然后单击"**安装**"选项卡。选择要上载的许可证格式类型：许可证文件或 16 个字符的许可证代码。 例如，对于文件，在**许可证文件中**，浏览到以前上载到 VM**的 mflic**文件，然后选择 **"安装许可证**"。

     ![微焦点许可证管理对话框](media/03-enterprise-server.png)

3. 验证企业服务器是否加载。 请尝试使用此 URL <http://localhost:86/> 从浏览器启动企业服务器管理网站。 企业服务器管理页如图所示。

     ![企业服务器管理页面](media/04-enterprise-admin.png)

## <a name="install-enterprise-developer-on-the-developer-machine"></a>在开发人员计算机上安装企业开发人员

1. 选择较早创建的资源组（例如 **，RGMicroFocusEntServer），** 然后选择开发人员映像。

2. 要登录到虚拟机，请转到 **"概述"** 部分并选择"**连接**"。 此登录将启动 RDP 会话。 使用为 VM 创建的凭据登录。

3. 从 RDP 会话中，加载以下两个文件（如果您愿意，请拖放）：

    - **edvs2017.exe**，企业服务器安装文件。

    - **，** 相应的许可证文件（企业开发人员不会加载没有它）。

4. 双击**edvs2017.exe**文件以启动安装。 在第一个窗口中，选择安装位置并接受最终用户许可协议。 如果需要，请选择 **"安装 Rumba 9.5"** 来安装此终端仿真器，您可能需要这样做。

     ![Visual Studio 2017 安装对话框的微焦点企业开发人员](media/04-enterprise-server.png)

5. 设置完成后，将显示以下消息：

     ![设置成功消息](media/05-enterprise-server.png)

6. 启动微焦点许可证管理器，就像为企业服务器所做的那样。 选择 **"开始**\>**微焦点许可证管理器**\>**许可证管理**"，然后单击"**安装**"选项卡。

7. 选择要上载的许可证格式类型：许可证文件或 16 个字符的许可证代码。 例如，对于文件，在**许可证文件中**，浏览到以前上载到 VM**的 mflic**文件，然后选择 **"安装许可证**"。

     ![微焦点许可证管理对话框](media/07-enterprise-server.png)

当企业开发人员加载时，您在 Azure 上部署微焦点开发和测试环境就完成了！

## <a name="next-steps"></a>后续步骤

- [设置银行演示应用程序](./demo.md)
- [在 Docker 容器中运行企业服务器](./run-enterprise-server-container.md)
- [大型机应用程序迁移](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
