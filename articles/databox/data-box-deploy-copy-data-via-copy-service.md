---
title: 通过 SMB 将数据复制到 Microsoft Azure Data Box | Microsoft Docs
description: 了解如何通过数据复制服务将数据复制到 Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 01/10/2019
ms.author: alkohli
ms.openlocfilehash: a71635abd036bb89546dd3421af97cd9b88f4327
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2019
ms.locfileid: "54439885"
---
# <a name="tutorial-use-data-copy-service-to-directly-ingest-data-into-azure-data-box-preview"></a>教程：使用数据复制服务直接将数据引入 Azure Data Box（预览版）

本教程介绍如何在不使用中间主机的情况下，使用数据复制服务引入数据。 数据复制服务在 Data Box 本地运行，通过 SMB 连接到 NAS 设备，并将数据复制到 Data Box。

数据复制服务：

- 可在中间主机不可用的网络连接存储 (NAS) 环境中使用。
- 可用于复制需要花费数周时间来引入和上传其中数据的小型文件。 此服务能够大幅缩减引入和上传时间。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 将数据复制到 Data Box
> * 准备交付 Data Box。

## <a name="prerequisites"></a>先决条件

在开始之前，请确保：

1. 已完成[教程：设置 Azure Data Box](data-box-deploy-set-up.md)。
2. 已收到 Data Box，并且门户中的订单状态为“已送达”。
3. 具有复制数据时要连接的源 NAS 设备的凭据。
4. 已连接到高速网络。 强烈建议你至少建立一个 10-GbE 连接。 如果 10-GbE 连接不可用，可以使用 1-GbE 数据链路，但复制速度会受影响。

## <a name="copy-data-to-data-box"></a>将数据复制到 Data Box

连接到 NAS 之后，下一步是复制数据。 在开始复制数据之前，请查看以下注意事项：

- 复制数据时，请确保数据大小符合 [Azure 存储和 Data Box 限制](data-box-limits.md)中所述的大小限制。
- 如果 Data Box 上传的数据同时已由 Data Box 外部的其他应用程序上传，则可能会导致上传作业失败和数据损坏。
- 如果数据复制服务读取数据时数据正在变动，则可能会出现失败或数据损坏的情况。

若要使用数据复制服务复制数据，需要创建一个作业。 遵循以下步骤创建用于复制数据的作业。

1. 在 Data Box 的本地 Web UI 中，转到“管理”>“复制数据”。
2. 在“复制数据”页上，单击“创建”。

    ![在“复制数据”页上单击“创建”](media/data-box-deploy-copy-data-via-copy-service/click-create.png)

3. 在“配置并启动”对话框中提供以下输入。
    
    |字段                          |值    |
    |-------------------------------|---------|
    |作业名称                       |作业的唯一名称，少于 230 个字符。 作业名称中不允许以下字符 - \<、\>、\|、\?、\*、\\、\:、\/ 和 \\\.         |
    |源位置                |使用 `\\<ServerIPAddress>\<ShareName>` 或 `\\<ServerName>\<ShareName>` 格式提供数据源的 SMB 路径。        |
    |用户名                       |用于访问数据源的用户名。        |
    |密码                       |用于访问数据源的密码。           |
    |目标存储帐户    |从下拉列表中选择要将数据上传到的目标存储帐户。         |
    |目标存储类型       |选择目标存储类型：块 Blob、页 Blob 或 Azure 文件。        |
    |目标容器/共享    |输入目标存储帐户中要将数据上传到的容器或共享的名称。 该名称可以是共享名称或容器名称。 例如 `myshare` 或 `mycontainer`。 也可以在云中以 `sharename\directory_name` 或 `containername\virtual_directory_name` 格式输入名称。        |
    |复制文件匹配模式    | 按以下两种方式输入文件名匹配模式。<ul><li>**使用通配符表达式**。通配符表达式中仅支持 `*` 和 `?`。 例如，表达式 `*.vhd` 匹配扩展名为 .vhd 的所有文件。 同理，`*.dl?` 匹配扩展名为 `.dl` 或 `.dll` 的所有文件。 此外，`*foo` 匹配名称以 `foo` 结尾的所有文件。<br>可以直接在该字段中输入通配符表达式。 默认情况下，在该字段中输入的值被视为通配符表达式。</li><li>**使用正则表达式** - 支持基于 POSIX 的正则表达式。 例如，正则表达式 `.*\.vhd` 匹配扩展名为 `.vhd` 的所有文件。 对于正则表达式，请直接提供 `<pattern>` 作为 `regex(<pattern>)`。 <li>有关正则表达式的详细信息，请转到[正则表达式语言 - 快速参考](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference)。</li><ul>|
    |文件优化              |启用此功能后，引入时会打包文件。 这样可以加快小型文件的数据复制速度。        |
 
4. 单击“启动”。 随后会验证输入，如果验证成功，则启动某个作业。 启动该作业可能需要花费几分钟时间。

    ![从“配置作业并启动”对话框启动作业](media/data-box-deploy-copy-data-via-copy-service/configure-and-start.png)

5. 将创建一个使用指定设置的作业。 选中相应的复选框，然后可以暂停、恢复、取消或重启作业。

    ![通过“复制数据”页管理作业](media/data-box-deploy-copy-data-via-copy-service/select-job.png)
    
    - 如果此作业在高峰期会影响 NAS 资源，可将其暂停。

        ![暂停作业](media/data-box-deploy-copy-data-via-copy-service/pause-job.png)

        进入非高峰期后，可以恢复作业。

        ![恢复作业](media/data-box-deploy-copy-data-via-copy-service/resume-job.png)

    - 随时可以取消作业。

        ![取消作业](media/data-box-deploy-copy-data-via-copy-service/cancel-job.png)取消作业时需要确认。

        ![确认取消作业](media/data-box-deploy-copy-data-via-copy-service/confirm-cancel-job.png)

        如果决定取消作业，已复制的数据不会删除。 若要删除已在 Data Box 中复制的所有数据，请重置设备。

        ![重置设备](media/data-box-deploy-copy-data-via-copy-service/reset-device.png)

        >[!NOTE]
        > 如果取消或暂停作业，正在复制的大型文件可能只复制了一半。 这些文件将以相同的状态上传到 Azure。 尝试取消或暂停时，请验证文件是否已正常复制。 若要验证这些文件，请查看 SMB 共享或下载 BOM 文件。

    - 如果网络假死等暂时性错误导致作业突然失败，可以重启作业。 如果作业已达到终结状态（例如，已成功完成，或已完成但出错），则无法重启作业。 失败可能是文件命名或文件大小问题造成的。 将记录这些错误，但作业一旦完成便不可重启。

        ![重启失败的作业](media/data-box-deploy-copy-data-via-copy-service/restart-failed-job.png)

        如果出现失败并且无法重启作业，请下载错误日志，并查看日志文件中的失败。 更正问题后，可以创建新作业来复制文件。 也可以[通过 SMB 复制文件](data-box-deploy-copy-data.md)。
    
    - 在此版本中无法删除作业。
    
    - 可以创建无限个作业，但在任意给定时间，一次最多只能运行 10 个作业。
    - 如果启用了文件优化，则引入时会打包小型文件，以提高复制性能。 在这种情况下，你会看到以下屏幕截图中所示的打包文件（GUID 为名称）。

        ![打包文件的示例](media/data-box-deploy-copy-data-via-copy-service/packed-file-on-ingest.png)

6. 当作业正在进行时，在“复制数据”页上：

    - 在“状态”列中，可以查看复制作业的状态。 状态可能是：
        - **正在运行**
        - 失败
        - 成功
        - **正在暂停**
        - **已暂停**
        - **正在取消**
        - **已取消**
        - **已完成但出错**
    - 在“文件”列中，可以看到正在复制的文件的总数和总大小。
    - 在“已处理”列中，可以看到已处理的文件的数目和大小。
    - 在“详细信息”列中，单击“查看”可以查看作业详细信息。
    - 如果在复制过程中出现了“错误数”列中所示的任何错误，请转到“错误日志”列，并下载错误日志用于故障排除。

等待复制作业完成。 由于某些错误仅记录在“连接并复制”页上，因此，在转到下一步骤之前，请确保复制作业已完成且未出错。

![“连接并复制”页上未显示错误](media/data-box-deploy-copy-data-via-copy-service/verify-no-errors-on-connect-and-copy.png)

为确保数据完整性，复制数据时将以内联方式计算校验和。 复制完成后，检查设备上的已用空间和可用空间。
    
![在仪表板上检查可用空间和已用空间](media/data-box-deploy-copy-data-via-copy-service/verify-used-space-dashboard.png)

复制作业完成后，可以转到“准备交付”。

>[!NOTE]
> 复制作业正在进行时，无法运行“准备交付”。

## <a name="prepare-to-ship"></a>准备交付

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]


## <a name="next-steps"></a>后续步骤

本教程介绍了有关 Azure Data Box 的主题，例如：

> [!div class="checklist"]
> * 将数据复制到 Data Box
> * 准备交付 Data Box

请继续学习下一篇教程，了解如何将 Data Box 寄回 Microsoft。

> [!div class="nextstepaction"]
> [将 Azure Data Box 寄送到 Microsoft](./data-box-deploy-picked-up.md)

