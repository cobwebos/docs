---
title: 为物理服务器设置 Azure Migrate 设备
description: 了解如何设置 Azure Migrate 设备进行物理服务器评估。
ms.service: azure-migrate
ms.topic: article
ms.date: 04/15/2020
ms.openlocfilehash: 348c12e08a9bed98016ac825921b67836b32b9e9
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91448068"
---
# <a name="set-up-an-appliance-for-physical-servers"></a>为物理服务器设置设备

本文介绍了如何设置 Azure Migrate 设备，前提是你要用 Azure Migrate： Server 评估工具评估物理服务器。

Azure Migrate 设备是一种轻型设备，由 Azure Migrate 服务器评估用于执行以下操作：

- 发现本地服务器。
- 将发现的服务器的元数据和性能数据发送到 Azure Migrate Server 评估。

[了解](migrate-appliance.md) Azure Migrate 设备的详细信息。


## <a name="appliance-deployment-steps"></a>设备部署步骤

若要设置该设备，请执行以下操作：
- 提供设备名称并在门户中生成 Azure Migrate 项目项。
- 从 Azure 门户下载带有 Azure Migrate 安装程序脚本的压缩文件。
- 从压缩文件中提取内容。 使用管理权限启动 PowerShell 控制台。
- 执行 PowerShell 脚本以启动设备 Web 应用程序。
- 第一次配置设备，并使用 Azure Migrate 项目密钥将其注册到 Azure Migrate 项目。

### <a name="generate-the-azure-migrate-project-key"></a>生成 Azure Migrate 项目密钥

1. 在“迁移目标” > “服务器” > “Azure Migrate:  服务器评估”中，选择“发现”。
2. 在“发现计算机” > “计算机是否已虚拟化?”中，选择“物理或其他(AWS、GCP 和 Xen 等)”  。
3. 在“1: 生成 Azure Migrate 项目密钥”中，提供将为发现物理服务器或虚拟服务器而设置的 Azure Migrate 设备的名称。该名称应为不超过 14 个字符的字母数字。
1. 单击“生成密钥”，开始创建所需的 Azure 资源。 在创建资源期间，请不要关闭“发现计算机”页。
1. 成功创建 Azure 资源后，会生成一个 Azure Migrate 项目密钥。
1. 复制密钥，因为配置设备时需要输入该密钥才能完成设备注册。

### <a name="download-the-installer-script"></a>下载安装程序脚本

在“2:下载 Azure Migrate 设备”中，单击“下载”。

   ![对于发现计算机的选择](./media/tutorial-assess-physical/servers-discover.png)


   ![对于生成密钥的选择](./media/tutorial-assess-physical/generate-key-physical.png)

### <a name="verify-security"></a>验证安全性

在部署压缩文件之前检查其安全性。

1. 在下载文件的计算机上，打开管理员命令窗口。
2. 运行以下命令以生成 zip 文件的哈希：
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 公有云的示例用法：```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public.zip SHA256 ```
    - 政府云的示例用法：```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip MD5 ```
3.  验证最新版本的设备和 [哈希值](tutorial-discover-physical.md#verify-security) 设置。
 

## <a name="run-the-azure-migrate-installer-script"></a>运行 Azure Migrate 安装程序脚本
此安装程序脚本执行以下操作：

- 安装用于物理服务器发现和评估的代理和 Web 应用程序。
- 安装 Windows 角色，包括 Windows 激活服务、IIS 和 PowerShell ISE。
- 下载并安装 IIS 可重写模块。 [了解详细信息](https://www.microsoft.com/download/details.aspx?id=7435)。
- 更新 Azure Migrate 的注册表项 (HKLM) 和永久性设置详细信息。
- 在路径下创建以下文件：
    - **配置文件**：%Programdata%\Microsoft Azure\Config
    - **日志文件**：%Programdata%\Microsoft Azure\Logs

按如下所示运行脚本：

1. 将压缩文件解压缩到托管设备的服务器上的某个文件夹中。  请确保不要在现有 Azure Migrate 设备上的计算机中运行该脚本。
2. 使用管理（提升）权限在上述服务器上启动 PowerShell。
3. 将 PowerShell 目录更改为从下载的压缩文件中提取内容的文件夹。
4. 通过运行以下命令，运行名为“AzureMigrateInstaller.ps1”的脚本：

    - 对于公有云： 
    
        ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 ```
    - 对于 Azure 政府： 
    
        ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>.\AzureMigrateInstaller.ps1 ```

    脚本将在成功完成时启动设备 Web 应用程序。

如果遇到任何问题，可以访问位于 C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log 的脚本日志来进行故障排除。



### <a name="verify-appliance-access-to-azure"></a>验证设备的 Azure 访问权限

确保设备 VM 可以连接到[公有云](migrate-appliance.md#public-cloud-urls)和[政府云](migrate-appliance.md#government-cloud-urls)的 Azure URL。

### <a name="configure-the-appliance"></a>配置设备

首次设置设备。

1. 在可连接到该设备的任一计算机上打开浏览器，然后打开设备 Web 应用的 URL： https://设备名称或 IP 地址:44368。

   或者，可以在桌面上单击应用快捷方式打开该应用。
2. 接受许可条款，并阅读第三方信息。
1. 在 Web 应用 >“设置必备组件”中执行以下操作：
    - **连接**：应用将检查服务器是否可访问 Internet。 如果服务器使用代理：
        - 单击“设置代理”，并指定代理地址（格式为 http://ProxyIPAddress 或 http://ProxyFQDN) ）和侦听端口。
        - 如果代理需要身份验证，请指定凭据。
        - 仅支持 HTTP 代理。
        - 如果已添加代理详细信息或已禁用代理和/或身份验证，请单击“保存”，再次触发连接性检查。
    - **时间同步**：将验证时间。 设备上的时间应与 Internet 时间同步，这样才能正常发现服务器。
    - **安装更新**：Azure Migrate 服务器评估检查设备是否安装了最新更新。检查完成后，可以单击“查看设备服务”，查看设备上运行的组件的状态和版本。

### <a name="register-the-appliance-with-azure-migrate"></a>将设备注册到 Azure Migrate

1. 粘贴从门户复制的 Azure Migrate 项目密钥。 如果没有密钥，请转到“服务器评估”>“发现”>“管理现有设备”，选择在生成密钥时提供的设备名称，然后复制相应的密钥。
1. 单击“登录”。 这将在新的浏览器选项卡中打开 Azure 登录提示。如果未显示该按钮，请确保已在浏览器中禁用弹出窗口阻止程序。
1. 在新选项卡上，使用 Azure 用户名和密码登录。
   
   不支持使用 PIN 登录。
3. 成功登录后，返回到 Web 应用。 
4. 如果用于登录的 Azure 用户帐户对在密钥生成过程中创建的 Azure 资源具有恰当的[权限](tutorial-prepare-physical.md)，会启动设备注册。
1. 成功注册设备后，可以通过单击“查看详细信息”来查看注册详细信息。


## <a name="start-continuous-discovery"></a>启动持续发现

现在，从设备连接到要发现的物理服务器，并启动发现。

1. 在**步骤 1：提供用于发现 Windows 和 Linux 物理服务器或虚拟服务器的凭据**中，单击“添加凭据”，为凭据指定一个易记名称，并为 Windows 或 Linux 服务器添加“用户名”和“密码”  。 单击“保存” 。
1. 如果要一次添加多个凭据，请单击“添加更多”，以保存和添加更多凭据。 物理服务器发现支持多个凭据。
1. 在“步骤 2：提供物理服务器或虚拟服务器详细信息”中，单击“添加发现源”，以指定服务器 IP地址/FQDN 以及为用于连接到服务器的凭据指定易记名称  。
1. 可以一次“添加单个项目”，也可以一次“添加多个项目” 。 还有一个选项是通过“导入 CSV”提供服务器详细信息。

    ![对于添加发现源的选择](./media/tutorial-assess-physical/add-discovery-source-physical.png)

    - 如果选择“添加单个项目”，则可以选择 OS 类型，为凭据指定一个易记名称，并添加服务器 IP地址/FQDN，然后单击“保存”  。
    - 如果选择“添加多个项目”，则可以通过在文本框中指定服务器 IP 地址/FQDN 并为凭据指定一个易记名称来一次添加多个记录。验证添加的记录并单击“保存”   。
    - 如果选择“导入 CSV”（默认已选），可以下载 CSV 模板文件，并使用服务器 IP地址/FQDN 和凭据的易记名称填充文件。 然后，将该文件导入设备，验证文件中的记录，然后单击“保存” 。

1. 单击保存后，设备将尝试验证与已添加服务器的连接，并在每个服务器的表中显示“验证状态”。
    - 如果服务器验证失败，请通过单击表中“状态”列中的“验证失败”来查看错误。 解决此问题，然后再次验证。
    - 若要删除服务器，请单击“删除”。
1. 在开始发现之前，可以随时重新验证与服务器之间的连接。
1. 单击“开始发现”，开始发现已成功验证的服务器。 成功启动发现后，你可以针对表中的每个服务器检查发现状态。


随即会启动发现。 每台服务器大约需要 2 分钟，才能将已发现的服务器的元数据显示在 Azure 门户中。

## <a name="verify-servers-in-the-portal"></a>验证门户中的服务器

发现完成后，可以验证服务器是否显示在门户中。

1. 打开 Azure Migrate 仪表板。
2. 在“Azure Migrate - 服务器” > “Azure Migrate: 服务器评估”页中，单击显示了**已发现服务器**计数的图标。


## <a name="next-steps"></a>后续步骤

试用 Azure Migrate 服务器评估 [的物理服务器评估](tutorial-assess-physical.md) 。
