---
title: 使用 Azure Migrate 服务器评估来评估要迁移到 Azure 的物理服务器
description: 介绍如何使用 Azure Migrate 服务器评估来评估要迁移到 Azure 的本地物理服务器。
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: raynew
ms.openlocfilehash: 9339a03fcb3f67402c0aab030cb69a45e1b42b45
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123509"
---
# <a name="assess-physical-servers-with-azure-migrate-server-assessment"></a>使用 Azure Migrate：服务器评估服务器评估”评估 VMware VM

> [!NOTE]
> 如果在 Azure Migrate 门户中看不到此功能，请等待一段时间。 大约在一周后，它就会出现。
 

本文介绍如何使用“Azure Migrate:服务器评估”工具评估本地 VMware VM。

[Azure Migrate](migrate-services-overview.md) 在一个中心位置提供多种工具，帮助你发现、评估应用、基础结构和工作负荷并将其迁移到 Microsoft Azure。 该中心包含 Azure Migrate 工具，以及第三方独立软件供应商 (ISV) 的产品/服务。

本教程是演示如何评估物理服务器以及将其迁移到 Azure 的教程系列中的第二篇文章。 本教程介绍如何执行下列操作：
> [!div class="checklist"]
> * 设置一个 Azure Migrate 项目。
> * 设置本地运行的 Azure Migrate 设备，用于评估物理服务器。
> * 开始连续发现本地物理服务器。 设备会将已发现的服务器的配置和性能数据发送到 Azure。
> * 对发现的服务器进行分组，并评估服务器组。
> * 查看评估。

> [!NOTE]
> 教程中演示了方案的最简单部署路径，使你能够快速设置概念证明。 教程尽可能使用默认选项，不会演示所有可能的设置和路径。 有关详细说明，请查看操作指南文章。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。


## <a name="prerequisites"></a>先决条件

- [完成](tutorial-prepare-physical.md)本教程系列中的第一篇教程。 否则，本教程中的说明不适用。
- 下面是在第一篇教程中应已完成的操作：
    - 为 Azure Migrate [设置 Azure 权限](tutorial-prepare-physical.md#prepare-azure)。
    - [准备物理服务器](tutorial-prepare-physical.md#prepare-for-physical-server-assessment)以进行评估。 应验证设备要求。 此外，应该设置一个帐户用于发现物理服务器。 所需的端口应该可用；你应该知道用于访问 Azure 的 URL。


## <a name="set-up-an-azure-migrate-project"></a>设置 Azure Migrate 项目

按如下所述设置新的 Azure Migrate 项目。

1. 在 Azure 门户中选择“所有服务”，然后搜索 **Azure Migrate**。 
2. 在“服务”下选择“Azure Migrate”。  
3. 在“概述”中的“发现、评估和迁移服务器”下，单击“评估和迁移服务器”。   

    ![发现和评估服务器](./media/tutorial-assess-physical/assess-migrate.png)

4. 在“开始”中，单击“添加工具”。  
5. 在“迁移项目”中选择你的 Azure 订阅，并创建一个资源组（如果没有）。      
6. 在“项目详细信息”中，指定项目名称以及要在其中创建项目的地理位置。  支持“亚洲”、“欧洲”、“英国”和“美国”。

    - 项目地理位置仅用于存储从本地服务器中收集的元数据。
    - 运行迁移时，可以选择任一目标区域。

    ![创建 Azure Migrate 项目](./media/tutorial-assess-physical/migrate-project.png)


7. 单击“下一步”。 
8. 在“选择评估工具”中，选择“Azure Migrate:   服务器评估” > “下一步”。 

    ![创建 Azure Migrate 项目](./media/tutorial-assess-physical/assessment-tool.png)

9. 在“选择迁移工具”中，选择“暂时跳过添加迁移工具” > “下一步”。   
10. 在“检查 + 添加工具”中检查设置，然后单击“添加工具”。  
11. 等待几分钟，让 Azure Migrate 项目部署完成。 随后将转到项目页。 如果未看到该项目，可以从 Azure Migrate 仪表板中的“服务器”访问它。 


## <a name="set-up-the-appliance"></a>设置设备

“Azure Migrate:服务器评估”运行一个轻型设备。

- 此设备执行物理服务器发现，并将服务器元数据和性能数据发送到 Azure Migrate 服务器评估。
- 若要设置该设备，请执行以下操作：
    - 从 Azure 门户下载带有 Azure Migrate 安装程序脚本的压缩文件。
    - 从压缩文件中提取内容。 使用管理权限启动 PowerShell 控制台。
    - 执行 PowerShell 脚本以启动设备 Web 应用程序。
    - 完成设备的首次配置，并将其注册到 Azure Migrate 项目。
- 可为单个 Azure Migrate 项目设置多个设备。 在所有设备中，你可以发现任意数量的物理服务器。 在每个设备上，最多可以发现 250 个服务器。

### <a name="download-the-installer-script"></a>下载安装程序脚本

下载设备的压缩文件。

1. 在“迁移目标” > “服务器” > “Azure Migrate:    服务器评估”中，单击“发现”。 
2. 在“发现计算机” > “计算机是否已虚拟化?”中，单击“未虚拟化/其他”。   
3. 单击“下载”以下载压缩文件  。

    ![下载安装程序](./media/tutorial-assess-physical/download-appliance.png)


### <a name="verify-security"></a>验证安全性

在部署压缩文件之前检查其安全性。

1. 在下载文件的计算机上，打开管理员命令窗口。
2. 运行以下命令以生成 zip 文件的哈希
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 用法示例：```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller\AzureMigrateInstaller.ps1 SHA256```

3.  对于设备版本 1.19.05.10，生成的哈希应与这些设置匹配。

  **算法** | **哈希值**
  --- | ---
  SHA256 | 598d2e286f9c972bb7f7382885e79e768eddedfe8a3d3460d6b8a775af7d7f79

### <a name="run-the-azure-migrate-installer-script"></a>运行 Azure Migrate 安装程序脚本

此安装程序脚本执行以下操作：

- 安装用于物理服务器发现和评估的代理和 Web 应用程序。
- 安装 Windows 角色，包括 Windows 激活服务、IIS 和 PowerShell ISE。
- 下载并安装 IIS 可重写模块。 [了解详细信息](https://www.microsoft.com/download/details.aspx?id=7435)。
- 更新 Azure Migrate 的注册表项 (HKLM) 和永久性设置详细信息。
- 在路径下创建以下文件：
    -  配置文件：%ProgramData%\Microsoft Azure\Config
    -  日志文件：%ProgramData%\Microsoft Azure\Logs

按如下所示运行脚本：

1. 将压缩文件解压缩到托管设备的服务器上的某个文件夹中。
2. 使用管理（提升）权限在上述服务器上启动 PowerShell。
3. 将 PowerShell 目录更改为从下载的压缩文件中提取内容的文件夹。
4. 通过运行以下命令，运行名为“AzureMigrateInstaller.ps1”的脚本  ：
    ```
    PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1
    ```
脚本将在成功完成时启动设备 Web 应用程序。 

如果出现任何问题，可以访问位于 C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log 的脚本日志以进行故障排除。

> [!NOTE]
> 请不要在现有的 Azure Migrate 设备上执行 Azure Migrate 安装程序脚本。

### <a name="verify-appliance-access-to-azure"></a>验证设备的 Azure 访问权限

确保设备可以连接到 [Azure URL](migrate-support-matrix-physical.md#assessment-appliance-url-access)。


### <a name="configure-the-appliance"></a>配置设备

首次设置设备。

1. 在可连接到该设备的任一计算机上打开浏览器，然后打开设备 Web 应用的 URL： https://设备名称或 IP 地址 ** :44368**。

   或者，可以在桌面上单击应用快捷方式打开该应用。
2. 在 Web 应用 >“设置必备组件”中执行以下操作： 
    - **许可证**：接受许可条款，并阅读第三方信息。
    - **连接**：应用将检查服务器是否可访问 Internet。 如果服务器使用代理：
        - 单击“代理设置”，并以 http://ProxyIPAddress 或 http://ProxyFQDN 格式指定代理地址和侦听端口。 
        - 如果代理需要身份验证，请指定凭据。
        - 仅支持 HTTP 代理。
    - **时间同步**：将验证时间。 设备上的时间应与 Internet 时间同步，这样才能正常发现服务器。
    - **安装更新**：Azure Migrate 服务器评估将检查设备上是否安装了最新更新。

### <a name="register-the-appliance-with-azure-migrate"></a>将设备注册到 Azure Migrate

1. 单击“登录”。  如果未显示该按钮，请确保已在浏览器中禁用弹出窗口阻止程序。
2. 在新的标签页中，使用 Azure 凭据登录。 
    - 使用用户名和密码登录。
    - 不支持使用 PIN 登录。
3. 成功登录后，返回到 Web 应用。
4. 选择在其中创建了 Azure Migrate 项目的订阅。 然后选择该项目。
5. 指定设备的名称。 该名称应是字母数字，长度为 14 个或更少的字符。
6. 单击“注册”  。


## <a name="start-continuous-discovery"></a>启动持续发现

现在，从设备连接到要发现的物理服务器，并启动发现。

1. 单击“添加凭据”以指定设备用于发现服务器的帐户凭据  。  
2. 指定“操作系统”、凭据的友好名称、“用户名”和“密码”，然后单击“添加”     。
你可以为 Windows 和 Linux 服务器分别添加一组凭据。
4. 单击“添加服务器”，然后指定服务器详细信息 - FQDN/IP 地址和凭据的友好名称（每行一个条目）以连接到服务器  。
3. 单击“验证”。  验证后，将显示可发现的服务器列表。
    - 如果服务器验证失败，请将鼠标悬停在“状态”列中的图标上以查看错误。  解决问题并再次验证。
    - 若要删除服务器，请选择 >“删除”  。
4. 验证之后，单击“保存并启动发现”以启动发现过程。 

随即会启动发现。 每台服务器大约需要 1.5 分钟，才能将已发现的服务器的元数据显示在 Azure 门户中。 

### <a name="verify-servers-in-the-portal"></a>验证门户中的服务器

发现完成后，可以验证服务器是否出现在 Azure 门户中。

1. 打开 Azure Migrate 仪表板。
2. 在“Azure Migrate - 服务器” > “Azure Migrate:   服务器评估”页中，单击显示了**已发现服务器**计数的图标。

## <a name="set-up-an-assessment"></a>设置评估

使用“Azure Migrate: 服务器评估”可以运行两种类型的评估。

**评估** | **详细信息** | **数据**
--- | --- | ---
**基于性能** | 基于收集的性能数据的评估 | **建议的 VM 大小**：基于 CPU 和内存利用率数据。<br/><br/> **建议的磁盘类型(标准或高级托管磁盘)** ：基于本地磁盘的 IOPS 和吞吐量。
**本地** | 基于本地大小的评估。 | **建议的 VM 大小**：基于本地服务器大小<br/><br> **建议的磁盘类型**：基于为评估选择的存储类型设置。


### <a name="run-an-assessment"></a>运行评估

按如下述运行评估：

1. 查看有关创建评估的[最佳做法](best-practices-assessment.md)。
2. 在“服务器”选项卡上的“Azure Migrate:   服务器评估”磁贴中，单击“评估”。 

    ![评估](./media/tutorial-assess-physical/assess.png)

2. 在“评估服务器”中，指定评估的名称。 
3. 单击“全部查看”查看评估属性  。

    ![评估属性](./media/tutorial-assess-physical/view-all.png)

3. 在“选择或创建组”中，选择“新建”并指定组名称。   组将要评估的一个或多个服务器集合到一起。
4. 在“将计算机添加到组”中，选择要添加到该组的服务器  。
5. 单击“创建评估”以创建该组，并运行评估。 

    ![创建评估](./media/tutorial-assess-physical/assessment-create.png)

6. 创建评估后，在“服务器” > “Azure Migrate:   服务器评估” > “评估”中查看它。 
7. 单击“导出评估”，将评估下载为 Excel 文件。 



## <a name="review-an-assessment"></a>查看评估

评估描述：

- **Azure 迁移就绪性**：服务器是否适合迁移到 Azure。
- **每月成本估算**：在 Azure 中运行服务器的估算每月计算和存储成本。
- **每月存储成本估算**：迁移后的磁盘存储估算成本。

### <a name="view-an-assessment"></a>查看评估

1. 在“迁移目标” >  “服务器”中，单击“Azure Migrate:     服务器评估”中的“评估”。
2. 在“评估”中，单击某项评估将其打开。 

    ![评估摘要](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>查看 Azure 迁移就绪性

1. 在“Azure 迁移就绪性”中，验证服务器是否已准备好迁移到 Azure  。
2. 查看状态：
    - **已做好 Azure 迁移准备**：对于评估中的 VM，Azure Migrate 将建议 VM 大小并显示估算成本。
    - **准备就绪但存在以下状况**：显示问题和建议的补救措施。
    - **尚未做好 Azure 迁移准备**：显示问题和建议的补救措施。
    - **就绪性未知**：当数据可用性问题导致 Azure Migrate 无法评估就绪性时使用。

2. 单击某种“Azure 迁移就绪性”状态。  可以查看服务器就绪性详细信息，并深入查看服务器详细信息，包括计算、存储和网络设置。



### <a name="review-cost-details"></a>查看成本详细信息

此视图显示在 Azure 中运行 VM 的估算计算和存储成本。

1. 查看每月计算和存储成本。 将聚合评估的组中所有服务器的成本。

    - 估算成本是根据计算机的大小建议及其磁盘和属性给出的。
    - 将显示估算的每月计算和存储成本。
    - 估算成本适用于作为 IaaS VM 运行的本地服务器。 Azure Migrate 服务器评估不考虑 PaaS 或 SaaS 成本。

2. 可以查看每月存储估算成本。 此视图显示评估的组的聚合存储成本，按不同类型的存储磁盘划分。
3. 可以深入查看特定服务器的详细信息。


### <a name="review-confidence-rating"></a>查看置信度分级

运行基于性能的评估时，会将一个置信度分级分配到评估。

![置信度分级](./media/tutorial-assess-physical/confidence-rating.png)

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

请继续学习本教程系列的第三篇教程，了解如何使用 Azure Migrate：服务器迁移服务器迁移。

> [!div class="nextstepaction"]
> [迁移物理服务器](./tutorial-migrate-physical-virtual-machines.md)
