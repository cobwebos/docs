---
title: 从 Azure Data Box 导出数据的教程 |Microsoft Docs
description: 了解部署先决条件以及如何从 Azure Data Box 导出数据
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: how-to
ms.date: 09/10/2020
ms.author: alkohli
ms.openlocfilehash: 5c3f87620c8a2a2d2438d7a5630541c0f76f9f17
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91575565"
---
# <a name="tutorial-create-export-order-for-azure-data-box-preview"></a>教程：创建 Azure Data Box (预览版的出口订单) 

Azure Data Box 是一种混合解决方案，可让你将 Azure 中的数据移到你的位置。 本教程介绍如何创建 Azure Data Box 的出口订单。 创建导出顺序的主要原因是为了进行灾难恢复，并且在本地存储区被泄露并且需要还原备份。

本教程的介绍内容包括：

> [!div class="checklist"]
>
> * 导出的先决条件
> * 为导出 Data Box 排序
> * 跟踪导出顺序
> * 取消导出顺序

[!INCLUDE [Data Box feature is in preview](../../includes/data-box-feature-is-preview-info.md)]

## <a name="prerequisites"></a>必备条件

对设备进行排序之前，请先完成 Data Box 服务和设备的以下配置先决条件。

### <a name="for-service"></a>对于服务

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

* 请确保现有的资源组可用于 Azure Data Box。

* 请确保要从中导出数据的 Azure 存储帐户是受支持的存储帐户类型中的一种，如 [Data Box](data-box-system-requirements.md#supported-storage-accounts)所述。

### <a name="for-device"></a>对于设备

在开始之前，请确保：

* 应该将一个主机连接到数据中心网络。 将 Azure Data Box 中的数据复制到此计算机。 主机必须按照 [Azure Data Box 系统要求](data-box-system-requirements.md)中的说明运行支持的操作系统。

* 数据中心需要有高速网络。 强烈建议你至少建立一个 10-GbE 连接。 如果 10-GbE 连接不可用，则可使用 1-GbE 数据链路，但复制速度会受影响。

## <a name="order-data-box-for-export"></a>导出 Data Box 顺序

在 Azure 门户中执行以下步骤以订购设备。

1. 使用 Microsoft Azure 凭据在以下 URL 登录：[https://portal.azure.com](https://portal.azure.com)。

2. 选择“+ 创建资源”并搜索 *Azure Data Box*。 选择“Azure Data Box”。

   ![创建资源](media/data-box-deploy-export-ordered/azure-data-box-export-00b.png)

3. 选择“创建”。

   ![创建 Azure Data Box](media/data-box-deploy-export-ordered/azure-data-box-export-00c.png)

4. 检查 Azure Data Box 服务是否在你的区域中可用。 输入或选择以下信息，然后选择“应用”。

    |设置  |值  |
    |---------|---------|
    |传输类型     | 选择 " **导出到 Azure**"。        |
    |订阅     | 对于 Data Box 服务，选择一个 EA、CSP 或 Azure 赞助订阅。 <br> 该订阅将链接到计费帐户。       |
    |资源组     |    选择现有资源组。 <br> 资源组是可以统一管理或部署的资源的逻辑容器。         |
    |源 Azure 区域    |    选择数据当前所在的 Azure 区域。         |
    |目标国家/地区     |     选择要将设备寄送到的国家/地区。        |

   ![选择 Data Box 设置](media/data-box-deploy-export-ordered/azure-data-box-export-01.png)

5. 选择“Data Box”。 单次订购的可用最大容量为 80 TB。 可以创建多个订单，以增加数据大小。

   ![选择 Data Box 容量](media/data-box-deploy-export-ordered/azure-data-box-export-02b.png)

6. 在 " **顺序**" 中，指定 **基本** 订单详细信息。 输入或选择以下信息，然后选择“下一步”。

    |设置  |值  |
    |---------|---------|
    |订阅     | 系统会根据前面所做的选择自动填充此订阅。|
    |资源组 | 之前选择的资源组。 |
    |导出顺序名称     |  提供友好名称用于跟踪订单。 <br> 名称可以为 3 到 24 个字符，包括字母、数字和连字符。 <br> 名称必须以字母或数字开头和结尾。      |

    ![出口订单基础知识](media/data-box-deploy-export-ordered/azure-data-box-export-03.png)

    选择 **下一步：** 要继续的数据选择。

7. 在 **数据选择**中，选择 " **添加存储帐户" 和 "导出类型**"。

    ![添加存储帐户和导出类型](media/data-box-deploy-export-ordered/azure-data-box-export-03b.png)

8. 在 " **选择导出选项**" 中，指定导出选项的详细信息。 输入或选择以下信息，然后选择 " **添加**"。

    |设置  |值  |
    |---------|---------|
    |存储帐户     | 要从中导出数据的 Azure 存储帐户。 |
    |导出类型     | 指定要从 **所有对象** 导出并 **使用 XML 文件**的数据类型。<ul><li> **所有对象** -指定作业根据你选择的 **传输选项**导出所有数据。</li><li> **使用 xml 文件** –指定一个 XML 文件，其中包含要从存储帐户导出的 blob 和/或文件的一组路径和前缀。 该 XML 文件需要位于所选存储帐户的容器中，并且当前不支持从文件共享中进行选择。 文件必须为非空 .xml 文件。</li></ul>        |
    |传输选项     |  指定 "全 **选**"、" **所有 blob**" 和 " **所有文件**" 中的数据传输选项。 <ul><li> **全选** -指定导出所有 Blob 和 Azure 文件。 如果你使用的存储帐户仅支持 blob (Blob 存储帐户) ，则 " **所有文件** " 选项将不可选择。</li><li> **所有 blob** -指定仅导出块和页 blob。</li><li> **所有文件** -指定导出所有文件时不包括 blob。 已 (GPv1 和 GPv2、高级存储或 blob 存储的存储帐户类型) 确定可导出的数据类型。 有关详细信息，请参阅 [支持的存储帐户导出](../storage/common/storage-import-export-requirements.md#supported-storage-types)。</li></ul>         |
    |包括详细日志     | 指示是否需要一个详细日志文件，其中包含已成功导出的所有文件的列表。        |

    > [!NOTE]
    >
    > 如果选择 " **将 xml 文件** 用于 **导出类型** " 设置，则需要确保 XML 包含有效路径和/或前缀。 必须构造和提供 XML 文件。  如果文件无效或没有任何数据与指定的路径相匹配，则该顺序将以部分数据终止或未导出任何数据。

    若要查看如何将 XML 文件添加到容器，请参阅 [使用 XML 文件导出顺序](data-box-deploy-export-ordered.md#export-order-using-xml-file)。

   ![选择导出选项](media/data-box-deploy-export-ordered/azure-data-box-export-04b.png)

   若要查看 xml 输入示例，请参阅 [示例 xml 输入](data-box-deploy-export-ordered.md#sample-xml-file)

9. 在 **数据选择**中，查看设置，然后选择 " **下一步：安全>**。

   ![联系人详细信息](media/data-box-deploy-export-ordered/azure-data-box-export-05.png)

1. 在“安全性”中，如果要启用基于软件的双重加密，请选择“为订单启用双重加密” 。 

   除了对 Data Box 上的数据进行 AES-256 位加密，还可执行基于软件的加密。

   > [!NOTE]
   > 启用此选项可能会导致订单处理和数据复制耗时较长。 创建订单后，不能更改此选项。

   ![Data Box 导入双重加密的安全性屏幕](media/data-box-deploy-export-ordered/azure-data-box-export-05b.png)

   在完成时选择“下一步:联系人详细信息”以继续。

10. 在 " **联系人详细信息**" 中，选择 " **+ 添加送货地址** " 以输入寄送信息。

    ![添加送货地址](media/data-box-deploy-export-ordered/azure-data-box-export-06.png)

11. 在 " **添加送货地址**" 中，提供你的名字和姓氏、公司名称和邮寄地址以及有效的电话号码。 选择“验证”。 服务将验证寄送地址，以确定服务是否在该区域可用。 如果服务在指定的寄送地址可用，则会将结果通知给你。

    ![验证送货地址](media/data-box-deploy-export-ordered/azure-data-box-export-07.png)

    如果要在可自行管理的装运可用的区域中进行排序，则可以选择此选项。 有关自托管寄送的详细信息，请参阅[使用自托管寄送](data-box-portal-customer-managed-shipping.md)。

12. 成功验证发货详细信息后，选择 " **添加寄送地址** "。

13. 在 " **联系人详细信息**" 中，查看您的发货地址和电子邮件地址。 服务会将有关任何订单状态更新的电子邮件通知发送到指定的电子邮件地址。

    我们建议使用组电子邮件，以便在组中的管理员离任后，可以持续收到通知。

    ![订单详细信息](media/data-box-deploy-export-ordered/azure-data-box-export-09.png)

14. 选择 **下一步：查看 + 订单>**。 必须接受条款和条件才能继续创建订单。

15. 选择“订单”。 创建订单需要几分钟时间。

    ![提交顺序](media/data-box-deploy-export-ordered/azure-data-box-export-10.png)

## <a name="export-order-using-xml-file"></a>使用 XML 文件导出订单

如果选择 " **使用 XML 文件**"，则可以指定要导出的 (页面和块) 的特定容器和 blob。 若要设置 XML 格式，需遵循 [示例 xml 文件表](#sample-xml-file) 规范。 下面的步骤演示了如何使用 XML 文件导出数据：

1. 对于 " **导出类型**"，请选择 " **使用 XML 文件**"。 这是 XML 文件，用于指定要导出的特定 blob 和 Azure 文件。 若要添加 XML 文件，请选择 " **单击此处以选择 xml 文件**"。
     ![选择导出选项的屏幕截图，单击此处选择一个称为 "X M L 文件" 的文件选项。](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-01.png)

2. 选择 " **+ 容器** " 以创建容器。
    ![容器部分的屏幕截图。](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-02.png)

3. 在从 Azure 门户右侧弹出的 " **新建容器** " 选项卡中，为容器添加一个名称。 名称必须为小写，并且可以包含数字和短划线 "-"。 然后从下拉列表框中选择 " **公共访问级别** "。 建议选择 " **专用 (非匿名访问) ** "，以防止其他人访问数据。 有关容器访问级别的详细信息，请参阅 [容器访问权限](../storage/blobs/storage-manage-access-to-resources.md#grant-anonymous-users-permissions-to-containers-and-blobs)。

   ![新 "容器" 选项卡的屏幕截图，其中显示 "专用 (没有选择匿名访问) " 选项。](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-04.png)

4. 选择“创建”。

   !["新建容器" 选项卡的屏幕截图，其中包含 "创建" 选项。](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-07.png)

   如果已成功创建容器，你将收到以下消息：

   ![显示 "已成功创建存储容器" 的消息的屏幕截图。](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-09.png)

5. 选择所创建的容器，并双击它。

   !["容器" 部分的屏幕截图，其中的私有测试容器容器称为 "out"。](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-08.png)

6. 双击容器将打开容器属性视图。 现在想要附加 (或浏览) 包含要导出的 blob 和/或 Azure 文件列表的 XML 文件。 选择“上传”。

   !["上传 blob" 对话框的屏幕截图，其中包含一个称为 "上载" 选项。](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-10c.png)

7. 已成功将 XML 文件添加到容器中。 将仅导出在此 XML 中指定的 blob 和 Azure 文件。

   !["订单向导" 的屏幕截图，其中包含下一个 "安全" 选项。](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-12.png)

## <a name="track-the-order"></a>跟踪订单

下单后，可以从 Azure 门户跟踪订单状态。 转到你的 Data Box 订单，然后转到“概述”来查看状态。 门户中会显示订单处于“已订购”状态。

设备准备完成后，将从所选存储帐户开始数据复制。 门户会显示 " **数据复制正在进行** " 状态的顺序。

![已处理 Data Box 导出顺序](media/data-box-deploy-export-ordered/azure-data-box-export-15b.png)

Data Box) 中的源存储 (帐户复制数据。 数据复制完成后，Data Box 被锁定，门户将显示 " **复制已完成** " 状态的顺序。

![Data Box 导出数据复制完成](media/data-box-deploy-export-ordered/azure-data-box-export-15c.png)

如果设备不可用，你将收到通知。 如果设备有货，Microsoft 会确定要发货的设备，并准备发货。 在设备准备过程中，将执行以下操作：

* 将为与设备关联的每个存储帐户创建 SMB 共享。
* 将为每个共享生成访问凭据（例如用户名和密码）。
* 设备已锁定，只能使用设备解锁密码进行访问。 若要检索密码，需要登录到 Azure 门户帐户并选择 " **设备详细信息**"。

然后，Microsoft 通过区域运营商准备并调度你的设备。 设备发货后，你会收到跟踪号。 门户会显示订单处于“已发运”状态。

![已调度 Data Box 出口订单](media/data-box-deploy-export-ordered/azure-data-box-export-16.png)

如果选择了 "自行管理的装运"，则将在设备准备好从数据中心选取时收到电子邮件通知，其中包含后续步骤。 有关与自行管理的发货相关的详细信息，请参阅 [自行管理发货](data-box-portal-customer-managed-shipping.md)。

![自助管理发货准备就绪](media/data-box-deploy-export-ordered/azure-data-box-export-17.png)

## <a name="cancel-the-order"></a>取消订单

若要取消此订单，请在 Azure 门户中转到“概览”，然后在命令栏中选择“取消” 。

下订单后，您可以在订单开始处理之前的任何时间点将其取消。

若要删除已取消的订单，请转到“概况”，然后在命令栏中选择“删除” 。

## <a name="sample-xml-file"></a>示例 XML 文件

下面的 xml 显示了在你选择 " **使用 xml 文件** " 选项时，导出顺序使用的 xml 格式的 blob 名称、blob 前缀和 Azure 文件的示例：

```xml
<?xml version="1.0" encoding="utf-8"?>
   <!-- BlobList/prefix/Container list for Blob storage for export  -->
   <BlobList>
      <BlobPath>/8tbpageblob/8tbpageblob/8tbpageblob</BlobPath>
      <BlobPathPrefix>/blockblob4dot75tbdata/</BlobPathPrefix>
      <BlobPathPrefix>/1tbfilepageblob</BlobPathPrefix>
      <BlobPathPrefix>/1tbfile/</BlobPathPrefix>
      <BlobPathPrefix>/8mbfiles/</BlobPathPrefix>
      <BlobPathPrefix>/64mbfiles/</BlobPathPrefix>
   </BlobList>
   <!-- FileList/prefix/Share list for Azure File storage for export  -->
   <AzureFileList>
      <FilePathPrefix>/64mbfiles/</FilePathPrefix>
      <FilePathPrefix>/4mbfiles/prefix2/subprefix</FilePathPrefix>
      <FilePathPrefix>/1tbfile/prefix</FilePathPrefix>
   </AzureFileList>
```

有关 xml 文件的一些要点：

* Xml 标记区分大小写，并且需要完全匹配以上示例中指定的内容。
* 开始和结束标记必须匹配。
* Xml 标记或格式不正确可能会导致数据导出失败。
* 如果 blob 和/或文件前缀无效，则不会导出任何数据。

### <a name="examples-of-valid-blob-paths"></a>有效 blob 路径示例

下表显示有效 Blob 路径的示例：

   | 选择器 | Blob 路径 | 说明 |
   | --- | --- | --- |
   | 开头为 |/ |导出存储帐户中的所有 Blob |
   | 开头为 |/$root/ |导出根容器中的所有 Blob |
   | 开头为 |/containers |导出任何容器中以前缀**容器**开头的所有 blob |
   | 开头为 |/container-name/ |导出容器容器中的所有 blob **-名称** |
   | 开头为 |/container-name/prefix |导出容器容器中的所有 blob **-名称**以前缀**前缀**开头 |
   | 等于 |$root/logo.bmp |导出根容器中的 Blob logo.bmp |
   | 等于 |8tbpageblob/mydata.txt |导出容器**8tbpageblob**中的 blob **mydata.txt** |

## <a name="sample-log-files"></a>示例日志文件

本部分提供在导出过程中生成的示例日志文件。 错误日志会自动生成。 若要生成详细日志文件，需要在配置导出顺序时选择 "在 Azure 门户中 **包括详细日志** 。
有关复制日志和详细日志的详细信息，请参阅 [复制日志](data-box-deploy-export-copy-data.md#copy-data-from-data-box)。

<!-- ### Verbose log

The following log files show examples of verbose logging when you select **Include verbose log**:

```xml
<File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3.4" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/helloEndWithDot..txt" Size="11" crc64="7320094093915972193"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/test..txt" Size="12" crc64="17906086011702236012"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/test1" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/.......txt" Size="11" crc64="7320094093915972193"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/copylogb08fa3095564421bb550d775fff143ed====..txt" Size="53638" crc64="1147139997367113454"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/testmaxChars-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-12345679" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file0" Size="0" crc64="0"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file1" Size="0" crc64="0"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000001" Size="4096" crc64="16969371397892565512"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000000" Size="4096" crc64="16969371397892565512"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed10.dat" Size="65536" crc64="10746682179555216785"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/LiveSiteReport_Oct.xlsx" Size="7028" crc64="6103506546789189963"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/NE_Oct_GeoReport.xlsx" Size="103197" crc64="13305485882546035852"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed1.dat" Size="65536" crc64="3140622834011462581"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-0" Size="1048576" crc64="16086591317856295272"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000001" Size="524288" crc64="8908547729214703832"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-0" Size="4194304" crc64="1339017920798612765"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000000" Size="524288" crc64="8908547729214703832"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-1" Size="8388608" crc64="3963298606737216548"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-1" Size="1048576" crc64="11061759121415905887"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/XLS-10MB.xls" Size="1199104" crc64="2218419493992437463"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-0" Size="8388608" crc64="1072783424245035917"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-1" Size="4194304" crc64="9991307204216370812"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/VL_Piracy_Negtive10_TPNameAndGCS.xlsx" Size="12398699" crc64="13526033021067702820"></File>
```

### Copy logs

For more information regarding copy logs, see [Copy logs](data-box-deploy-export-copy-data.md#copy-data-from-data-box). -->

<!-- The following xml shows an example of the copy log when the export is successful:

```xml
<CopyLog Summary="Summary">
  <Status>Succeeded</Status>
    <TotalFiles_Blobs>27</TotalFiles_Blobs>
    <FilesErrored>0</FilesErrored>
</CopyLog>
```

For more information regarding copy logs, see [Copy logs](data-box-deploy-export-copy-data.md#copy-data-from-data-box).

The following xml shows an example of the copy log when the export has errors:

```xml
<ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/wastorage.v140.3.0.2.nupkg">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444.hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444 (1).hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>4</TotalFiles_Blobs>
  <FilesErrored>3</FilesErrored>
</CopyLog>
``` -->

## <a name="next-steps"></a>后续步骤

本教程介绍了有关 Azure Data Box 的主题，例如：

> [!div class="checklist"]
>
> * 导出的先决条件
> * 为导出 Data Box 排序
> * 跟踪导出顺序
> * 取消导出顺序

请继续学习下一篇教程，了解如何设置 Data Box。

> [!div class="nextstepaction"]
> [设置 Azure Data Box](./data-box-deploy-set-up.md)
