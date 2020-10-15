---
title: 使用导入的 CSV 文件通过 Azure Migrate 服务器评估来评估本地服务器
description: 描述如何在 Azure Migrate 服务器评估中使用导入的 CSV 文件来发现要迁移到 Azure 的本地服务器
ms.topic: tutorial
ms.date: 09/14/2020
ms.openlocfilehash: 743f18ce72e3f14fe54e0bbadff254ea03fc6278
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90604217"
---
# <a name="tutorial-assess-servers-using-an-imported-csv-file"></a>教程：使用导入的 CSV 文件评估服务器

在迁移到 Azure 的过程中，你将发现本地库存和工作负载。 

本教程演示如何使用 Azure Migrate 评估本地计算机：服务器评估工具，使用导入的逗号分隔值 (CSV) 文件。 

如果使用 CSV 文件，则不需要设置 Azure Migrate 设备来发现和评估服务器。 你可以控制在文件中共享的数据，并且许多数据都是可选的。 这种方法可用于以下情况：

- 在部署设备之前，你需要创建快速初始评估。
- 你无法在组织中部署 Azure Migrate 设备。
- 你无法共享允许访问本地服务器的凭据。
- 安全约束阻止你收集设备所收集的数据并将其发送到 Azure。

> [!NOTE]
> 无法使用 CSV 文件迁移导入的服务器。

在本教程中，你将了解如何执行以下操作：
> [!div class="checklist"]
> * 设置 Azure 帐户
> * 设置 Azure Migrate 项目
> * 准备 CSV 文件
> * 导入文件
> * 评估服务器

> [!NOTE]
> 教程显示尝试方案的最快路径，并尽可能使用默认选项。 

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="prerequisites"></a>先决条件

- 最多可以在单个 CSV 文件和 Azure Migrate 项目中添加 20,000 个服务器。 
- 在 CSV 文件中指定的操作系统名称必须包含并匹配[支持的名称](#supported-operating-system-names)。


## <a name="prepare-an-azure-user-account"></a>准备 Azure 用户帐户

若要创建 Azure Migrate 迁移项目，则需要一个帐户，其中包含：
- Azure 订阅的参与者或所有者权限。
- 用于注册 Azure Active Directory 应用的权限。

如果你刚刚创建了免费的 Azure 帐户，那么你就是订阅的所有者。 如果你不是订阅所有者，请让所有者分配权限，如下所示：

1. 在 Azure 门户中，搜索“订阅”，然后在“服务”下选择“订阅” 。

    ![用于搜索 Azure 订阅的搜索框](./media/tutorial-discover-import/search-subscription.png)

2. 在“订阅”页上，选择要在其中创建 Azure Migrate 项目的订阅。 
3. 在“订阅”中，选择“访问控制 (IAM)” > “检查访问权限” 。
4. 在“检查访问权限”中，搜索相关的用户帐户。
5. 在“添加角色分配”中，单击“添加” 。

    ![搜索用户帐户，检查访问权限，并分配角色](./media/tutorial-discover-import/azure-account-access.png)

6. 在“添加角色分配”中，选择“参与者”或“所有者”角色，然后选择帐户（在我们的示例中为 azmigrateuser）。 然后单击“保存”  。

    ![打开“添加角色分配”页，将角色分配给帐户](./media/tutorial-discover-import/assign-role.png)

7. 在门户中搜索用户，然后在“服务”下，选择“用户” 。
8. 在“用户设置”中，验证 Azure AD 用户是否可以注册应用程序（默认情况下设置为“是”） 。

    ![在用户设置中，验证用户是否可以注册 Active Directory 应用](./media/tutorial-discover-import/register-apps.png)



## <a name="set-up-a-project"></a>设置项目

如果没有 Azure Migrate 项目，则新建一个。

1. 在 Azure 门户中选择“所有服务”，然后搜索 **Azure Migrate**。
2. 在“服务”下选择“Azure Migrate”。 
3. 在“概述”中，选择“创建项目” 。
5. 在“创建项目”中，选择 Azure 订阅和资源组。 如果没有资源组，请创建一个资源组。
6. 在“项目详细信息”中，指定项目名称以及要在其中创建项目的地理位置。 查看[公有云](migrate-support-matrix.md#supported-geographies-public-cloud)和[政府云](migrate-support-matrix.md#supported-geographies-azure-government)支持的地理位置。

   ![用于项目名称和区域的框](./media/tutorial-discover-import/new-project.png)

7. 选择“创建”。
8. 等待几分钟，让 Azure Migrate 项目部署完成。

默认会将“Azure Migrate:服务器评估”工具添加到新项目。

![显示默认情况下已添加的服务器评估工具的页面](./media/tutorial-discover-import/added-tool.png)

## <a name="prepare-the-csv"></a>准备 CSV

下载 CSV 模板，并向其中添加服务器信息。

### <a name="download-the-template"></a>下载模板

1. 在“迁移目标” > “服务器” > “Azure Migrate:  服务器评估”中，选择“发现”。
2. 在“发现计算机”中，选择“使用 CSV 导入”。
3. 选择“下载”以下载 CSV 模板。 或者，也可以[直接下载它](https://go.microsoft.com/fwlink/?linkid=2109031)。

    ![下载 CSV 模板](./media/tutorial-discover-import/download-template.png)

### <a name="add-server-information"></a>添加服务器信息

收集服务器数据，并将其添加到 CSV 文件中。

- 若要收集数据，可以从用于本地服务器管理的工具（如 VMware vSphere 或配置管理数据库 (CMDB)）导出数据。
- 若要查看示例数据，请下载我们的[示例文件](https://go.microsoft.com/fwlink/?linkid=2108405)。

下表汇总了要填写的文件字段：

**字段名称** | **必需** | **详细信息**
--- | --- | ---
**服务器名称** | 是 | 建议指定完全限定的域名 (FQDN)。
IP 地址 | 否 | 服务器地址。
**核心数** | 是 | 分配给服务器的处理器内核数。
**内存** | 是 | 分配给服务器的总 RAM (MB)。
**OS 名称** | 是 | 服务器操作系统。 <br/> 匹配或包含[此](#supported-operating-system-names)列表中的名称的操作系统名称将被评估识别。
**OS 版本** | 否 | 服务器操作系统版本。
**OS 体系结构** | 否 | 服务器 OS 体系结构 <br/> 有效值为：x64、x86、amd64、32 位或 64 位
**磁盘数目** | 否 | 如果提供了单独的磁盘详细信息，则不需要。
**磁盘 1 大小**  | 否 | 磁盘最大大小 (GB)。<br/>可以通过在模板中[添加列](#add-multiple-disks)来添加更多磁盘的详细信息。 最多可以添加八个磁盘。
**磁盘 1 读取操作数** | 否 | 每秒的磁盘读取操作次数。
**磁盘 1 写入操作数** | 否 | 每秒的磁盘写入操作次数。
**磁盘 1 读取吞吐量** | 否 | 每秒从磁盘读取的数据，以 MB/秒为单位。
**磁盘 1 写入吞吐量** | 否 | 每秒写入磁盘的数据，以 MB/秒为单位。
**CPU 使用率百分比** | 否 | CPU 使用百分比。
**内存使用率百分比** | 否 | RAM 使用百分比。
**磁盘读取操作总数** | 否 | 每秒的磁盘读取操作次数。
**磁盘写入操作总数** | 否 | 每秒的磁盘写入操作次数。
**磁盘读取吞吐量总数** | 否 | 从磁盘读取的数据，以 MB/秒为单位。
**磁盘写入吞吐量总数** | 否 | 写入磁盘的数据，以 MB/秒为单位。
**网络流入吞吐量** | 否 | 服务器接收的数据，以 MB/秒为单位。
**网络流出吞吐量** | 否 | 服务器传输的数据，以 MB/秒为单位。
**固件类型** | 否 | 服务器固件。 值可以是“BIOS”或“UEFI”。
**MAC 地址**| 否 | 服务器 MAC 地址。


### <a name="add-operating-systems"></a>添加操作系统

评估识别特定的操作系统名称。 你指定的任何名称都必须与[支持的名称列表](#supported-operating-system-names)中的字符串之一匹配。

### <a name="add-multiple-disks"></a>添加多个磁盘

模板为第一个磁盘提供默认字段。 你可以为最多八个磁盘添加类似的列。

例如，若要指定第二个磁盘的所有字段，请添加以下列：

- 磁盘 2 大小
- 磁盘 2 读取操作数
- 磁盘 2 写入操作数
- 磁盘 2 读取吞吐量
- 磁盘 2 写入吞吐量


## <a name="import-the-server-information"></a>导入服务器信息

将信息添加到 CSV 模板之后，将 CSV 文件导入到“服务器评估”。

1. 在 Azure Migrate 中，在“发现计算机”中，转到完成的模板。
2. 选择“导入”。
3. 将显示导入状态。
    - 如果状态中出现警告，则可以修复它们，也可以继续操作而不解决它们。
    - 若要提高评估准确性，请按照警告中建议的方式改进服务器信息。
    - 若要查看并修复警告，请单击“下载警告详细信息 .CSV”。 此操作将下载包含警告的 CSV。 查看警告并根据需要解决问题。
    - 如果状态中出现错误，导致导入状态为“失败”，则必须修复这些错误，然后才能继续导入：
        1. 下载 CSV，现在其中包含错误详细信息。
        1. 查看并根据需要处理错误。 
        1. 重新上传修改后的文件。
4. 如果导入状态为“已完成”，则服务器信息已导入。 如果导入过程似乎未完成，请刷新。

## <a name="update-server-information"></a>更新服务器信息

可以通过使用相同的服务器名称再次上传服务器数据来更新服务器信息。 不能修改“服务器名称”字段。 目前不支持删除服务器。

## <a name="verify-servers-in-the-portal"></a>验证门户中的服务器

在发现完成后，若要验证服务器是否出现在 Azure 门户中，请执行以下操作：

1. 打开 Azure Migrate 仪表板。
2. 在“Azure Migrate - 服务器” > “Azure Migrate: 服务器评估”页面上，选择显示了**已发现服务器**计数的图标。
3. 选择“基于导入”选项卡。



## <a name="supported-operating-system-names"></a>支持的操作系统名称

CSV 中提供的操作系统名称必须包含此列表中的名称并与之匹配。 否则，你将无法对它们进行评估。 

**A-H** | **I-R** | **S-T** | **U-Z**
--- | --- | --- | ---
Apple Mac OS X 10<br/>Asianux 3<br/>Asianux 4<br/>Asianux 5<br/>CentOS<br/>CentOS 4/5<br/>CoreOS Linux<br/>Debian GNU/Linux 4<br/>Debian GNU/Linux 5<br/>Debian GNU/Linux 6<br/>Debian GNU/Linux 7<br/>Debian GNU/Linux 8<br/>FreeBSD | IBM OS/2<br/>MS-DOS<br/>Novell NetWare 5<br/>Novell NetWare 6<br/>Oracle Linux<br/>Oracle Linux 4/5<br/>Oracle Solaris 10<br/>Oracle Solaris 11<br/>Red Hat Enterprise Linux 2<br/>Red Hat Enterprise Linux 3<br/>Red Hat Enterprise Linux 4<br/>Red Hat Enterprise Linux 5<br/>Red Hat Enterprise Linux 6<br/>Red Hat Enterprise Linux 7<br/>Red Hat Fedora | SCO OpenServer 5<br/>SCO OpenServer 6<br/>SCO UnixWare 7<br/> Serenity Systems eComStation 1<br/>Serenity Systems eComStation <br/>Sun Microsystems Solaris 8<br/>Sun Microsystems Solaris 9<br/><br/>SUSE Linux Enterprise 10<br/>SUSE Linux Enterprise 11<br/>SUSE Linux Enterprise 12<br/>SUSE Linux Enterprise 8/9<br/>SUSE Linux Enterprise 11<br/>SUSE openSUSE | Ubuntu Linux<br/>VMware ESXi 4<br/>VMware ESXi 5<br/>VMware ESXi 6<br/>Windows 10<br/>Windows 2000<br/>Windows 3<br/>Windows 7<br/>Windows 8<br/>Windows 95<br/>Windows 98<br/>Windows NT<br/>Windows Server (R) 2008<br/>Windows Server 2003<br/>Windows Server 2008<br/>Windows Server 2008 R2<br/>Windows Server 2012<br/>Windows Server 2012 R2<br/>Windows Server 2016<br/>Windows Server 2019<br/>Windows Server Threshold<br/>Windows Vista<br/>Windows Web Server 2008 R2<br/>Windows XP Professional

## <a name="next-steps"></a>后续步骤

本教程介绍以下操作：

> [!div class="checklist"]
> * 创建 Azure Migrate 项目 
> * 使用导入的 CSV 文件发现服务器。 现在，为[到 Azure VM 的 VMware VM 迁移](tutorial-assess-vmware.md)运行评估。
