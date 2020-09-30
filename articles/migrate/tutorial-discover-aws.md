---
title: 使用 Azure Migrate 服务器评估发现 AWS 实例
description: 了解如何使用 Azure Migrate 服务器评估发现 AWS 实例。
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: mvc
ms.openlocfilehash: c2d91e0b2c2eaa2df8b01aca60e5a0e18e251fb8
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/16/2020
ms.locfileid: "90603690"
---
# <a name="tutorial-discover-aws-instances-with-server-assessment"></a>教程：使用服务器评估发现 AWS 实例

在迁移到 Azure 的过程中，你将发现用于评估和迁移的服务器。

本教程介绍如何使用 Azure Migrate 发现 Amazon Web Services (AWS) 实例：服务器评估工具，使用轻型 Azure Migrate 设备。 需要将设备部署为物理服务器，以持续发现计算机和性能元数据。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 设置 Azure 帐户。
> * 准备 AWS 实例以进行发现。
> * 创建 Azure Migrate 项目。
> * 设置 Azure Migrate 设备。
> * 启动持续发现。

> [!NOTE]
> 教程显示尝试方案的最快路径，并使用默认选项。  

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="prerequisites"></a>先决条件

在开始本教程之前，请先检查是否已满足这些先决条件。

**要求** | **详细信息**
--- | ---
**设备** | 需要一台运行 Azure Migrate 设备的 EC2 VM。 计算机应：<br/><br/> - 已安装 Windows Server 2016。 不支持在具有 Windows Server 2019 的计算机上运行设备。<br/><br/> - 具有 16-GB RAM、8 个 vCPU、约 80 GB 的磁盘存储和外部虚拟交换机。<br/><br/> - 静态或动态 IP 地址，可直接访问或通过代理访问 Internet。
**Windows 实例** | 允许 WinRM 端口 5985 (HTTP) 上的入站连接，使设备可以拉取配置和性能元数据。
**Linux 实例** | 允许端口 22 (TCP) 上的入站连接。

## <a name="prepare-an-azure-user-account"></a>准备 Azure 用户帐户

若要创建 Azure Migrate 项目并注册 Azure Migrate 设备，需要一个具有以下权限的帐户：
- Azure 订阅的参与者或所有者权限。
- 用于注册 Azure Active Directory 应用的权限。

如果你刚刚创建了免费的 Azure 帐户，那么你就是订阅的所有者。 如果你不是订阅所有者，请让所有者分配权限，如下所示：

1. 在 Azure 门户中，搜索“订阅”，然后在“服务”下选择“订阅” 。

    ![用于搜索 Azure 订阅的搜索框](./media/tutorial-discover-aws/search-subscription.png)

2. 在“订阅”页上，选择要在其中创建 Azure Migrate 项目的订阅。 
3. 在“订阅”中，选择“访问控制 (IAM)” > “检查访问权限” 。
4. 在“检查访问权限”中，搜索相关的用户帐户。
5. 在“添加角色分配”中，单击“添加” 。

    ![搜索用户帐户，检查访问权限，并分配角色](./media/tutorial-discover-aws/azure-account-access.png)

6. 在“添加角色分配”中，选择“参与者”或“所有者”角色，然后选择帐户（在我们的示例中为 azmigrateuser）。 然后单击“保存”  。

    ![打开“添加角色分配”页，将角色分配给帐户](./media/tutorial-discover-aws/assign-role.png)

7. 在门户中搜索用户，然后在“服务”下，选择“用户” 。
8. 在“用户设置”中，验证 Azure AD 用户是否可以注册应用程序（默认情况下设置为“是”） 。

    ![在用户设置中，验证用户是否可以注册 Active Directory 应用](./media/tutorial-discover-aws/register-apps.png)


## <a name="prepare-aws-instances"></a>准备 AWS 实例

设置设备可用于访问 AWS 实例的帐户。

- 对于 Windows 服务器，在要包括在发现中的所有 Windows 服务器上设置一个本地用户帐户。 将该用户帐户添加到以下组：- 远程管理用户 - 性能监视器用户 - 性能日志用户。
 - 对于 Linux 服务器，需要在要发现的 Linux 服务器上拥有根帐户。


## <a name="set-up-a-project"></a>设置项目

设置新的 Azure Migrate 项目。

1. 在 Azure 门户中选择“所有服务”，然后搜索 **Azure Migrate**。
2. 在“服务”下选择“Azure Migrate”。 
3. 在“概述”中，选择“创建项目” 。
5. 在“创建项目”中，选择 Azure 订阅和资源组。 如果没有资源组，请创建一个资源组。
6. 在“项目详细信息”中，指定项目名称以及要在其中创建项目的地理位置。 查看[公有云](migrate-support-matrix.md#supported-geographies-public-cloud)和[政府云](migrate-support-matrix.md#supported-geographies-azure-government)支持的地理位置。

   ![用于项目名称和区域的框](./media/tutorial-discover-aws/new-project.png)

7. 选择“创建”。
8. 等待几分钟，让 Azure Migrate 项目部署完成。

默认会将“Azure Migrate:服务器评估”工具添加到新项目。

![显示默认情况下已添加的服务器评估工具的页面](./media/tutorial-discover-aws/added-tool.png)

## <a name="set-up-the-appliance"></a>设置设备

Azure Migrate 设备是一种轻型设备，由 Azure Migrate 服务器评估用于执行以下操作：

- 发现本地服务器。
- 将已发现的服务器的元数据和性能数据发送到 Azure Migrate 服务器评估。

[详细了解](migrate-appliance.md) Azure Migrate 设备。


## <a name="appliance-deployment-steps"></a>设备部署步骤

若要设置该设备，请执行以下操作：
- 提供设备名称，并在门户中生成 Azure Migrate 项目密钥。
- 从 Azure 门户下载带有 Azure Migrate 安装程序脚本的压缩文件。
- 从压缩文件中提取内容。 使用管理权限启动 PowerShell 控制台。
- 执行 PowerShell 脚本以启动设备 Web 应用程序。
- 完成设备的首次配置，并使用 Azure Migrate 项目密钥将其注册到 Azure Migrate 项目。

### <a name="generate-the-azure-migrate-project-key"></a>生成 Azure Migrate 项目密钥

1. 在“迁移目标” > “服务器” > “Azure Migrate:  服务器评估”中，选择“发现”。
2. 在“发现计算机” > “计算机是否已虚拟化?”中，选择“物理或其他(AWS、GCP 和 Xen 等)”  。
3. 在“1: 生成 Azure Migrate 项目密钥”中，提供将为发现物理服务器或虚拟服务器而设置的 Azure Migrate 设备的名称。该名称应为不超过 14 个字符的字母数字。
1. 单击“生成密钥”，开始创建所需的 Azure 资源。 在创建资源期间，请不要关闭“发现计算机”页。
1. 成功创建 Azure 资源后，会生成一个 Azure Migrate 项目密钥。
1. 复制密钥，因为配置设备时需要输入该密钥才能完成设备注册。

### <a name="download-the-installer-script"></a>下载安装程序脚本

在“2:下载 Azure Migrate 设备”中，单击“下载”。


### <a name="verify-security"></a>验证安全性

在部署压缩文件之前检查其安全性。

1. 在下载文件的计算机上，打开管理员命令窗口。
2. 运行以下命令以生成 zip 文件的哈希：
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 公有云的示例用法：```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public.zip SHA256 ```
    - 政府云的示例用法：```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip SHA256 ```
3.  验证最新的设备版本和哈希值：
    - 对于公有云：

        **方案** | **下载*** | **哈希值**
        --- | --- | ---
        物理 (85 MB) | [最新版本](https://go.microsoft.com/fwlink/?linkid=2140334) | 207157bab39303dca1c2b93562d6f1deaa05aa7c992f480138e17977641163fb

    - 对于 Azure 政府：

        **方案** | **下载*** | **哈希值**
        --- | --- | ---
        物理 (85 MB) | [最新版本](https://go.microsoft.com/fwlink/?linkid=2140338) | ca67e8dbe21d113ca93bfe94c1003ab7faba50472cb03972d642be8a466f78ce
 

### <a name="run-the-azure-migrate-installer-script"></a>运行 Azure Migrate 安装程序脚本
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

发现完成后，可以验证服务器是否出现在门户中。

1. 打开 Azure Migrate 仪表板。
2. 在“Azure Migrate - 服务器” > “Azure Migrate: 服务器评估”页中，单击显示了**已发现服务器**计数的图标。

## <a name="next-steps"></a>后续步骤

- [评估物理服务器](tutorial-migrate-aws-virtual-machines.md)以便迁移到 Azure VM。
- [查看设备在发现期间收集的数据](migrate-appliance.md#collected-data---physical)。
