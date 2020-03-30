---
title: 使用脚本设置 Azure 迁移设备
description: 了解如何使用脚本设置 Azure 迁移设备
ms.topic: article
ms.date: 03/23/2020
ms.openlocfilehash: bf8d7148f685d4ac6a5f33603020a0503b0c62e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337683"
---
# <a name="set-up-an-appliance-with-a-script"></a>使用脚本设置设备

本文介绍如何使用 PowerShell 安装程序脚本设置[Azure 迁移设备](deploy-appliance.md)。

该脚本提供：
- 使用 OVA 模板设置设备，用于 VMware VM 的评估和无代理迁移。
- 使用 VHD 模板设置设备，用于评估和迁移超 VM 的替代方法。
- 为了评估物理服务器（或要作为物理服务器迁移的 VM），脚本是设置设备的唯一方法。

## <a name="prerequisites"></a>先决条件

该脚本在现有物理计算机或 VM 上设置 Azure 迁移设备。

- 充当设备的计算机必须运行 Windows Server 2016，内存为 32 GB，具有 8 个 vCPU、大约 80 GB 的磁盘存储和外部虚拟交换机。 它需要静态或动态 IP 地址，并访问互联网。
- 在部署设备之前，请查看[VMware VM、](migrate-appliance.md#appliance---vmware)[超 VM](migrate-appliance.md#appliance---hyper-v)和[物理服务器](migrate-appliance.md#appliance---physical)的详细设备要求。
- 不要在现有的 Azure 迁移设备上运行脚本。


## <a name="download-the-script"></a>下载脚本

1. 找到将充当 Azure 迁移设备的计算机/VM。
2. 在机器上，执行以下操作：

    - 要将设备与 VMware VM 或超 V VM 一起使用，[请下载](https://go.microsoft.com/fwlink/?linkid=2105112)包含安装程序脚本和 MSIs 的压缩文件夹。
    - 要将设备与物理服务器一起使用，请从 Azure 迁移门户下载脚本，如[本教程](tutorial-assess-physical.md#set-up-the-appliance)中所述。

## <a name="verify-file-security"></a>验证文件安全性

在部署压缩文件之前检查其安全性。

1. 在下载文件的计算机上，打开管理员命令窗口。
2. 运行以下命令以生成 zip 文件的哈希
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 用法示例：```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```

3. 验证生成的哈希值是否与这些设置匹配（对于最新版本）：

    **算法** | **哈希值**
      --- | ---
      MD5 | 1e92ede3e87c03bd148e56a708cdd33f
      SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c

## <a name="run-the-script"></a>运行脚本

下面是脚本的作用：

- 安装代理和 Web 应用程序。
- 安装 Windows 角色，包括 Windows 激活服务、IIS 和 PowerShell ISE。
- 下载并安装 IIS 可重写模块。 [了解详情](https://www.microsoft.com/download/details.aspx?id=7435)。
- 更新注册表项 （HKLM），具有 Azure 迁移的持久设置。
- 创建日志和配置文件，如下所示：
    - **** 配置文件：%ProgramData%\Microsoft Azure\Config
    - **** 日志文件：%ProgramData%\Microsoft Azure\Logs

若要运行该脚本，请执行以下操作：

1. 将压缩文件提取到将承载设备的计算机上的文件夹。
2. 使用管理员（提升）权限在计算机上启动 PowerShell。
3. 将 PowerShell 目录更改为包含从下载的压缩文件中提取的内容的文件夹。
4. 运行脚本**AzureMigrate 安装程序.ps1**如下：

    - 对于 VMware： 
        ```
        PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario VMware
        ```
    - 对于 Hyper-V：
        ```
        PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario Hyperv
        ```
    - 对于物理服务器：
        ```
        PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1
        ```      
5. 脚本成功运行后，它会启动设备 Web 应用程序，以便您可以设置设备。 如果遇到任何问题，可以在 C：_程序数据\微软 Azure_logs_AzureMigrateScenarioInstaller_<em>时间戳</em>.log 上查看脚本日志。

## <a name="next-steps"></a>后续步骤

使用脚本设置设备后，请按照以下说明操作：

- 为[VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance)设置产品。
- 为[Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance)设置产品。
- 为[物理服务器](how-to-set-up-appliance-physical.md)设置设备。