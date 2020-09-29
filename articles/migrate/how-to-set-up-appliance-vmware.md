---
title: 为 VMware 设置 Azure Migrate 设备
description: 了解如何设置 Azure Migrate 设备来评估和迁移 VMware Vm。
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: 9a4e652180b236262ea57ae49d35410ebfbdc927
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91448644"
---
# <a name="set-up-an-appliance-for-vmware-vms"></a>为 VMware Vm 设置设备

按照本文所述，使用 [Azure Migrate： Server 评估](migrate-services-overview.md#azure-migrate-server-assessment-tool) 工具设置 Azure Migrate 设备进行评估，并使用 [Azure Migrate： server 迁移](migrate-services-overview.md#azure-migrate-server-migration-tool) 工具进行无代理迁移。

[Azure Migrate 设备](migrate-appliance.md)是一种轻型设备，由 Azure Migrate：服务器评估和服务器迁移，用于发现本地 VMware vm、将 VM 元数据/性能数据发送到 Azure，以及在无代理迁移期间复制 VMware vm。

可以使用几种方法来部署设备：

- 使用下载的 OVA 模板在 VMware VM 上进行设置。 这是本文中所述的方法。
- 使用 PowerShell 安装程序脚本在 VMware VM 或物理计算机上进行设置。 如果无法使用 OVA 模板设置 VM，或者你使用的是 Azure 政府，则应使用[此方法](deploy-appliance-script.md)。

创建设备后，请检查它是否可以连接到 Azure Migrate:服务器评估，首次配置该设备，并将其注册到 Azure Migrate 项目。


## <a name="appliance-deployment-ova"></a>设备部署 (.OVA) 

若要使用 .OVA 模板设置设备，请执行以下操作：
- 提供设备名称并在门户中生成 Azure Migrate 项目密钥
- 下载 OVA 模板文件，并将其导入 vCenter Server。
- 创建设备，并检查它是否可以连接到 Azure Migrate 服务器评估。
- 第一次配置设备，并使用 Azure Migrate 项目密钥将其注册到 Azure Migrate 项目。

### <a name="generate-the-azure-migrate-project-key"></a>生成 Azure Migrate 项目密钥

1. 在“迁移目标” > “服务器” > “Azure Migrate:  服务器评估”中，选择“发现”。
2. 在“发现计算机” > “计算机是否已虚拟化?”中，选择“是，使用 VMware vSphere 虚拟机监控程序”  。
3. 在“1: 生成 Azure Migrate 项目密钥”中，提供将为发现 VMware VM 而设置的 Azure Migrate 设备的名称。该名称应为不超过 14 个字符的字母数字。
1. 单击“生成密钥”，开始创建所需的 Azure 资源。 在创建资源期间，请不要关闭“发现计算机”页。
1. 成功创建 Azure 资源后，会生成一个 Azure Migrate 项目密钥。
1. 复制密钥，因为配置设备时需要输入该密钥才能完成设备注册。

### <a name="download-the-ova-template"></a>下载 OVA 模板
在“2:下载 Azure Migrate 设备”中，选择 .OVA 文件并单击“下载”。 


   ![对于发现计算机的选择](./media/tutorial-assess-vmware/servers-discover.png)


   ![对于生成密钥的选择](./media/tutorial-assess-vmware/generate-key-vmware.png)

### <a name="verify-security"></a>验证安全性

在部署 .OVA 文件之前请检查其安全性。

1. 在下载文件的计算机上，打开管理员命令窗口。
2. 运行以下命令，生成 .OVA 的哈希：
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 用法示例：```C:\>C:\>CertUtil -HashFile C:\Users\Administrator\Desktop\MicrosoftAzureMigration.ova SHA256```
3. 对于最新的设备版本，生成的哈希应与这些 [设置](./tutorial-assess-vmware.md#verify-security)相符。



## <a name="create-the-appliance-vm"></a>创建设备 VM

导入下载的文件，然后创建 VM。

1. 在 vSphere 客户端控制台中，单击 "**文件**" "  >  **部署 OVF 模板**"。
![用于部署 OVF 模板的菜单命令](./media/tutorial-assess-vmware/deploy-ovf.png)

2. 在“部署 OVF 模板向导”>“源”中，指定 OVA 文件的位置。
3. 在“名称”和“位置”中，为 VM 指定一个易记名称。 选择要在其中托管 VM 的库存对象。
5. 在“主机/群集”中，指定要在其上运行 VM 的主机或群集。
6. 在“存储”中，指定 VM 的存储目标。
7. 在“磁盘格式”中，指定磁盘类型和大小。
8. 在 " **网络映射**" 中，指定 VM 将连接到的网络。 该网络需要与 Internet 建立连接，这样才能向 Azure Migrate 服务器评估发送元数据。
9. 检查并确认设置，然后单击“完成”****。


## <a name="verify-appliance-access-to-azure"></a>验证设备的 Azure 访问权限

确保设备 VM 可以连接到[公有云](migrate-appliance.md#public-cloud-urls)和[政府云](migrate-appliance.md#government-cloud-urls)的 Azure URL。


### <a name="configure-the-appliance"></a>配置设备

首次设置设备。

> [!NOTE]
> 如果使用 [PowerShell 脚本](deploy-appliance-script.md)而不是下载的 OVA 设置设备，则此过程中的前两个步骤不相关。

1. 在 vSphere 客户端控制台中右键单击“VM”，然后选择“打开控制台”。
2. 提供设备的语言、时区和密码。
3. 在可连接到该 VM 的任一计算机上打开浏览器，然后打开设备 Web 应用的 URL：**https://*设备名称或 IP 地址*:** 44368。

   或者，可以在设备桌面上选择应用快捷方式打开该应用。
1. 接受许可条款，并阅读第三方信息。
1. 在 Web 应用 >“设置必备组件”中执行以下操作：
   - **连接**：应用将检查 VM 是否可访问 Internet。 如果 VM 使用代理：
     - 单击“设置代理”，指定代理地址（格式为 http://ProxyIPAddress 或 http://ProxyFQDN) ）以及侦听端口。
     - 如果代理需要身份验证，请指定凭据。
     - 仅支持 HTTP 代理。
     - 如果已添加代理详细信息或已禁用代理和/或身份验证，请单击“保存”，再次触发连接性检查。
   - **时间同步**：设备上的时间应与 Internet 时间同步，这样才能正常进行发现。
   - **安装更新**：设备可确保安装最新的更新。 检查完成后，可以单击“查看设备服务”查看设备上运行的组件的状态和版本。
   - **安装 VDDK**：设备将检查是否已安装 VMware vSphere 虚拟磁盘开发工具包 (VDDK)。 从 VMware 下载 VDDK 6.7（如果尚未安装它），并按照“安装说明”中提供的信息将下载的 zip 内容解压缩到设备上的指定位置。

     Azure Migrate 服务器迁移在迁移到 Azure 期间使用 VDDK 复制计算机。 
1. 如果需要，可以在设备配置期间随时重新运行先决条件，以检查设备是否满足所有先决条件。

### <a name="register-the-appliance-with-azure-migrate"></a>将设备注册到 Azure Migrate

1. 粘贴从门户复制的 Azure Migrate 项目密钥。 如果没有密钥，请转到“服务器评估”>“发现”>“管理现有设备”，选择在生成密钥时提供的设备名称，然后复制相应的密钥。
1. 单击“登录”。 这将在新的浏览器选项卡中打开 Azure 登录提示。如果未显示该按钮，请确保已在浏览器中禁用弹出窗口阻止程序。
1. 在新选项卡上，使用 Azure 用户名和密码登录。
   
   不支持使用 PIN 登录。
3. 成功登录后，返回到 Web 应用。 
4. 如果用于登录的 Azure 用户帐户对在密钥生成过程中创建的 Azure 资源具有恰当的[权限](tutorial-prepare-vmware.md#prepare-azure)，会启动设备注册。
1. 成功注册设备后，可以通过单击“查看详细信息”来查看注册详细信息。


## <a name="start-continuous-discovery"></a>启动持续发现

设备需连接 vCenter Server，以发现 VM 的配置和性能数据。

1. 在**步骤 1：提供 vCenter Server 凭据**中，单击“添加凭据”以指定凭据的易记名称，为设备将用于在 vCenter Server 实例上发现 VM 的 vCenter Server 帐户添加“用户名”和“密码”  。
    - 你应该已在[上一教程](tutorial-prepare-vmware.md#set-up-permissions-for-assessment)中设置具有所需权限的帐户。
    - 如果要将发现范围限定于特定的 VMware 对象（vCenter Server 数据中心、群集、群集文件夹、主机、主机文件夹或单个 VM），请参阅[此文](set-discovery-scope.md)中的说明，以限制 Azure Migrate 所使用的帐户。
1. 在**步骤 2：提供 vCenter Server 详细信息**中，单击“添加发现源”，从下拉列表中选择凭据的易记名称，指定 vCenter Server 实例的 IP 地址/FQDN 。 可以保留“端口”的默认值 443，或指定 vCenter Server 侦听的自定义端口，然后单击“保存” 。
1. 单击“保存”后，设备将尝试通过提供的凭据验证与 vCenter Server 的连接，并根据 vCenter Server IP 地址/FQDN 在表中显示验证状态。
1. 在开始发现之前，可以随时重新验证与 vCenter Server 之间的连接。
1. 在**步骤 3：提供 VM 凭据来发现已安装的应用程序并执行无代理依赖项映射**中，单击“添加凭据”，并指定为其提供凭据的操作系统、凭据的易记名称以及“用户名”和“密码”  。 然后单击“保存”。

    - 如果已创建用于[应用程序发现功能](how-to-discover-applications.md)或[无代理依赖项分析功能](how-to-create-group-machine-dependencies-agentless.md)的帐户，则可选择在此处添加凭据。
    - 如果不想使用这些功能，请通过单击滑块跳过该步骤。 你以后可以随时恢复使用这些功能。
    - 查看[应用程序发现](migrate-support-matrix-vmware.md#application-discovery-requirements)或[无代理依赖项分析](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless)所需的凭据。

5. 单击“开始发现”以启动 VM 发现。 成功启动发现后，可以针对表中的 vCenter Server IP 地址/FQDN 检查发现状态。

发现的工作原理如下：
- 大约 15 分钟后，已发现 VM 的元数据会显示在门户中。
- 发现已安装的应用程序、角色和功能需要一些时间， 具体时间取决于待发现的 VM 数量。 如果是 500 个 VM，Azure Migrate 门户大约需要一小时才会显示应用程序清单。

## <a name="next-steps"></a>后续步骤

查看 [VMware 评估](tutorial-assess-vmware.md) 和 [无代理迁移](tutorial-migrate-vmware.md)教程。
