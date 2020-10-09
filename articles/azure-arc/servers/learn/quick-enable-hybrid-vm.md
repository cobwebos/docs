---
title: 使用启用了 Azure Arc 的服务器连接混合计算机
description: 了解如何使用启用了 Azure Arc 的服务器连接和注册混合计算机。
ms.topic: quickstart
ms.date: 09/23/2020
ms.openlocfilehash: b57f30821a105a99041d8187716b75096116ea8e
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91327878"
---
# <a name="quickstart-connect-hybrid-machine-with-azure-arc-enabled-servers"></a>快速入门：使用启用了 Azure Arc 的服务器连接混合计算机

通过[启用了 Azure Arc 的服务器](../overview.md)，可管理和控制跨本地、边缘和多云环境托管的 Windows 和 Linux 计算机。 在本快速入门中，你将在 Azure 外托管的 Windows 或 Linux 计算机上部署和配置 Connected Machine 代理，以便通过启用了 Arc 的服务器进行管理。

## <a name="prerequisites"></a>先决条件

* 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 部署启用了 Arc 的服务器 Hybrid Connected Machine 代理要求你在计算机上具有管理员权限，才能安装和配置代理。 在 Linux 上，需使用根帐户；在 Windows 上，需使用作为“本地管理员”组的成员的帐户。

* 开始之前，请务必查看代理[先决条件](../agent-overview.md#prerequisites)，并验证以下内容：

    * 目标计算机正在运行受支持的[操作系统](../agent-overview.md#supported-operating-systems)。

    * 你的帐户已被分配了[所需 Azure 角色](../agent-overview.md#required-permissions)。

    * 如果计算机通过防火墙或代理服务器连接以通过 Internet 进行通信，请确保未阻止[列出的](../agent-overview.md#networking-configuration) URL。

    * 启用了 Azure Arc 的服务器仅支持[此处](../overview.md#supported-regions)指定的区域。

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

## <a name="register-azure-resource-providers"></a>注册 Azure 资源提供程序

启用了 Azure Arc 的服务器依赖于通过订阅中的以下 Azure 资源提供程序来使用此服务：

* Microsoft.HybridCompute
* Microsoft.GuestConfiguration

使用以下命令来注册它们：

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

## <a name="generate-installation-script"></a>生成安装脚本

Azure 门户中提供了用于自动执行下载、安装以及与 Azure Arc 建立连接的脚本。 若要完成该过程，请执行以下操作：

1. 在 Azure 门户中单击“所有服务”，然后搜索并选择“计算机 - Azure Arc”，以启动 Azure Arc 服务 。

    :::image type="content" source="./media/quick-enable-hybrid-vm/search-machines.png" alt-text="在“所有服务”中搜索启用了 Azure Arc 的服务器" border="false":::

1. 在“计算机 - Azure Arc”页上，选择左上角的“添加”，或者选择中间窗格底部的“创建计算机 - Azure Arc”选项。  

1. 在“选择方法”页上选择“使用交互式脚本添加计算机”磁贴，然后选择“生成脚本”。  

1. 在“生成脚本”页上，选择你要在 Azure 中管理的计算机所在的订阅和资源组。 选择要将计算机元数据存储到的 Azure 位置。

1. 在“生成脚本”页上的“操作系统”下拉列表中，选择运行脚本的操作系统。 

1. 如果计算机通过代理服务器连接到 Internet 进行通信，请选择“下一步:代理服务器”。

1. 在“代理服务器”选项卡上，指定计算机用来与代理服务器通信的代理服务器 IP 地址或名称以及端口号。 按格式 `http://<proxyURL>:<proxyport>` 输入值。

1. 选择“查看 + 生成”。

1. 在“查看 + 生成”选项卡上查看摘要信息，然后选择“下载”。  如果仍需进行更改，请选择“上一页”。

## <a name="install-the-agent-using-the-script"></a>使用脚本安装代理

### <a name="windows-agent"></a>Windows 代理

1. 登录到服务器。

1. 打开权限提升的 64 位 PowerShell 命令提示符。

1. 切换到脚本所复制到的文件夹或共享，然后在服务器上运行 `./OnboardingScript.ps1` 脚本来执行复制的脚本。

### <a name="linux-agent"></a>Linux 代理

1. 若要在可以直接与 Azure 通信的目标计算机上安装 Linux 代理，请运行以下命令：

    ```bash
    bash ~/Install_linux_azcmagent.sh
    ```

    * 如果目标计算机通过代理服务器进行通信，请运行以下命令：

        ```bash
        bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
        ```

## <a name="verify-the-connection-with-azure-arc"></a>验证是否与 Azure Arc 连接

安装代理并将其配置为连接到启用了 Azure Arc 的服务器后，请转到 Azure 门户，验证是否已成功连接服务器。 在 [Azure 门户](https://aka.ms/hybridmachineportal)中查看你的计算机。

:::image type="content" source="./media/quick-enable-hybrid-vm/enabled-machine.png" alt-text="在“所有服务”中搜索启用了 Azure Arc 的服务器" border="false":::

## <a name="next-steps"></a>后续步骤

你已经启用了 Linux 或 Windows 混合计算机并成功连接到了该服务，现在可以启用 Azure Policy 以了解 Azure 中的合规性。

若要了解如何识别未安装 Log Analytics 代理且已启用“启用了 Azure Arc 的服务器”的计算机，请继续学习教程：

> [!div class="nextstepaction"]
> [创建策略分配以识别不合规资源](tutorial-assign-policy-portal.md)
