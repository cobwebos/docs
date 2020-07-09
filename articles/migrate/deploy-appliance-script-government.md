---
title: 在 Azure 政府版中设置 Azure Migrate 设备
description: 了解如何在 Azure 政府版中设置 Azure Migrate 设备
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: f0ebc882646b5ff3f62ddddf91cffc85cb5e0da6
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86109971"
---
# <a name="set-up-an-appliance-in-azure-government"></a>在 Azure 政府版中设置设备 

按照本文中的步骤，在 Azure 政府版云中为 VMware Vm、Hyper-v Vm 和物理服务器部署[Azure Migrate 的设备](./migrate-appliance-architecture.md)。 运行脚本来创建设备，并验证它是否可以连接到 Azure。 如果要在公有云中设置设备，请遵循[此文](deploy-appliance-script.md)。


> [!NOTE]
> Azure 政府版不支持使用模板（适用于 VMware Vm 和 Hyper-v Vm）部署设备的选项。


## <a name="prerequisites"></a>先决条件

此脚本在现有物理计算机或 VM 上设置 Azure Migrate 设备。

- 用作设备的计算机必须运行 Windows Server 2016，其中包含 32 GB 内存、8个个 vcpu、大约 80 GB 的磁盘存储和外部虚拟交换机。 它需要静态或动态 IP 地址以及对 internet 的访问权限。
- 在部署设备之前，请查看[VMware vm](migrate-appliance.md#appliance---vmware)、 [hyper-v vm](migrate-appliance.md#appliance---hyper-v)和[物理服务器](migrate-appliance.md#appliance---physical)的详细设备要求。
- 不要在现有 Azure Migrate 设备上运行该脚本。

## <a name="set-up-the-appliance-for-vmware"></a>为 VMware 设置设备

若要为 VMware 设置设备，请从 Azure 门户下载压缩文件，然后提取内容。 运行 PowerShell 脚本以启动设备 web 应用。 首先设置设备并进行配置。 然后，将设备注册到 Azure Migrate 项目。

### <a name="download-the-script"></a>下载脚本

1.  在“迁移目标” > “服务器” > “Azure Migrate:  服务器评估”中，单击“发现”。
2.  在“发现计算机” > “计算机是否已虚拟化?”中，选择“是，使用 VMWare vSphere 虚拟机监控程序”。  
3.  单击 "**下载**" 以下载压缩的文件。 


### <a name="verify-file-security"></a>验证文件安全

在部署压缩文件之前检查其安全性。

1. 在下载文件的计算机上，打开管理员命令窗口。
2. 运行以下命令以生成 zip 文件的哈希
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 示例： ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMWare-USGov.zip SHA256```

3. 验证最新的设备版本和哈希值：

    **算法** | **下载** | **SHA256**
    --- | --- | ---
    VMware (63.1 MB) | [最新版本](https://go.microsoft.com/fwlink/?linkid=2120300&clcid=0x409 ) | 3d5822038646b81f458d89d706832c0a2c0e827bfa9b0a55cc478eaf2757a4de


### <a name="run-the-script"></a>运行脚本

脚本执行以下操作：

- 安装代理和 web 应用程序。
- 安装 Windows 角色，包括 Windows 激活服务、IIS 和 PowerShell ISE。
- 下载并安装可重写的 IIS 模块。 [了解详细信息](https://www.microsoft.com/download/details.aspx?id=7435)。
- 更新注册表项（HKLM），并为 Azure Migrate 提供持久设置。
- 按如下所示创建日志文件和配置文件：
    - **配置文件**：%ProgramData%\Microsoft Azure\Config
    - **日志文件**：%ProgramData%\Microsoft Azure\Logs

若要运行该脚本，请执行以下操作：

1. 将压缩的文件提取到将托管设备的计算机上的文件夹中。 请确保不要在现有 Azure Migrate 设备上的计算机中运行该脚本。
2. 在计算机上启动 PowerShell，同时提供管理员（提升）权限。
3. 将 PowerShell 目录更改为包含从已下载的压缩文件中提取的内容的文件夹。
4. 运行脚本**AzureMigrateInstaller.ps1**，如下所示：``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-VMWare-USGov>AzureMigrateInstaller.ps1 ```
5. 脚本成功运行后，将启动设备 web 应用程序，以便你可以设置设备。 如果遇到任何问题，请查看 C:\ProgramData\Microsoft Azure\Logs\ 上的脚本日志 AzureMigrateScenarioInstaller_<em>时间戳</em>。

### <a name="verify-access"></a>验证访问

请确保设备可以连接到适用于[政府云](migrate-appliance.md#government-cloud-urls)的 Azure url。


## <a name="set-up-the-appliance-for-hyper-v"></a>为 Hyper-v 设置设备

若要设置适用于 Hyper-v 的设备，请从 Azure 门户下载压缩文件，然后提取内容。 运行 PowerShell 脚本以启动设备 web 应用。 首先设置设备并进行配置。 然后，将设备注册到 Azure Migrate 项目。

### <a name="download-the-script"></a>下载脚本

1.  在“迁移目标” > “服务器” > “Azure Migrate:  服务器评估”中，单击“发现”。
2.  在“发现计算机” > “计算机是否已虚拟化?”中，选择“是，使用 Hyper-V”。  
3.  单击 "**下载**" 以下载压缩的文件。 


### <a name="verify-file-security"></a>验证文件安全

在部署压缩文件之前检查其安全性。

1. 在下载文件的计算机上，打开管理员命令窗口。
2. 运行以下命令以生成 zip 文件的哈希
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 示例： ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-HyperV-USGov.zip SHA256```

3. 验证最新的设备版本和哈希值：

    **方案** | **下载** | **SHA256**
    --- | --- | ---
    Hyper-V (63.1 MB) | [最新版本](https://go.microsoft.com/fwlink/?linkid=2120200&clcid=0x409) |  2c5e73a1e5525d4fae468934408e43ab55ff397b7da200b92121972e683f9aa3

          

### <a name="run-the-script"></a>运行脚本

脚本执行以下操作：

- 安装代理和 web 应用程序。
- 安装 Windows 角色，包括 Windows 激活服务、IIS 和 PowerShell ISE。
- 下载并安装可重写的 IIS 模块。 [了解详细信息](https://www.microsoft.com/download/details.aspx?id=7435)。
- 更新注册表项（HKLM），并为 Azure Migrate 提供持久设置。
- 按如下所示创建日志文件和配置文件：
    - **配置文件**：%ProgramData%\Microsoft Azure\Config
    - **日志文件**：%ProgramData%\Microsoft Azure\Logs

若要运行该脚本，请执行以下操作：

1. 将压缩的文件提取到将托管设备的计算机上的文件夹中。 请确保不要在现有 Azure Migrate 设备上的计算机中运行该脚本。
2. 在计算机上启动 PowerShell，同时提供管理员（提升）权限。
3. 将 PowerShell 目录更改为包含从已下载的压缩文件中提取的内容的文件夹。
4. 运行脚本**AzureMigrateInstaller.ps1**，如下所示：``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-HyperV-USGov>AzureMigrateInstaller.ps1 ``` 
5. 脚本成功运行后，将启动设备 web 应用程序，以便你可以设置设备。 如果遇到任何问题，请查看 C:\ProgramData\Microsoft Azure\Logs\ 上的脚本日志 AzureMigrateScenarioInstaller_<em>时间戳</em>。

### <a name="verify-access"></a>验证访问

请确保设备可以连接到适用于[政府云](migrate-appliance.md#government-cloud-urls)的 Azure url。


## <a name="set-up-the-appliance-for-physical-servers"></a>为物理服务器设置设备

若要为 VMware 设置设备，请从 Azure 门户下载压缩文件，然后提取内容。 运行 PowerShell 脚本以启动设备 web 应用。 首先设置设备并进行配置。 然后，将设备注册到 Azure Migrate 项目。

### <a name="download-the-script"></a>下载脚本

1.  在“迁移目标” > “服务器” > “Azure Migrate:  服务器评估”中，单击“发现”。
2.  在 "**发现计算机**  >  **已虚拟化"** 中，选择 "**未虚拟化/其他**"。
3.  单击 "**下载**" 以下载压缩的文件。 


### <a name="verify-file-security"></a>验证文件安全

在部署压缩文件之前检查其安全性。

1. 在下载文件的计算机上，打开管理员命令窗口。
2. 运行以下命令以生成 zip 文件的哈希
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 示例： ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip SHA256```

3. 验证最新的设备版本和哈希值：

    **方案** | **下载*** | **哈希值**
    --- | --- | ---
    Physical (63.1 MB) | [最新版本](https://go.microsoft.com/fwlink/?linkid=2120100&clcid=0x409) | 93dfef131026e70acdfad2769cd208ff745ab96a96f013cdf3f9e1e61c9b37e1
          

### <a name="run-the-script"></a>运行脚本

脚本执行以下操作：

- 安装代理和 web 应用程序。
- 安装 Windows 角色，包括 Windows 激活服务、IIS 和 PowerShell ISE。
- 下载并安装可重写的 IIS 模块。 [了解详细信息](https://www.microsoft.com/download/details.aspx?id=7435)。
- 更新注册表项（HKLM），并为 Azure Migrate 提供持久设置。
- 按如下所示创建日志文件和配置文件：
    - **配置文件**：%ProgramData%\Microsoft Azure\Config
    - **日志文件**：%ProgramData%\Microsoft Azure\Logs

若要运行该脚本，请执行以下操作：

1. 将压缩的文件提取到将托管设备的计算机上的文件夹中。 请确保不要在现有 Azure Migrate 设备上的计算机中运行该脚本。
2. 在计算机上启动 PowerShell，同时提供管理员（提升）权限。
3. 将 PowerShell 目录更改为包含从已下载的压缩文件中提取的内容的文件夹。
4. 运行脚本**AzureMigrateInstaller.ps1**，如下所示：``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>AzureMigrateInstaller.ps1 ```
5. 脚本成功运行后，将启动设备 web 应用程序，以便你可以设置设备。 如果遇到任何问题，请查看 C:\ProgramData\Microsoft Azure\Logs\ 上的脚本日志 AzureMigrateScenarioInstaller_<em>时间戳</em>。

### <a name="verify-access"></a>验证访问

请确保设备可以连接到适用于[政府云](migrate-appliance.md#government-cloud-urls)的 Azure url。

## <a name="next-steps"></a>后续步骤

部署设备后，需首次配置该设备，并将其注册到 Azure Migrate 项目。

- 为[VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance)设置设备。
- 为[hyper-v](how-to-set-up-appliance-hyper-v.md#configure-the-appliance)设置设备。
- 为[物理服务器](how-to-set-up-appliance-physical.md)设置设备。
