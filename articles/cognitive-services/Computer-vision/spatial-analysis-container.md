---
title: 如何安装和运行空间分析容器-计算机视觉
titleSuffix: Azure Cognitive Services
description: 利用空间分析容器，可以检测人员和距离。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: aahi
ms.openlocfilehash: 52df2ad0dc4c60c24e341a9765e31bcf9776bf5e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91277285"
---
# <a name="install-and-run-the-spatial-analysis-container-preview"></a> (预览中安装并运行空间分析容器) 

利用空间分析容器，你可以分析实时流式处理视频，以了解人员之间的空间关系、移动和物理环境中对象之间的交互。 容器非常适合用于满足特定的安全性和数据管理要求。

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services)
* 拥有 Azure 订阅后，在 Azure 门户中<a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="创建计算机视觉资源"  target="_blank">创建计算机视觉资源 <span class="docon docon-navigate-external x-hidden-focus"></span></a>，获取密钥和终结点。 部署后，单击“转到资源”。
    * 你将需要创建的资源的密钥和终结点来运行空间分析容器。 稍后将使用密钥和终结点。


### <a name="spatial-analysis-container-requirements"></a>空间分析容器要求

若要运行空间分析容器，需要具有 [NVIDIA Tesla T4 GPU](https://www.nvidia.com/en-us/data-center/tesla-t4/)的计算设备。 建议你使用 GPU 加速 [Azure Stack 边缘](https://azure.microsoft.com/products/azure-stack/edge/) ，但该容器在满足最低要求的任何其他台式计算机上运行。 我们会将此设备称为 "主计算机"。

#### <a name="azure-stack-edge-device"></a>[Azure Stack 边缘设备](#tab/azure-stack-edge)

Azure Stack Edge 是一种硬件即服务解决方案，是一种支持 AI 的边缘计算设备，具有网络数据传输功能。 有关详细准备和设置说明，请参阅 [Azure Stack Edge 文档](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-prep)。

#### <a name="desktop-machine"></a>[台式计算机](#tab/desktop-machine)

#### <a name="minimum-hardware-requirements"></a>最低硬件需求

* 4 GB 系统 RAM
* 4 GB 的 GPU RAM
* 8核 CPU
* 1个 NVIDIA Tesla T4 GPU
* 20 GB HDD 空间

#### <a name="recommended-hardware"></a>推荐硬件

* 32 GB 系统 RAM
* 16 GB GPU RAM
* 8核 CPU
* 2个 NVIDIA Tesla T4 Gpu
* 50 GB SSD 空间

在本文中，你将下载并安装以下软件包。 主计算机必须能够运行以下 (请参阅下面的说明) ：

* [Nvidia 图形驱动程序](https://docs.nvidia.com/datacenter/tesla/tesla-installation-notes/index.html) 和 [nvidia CUDA 工具包](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html)
* [NVIDIA MPS](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf) (多进程服务) 的配置。
* [DOCKER CE](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-engine---community-1) 和 [NVIDIA-Docker2](https://github.com/NVIDIA/nvidia-docker) 
* [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) 运行时。

---

| 要求 | 说明 |
|--|--|
| 照相机 | 空间分析容器与特定相机品牌无关。 照相机设备需要：支持 Real-Time 流式处理协议 (RTSP) 和 h.264 编码，主机计算机可以访问该设备，并可通过15FPS 和1080p 分辨率进行流式处理。 |
| Linux OS | [Ubuntu Desktop 18.04 LTS](http://releases.ubuntu.com/18.04/) 必须安装在主计算机上。  |


## <a name="request-approval-to-run-the-container"></a>请求批准以运行容器

填写并提交 [请求表单](https://aka.ms/csgate) ，请求批准以运行容器。

通过该表单请求有关你、你的公司以及要使用该容器的用户方案的信息。 提交该表单后，Azure 认知服务团队将会对其进行评审，并通过一种决定向你发送电子邮件。

> [!IMPORTANT]
> * 在窗体上，必须使用与 Azure 订阅 ID 关联的电子邮件地址。
> * 用于运行容器的计算机视觉资源必须已使用已批准的 Azure 订阅 ID 创建。

获得批准后，你将能够在从 Microsoft 容器注册表 (MCR) 下载容器后运行该容器，本文稍后将对此进行介绍。

如果你的 Azure 订阅尚未获得批准，你将无法运行该容器。

## <a name="set-up-the-host-computer"></a>设置主计算机

建议为主计算机使用 Azure Stack Edge 设备。 如果要配置其他设备，请单击 " **台式计算机** "。

#### <a name="azure-stack-edge-device"></a>[Azure Stack 边缘设备](#tab/azure-stack-edge)

### <a name="configure-compute-on-the-azure-stack-edge-portal"></a>在 Azure Stack Edge 门户上配置计算 
 
空间分析使用 Azure Stack 边缘的计算功能来运行 AI 解决方案。 若要启用计算功能，请确保： 

* 已 [连接并激活](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-connect-setup-activate) Azure Stack Edge 设备。 
* 你有运行 PowerShell 5.0 或更高版本的 Windows 客户端系统来访问设备。  
* 若要部署 Kubernetes 群集，需要通过[Azure 门户](https://portal.azure.com/)上的**本地 UI**配置 Azure Stack Edge 设备： 
  1. 启用 Azure Stack 边缘设备上的计算功能。 若要启用计算，请在设备的 web 界面中转到 **计算** 页。 
  2. 选择要为计算启用的网络接口，然后单击 " **启用**"。 这会在设备上的该网络接口上创建一个虚拟交换机。
  3. 将 Kubernetes 测试节点 IP 地址和 Kubernetes 外部服务 IP 地址留空。
  4. 单击“应用”。 此操作可能需要大约两分钟的时间。 

![配置计算](media/spatial-analysis/configure-compute.png)

### <a name="set-up-an-edge-compute-role-and-create-an-iot-hub-resource"></a>设置边缘计算角色并创建 IoT 中心资源

在 [Azure 门户](https://portal.azure.com/)中，导航到 Azure Stack Edge 资源。 在 " **概述** " 页或导航列表中，单击 "边缘计算 **入门** " 按钮。 在 " **配置边缘计算**   " 磁贴中，单击 "**配置**"。 

![链接](media/spatial-analysis/configure-edge-compute-tile.png)

在 " **配置边缘计算**"   页上，选择现有的 IoT 中心，或选择创建一个新的 IoT 中心。 默认情况下，将使用标准 (S1) 定价层创建 IoT 中心资源。 若要使用免费层 IoT 中心资源，请创建一个，然后选择它。 IoT 中心资源使用的订阅和资源组与 Azure Stack Edge 资源使用的相同 

单击“创建”。 IoT 中心资源创建可能需要几分钟的时间。 创建 IoT 中心资源后，" **配置边缘计算** " 磁贴将更新以显示新的配置。 若要确认已配置边缘计算角色，请选择 " **配置计算**" 磁贴上的 " **查看配置**"   。

如果在 Edge 设备上设置了 Edge 计算角色，则会创建两个设备：一个 IoT 设备，一个 IoT Edge 设备。 可在 IoT 中心资源中查看这两个设备。 Azure IoT Edge 运行时将已在 IoT Edge 设备上运行。            

> [!NOTE]
> * 目前 IoT Edge 设备仅支持 Linux 平台。 有关对 Azure Stack Edge 设备进行故障排除的帮助，请参阅 [日志记录和故障排除](spatial-analysis-logging.md) 一文。
> * 若要详细了解如何配置 IoT Edge 设备以便通过代理服务器进行通信，请参阅 [将 IoT Edge 设备配置为通过代理服务器进行通信](https://docs.microsoft.com/azure/iot-edge/how-to-configure-proxy-support#azure-portal)

###  <a name="enable-mps-on-azure-stack-edge"></a>在 Azure Stack 边缘上启用 MPS 

1. 以管理员身份运行 Windows PowerShell 会话。 

2. 请确保 Windows 远程管理服务在客户端上运行。 在 PowerShell 终端中，使用以下命令 
    
    ```powershell
    winrm quickconfig
    ```
    
    如果你看到关于防火墙例外的警告，请检查你的网络连接类型，然后查看 [Windows 远程管理](https://docs.microsoft.com/windows/win32/winrm/installation-and-configuration-for-windows-remote-management) 文档。

3. 将变量分配给设备 IP 地址。 
    
    ```powershell
    $ip = "" Replace with the IP address of your device. 
    ```
    
4. 若要将设备的 IP 地址添加到客户端的受信任主机列表中，请使用以下命令： 
    
    ```powershell
    Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force 
    ```

5. 在设备上启动 Windows PowerShell 会话。 

    ```powershell
    Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell 
    ```

6. 在出现提示时提供密码。 使用用于登录本地 web UI 的相同密码。 默认的本地 web UI 密码是 `Password1` 。

键入 `Start-HcsGpuMPS` 以在设备上启动 MPS 服务。 

若要帮助排查 Azure Stack Edge 设备的问题，请参阅 [Azure Stack Edge 设备的疑难解答](spatial-analysis-logging.md#troubleshooting-the-azure-stack-edge-device) 

#### <a name="desktop-machine"></a>[台式计算机](#tab/desktop-machine)

如果您的主计算机不是 Azure Stack 边缘设备，请遵循这些说明。

#### <a name="install-nvidia-cuda-toolkit-and-nvidia-graphics-drivers-on-the-host-computer"></a>在主计算机上安装 NVIDIA CUDA 工具包和 Nvidia 图形驱动程序

使用以下 bash 脚本安装所需的 Nvidia 图形驱动程序和 CUDA 工具包。

```bash
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-ubuntu1804.pin
sudo mv cuda-ubuntu1804.pin /etc/apt/preferences.d/cuda-repository-pin-600
sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub
sudo add-apt-repository "deb http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/ /"
sudo apt-get update
sudo apt-get -y install cuda
```

重新启动计算机，并运行以下命令。

```bash
nvidia-smi
```

应该会看到以下输出。

![NVIDIA 驱动程序输出](media/spatial-analysis/nvidia-driver-output.png)

### <a name="install-docker-ce-and-nvidia-docker2-on-the-host-computer"></a>在主计算机上安装 Docker CE 和 nvidia-docker2

在主计算机上安装 Docker CE。

```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io
```

安装 *nvidia-docker-2* 软件包。

```bash
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
sudo apt-get update
sudo apt-get install -y docker-ce nvidia-docker2
sudo systemctl restart docker
```

## <a name="enable-nvidia-mps-on-the-host-computer"></a>在主计算机上启用 NVIDIA MPS

> [!TIP]
> * 如果 GPU 计算功能小于 7. x (预 Volta) ，请不要安装 MPS。 有关参考，请参阅 [CUDA 兼容](https://docs.nvidia.com/deploy/cuda-compatibility/index.html#support-title) 性。 
> * 从主计算机上的终端窗口中运行 MPS 指令。 不在 Docker 容器实例内。

为获得最佳性能和利用率，请为 [NVIDIA 多服务器服务 (MPS) ](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf)配置主机计算机的 GPU (s) 。 从主计算机上的终端窗口中运行 MPS 指令。

```bash
# Set GPU(s) compute mode to EXCLUSIVE_PROCESS
sudo nvidia-smi --compute-mode=EXCLUSIVE_PROCESS

# Cronjob for setting GPU(s) compute mode to EXCLUSIVE_PROCESS on boot
echo "SHELL=/bin/bash" > /tmp/nvidia-mps-cronjob
echo "PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin" >> /tmp/nvidia-mps-cronjob
echo "@reboot   root    nvidia-smi --compute-mode=EXCLUSIVE_PROCESS" >> /tmp/nvidia-mps-cronjob

sudo chown root:root /tmp/nvidia-mps-cronjob
sudo mv /tmp/nvidia-mps-cronjob /etc/cron.d/

# Service entry for automatically starting MPS control daemon
echo "[Unit]" > /tmp/nvidia-mps.service
echo "Description=NVIDIA MPS control service" >> /tmp/nvidia-mps.service
echo "After=cron.service" >> /tmp/nvidia-mps.service
echo "" >> /tmp/nvidia-mps.service
echo "[Service]" >> /tmp/nvidia-mps.service
echo "Restart=on-failure" >> /tmp/nvidia-mps.service
echo "ExecStart=/usr/bin/nvidia-cuda-mps-control -f" >> /tmp/nvidia-mps.service
echo "" >> /tmp/nvidia-mps.service
echo "[Install]" >> /tmp/nvidia-mps.service
echo "WantedBy=multi-user.target" >> /tmp/nvidia-mps.service

sudo chown root:root /tmp/nvidia-mps.service
sudo mv /tmp/nvidia-mps.service /etc/systemd/system/

sudo systemctl --now enable nvidia-mps.service
```

## <a name="configure-azure-iot-edge-on-the-host-computer"></a>在主计算机上配置 Azure IoT Edge

若要在主计算机上部署空间分析容器，请使用标准 (S1) 或免费 (F0) 定价层创建 [Azure IoT 中心](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal) 服务的实例。 如果你的主计算机是 Azure Stack 边缘，请使用 Azure Stack Edge 资源使用的同一订阅和资源组。

使用 Azure CLI 创建 Azure IoT 中心的实例。 将参数替换为适当的位置。 或者，你可以在 [Azure 门户](https://portal.azure.com/)上创建 Azure IoT 中心。

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
az login
az account set --subscription <name or ID of Azure Subscription>
az group create --name "test-resource-group" --location "WestUS"

az iot hub create --name "test-iot-hub-123" --sku S1 --resource-group "test-resource-group"

az iot hub device-identity create --hub-name "test-iot-hub-123" --device-id "my-edge-device" --edge-enabled
```

如果主机不是 Azure Stack Edge 设备，则需要安装 [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) 版本1.0.8。 请按照以下步骤下载正确的版本：

Ubuntu Server 18.04：
```bash
curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
```

复制生成的列表。
```bash
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
```

安装 Microsoft GPG 公钥。

```bash
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

更新设备上的包列表。

```bash
sudo apt-get update
```

安装1.0.8 版本：

```bash
sudo apt-get install iotedge=1.0.8* libiothsm-std=1.0.8*
```

接下来，使用 [连接字符串](https://docs.microsoft.com/azure/iot-edge/how-to-register-device#register-in-the-azure-portal)将主机计算机注册为 IoT 中心实例中的 IoT Edge 设备。

需要将 IoT Edge 设备连接到 Azure IoT 中心。 需要从前面创建的 IoT Edge 设备中复制连接字符串。 或者，你可以在 Azure CLI 中运行以下命令。

```bash
az iot hub device-identity show-connection-string --device-id my-edge-device --hub-name test-iot-hub-123
```

在主计算机上打开  `/etc/iotedge/config.yaml` 以进行编辑。 替换 `ADD DEVICE CONNECTION STRING HERE` 为连接字符串。 保存并关闭该文件。 运行此命令以重新启动主计算机上的 IoT Edge 服务。

```bash
sudo systemctl restart iotedge
```

将空间分析容器作为 IoT 模块部署在主计算机上，无论是从 [Azure 门户](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal) 还是 [Azure CLI](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-cli)。 如果使用的是门户，请将映像 URI 设置为 Azure 容器注册表的位置。 

使用以下步骤使用 Azure CLI 部署容器。

---

### <a name="iot-deployment-manifest"></a>IoT 部署清单

若要简化多台主机计算机上的容器部署，可以创建部署清单文件来指定容器创建选项和环境变量。 可 [在 GitHub 上找到部署清单](https://go.microsoft.com/fwlink/?linkid=2142179)的示例。

下表显示 IoT Edge 模块使用的各种环境变量。 你还可以使用中的属性在上面链接的部署清单中设置它们 `env` `spatialanalysis` ：

| 设置名称 | 值 | 说明|
|---------|---------|---------|
| ARCHON_LOG_LEVEL | 信息详细 | 日志记录级别，请选择以下两个值之一|
| ARCHON_SHARED_BUFFER_LIMIT | 377487360 | 不修改|
| ARCHON_PERF_MARKER| false| 对于性能日志记录，将此值设置为 true，否则应为 false| 
| ARCHON_NODES_LOG_LEVEL | 信息详细 | 日志记录级别，请选择以下两个值之一|
| OMP_WAIT_POLICY | 活动 | 不修改|
| QT_X11_NO_MITSHM | 1 | 不修改|
| API_KEY | 你的 API 密钥| 从计算机视觉资源 Azure 门户收集此值。 可以在资源的 " **密钥和终结点** " 部分中找到它。 |
| BILLING_ENDPOINT | 终结点 URI| 从计算机视觉资源 Azure 门户收集此值。 可以在资源的 " **密钥和终结点** " 部分中找到它。|
| EULA | accept | 要使容器运行，需要将此值设置为 " *接受* " |
| DISPLAY | ：1 | 此值需要与主计算机上的的输出相同 `echo $DISPLAY` 。 Azure Stack 边缘设备没有显示。 此设置不适用|


> [!IMPORTANT]
> 必须指定 `Eula`、`Billing` 和 `ApiKey` 选项运行容器；否则，该容器不会启动。  有关详细信息，请参阅[计费](#billing)。

使用你自己的设置和操作选择更新文件的示例 [DeploymentManifest.js](https://go.microsoft.com/fwlink/?linkid=2142179) 后，你可以使用以下 [Azure CLI](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-cli) 命令在主计算机上部署容器，如 IoT Edge 模块。

```azurecli
az login
az extension add --name azure-iot
az iot edge set-modules --hub-name "<IoT Hub name>" --device-id "<IoT Edge device name>" --content DeploymentManifest.json -–subscription "<subscriptionId>"
```

|参数  |说明  |
|---------|---------|
| `--deployment-id` | 部署的新名称。 |
| `--hub-name` | Azure IoT 中心名称。 |
| `--content` | 部署文件的名称。 |
| `--target-condition` | 主计算机的 IoT Edge 设备名称。 |
| `-–subscription` | 订阅 ID 或名称。 |

此命令将启动部署。 在 Azure 门户中导航到 Azure IoT 中心实例的页面，以查看部署状态。 状态可能显示为 *417 –设备的部署配置* 在设备完成下载容器映像并开始运行之前未设置。

## <a name="validate-that-the-deployment-is-successful"></a>验证部署是否成功

有几种方法可用于验证容器是否正在运行。 在 Azure 门户上的 Azure IoT 中心实例中的 "空间分析" 模块**IoT Edge 模块设置**中找到 "*运行时状态*"。 验证*运行时状态*所**需的值**和**报告的值**是否*正在运行*。

![部署验证示例](./media/spatial-analysis/deployment-verification.png)

部署完成并且容器运行后， **主机** 会开始将事件发送到 Azure IoT 中心。 如果你使用了这些 `.debug` 操作的版本，你将看到你在部署清单中配置的每个照相机的可视化工具窗口。 你现在可以定义要在部署清单中监视的行和区域，然后按照说明重新部署。 

## <a name="configure-the-operations-performed-by-spatial-analysis"></a>配置空间分析执行的操作

你将需要使用 [空间分析操作](spatial-analysis-operations.md) 来配置容器，使其使用连接相机、配置操作等。 对于配置的每个照相机设备，空间分析的操作将生成 JSON 消息的输出流，并将其发送到 Azure IoT 中心的实例。

## <a name="redeploy-or-delete-the-deployment"></a>重新部署或删除部署

如果需要更新部署，需要确保已成功部署以前的部署，或者需要删除未完成 IoT Edge 设备部署。 否则，这些部署将继续，使系统处于错误的状态。 您可以使用 Azure 门户或 [Azure CLI](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment)。

## <a name="use-the-output-generated-by-the-container"></a>使用容器生成的输出

如果要开始使用容器生成的输出，请参阅以下文章：

*   使用所选编程语言的 Azure 事件中心 SDK 连接到 Azure IoT 中心终结点，并接收事件。 有关详细信息，请参阅 [从内置终结点读取设备到云的消息](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-read-builtin) 。 
*   在 Azure IoT 中心设置消息路由，将事件发送到其他终结点，或者将事件保存到 Azure Blob 存储等。有关详细信息，请参阅 [IoT 中心消息路由](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c) 。 

## <a name="running-spatial-analysis-with-a-recorded-video-file"></a>使用录制的视频文件运行空间分析

可对录制的视频或实时视频使用空间分析。 若要对录制的视频使用空间分析，请尝试录制视频文件并将其保存为 "有文件" 文件。 在 Azure 中创建 blob 存储帐户，或使用现有存储帐户。 然后，更新 Azure 门户中的以下 blob 存储设置：
    1. 更改**所需的安全传输** **Disabled**
    2. 更改**允许 Blob 公共访问****已启用**

导航到 " **容器** " 部分，并创建新容器或使用现有容器。 然后将视频文件上传到该容器。 展开已上传文件的文件设置，然后选择 " **生成 SAS**"。 请确保将 **到期日期** 设置为足够长的时间以涵盖测试期间。 ) 不支持将 **允许的协议** 设置为 *HTTP* (*HTTPS* 。

单击 " **生成 SAS 令牌和 URL** " 并复制 "BLOB SAS URL"。 将开头的替换为 `https` `http` ，并在支持视频播放的浏览器中测试 URL。

将 `VIDEO_URL` [部署清单](https://go.microsoft.com/fwlink/?linkid=2142179) 中的替换为所有关系图的 URL。 设置 `VIDEO_IS_LIVE` 为 `false` ，然后重新部署具有更新清单的空间分析容器。 请参阅以下示例。

空间分析模块将开始使用视频文件，并且还会持续自动重放。


```json
"zonecrossing": {
  "operationId" : "cognitiveservices.vision.spatialanalysis-personcrossingpolygon",
  "version": 1,
  "enabled": true,
  "parameters": {
      "VIDEO_URL": "Replace http url here",
      "VIDEO_SOURCE_ID": "personcountgraph",
      "VIDEO_IS_LIVE": false,
        "VIDEO_DECODE_GPU_INDEX": 0,
      "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0 }",
      "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"queue\",\"polygon\":[[0.3,0.3],[0.3,0.9],[0.6,0.9],[0.6,0.3],[0.3,0.3]], \"threshold\":35.0}]}"
    }
  },

```

## <a name="troubleshooting"></a>疑难解答

如果在启动或运行容器时遇到问题，请参阅 [遥测和故障排除](spatial-analysis-logging.md) ，了解常见问题的步骤。 本文还包含有关生成和收集日志以及收集系统运行状况的信息。

## <a name="billing"></a>计费

空间分析容器使用 Azure 帐户上的计算机视觉资源将计费信息发送到 Azure。 目前可免费使用公共预览版中的空间分析。 

Azure 认知服务容器未获得许可，无需连接到计量/计费终结点。 必须始终让容器可以向计费终结点传送计费信息。 认知服务容器不会将客户数据（例如正在分析的视频或图像）发送给 Microsoft。


## <a name="summary"></a>总结

本文介绍了用于下载、安装和运行空间分析容器的概念和工作流。 综上所述：

* 空间分析是适用于 Docker 的 Linux 容器。
* 将从 Microsoft 容器注册表下载容器映像。
* 容器映像作为 IoT 模块在 Azure IoT Edge 中运行。
* 如何配置容器并将其部署在主机上。

## <a name="next-steps"></a>后续步骤

* [部署计算 web 应用程序的人员](spatial-analysis-web-app.md)
* [配置空间分析操作](spatial-analysis-operations.md)
* [日志记录和故障排除](spatial-analysis-logging.md)
* [照相机位置指南](spatial-analysis-camera-placement.md)
* [区域和线条位置指南](spatial-analysis-zone-line-placement.md)
