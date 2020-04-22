---
title: 使用脚本设置 Azure 迁移设备
description: 了解如何使用脚本设置 Azure 迁移设备
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: 0c4d85909bbfa623b5ad8590e973250474d9d95a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676301"
---
# <a name="set-up-an-appliance-with-a-script"></a>使用脚本设置设备

请按照本文创建[Azure 迁移设备](deploy-appliance.md)，用于 VMware VM 和超 VM 的评估/迁移。 运行脚本以创建设备，并验证是否可以连接到 Azure。 

您可以使用脚本或使用从 Azure 门户下载的模板为 VMware 和超 V VM 部署设备。 如果您无法使用下载的模板创建 VM，则使用脚本非常有用。

- 要使用模板，请按照[VMware](tutorial-prepare-vmware.md)或[Hyper-V 的](tutorial-prepare-hyper-v.md)教程进行操作。
- 要为物理服务器设置设备，只能使用脚本。 请按照[本文进行](how-to-set-up-appliance-physical.md)操作。
- 要在 Azure 政府云中设置设备，请按照[本文操作](deploy-appliance-script-government.md)。

## <a name="prerequisites"></a>先决条件

该脚本在现有物理计算机或 VM 上设置 Azure 迁移设备。

- 充当设备的计算机必须运行 Windows Server 2016，内存为 32 GB，具有 8 个 vCPU、大约 80 GB 的磁盘存储和外部虚拟交换机。 它需要静态或动态 IP 地址，并访问互联网。
- 在部署设备之前，请查看[VMware VM、](migrate-appliance.md#appliance---vmware)[超 VM](migrate-appliance.md#appliance---hyper-v)和[物理服务器](migrate-appliance.md#appliance---physical)的详细设备要求。
- 不要在现有的 Azure 迁移设备上运行脚本。

## <a name="set-up-the-appliance-for-vmware"></a>为 VMware 设置产品

要为 VMware 设置设备，请从 Azure 门户下载压缩文件，并提取内容。 运行 PowerShell 脚本以启动设备 Web 应用。 设置设备并首次对其进行配置。 然后，将设备注册到 Azure 迁移项目。

### <a name="download-the-script"></a>下载脚本

1.  在“迁移目标” > “服务器” > “Azure Migrate:    服务器评估”中，单击“发现”。 
2.  在“发现计算机” > “计算机是否已虚拟化?”中，选择“是，使用 VMWare vSphere 虚拟机监控程序”。   
3.  单击 **"下载**"，下载压缩文件。 


### <a name="verify-file-security"></a>验证文件安全性

在部署压缩文件之前检查其安全性。

1. 在下载文件的计算机上，打开管理员命令窗口。
2. 运行以下命令以生成 zip 文件的哈希
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 示例： ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```
3. 验证生成的哈希值。 对于最新版本：

    **算法** | **哈希值**
    --- | ---
    MD5 | 1e92ede3e87c03bd148e56a708cdd33f
    SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c



### <a name="run-the-script"></a>运行脚本

下面是脚本的作用：

- 安装代理和 Web 应用程序。
- 安装 Windows 角色，包括 Windows 激活服务、IIS 和 PowerShell ISE。
- 下载并安装 IIS 可重写模块。 [了解详细信息](https://www.microsoft.com/download/details.aspx?id=7435)。
- 更新注册表项 （HKLM），具有 Azure 迁移的持久设置。
- 创建日志和配置文件，如下所示：
    - **** 配置文件：%ProgramData%\Microsoft Azure\Config
    - **** 日志文件：%ProgramData%\Microsoft Azure\Logs

若要运行该脚本，请执行以下操作：

1. 将压缩文件提取到将承载设备的计算机上的文件夹。 确保没有在现有 Azure 迁移设备上的计算机上运行脚本。
2. 使用管理员（提升）权限在计算机上启动 PowerShell。
3. 将 PowerShell 目录更改为包含从下载的压缩文件中提取的内容的文件夹。
4. 运行脚本**AzureMigrate 安装程序.ps1，** 如下所示：

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario VMware ```
   
5. 脚本成功运行后，它会启动设备 Web 应用程序，以便您可以设置设备。 如果遇到任何问题，请查看 C：_程序数据\微软 Azure_Logs_AzureMigrateScenarioInstaller_<em>时间戳</em>.log 上的脚本日志。

### <a name="verify-access"></a>验证访问权限

确保设备可以连接到[公共](migrate-appliance.md#public-cloud-urls)云的 Azure URL。

## <a name="set-up-the-appliance-for-hyper-v"></a>为 Hyper-V 设置产品

要为 Hyper-V 设置设备，请从 Azure 门户下载压缩文件，并提取内容。 运行 PowerShell 脚本以启动设备 Web 应用。 设置设备并首次对其进行配置。 然后，将设备注册到 Azure 迁移项目。

### <a name="download-the-script"></a>下载脚本

1.  在“迁移目标” > “服务器” > “Azure Migrate:    服务器评估”中，单击“发现”。 
2.  在“发现计算机” > “计算机是否已虚拟化?”中，选择“是，使用 Hyper-V”。   
3.  单击 **"下载**"，下载压缩文件。 


### <a name="verify-file-security"></a>验证文件安全性

在部署压缩文件之前检查其安全性。

1. 在下载文件的计算机上，打开管理员命令窗口。
2. 运行以下命令以生成 zip 文件的哈希
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 示例： ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```

3. 验证生成的哈希值。 对于最新版本：

    **算法** | **哈希值**
    --- | ---
    MD5 | 1e92ede3e87c03bd148e56a708cdd33f
    SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c

### <a name="run-the-script"></a>运行脚本

下面是脚本的作用：

- 安装代理和 Web 应用程序。
- 安装 Windows 角色，包括 Windows 激活服务、IIS 和 PowerShell ISE。
- 下载并安装 IIS 可重写模块。 [了解详细信息](https://www.microsoft.com/download/details.aspx?id=7435)。
- 更新注册表项 （HKLM），具有 Azure 迁移的持久设置。
- 创建日志和配置文件，如下所示：
    - **** 配置文件：%ProgramData%\Microsoft Azure\Config
    - **** 日志文件：%ProgramData%\Microsoft Azure\Logs

若要运行该脚本，请执行以下操作：

1. 将压缩文件提取到将承载设备的计算机上的文件夹。 确保没有在现有 Azure 迁移设备上的计算机上运行脚本。
2. 使用管理员（提升）权限在计算机上启动 PowerShell。
3. 将 PowerShell 目录更改为包含从下载的压缩文件中提取的内容的文件夹。
4. 运行脚本**AzureMigrate 安装程序.ps1，** 如下所示：``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario Hyperv ```
   
5. 脚本成功运行后，它会启动设备 Web 应用程序，以便您可以设置设备。 如果遇到任何问题，请查看 C：_程序数据\微软 Azure_Logs_AzureMigrateScenarioInstaller_<em>时间戳</em>.log 上的脚本日志。

### <a name="verify-access"></a>验证访问权限

确保设备可以连接到[公共](migrate-appliance.md#public-cloud-urls)云的 Azure URL。

## <a name="next-steps"></a>后续步骤

部署设备后，需要首次对其进行配置，并将其注册到 Azure 迁移项目。

- 为[VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance)设置产品。
- 为[Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance)设置产品。
