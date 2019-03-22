---
title: 数据复制或移动到 Azure 存储使用 AzCopy v10 （预览版） |Microsoft Docs
description: 使用 AzCopy v10 （预览版） 命令行实用工具来移动或复制数据到或从 blob、 data lake 和文件内容。 从本地文件将数据复制到 Azure 存储，或者在存储帐户中或存储帐户之间复制数据。 轻松地将数据迁移到 Azure 存储。
services: storage
author: artemuwka
ms.service: storage
ms.topic: article
ms.date: 02/24/2019
ms.author: artemuwka
ms.subservice: common
ms.openlocfilehash: ca7081bdfedae3abb5ec426a9d3ec0a7867a2ef9
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/21/2019
ms.locfileid: "58337011"
---
# <a name="transfer-data-with-azcopy-v10-preview"></a>使用 AzCopy v10 传输数据 （预览版）

AzCopy v10 （预览版） 是用于向 / 从 Microsoft Azure Blob 和文件存储复制数据的命令行实用程序。 AzCopy v10 提供了一个经过重新设计的命令行接口，并将新的体系结构的可靠的数据传输。 使用 AzCopy，可以将数据之间的文件系统和存储帐户，或存储帐户之间复制。

## <a name="whats-new-in-azcopy-v10"></a>AzCopy v10 中的新增功能

- 将同步到 Azure Blob 存储或执行相反的文件系统。 使用 `azcopy sync <source> <destination>`。 非常适合增量复制方案。
- 支持 Azure Data Lake Storage Gen2 API。 使用`myaccount.dfs.core.windows.net`为调用 Data Lake 存储第 2 代 Api 的 URI。
- 支持将整个帐户（仅限 Blob 服务）复制到另一个帐户。
- 使用新[从 URL 放置块](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url)Api 来支持帐户帐户副本。 数据传输是速度更快，因为传输到客户端不是必需的。
- 列出或删除文件和 blob 中给定的路径。
- 支持通配符模式中的路径和-排除标志。
- 创建每个 AzCopy 实例工作订单和相关的日志文件。 您可以查看和重新启动上一个作业和恢复失败的作业。 AzCopy 还会在失败后自动重试传输。
- 功能的常规性能改进。

## <a name="download-and-install-azcopy"></a>下载并安装 AzCopy

### <a name="latest-preview-version-v10"></a>最新预览版本 (v10)

下载 AzCopy 的最新预览版：
- [Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

### <a name="latest-production-version-v81"></a>最新生产版本 (v8.1)

下载 [AzCopy for Windows 的最新生产版](https://aka.ms/downloadazcopy)。

### <a name="azcopy-supporting-table-storage-service-v73"></a>支持表存储服务的 AzCopy (v7.3)

下载[支持向/从 Microsoft Azure 表存储服务复制数据的 AzCopy v7.3](https://aka.ms/downloadazcopynet)。

## <a name="post-installation-steps"></a>安装后的步骤

AzCopy v10 不需要进行安装。 打开首选的命令行应用程序，并浏览到的文件夹位置`azcopy.exe`所在。 如果需要可以将 AzCopy 的文件夹位置添加到系统路径的易于使用。

## <a name="authentication-options"></a>身份验证选项

使用 Azure 存储进行身份验证时，AzCopy v10 将支持以下选项：
- **Azure Active Directory** (适用于**Blob 和数据湖存储第 2 代服务**)。 使用```.\azcopy login```使用 Azure Active Directory 进行登录。  用户应具有["存储 Blob 数据参与者"角色分配](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac)要写入到 Blob 存储与 Azure Active Directory 身份验证。 对于通过 Azure 资源的管理的标识的身份验证，使用`azcopy login --identity`。
- **共享访问签名令牌 [Blob 和文件服务支持]**。 将共享的访问签名 (SAS) 令牌附加到要使用它的命令行上的 blob 路径。 您可以生成 SAS 令牌具有 Azure 门户中，[存储资源管理器](https://blogs.msdn.microsoft.com/jpsanders/2017/10/12/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer/)， [PowerShell](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageblobsastoken)，或所选的其他工具。 有关详细信息，请参阅[示例](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2)。

## <a name="getting-started"></a>入门

> [!TIP]
> **更喜欢图形用户界面？**
>
> [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)，简化了管理的 Azure 存储数据的桌面客户端现在使用 AzCopy 来加速数据传输和传出 Azure 存储。
>
> 启用在存储资源管理器下的 AzCopy**预览版**菜单。
> ![为 Azure 存储资源管理器中的传输引擎启用 AzCopy](media/storage-use-azcopy-v10/enable-azcopy-storage-explorer.jpg)

AzCopy v10 具有自有案可稽的语法。 当已登录到 Azure Active Directory 时，常规语法看起来如下所示：

```azcopy
.\azcopy <command> <arguments> --<flag-name>=<flag-value>

# Examples if you have logged into the Azure Active Directory:
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/container" --recursive=true
.\azcopy cp "C:\local\path\myfile" "https://account.blob.core.windows.net/container/myfile"
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/container"

# Examples if you're using SAS tokens to authenticate:
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/container?sastoken" --recursive=true
.\azcopy cp "C:\local\path\myfile" "https://account.blob.core.windows.net/container/myfile?sastoken"
```

以下语法介绍如何获取可用命令的列表：

```azcopy
.\azcopy --help
# To use the alias instead
.\azcopy -h
```

若要查看的帮助页和特定命令的示例，请运行以下命令：

```azcopy
.\azcopy <cmd> --help
# Example:
.\azcopy cp -h
```

## <a name="create-a-blob-container-or-file-share"></a>创建 blob 容器或文件共享 

**创建 blob 容器**

```azcopy
.\azcopy make "https://account.blob.core.windows.net/container-name"
```

**创建文件共享**

```azcopy
.\azcopy make "https://account.file.core.windows.net/share-name"
```

**使用 Azure 数据湖存储第 2 代创建 blob 容器**

如果已在 Blob 存储帐户上启用分层命名空间，可以使用以下命令以创建新的 blob 容器上传的文件。

```azcopy
.\azcopy make "https://account.dfs.core.windows.net/top-level-resource-name"
```

## <a name="copy-data-to-azure-storage"></a>将数据复制到 Azure 存储

使用 copy 命令将数据从源传输到目标。 源或目标可以是：
- 本地文件系统
- Azure Blob/虚拟目录/容器 URI
- Azure 文件/目录/文件共享 URI
- Azure Data Lake Storage Gen2 文件系统/目录/文件 URI

```azcopy
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
# Using the alias instead 
.\azcopy cp <source path> <destination path> --<flag-name>=<flag-value>
```

以下命令将该文件夹下的所有文件上都传`C:\local\path`到容器的递归`mycontainer1`，则创建`path`目录容器中：

```azcopy
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true
```

以下命令将文件夹 `C:\local\path` 下的所有文件上传到容器 `mycontainer1`（不递归到子目录中）：

```azcopy
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/mycontainer1<sastoken>"
```

若要查找更多示例，请使用以下命令：

```azcopy
.\azcopy cp -h
```

## <a name="copy-data-between-two-storage-accounts"></a>在两个存储帐户之间复制数据

使用两个存储帐户之间复制数据[放置块从 URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API，但不使用客户端计算机的网络带宽。 AzCopy 只需协调复制操作时，将直接，两个 Azure 存储服务器之间复制数据。 此选项目前仅适用于 Blob 存储。

若要在两个存储帐户之间复制数据，请使用以下命令：
```azcopy
.\azcopy cp "https://myaccount.blob.core.windows.net/<sastoken>" "https://myotheraccount.blob.core.windows.net/<sastoken>" --recursive=true
```

> [!NOTE]
> 此命令将枚举所有的 blob 容器，并将它们复制到目标帐户。 在此期间，AzCopy v10 支持块 blob 仅两个存储帐户之间复制。 它将跳过所有其他存储帐户对象 （如追加 blob、 页 blob、 文件、 表和队列）。

## <a name="copy-a-vhd-image-to-a-storage-account"></a>将 VHD 映像复制到存储帐户

默认情况下，AzCopy v10 将数据上传到块 blob 中。 但是，如果源文件具有`.vhd`扩展，AzCopy v10 将默认为上传到页 blob。 在此期间，此操作不是可配置。

## <a name="sync-incremental-copy-and-delete-blob-storage-only"></a>同步：增量复制和删除（仅适用于 Blob 存储）

同步命令进行同步到的目录中目标，将文件名称进行比较的源目录的内容，上次修改时间戳。 如果在源中不存在，此操作包括可选目标文件的删除时`--delete-destination=prompt|true`提供标志。 默认情况下，禁用删除行为。 

> [!NOTE] 
> 使用`--delete-destination`时要注意的标志。 启用[软删除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)功能之前启用保持同步，以防止意外删除你的帐户中的删除行为。 

> 当`--delete-destination`设置为 true，AzCopy 将删除中没有任何提示的目标用户的源中不存在的文件。 如果你想要系统提示确认，则使用`--delete-destination=prompt`。

若要将本地文件系统同步到存储帐户，请使用以下命令：

```azcopy
.\azcopy sync "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true
```

此外可以同步到本地文件系统的 blob 容器：

```azcopy
# The SAS token isn't required for Azure Active Directory authentication.
.\azcopy sync "https://account.blob.core.windows.net/mycontainer1" "C:\local\path" --recursive=true
```

此命令将以增量方式同步源到目标基于上次修改时间戳。 如果在源中添加或删除文件，AzCopy v10 将在目标中执行相同的操作。 之前删除，AzCopy 将提示你确认。

## <a name="advanced-configuration"></a>高级配置

### <a name="configure-proxy-settings"></a>配置代理设置

若要配置 AzCopy v10 的代理设置，请使用以下命令设置环境变量 https_proxy:

```cmd
# For Windows:
set https_proxy=<proxy IP>:<proxy port>
# For Linux:
export https_proxy=<proxy IP>:<proxy port>
# For MacOS
export https_proxy=<proxy IP>:<proxy port>
```

### <a name="optimize-throughput"></a>优化吞吐量

设置环境变量 AZCOPY_CONCURRENCY_VALUE 配置数目的并发请求，并控制的吞吐量性能和资源消耗情况。 默认情况下，该值设置为 300。 减小该值将限制 AzCopy v10 使用的带宽和 CPU。

```cmd
# For Windows:
set AZCOPY_CONCURRENCY_VALUE=<value>
# For Linux:
export AZCOPY_CONCURRENCY_VALUE=<value>
# For MacOS
export AZCOPY_CONCURRENCY_VALUE=<value>
# To check the current value of the variable on all the platforms
.\azcopy env
# If the value is blank then the default value is currently in use
```

### <a name="change-the-location-of-the-log-files"></a>更改日志文件的位置

你可以更改日志文件的位置以满足需要或者避免填满 OS 磁盘。

```cmd
# For Windows:
set AZCOPY_LOG_LOCATION=<value>
# For Linux:
export AZCOPY_LOG_LOCATION=<value>
# For MacOS
export AZCOPY_LOG_LOCATION=<value>
# To check the current value of the variable on all the platforms
.\azcopy env
# If the value is blank, then the default value is currently in use
```
### <a name="change-the-default-log-level"></a>更改默认日志级别 

默认情况下，AzCopy 日志级别设置为信息。 如果想要降低日志详细程度以节省磁盘空间，请使用 ``--log-level`` 选项覆盖该设置。 可用日志级别为：调试、 信息、 警告、 错误、 死机和严重。

### <a name="review-the-logs-for-errors"></a>查看错误日志

以下命令将从 04dc9ca9-158f-7945-5933-564021086c79 日志中获取状态为 UPLOADFAILED 的所有错误：

```azcopy
cat 04dc9ca9-158f-7945-5933-564021086c79.log | grep -i UPLOADFAILED
```
## <a name="troubleshooting"></a>故障排除

AzCopy v10 创建日志文件和每个作业的计划文件。 可以使用日志调查并解决任何潜在问题。 日志包含失败状态（UPLOADFAILED、COPYFAILED 和 DOWNLOADFAILED）、完整路径以及失败原因。 作业日志和计划文件在 Windows 上位于 %USERPROFILE\\.azcopy 文件夹中，在 Mac 和 Linux 上位于 $HOME\\.azcopy 文件夹中。

> [!IMPORTANT]
> 提交到 Microsoft 支持部门 （或故障排除问题涉及任何第三方），请求时共享你想要执行的命令经过修订的版本。 这可确保 SAS 不会意外地与任何人共享。 可以在日志文件的开头找到经修订的版本。

### <a name="view-and-resume-jobs"></a>查看和恢复作业

每个传输操作都将创建一个 AzCopy 作业。 使用以下命令查看作业的历史记录：

```azcopy
.\azcopy jobs list
```

若要查看作业统计信息，请使用以下命令：

```azcopy
.\azcopy jobs show <job-id>
```

若要按状态筛选传输，请使用以下命令：

```azcopy
.\azcopy jobs show <job-id> --with-status=Failed
```

使用以下命令来恢复失败/已取消的作业。 此命令使用连同 SAS 令牌的标识符。 它不是永久性出于安全原因：

```azcopy
.\azcopy jobs resume <jobid> --sourcesastokenhere --destinationsastokenhere
```

## <a name="next-steps"></a>后续步骤

如果你有疑问、 问题或常规反馈，将其提交[GitHub 上](https://github.com/Azure/azure-storage-azcopy.)。


