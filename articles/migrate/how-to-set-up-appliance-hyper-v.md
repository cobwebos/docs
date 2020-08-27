---
title: 为 Hyper-v 设置 Azure Migrate 设备
description: 了解如何设置 Azure Migrate 设备来评估和迁移 Hyper-v Vm。
ms.topic: article
ms.date: 03/23/2020
ms.openlocfilehash: 21d88c4a2b2095fe677fe479bd7320f7a494db9e
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2020
ms.locfileid: "88929905"
---
# <a name="set-up-an-appliance-for-hyper-v-vms"></a>为 Hyper-v Vm 设置设备

按照本文所述设置 Azure Migrate 设备，以便通过 [Azure Migrate：服务器评估](migrate-services-overview.md#azure-migrate-server-assessment-tool) 工具评估 hyper-v vm。

[Azure Migrate 设备](migrate-appliance.md)是 Azure Migrate 使用的轻型设备，用于发现本地 hyper-v vm，并将 VM 元数据/性能数据发送到 Azure。

可以使用几种方法来部署设备：

- 使用下载的 VHD 在 Hyper-v VM 上设置。 这是本文中所述的方法。
- 使用 PowerShell 安装程序脚本在 Hyper-V VM 或物理计算机上进行设置。 如果无法使用 VHD 设置 VM，或者在 Azure 政府版中，则应使用[此方法](deploy-appliance-script.md)。

创建设备后，请检查它是否可以连接到 Azure Migrate:服务器评估，首次配置该设备，并将其注册到 Azure Migrate 项目。

## <a name="appliance-deployment-vhd"></a>设备部署 (VHD) 

使用 VHD 模板设置设备：

- 提供设备名称并在门户中生成 Azure Migrate 项目项。
- 从 Azure 门户下载压缩的 Hyper-V VHD。
- 创建设备，并检查它是否可以连接到 Azure Migrate 服务器评估。
- 第一次配置设备，并使用 Azure Migrate 项目密钥将其注册到 Azure Migrate 项目。

### <a name="generate-the-azure-migrate-project-key"></a>生成 Azure Migrate 项目密钥

1. 在“迁移目标” > “服务器” > “Azure Migrate:  服务器评估”中，选择“发现”。
2. 在“发现计算机” > “计算机是否已虚拟化?”中，选择“是，使用 Hyper-V”。  
3. 在 **1：生成 Azure Migrate 项目密钥**中，提供将为 hyper-v vm 的发现设置的 Azure Migrate 设备的名称。该名称的字母数字应为14个字符或更少。
1. 单击 " **生成密钥** " 开始创建所需的 Azure 资源。 请不要在创建资源的过程中关闭 "发现计算机" 页。
1. 成功创建 Azure 资源后，将生成 **Azure Migrate 项目密钥** 。
1. 复制密钥，因为在配置期间完成设备注册需要用到它。

### <a name="download-the-vhd"></a>下载 VHD

在 **2：下载 Azure Migrate 设备**上，选择。VHD 文件，然后单击 " **下载**"。 

   ![用于发现计算机的选项](./media/tutorial-assess-hyper-v/servers-discover.png)


   ![用于生成密钥的选项](./media/tutorial-assess-hyper-v/generate-key-hyperv.png)


### <a name="verify-security"></a>验证安全性

在部署压缩文件之前检查其安全性。

1. 在下载文件的计算机上，打开管理员命令窗口。
2. 运行以下命令以生成 VHD 的哈希
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 用法示例：```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.vhd SHA256```
3.  对于设备版本2.19.11.12，生成的哈希应与这些 [设置](./tutorial-assess-hyper-v.md#verify-security)相符。




## <a name="create-the-appliance-vm"></a>创建设备 VM

导入下载的文件，然后创建 VM。

1. 将压缩的 VHD 文件解压缩到托管设备 VM 的 Hyper-V 主机上的某个文件夹中。 将解压缩三个文件夹。
2. 打开 Hyper-V 管理器。 在“操作”中，单击“导入虚拟机”。 

    ![部署 VHD](./media/how-to-set-up-appliance-hyper-v/deploy-vhd.png)

2. 在“导入虚拟机向导”>“开始之前”中，单击“下一步”。 
3. 在“查找文件夹”中，指定包含已解压缩的 VHD 的文件夹。**** 然后单击“下一步”  。
1. 在“选择虚拟机”中，单击“下一步”。 
2. 在“选择导入类型”中，单击“复制虚拟机(创建新的唯一 ID)”。  然后单击“下一步”  。
3. 在“选择目标”中保留默认设置。 单击“下一步”。
4. 在“存储文件夹”中保留默认设置。 单击“下一步”。
5. 在“选择网络”中，指定 VM 要使用的虚拟交换机。 该交换机需要与 Internet 建立连接才能向 Azure 发送数据。
6. 在“摘要”中检查设置。 然后单击“完成”。
7. 在“Hyper-V 管理器”>“虚拟机”中启动 VM。


### <a name="verify-appliance-access-to-azure"></a>验证设备的 Azure 访问权限

确保设备 VM 可以连接到[公有云](migrate-appliance.md#public-cloud-urls)和[政府云](migrate-appliance.md#government-cloud-urls)的 Azure URL。

### <a name="configure-the-appliance"></a>配置设备

首次设置设备。

> [!NOTE]
> 如果使用 [PowerShell 脚本](deploy-appliance-script.md)而不是下载的 VHD 设置设备，则此过程中的前两个步骤不相关。

1. 在“Hyper-V 管理器”>“虚拟机”中，右键单击该 VM 并选择“连接”。 
2. 提供设备的语言、时区和密码。
3. 在可连接到该 VM 的任一计算机上打开浏览器，然后打开设备 Web 应用的 URL：**https://*设备名称或 IP 地址*:** 44368。

   或者，可以在设备桌面上单击应用快捷方式打开该应用。
1. 接受 **许可条款**，并阅读第三方信息。
1. 在 Web 应用 >“设置必备组件”中执行以下操作：
    - **连接**：应用将检查 VM 是否可访问 Internet。 如果 VM 使用代理：
      - 单击 " **设置代理** "，并在窗体 http://ProxyIPAddress 或侦听端口中指定代理地址 (http://ProxyFQDN) 。
      - 如果代理需要身份验证，请指定凭据。
      - 仅支持 HTTP 代理。
      - 如果已添加代理详细信息或禁用代理和/或身份验证，请单击 " **保存** " 再次触发连接检查。
    - **时间同步**：将验证时间。 设备上的时间应与 Internet 时间同步，这样才能正常发现 VM。
    - **安装更新**： Azure Migrate Server 评估检查设备是否安装了最新更新。检查完成后，可以单击 " **查看设备服务** " 查看设备上运行的组件的状态和版本。

### <a name="register-the-appliance-with-azure-migrate"></a>将设备注册到 Azure Migrate

1. 粘贴从门户复制的 **Azure Migrate 项目项** 。 如果没有密钥，请参阅 " **服务器评估"> 发现> 管理现有设备**"，选择在生成密钥时提供的设备名称并复制相应的密钥。
1. 单击 " **登录**"。 这会在新的浏览器选项卡中打开 Azure 登录提示。如果未显示，请确保在浏览器中禁用了弹出窗口阻止程序。
1. 在新选项卡上，使用 Azure 用户名和密码登录。
   
   不支持使用 PIN 登录。
3. 成功登录后，返回到 web 应用。 
4. 如果用于日志记录的 Azure 用户帐户对密钥生成过程中创建的 Azure 资源具有适当的 [权限](tutorial-prepare-hyper-v.md#prepare-azure) ，则将启动设备注册。
1. 成功注册设备后，可通过单击 " **查看详细信息**" 来查看注册详细信息。



### <a name="delegate-credentials-for-smb-vhds"></a>为 SMB VHD 委托凭据

如果在 SMB 上运行 VHD，必须启用从设备到 Hyper-V 主机的凭据委托。 若要从设备执行此操作：

1. 在设备 VM 上运行此命令。 HyperVHost1/HyperVHost2 是示例主机名。

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
    ```

2. 或者，在设备上的本地组策略编辑器中执行此操作：
    - 在“本地计算机策略” > “计算机配置”中，单击“管理模板” > “系统” > “凭据委托”。    
    - 双击“允许委托新凭据”，并选择“已启用”。 
    - 在“选项”中单击“显示”，将要发现的每台 Hyper-V 主机添加到列表中，并使用 wsman/ 作为前缀。  
    - 在“凭据委托”中，双击“允许允许新凭据并使用仅限 NTLM 的服务器身份验证”。******** 再次将要发现的每台 Hyper-V 主机添加到列表中，并使用 **wsman/** 作为前缀。

## <a name="start-continuous-discovery"></a>启动持续发现

从设备连接到 Hyper-V 主机或群集，并启动 VM 发现。

1. 在 **步骤1：提供 hyper-v 主机凭据**中，单击 " **添加凭据** " 以指定凭据的友好名称，添加设备将用于发现 vm 的 hyper-v 主机/群集的 **用户名** 和 **密码** 。 单击“保存” 。
1. 如果要一次添加多个凭据，请单击 " **添加更多** " 以保存并添加更多凭据。 Hyper-v Vm 发现支持多个凭据。
1. 在 **步骤2：提供 hyper-v 主机/群集详细信息**中，单击 " **添加发现源** " 以指定 Hyper-v 主机/群集 **IP 地址/FQDN** 和用于连接到主机/群集的凭据的友好名称。
1. 可以一次 **添加一个项** ，也可以在一次中 **添加多个项** 。 还提供了通过 **导入 CSV**提供 hyper-v 主机/群集详细信息的选项。

    ![用于添加发现源的选择](./media/tutorial-assess-hyper-v/add-discovery-source-hyperv.png)

    - 如果选择 " **添加单个项**"，则需要为凭据和 hyper-v 主机/群集 **IP 地址/FQDN** 指定友好名称，然后单击 " **保存**"。
    - 如果选择 " **添加多个项**" _ (默认情况下选择) _，则可以通过在文本框中指定具有凭据的友好名称的 Hyper-v 主机/群集 **IP 地址/FQDN** ，一次添加多个记录。**验证** 已添加的记录，然后单击 " **保存**"。
    - 如果选择 " **导入 csv**"，则可以下载 csv 模板文件，使用 hyper-v 主机/群集 **IP 地址/FQDN** 和凭据的友好名称填充文件。 然后，将该文件导入设备， **验证** 文件中的记录，然后单击 " **保存**"。

1. 单击 "保存" 后，设备将尝试验证与添加的 Hyper-v 主机/群集的连接，并针对每个主机/群集显示表中的 **验证状态** 。
    - 对于成功验证的主机/群集，可以通过单击其 IP 地址/FQDN 来查看更多详细信息。
    - 如果主机验证失败，请单击表的 "状态" 列中的 " **验证失败** " 来查看错误。 解决问题，然后重新验证。
    - 若要删除主机或群集，请单击 " **删除**"。
    - 无法删除群集中的特定主机。 只能删除整个群集。
    - 即使群集中的特定主机出现问题，你也可以添加群集。
1. 在开始发现之前，你可以随时重新 **验证** 到主机/群集的连接。
1. 单击 " **启动发现**"，从已成功验证的主机/群集中启动 VM 发现。 成功启动发现后，你可以根据表中的每个主机/群集来检查发现状态。

随即会启动发现。 每台主机大约需要2分钟的时间，发现的服务器的元数据才会出现在 Azure 门户中。

## <a name="verify-vms-in-the-portal"></a>在门户中验证 VM

发现完成后，可以验证 VM 是否出现在门户中。

1. 打开 Azure Migrate 仪表板。
2. 在“Azure Migrate - 服务器” > “Azure Migrate: 服务器评估”页中，单击显示了**已发现服务器**计数的图标。


## <a name="next-steps"></a>后续步骤

试用 Azure Migrate Server 评估的 [hyper-v 评估](tutorial-assess-hyper-v.md) 。
