---
title: 使用 Azure Migrate 评估要迁移到 Azure 的 Hyper-V VM | Microsoft Docs
description: 介绍如何使用 Azure Migrate 服务器评估工具评估要迁移到 Azure 的本地 Hyper-V VM。
ms.topic: tutorial
ms.date: 06/03/2020
ms.custom: mvc
ms.openlocfilehash: eae7e2d371ed8156debe9ae24cf0744bd6273943
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2020
ms.locfileid: "88950266"
---
# <a name="assess-hyper-v-vms-with-azure-migrate-server-assessment"></a>使用 Azure Migrate 服务器评估工具评估 Hyper-V VM

本文介绍如何使用 [Azure Migrate:服务器评估](migrate-services-overview.md#azure-migrate-server-assessment-tool)工具评估本地 Hyper-V VM。


本教程是演示如何评估 Hyper-V VM 以及将其迁移到 Azure 的教程系列中的第二篇文章。 在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 设置一个 Azure Migrate 项目。
> * 设置并注册 Azure Migrate 设备。
> * 开始连续发现本地 VM。
> * 分组已发现的 VM 并评估该组。
> * 查看评估。

> [!NOTE]
> 教程中演示了方案的最简单部署路径，使你能够快速设置概念证明。 教程尽可能使用默认选项，不会演示所有可能的设置和路径。 有关详细说明，请查看操作指南文章。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。


## <a name="prerequisites"></a>先决条件

- [完成](tutorial-prepare-hyper-v.md)本教程系列中的第一篇教程。 否则，本教程中的说明不适用。
- 下面是在第一篇教程中应已完成的操作：
    - [准备 Azure](tutorial-prepare-hyper-v.md#prepare-azure) 以使用 Azure Migrate。
    - [准备 Hyper-V](tutorial-prepare-hyper-v.md#prepare-for-assessment) 主机和 VM 评估。
    - [验证](tutorial-prepare-hyper-v.md#prepare-for-appliance-deployment)部署用于 Hyper-V 评估的 Azure Migrate 设备所需的内容。

## <a name="set-up-an-azure-migrate-project"></a>设置 Azure Migrate 项目

1. 在 Azure 门户中选择“所有服务”，然后搜索 **Azure Migrate**。
2. 在搜索结果中，选择“Azure Migrate”。
3. 在“概述”中的“发现、评估和迁移服务器”下，单击“评估和迁移服务器”。  

    ![发现和评估服务器](./media/tutorial-assess-hyper-v/assess-migrate.png)

4. 在“开始”中，单击“添加工具”。 
5. 在“迁移项目”选项卡中选择你的 Azure 订阅，并创建一个资源组（如果没有）。
6. 在“项目详细信息”中，指定项目名称以及要在其中创建项目的区域。 查看[公有云](migrate-support-matrix.md#supported-geographies-public-cloud)和[政府云](migrate-support-matrix.md#supported-geographies-azure-government)支持的地理位置。

    - 项目区域仅用于存储从本地 VM 中收集的元数据。
    - 迁移 VM 时，可以选择其他 Azure 目标区域。 迁移目标支持所有 Azure 区域。

    ![创建 Azure Migrate 项目](./media/tutorial-assess-hyper-v/migrate-project.png)

7. 单击“下一步”。
8. 在“选择评估工具”中，选择“Azure Migrate: 服务器评估” > “下一步”。

    ![创建 Azure Migrate 项目](./media/tutorial-assess-hyper-v/assessment-tool.png)

9. 在“选择迁移工具”中，选择“暂时跳过添加迁移工具” > “下一步”。  
10. 在“检查 + 添加工具”中检查设置，然后单击“添加工具”。 
11. 等待几分钟，让 Azure Migrate 项目部署完成。 随后将转到项目页。 如果未看到该项目，可以从 Azure Migrate 仪表板中的“服务器”访问它。

## <a name="set-up-the-azure-migrate-appliance"></a>设置 Azure Migrate 设备


Azure Migrate:服务器评估使用轻型 Azure Migrate 设备。 此设备执行 VM 发现并将 VM 元数据和性能数据发送到 Azure Migrate。 可通过多种方式设置此设备。

- 使用已下载的 Hyper-V VHD 在 Hyper-V VM 上进行设置。 这是本教程中使用的方法。
- 使用 PowerShell 安装程序脚本在 Hyper-V VM 或物理计算机上进行设置。 如果无法使用 VHD 设置 VM，或者你使用的是 Azure 政府，则应使用[此方法](deploy-appliance-script.md)。

创建设备后，请检查它是否可以连接到 Azure Migrate:服务器评估，首次配置该设备，并将其注册到 Azure Migrate 项目。

### <a name="generate-the-azure-migrate-project-key"></a>生成 Azure Migrate 项目密钥

1. 在“迁移目标” > “服务器” > “Azure Migrate:  服务器评估”中，选择“发现”。
2. 在“发现计算机” > “计算机是否已虚拟化?”中，选择“是，使用 Hyper-V”。  
3. 在“1: 生成 Azure Migrate 项目密钥”中，提供将为发现 Hyper-V VM 而设置的 Azure Migrate 设备的名称。该名称应为不超过 14 个字符的字母数字字符。
1. 单击“生成密钥”，开始创建所需的 Azure 资源。 在创建资源期间，请不要关闭“发现计算机”页。
1. 成功创建 Azure 资源后，会生成一个 Azure Migrate 项目密钥。
1. 复制密钥，因为配置设备时需要输入该密钥才能完成设备注册。

### <a name="download-the-vhd"></a>下载 VHD

在“2: 下载 Azure Migrate 设备”中，选择 .VHD 文件，然后单击“下载”。 

   ![对于发现计算机的选择](./media/tutorial-assess-hyper-v/servers-discover.png)


   ![对于生成密钥的选择](./media/tutorial-assess-hyper-v/generate-key-hyperv.png)


### <a name="verify-security"></a>验证安全性

在部署压缩文件之前检查其安全性。

1. 在下载文件的计算机上，打开管理员命令窗口。

2. 运行以下 PowerShell 命令以生成 ZIP 文件的哈希
    - ```C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm]```
    - 用法示例：```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v1.19.06.27.zip -Algorithm SHA256```

3.  验证最新的设备版本和哈希值：

    - 对于 Azure 公有云：

        **方案** | **下载** | **SHA256**
        --- | --- | ---
        Hyper-V (10.4 GB) | [最新版本](https://go.microsoft.com/fwlink/?linkid=2140422) |  79c151588de049cc102f61b910d6136e02324dc8d8a14f47772da351b46d9127

    - 对于 Azure 政府：

        **方案*** | **下载** | **SHA256**
        --- | --- | ---
        Hyper-V (85 MB) | [最新版本](https://go.microsoft.com/fwlink/?linkid=2140424) |  8025f315e41c01ebdb4ffb1de87982ae6cc4ea7c4cce612612c7e90a44e79b44


### <a name="create-the-appliance-vm"></a>创建设备 VM

导入下载的文件，然后创建 VM。

1. 将压缩的 VHD 文件下载到将放置设备 VM 的 Hyper-V 主机后，提取压缩文件。
    - 在提取的位置，文件将解压缩到名为 **AzureMigrateAppliance_VersionNumber** 的文件夹中。
    - 此文件夹包含一个子文件夹，也称为 **AzureMigrateAppliance_VersionNumber**。
    - 此子文件夹包含另外三个子文件夹 - **快照**、**虚拟硬盘**和**虚拟机**。

2. 打开 Hyper-V 管理器。 在“操作”中，单击“导入虚拟机”。 

    ![部署 VHD](./media/tutorial-assess-hyper-v/deploy-vhd.png)

2. 在“导入虚拟机向导”>“开始之前”中，单击“下一步”。 
3. 在“查找文件夹”中，选择“虚拟机”文件夹。 然后单击“下一步”  。
1. 在“选择虚拟机”中，单击“下一步”。 
2. 在“选择导入类型”中，单击“复制虚拟机(创建新的唯一 ID)”。  然后单击“下一步”  。
3. 在“选择目标”中保留默认设置。 单击“下一步”。
4. 在“存储文件夹”中保留默认设置。 单击“下一步”。
5. 在“选择网络”中，指定 VM 要使用的虚拟交换机。 该交换机需要与 Internet 建立连接才能向 Azure 发送数据。 [了解](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-switch-for-hyper-v-virtual-machines)如何创建虚拟交换机。
6. 在“摘要”中检查设置。 然后单击“完成”。
7. 在“Hyper-V 管理器”>“虚拟机”中启动 VM。


## <a name="verify-appliance-access-to-azure"></a>验证设备的 Azure 访问权限

确保设备 VM 可以连接到[公有云](migrate-appliance.md#public-cloud-urls)和[政府云](migrate-appliance.md#government-cloud-urls)的 Azure URL。

### <a name="configure-the-appliance"></a>配置设备

首次设置设备。

> [!NOTE]
> 如果使用 [PowerShell 脚本](deploy-appliance-script.md)而不是下载的 VHD 设置设备，则此过程中的前两个步骤不相关。

1. 在“Hyper-V 管理器”>“虚拟机”中，右键单击该 VM 并选择“连接”。 
2. 提供设备的语言、时区和密码。
3. 在可连接到该 VM 的任一计算机上打开浏览器，然后打开设备 Web 应用的 URL：**https://*设备名称或 IP 地址*:** 44368。

   或者，可以在设备桌面上单击应用快捷方式打开该应用。
1. 接受许可条款，并阅读第三方信息。
1. 在 Web 应用 >“设置必备组件”中执行以下操作：
    - **连接**：应用将检查 VM 是否可访问 Internet。 如果 VM 使用代理：
      - 单击“设置代理”，并指定代理地址（格式为 http://ProxyIPAddress 或 http://ProxyFQDN) ）和侦听端口。
      - 如果代理需要身份验证，请指定凭据。
      - 仅支持 HTTP 代理。
      - 如果已添加代理详细信息或已禁用代理和/或身份验证，请单击“保存”，再次触发连接性检查。
    - **时间同步**：将验证时间。 设备上的时间应与 Internet 时间同步，这样才能正常发现 VM。
    - **安装更新**：Azure Migrate 服务器评估检查设备是否安装了最新更新。检查完成后，可以单击“查看设备服务”，查看设备上运行的组件的状态和版本。

### <a name="register-the-appliance-with-azure-migrate"></a>将设备注册到 Azure Migrate

1. 粘贴从门户复制的 Azure Migrate 项目密钥。 如果没有密钥，请转到“服务器评估”>“发现”>“管理现有设备”，选择在生成密钥时提供的设备名称，然后复制相应的密钥。
1. 单击“登录”。 这将在新的浏览器选项卡中打开 Azure 登录提示。如果未显示该按钮，请确保已在浏览器中禁用弹出窗口阻止程序。
1. 在新选项卡上，使用 Azure 用户名和密码登录。
   
   不支持使用 PIN 登录。
3. 成功登录后，返回到 Web 应用。 
4. 如果用于登录的 Azure 用户帐户对在密钥生成过程中创建的 Azure 资源具有恰当的[权限](tutorial-prepare-hyper-v.md#prepare-azure)，会启动设备注册。
1. 成功注册设备后，可以通过单击“查看详细信息”来查看注册详细信息。



### <a name="delegate-credentials-for-smb-vhds"></a>为 SMB VHD 委托凭据

如果在 SMB 上运行 VHD，必须启用从设备到 Hyper-V 主机的凭据委托。 为此，请允许每个主机充当设备的代理。 如果你按照教程顺序操作，则在上一教程准备 Hyper-V 进行评估和迁移时执行了此操作。 应该[手动](tutorial-prepare-hyper-v.md#enable-credssp-to-delegate-credentials)或通过[运行执行此操作的脚本](tutorial-prepare-hyper-v.md#run-the-script)为主机设置 CredSSP。

在设备上启用，如下所示：

#### <a name="option-1"></a>选项 1

在设备 VM 上运行此命令。 HyperVHost1/HyperVHost2 是示例主机名。

```
Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com, HyperVHost2.contoso.com, HyperVHost1, HyperVHost2 -Force
```

示例： ` Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force `

#### <a name="option-2"></a>方法 2

或者，在设备上的本地组策略编辑器中执行此操作：

1. 在“本地计算机策略” > “计算机配置”中，单击“管理模板” > “系统” > “凭据委托”。    
2. 双击“允许委托新凭据”，并选择“已启用”。 
3. 在“选项”中单击“显示”，将要发现的每台 Hyper-V 主机添加到列表中，并使用 wsman/ 作为前缀。  
4. 在“凭据分配”中，双击“允许分配新的凭据用于仅 NTLM 服务器身份验证”。  再次将要发现的每台 Hyper-V 主机添加到列表中，并使用 **wsman/** 作为前缀。

## <a name="start-continuous-discovery"></a>启动持续发现

从设备连接到 Hyper-V 主机或群集，并启动 VM 发现。

1. 在**步骤 1：提供 Hyper-V 主机凭据**中，单击“添加凭据”以指定凭据的易记名称，为设备将用于发现 VM 的 Hyper-V 主机/群集添加“用户名”和“密码”。 单击“保存” 。
1. 如果要一次添加多个凭据，请单击“添加更多”，以保存和添加更多凭据。 Hyper-V VM 发现支持多个凭据。
1. 在**步骤 2：提供 Hyper-V 主机/群集详细信息**中，单击“添加发现源”，以指定 Hyper-V 主机/群集 IP 地址/FQDN 以及用于连接到主机/群集的凭据的易记名称。
1. 可以一次“添加单个项目”，也可以一次“添加多个项目” 。 还有一个选项是通过“导入 CSV”提供 Hyper-V 主机/群集详细信息。

    ![对于添加发现源的选择](./media/tutorial-assess-hyper-v/add-discovery-source-hyperv.png)

    - 如果选择“添加单个项目”，则需要指定凭据的易记名称和 Hyper-V 主机/群集 IP 地址/FQDN，然后单击“保存”。
    - 如果选择“添加多个项目”（默认情况下处于选中状态），则可以通过在文本框中指定 Hyper-V 主机/群集 IP 地址/FQDN 和凭据的易记名称来一次添加多个记录。验证添加的记录并单击“保存”。
    - 如果选择“导入 CSV”，可以下载 CSV 模板文件，并使用 Hyper-V 主机/群集 IP 地址/FQDN 和凭据的易记名称填充文件。 然后，将该文件导入设备，验证文件中的记录，然后单击“保存” 。

1. 单击“保存”后，设备将尝试验证与已添加 Hyper-V 主机/群集的连接，并在表中显示每个主机/群集的“验证状态”。
    - 对于验证成功的主机/群集，可以通过单击其 IP 地址/FQDN 来查看更多详细信息。
    - 如果主机验证失败，请通过单击表中“状态”列的“验证失败”来查看错误。 解决此问题，然后再次验证。
    - 若要删除主机或群集，请单击“删除”。
    - 无法删除群集中的特定主机。 只能删除整个群集。
    - 即使群集中的特定主机出现问题，你也可以添加群集。
1. 在开始发现之前，可以随时重新验证与主机/群集之间的连接。
1. 单击“开始发现”，从已成功验证的主机/群集开始发现 VM。 成功启动发现后，你可以针对表中的每个主机/群集检查发现状态。

随即会启动发现。 在 Azure 门户中，每台主机大约需要 2 分钟才能显示发现的服务器的元数据。

### <a name="verify-vms-in-the-portal"></a>在门户中验证 VM

发现完成后，可以验证 VM 是否出现在门户中。

1. 打开 Azure Migrate 仪表板。
2. 在“Azure Migrate - 服务器” > “Azure Migrate: 服务器评估”页中，单击显示了**已发现服务器**计数的图标。

## <a name="set-up-an-assessment"></a>设置评估

使用 Azure Migrate 服务器评估可以运行两种类型的评估。

**评估** | **详细信息** | **数据**
--- | --- | ---
**基于性能** | 基于收集的性能数据的评估 | **建议的 VM 大小**：基于 CPU 和内存利用率数据。<br/><br/> **建议的磁盘类型(标准或高级托管磁盘)** ：基于本地磁盘的 IOPS 和吞吐量。
**本地** | 基于本地大小的评估。 | **建议的 VM 大小**：基于本地 VM 大小<br/><br> **建议的磁盘类型**：基于为评估选择的存储类型设置。



### <a name="run-an-assessment"></a>运行评估

按如下述运行评估：

1. 查看有关创建评估的[最佳做法](best-practices-assessment.md)。
2. 在“服务器” > “Azure Migrate: 服务器评估”中，单击“评估”。

    ![评估](./media/tutorial-assess-hyper-v/assess.png)

3. 在“评估服务器”中，指定评估的名称。
4. 单击“全部查看”查看评估属性。

    ![评估属性](./media/tutorial-assess-hyper-v/assessment-properties.png)

3. 在“选择或创建组”中，选择“新建”并指定组名称。 组将要评估的一个或多个 VM 集合到一起。
4. 在“将计算机添加到组”中，选择要添加到该组的 VM。
5. 单击“创建评估”以创建该组，并运行评估。

    ![创建评估](./media/tutorial-assess-hyper-v/assessment-create.png)

6. 创建评估后，在“服务器” > “Azure Migrate: 服务器评估”中查看该评估。
7. 单击“导出评估”，将评估下载为 Excel 文件。


## <a name="review-an-assessment"></a>查看评估

评估描述：

- **Azure 迁移就绪性**：VM 是否适合迁移到 Azure。
- **每月成本估算**：在 Azure 中运行 VM 的估算每月计算和存储成本。
- **每月存储成本估算**：迁移后的磁盘存储估算成本。


### <a name="view-an-assessment"></a>查看评估

1. 在“迁移目标” >  “服务器” > “Azure Migrate:  服务器评估”中，单击“评估”。
2. 在“评估”中，单击某项评估将其打开。

    ![评估摘要](./media/tutorial-assess-hyper-v/assessment-summary.png)


### <a name="review-azure-readiness"></a>查看 Azure 迁移就绪性

1. 在“Azure 迁移就绪性”中，验证 VM 是否已准备好迁移到 Azure。
2. 查看 VM 状态：
    - **已做好 Azure 迁移准备**：对于评估中的 VM，Azure Migrate 将建议 VM 大小并显示估算成本。
    - **准备就绪但存在以下状况**：显示问题和建议的补救措施。
    - **尚未做好 Azure 迁移准备**：显示问题和建议的补救措施。
    - **就绪性未知**：当数据可用性问题导致 Azure Migrate 无法评估就绪性时使用。

2. 单击某种“Azure 迁移就绪性”状态。 可以查看 VM 就绪性详细信息，并深入查看 VM 详细信息，包括计算、存储和网络设置。

### <a name="review-cost-details"></a>查看成本详细信息

此视图显示在 Azure 中运行 VM 的估算计算和存储成本。

1. 查看每月计算和存储成本。 将聚合评估的组中所有 VM 的成本。

    - 估算成本是根据计算机的大小建议及其磁盘和属性给出的。
    - 将显示估算的每月计算和存储成本。
    - 估算成本适用于作为 IaaS VM 运行的本地 VM。 Azure Migrate 服务器评估不考虑 PaaS 或 SaaS 成本。

2. 可以查看每月存储估算成本。 此视图显示评估的组的聚合存储成本，按不同类型的存储磁盘划分。
3. 可以深入查看特定 VM 的详细信息。


### <a name="review-confidence-rating"></a>查看置信度分级

运行基于性能的评估时，会将一个置信度分级分配到评估。

![置信度分级](./media/tutorial-assess-hyper-v/confidence-rating.png)

- 将授予从 1 星（最低）到 5 星（最高）的分级。
- 置信度分级可帮助你判断评估所提供的大小建议的可靠性。
- 置信度分级基于计算评估所需的数据点的可用性。

评估的置信度分级如下。

**数据点可用性** | **置信度分级**
--- | ---
0%-20% | 1 星
21%-40% | 2 星
41%-60% | 3 星
61%-80% | 4 星
81%-100% | 5 星

[详细了解](best-practices-assessment.md#best-practices-for-confidence-ratings)有关置信度分级的最佳做法。





## <a name="next-steps"></a>后续步骤

本教程介绍以下操作：

> [!div class="checklist"]
> * 已设置一个 Azure Migrate 设备
> * 已创建并查看评估

请继续学习本教程系列的第三篇教程，了解如何使用 Azure Migrate 服务器迁移将 Hyper-V VM 迁移到 Azure。

> [!div class="nextstepaction"]
> [迁移 Hyper-V VM](./tutorial-migrate-hyper-v.md)
