---
title: 使用 AzCopy v10（预览版）将数据复制或移到 Azure 存储 | Microsoft Docs
description: 使用 AzCopy v10（预览版）实用程序向/从 blob、Data Lake 和文件内容移动或复制数据。 从本地文件将数据复制到 Azure 存储，或者在存储帐户中或存储帐户之间复制数据。 轻松地将数据迁移到 Azure 存储。
services: storage
author: artemuwka
ms.service: storage
ms.topic: article
ms.date: 02/24/2019
ms.author: artemuwka
ms.subservice: common
ms.openlocfilehash: 111c24c1cd608542a5ef7da85f93ca22082af6d9
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2019
ms.locfileid: "57726713"
---
# <a name="transfer-data-with-the-azcopy-v10-preview"></a>使用 AzCopy v10（预览版）传输数据

AzCopy v10（预览版）是用于向/从 Microsoft Azure Blob 和文件存储复制数据的下一代命令行实用程序，它提供经过重新设计的命令行接口和全新体系结构，以便实现高性能可靠的数据传输。 使用 AzCopy，可在文件系统和存储帐户之间或在存储帐户之间复制数据。

## <a name="whats-new-in-azcopy-v10"></a>AzCopy v10 中的新增功能

- 将文件系统同步到 Azure Blob，反之亦然。 使用 `azcopy sync <source> <destination>`。 非常适合增量复制方案。
- 支持 Azure Data Lake Storage Gen2 API。 将 `myaccount.dfs.core.windows.net` 用作 URI 以调用 ADLS Gen2 API。
- 支持将整个帐户（仅限 Blob 服务）复制到另一个帐户。
- 帐户复制到的帐户现在使用新[从 URL 放置块](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url)Api。 无需向客户端传输数据，使传输速度更快！
- 列出/删除给定路径中的文件和 blob。
- 中的路径中-还支持通配符模式排除标志。
- 改进了复原能力：每个 AzCopy 实例都将创建一个作业顺序和一个相关的日志文件。 可以查看并重启之前的作业并恢复失败的作业。 AzCopy 还会在失败后自动重试传输。
- 常规性能改进。

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

## <a name="post-installation-steps"></a>安装后步骤

AzCopy v10 不需要安装。 打开首选的命令行应用程序并导航到的文件夹位置`azcopy.exe`(Windows) 或`azcopy`(Linux) 可执行文件的位置。 如需要，可以将 AzCopy 文件夹位置添加到系统路径。

## <a name="authentication-options"></a>身份验证选项

使用 Azure 存储进行身份验证时，AzCopy v10 允许你使用以下选项：
- **Azure Active Directory [支持用于 Blob 和 ADLS Gen2 服务]**。 借助 Azure Active Directory，使用 ```.\azcopy login``` 登录。  用户应具有[分配的“存储 Blob 数据参与者”角色](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac)，以便使用 Azure Active Directory 身份验证写入 Blob 存储。 对于使用托管服务标识 (MSI) 进行身份验证，使用`azcopy login --identity`后授予参与者角色的 Azure 计算实例。
- **SAS 令牌 [支持用于 Blob 和文件服务]**。 在命令行上将 SAS 令牌追加到 blob 路径以使用它。 可以使用 Azure 门户、[存储资源管理器](https://blogs.msdn.microsoft.com/jpsanders/2017/10/12/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer/)、[PowerShell](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageblobsastoken) 或所选择的其他工具生成 SAS 令牌。 有关详细信息，请参阅[示例](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2)。

## <a name="getting-started"></a>入门

> [!TIP]
> **更喜欢图形用户界面？**
>
> 请尝试[Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)，桌面客户端，可简化管理的 Azure 存储数据，并**现在使用 AzCopy**来加速数据传输和传出 Azure 存储。
>
> 只需启用 AzCopy 功能在存储资源管理器预览菜单下。 然后，存储资源管理器将使用 AzCopy 上传和下载数据到 Blob 存储以提高性能时。
> ![为 Azure 存储资源管理器中的传输引擎启用 AzCopy](media/storage-use-azcopy-v10/enable-azcopy-storage-explorer.jpg)

AzCopy v10 具有简单的自记录语法。 当已登录到 Azure Active Directory 时，常规语法如下所示：

```azcopy
.\azcopy <command> <arguments> --<flag-name>=<flag-value>
# Examples if you have logged into the Azure Active Directory:
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/container" --recursive=true
.\azcopy cp "C:\local\path\myfile" "https://account.blob.core.windows.net/container/myfile"
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/container"

# Examples if you are using SAS tokens to authenticate:
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/container?sastoken" --recursive=true
.\azcopy cp "C:\local\path\myfile" "https://account.blob.core.windows.net/container/myfile?sastoken"
```

以下语法介绍如何获取可用命令的列表：

```azcopy
.\azcopy --help
# Using the alias instead
.\azcopy -h
```

若要查看特定命令的帮助页面和示例，请运行以下命令：

```azcopy
.\azcopy <cmd> --help
# Example:
.\azcopy cp -h
```

## <a name="create-a-blob-container-or-file-share"></a>创建 Blob 容器或文件共享 

**创建 Blob 容器**

```azcopy
.\azcopy make "https://account.blob.core.windows.net/container-name"
```

**创建文件共享**

```azcopy
.\azcopy make "https://account.file.core.windows.net/share-name"
```

**使用 ADLS Gen2 创建 Blob 容器**

如果已在 Blob 存储帐户上启用分层命名空间，则可以使用以下命令创建新的文件系统（Blob 容器），以便将文件上传到该文件系统。

```azcopy
.\azcopy make "https://account.dfs.core.windows.net/top-level-resource-name"
```

## <a name="copy-data-to-azure-storage"></a>将数据复制到 Azure 存储

使用 copy 命令将数据从源传输到目标。 源/目标可以是：
- 本地文件系统
- Azure Blob/虚拟目录/容器 URI
- Azure 文件/目录/文件共享 URI
- Azure Data Lake Storage Gen2 文件系统/目录/文件 URI

```azcopy
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
# Using alias instead
.\azcopy cp <source path> <destination path> --<flag-name>=<flag-value>
```

以下命令以递归方式将文件夹 `C:\local\path` 下的所有文件上传到容器 `mycontainer1`，从而在该容器中创建 `path` 目录：

```azcopy
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true
```

以下命令将文件夹 `C:\local\path` 下的所有文件上传到容器 `mycontainer1`（不递归到子目录中）：

```azcopy
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/mycontainer1<sastoken>"
```

若要获取更多示例，请使用以下命令：

```azcopy
.\azcopy cp -h
```

## <a name="copy-data-between-two-storage-accounts"></a>在两个存储帐户之间复制数据

在两个存储帐户之间复制数据使用的是 [Put Block From URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API，而不使用客户端计算机的网络带宽。 直接在两个 Azure 存储服务器之间复制数据，而 AzCopy 只是协调复制操作。 此选项目前仅适用于 Blob 存储。

若要在两个存储帐户之间复制数据，请使用以下命令：
```azcopy
.\azcopy cp "https://account.blob.core.windows.net/<sastoken>" "https://otheraccount.blob.core.windows.net/<sastoken>" --recursive=true
```

> [!NOTE]
> 该命令将枚举所有 blob 容器并将它们复制到目标帐户。 目前，AzCopy v10 支持仅在两个存储帐户之间复制块 blob。 跳过所有其他存储帐户对象（追加 blob、页 blob、文件、表和队列）。

## <a name="copy-a-vhd-image-to-a-storage-account"></a>将 VHD 映像复制到存储帐户

使用`--blob-type=PageBlob`若要将磁盘映像上传到 Blob 存储作为页 Blob。

```azcopy
.\azcopy cp "C:\myimages\diskimage.vhd" "https://account.blob.core.windows.net/mycontainer/diskimage.vhd<sastoken>" --blob-type=PageBlob
```

## <a name="sync-incremental-copy-and-optional-delete-blob-storage-only"></a>同步： 增量复制和 （可选） 删除 （仅适用于 Blob 存储）

同步命令将同步到的目录中的目标比较文件的名称和上次修改时间戳的源目录的内容。 如果在源中不存在，此操作还可以选择包含目标文件的删除时`--delete-destination=prompt|true`提供标志。 默认情况下被禁用的删除行为。

> [!NOTE]
> 使用`--delete-destination`时要注意的标志。 启用[软删除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)功能之前启用保持同步，以防止意外删除你的帐户中的删除行为。
>
> 当`--delete-destination`设置为 true，AzCopy 将删除中没有任何提示的目标用户的源中不存在的文件。 如果你想要系统提示确认，则使用`--delete-destination=prompt`。

若要将本地文件系统同步到存储帐户，请使用以下命令：

```azcopy
.\azcopy sync "C:\local\path" "https://account.blob.core.windows.net/mycontainer<sastoken>"
```

以同样的方式，也可将 Blob 容器同步到本地文件系统：

```azcopy
# If you're using Azure Active Directory authentication the sastoken is not required
.\azcopy sync "https://account.blob.core.windows.net/mycontainer" "C:\local\path"
```

通过该命令，可根据上次修改的时间戳将源以增量方式同步到目标。 如果在源中添加或删除文件，AzCopy v10 将在目标中执行相同的操作。 如果在同步命令启用删除行为，则 AzCopy 会删除文件从目标如果它们不在源中不再存在。

## <a name="advanced-configuration"></a>高级配置

### <a name="configure-proxy-settings"></a>配置代理设置

若要配置 AzCopy v10 的代理设置，请使用以下命令设置环境变量 https_proxy：

```cmd
# For Windows:
set https_proxy=<proxy IP>:<proxy port>
# For Linux:
export https_proxy=<proxy IP>:<proxy port>
# For MacOS
export https_proxy=<proxy IP>:<proxy port>
```

### <a name="optimize-throughput"></a>优化吞吐量

设置环境变量 AZCOPY_CONCURRENCY_VALUE，配置并发请求数并控制吞吐量性能和资源消耗。 默认情况下，该值设置为 300。 减小该值将限制 AzCopy v10 使用的带宽和 CPU。

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
# If the value is blank then the default value is currently in use
```

### <a name="change-the-default-log-level"></a>更改默认日志级别

默认情况下，AzCopy 日志级别设置为 INFO。 如果想要降低日志详细程度以节省磁盘空间，请使用 ``--log-level`` 选项覆盖该设置。 可用日志级别为：DEBUG、INFO、WARNING、ERROR、PANIC 和 FATAL

## <a name="troubleshooting"></a>故障排除

AzCopy v10 为所有作业创建日志文件和计划文件。 可以使用日志调查并解决任何潜在问题。 日志包含失败状态（UPLOADFAILED、COPYFAILED 和 DOWNLOADFAILED）、完整路径以及失败原因。 作业日志和计划文件位于 %USERPROFILE%\\.azcopy 文件夹 Windows 或 $HOME\\.azcopy Mac 和 Linux 上的文件夹。

> [!IMPORTANT]
> 向 Microsoft 支持部门提交支持请求时（或者排查涉及第三方的问题时），请共享你尝试执行的命令的经修订版本，以确保不会意外地与任何人共享 SAS。 可以在日志文件的开头找到经修订的版本。

### <a name="review-the-logs-for-errors"></a>查看错误日志

以下命令将从 04dc9ca9-158f-7945-5933-564021086c79 日志中获取状态为 UPLOADFAILED 的所有错误：

```azcopy
cat 04dc9ca9-158f-7945-5933-564021086c79.log | grep -i UPLOADFAILED
```

或者请参阅未能传输使用的文件名`azcopy jobs show <jobid> --with-status=Failed`命令。

### <a name="view-and-resume-jobs"></a>查看和恢复作业

每个传输操作都将创建一个 AzCopy 作业。 可以使用以下命令查看作业历史记录：

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

可以使用失败/取消的作业的标识符以及 SAS 令牌来恢复该作业（出于安全原因，该作业并不是永久性的）：

```azcopy
.\azcopy jobs resume <jobid> --sourcesastokenhere --destinationsastokenhere
```

## <a name="next-steps"></a>后续步骤

随时欢迎你的反馈。 如有任何疑问、问题或常规反馈，请通过 https://github.com/Azure/azure-storage-azcopy 提交。 谢谢！
