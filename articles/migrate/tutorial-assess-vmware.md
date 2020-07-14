---
title: 使用 Azure Migrate 服务器评估工具评估 VMware VM
description: 介绍如何使用 Azure Migrate 服务器评估工具评估要迁移到 Azure 的本地 VMware VM。
ms.topic: tutorial
ms.date: 06/03/2020
ms.custom: mvc
ms.openlocfilehash: 6c395d7e2be151e97808fa9601ff6001801d243b
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86110345"
---
# <a name="assess-vmware-vms-with-server-assessment"></a>使用“服务器评估”功能评估 VMware VM

本文介绍如何使用 [Azure Migrate:服务器评估](migrate-services-overview.md#azure-migrate-server-assessment-tool)工具来评估本地 VMware 虚拟机 (VM)。


本教程是演示如何评估 VMware VM 以及将其迁移到 Azure 的教程系列中的第二篇文章。 在本教程中，你将了解如何执行以下操作：
> [!div class="checklist"]
> * 设置一个 Azure Migrate 项目。
> * 设置本地运行的 Azure Migrate 设备用于评估 VM。
> * 开始连续发现本地 VM。 设备会将已发现的 VM 的配置和性能数据发送到 Azure。
> * 分组已发现的 VM 并评估 VM 组。
> * 查看评估。

> [!NOTE]
> 教程中演示了方案的最简单部署路径，使你能够快速设置概念证明。 教程尽可能使用默认选项，不会演示所有可能的设置和路径。 有关详细说明，请查看操作指南文章。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="prerequisites"></a>先决条件

- [完成第一个教程](tutorial-prepare-vmware.md)（本系列）。 否则，本教程中的说明不适用。
- 下面是在第一篇教程中应已完成的操作：
    - [准备 Azure](tutorial-prepare-vmware.md#prepare-azure) 以便使用 Azure Migrate。
    - [准备 VMware 以进行评估](tutorial-prepare-vmware.md#prepare-for-assessment)。 这包括检查 VMware 设置、设置供 Azure Migrate 用来访问 vCenter Server 的帐户。
    - [验证](tutorial-prepare-vmware.md#verify-appliance-settings-for-assessment)部署用于 VMware 评估的 Azure Migrate 设备所需的内容。

## <a name="set-up-an-azure-migrate-project"></a>设置 Azure Migrate 项目

按如下所述设置新的 Azure Migrate 项目：

1. 在 Azure 门户中选择“所有服务”，然后搜索 **Azure Migrate**。
2. 在“服务”下选择“Azure Migrate”。 
3. 在“概览”中的“发现、评估和迁移服务器”下，选择“评估和迁移服务器”。  

   ![用于评估和迁移服务器的按钮](./media/tutorial-assess-vmware/assess-migrate.png)

4. 在“入门”中，选择“添加工具”。 
5. 在“迁移项目”中选择你的 Azure 订阅，并创建一个资源组（如果没有）。     
6. 在“项目详细信息”中，指定项目名称以及要在其中创建项目的地理位置。 查看[公有云](migrate-support-matrix.md#supported-geographies-public-cloud)和[政府云](migrate-support-matrix.md#supported-geographies-azure-government)支持的地理位置。

   ![用于项目名称和区域的框](./media/tutorial-assess-vmware/migrate-project.png)

7. 选择“**下一页**”。
8. 在“选择评估工具”中，选择“Azure Migrate: 服务器评估” > “下一步”。

   ![用于服务器评估工具的选项](./media/tutorial-assess-vmware/assessment-tool.png)

9. 在“选择迁移工具”中，选择“暂时跳过添加迁移工具” > “下一步”。  
10. 在“查看 + 添加工具”中查看设置，然后选择“添加工具”。 
11. 等待几分钟，让 Azure Migrate 项目部署完成。 随后将转到项目页。 如果未看到该项目，可以从 Azure Migrate 仪表板中的“服务器”访问它。

## <a name="set-up-the-azure-migrate-appliance"></a>设置 Azure Migrate 设备

Azure Migrate:服务器评估使用轻型 Azure Migrate 设备。 此设备执行 VM 发现并将 VM 元数据和性能数据发送到 Azure Migrate。 可通过多种方式设置此设备。

- 使用下载的 OVA 模板在 VMware VM 上进行设置。 这是本教程中使用的方法。
- 使用 PowerShell 安装程序脚本在 VMware VM 或物理计算机上进行设置。 如果无法使用 OVA 模板设置 VM，或者你使用的是 Azure 政府，则应使用[此方法](deploy-appliance-script.md)。

创建设备后，请检查它是否可以连接到 Azure Migrate:服务器评估，首次配置该设备，并将其注册到 Azure Migrate 项目。


### <a name="download-the-ova-template"></a>下载 OVA 模板

1. 在“迁移目标” > “服务器” > “Azure Migrate:  服务器评估”中，选择“发现”。
2. 在“发现计算机” > “计算机是否已虚拟化?”中，选择“是，使用 VMWare vSphere 虚拟机监控程序”。  
3. 选择“下载”以下载 OVA 模板文件。

   ![用于下载 OVA 文件的选项](./media/tutorial-assess-vmware/download-ova.png)

### <a name="verify-security"></a>验证安全性

在部署 OVA 文件之前检查其安全性：

1. 在下载文件的计算机上，打开管理员命令窗口。
2. 运行以下命令，生成 OVA 文件的哈希：
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   用法示例：```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```

3. 验证最新的设备版本和哈希值：

    - 对于 Azure 公有云：
    
        **算法** | **下载** | **SHA256**
        --- | --- | ---
        VMware (10.9 GB) | [最新版本](https://aka.ms/migrate/appliance/vmware) | cacbdaef927fe5477fa4e1f494fcb7203cbd6b6ce7402b79f234bc0fe69663dd

    - 对于 Azure 政府：
    
        **算法** | **下载** | **SHA256**
        --- | --- | ---
        VMware (63.1 MB) | [最新版本](https://go.microsoft.com/fwlink/?linkid=2120300&clcid=0x409 ) | 3d5822038646b81f458d89d706832c0a2c0e827bfa9b0a55cc478eaf2757a4de


### <a name="create-the-appliance-vm"></a>创建设备 VM

导入下载的文件，然后创建 VM：

1. 在 vSphere 客户端控制台中，选择“文件” > “部署 OVF 模板”。

   ![用于部署 OVF 模板的菜单命令](./media/tutorial-assess-vmware/deploy-ovf.png)

2. 在“部署 OVF 模板向导”>“源”中，指定 OVA 文件的位置。
3. 在“名称”和“位置”中，为 VM 指定一个易记名称。  选择要在其中托管 VM 的库存对象。
4. 在“主机/群集”中，指定要在其上运行 VM 的主机或群集。
5. 在“存储”中，指定 VM 的存储目标。
6. 在“磁盘格式”中，指定磁盘类型和大小。
7. 在“网络映射”中，指定 VM 要连接到的网络。 该网络需要与 Internet 建立连接，这样才能向 Azure Migrate 服务器评估发送元数据。
8. 检查并确认设置，然后选择“完成”。

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
1. 在 Web 应用 >“设置必备组件”中执行以下操作：
   - **许可证**：接受许可条款，并阅读第三方信息。
   - **连接**：应用将检查 VM 是否可访问 Internet。 如果 VM 使用代理：
     - 选择“代理设置”，并以 http://ProxyIPAddress 或 http://ProxyFQDN 格式指定代理地址和侦听端口。
     - 如果代理需要身份验证，请指定凭据。
     - 仅支持 HTTP 代理。
   - **时间同步**：设备上的时间应与 Internet 时间同步，这样才能正常进行发现。
   - **安装更新**：设备可确保安装最新的更新。
   - **安装 VDDK**：设备将检查是否已安装 VMWare vSphere 虚拟磁盘开发工具包 (VDDK)。 从 VMware 下载 VDDK 6.7（如果尚未安装它），并将下载的 zip 内容解压缩到设备上的指定位置。

     Azure Migrate 服务器迁移在迁移到 Azure 期间使用 VDDK 复制计算机。       

### <a name="register-the-appliance-with-azure-migrate"></a>将设备注册到 Azure Migrate

1. 选择“登录”。 如果未显示该按钮，请确保已在浏览器中禁用弹出窗口阻止程序。
2. 在新选项卡上，使用 Azure 用户名和密码登录。
   
   不支持使用 PIN 登录。
3. 成功登录后，返回到 Web 应用。
4. 选择在其中创建了 Azure Migrate 项目的订阅，然后选择项目。
5. 指定设备的名称。 该名称应是字母数字，长度为 14 个或更少的字符。
6. 选择“注册”。


## <a name="start-continuous-discovery"></a>启动持续发现

设备需连接 vCenter Server，以发现 VM 的配置和性能数据。

### <a name="specify-vcenter-server-details"></a>指定 vCenter Server 详细信息
1. 在“指定 vCenter Server 详细信息”中，指定 vCenter Server 实例的名称 (FQDN) 或 IP 地址。 可以保留默认端口，或指定 vCenter Server 侦听的自定义端口。
2. 在“用户名”和“密码”中，指定设备用来发现 vCenter 服务器实例上的 VM 的 vCenter Server 帐户凭据 。 

    - 你应该已在[上一教程](tutorial-prepare-vmware.md#set-up-permissions-for-assessment)中设置具有所需权限的帐户。
    - 如果要将发现范围限定于特定的 VMware 对象（vCenter Server 数据中心、群集、群集文件夹、主机、主机文件夹或单个 VM），请参阅[此文](set-discovery-scope.md)中的说明，以限制 Azure Migrate 所使用的帐户。

3. 选择“验证连接”，确保设备可以连接到 vCenter Server。
4. 在“发现 VM 上的应用程序和依赖项”中，可以选择单击“添加凭据”，并指定与凭据相关的操作系统以及用户名和密码这两项凭据。  然后单击“添加”。

    - 如果已创建用于[应用程序发现功能](how-to-discover-applications.md)或[无代理依赖项分析功能](how-to-create-group-machine-dependencies-agentless.md)的帐户，则可选择在此处添加凭据。
    - 如果不使用这些功能，则可跳过此设置。
    - 查看[应用发现](migrate-support-matrix-vmware.md#application-discovery-requirements)或[无代理分析](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless)所需的凭据。

5. **保存并启动发现**，即可启动 VM 发现。

发现的工作原理如下：
- 大约 15 分钟后，已发现 VM 的元数据会显示在门户中。
- 发现已安装的应用程序、角色和功能需要一些时间， 具体时间取决于待发现的 VM 数量。 如果是 500 个 VM，Azure Migrate 门户大约需要一小时才会显示应用程序清单。

### <a name="verify-vms-in-the-portal"></a>在门户中验证 VM

发现完成后，可以验证 VM 是否出现在 Azure 门户中：

1. 打开 Azure Migrate 仪表板。
2. 在“Azure Migrate - 服务器” > “Azure Migrate: 服务器评估”中，选择显示了**已发现服务器**计数的图标。

## <a name="set-up-an-assessment"></a>设置评估

可以使用 Azure Migrate 服务器评估创建两种类型的评估：

**评估类型** | **详细信息**
--- | --- 
**Azure VM** | 将本地服务器迁移到 Azure 虚拟机的评估。 <br/><br/> 可使用此评估类型评估要迁移到 Azure 的本地 [VMware VM](how-to-set-up-appliance-vmware.md)、[Hyper-V VM](how-to-set-up-appliance-hyper-v.md) 和[物理服务器](how-to-set-up-appliance-physical.md)。 [了解详细信息](concepts-assessment-calculation.md)
**Azure VMware 解决方案 (AVS)** | 将本地服务器迁移到 [Azure VMware 解决方案 (AVS)](../azure-vmware/introduction.md) 的评估。 <br/><br/> 可使用此评估类型评估要迁移到 Azure VMware 解决方案 (AVS) 的本地 [VMware VM](how-to-set-up-appliance-vmware.md)。 [了解详细信息](concepts-azure-vmware-solution-assessment-calculation.md)

服务器评估提供两个调整大小标准选项：

**调整大小标准** | **详细信息** | **数据**
--- | --- | ---
**基于性能** | 基于收集的性能数据提出建议的评估 | **Azure VM 评估**：VM 大小建议基于 CPU 和内存利用率数据。<br/><br/> 磁盘类型建议（标准 HDD/SSD 或高级托管磁盘）基于本地磁盘的 IOPS 和吞吐量。<br/><br/> **Azure VMware 解决方案 (AVS) 评估**：AVS 节点建议基于 CPU 和内存利用率数据。
**按本地原样** | 不使用性能数据来提出建议的评估。 | **Azure VM 评估**：VM 大小建议基于本地 VM 大小<br/><br> 建议的磁盘类型基于在存储类型设置中选择要评估的内容。<br/><br/> **Azure VMware 解决方案 (AVS) 评估**：AVS 节点建议基于本地 VM 大小。

## <a name="run-an-assessment"></a>运行评估

运行 Azure VM 评估，如下所述：

1. 查看有关创建评估的[最佳做法](best-practices-assessment.md)。
2. 在“服务器”选项卡上的“Azure Migrate: 服务器评估”磁贴中，选择“评估”。

   ![“评估”按钮的位置](./media/tutorial-assess-vmware/assess.png)

3. 在“评估服务器”中，将评估类型选择为“Azure VM”，选择发现源并指定评估名称。

    ![评估基本信息](./media/tutorial-assess-vmware/assess-servers-azurevm.png)
 
4. 选择“全部查看”，然后查看评估属性。

   ![评估属性](./media/tutorial-assess-vmware/view-all.png)

5. 单击“下一步”以转到“选择要评估的计算机” 。 在“选择或创建组”中，选择“新建”并指定组名称。  组将要评估的一个或多个 VM 集合到一起。
6. 在“将计算机添加到组”中，选择要添加到该组的 VM。
7. 单击“下一步”以转到“查看 + 创建评估”以查看评估详细信息 。
8. 选择“创建评估”以创建该组并运行评估。

   ![评估服务器](./media/tutorial-assess-vmware/assessment-create.png)

8. 创建评估后，在“服务器” > “Azure Migrate: 服务器评估” > “评估”中查看它。
9. 选择“导出评估”，将评估下载为 Excel 文件。

如果要运行 Azure VMware 解决方案 (AVS) 评估，请按照[此处](how-to-create-azure-vmware-solution-assessment.md)所述步骤进行操作。


## <a name="review-an-assessment"></a>查看评估

评估描述：

- **Azure 迁移就绪性**：VM 是否适合迁移到 Azure。
- **每月成本估算**：在 Azure 中运行 VM 的估算每月计算和存储成本。
- **每月存储成本估算**：迁移后的磁盘存储估算成本。

若要查看评估，请执行以下操作：

1. 在“迁移目标” > “服务器”中，选择“Azure Migrate:   服务器评估”中的“评估”。
2. 在“评估”中，选择某项评估将其打开。

   ![评估摘要](./media/tutorial-assess-vmware/assessment-summary.png)

### <a name="review-azure-readiness"></a>查看 Azure 迁移就绪性

1. 在“Azure 迁移就绪性”中，验证 VM 是否已准备好迁移到 Azure。
2. 查看 VM 状态：
    - **已做好 Azure 迁移准备**：在 Azure Migrate 针对评估中的 VM 建议 VM 大小并显示估算成本时使用。
    - **准备就绪但存在以下状况**：显示问题和建议的补救措施。
    - **尚未做好 Azure 迁移准备**：显示问题和建议的补救措施。
    - **就绪性未知**：当数据可用性问题导致 Azure Migrate 无法评估就绪性时使用。

3. 选择某种“Azure 迁移就绪性”状态。 可以查看 VM 就绪详情。 还可以深入查看 VM 详细信息，包括计算、存储和网络设置。

### <a name="review-cost-details"></a>查看成本详细信息

评估摘要显示估算出来的在 Azure 中运行 VM 的计算和存储成本。 将聚合评估的组中所有 VM 的成本。 可以深入查看特定 VM 的成本详细信息。

> [!NOTE]
> 成本估算基于计算机及其磁盘和属性的大小建议。 估算适用于以 IaaS VM 形式运行本地 VM。 Azure Migrate 服务器评估不考虑 PaaS 或 SaaS 成本。

已评估组的聚合存储成本按不同类型的存储磁盘划分。 

### <a name="review-confidence-rating"></a>查看置信度分级

Azure Migrate 服务器评估会为基于性能的评估分配置信度分级，从 1 星（最低）到 5 星（最高）不等。

![置信度分级](./media/tutorial-assess-vmware/confidence-rating.png)

置信度分级用于判断评估的大小建议的可靠性。 此分级基于对评估进行计算时所需数据点的可用性：

**数据点可用性** | **置信度分级**
--- | ---
0%-20% | 1 星
21%-40% | 2 星
41%-60% | 3 星
61%-80% | 4 星
81%-100% | 5 星

[了解最佳做法](best-practices-assessment.md#best-practices-for-confidence-ratings)（针对置信度分级）。

## <a name="next-steps"></a>后续步骤

在本教程中，我们设置了一个 Azure Migrate 设备。 我们还创建并查看了评估。

若要了解如何使用 Azure Migrate 服务器迁移将 VMware VM 迁移到 Azure，请继续学习本系列的第三个教程：

> [!div class="nextstepaction"]
> [迁移 VMware VM](./tutorial-migrate-vmware.md)
