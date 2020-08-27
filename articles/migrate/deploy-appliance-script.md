---
title: 使用脚本设置 Azure Migrate 设备
description: 了解如何使用脚本设置 Azure Migrate 设备
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: 43e47c6b4121a2f389dfec27873b90b7031c4f2c
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2020
ms.locfileid: "88929969"
---
# <a name="set-up-an-appliance-with-a-script"></a>使用脚本设置设备

按照本文所述，为 VMware Vm 和 Hyper-v Vm 的评估/迁移创建一个 [Azure Migrate 的设备](./migrate-appliance-architecture.md) 。 运行脚本来创建设备，并验证它是否可以连接到 Azure。 

你可以使用脚本部署适用于 VMware 和 Hyper-v Vm 的设备，或使用从 Azure 门户下载的模板。 如果无法使用下载的模板创建 VM，则使用脚本会很有用。

- 若要使用模板，请遵循 [VMware](tutorial-prepare-vmware.md) 或 [hyper-v](tutorial-prepare-hyper-v.md)教程。
- 若要为物理服务器设置设备，只能使用脚本。 请参阅 [此文](how-to-set-up-appliance-physical.md)。
- 若要在 Azure 政府版云中设置设备，请遵循 [此文](deploy-appliance-script-government.md)。

## <a name="prerequisites"></a>先决条件

此脚本在现有物理计算机或 VM 上设置 Azure Migrate 设备。

- 用作设备的计算机必须满足以下硬件和操作系统要求：

场景 | 要求
--- | ---
VMware | Windows Server 2016，含 32 GB 内存，8个个 vcpu，大约为 80 GB 的磁盘存储
Hyper-V | Windows Server 2016，具有 16 GB 内存，8个个 vcpu，大约 80 GB 的磁盘存储
- 计算机还需要外部虚拟交换机。 它需要静态或动态 IP 地址以及对 internet 的访问权限。
- 在部署设备之前，请查看 [VMware vm](migrate-appliance.md#appliance---vmware)、 [hyper-v vm](migrate-appliance.md#appliance---hyper-v)的详细设备要求。
- 不要在现有 Azure Migrate 设备上运行该脚本。

## <a name="set-up-the-appliance-for-vmware"></a>为 VMware 设置设备

若要为 VMware 设置设备，请从门户或从 [此处](https://go.microsoft.com/fwlink/?linkid=2140334)下载名为 AzureMigrateInstaller-Server-Public.zip 的压缩文件，然后提取内容。 运行 PowerShell 脚本以启动设备 web 应用。 首先设置设备并进行配置。 然后，将设备注册到 Azure Migrate 项目。


### <a name="verify-file-security"></a>验证文件安全

在部署压缩文件之前检查其安全性。

1. 在下载文件的计算机上，打开管理员命令窗口。
2. 运行以下命令以生成 zip 文件的哈希
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 示例： ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public.zip SHA256```
3. 验证 Azure 公有云的最新设备版本和脚本：

    **算法** | **下载** | **SHA256**
    --- | --- | ---
    VMware (85 MB)  | [最新版本](https://go.microsoft.com/fwlink/?linkid=2140334) | 5d0a3dbce4b5010980d59d49859f809acfeb17f5a36f57af4dac44a0a62dde1f



### <a name="run-the-script"></a>运行脚本

脚本执行以下操作：

- 安装代理和 web 应用程序。
- 安装 Windows 角色，包括 Windows 激活服务、IIS 和 PowerShell ISE。
- 下载并安装可重写的 IIS 模块。 [了解详细信息](https://www.microsoft.com/download/details.aspx?id=7435)。
- 使用 Azure Migrate 的持久设置更新 HKLM)  (的注册表项。
- 按如下所示创建日志文件和配置文件：
    - **配置文件**：%ProgramData%\Microsoft Azure\Config
    - **日志文件**：%ProgramData%\Microsoft Azure\Logs

若要运行该脚本，请执行以下操作：

1. 将压缩的文件提取到将托管设备的计算机上的文件夹中。 请确保不要在现有 Azure Migrate 设备上的计算机中运行该脚本。
2. 在计算机上启动 PowerShell，管理员 (提升) 特权。
3. 将 PowerShell 目录更改为包含从已下载的压缩文件中提取的内容的文件夹。
4. 运行脚本 **AzureMigrateInstaller.ps1**，如下所示：

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 -scenario VMware ```
   
5. 脚本成功运行后，将启动设备 web 应用程序，以便你可以设置设备。 如果遇到任何问题，请查看 C:\ProgramData\Microsoft Azure\Logs\ 上的脚本日志 AzureMigrateScenarioInstaller_<em>时间戳</em>。

### <a name="verify-access"></a>验证访问

确保设备可以连接到 [公有](migrate-appliance.md#public-cloud-urls) 云的 Azure url。

## <a name="set-up-the-appliance-for-hyper-v"></a>为 Hyper-v 设置设备

若要设置适用于 Hyper-v 的设备，请从门户或从 [此处](https://go.microsoft.com/fwlink/?linkid=2105112)下载名为 AzureMigrateInstaller-Server-Public.zip 的压缩文件，然后提取内容。 运行 PowerShell 脚本以启动设备 web 应用。 首先设置设备并进行配置。 然后，将设备注册到 Azure Migrate 项目。


### <a name="verify-file-security"></a>验证文件安全

在部署压缩文件之前检查其安全性。

1. 在下载文件的计算机上，打开管理员命令窗口。
2. 运行以下命令以生成 zip 文件的哈希
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 示例： ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public.zip SHA256```

3. 验证 Azure 公有云的最新设备版本和脚本：

    **方案** | **下载** | **SHA256**
    --- | --- | ---
    Hyper-v (85 MB)  | [最新版本](https://go.microsoft.com/fwlink/?linkid=2140334) |  5d0a3dbce4b5010980d59d49859f809acfeb17f5a36f57af4dac44a0a62dde1f

### <a name="run-the-script"></a>运行脚本

脚本执行以下操作：

- 安装代理和 web 应用程序。
- 安装 Windows 角色，包括 Windows 激活服务、IIS 和 PowerShell ISE。
- 下载并安装可重写的 IIS 模块。 [了解详细信息](https://www.microsoft.com/download/details.aspx?id=7435)。
- 使用 Azure Migrate 的持久设置更新 HKLM)  (的注册表项。
- 按如下所示创建日志文件和配置文件：
    - **配置文件**：%ProgramData%\Microsoft Azure\Config
    - **日志文件**：%ProgramData%\Microsoft Azure\Logs

若要运行该脚本，请执行以下操作：

1. 将压缩的文件提取到将托管设备的计算机上的文件夹中。 请确保不要在现有 Azure Migrate 设备上的计算机中运行该脚本。
2. 在计算机上启动 PowerShell，管理员 (提升) 特权。
3. 将 PowerShell 目录更改为包含从已下载的压缩文件中提取的内容的文件夹。
4. 运行脚本 **AzureMigrateInstaller.ps1**，如下所示： 

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 -scenario Hyperv ```
   
5. 脚本成功运行后，将启动设备 web 应用程序，以便你可以设置设备。 如果遇到任何问题，请查看 C:\ProgramData\Microsoft Azure\Logs\ 上的脚本日志 AzureMigrateScenarioInstaller_<em>时间戳</em>。

### <a name="verify-access"></a>验证访问

确保设备可以连接到 [公有](migrate-appliance.md#public-cloud-urls) 云的 Azure url。

## <a name="next-steps"></a>后续步骤

部署设备后，需首次配置该设备，并将其注册到 Azure Migrate 项目。

- 为 [VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance)设置设备。
- 为 [hyper-v](how-to-set-up-appliance-hyper-v.md#configure-the-appliance)设置设备。
