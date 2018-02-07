---
title: "使用 Azure 导入/导出将数据传入/传出 Azure 存储 | Microsoft Docs"
description: "了解如何在 Azure 门户中创建导入和导出作业，以便将数据传入/传出到 Azure 存储。"
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 668f53f2-f5a4-48b5-9369-88ec5ea05eb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2017
ms.author: muralikk
ms.openlocfilehash: 37860425460496c5fc2451713d1d3ec58ac9106d
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2018
---
# <a name="use-the-microsoft-azure-importexport-service-to-transfer-data-to-azure-storage"></a>使用 Microsoft Azure 导入/导出服务将数据传输到 Azure 存储中
本文分步介绍如何使用 Azure 导入/导出服务将磁盘驱动器寄送到 Azure 数据中心，从而安全地将大量数据传输到 Azure Blob 存储和 Azure 文件。 此外，还可以使用此服务将数据从 Azure 存储传输到硬盘驱动器，然后再寄送到本地站点。 可将单个内部 SATA 磁盘驱动器中的数据导入 Azure Blob 存储或 Azure 文件。 

> [!IMPORTANT] 
> 此服务仅接受内部 SATA HDD 或 SSD。 不支持任何其他设备。 请勿发送外部 HDD、NAS 设备等，因为可能会将其返回（若可能）或丢弃。
>
>

如果将磁盘上的数据导入 Azure 存储，请执行以下步骤。
### <a name="step-1-prepare-the-drives-using-waimportexport-tool-and-generate-journal-files"></a>步骤 1：使用 WAImportExport 工具准备驱动器，并生成日志文件。

1.  标识要导入 Azure 存储的数据。 导入的数据可以是本地服务器或网络共享中的目录和独立文件。
2.  根据数据的总大小，采购所需数目的 2.5 英寸 SSD 或者 2.5/3.5 英寸 SATA II 或 III 硬盘驱动器。
3.  直接使用 SATA 或通过外部 USB 适配器将硬盘驱动器附加到 Windows 计算机。
4.  在每个硬盘驱动器上创建一个 NTFS 卷，并向该卷分配一个驱动器号。 没有装入点。
5.  若要在 Windows 计算机上启用加密，请启用 NTFS 卷上的 bit locker 加密。 使用 https://technet.microsoft.com/en-us/library/cc731549(v=ws.10).aspx 上的说明。
6.  使用复制粘贴或拖放操作，或使用 RoboCopy 或任何类似的工具将数据完全复制到磁盘上这些加密的 NTFS 卷。
7.  从 https://www.microsoft.com/en-us/download/details.aspx?id=42659 下载 WAImportExport V1
8.  解压缩到默认文件夹 waimportexportv1。 例如，C:\WaImportExportV1  
9.  以管理员身份运行并打开 PowerShell 或命令行，并将目录更改为解压缩的文件夹。 例如，cd C:\WaImportExportV1
10. 将以下命令行复制到记事本并进行编辑，创建命令行。
  ./WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1 /sk:== /t:D /bk: /srcdir:D:\ /dstdir:ContainerName/ /skipwrite
    
    /j：扩展名为 .jrn 的“日志文件”的文件名。 在每个驱动器上生成一个日志文件，因此建议使用磁盘序列号作为日志文件名。
    /sk：Azure 存储帐户密钥。 /t：要寄送的磁盘的驱动器号。 例如，D /bk 是驱动器的 bit locker 密钥，/srcdir 是要寄送的磁盘的驱动器号，后跟 :\。 例如，D:\
    /dstdir：要向其导入数据的 Azure 存储容器的名称。
    /skipwrite 
    
11. 为每个需要寄送的磁盘重复步骤 10。
12. 每次运行命令行时，都会创建使用 /j： 参数提供名称的日志文件。

### <a name="step-2-create-an-import-job-on-azure-portal"></a>步骤 2：在 Azure 门户中创建导入作业。

1. 登录 https://portal.azure.com/，在“更多服务”->“存储”->“导入/导出作业”下，单击“创建导入/导出作业”。

2. 在“基本”部分中，选择“导入 Azure”，输入作业名称字符串，选择订阅，输入或选择资源组。 输入导入作业的描述性名称。 请注意，输入的名称只能包含小写字母、数字、连字符和下划线，必须以字母开头并且不得包含空格。 在作业进行中以及作业完成后，使用所选名称来跟踪作业。

3. 在“作业详细信息”部分中，上传在驱动器准备步骤中获取的驱动器日志文件。 如果使用了 waimportexport.exe version1，需要为已准备好的每个驱动器上传一个文件。 在“导入目标”存储帐户部分中，选择要将数据导入哪个存储帐户。 “放置位置”根据选定存储帐户所属的区域自动进行填充。
   
   ![创建导入作业 - 步骤 3](./media/storage-import-export-service/import-job-03.png)
4. 在“回寄信息”部分中，从下拉列表中选择快递商，并输入已通过此快递商创建的有效快递商帐号。 导入作业完成后，Microsoft 使用此帐户寄回驱动器。 输入完整、有效的联系人姓名、电话号码、电子邮件地址、街道地址、城市、邮政编码、州/省/自治区/直辖市和国家/地区。
   
5. 在“摘要”部分中，输入 Azure 数据中心寄送地址是为了将磁盘寄送到 Azure 数据中心。 请确保寄送标签上标明了作业名称和完整地址。 

6. 单击“摘要页”上的“确定”，完成“创建导入作业”。

### <a name="step-3-ship-the-drives-to-the-azure-datacenter-shipping-address-provided-in-step-2"></a>步骤 3：将驱动器寄送到步骤 2 中提供的 Azure 数据中心寄送地址。
可以使用 FedEx、UPS 或 DHL 将包裹寄送到 Azure 数据中心。

### <a name="step-4-update-the-job-created-in-step2-with-tracking-number-of-the-shipment"></a>步骤 4：使用货物的跟踪号更新在步骤 2 中创建的作业。
寄送磁盘后，返回到 Azure 门户上的“导入/导出”页，通过以下步骤更新跟踪号：a) 转到并单击导入作业；b) 单击“驱动器寄送后，更新作业状态和跟踪信息”。 c) 选中“标记为‘已寄送’”复选框；d) 输入“快递商”和“跟踪号码”。
如果在创建作业后的 2 周内未更新跟踪号，该作业会过期。 可以在门户仪表板上跟踪作业进度。 若要了解上一部分中每个作业状态的含义，请[查看作业状态](#viewing-your-job-status)。

## <a name="when-should-i-use-the-azure-importexport-service"></a>应该在什么时候使用 Azure 导入/导出服务？
如果通过网络上传或下载数据速度太慢，或者获取额外的网络带宽因成本过高而受到限制，则可考虑使用 Azure 导入/导出服务。

下面这样的场景可以使用此服务：

* 将数据迁移到云：将大量数据快速且经济高效地转移到 Azure。
* 内容分发：将数据快速发送到客户站点。
* 备份：将本地数据备份后存储在 Azure 存储中。
* 数据恢复：恢复存储在存储中的大量数据，然后将其递送到本地位置。

## <a name="prerequisites"></a>先决条件
本部分列出了使用此服务需要满足的先决条件。 在寄送驱动器之前仔细查看这些先决条件。

### <a name="storage-account"></a>存储帐户
必须拥有 Azure 订阅以及一个或多个存储帐户才能使用导入/导出服务。 每个作业只能用于将数据传输到一个存储帐户或者从一个存储帐户传输数据。 换言之，一个导入/导出作业不能跨多个存储帐户。 有关创建新存储帐户的信息，请参阅[如何创建存储帐户](storage-create-storage-account.md#create-a-storage-account)。

### <a name="data-types"></a>数据类型
可使用 Azure 导入/导出服务将数据复制到“块”Blob、“页”Blob 或文件中。 与之相反，只能使用此服务从 Azure 存储中导出**块** Blob、**页** Blob 或**追加** Blob。 该服务仅支持将 Azure 文件导入到 Azure 存储。 当前暂不支持导出 Azure 文件。

### <a name="job"></a>作业
要开始从存储进行导入或导出的过程，首先要创建一个“作业”。 作业可以是“导入作业”或“导出作业”：

* 需要将本地数据传输到 Azure 存储帐户时，可创建导入作业。
* 若要将当前在存储帐户中存储的数据传输到寄送给 Microsoft 的硬盘，请创建导出作业。 创建作业时，需通知导入/导出服务：要将一个或多个硬盘驱动器运送到 Azure 数据中心。

* 对于导入作业，需要寄送包含数据的硬盘驱动器。
* 对于导出作业，需要寄送空硬盘驱动器。
* 每个作业最多可以寄送 10 个硬盘驱动器。

可以使用 Azure 门户或 [Azure 存储导入/导出 REST API](/rest/api/storageimportexport) 创建导入或导出作业。

> [!Note]
> 自 2018 年 2 月 28 日起将不再支持 RDFE API。 若要继续使用此服务，请迁移到 [ARM 导入/导出 REST API](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/storageimportexport/resource-manager/Microsoft.ImportExport/stable/2016-11-01/storageimportexport.json)。 

### <a name="waimportexport-tool"></a>WAImportExport 工具
创建**导入**作业的第一步是准备需要通过寄送进行数据导入的驱动器。 要准备驱动器，必须将其连接到本地服务器，然后在本地服务器上运行 WAImportExport 工具。 使用此 WAImportExport 工具可以方便地将数据复制到驱动器、使用 BitLocker 加密驱动器上的数据，以及生成驱动器日志文件。

日志文件存储有关作业和驱动器的基本信息，例如驱动器序列号和存储帐户名称。 此日志文件不存储在该驱动器上。 它在导入作业创建期间使用。 本文后面提供了有关作业创建过程的分步详细信息。

WAImportExport 工具仅兼容 64 位 Windows 操作系统。 请参阅[操作系统](#operating-system)部分以了解受支持的特定 OS 版本。

下载最新版本的 [WAImportExport 工具](http://download.microsoft.com/download/3/6/B/36BFF22A-91C3-4DFC-8717-7567D37D64C5/WAImportExportV2.zip)。 有关使用 WAImportExport 工具的详细信息，请参阅[使用 WAImportExport 工具](storage-import-export-tool-how-to.md)。

>[!NOTE]
>**以前的版本：**可以[下载 WAImportExpot V1](http://download.microsoft.com/download/0/C/D/0CD6ABA7-024F-4202-91A0-CE2656DCE413/WaImportExportV1.zip) 版本的工具，并参考 [WAImportExpot V1 使用指南](storage-import-export-tool-how-to-v1.md)。 WAImportExpot V1 版本的工具支持**在已将数据预先写入磁盘的情况下准备磁盘**。 如果唯一可用的密钥是 SAS 密钥，则也需要 WAImportExpot V1 工具。

>

### <a name="hard-disk-drives"></a>硬盘驱动器
只支持将 2.5 英寸 SSD 或者 2.5/3.5 英寸 SATA II 或 III 内部 HDD 用于导入/导出服务。 单个导入/导出作业最多可以有 10 个 HDD/SSD，并且每个 HDD/SSD 可以为任意大小。 大量驱动器可以分布在多个作业上，并且可创建的作业数没有限制。 

对于导入作业，仅处理驱动器上的第一个数据卷。 该数据卷必须使用 NTFS 进行格式化。

> [!IMPORTANT]
> 此服务不支持内置 USB 适配器附带的外部硬盘驱动器。 此外，不能使用外部 HDD 包装内的磁盘；请勿寄送外部 HDD。
> 
> 

以下是用于将数据复制到内部 HDD 的外部 USB 适配器列表。 Anker 68UPSATAA-02BU Anker 68UPSHHDS-BU Startech SATADOCK22UE Orico 6628SUS3-C-BK（6628 系列）Thermaltake BlacX Hot-Swap SATA 外部硬盘驱动器坞站（USB 2.0 和 eSATA）

### <a name="encryption"></a>加密
必须使用 BitLocker 驱动器加密对驱动器上的数据进行加密。 此加密会在运送过程中保护数据。

对于导入作业，可以通过两种方式进行加密。 第一种方式是在运行 WAImportExport 工具准备驱动器时，使用数据集 CSV 文件指定该选项。 第二种方式是在准备驱动器期间，在驱动器上手动启用 BitLocker 加密并在运行 WAImportExport 工具命令行时，在驱动集 CSV 文件中指定加密密钥。

对于导出作业，在将数据复制到驱动器以后，此服务会使用 BitLocker 加密驱动器，然后再将驱动器寄回给你。 加密密钥是通过 Azure 门户提供的。  

### <a name="operating-system"></a>操作系统
在将驱动器寄送到 Azure 之前，可以使用下述 64 位操作系统之一通过 WAImportExport 工具准备硬盘驱动器：

Windows 7 Enterprise、Windows 7 Ultimate、Windows 8 Pro、Windows 8 Enterprise、Windows 8.1 Pro、Windows 8.1 Enterprise、Windows 10<sup>1</sup>、Windows Server 2008 R2、Windows Server 2012、Windows Server 2012 R2。 所有这些操作系统都支持 BitLocker 驱动器加密。

### <a name="locations"></a>位置
Azure 导入/导出服务支持将数据复制到所有公共 Azure 存储帐户，以及从后者进行复制。 可以将硬盘驱动器寄送到列出的其中一个位置。 如果存储帐户所在的公共 Azure 位置没有在这里指定，则使用 Azure 门户或导入/导出 REST API 创建作业时，会提供备用的寄送位置。

支持的寄送位置：

* 美国东部
* 美国西部
* 美国东部 2
* 美国西部 2
* 美国中部
* 美国中北部
* 美国中南部
* 美国中西部
* 北欧
* 欧洲西部
* 东亚
* 东南亚
* 澳大利亚东部
* 澳大利亚东南部
* 日本西部
* 日本东部
* 印度中部
* 印度南部
* 印度西部
* 加拿大中部
* 加拿大东部
* 巴西南部
* 韩国中部
* 美国政府弗吉尼亚州
* 美国政府爱荷华州
* 美国 DoD 东部
* 美国 DoD 中部
* 中国东部
* 中国北部
* 英国南部
* 德国中部
* 德国东北部

### <a name="shipping"></a>寄送
**将驱动器寄送到数据中心：**

创建导入或导出作业时，会向你提供某个受支持位置的寄送地址，以便你寄送自己的驱动器。 提供的寄送地址取决于存储帐户的位置，但可能不同于存储帐户位置。

FedEx、UPS 或 DHL 可用于将驱动器寄送到寄送地址。

**从数据中心寄送驱动器：**

创建导入或导出作业时，必须提供回寄地址，以便 Microsoft 在作业完成后使用该地址将驱动器寄回给你。 请确保提供有效的回寄地址，以免延误对驱动器的处理。

该承运人商应提供相应的跟踪号来维护监护链。 必须提供有效的 FedEx、UPS 或 DHL 快递商帐号，以便 Microsoft 可以寄回驱动器。 必须有 FedEx、UPS 或 DHL 帐号，才能从美国和欧洲寄回驱动器。 将驱动器从亚洲和澳大利亚的各个位置寄回给你时，需要 DHL 帐户号码。 如果没有快递商帐户，可以创建一个 [FedEx](http://www.fedex.com/us/oadr/)（针对美国和欧洲）或 [DHL](http://www.dhl.com/)（针对亚洲和澳大利亚）快递商帐户。 如果已经有了一个快递商帐户号码，请确保其有效。

发运包裹时，必须遵循 [Microsoft Azure 服务条款](https://azure.microsoft.com/support/legal/services-terms/)中的条款。

> [!IMPORTANT]
> 请注意，发运的物理介质可能需要穿越国界。 应当负责确保物理介质和数据是遵照适用的法律导入和/或导出的。 在寄送物理介质之前，请咨询顾问以验证介质和数据是否可以合法地寄送到所确定的数据中心。 这有助于确保它可以及时到达 Microsoft。 例如，任何跨国界的包裹都需要附上商业发票（除非在欧盟内跨越国界）。 可从快递商网站打印填写好的商业发票的副本。 比如，商业发票可以是 [DHL 商业发票](http://invoice-template.com/wp-content/uploads/dhl-commercial-invoice-template.pdf)和 [FedEx 商业发票](http://images.fedex.com/downloads/shared/shipdocuments/blankforms/commercialinvoice.pdf)。 请确保 Microsoft 未被指定为导出者。
> 
> 

## <a name="how-does-the-azure-importexport-service-work"></a>Azure 导入/导出服务是如何工作的？
可以使用 Azure 导入/导出服务在本地站点和 Azure 存储之间传输数据，只需创建作业，然后将硬盘驱动器寄送到 Azure 数据中心即可。 寄送的每个硬盘驱动器都与单个作业相关联。 每个作业都与单个存储帐户相关联。 请仔细查看[先决条件](#pre-requisites)部分，以了解此服务的具体情况，例如支持的数据类型、磁盘类型、位置和寄送方式。

此部分介绍作业导入和导出操作中涉及的高级别步骤。 随后，将在[快速启动部分](#quick-start)提供分步说明，指导如何创建导入和导出作业。

### <a name="inside-an-import-job"></a>关于导入作业
概括而言，导入作业包括以下步骤：

* 确定要导入的数据，以及所需驱动器数目。
* 确定 Azure 存储中用于存储数据的目标 Blob 或文件位置。
* 使用 WAImportExport 工具将数据复制到一个或多个硬盘驱动器，并使用 BitLocker 进行加密。
* 使用 Azure 门户或导入/导出 REST API 在目标存储帐户中创建导入作业。 如果使用 Azure 门户，请上传驱动器日记文件。
* 请提供回寄地址以及快递商帐户号码，以便我们将驱动器寄回给你。
* 将硬盘驱动器寄送到在创建作业时获得的寄送地址。
* 在导入作业详细信息中更新快递跟踪号码，并提交导入作业。
* Azure 数据中心在收到驱动器后会对其进行处理。
* 该中心会使用快递商帐户将驱动器寄送到在导入作业中提供的回寄地址。
  
    ![图 1：导入作业流](./media/storage-import-export-service/importjob.png)

### <a name="inside-an-export-job"></a>关于导出作业
> [!IMPORTANT]
> 此服务仅支持导出 Azure Blob，不支持导出 Azure 文件。
> 
>

概括而言，导出作业包括以下步骤：

* 确定要导出的数据，以及所需驱动器数目。
* 确定数据在 Blob 存储中的源 Blob 或容器路径。
* 使用 Azure 门户或导入/导出 REST API 在源存储帐户中创建导出作业。
* 指定数据在导出作业中的源 Blob 或容器路径。
* 请提供回寄地址以及快递商帐户号码，以便我们将驱动器寄回给你。
* 将硬盘驱动器寄送到在创建作业时获得的寄送地址。
* 在导出作业详细信息中更新快递跟踪号码，并提交导出作业。
* Azure 数据中心在收到驱动器后会对其进行处理。
* 驱动器使用 BitLocker 加密；密钥通过 Azure 门户提供。  
* 该中心会使用快递商帐户将驱动器寄送到在导入作业中提供的回寄地址。
  
    ![图 2：导出作业流](./media/storage-import-export-service/exportjob.png)

### <a name="viewing-your-job-and-drive-status"></a>查看作业和驱动器状态
可以从 Azure 门户跟踪导入或导出作业的状态。 单击“导入/导出”选项卡。随即页面上显示作业列表。

![查看作业状态](./media/storage-import-export-service/jobstate.png)

会看到以下作业状态之一，具体取决于驱动器处于哪个处理阶段。

| 作业状态 | 说明 |
|:--- |:--- |
| Creating | 创建一个作业后，其状态设置为 Creating。 当作业处于 Creating 状态时，导入/导出服务假设驱动器尚未寄送到数据中心。 作业可保持 Creating 状态最多两周，之后服务会自动将其删除。 |
| 寄送 | 寄出包裹后，应在 Azure 门户中更新跟踪信息。  这会将作业转为“Shipping”状态。 作业将保持 Shipping 状态最多两周。 
| Received | 数据中心中收到所有驱动器后，作业状态将设置为 Received。 |
| 转移 | 至少已开始处理一个驱动器后，作业状态将设置为 Transferring。 有关详细信息，请参阅下面的“驱动器状态”部分。 |
| 打包 | 处理完所有驱动器后，作业将进入 Packaging 状态，直到将驱动器寄回给客户。 |
| 已完成 | 将所有驱动器寄回客户后，如果完成作业时未出现错误，该作业将设置为 Completed 状态。 作业在保持 Completed 状态 90 天后会自动删除。 |
| 已关闭 | 将所有驱动器寄回客户后，如果在处理作业期间出现过错误，该作业将设置为 Closed 状态。 作业在保持 Closed 状态 90 天后会自动删除。 |

下表描述了单个驱动器在导入或导出作业中转换时的生命周期。 现在，作业中每个驱动器的当前状态会在 Azure 门户中显示。
下表描述了每个驱动器在作业中可能经历的每种状态。

| 驱动器状态 | 说明 |
|:--- |:--- |
| Specified | 对于导入作业，在 Azure 门户中创建作业时，驱动器的初始状态为 Specified。 对于导出作业，由于在创建该作业时未指定驱动器，因此驱动器的初始状态为 Received。 |
| Received | 导入/导出服务操作员为导入作业处理从货运公司收到的驱动器后，驱动器将转换为 Received 状态。 对于导出作业，初始驱动器状态为 Received。 |
| NeverReceived | 当作业的包裹已送达但包裹不包含驱动器时，驱动器将转换为 NeverReceived 状态。 如果在服务收到发货信息后的两周内包裹未送达数据中心，驱动器也会转换为此状态。 |
| 转移 | 服务开始将数据从驱动器传输到 Microsoft Azure 存储时，驱动器将转换为 Transferring 状态。 |
| 已完成 | 服务成功传输所有数据且未出错时，驱动器将转换为 Completed 状态。
| CompletedMoreInfo | 如果服务在从驱动器中复制数据或将数据复制到驱动器时遇到一些问题，驱动器将转换为 CompletedMoreInfo 状态。 信息可以包含有关覆盖 Blob 的错误、警告或信息性消息。
| ShippedBack | 驱动器从数据中心寄送到回邮地址后，驱动器将转换为 ShippedBack 状态。 |

Azure 门户中的此映像会显示示例作业的驱动器状态：

![查看驱动器状态](./media/storage-import-export-service/drivestate.png)

下表描述了驱动器故障状态以及针对每种状态采取的措施。

| 驱动器状态 | 事件 | 解决方法/后续步骤 |
|:--- |:--- |:--- |
| NeverReceived | 标记为 NeverReceived 的驱动器（因为在作业寄送过程中未收到）通过另一次寄送送达。 | 运营团队将驱动器状态转换为 Received。 |
| 不适用 | 不属于任何作业的驱动器将作为其他作业的一部分送至数据中心。 | 完成与原始包裹关联的作业后，驱动器将标记为额外驱动器并寄回给客户。 |

### <a name="time-to-process-job"></a>处理作业的时间
处理导入/导出作业的时间各不相同，取决于不同的因素，例如寄送时间、作业类型、要复制的数据的类型和大小，以及所提供磁盘的大小。 导入/导出服务没有 SLA，但在收到磁盘之后，服务力求在 7 到 10 天内完成复制。 可以通过 REST API 更密切地跟踪作业进度。 在“列出作业”操作中有一个完成百分比参数，该参数指示复制进度。 如果需要估算何时才能完成时间要求紧的导入/导出作业，请联系我们。

### <a name="pricing"></a>定价
**驱动器处理费用**

在导入或导出作业的过程中，处理每个驱动器都需要支付驱动器处理费用。 请参阅有关 [Azure 导入/导出定价](https://azure.microsoft.com/pricing/details/storage-import-export/)的详细信息。

**寄送费用**

将驱动器寄送到 Azure 时，需要向快递商支付寄送费用。 当 Microsoft 将驱动器寄回给你时，寄送费用由你在创建作业时提供的快递商帐户支付。

**事务成本**

将数据导入 Azure 存储没有事务费用。 将数据从 Blob 存储导出时，需支付标准的传出费用。 有关事务费用的更多详细信息，请参阅[数据传输定价。](https://azure.microsoft.com/pricing/details/data-transfers/)



## <a name="how-to-import-data-into-azure-file-storage-using-internal-sata-hdds-and-ssds"></a>如何使用内部 SATA HDD 和 SSD 将数据导入 Azure 文件存储？
如果将磁盘上的数据导入 Azure 文件 存储，请执行以下步骤。
使用 Azure 导入/导出服务导入数据时，第一步是通过 WAImportExport 工具准备驱动器。 按照以下步骤准备驱动器。

1. 标识要导入 Azure 文件存储的数据。 导入的数据可以是本地服务器或网络共享中的目录和独立文件。  
2. 根据数据总大小确定所需驱动器数目。 采购所需数目的 2.5 英寸 SSD 或者 2.5/3.5 英寸 SATA II 或 III 硬盘驱动器。
4. 确定要复制到每个磁盘驱动器的目录和/或独立文件。
5. 为数据集和驱动器集创建 CSV 文件。
    
  以下是导入数据作为 Azure 文件的数据集 CSV 文件示例：
  
    ```
    BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
    "F:\50M_original\100M_1.csv.txt","fileshare/100M_1.csv.txt",file,rename,"None",None
    "F:\50M_original\","fileshare/",file,rename,"None",None 
    ```
   在上面的示例中，100M_1.csv.txt 将复制到“fileshare”的根目录中。 如果“Fileshare”不存在，将创建一个。 50M_original 下的所有文件和文件夹以递归方式复制到 fileshare。 文件夹结构保持不变。

    详细了解如何[准备数据集 CSV 文件](storage-import-export-tool-preparing-hard-drives-import.md#prepare-the-dataset-csv-file)。
    


    **驱动器集 CSV 文件**

    驱动器集标志的值是一个 CSV 文件，其中包含要使工具可以正确选择要准备的磁盘列表，要将驱动器号映射到的磁盘的列表。 

    下面是驱动器集 CSV 文件的示例：
    
    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631 |
    H,Format,SilentMode,Encrypt,
    ```

    在上面的示例中，假设附加了两个磁盘，并创建了盘符为 G:\ 和 H:\ 的基本 NFTS 卷。 工具将格式化并加密托管 H:\ 的磁盘，但不会格式化并加密托管卷 G:\ 的磁盘。

    详细了解如何[准备驱动器集 CSV 文件](storage-import-export-tool-preparing-hard-drives-import.md#prepare-initialdriveset-or-additionaldriveset-csv-file)。

6.  使用 [WAImportExport 工具](http://download.microsoft.com/download/3/6/B/36BFF22A-91C3-4DFC-8717-7567D37D64C5/WAImportExport.zip)将数据复制到一个或多个硬盘驱动器。
7.  可以通过在驱动器集 CSV 中的 Encryption 字段内指定“Encrypt”，在硬盘驱动器上启用 BitLocker 加密。 也可以手动在硬盘驱动器上启用 BitLocker 加密，并在运行工具时，在驱动器集 CSV 文件中指定“AlreadyEncrypted”并提供密钥。

8. 完成磁盘准备操作以后，请勿修改硬盘驱动器上的数据，也勿修改日志文件。

> [!IMPORTANT]
> 准备的每个硬盘驱动器都会生成一个日记文件。 使用 Azure 门户创建导入作业时，必须上传属于该导入作业的驱动器的所有日记文件。 不带日志文件的驱动器将得不到处理。
> 
>

下面是使用 WAImportExport 工具进行硬盘驱动器准备的命令和示例。

在第一个复制会话中使用新复制会话复制目录和/或文件的 WAImportExport 工具 PrepImport 命令：

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
```

**导入示例 1**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

若要**添加更多驱动器**，可以创建一个新的驱动器集文件并运行以下命令。 如果后续复制会话中的磁盘驱动器与 InitialDriveset .csv 中指定的不同，可以指定一个新的驱动器集 CSV 文件并将其作为值提供给“AdditionalDriveSet”参数。 使用**同一日记文件**的名称并提供**新的会话 ID**。 AdditionalDriveset CSV 文件的格式与 InitialDriveSet 的格式相同。

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
```

**导入示例 2**
```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
```

若要向同一个驱动器集添加更多数据，后续复制会话可以调用 WAImportExport 工具的 PrepImport 命令来复制其他文件/目录：对于 InitialDriveset .csv 文件中为相同硬盘驱动器指定的后续复制会话，请指定**同一日记文件**的名称并提供**新的会话 ID**；不需要提供存储帐户密钥。

```
WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
```

**导入示例 3**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

若要更详细了解如何使用 WAImportExport 工具，请参阅[为导入准备硬盘驱动器](storage-import-export-tool-preparing-hard-drives-import.md)。

另请参阅[为导入作业准备硬盘驱动器的示例工作流](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md)，以获取更详细的分步说明。  



## <a name="create-an-export-job"></a>创建导出作业
创建导出作业的目的是通知导入/导出服务：要将一个或多个空驱动器寄送到数据中心；这样数据中心就可以将数据从存储帐户导出到驱动器，并将驱动器寄送给用户。

### <a name="prepare-your-drives"></a>准备驱动器
对驱动器进行准备以便完成导出作业时，建议执行以下预检查：

1. 使用 WAImportExport 工具的 PreviewExport 命令检查所需的磁盘数。 有关详细信息，请参阅[预览导出作业的驱动器使用情况](https://msdn.microsoft.com/library/azure/dn722414.aspx)。 它可以根据要使用的驱动器大小，帮助你预览所选 Blob 的驱动器使用情况。
2. 请检查是否可以读取/写入要寄送的用于导出作业的硬盘驱动器。

### <a name="create-the-export-job"></a>创建导出作业
1. 若要创建导出作业，请在 Azure 门户上依次转到“更多服务”->“存储”->“导入/导出作业”。 单击“创建导入/导出作业”。
2. 在“第 1 步:基本信息”中，选择“从 Azure 导出”，输入作业名称字符串，选择订阅，输入或选择资源组。 输入导入作业的描述性名称。 请注意，输入的名称只能包含小写字母、数字、连字符和下划线，必须以字母开头并且不得包含空格。 在作业进行中以及作业完成后，将使用所选名称来跟踪作业。 输入此导出作业的负责人的联系信息。 

3. 在“第 2 步:作业详细信息”中，从存储帐户部分中选择要导出其中数据的存储帐户。 “放置位置”将根据选定存储帐户所属的区域自动进行填充。 指定要从存储帐户导出到空驱动器的 blob 数据。 可以选择导出该存储帐户中的所有 Blob 数据，也可以指定要导出的 Blob 或 Blob 组。
   
   若要指定要导出的 Blob，请使用“等于”选择器，并指定该 Blob 的相对路径，以容器名称开头。 使用 *$root* 指定根容器。
   
   若要指定以某一前缀开头的所有 Blob，请使用“开头为”选择器，并指定前缀，以正斜杠“/”开头。 该前缀可以是容器名称的前缀、完整容器名称或者后跟 Blob 名称前缀的完整容器名称。
   
   下表显示有效 Blob 路径的示例：
   
   | 选择器 | Blob 路径 | 说明 |
   | --- | --- | --- |
   | 开头为 |/ |导出存储帐户中的所有 Blob |
   | 开头为 |/$root/ |导出根容器中的所有 Blob |
   | 开头为 |/book |导出任何容器中以前缀 **book** 开头的所有 Blob |
   | 开头为 |/music/ |导出容器 **music** 中的所有 Blob |
   | 开头为 |/music/love |导出容器 **music** 中以前缀 **love** 开头的所有 Blob |
   | 等于 |$root/logo.bmp |导出根容器中的 Blob **logo.bmp** |
   | 等于 |videos/story.mp4 |导出容器 **videos** 中的 Blob **story.mp4** |
   
   必须以有效格式提供 Blob 路径，以免在处理过程中出现错误，如以下屏幕快照所示。
   
   ![创建导出作业 - 步骤 3](./media/storage-import-export-service/export-job-03.png)

4. 在“第 3 步:回寄信息”中，从下拉列表中选择快递商，并输入已通过此快递商创建的有效快递商帐号。 当导入作业完成后，Microsoft 将使用此帐户寄回驱动器。 输入完整、有效的联系人姓名、电话号码、电子邮件地址、街道地址、城市、邮政编码、州/省/自治区/直辖市和国家/地区。
   
 5. 在“摘要页”中，输入 Azure 数据中心寄送地址是为了将磁盘寄送到 Azure DC。 请确保寄送标签上标明了作业名称和完整地址。 

6. 单击“摘要页”上的“确定”，完成“创建导入作业”

7. 寄送磁盘后，返回到 Azure 门户上的“导入/导出”页，再执行以下操作：a) 转到并单击导入作业；b) 单击“驱动器寄送后，更新作业状态和跟踪信息”； 
     c) 选中“标记为‘已寄送’”复选框；d) 输入“快递商”和“跟踪号码”。
    
   如果在创建作业后的 2 周内未更新跟踪号，该作业会过期。
   
8. 可以在门户仪表板上跟踪作业进度。 若要了解上一部分中每个作业状态的含义，请[查看作业状态](#viewing-your-job-status)。

   > [!NOTE]
   > 如果在复制到硬盘时要导出的 Blob 正在使用中，Azure 导入/导出服务将生成该 Blob 的快照，然后复制快照。
   > 
   > 
 
9. 收到包含已导出数据的驱动器以后，即可查看和复制该服务为驱动器生成的 BitLocker 密钥。 在 Azure 门户中，转到导出作业，再单击“导入/导出”选项卡。从列表中选择导出作业，再单击“BitLocker 密钥”选项。 BitLocker 密钥随即出现，如下所示：
   
   ![查看导出作业的 BitLocker 密钥](./media/storage-import-export-service/export-job-bitlocker-keys.png)

请查看下面的“常见问题”部分，因为该部分介绍了客户在使用此服务时遇到的最常见问题。

## <a name="frequently-asked-questions"></a>常见问题

**能否使用 Azure 导入/导出服务复制 Azure 文件存储？**

可以，Azure 导入/导出服务支持导入到 Azure 文件存储。 目前它不支持导出 Azure 文件。

**Azure 导入/导出服务是否适用于 CSP 订阅？**

Azure 导入/导出服务支持 CSP 订阅。

**能否跳过导入作业的驱动器准备步骤？能否在不复制的情况下准备驱动器？**

任何需要寄送到数据中心进行数据导入的驱动器都必须使用 WAImportExport 工具进行准备。 必须使用 WAImportExport 工具将数据复制到驱动器。

**在创建导出作业时我是否需要执行任何磁盘准备操作？**

不需要，但建议执行一些预先检查。 使用 WAImportExport 工具的 PreviewExport 命令检查所需的磁盘数。 有关详细信息，请参阅[预览导出作业的驱动器使用情况](https://msdn.microsoft.com/library/azure/dn722414.aspx)。 它可以根据要使用的驱动器大小，帮助你预览所选 Blob 的驱动器使用情况。 此外，请检查是否可以读取和写入要寄送的用于导出作业的硬盘驱动器。

**如果我无意中发送了不符合支持的要求的 HDD，会发生什么情况？**

Azure 数据中心会将不符合支持要求的驱动器返还给你。 如果包裹中只有某些驱动器满足支持要求，将处理这些驱动器，并且不符合支持的要求的驱动器将返还给你。

**是否可以取消我的作业？**

可以取消状态为“创建”或“装运”的作业。

**在 Azure 门户中可以查看多长时间的已完成作业的状态？**

可以查看最长 90 天的已完成作业的状态。 已完成作业会在 90 天后被删除。

**如果我想要导入或导出超过 10 个驱动器，我应该做什么？**

对于导入/导出服务，一个导入或导出作业在单个作业中只能引用 10 个驱动器。 如果想要装运超过 10 个驱动器，可以创建多个作业。 与同一作业关联的驱动器必须放在同一个包裹中一起寄送。
数据容量跨多个磁盘导入作业时，Microsoft 可提供指导和帮助。 有关详细信息，请联系 bulkimport@microsoft.com

**该服务是否会在返还驱动器之前将其格式化？**

不会。 所有驱动器都使用 BitLocker 加密。

**我是否可为导入/导出作业从 Microsoft 购买驱动器？**

不会。 对于导入和导出作业，用户将需要装运自己的驱动器。

**如何才能访问此服务导入的数据？**

以 Azure 存储帐户名义存储的数据可以通过 Azure 门户访问，也可以使用存储资源管理器这一独立工具进行访问。 https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer 

**导入作业完成后，我的数据在存储帐户中看起来是什么样的？是否会保留我的目录层次结构？**

为导入作业准备硬盘驱动器时，目标由数据集 CSV 文件中的 DstBlobPathOrPrefix 字段指定。 该目标是存储帐户中的目标容器，可以将硬盘驱动器中的数据复制到其中。 在该目标容器中，将为硬盘驱动器中的文件夹创建虚拟目录，为文件创建 Blob。 

**如果驱动器的文件已存在于我的存储帐户中，该服务是否会覆盖我的存储帐户中的现有 Blob 或文件？**

准备驱动器时，可以使用数据集 CSV 文件中名为 /Disposition:<rename|no-overwrite|overwrite> 的字段指定是否应覆盖或忽略目标文件。 默认情况下，该服务会将新文件重命名，而不是覆盖现有 Blob 或文件。

**WAImportExport 工具是否与 32 位操作系统兼容？**
不是。 WAImportExport 工具仅兼容 64 位 Windows 操作系统。 有关受支持的 OS 版本的完整列表，请参阅[先决条件](#pre-requisites)中的“操作系统”部分。

**是否应该在包裹中添加除硬盘驱动器之外的其他东西？**

请仅发运硬盘驱动器。 不要包括电源线或 USB 电缆之类的物品。

**是否必须通过 FedEx 或 DHL 寄送我的驱动器？**

可以通过任何知名的快递商（例如 FedEx、DHL、UPS 或美国邮政总局）将驱动器寄送到数据中心。 但是，如果在美国和欧洲，则必须提供 FedEx 帐户号码，如果在亚洲和澳大利亚地区，则必须提供 DHL 帐户号码，以便数据中心将驱动器寄回给你。

**跨国寄送驱动器是否存在限制？**

请注意，发运的物理介质可能需要穿越国界。 应当负责确保物理介质和数据是遵照适用的法律导入和/或导出的。 在发运物理介质之前，请咨询顾问以验证介质和数据是否可以合法地发运到所确定的数据中心。 这会有助于确保它可以及时到达 Microsoft。

**创建作业时，寄送地址是一个不同于存储帐户位置的位置。我该怎样做？**

某些存储帐户位置映射到备用寄送位置。 此前可用的寄送位置也可临时映射到备用位置。 在寄送驱动器之前，请始终查看在创建作业过程中提供的寄送地址。

**寄送我的驱动器时，快递商要求我提供数据中心的联系地址和电话号码。我该如何提供？**

电话号码和数据中心地址作为作业创建的一部分提供。

**能否使用 Azure 导入/导出服务将 PST 邮箱和 SharePoint 数据复制到 O365？**

请参阅[将 PST 文件或 SharePoint 数据导入到 Office 365](https://technet.microsoft.com/library/ms.o365.cc.ingestionhelp.aspx)。

**能否使用 Azure 导入/导出服务将我的备份脱机复制到 Azure 备份服务？**

请参阅 [Azure 备份中的脱机备份工作流](../../backup/backup-azure-backup-import-export.md)。

**一次装运的最大 HDD数量是多少？**

一次装运中可以有任意数量的 HDD，如果磁盘属于多个作业，则建议 a) 将磁盘标记上相应的作业名称。 b) 使用后缀为 -1、-2 等的跟踪号码更新作业。
  
**磁盘导入/导出支持的最大块 Blob 和页 Blob 大小是多少？**

最大块 Blob 大小大约为 4.768TB 或 5,000,000 MB。
最大页 Blob 大小为 1TB。

**磁盘导入/导出是否支持 AES 256 加密？**

默认情况下，Azure 导入/导出服务使用 AES 128 BitLocker 加密进行加密，但在复制数据前，可以使用 BitLocker 进行手动加密，从而将加密提升为 AES 256。 

如果使用的是 [WAImportExpot V1](http://download.microsoft.com/download/0/C/D/0CD6ABA7-024F-4202-91A0-CE2656DCE413/WaImportExportV1.zip)，下面展示了示例命令
```
WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>] 
```
如果使用的是 [WAImportExport 工具](http://download.microsoft.com/download/3/6/B/36BFF22A-91C3-4DFC-8717-7567D37D64C5/WAImportExport.zip)，请指定“AlreadyEncrypted”，并在驱动集 CSV 中提供密钥。
```
DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631 |
```
## <a name="next-steps"></a>后续步骤

* [设置 WAImportExport 工具](storage-import-export-tool-how-to.md)
* [使用 AzCopy 命令行实用程序传输数据](storage-use-azcopy.md)
* [Azure 导入/导出 REST API 示例](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)

