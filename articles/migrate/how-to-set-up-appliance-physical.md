---
title: 使用 Azure Migrate Server 评估设置设备以评估物理服务器
description: 描述如何使用 Azure Migrate Server 评估设置设备以对物理服务器进行评估。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 10/21/2019
ms.author: raynew
ms.openlocfilehash: ddd659e8cbcb54a36868848d0c6327f294d531b1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512621"
---
# <a name="set-up-an-appliance-for-physical-servers"></a>为物理服务器设置设备

本文介绍了如何设置 Azure Migrate 设备，前提是你要用 Azure Migrate： Server 评估工具评估物理服务器。

Azure Migrate 设备是一种轻型设备，由 Azure Migrate 服务器评估用于执行以下操作：

- 发现本地服务器。
- 将发现的服务器的元数据和性能数据发送到 Azure Migrate Server 评估。

[了解](migrate-appliance.md)Azure Migrate 设备的详细信息。


## <a name="appliance-deployment-steps"></a>设备部署步骤

若要设置该设备，请执行以下操作：
- 从 Azure 门户下载包含 Azure Migrate 安装程序脚本的压缩文件。
- 从 zip 文件中提取内容。 启动具有管理权限的 PowerShell 控制台。
- 执行 PowerShell 脚本以启动设备 web 应用程序。
- 完成设备的首次配置，并将其注册到 Azure Migrate 项目。

## <a name="download-the-installer-script"></a>下载安装程序脚本

下载设备的压缩文件。

1. 在 "**迁移目标** > **服务器** > **Azure Migrate：服务器评估**中，单击"**发现**"。
2. 在“发现计算机” **“计算机是否已虚拟化?”中，单击“未虚拟化/其他”。**  > 
3. 单击 "**下载**" 以下载压缩的文件。

    ![下载 VM](./media/how-to-set-up-appliance-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>验证安全性

在部署压缩文件之前检查其安全性。

1. 在下载文件的计算机上，打开管理员命令窗口。
2. 运行以下命令以生成 VHD 的哈希
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 用法示例：```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3.  对于设备版本1.19.05.10，生成的哈希应与这些设置相符。

  **算法** | **哈希值**
  --- | ---
  SHA256 | 598d2e286f9c972bb7f7382885e79e768eddedfe8a3d3460d6b8a775af7d7f79


  
## <a name="run-the-azure-migrate-installer-script"></a>运行 Azure Migrate 安装程序脚本
= 安装程序脚本执行以下操作：

- 安装代理和 web 应用程序，以便进行物理服务器发现和评估。
- 安装 Windows 角色，包括 Windows 激活服务、IIS 和 PowerShell ISE。
- 下载并安装可重写的 IIS 模块。 [了解详细信息](https://www.microsoft.com/download/details.aspx?id=7435)。
- 使用 Azure Migrate 的永久设置详细信息更新注册表项（HKLM）。
- 在路径下创建以下文件：
    - **配置文件**：%Programdata%\Microsoft Azure\Config
    - **日志文件**：%Programdata%\Microsoft Azure\Logs

按如下所示运行脚本：

1. 将压缩文件解压缩到将托管设备的服务器上的文件夹中。
2. 在上述服务器上以管理（提升）权限启动 PowerShell。
3. 将 PowerShell 目录更改为已从下载的压缩文件中提取内容的文件夹。
4. 通过运行以下命令运行该脚本：
    ```
    PS C:\Users\Administrators\Desktop> AzureMigrateInstaller-physical.ps1
    ```
脚本成功完成后，该脚本将启动设备 web 应用程序。



### <a name="verify-appliance-access-to-azure"></a>验证设备的 Azure 访问权限

请确保设备 VM 可以连接到所需的[Azure url](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access)。

## <a name="configure-the-appliance"></a>配置设备

首次设置设备。

1. 在任何可以连接到 VM 的计算机上打开浏览器，并打开设备 web 应用的 URL： **https://*设备名称或 IP 地址*： 44368**。

   或者，您可以通过单击应用程序快捷方式从桌面上打开该应用程序。
2. 在 Web 应用 >“设置必备组件”中执行以下操作：
    - **许可证**：接受许可条款，并阅读第三方信息。
    - **连接**：应用检查 VM 是否可以访问 internet。 如果 VM 使用代理：
        - 单击“代理设置”，并以 **或** 格式指定代理地址和侦听端口。 http://ProxyIPAddresshttp://ProxyFQDN
        - 如果代理需要身份验证，请指定凭据。
        - 仅支持 HTTP 代理。
    - **时间同步**：时间已验证。 设备上的时间应与 Internet 时间同步，这样才能正常发现 VM。
    - **安装更新**： Azure Migrate Server 评估检查设备是否安装了最新更新。

### <a name="register-the-appliance-with-azure-migrate"></a>将设备注册到 Azure Migrate

1. 单击“登录”。 如果未显示该按钮，请确保已在浏览器中禁用弹出窗口阻止程序。
2. 在新的标签页中，使用 Azure 凭据登录。 
    - 使用用户名和密码登录。
    - 不支持使用 PIN 登录。
3. 成功登录后，返回到 Web 应用。
4. 选择在其中创建了 Azure Migrate 项目的订阅。 然后选择该项目。
5. 指定设备的名称。 该名称应是字母数字，长度为 14 个或更少的字符。
6. 单击“注册”。


## <a name="start-continuous-discovery"></a>启动持续发现

从设备连接到物理服务器，然后启动发现。

1. 单击 "**添加凭据**" 以指定设备将用于发现服务器的帐户凭据。  
2. 指定**操作系统**、凭据的友好名称、**用户名**和**密码**，然后单击 "**添加**"。
你可以为 Windows 和 Linux 服务器添加一组凭据。
4. 单击 "**添加服务器**"，然后指定服务器详细信息-FQDN/IP 地址和凭据的友好名称（每行一个条目）以连接到服务器。
3. 单击“验证”。 验证后，将显示可发现的服务器的列表。
    - 如果服务器验证失败，请将鼠标悬停在 "**状态**" 列中的图标上，查看错误。 解决问题并再次验证。
    - 若要删除服务器，请选择 ">**删除**"。
4. 验证之后，单击“保存并启动发现”以启动发现过程。

随即会启动发现。 大约 15 分钟后，已发现的 VM 的元数据将显示在 Azure 门户中。 

## <a name="verify-servers-in-the-portal"></a>验证门户中的服务器

发现完成后，可以验证服务器是否显示在门户中。

1. 打开 Azure Migrate 仪表板。
2. 在**Azure Migrate-服务器** > **Azure Migrate：服务器评估**"页上，单击显示**发现的服务器**的计数的图标。 


## <a name="next-steps"></a>后续步骤

试用 Azure Migrate 服务器评估[的物理服务器评估](tutorial-assess-physical.md)。
