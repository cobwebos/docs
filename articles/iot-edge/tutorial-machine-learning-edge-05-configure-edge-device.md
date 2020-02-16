---
title: 教程：配置 IoT Edge 设备 - Azure IoT Edge 上的机器学习
description: 在本教程中，我们将运行 Linux 的 Azure 虚拟机配置为充当透明网关的 Azure IoT Edge 设备。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 2/5/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ab3ed567d34c6284959f7875bb121ced4770d65e
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77133318"
---
# <a name="tutorial-configure-an-iot-edge-device"></a>教程：配置 IoT Edge 设备

> [!NOTE]
> 有一系列教程介绍如何在 IoT Edge 上使用 Azure 机器学习，本文是其中的一篇。 如果你是直接转到本文的，建议从本系列的[第一篇文章](tutorial-machine-learning-edge-01-intro.md)开始，以获得最佳学习效果。

在本文中，我们会将一个运行 Linux 的 Azure 虚拟机配置为充当透明网关的 IoT Edge 设备。 借助透明网关配置，设备在不知道网关存在的情况下就能通过网关连接到 Azure IoT 中心。 同时，与 Azure IoT 中心内的设备交互的用户也察觉不到中间网关设备。 最后，我们将通过向透明网关添加 IoT Edge 模块，向我们的系统添加边缘分析功能。

本文的步骤通常由云开发人员执行。

## <a name="create-certificates"></a>创建证书

充当网关的设备必须能够安全地连接到下游设备。 Azure IoT Edge 允许使用公钥基础结构 (PKI) 在设备之间建立安全连接。 在这种情况下，我们可以将下游 IoT 设备连接到充当透明网关的 IoT Edge 设备。 要维持合理的安全性，下游设备应确认 IoT Edge 设备的标识。 要详细了解 IoT Edge 设备如何使用证书，请查看 [Azure IoT Edge 证书使用详细信息](iot-edge-certs.md)。

在此部分中，我们要使用到时会生成和运行的 Docker 映像来创建自签名证书。 我们选择用 Docker 映像来完成此步骤的原因是，它显著减少了在 Windows 开发计算机上创建证书所需的步骤数。 请参阅[创建演示证书以测试 IoT Edge 设备功能](how-to-create-test-certificates.md)，了解通过 Docker 映像实现自动化的操作。

1. 登录到开发 VM。

2. 使用路径和名称 `c:\edgeCertificates` 创建一个新的文件夹。

3. 从 Windows 的“开始”菜单启动用于 Windows 的 Docker  。

4. 打开 Visual Studio Code。

5. 选择“文件” > “打开文件夹...”，再选择 C:\\source\\IoTEdgeAndMlSample\\CreateCertificates    。

6. 在“资源管理器”窗格中，右键单击 **dockerfile** 并选择“生成映像”  。

7. 在对话框中，接受映像名称和标记的默认值：**createcertificates: latest**。

    ![在 Visual Studio Code 中创建证书](media/tutorial-machine-learning-edge-05-configure-edge-device/create-certificates.png)

8. 等待生成完成。

    > [!NOTE]
    > 你可能会看到一个警告，提示缺少公钥。 可放心忽略此警告。 同样，你将看到一个安全警告，它建议你检查/重置映像上的权限 - 对此映像而言，可放心忽略。

9. 在 Visual Studio Code 终端窗口中，运行 createcertificates 容器。

    ```cmd
    docker run --name createcertificates --rm -v c:\edgeCertificates:/edgeCertificates createcertificates /edgeCertificates
    ```

10. Docker 将提示提供对 c:\\ 驱动器的访问权限  。 选择“共享”  。

11. 在系统提示时提供凭据。

12. 容器运行完毕后，立即检查 c:\\edgeCertificates 中是否存在以下文件  ：

    * c:\\edgeCertificates\\certs\\azure-iot-test-only.root.ca.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device-full-chain.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pfx
    * c:\\edgeCertificates\\private\\new-edge-device.key.pem

## <a name="upload-certificates-to-azure-key-vault"></a>将证书上传到 Azure Key Vault

为安全存储证书并使其可通过多台设备访问，我们将证书上传到 Azure Key Vault 中。 如在上述列表中看到的那样，我们有两种类型的证书文件：PFX 和 PEM。 我们将 PFX 看作 Key Vault 证书，将其上传到 Key Vault。 PEM 文件是纯文本形式，我们将其视为 Key Vault 机密。 我们之前通过运行 [Azure Notebook](tutorial-machine-learning-edge-04-train-model.md#run-azure-notebooks).创建了 Azure 机器学习工作区，现在我们将使用与该工作区关联的 Key Vault。

1. 从 [Azure 门户](https://portal.azure.com)导航到 Azure 机器学习工作区。

2. 在 Azure 机器学习工作区的概述页中，找到“Key Vault”的名称  。

    ![复制 Key Vault 的名称](media/tutorial-machine-learning-edge-05-configure-edge-device/find-key-vault-name.png)

3. 在部署计算机上，将证书上传到 Key Vault。 将 \<subscriptionId\> 和 \<keyvaultname\> 替换为你的资源信息   。

    ```powershell
    c:\source\IoTEdgeAndMlSample\CreateCertificates\upload-keyvaultcerts.ps1 -SubscriptionId <subscriptionId> -KeyVaultName <keyvaultname>
    ```

4. 出现提示时，请登录到 Azure。

5. 脚本将运行几分钟时间，其输出中列出新的 Key Vault 条目。

    ![Key Vault 脚本输出](media/tutorial-machine-learning-edge-05-configure-edge-device/key-vault-entries-output.png)

## <a name="create-iot-edge-device"></a>创建 IoT Edge 设备

要将 Azure IoT Edge 设备连接到 IoT 中心，首先需在中心内创建该设备的标识。 我们从云端设备标识中提取连接字符串，用它在 IoT Edge 设备上配置运行时。 在所配置的设备连接到中心后，我们就能够部署模块并发送消息了。 我们还能更改 IoT 中心内相应的设备标识，进而更改物理 IoT Edge 设备的配置。

在本教程中，我们会使用 Visual Studio Code 创建新的设备标识。 你可通过 [Azure 门户](how-to-register-device.md#register-in-the-azure-portal)或 [Azure CLI](how-to-register-device.md#register-with-the-azure-cli) 完成这些步骤。

1. 在开发计算机上，打开 Visual Studio Code。

2. 从 Visual Studio Code 资源管理器视图中，展开“Azure IoT 中心”框架  。

3. 单击省略号并选择“创建 IoT Edge 设备”  。

4. 向设备提供一个名称。 为方便起见，我们使用名称 **aaTurbofanEdgeDevice**，以便它排在所列出设备的顶部。

5. 新设备将在设备列表中显示。

    ![在 VS Code 资源管理器中查看新的 aaTurbofanEdgeDevice](media/tutorial-machine-learning-edge-05-configure-edge-device/iot-hub-devices-list.png)

## <a name="deploy-azure-virtual-machine"></a>部署 Azure 虚拟机

我们使用来自 Azure 市场的 [Azure IoT Edge on Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) 来创建本教程所用的 IoT Edge 设备。 Azure IoT Edge on Ubuntu 映像会在启动时安装最新的 Azure IoT Edge 运行时及其依赖项。 我们使用 PowerShell 脚本 `Create-EdgeVM.ps1`、Resource Manager 模板 `IoTEdgeVMTemplate.json` 和 shell 脚本 `install packages.sh` 来部署 VM。

### <a name="enable-programmatic-deployment"></a>启用编程部署

若要在脚本化部署中使用市场提供的映像，我们需要为该映像启用编程部署。

1. 登录到 Azure 门户。

1. 选择“所有服务”  。

1. 在搜索栏中，输入并选择“市场”  。

1. 在市场搜索栏中，输入并选择“Azure IoT Edge on Ubuntu”  。

1. 选择**入门**超链接以编程方式进行部署。

1. 选择“启用”按钮，然后选择“保存”   。

    ![为 VM 启用编程部署](media/tutorial-machine-learning-edge-05-configure-edge-device/deploy-ubuntu-vm.png)

1. 你将看到一则成功通知。

### <a name="create-virtual-machine"></a>创建虚拟机

接下来，运行脚本来为 IoT Edge 设备创建虚拟机。

1. 打开 PowerShell 窗口，导航到 EdgeVM 目录  。

    ```powershell
    cd c:\source\IoTEdgeAndMlSample\EdgeVM
    ```

2. 运行脚本来创建虚拟机。

    ```powershell
    .\Create-EdgeVm.ps1
    ```

3. 出现提示时，为每个参数提供值。 建议在这整个教程中，所有资源的订阅、资源组和位置均保持一致。

    * **Azure 订阅 ID**：参见 Azure 门户
    * **资源组名称**：在本教程中，使用易记名称对资源进行分组整理
    * **位置**：将在其中创建虚拟机的 Azure 位置， 例如 westus2 或 northeurope。 有关详细信息，请查看所有 [Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。
    * **AdminUsername**：管理员帐户的名称，你将用它来登录虚拟机
    * **AdminPassword**：虚拟机上为 AdminUsername 设置的密码

4. 要使脚本能够设置 VM，你需要使用与当前所用的 Azure 订阅关联的凭据登录到 Azure。

5. 该脚本会确认此信息来创建 VM。 选择“y”或 Enter 继续操作   。

6. 脚本在执行以下步骤时，会运行几分钟：

    * 创建资源组（如果尚不存在）
    * 创建虚拟机
    * 为端口 22 (SSH)、5671 (AMQP)、5672 (AMPQ) 和 443 (SSL) 添加 VM 的 NSG 例外
    * 安装 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-apt?view=azure-cli-latest)

7. 该脚本会输出 SSH 连接字符串用于连接到 VM。 复制连接字符串供下一步使用。

    ![复制 VM 的 SSH 连接字符串](media/tutorial-machine-learning-edge-05-configure-edge-device/vm-ssh-connection-string.png)

## <a name="connect-to-your-iot-edge-device"></a>连接到 IoT Edge 设备

下面几个部分会配置我们创建的 Azure 虚拟机。 首先是连接到虚拟机。

1. 打开命令提示符，粘贴从脚本输出中复制的 SSH 连接字符串。 根据你在上一部分中提供给 PowerShell 脚本的值，输入自己的用户名、前缀和区域信息。

    ```cmd
    ssh -l <username> iotedge-<suffix>.<region>.cloudapp.azure.com
    ```

2. 当提示验证主机的真实性时，请键入“是”并选择 Enter   。

3. 出现提示时，请提供密码。

4. Ubuntu 会显示一条欢迎消息，然后你应会看到一个与 `<username>@<machinename>:~$` 类似的提示。

## <a name="download-key-vault-certificates"></a>下载 Key Vault 证书

在本文的前面部分中，我们已将证书上传到 Key Vault，使其可供 IoT Edge 设备和叶设备使用。 叶设备是一个下游设备，它使用 IoT Edge 设备作为网关与 IoT 中心进行通信。

在本文稍后部分，我们将讲解叶设备。 在此部分中，请将证书下载到 IoT Edge 设备。

1. 从 Linux 虚拟机上的 SSH 会话，使用 Azure CLI 登录到 Azure。

    ```bash
    az login
    ```

1. 系统将提示打开浏览器转到 <https://microsoft.com/devicelogin> 并提供唯一代码。 你可在本地计算机上执行这些步骤。 完成身份验证后，关闭浏览器窗口。

1. 如果你成功通过身份验证，Linux VM 将进行登录并列出你的 Azure 订阅。

1. 设置要对 Azure CLI 命令使用的 Azure 订阅。

    ```bash
    az account set --subscription <subscriptionId>
    ```

1. 在 VM 上为证书创建一个目录。

    ```bash
    sudo mkdir /edgeMlCertificates
    ```

1. 下载存储在密钥保管库中的证书：new-edge-device-full-chain.cert.pem、new-edge-device.key.pem 和 azure-iot-test-only.root.ca.cert.pem

    ```bash
    key_vault_name="<key vault name>"
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-full-chain-cert-pem -f /edgeMlCertificates/new-edge-device-full-chain.cert.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-key-pem -f /edgeMlCertificates/new-edge-device.key.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name azure-iot-test-only-root-ca-cert-pem -f /edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem
    ```

## <a name="update-the-iot-edge-device-configuration"></a>更新 IoT Edge 设备配置

IoT Edge 运行时使用 `/etc/iotedge/config.yaml` 文件来保留其配置。 我们需要更新此文件中的三部分信息：

* **设备连接字符串**：IoT 中心内来自此设备的标识的连接字符串
* **证书**：用于通过下游设备建立的连接的证书
* **主机名**：VM IoT Edge 设备的完全限定的域名 (FQDN)。

我们用来创建 IoT Edge VM 的 Azure IoT Edge on Ubuntu 映像随附了一个 shell 脚本，它会使用连接字符串更新 config.yaml  。

1. 在 Visual Studio Code 中，右键单击 IoT Edge 设备，然后选择“复制设备连接字符串”  。

    ![复制 Visual Studio Code 中的连接字符串](media/tutorial-machine-learning-edge-05-configure-edge-device/copy-device-connection-string-command.png)

2. 在 SSH 会话中运行命令，使用设备连接字符串更新 config.yaml 文件。

    ```bash
    sudo /etc/iotedge/configedge.sh "<your_iothub_edge_device_connection_string>"
    ```

接下来，我们将直接编辑 config.yaml 来更新证书和主机名。

1. 打开 config.yaml 文件。

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

2. 通过删除前导 `#` 更新 config.yaml 的证书部分，使文件如下所示：

    ```yaml
    certificates:
      device_ca_cert: "/edgeMlCertificates/new-edge-device-full-chain.cert.pem"
      device_ca_pk: "/edgeMlCertificates/new-edge-device.key.pem"
      trusted_ca_certs: "/edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem"
    ```

    请确保“证书:”  行前面没有空格，并且每个嵌套证书都缩进了两个空格。

    在 nano 中右键单击会将剪贴板的内容粘贴到当前游标位置。 要替换字符串，可使用键盘箭头导航到要替换的字符串，删除该字符串，然后右键单击来粘贴缓冲区中的内容。

3. 在 Azure 门户中，导航到你的虚拟机。 复制“概述”部分中的 DNS 名称（计算机的 FQDN）  。

4. 将 FQDN 粘贴到 config.yml 的主机名部分。 确保该名称全部小写。

    ```yaml
    hostname: '<machinename>.<region>.cloudapp.azure.com'
    ```

5. 保存并关闭文件（(`Ctrl + X`、`Y`、`Enter`).）。

6. 重启 iotedge 守护程序。

    ```bash
    sudo systemctl restart iotedge
    ```

7. 检查 IoT Edge 守护程序的状态（在命令后键入“:q”可退出）。

    ```bash
    systemctl status iotedge
    ```

8. 如果在状态中看到错误（带有“\[ERROR\]”前缀且标有颜色的文本），可查看守护程序日志了解详细错误信息。

    ```bash
    journalctl -u iotedge --no-pager --no-full
    ```

## <a name="next-steps"></a>后续步骤

我们刚才完成了将 Azure VM 配置为 Azure IoT Edge 透明网关的操作。 我们首先创建了测试证书并将它们上传到 Azure Key Vault。 接下来，我们使用了脚本和 Resource Manager 模板，通过 Azure 市场中的“Ubuntu Server 16.04 LTS + Azure IoT Edge 运行时”映像部署了 VM。 在 VM 启动并运行后，我们通过 SSH 进行了连接，登录到 Azure 并从 Key Vault 下载了证书。 我们通过更新 config.yaml 文件对 IoT Edge 运行时的配置进行了多处更新。

有关详细信息，请参阅[如何将 IoT Edge 设备用作网关](iot-edge-as-gateway.md)和[将 IoT Edge 设备配置为充当透明网关](how-to-create-transparent-gateway.md)。

请继续学习下一篇文章，了解如何构建 IoT Edge 模块。

> [!div class="nextstepaction"]
> [创建并部署自定义 IoT Edge 模块](tutorial-machine-learning-edge-06-custom-modules.md)
