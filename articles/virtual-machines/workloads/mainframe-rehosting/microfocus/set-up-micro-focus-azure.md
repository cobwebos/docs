---
title: 在 Azure 上安装微聚焦企业服务器5.0 和企业开发人员 5.0 |Microsoft Docs
description: 在 Azure 虚拟机 (Vm) 上使用微焦点开发和测试环境 Rehost IBM z/OS 大型机工作负荷。
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
ms.openlocfilehash: 59566fc051b1e84d8e271b3c9d061f2481dcc58f
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2020
ms.locfileid: "88245309"
---
# <a name="install-micro-focus-enterprise-server-50-and-enterprise-developer-50-on-azure"></a>在 Azure 上安装微聚焦企业服务器5.0 和企业开发人员5。0

本文介绍如何在 Microsoft Azure 上设置 [微聚焦企业服务器 5.0](https://www.microfocus.com/documentation/enterprise-developer/ed50pu5/ES-WIN/GUID-F7D8FD6E-BDE0-4169-8D8C-96DDFFF6B495.html) 和 [微型企业开发人员 5.0](https://www.microfocus.com/documentation/enterprise-developer/ed50/) 。

Azure 上的常见工作负载是开发和测试环境。 这种情况很常见，因为它非常经济高效且易于部署和拆卸。 企业服务器的微侧重点已创建了一个可用的最大大型机重新承载平台。 你可以使用 Windows 或 Linux 虚拟机 (Vm) 在 Azure 上运行 z/OS 工作负荷。

此安装程序使用已安装 Microsoft SQL Server 2017 的 Azure Marketplace 中运行 Windows Server 2016 映像的 Azure Vm。 此设置还适用于 Azure Stack。

企业服务器的相应开发环境是企业开发人员，在 Microsoft Visual Studio 2017 或更高版本上运行，Visual Studio 社区 (免费下载) 或 Eclipse。 本文介绍如何使用安装了 Visual Studio 2017 或更高版本的 Windows Server 2016 虚拟机来部署它。

## <a name="prerequisites"></a>先决条件

在开始之前，请先查看以下先决条件：

-   Azure 订阅。 如果还没有该订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

-   微重点软件和有效许可证 (或试用许可证) 。 如果你是现有的微聚焦客户，请联系你的微侧重点代表。 否则， [请求试用](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/)。

-   获取 [企业服务器和企业开发人员](https://www.microfocus.com/documentation/enterprise-developer/ed50/)的文档。

    > [!Note]
    > 有几个选项可用于控制对 Vm 的访问：
    > -   最佳做法是设置 [Azure 堡垒](https://azure.microsoft.com/services/azure-bastion/)。
    > -   [站点到站点虚拟专用网络 (VPN) ](../../../../vpn-gateway/vpn-gateway-tutorial-vpnconnection-powershell.md)隧道。
    > -   Jumpbox VM。

## <a name="install-enterprise-server"></a>安装 Enterprise Server

1.  为了获得更好的安全性和可管理性，请考虑仅为此项目创建新的资源组，例如 **RGMicroFocusEntServer**。 使用 Azure 中名称的第一部分来选择资源类型，以便更轻松地发现列表。

2.  创建虚拟机。 从 Azure Marketplace 中，选择所需的虚拟机和操作系统。 下面是建议的设置：

    -   **企业服务器：** 选择安装了 Windows Server 2016 和 SQL Server 2017 的2个 vcpu 和 16 GB 内存) 的 **ES2 V3 VM** (。 可从 Azure Marketplace 获取此映像。 企业服务器还可以使用 Azure SQL 数据库。

    -   **企业开发人员：** 选择安装了 Windows 10 和 Visual Studio 的 **B2MS VM** (具有2个个 vcpu 和 8 GB 内存) 。 可从 Azure Marketplace 获取此映像。

3.  在 " **基本** 信息" 部分中，输入用户名和密码。 选择要用于 Vm 的 **订阅** 和 **位置/区域** 。 为资源组选择 **RGMicroFocusEntServer** 。

4.  将这两个 Vm 放入同一虚拟网络，以便它们相互通信。

5.  接受其余设置的默认值。 请记住为这些 Vm 的管理员创建的用户名和密码。

6.  创建虚拟机后，在企业服务器计算机上打开适用于 HTTP 和**3389**的入站端口**9003、86**和**80** ，并为开发人员计算机上的**3389**打开 (RDP) 远程桌面协议。

7.  若要登录到企业服务器虚拟机，请在 Azure 门户中选择 "ES2 v3 VM"。 请参阅 " **概述** " 部分，并选择 " **连接** " 以启动 RDP 会话。 使用为 VM 创建的凭据登录。

8.  从 RDP 会话加载以下两个文件。 由于你正在使用 Windows，因此你可以将文件拖放到 RDP 会话中：

    -   `es\_50.exe`，企业服务器安装文件。

    -   `mflic`（相应的许可证文件）：企业服务器将不会加载。

9.  双击该文件以开始安装。 在第一个窗口中，选择安装位置并接受最终用户许可协议。

    ![微聚焦企业服务器安装屏幕](media/install-image-1.png)

    安装完成后，将显示以下消息：

    ![微聚焦企业服务器安装屏幕](media/install-image-2.png)

 ### <a name="check-for-updates"></a>检查更新

安装完成后，请务必检查是否有许多先决条件（如 Microsoft c + + 可再发行组件和 .NET Framework）随企业服务器一起安装。

### <a name="upload-the-license"></a>上传许可证

1.  开始获得微 "许可管理"。

2.  选择 " **开始**微" " \> **许可证管理器** \> **许可证管理**"，然后单击 " **安装** " 选项卡。选择要上传的许可证格式的类型：许可证文件或16个字符的许可代码。 例如，对于文件，在 " **许可证文件**" 中，浏览到 `mflic` 以前上传到 VM 的 * 文件，然后选择 " **安装许可证**"。

    !["微重点许可管理" 对话框](media/install-image-3.png)

3.  验证企业服务器是否已加载。 尝试使用以下 URL 在浏览器中启动企业服务器管理站点： `http://localhost:86/` 。 将显示 "企业服务器管理" 页，如下所示。

    ![企业服务器管理页](media/install-image-4.png)

## <a name="install-enterprise-developer-on-the-developer-machine"></a>在开发人员计算机上安装企业开发人员

1.  选择之前创建的资源组 (例如 **RGMicroFocusEntServer**) ，然后选择 "开发人员映像"。

2.  若要登录到虚拟机，请参阅 " **概述** " 部分，并选择 " **连接**"。 此登录启动 RDP 会话。 使用为 VM 创建的凭据登录。

3.  在 RDP 会话中，将以下两个文件加载 (拖放，如) 所示：

    -   `edvs2017.exe`，企业服务器安装文件。

    -   `mflic`，在不)  (，企业开发人员将无法加载相应的许可证文件。

4.  双击 **edvs2017.exe** 文件以开始安装。 在第一个窗口中，选择安装位置并接受最终用户许可协议。 如果需要，请选择 " **安装 Rumba 9.5** " 以安装此终端模拟器，你可能需要这样做。

    ![适用于 Visual Studio 2017 的微企业开发人员安装对话框](media/install-image-5.png)

5.  安装完成后，将显示以下消息：

    ![安装成功消息](media/install-image-6.png)

6.  像对企业服务器一样，启动微版许可证管理器。 选择 " **开始** \> **微集中" "许可证管理器** \> **许可证管理**"，然后单击 " **安装** " 选项卡。

7.  选择要上传的许可证格式类型：许可证文件或16个字符的许可代码。 例如，对于文件，在 " **许可证文件**" 中，浏览到 `mflic` 以前上传到 VM 的文件，然后选择 "  **安装许可证**"。

    !["微重点许可管理" 对话框](media/install-image-7.png)

部署企业开发人员时，在 Azure 上部署微侧重点开发和测试环境已完成！

**后续步骤**

-   [设置 BankDemo 应用程序](./demo.md)

-   [在 Docker 容器中运行企业服务器](./run-enterprise-server-container.md)

-   [大型机应用程序迁移](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
