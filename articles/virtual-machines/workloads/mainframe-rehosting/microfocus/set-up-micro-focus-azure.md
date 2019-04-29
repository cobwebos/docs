---
title: 在 Azure 上安装微焦点 Enterprise Server 4.0 和企业级开发版 4.0 |Microsoft Docs
description: 重新托管在 IBM z/OS 大型机的工作负荷使用 Micro Focus 开发和测试 Azure 虚拟机 (Vm) 上的环境。
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
keywords: ''
ms.openlocfilehash: 33d0baf10df1882baf212c3e2c2683c8ca072fcc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61487568"
---
# <a name="install-micro-focus-enterprise-server-40-and-enterprise-developer-40-on-azure"></a>在 Azure 上安装微焦点 Enterprise Server 4.0 和企业级开发版 4.0

本文介绍如何设置[Micro 焦点 Enterprise Server 4.0](https://www.microfocus.com/documentation/enterprise-developer/es30/)并[Micro 焦点企业开发人员 4.0](https://www.microfocus.com/documentation/enterprise-developer/ed_30/)在 Azure 上。

Azure 上的常见工作负荷是开发和测试环境中，因为它是因此经济高效且易于部署和拆解。 与企业服务器，Micro Focus 已创建的最大大型机重新承载平台之一提供。 可以在成本较低的 x86 上运行 z/OS 的工作负荷在 Azure 中使用 Windows 或 Linux 虚拟机 (Vm) 的平台。

此安装程序将使用从 Azure Marketplace 与 Microsoft SQL Server 2017 已安装运行 Windows Server 2016 映像的 Azure Vm。 此设置也适用于 Azure Stack。

适用于 Enterprise Server 的相应开发环境是企业开发人员，在任一 Microsoft Visual Studio 2017，Visual Studio Community （免费下载） 运行，或 Eclipse。 本文介绍如何使用 Windows Server 2016 虚拟机安装 Visual Studio 2017 附带部署该网站。

## <a name="prerequisites"></a>必备组件

开始之前，请查看这些系统必备组件：

- Azure 订阅。 如果还没有该订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- Micro Focus 软件和有效的许可证 （或试用版许可证）。 如果您现有 Micro Focus 客户，请联系你的 Micro Focus 代表。 否则为[请求试用版](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/)。

- 获取有关的文档[企业服务器和企业开发人员](https://www.microfocus.com/documentation/enterprise-developer/#")。

> [!NOTE]
> 最佳做法是设置站点到站点虚拟专用网络 (VPN) 隧道或 jumpbox，以便可以控制对 Azure Vm 的访问。

## <a name="install-enterprise-server"></a>安装 Enterprise Server

1. 为了更好的安全性和可管理性，请考虑创建新的资源组，只需为此项目 — 例如， **RGMicroFocusEntServer**。 在 Azure 中使用的第一部分名称来指定要使其更容易发现列表中的资源类型。

2. 创建虚拟机。 从 Azure Marketplace 中，选择虚拟机和所需的操作系统。 下面是建议的安装程序：

    - **企业服务器**:选择使用 Windows Server 2016 和 SQL Server 2017 安装 ES2 v3 VM （使用 2 个 Vcpu 和 16 GB 内存）。 此映像是可从 Azure Marketplace。 企业服务器也可以使用 Azure SQL 数据库。

    - **企业级开发版**:选择 B2ms VM （使用 2 个 Vcpu 和 8 GB 内存） 使用 Windows 10 和 Visual Studio 安装。 此映像是可从 Azure Marketplace。

3. 上**基础知识**边栏选项卡中，输入用户名和密码。 选择**订阅**并**位置/区域**你想要使用的 vm。 选择**RGMicroFocusEntServer**资源组。

4. 将这两个 Vm 放入同一个虚拟网络，以便它们可以与彼此通信。

5. 接受其余的设置的默认值。 请记住的用户名和密码为这些 Vm 的管理员创建。

6. 创建虚拟机后，打开入站的端口 9003，和 80 用于 HTTP 和企业服务器计算机上的 RDP 的 3389 和 3389 开发人员计算机上的。

7. 若要登录到企业服务器虚拟机，在 Azure 门户中，选择 ES2 v3 VM。 转到**概述**边栏选项卡，然后选择**Connect**以启动一个 RDP 会话。 使用为 VM 创建的凭据登录。

8. 从 RDP 会话中，加载以下两个文件。 由于这是 Windows，因此可以将拖放到 RDP 会话中的文件：

    - **es\_40.exe**，企业服务器安装文件。

    - **mflic**，相应的许可证文件 — 企业服务器将不会加载没有它。

9. 双击该文件以开始安装。 在第一个窗口中，选择安装位置并接受最终用户许可协议。

     ![Micro 焦点 Enterprise Server 安装程序屏幕](media/01-enterprise-server.png)

     安装程序完成后，会显示以下消息：

     ![Micro 焦点 Enterprise Server 安装程序屏幕](media/02-enterprise-server.png)

### <a name="check-for-updates"></a>检查更新

安装完成后，请务必检查任何其他更新自多个先决条件，如 MicrosoftC++可再发行组件和.NET Framework 将随企业服务器一起安装。

### <a name="upload-the-license"></a>上传许可证

1. 启动微焦点许可证管理。

2. 单击**启动** \> **Micro 焦点许可证管理器** \> **许可证管理**，然后单击**安装**选项卡。选择要上传的许可证格式类型： 16 个字符许可证代码或许可证文件。 例如，对于一个文件中**许可证文件**，浏览到**mflic**文件之前上载到 VM，然后选择**安装许可证**。

     ![Micro 焦点许可证管理对话框](media/03-enterprise-server.png)

3. 验证加载了企业服务器。 尝试启动浏览器中使用此 URL 的企业服务器管理站点 <http://localhost:86/> 。 企业服务器管理页将显示所示。

     ![企业服务器管理页](media/04-enterprise-admin.png)

## <a name="install-enterprise-developer-on-the-developer-machine"></a>开发人员计算机上安装企业级开发版

1. 选择前面创建的资源组 (例如， **RGMicroFocusEntServer**)，然后选择的开发人员映像。

2. 若要登录到虚拟机，请转到**概述**边栏选项卡，然后选择**Connect**。 这将启动一个 RDP 会话。 使用为 VM 创建的凭据登录。

3. 从 RDP 会话中，加载以下两个文件 （拖放，如果您喜欢）：

    - **edvs2017.exe**，企业服务器安装文件。

    - **mflic**，相应的许可证文件 （企业开发人员不会加载没有它）。

4. 双击**edvs2017.exe**文件以开始安装。 在第一个窗口中，选择安装位置并接受最终用户许可协议。 如果你想选择**安装 Rumba 9.5**安装您可能需要此终端模拟器。

     ![Visual Studio 2017 安装程序对话框中的微焦点企业级开发版](media/04-enterprise-server.png)

5. 安装程序完成后，会显示以下消息：

     ![安装程序成功消息](media/05-enterprise-server.png)

6. 就像你对企业服务器，请启动 Micro 焦点许可证管理器。 选择**启动** \> **Micro 焦点许可证管理器** \> **许可证管理**，然后单击**安装**选项卡。

7. 选择要上传的许可证格式类型： 16 个字符许可证代码或许可证文件。 例如，对于一个文件中**许可证文件**，浏览到**mflic**文件之前上载到 VM，然后选择**安装许可证**。

     ![Micro 焦点许可证管理对话框](/edia/07-enterprise-server.png)

在企业级开发版加载后，你的 Azure 上的 Micro Focus 开发和测试环境的部署已完成 ！

## <a name="next-steps"></a>后续步骤

- [设置银行演示应用程序](./demo.md)
- [在 Docker 容器中运行企业服务器](./run-enterprise-server-container.md)
- [大型机应用程序迁移](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
