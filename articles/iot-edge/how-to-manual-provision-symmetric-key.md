---
title: 通过对称密钥进行设置-Azure IoT Edge |Microsoft Docs
description: 安装完成后，使用对称密钥预配一个 IoT Edge 设备，并使用其连接字符串并向 IoT 中心进行身份验证
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: kgremban
ms.openlocfilehash: f5371539c1b45c14b519729c7c07003bf74847a0
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92043861"
---
# <a name="set-up-an-azure-iot-edge-device-with-symmetric-key-authentication"></a>设置 Azure IoT Edge 设备进行对称密钥身份验证

本文提供在 IoT 中心注册新 IoT Edge 设备并将设备配置为使用对称密钥进行身份验证的步骤。

本文中的步骤逐步讲解称为手动设置的过程，在此过程中，将每个设备手动连接到其 IoT 中心。 另一种方法是使用 IoT 中心设备预配服务进行自动预配，在有许多设备进行预配时，这会很有用。

<!--TODO: Add auto-provision info/links-->

对于手动设置，您可以使用两个选项来对 IoT Edge 设备进行身份验证：

* **对称密钥**：在 IoT 中心创建新设备标识时，服务将创建两个密钥。 将其中一个密钥放置在设备上，并在进行身份验证时向 IoT 中心提供密钥。

  此身份验证方法的启动速度更快，但并不安全。

* **X.509 自签名**：创建两个 x.509 标识证书，并将其放置在设备上。 当你在 IoT 中心内创建新的设备标识时，将提供两个证书的指纹。 当设备向 IoT 中心进行身份验证时，它会显示其证书，IoT 中心可以验证它们是否与指纹匹配。

  此身份验证方法更加安全，建议用于生产方案。

本文逐步讲解使用对称密钥身份验证的注册和预配过程。 如果要了解如何使用 x.509 证书设置设备，请参阅 [使用 x.509 证书身份验证设置 Azure IoT Edge 设备](how-to-manual-provision-x509.md)。

## <a name="prerequisites"></a>必备条件

在按照本文中的步骤执行操作之前，应该已在设备上安装了 IoT Edge 运行时。 否则，请按照 [安装或卸载 Azure IoT Edge 运行时](how-to-install-iot-edge.md)中的步骤操作。

## <a name="register-a-new-device"></a>注册新设备

连接到 IoT 中心的每个设备都有一个设备 ID，用于跟踪云到设备或设备到云的通信。 使用其连接信息配置设备，其中包括 IoT 中心主机名、设备 ID 和设备用于向 IoT 中心进行身份验证的信息。

对于对称密钥身份验证，会在连接字符串中收集此信息，可以从 IoT 中心检索该 *连接字符串* ，然后将其放在 IoT Edge 设备上。

可以使用多个工具在 IoT 中心内注册新的 IoT Edge 设备，并检索其连接字符串，具体取决于你的偏好。

# <a name="portal"></a>[门户](#tab/azure-portal)

### <a name="prerequisites-for-the-azure-portal"></a>Azure 门户的必备组件

Azure 订阅中的免费或标准 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>在 Azure 门户中创建 IoT Edge 设备

在 Azure 门户的 IoT 中心，IoT Edge 设备的创建和管理独立于不支持 Edge 的 IoT 设备。

1. 登录 [Azure 门户](https://portal.azure.com)，导航到 IoT 中心。

1. 在左窗格中，从菜单中选择 " **IoT Edge** "，然后选择 " **添加 IoT Edge 设备**"。

   ![从 Azure 门户添加 IoT Edge 设备](./media/how-to-manual-provision-symmetric-key/portal-add-iot-edge-device.png)

1. 在 " **创建设备** " 页上，提供以下信息：

   * 创建描述性设备 ID。
   * 选择“对称密钥”作为身份验证类型。
   * 使用默认设置自动生成身份验证密钥并将新设备连接到中心。

1. 选择“保存”。 

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>在 Azure 门户中查看 IoT Edge 设备

所有连接到 IoT 中心并已启用 Edge 的设备都列在 **IoT Edge** 页上。

![使用 Azure 门户查看 IoT 中心内的所有 IoT Edge 设备](./media/how-to-manual-provision-symmetric-key/portal-view-devices.png)

### <a name="retrieve-the-connection-string-in-the-azure-portal"></a>在 Azure 门户中检索连接字符串

如果已准备好设置设备，则需要连接字符串，该字符串使用物理设备在 IoT 中心内的标识链接该设备。

1. 在门户的 **IoT Edge** 页中，单击 IoT Edge 设备列表中的设备 ID。
2. 复制“主连接字符串”或“辅助连接字符串”的值   。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="prerequisites-for-visual-studio-code"></a>Visual Studio Code 的必备组件

* Azure 订阅中的免费或标准[IoT 中心](../iot-hub/iot-hub-create-through-portal.md)
* [Visual Studio Code](https://code.visualstudio.com/)
* 适用于 Visual Studio Code 的 [Azure IoT 工具](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

### <a name="sign-in-to-access-your-iot-hub"></a>登录以访问 IoT 中心

可使用适用于 Visual Studio Code 的 Azure IoT 扩展来执行与 IoT 中心相关的操作。 为了使这些操作顺利执行，你需要登录到 Azure 帐户并选择 IoT 中心。

1. 在 Visual Studio Code 中打开“资源管理器”视图  。
1. 在资源管理器底部，展开“Azure IoT 中心”部分  。

   ![展开“Azure IoT 中心设备”部分](./media/how-to-manual-provision-symmetric-key/azure-iot-hub-devices.png)

1. 单击“Azure IoT 中心”部分标题中的“...”   。 如果没有看到省略号，请单击标题或将鼠标指针悬停在标题上。
1. 选择“选择 IoT 中心”  。
1. 如果尚未登录到 Azure 帐户，请按照相关提示执行此操作。
1. 选择 Azure 订阅。
1. 选择 IoT 中心。

### <a name="create-an-iot-edge-device-with-visual-studio-code"></a>使用 Visual Studio Code 创建 IoT Edge 设备

1. 在 VS Code 资源管理器中，展开“Azure IoT 中心设备”部分  。
1. 单击“Azure IoT 中心设备”部分标题中的“...”   。 如果没有看到省略号，请单击标题或将鼠标指针悬停在标题上。
1. 选择“创建 IoT Edge 设备”  。
1. 在打开的文本框中提供设备 ID。

在输出屏幕中，可以看到命令的结果。 其中显示有设备信息，包括所提供的“deviceId”以及可用于将物理设备连接到 IoT 中心的“connectionString”   。

在输出屏幕中，可以看到命令的结果。 其中显示有设备信息，包括所提供的“deviceId”以及可用于将物理设备连接到 IoT 中心的“connectionString”   。

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>使用 Visual Studio Code 查看 IoT Edge 设备

Visual Studio Code 资源管理器的“Azure IoT 中心”部分中列出了连接到 IoT 中心的所有设备  。 IoT Edge 设备和非 Edge 设备可通过不同的图标加以区别。事实上， **$edgeAgent** 和 **$edgeHub** 模块会部署到每个 IoT Edge 设备。

![使用 VS Code 查看 IoT 中心内的所有 IoT Edge 设备](./media/how-to-manual-provision-symmetric-key/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>通过 Visual Studio Code 检索连接字符串

如果已准备好设置设备，则需要连接字符串，该字符串使用物理设备在 IoT 中心内的标识链接该设备。

1. 右键单击“Azure IoT 中心”部分中的设备 ID  。
1. 选择“复制设备连接字符串”  。

   连接字符串会复制到剪贴板。

还可从右键菜单中选择“获取设备信息”，在输出窗口中查看包括连接字符串在内的所有设备信息  。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="prerequisites-for-the-azure-cli"></a>Azure CLI 的必备组件

* Azure 订阅中的 [IoT 中心](../iot-hub/iot-hub-create-using-cli.md)。
* 环境中的 [Azure CLI](/cli/azure/install-azure-cli)。 Azure CLI 版本至少必须是2.0.70 或更高版本。 请使用 `az --version` 验证版本。 此版本支持 az 扩展命令，并引入了 Knack 命令框架。
* [适用于 Azure CLI 的 IoT 扩展](https://github.com/Azure/azure-iot-cli-extension)。

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>通过 Azure CLI 创建 IoT Edge 设备

使用 [az iot hub device-identity create](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) 命令在 IoT 中心创建新设备标识。 例如：

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

此命令包括三个参数：

* `--device-id` 或 `-d` ：提供 IoT 中心独有的描述性名称。
* `hub-name` 或 `-n` ：提供 IoT 中心的名称。
* `--edge-enabled` 或 `--ee` ：声明设备是 IoT Edge 设备。

   ![az iot hub device-identity create output](./media/how-to-manual-provision-symmetric-key/create-edge-device-cli.png)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>通过 Azure CLI 查看 IoT Edge 设备

使用 [az iot hub device-identity list](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-list) 命令在 IoT 中心查看所有设备。 例如：

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

注册为 IoT Edge 设备的任何设备的 **capabilities.iotEdge** 属性都会设置为 **true**。

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>通过 Azure CLI 检索连接字符串

如果已准备好设置设备，则需要连接字符串，该字符串使用物理设备在 IoT 中心内的标识链接该设备。 使用 [az iot hub device-identity show-connection-string](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-show-connection-string) 命令返回单个设备的连接字符串：

   ```azurecli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

`device-id` 参数的值区分大小写。 不要复制连接字符串两端的引号。

---

## <a name="provision-an-iot-edge-device"></a>预配 IoT Edge 设备

IoT Edge 设备在 IoT 中心具有标识并且可用于身份验证的连接字符串后，你需要使用此信息来预配设备本身。

在 Linux 设备上，可以通过编辑 yaml 文件来提供连接字符串。 在 Windows 设备上，通过运行 PowerShell 脚本来提供连接字符串。

# <a name="linux"></a>[Linux](#tab/linux)

在 IoT Edge 设备上，打开配置文件。

```bash
sudo nano /etc/iotedge/config.yaml
```

查找文件的预配配置，并 **使用连接字符串部分取消注释手动设置配置** 。 

使用 IoT Edge 设备的连接字符串更新 **device_connection_string** 的值。 请确保注释掉任何其他预配部分。请确保 **provisioning:** 行前面没有空格，并且嵌套项缩进了两个空格。

```yml
# Manual provisioning configuration using a connection string
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  dynamic_reprovisioning: false
```

将剪贴板内容粘贴到 Nano `Shift+Right Click` 或按 `Shift+Insert`。

保存并关闭该文件。

   `CTRL + X`, `Y`, `Enter`

在配置文件中输入预配信息后，重启守护程序：

```bash
sudo systemctl restart iotedge
```

# <a name="windows"></a>[Windows](#tab/windows)

1. 在 IoT Edge 设备上，以管理员身份运行 PowerShell。

2. 使用 [IoTEdge](reference-windows-scripts.md#initialize-iotedge) 命令可配置计算机上的 IoT Edge 运行时。 该命令默认为使用 Windows 容器手动预配。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualConnectionString -ContainerOs Windows
   ```

   * 如果使用的是 Linux 容器，请将 `-ContainerOs` 参数添加到标志。 与你在前面运行的命令所选择的容器选项一致 `Deploy-IoTEdge` 。

      ```powershell
      . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
      Initialize-IoTEdge -ContainerOs Linux
      ```

   * 如果已将 IoTEdgeSecurityDaemon.ps1 脚本下载到设备进行脱机或特定版本安装，请务必引用该脚本的本地副本。

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. 出现提示时，请提供在上一部分中检索到的设备连接字符串。 设备连接字符串将物理设备与 IoT 中心中的设备 ID 相关联，并提供身份验证信息。

   该设备连接字符串采用以下格式（不包括引号）：`HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

手动设置设备时，可以使用其他参数来修改该过程，包括：

* 定向流量，使其通过代理服务器
* 声明特定的 edgeAgent 容器映像，并在其位于私有注册表中时提供凭据

有关这些其他参数的详细信息，请参阅 [适用于 Windows 上的 IoT Edge 的 PowerShell 脚本](reference-windows-scripts.md)。

---

[!INCLUDE [Verify and troubleshoot installation](../../includes/iot-edge-verify-troubleshoot-install.md)]

## <a name="next-steps"></a>后续步骤

继续 [部署 IoT Edge 模块](how-to-deploy-modules-portal.md) ，了解如何将模块部署到设备上。