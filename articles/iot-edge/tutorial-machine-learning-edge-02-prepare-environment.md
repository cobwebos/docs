---
title: 教程：设置环境 - Azure IoT Edge 上的机器学习
description: 教程：准备环境，以便开发和部署用于在边缘实现机器学习的模块。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 1/23/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9809a9b4d644a1fa11fe9cce1cbd35b037206b29
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2020
ms.locfileid: "78944292"
---
# <a name="tutorial-set-up-an-environment-for-machine-learning-on-iot-edge"></a>教程：在 IoT Edge 上设置机器学习环境

> [!NOTE]
> 有一系列教程介绍如何在 IoT Edge 上使用 Azure 机器学习，本文是其中的一篇。 如果你是直接转到本文的，建议从本系列的[第一篇文章](tutorial-machine-learning-edge-01-intro.md)开始，以获得最佳学习效果。

本文将帮助你准备好用于开发和部署的环境。 首先，在开发计算机上设置好所有必需工具。 然后，在 Azure 中创建必要的云资源。

## <a name="set-up-the-development-vm"></a>设置开发 VM

此步骤通常由云开发人员执行。 其中一些软件也可能对数据科学家有所帮助。

我们创建了一个 PowerShell 脚本，用于创建已配置多个先决条件的 Azure 虚拟机。 创建的 VM 必须能够处理[嵌套虚拟化](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization)，因此我们要选择 [Standard_D8s_v3](../virtual-machines/dv3-dsv3-series.md) 虚拟机大小。

为开发 VM 设置以下项：

* Windows 10
* [Chocolatey](https://chocolatey.org/)
* [适用于 Windows 的 Docker Desktop](https://www.docker.com/products/docker-desktop)
* [Git for Windows](https://gitforwindows.org/)
* [适用于 Windows 的 Git 凭据管理器](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)
* [.NET Core SDK](https://dotnet.microsoft.com/)
* [Python 3](https://www.python.org/)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.1.0)
* [VS Code Extensions](https://marketplace.visualstudio.com/search?target=VSCode)
  * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
  * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
  * [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
  * [PowerShell](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)

开发人员 VM 并非绝对必要 - 所有开发工具都可以在本地计算机上运行。 但是，强烈建议使用 VM，以确保公平的竞争环境。

创建和配置虚拟机大约需要 30 分钟的时间。

1. 将[机器学习和 IoT Edge](https://github.com/Azure-Samples/IoTEdgeAndMlSample) 示例存储库克隆或下载到本地计算机。

1. 以管理员身份打开 PowerShell，导航到代码所下载到的根目录中的 **\IoTEdgeAndMlSample\DevVM** 目录。 我们将源的根目录称为 `srcdir`。

    ```powershell
    cd c:\srcdir\IoTEdgeAndMlSample\DevVM
    ```

   DevVM 目录包含创建适合完成本教程的 Azure 虚拟机所需的文件。

1. 运行以下命令，以允许执行脚本。 系统出现提示时，请选择“全部同意”  。

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

1. 运行 Create-AzureDevVM.ps1。

    ```powershell
    .\Create-AzureDevVm.ps1
    ```

    系统出现提示时，请提供以下信息：

    * **Azure 订阅 ID**：你的订阅 ID，可在门户中的“[Azure 订阅](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)”中找到。
    * **资源组名称**：Azure 中新的或现有的资源组的名称。
    * **位置**：选择将在其中创建虚拟机的 Azure 位置。 例如“美国西部 2”或“北欧”。 有关详细信息，请参阅 [Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。
    * **用户名**：为 VM 的管理员帐户提供一个易记的名称。
    * **密码**：为 VM 的管理员帐户设置密码。

   脚本在执行以下步骤时，会运行几分钟：

    1. 安装 [Azure PowerShell Az 模块](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.1.0)。
    1. 系统会提示你登录到 Azure。
    1. 确认用于创建 VM 的信息。 按 **y** 或 **Enter** 继续操作。
    1. 创建资源组（如果不存在）。
    1. 部署虚拟机。
    1. 在 VM 上启用 Hyper-V。
    1. 安装开发所需的软件并克隆示例存储库。
    1. 重启 VM。
    1. 在桌面上创建 RDP 文件以连接到 VM。

   如果系统提示输入 VM 的名称以将其重启，可以从脚本输出中复制其名称。 输出中还显示了用于连接到 VM 的 RDP 文件的路径。

### <a name="set-auto-shutdown-schedule"></a>设置自动关机计划

为了帮助降低成本，创建开发 VM 时设置了自动关闭计划，将在太平洋标准时间 (PST) 19:00 自动关闭。 根据你的位置和计划，可能需要更新此设置。 若要更新关机计划，请执行以下操作：

1. 在 Azure 门户中，导航到脚本创建的 VM。

1. 在左侧窗格菜单中的“操作”下，选择“自动关闭”。  

1. 根据需要调整“计划的关闭”和“时区”，然后选择“保存”。   

## <a name="connect-to-the-development-vm"></a>连接到开发 VM

我们现已创建 VM，需要安装完成教程所需软件。

1. 在桌面上双击脚本创建的 RDP 文件。

1. 系统将显示一个对话框，其中显示远程连接的发布者未知。 这是正常情况，因此请选择“连接”。 

1. 提供创建 VM 时所提供的管理员密码，然后单击“确定”。 

1. 系统将提示你是否接受 VM 的证书。 请选择“是”。 

## <a name="install-visual-studio-code-extensions"></a>安装 Visual Studio Code 扩展

现在你已连接到开发计算机，请为 Visual Studio Code 添加一些有用的扩展，以使开发体验更加轻松。

1. 连接到开发 VM，打开 PowerShell 窗口，然后导航到 **C:\source\IoTEdgeAndMlSample\DevVM** 目录。 此目录是创建 VM 的脚本所创建的。

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\DevVM
    ```

1. 运行以下命令，以允许执行脚本。 系统出现提示时，请选择“全部同意”  。

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

1. 运行 Visual Studio Code 扩展脚本。

    ```powershell
    .\Enable-CodeExtensions.ps1
    ```

1. 该脚本将运行几分钟以安装以下 VS 代码扩展：

    * Azure IoT Tools
    * Python
    * C#
    * Docker
    * PowerShell

## <a name="set-up-iot-hub-and-storage"></a>设置 IoT 中心和存储

这些步骤通常由云开发人员执行。

Azure IoT 中心是任何 IoT 应用程序的核心，它会处理 IoT 设备和云之间的安全通信。 它是 IoT Edge 机器学习解决方案运行的主要协调点。

* IoT 中心使用路由将来自 IoT 设备的传入数据直接传送到其他下游服务。 我们将利用 IoT 中心路由将设备数据发送到 Azure 存储。 Azure 机器学习使用 Azure 存储中的设备数据来训练剩余的使用寿命 (RUL) 分类器。

* 在本教程的后面部分，我们将使用 IoT 中心来配置和管理 Azure IoT Edge 设备。

在本部分中，你要使用脚本来创建 Azure IoT 中心和 Azure 存储帐户。 然后，在 Azure 门户中配置路由，将中心收到的数据转发到 Azure 存储容器。 完成这些步骤大约需要 10 分钟。

1. 连接到开发 VM，打开 PowerShell 窗口，然后导航到 **IoTHub** 目录。

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\IoTHub
    ```

1. 运行创建脚本。 使用创建开发 VM 时所用的订阅 ID、位置和资源组值。

    ```powershell
    .\New-HubAndStorage.ps1 -SubscriptionId <subscription id> -Location <location> -ResourceGroupName <resource group>
    ```

    * 系统会提示登录到 Azure。
    * 该脚本会确认信息来创建中心和存储帐户。 按 **y** 或 **Enter** 继续操作。

运行该脚本需要大约 2 分钟。 完成后，脚本将输出 IoT 中心和存储帐户的名称。

## <a name="review-route-to-storage-in-iot-hub"></a>查看 IoT 中心存储的路由

我们在上一节中运行脚本以创建 IoT 中心时，该脚本还创建了自定义终结点和路由。 IoT 中心路由包含查询表达式和终结点。 如果消息与表达式匹配，则数据将沿路由发送到关联的终结点。 终结点可以是事件中心、服务总线队列和主题。 在本例中，终结点是存储帐户中的 Blob 容器。 让我们使用 Azure 门户来查看脚本创建的路由。

1. 打开 [Azure 门户](https://portal.azure.com)，并转到本教程所用的资源组。

1. 在资源列表中，选择脚本创建的 IoT 中心。 其名称以随机字符结尾，例如 `IotEdgeAndMlHub-jrujej6de6i7w`。

1. 在左侧窗格菜单中的“消息”下，选择“消息路由”。  

1. 在“消息路由”页上，选择“自定义终结点”选项卡。  

1. 展开“存储”部分： 

   ![检查 turbofandevicestorage 是否在自定义终结点列表中](media/tutorial-machine-learning-edge-02-prepare-environment/custom-endpoints.png)

   我们看到，**turbofandevicestorage** 包含在自定义终结点列表中。 请注意有关此终结点的以下特征：

   * 它指向你创建的名为 `devicedata` 的 Blob 存储容器，如“容器名称”所示。 
   * 其“文件名格式”使用分区作为名称中的最后一个元素。  我们发现，对于将在本教程后面部分使用 Azure Notebooks 执行的文件操作，此格式更为方便。

1. 选择“路由”选项卡  。

1. 选择名为“turbofanDeviceDataToStorage”的路由  。

1. 在“路由详细信息”页上，请注意，路由的终结点是 **turbofanDeviceStorage** 终结点。 

   ![查看有关 turbofanDeviceDataToStorage 路由的详细信息](media/tutorial-machine-learning-edge-02-prepare-environment/route-details.png)

1. 查看设置为“true”的“路由查询”   。 此设置意味着所有设备遥测消息都将与此路由匹配，因此所有消息将发送到 **turbofanDeviceStorage** 终结点。

1. 由于我们未进行任何编辑，因此请直接关闭此页。

## <a name="next-steps"></a>后续步骤

在本文中，我们创建了 IoT 中心，并配置了 Azure 存储帐户的路由。 接下来，我们将通过 IoT 中心将来自一组模拟设备的数据发送到存储帐户。 在本教程的后面部分，在配置 IoT Edge 设备和模块之后，我们将再次关注路由并详细了解路由查询。

有关 IoT Edge 上的机器学习教程这部分所涵盖的步骤详细信息，请参阅：

* [Azure IoT 基础](https://docs.microsoft.com/azure/iot-fundamentals/)
* [使用 IoT 中心配置消息路由](../iot-hub/tutorial-routing.md)
* [使用 Azure 门户创建 IoT 中心](../iot-hub/iot-hub-create-through-portal.md)

继续阅读下一篇文章，创建模拟设备以进行监视。

> [!div class="nextstepaction"]
> [生成设备数据](tutorial-machine-learning-edge-03-generate-data.md)
