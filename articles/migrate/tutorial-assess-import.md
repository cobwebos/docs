---
title: 使用导入的服务器数据通过 Azure Migrate 服务器评估来评估服务器
description: 描述如何使用导入的数据通过 Azure Migrate 服务器评估来评估要迁移到 Azure 的本地服务器。
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: raynew
ms.openlocfilehash: 7bf47731f2a3621e7bbdc1b104d94e97f2d03099
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158646"
---
# <a name="assess-servers-using-imported-data"></a>使用导入的数据评估服务器

本文介绍如何通过使用 CSV 导入服务器元数据，利用 [Azure Migrate：服务器评估](migrate-services-overview.md#azure-migrate-server-assessment-tool)来评估本地服务器。 使用此评估方法，无需设置 Azure Migrate 设备即可创建评估。 这在下列情况下非常有用：

- 在部署设备之前，你需要创建快速初始评估。
- 你无法在组织中部署 Azure Migrate 设备。
- 你无法共享允许访问本地服务器的凭据。
- 安全约束阻止你收集设备所收集的数据并将其发送到 Azure。 使用导入的文件，你可以控制共享的数据，并且很多数据（例如提供 IP 地址）是可选的。


## <a name="before-you-start"></a>开始之前

请注意：

- 最多可以在一个 CSV 文件中添加 20000 个服务器。
- 使用 CSV，最多可以在 Azure Migrate 项目中添加 20000 个服务器。
- 可以使用 CSV 多次将服务器信息上载到 Azure Migrate 服务器评估。
- 尽管在评估本地环境以进行迁移时收集应用程序信息非常有用，但 Azure Migrate 服务器评估目前不会执行应用程序级评估，并且不会在创建评估时考虑应用程序。

本教程介绍如何执行下列操作：
> [!div class="checklist"]
> * 设置一个 Azure Migrate 项目。
> * 在 CSV 文件中填写服务器信息。
> * 导入文件以将服务器信息添加到 Azure Migrate 服务器评估。
> * 创建和查看评估。

> [!NOTE]
> 教程中演示了方案的最简单部署路径，使你能够快速设置概念证明。 教程尽可能使用默认选项，不会演示所有可能的设置和路径。 有关详细说明，请查看操作指南文章。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。


## <a name="set-azure-permissions-for-azure-migrate"></a>为 Azure Migrate 设置 Azure 权限

你的 Azure 帐户需要有权创建 Azure Migrate 项目。

1. 在 Azure 门户中打开订阅，然后选择“访问控制(IAM)”。 
2. 在“检查访问权限”中找到相关的帐户，然后单击它以查看权限。 
3. 你应该拥有“参与者”或“所有者”权限。  
    - 如果你刚刚创建了免费的 Azure 帐户，那么你就是订阅的所有者。
    - 如果你不是订阅所有者，请让所有者分配该角色。


## <a name="set-up-an-azure-migrate-project"></a>设置 Azure Migrate 项目

按如下所述设置新的 Azure Migrate 项目。

1. 在 Azure 门户中选择“所有服务”，然后搜索 **Azure Migrate**。 
2. 在“服务”下选择“Azure Migrate”。  
3. 在“概述”中的“发现、评估和迁移服务器”下，单击“评估和迁移服务器”。   

    ![发现和评估服务器](./media/tutorial-assess-import/assess-migrate.png)

4. 在“开始”中，单击“添加工具”。  
5. 在“迁移项目”中选择你的 Azure 订阅，并创建一个资源组（如果没有）。      
6. 在“项目详细信息”中，指定项目名称以及要在其中创建项目的地理位置。 

    - [查看](migrate-support-matrix.md#supported-geographies)支持的地理位置。 项目地理位置仅用于存储从本地 VM 中收集的元数据。
    - 运行迁移时，可以选择任一目标区域。

    ![创建 Azure Migrate 项目](./media/tutorial-assess-import/migrate-project.png)


7. 单击“下一步”。 
8. 在“选择评估工具”中，选择“Azure Migrate:   服务器评估” > “下一步”。 

    ![创建 Azure Migrate 项目](./media/tutorial-assess-import/assessment-tool.png)

9. 在“选择迁移工具”中，选择“暂时跳过添加迁移工具” > “下一步”。   
10. 在“检查 + 添加工具”中检查设置，然后单击“添加工具”。  
11. 等待几分钟，让 Azure Migrate 项目部署完成。 随后将转到项目页。 如果未看到该项目，可以从 Azure Migrate 仪表板中的“服务器”访问它。 


## <a name="prepare-the-csv"></a>准备 CSV

下载 CSV 模板，并向其中添加服务器信息。


### <a name="download-the-template"></a>下载模板

1. 在“迁移目标” > “服务器” > “Azure Migrate:    服务器评估”中，单击“发现”。 
2. 在“发现计算机”  上，选择“使用 .CSV 导入”  。
3. 单击“下载”以下载 .CSV 模板  。 或者，也可以[直接下载它](https://go.microsoft.com/fwlink/?linkid=2108404)。

    ![下载 .CSV 模板](./media/tutorial-assess-import/download-template.png)


### <a name="add-server-information"></a>添加服务器信息

收集服务器数据，并将其添加到 CSV 文件中。

- 若要收集数据，可以从用于本地服务器管理的工具（如 VMware vSphere 或配置管理数据库 (CMDB)）导出数据。
- 若要查看示例数据，请下载我们的[示例文件](https://go.microsoft.com/fwlink/?linkid=2108405)。


下表总结了要填写的文件字段。

**字段名称** | **必需** | **详细信息**
--- | --- | ---
**服务器名称** | 是 | 建议指定 FQDN。
IP 地址  | 否 | 服务器地址。
**内核数** | 是 | 分配给服务器的处理器内核数。
**内存** | 是 | 分配给服务器的总 RAM (MB)。
**OS 名称** | 是 | 服务器操作系统。
**OS 版本** | 否 | 服务器操作系统版本。
**磁盘数目** | 否 | 如果提供了单独的磁盘详细信息，则不需要。
**磁盘 1 大小**  | 否 | 磁盘最大大小 (GB)<br/> 可以通过在模板中[添加列](#add-multiple-disks)来添加更多磁盘的详细信息。 最多可以添加八个磁盘。
**磁盘 1 读取操作数** | 否 | 每秒的磁盘读取操作次数。
**磁盘 1 写入操作数** | 否 | 每秒的磁盘写入操作次数。
**磁盘 1 读取吞吐量** | 否 | 每秒从磁盘读取的数据，以 MB/秒为单位。
**磁盘 1 写入吞吐量** | 否 | 每秒写入磁盘的数据，以 MB/秒为单位。
**CPU 使用率百分比** | 否 | CPU 使用率百分比。
**内存使用率百分比** | 否 | RAM 使用率百分比。
**磁盘读取操作总数** | 否 | 每秒的磁盘读取操作次数。
**磁盘写入操作总数** | 否 | 每秒的磁盘写入操作次数。
**磁盘读取吞吐量总数** | 否 | 从磁盘读取的数据，以 MB/秒为单位。
**磁盘写入吞吐量总数** | 否 | 写入磁盘的数据，以 MB/秒为单位。
**网络流入吞吐量** | 否 | 服务器接收的数据，以 MB/秒为单位。
**网络流出吞吐量** | 否 | 服务器传输的数据，以 MB/秒为单位。
**固件类型** | 否 | 服务器固件。 值可以是“BIOS”或“UEFI”
**服务器类型** | 否 | 值可以是“物理”或“虚拟”。
**虚拟机监控程序** | 否 | 计算机正在其上运行的虚拟机监控程序。 <br/> 值可以是“VMware”、“Hyper-v”、“Xen”、“AWS”、“GCP”或“其他”。
**虚拟机监控程序版本号** | 否 | 虚拟机监控程序版本。
**虚拟机 ID** | 否 | VM 标识符。 这是 InstanceUUid（对于 VMware vCenter VM）或 Hyper-V VM ID（对于 Hyper-V）   。
**Virtual Machine Manager** | 否 | 这是 InstanceUUid（对于 VMWare vCenter）  。 Hyper-V 不需要此项。
**MAC 地址**| 否 | 服务器 MAC 地址。
**BIOS ID** | 否 | 服务器 BIOS ID。
**自定义服务器 ID**| 否 | 本地唯一服务器 ID。 <br/> 用于按本地 ID 跟踪导入的服务器。
**应用程序 1 名称** | 否 | 服务器上运行的工作负载的名称。<br/> 可以通过在模板中[添加列](#add-multiple-applications)来添加更多应用程序的详细信息。 最多可添加五个应用程序。
**应用程序 1 类型** | 否 | 服务器中运行的工作负载的类型
**应用程序 1 版本** | 否 | 服务器上运行的工作负载的版本。
**应用程序 1 许可证过期** | 否 | 工作负载的许可证过期（如果适用）。
**业务部门** | 否 | 服务器所属的业务部门。
**业务负责人** | 否 | 业务部门所有者。
**业务应用程序名称** | 否 | 应用所属的应用程序的名称。
**位置** | 否 | 服务器所在的数据中心。
**服务器解除授权日期** | 否 | 物理服务器或虚拟服务器的基础物理服务器的解除授权日期

### <a name="add-operating-systems"></a>添加操作系统

评估识别特定的操作系统名称。 指定的任何操作系统名称都必须与[支持的名称](#supported-operating-system-names)列表中的选项之一匹配。


### <a name="add-multiple-disks"></a>添加多个磁盘

模板为第一个磁盘提供默认字段。  可以为多达 8 个磁盘添加类似的列。

例如，若要指定第二个磁盘的所有字段，请添加列：

磁盘 2 大小  磁盘 2 读取操作数  磁盘 2 写入操作数  磁盘 2 读取吞吐量  磁盘 2 写入吞吐量

你可以选择仅为磁盘添加特定字段。


### <a name="add-multiple-applications"></a>添加多个应用程序

模板为单个应用程序提供字段。 你可以为多达五个应用程序添加类似的列。  

例如，若要指定第二个应用程序的所有字段，请添加列：

应用程序 2 名称  应用程序 2 类型  应用程序 2 版本  应用程序 2 许可证过期


你可以选择仅为应用程序添加特定字段。

> [!NOTE]
> 评估本地环境以进行迁移时，应用程序信息非常有用。 但是，Azure Migrate 服务器评估目前不会执行应用程序级评估，并且在创建评估时不会考虑应用程序。


## <a name="upload-the-server-information"></a>上载服务器信息

将信息添加到 CSV 模板之后，将服务器导入 Azure Migrate：两种类型的评估。

1. 在“Azure Migrate”>“发现计算机”  中，浏览到已填充的模板。
2. 单击“导入”  。
3. 将显示导入状态。
    - 如果状态中出现警告，则可以修复它们，也可以继续操作而不解决它们。
    - 按警告中建议的方式改进服务器信息可提高评估准确性。
    - 若要查看并修复出现的警告，请单击“下载警告详细信息 .CSV”  。 这会下载 CSV，并添加警告。 可以查看警告，并根据需要解决问题。
    如果状态中出现错误（导入状态为“失败”  ），则需要修复这些错误，然后才能继续导入。 为此，请下载此时已添加错误详细信息的 CSV。 查看并根据需要处理错误。 然后重新上载修改后的文件。
4. 如果导入状态为“已完成”  ，则服务器信息已导入。


> [!NOTE]
> 若要更新上载到 Azure Migrate 的服务器信息，请使用同一服务器名称  重新上载服务器的数据。 请注意，在导入模板之后，不能修改“服务器名称”  字段。 目前不支持删除服务器。

## <a name="updating-server-information"></a>更新服务器信息

可以通过使用相同的服务器名称再次上载服务器的数据来更新服务器信息  。 不能修改“服务器名称”  字段。

目前不支持删除服务器。

### <a name="verify-servers-in-the-portal"></a>验证门户中的服务器

发现完成后，可以验证服务器是否出现在 Azure 门户中。

1. 打开 Azure Migrate 仪表板。
2. 在“Azure Migrate - 服务器” > “Azure Migrate:   服务器评估”页中，单击显示了**已发现服务器**计数的图标。
3. 单击“基于导入”  选项卡。

## <a name="set-up-an-assessment"></a>设置评估

使用“Azure Migrate: 服务器评估”可以运行两种类型的评估。

**评估** | **详细信息** | **数据**
--- | --- | ---
**基于性能** | 基于指定的性能数据值的评估 | **建议的 VM 大小**：基于 CPU 和内存利用率数据。<br/><br/> **建议的磁盘类型(标准或高级托管磁盘)** ：基于本地磁盘的 IOPS 和吞吐量。
**本地** | 基于本地大小的评估。 | **建议的 VM 大小**：基于指定的服务器大小<br/><br> **建议的磁盘类型**：基于为评估选择的存储类型设置。


### <a name="run-an-assessment"></a>运行评估

按如下述运行评估：

1. 查看有关创建评估的[最佳做法](best-practices-assessment.md)。
2. 在“服务器”选项卡上的“Azure Migrate:   服务器评估”磁贴中，单击“评估”。 

    ![评估](./media/tutorial-assess-physical/assess.png)

2. 在“评估服务器”中，指定评估的名称。 
3. 在“发现源”  中，选择“通过导入添加到 Azure Migrate 的计算机” 
3. 单击“全部查看”查看评估属性  。

    ![评估属性](./media/tutorial-assess-physical/view-all.png)

3. 在“选择或创建组”中，选择“新建”并指定组名称。   组将要评估的一个或多个 VM 集合到一起。
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
3. 可以深入查看特定 VM 的详细信息。

> [!NOTE]
> 不会将置信度评级分配到使用 CSV 导入到 Azure Migrate 服务器评估中的服务器评估。


## <a name="supported-operating-system-names"></a>支持的操作系统名称

Name | Name
--- | ---
**A - H** |
Apple Mac OS X 10 | Asianux 3<br/>Asianux 4<br/>Asianux 5
CentOS<br/>CentOS 4/5 | CoreOS Linux
Debian GNU/Linux 4<br/>Debian GNU/Linux 5<br/>Debian GNU/Linux 6<br/>Debian GNU/Linux 7<br/>Debian GNU/Linux 8 | FreeBSD
**I - R** |
IBM OS/2 | MS-DOS |
Novell NetWare 5<br/>Novell NetWare 6 | Oracle Linux<br/> Oracle Linux 4/5<br/>Oracle Solaris 10<br/> Oracle Solaris 11
Red Hat Enterprise Linux 2<br/>Red Hat Enterprise Linux 3<br/>Red Hat Enterprise Linux 4<br/>Red Hat Enterprise Linux 5<br/>Red Hat Enterprise Linux 6<br/>Red Hat Enterprise Linux 7<br/>Red Hat Fedora |
**S-T** |
SCO OpenServer 5<br/>SCO OpenServer 6<br/>SCO UnixWare 7 | Serenity Systems eComStation 1<br/>Serenity Systems eComStation 2
Sun Microsystems Solaris 8<br/>Sun Microsystems Solaris 9 | SUSE Linux Enterprise 10<br/> SUSE Linux Enterprise 11<br/>SUSE Linux Enterprise 12<br/>SUSE Linux Enterprise 8/9<br/>SUSE Linux Enterprise 11<br/>SUSE openSUSE
**U-Z** |
Ubuntu Linux | VMware ESXi 4<br/>VMware ESXi 5<br/>VMware ESXi 6
Windows 10<br/>Windows 2000<br/>Windows 3<br/>Windows 7<br/>Windows 8<br/>Windows 95<br/>Windows 98<br/>Windows NT<br/>Windows Server (R) 2008<br/>Windows Server 2003 | Windows Server 2008<br/>Windows Server 2008 R2<br/>Windows Server 2012<br/>Windows Server 2012 R2<br/>Windows Server 2016<br/>Windows Server 2019<br/>Windows Server Threshold<br/>Windows Vista<br/>Windows Web Server 2008 R2<br/>Windows XP Professional


## <a name="next-steps"></a>后续步骤

本教程介绍以下操作：

> [!div class="checklist"]
> * 使用 CSV 导入到“Azure Migrate：服务器评估”的服务器。
> * 已创建并查看评估

现在，[部署一个设备](./migrate-appliance.md)以实现更准确的评估，并使用[依赖项分析](./concepts-dependency-visualization.md)将服务器汇集到组中进行更深入的评估。
