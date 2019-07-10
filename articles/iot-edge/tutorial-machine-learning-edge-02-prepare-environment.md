---
title: 设置环境 - Azure IoT Edge 上的机器学习 | Microsoft Docs
description: 准备环境，以便开发和部署用于在边缘实现机器学习的模块。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: fd3b5766ec2bd8d1babf847598f1fbe5b6511ce7
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67432830"
---
# <a name="tutorial-set-up-an-environment-for-machine-learning-on-iot-edge"></a>教程：在 IoT Edge 上设置机器学习环境

> [!NOTE]
> 有一系列教程介绍如何在 IoT Edge 上使用 Azure 机器学习，本文是其中的一篇。 如果你是直接转到本文的，建议从本系列的[第一篇文章](tutorial-machine-learning-edge-01-intro.md)开始，以获得最佳学习效果。

本文出自 IoT Edge 上的端到端 Azure 机器学习教程，可帮助你准备环境以进行开发和部署。 首先，在开发计算机上设置好所有必需工具。 然后，在 Azure 中创建必要的云资源。

## <a name="set-up-a-development-machine"></a>设置开发计算机

此步骤通常由云开发人员执行。 其中一些软件也可能对数据科学家有所帮助。

在本文中，我们将执行各种开发人员任务，包括编码、编译、配置和部署 IoT Edge 模块和 IoT 相关设备。 为了便于使用，我们创建了一个 PowerShell 脚本，用于创建已配置多个先决条件的 Azure 虚拟机。 创建的 VM 必须能够处理[嵌套虚拟化](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization)，因此我们要选择 DS8V3 虚拟机大小。

为开发 VM 设置以下项：

* Windows 10
* [Chocolatey](https://chocolatey.org/)
* [适用于 Windows 的 Docker Desktop](https://www.docker.com/products/docker-desktop)
* [Git for Windows](https://gitforwindows.org/)
* [适用于 Windows 的 Git 凭据管理器](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)
* [.Net Core SDK](https://dotnet.microsoft.com/)
* [Python 3](https://www.python.org/)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.1.0)
* [VS Code Extensions](https://marketplace.visualstudio.com/search?target=VSCode)
  * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
  * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  * [C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
  * [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
  * [PowerShell](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)

开发人员 VM 并非绝对必要 - 所有开发工具都可以在本地计算机上运行。 但是，强烈建议使用 VM，以确保公平的竞争环境。

创建和配置虚拟机大约需要 30 分钟的时间。

### <a name="get-the-script"></a>获取脚本

克隆或下载[机器学习和 IoT Edge](https://github.com/Azure-Samples/IoTEdgeAndMlSample) 示例存储库中的 PowerShell 脚本。

### <a name="create-an-azure-virtual-machine"></a>创建 Azure 虚拟机

DevVM 目录包含创建适合完成本教程的 Azure 虚拟机所需的文件。

1. 以管理员身份打开 Powershell 并导航到已下载代码的目录。 我们将源的根目录称为 `<srcdir>`。

    ```powershell
    cd <srcdir>\IoTEdgeAndMlSample\DevVM
    ```

2. 运行以下命令，以允许执行脚本。 系统出现提示时，请选择“全部同意”  。

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

3. 从该目录运行 Create-AzureDevVM.ps1。

    ```powershell
    .\Create-AzureDevVm.ps1
    ```

    * 系统出现提示时，请提供以下信息：
      * **Azure 订阅 ID**：订阅 ID，可以在 Azure 门户中找到
      * **资源组名称**：Azure 中新的或现有的资源组的名称
      * **位置**：选择将在其中创建虚拟机的 Azure 位置。 例如 westus2 或 northeurope。 有关详细信息，请参阅 [Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。
      * **AdminUsername**：为要在虚拟机上创建和使用的管理员帐户提供容易记住的名称。
      * **AdminPassword**：在虚拟机上设置管理员帐户的密码。

    * 如果尚未安装 Azure PowerShell，脚本将安装 [Azure PowerShell Az 模块](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.1.0)

    * 系统会提示登录到 Azure。

    * 该脚本会确认此信息来创建 VM。 按 `y` 或 `Enter` 以继续操作。

脚本在执行以下步骤时，会运行几分钟：

* 创建资源组（如果不存在）
* 部署虚拟机
* 在 VM 上启用 Hyper-V
* 安装开发所需的软件并克隆示例存储库
* 重启 VM
* 在桌面上创建 RDP 文件以连接到 VM

### <a name="set-auto-shutdown-schedule"></a>设置自动关机计划

为了帮助降低成本，创建 VM 时设置了自动关机计划，将在太平洋标准时间 (PST) 19:00 自动关机。 根据你的位置和计划，可能需要更新此时间。 若要更新关机计划，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 在你在上一部分中提供的资源组中，导航到你的虚拟机。

3. 在侧边导航栏上选择“自动关闭”  。

4. 在“计划关闭时间”中输入新的关闭时间或更改“时区”，然后单击“保存”    。

### <a name="connect-and-configure-development-machine"></a>连接并配置开发计算机

我们现已创建 VM，需要安装完成教程所需软件。

#### <a name="start-a-remote-desktop-session"></a>启动远程桌面会话

1. VM 创建脚本在桌面上创建了 RDP 文件。

2. 双击名为“\<Azure VM 名称\>.rdp”的文件  。

3. 系统将显示一个对话框，其中显示远程连接的发布者未知。 单击“不再询问是否连接到此计算机”复选框，然后选择“连接”   。

4. 系统出现提示时，提供运行脚本以设置 VM 时使用的 AdminPassword，然后单击“确定”  。

5. 系统将提示你是否接受 VM 的证书。 选择“不再询问是否连接到此计算机”，然后选择“是”   。

#### <a name="install-visual-studio-code-extensions"></a>安装 Visual Studio Code 扩展

现在你已连接到开发计算机，请为 Visual Studio Code 添加一些有用的扩展，以使开发体验更加轻松。

1. 在 PowerShell 窗口中，导航到 C:\\source\\IoTEdgeAndMlSample\\DevVM  。

2. 允许通过键入内容在虚拟机上执行脚本。

    ```powershell
    Set-ExecutionPolicy Bypass -Scope CurrentUser -Force
    ```

3. 运行该脚本。

    ```powershell
    .\Enable-CodeExtensions.ps1
    ```

4. 该脚本将运行几分钟以安装以下 VS 代码扩展：

    * Azure IoT Tools
    * Python
    * C#
    * Docker
    * PowerShell

## <a name="set-up-iot-hub-and-storage"></a>设置 IoT 中心和存储

这些步骤通常由云开发人员执行。

Azure IoT 中心是所有 IoT 应用程序的核心。 它处理 IoT 设备和云之间的安全通信。 它是 IoT Edge 机器学习解决方案运行的主要协调点。

* IoT 中心使用路由将来自 IoT 设备的传入数据直接传送到其他下游服务。 我们将利用 IoT 中心路由将设备数据发送到 Azure 存储，以便 Azure 机器学习使用这些数据来训练剩余使用寿命 (RUL) 分类器。

* 在本教程的后面部分，我们将使用 IoT 中心来配置和管理 Azure IoT Edge 设备。

在本部分中，你要使用脚本来创建 Azure IoT 中心和 Azure 存储帐户。 然后，使用 Azure 门户来配置路由，将中心接收的数据转发到 Azure 存储 Blob 容器。 完成这些步骤大约需要 10 分钟。

### <a name="create-cloud-resources"></a>创建云资源

1. 在开发计算机上，打开 PowerShell 窗口。

1. 切换到 IoTHub 目录。

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\IoTHub
    ```

1. 运行创建脚本。 使用创建开发 VM 时所用的订阅 ID、位置和资源组值。

    ```powershell
    .\New-HubAndStorage.ps1 -SubscriptionId <subscription id> -Location
    <location> -ResourceGroupName <resource group>
    ```

    * 系统会提示登录到 Azure。
    * 该脚本会确认信息来创建中心和存储帐户。 按 `y` 或 `Enter` 以继续操作。

运行该脚本需要大约 2 分钟。 完成后，脚本将输出中心和存储帐户的名称。

### <a name="review-route-to-storage-in-iot-hub"></a>查看 IoT 中心存储的路由

我们在上一节中运行脚本以创建 IoT 中心时，该脚本还创建了自定义终结点和路由。 IoT 中心路由包含查询表达式和终结点。 如果消息与表达式匹配，则数据将沿路由发送到关联的终结点。 终结点可以是事件中心、服务总线队列和主题。 在本例中，终结点是存储帐户中的 Blob 容器。 让我们使用 Azure 门户来查看脚本创建的路由。

1. 打开 [Azure 门户](https://portal.azure.com)。

1. 从左侧导航栏中选择所有服务，在搜索框中键入 IoT，然后选择“IoT 中心”  。

1. 选择上一步中创建的 IoT 中心。

1. 在 IoT 中心侧边导航栏中，选择“消息路由”  。

1. 消息路由页有两个选项卡：“路由”和“自定义终结点”   。 选择“自定义终结点”选项卡  。

1. 在“Blob 存储”下，选择“turbofanDeviceStorage”   。

1. 请注意，此终结点指向上一步中创建的存储帐户（名为 iotedgeandml\<unique suffix\>）中名为“devicedata”的 Blob 容器   。

1. 另请注意，Blob 文件名格式已由默认格式发生改变，现在，它将分区作为名称中的最后一个元素  。 我们发现，对于将在本教程后面部分使用 Azure Notebooks 执行的文件操作，此格式更为方便。

1. 关闭终结点详细信息边栏选项卡，以返回“消息路由”页  。

1. 选择“路由”选项卡  。

1. 选择名为“turbofanDeviceDataToStorage”的路由  。

1. 请注意，路由的终结点是“turbofanDeviceStorage”自定义终结点  。

1. 查看设置为“true”的路由查询  。 这意味着所有设备遥测消息都将与此路由匹配，因此所有消息都将发送到“turbofanDeviceStorage”终结点  。

1. 关闭路由详细信息。

## <a name="next-steps"></a>后续步骤

在本文中，我们创建了 IoT 中心，并配置了 Azure 存储帐户的路由。 在下一篇文章中，我们将通过 IoT 中心将来自一组模拟设备的数据发送到存储帐户。 在本教程的后面部分，在配置 IoT Edge 设备和模块之后，我们将再次关注路由并详细了解路由查询。

有关 IoT Edge 上的机器学习教程这部分所涵盖的步骤详细信息，请参阅：

* [Azure IoT 基础](https://docs.microsoft.com/azure/iot-fundamentals/)
* [使用 IoT 中心配置消息路由](../iot-hub/tutorial-routing.md)
* [使用 Azure 门户创建 IoT 中心](../iot-hub/iot-hub-create-through-portal.md)

继续阅读下一篇文章，创建模拟设备以进行监视。

> [!div class="nextstepaction"]
> [生成设备数据](tutorial-machine-learning-edge-03-generate-data.md)
