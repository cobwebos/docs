---
title: 通过 SMB 从 Azure Data Box 复制数据的教程 | Microsoft Docs
description: 了解如何通过 SMB 将数据复制到 Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 07/10/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 1d39b29ba340b34d6f0add8ff5da473408bd6360
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2020
ms.locfileid: "86259124"
---
# <a name="tutorial-copy-data-from-azure-data-box-via-smb-preview"></a>教程：通过 SMB 从 Azure Data Box 复制数据（预览版）

本教程介绍如何使用本地 Web UI 连接到 Data Box 并从中将数据复制到本地服务器。 Data Box 设备包含从 Azure 存储帐户导出的数据。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
>
> * 先决条件
> * 连接到 Data Box
> * 从 Data Box 复制数据

[!INCLUDE [Data Box feature is in preview](../../includes/data-box-feature-is-preview-info.md)]

## <a name="prerequisites"></a>先决条件

在开始之前，请确保：

1. 你已订购 Azure Data Box。
    - 有关导入订单，请参阅[教程：订购 Azure Data Box](data-box-deploy-ordered.md)。
    - 有关导出订单，请参阅[教程：订购 Azure Data Box](data-box-deploy-export-ordered.md)。
2. 已收到 Data Box，并且门户中的订单状态为“已送达”。
3. 你有一台主机，需要将数据从 Data Box 复制到其中。 该主机必须
   * 运行[支持的操作系统](data-box-system-requirements.md)。
   * 连接到高速网络。 强烈建议你至少建立一个 10-GbE 连接。 如果 10-GbE 连接不可用，可以使用 1-GbE 数据链路，但复制速度会受影响。

## <a name="connect-to-data-box"></a>连接到 Data Box

[!INCLUDE [data-box-shares](../../includes/data-box-shares.md)]

如果使用 Windows Server 主机，请按照以下步骤连接到 Data Box。

1. 第一步是进行身份验证并启动会话。 转到“连接和复制”。 选择“获取凭据”，获取与存储帐户关联的共享的访问凭据。 

    ![获取共享凭据 1](media/data-box-deploy-export-copy-data/get-share-credentials-1.png)

2. 在“访问共享和复制数据”对话框中，复制对应于该共享的“用户名”和“密码”。  选择“确定”。
    
    ![获取共享凭据 2](media/data-box-deploy-export-copy-data/get-share-credentials-2.png)

3. 若要从主机访问与存储帐户关联的共享（在以下示例中为“exportbvtdataset2”），请打开命令窗口。 在命令提示符处，键入：

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    根据数据格式，共享路径如下：
    - Azure 块 blob - `\\169.254.143.85\exportbvtdataset2_BlockBlob`
    - Azure 页 blob - `\\169.254.143.85\exportbvtdataset2_PageBlob`
    - Azure 文件 - `\\169.254.143.85\exportbvtdataset2_AzFile`

4. 出现提示时，请输入共享的密码。 以下示例演示如何通过前面的命令连接到共享。

    ```
    C:\Users\Databoxuser>net use \\169.254.143.85\exportbvtdataset2_BlockBlob /u:exportbvtdataset2
    Enter the password for 'exportbvtdataset2' to connect to '169.254.143.85':
    The command completed successfully.
    ```

4. 按 Windows+R。在“运行”窗口中指定 `\\<device IP address>`。 选择“确定”以打开文件资源管理器。
    
    ![通过文件资源管理器连接到共享 2](media/data-box-deploy-export-copy-data/connect-shares-file-explorer-1.png)

    此时应能看到文件夹形式的共享。
    
    ![通过文件资源管理器连接到共享 2](media/data-box-deploy-export-copy-data/connect-shares-file-explorer-2.png)

    
如果使用 Linux 客户端，请使用以下命令装载 SMB 共享。 下面的“vers”参数是 Linux 主机支持的 SMB 版本。 在下面的命令中插入相应版本。 有关 Data Box 支持的 SMB 版本，请参阅 [Linux 客户端支持的文件系统](https://docs.microsoft.com/azure/databox/data-box-system-requirements#supported-file-systems-for-linux-clients) 

```console
sudo mount -t nfs -o vers=2.1 169.254.143.85:/exportbvtdataset2_BlockBlob /home/databoxubuntuhost/databox
```

## <a name="copy-data-from-data-box"></a>从 Data Box 复制数据

连接到 Data Box 共享后，下一步是复制数据。

[!INCLUDE [data-box-export-review-logs](../../includes/data-box-export-review-logs.md)]


 连接到 SMB 共享后，开始数据复制。 可以使用与 SMB 兼容的任何文件复制工具（例如 Robocopy）复制数据。 可以使用 Robocopy 启动多个复制作业。 


有关 Robocopy 命令的详细信息，请转到 [Robocopy 和几个示例](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx)。

复制完成后，转到“仪表板”，检查设备上的已用空间和可用空间。

现在，你可以继续将 Data Box 交付到 Microsoft。


## <a name="next-steps"></a>后续步骤

本教程介绍了有关 Azure Data Box 的主题，例如：

> [!div class="checklist"]
>
> * 先决条件
> * 连接到 Data Box
> * 从 Data Box 复制数据

请继续学习下一篇教程，了解如何将 Data Box 寄回 Microsoft。

> [!div class="nextstepaction"]
> [将 Azure Data Box 寄送到 Microsoft](./data-box-deploy-export-picked-up.md)

