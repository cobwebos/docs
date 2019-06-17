---
title: 使用 AzCopy v10 将数据复制或移到 Azure 存储 | Microsoft Docs
description: AzCopy 是一个命令行实用工具，可用于将数据复制到，或存储帐户之间。 本文可帮助你下载 AzCopy，连接到你的存储帐户，然后将文件传输。
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: bfa3e5a943ee59b1ed335f45e113a60f62572675
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66735033"
---
# <a name="get-started-with-azcopy"></a>AzCopy 入门

AzCopy 是一个命令行实用工具，可用于向 / 从存储帐户复制 blob 或文件。 本文可帮助你下载 AzCopy，连接到你的存储帐户，然后将文件传输。

> [!NOTE]
> AzCopy **V10**是当前支持的版本的 AzCopy。
>
> 如果你需要使用 AzCopy **v8.1**，请参阅[使用以前版本的 AzCopy](#previous-version)本文的部分。

<a id="download-and-install-azcopy" />

## <a name="download-azcopy"></a>下载 AzCopy

首先，下载 AzCopy V10 可执行文件到您的计算机上的任何目录。 

- [Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

> [!NOTE]
> 如果你想要将数据复制到和从你[Azure 表存储](https://docs.microsoft.com/azure/storage/tables/table-storage-overview)服务，然后安装[AzCopy 版本 7.3](https://aka.ms/downloadazcopynet)。

## <a name="run-azcopy"></a>运行 AzCopy

为方便起见，请考虑将 AzCopy 可执行文件的目录位置添加到系统路径中的易于使用。 这样一来，您可以键入`azcopy`从你的系统上的任何目录。

如果您选择不将 AzCopy 目录添加到你的路径，您必须将目录更改为 AzCopy 可执行文件和类型的位置`azcopy`或`.\azcopy`在 Windows PowerShell 的命令提示中。

若要查看命令的列表，请键入`azcopy -h`，然后按 ENTER 键。

若要了解有关特定命令，只需包含的命令名称 (例如： `azcopy list -h`)。

![内联帮助](media/storage-use-azcopy-v10/azcopy-inline-help.png)

您可以执行任何意义，使用 AzCopy 操作之前，您需要决定将提供到存储服务的授权凭据的方式。

## <a name="choose-how-youll-provide-authorization-credentials"></a>选择将提供授权凭据的方式

使用 Azure Active Directory (AD)，或使用共享访问签名 (SAS) 令牌，可以提供授权凭据。

使用此表作为指南：

| 存储类型 | 当前受支持的方法的授权 |
|--|--|
|**Blob 存储** | Azure AD & SAS |
|**Blob 存储 （分层命名空间）** | 仅 Azure AD |
|**文件存储** | 仅 SAS |

### <a name="option-1-use-azure-ad"></a>选项 1：使用 Azure AD

所需的授权级别取决于你是否计划将文件上传或只需下载它们。

#### <a name="authorization-to-upload-files"></a>若要将文件上传的授权

验证，这些角色之一的已分配给你的身份：

- [存储 Blob 数据参与者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [存储 Blob 数据所有者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

这些角色可以分配给你在任何这些作用域中的身份：

- 容器 （文件系统）
- 存储帐户
- 资源组
- 订阅

若要了解如何验证并分配角色，请参阅[授予对 Azure blob 和队列数据使用 RBAC 在 Azure 门户中访问](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

不需要有一个分配给你的身份，如果你的身份添加到目标容器或目录的访问控制列表 (ACL) 这些角色。 在 ACL 中你的身份需要在目标目录中，写入权限和对容器和每个父目录的执行权限。

若要了解详细信息，请参阅[Azure 数据湖存储第 2 代中的访问控制](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。

#### <a name="authorization-to-download-files"></a>若要下载文件的授权

验证，这些角色之一的已分配给你的身份：

- [存储 Blob 数据读者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader)
- [存储 Blob 数据参与者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [存储 Blob 数据所有者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

这些角色可以分配给你在任何这些作用域中的身份：

- 容器 （文件系统）
- 存储帐户
- 资源组
- 订阅

若要了解如何验证并分配角色，请参阅[授予对 Azure blob 和队列数据使用 RBAC 在 Azure 门户中访问](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

不需要有一个分配给你的身份，如果你的身份添加到目标容器或目录的访问控制列表 (ACL) 这些角色。 在 ACL 中你的身份需要对目标目录读取权限和对容器和每个父目录的执行权限。

若要了解详细信息，请参阅[Azure 数据湖存储第 2 代中的访问控制](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。

#### <a name="authenticate-your-identity"></a>对你的身份进行身份验证

验证你的身份已被授予必要的授权级别后，打开命令提示符、 键入以下命令，然后按 ENTER 键。

```azcopy
azcopy login
```

此命令返回身份验证代码和网站的 URL。 打开网站，提供代码，然后选择“下一步”按钮。 

![创建容器](media/storage-use-azcopy-v10/azcopy-login.png)

此时会出现登录窗口。 在该窗口中，使用 Azure 帐户凭据登录到 Azure 帐户。 成功登录后，可以关闭浏览器窗口，开始使用 AzCopy。

### <a name="option-2-use-a-sas-token"></a>选项 2：使用 SAS 令牌

AzCopy 命令中，可以将 SAS 令牌附加到使用每个源或目标 URL。

此示例命令以递归方式将数据从本地目录复制到 blob 容器。 虚构的 SAS 令牌追加到末尾的容器 URL。

```azcopy
azcopy cp "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

若要了解有关 SAS 令牌和如何获取一个详细信息，请参阅[使用共享访问签名 (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)。

## <a name="transfer-files"></a>传输文件

已通过身份验证你的身份或获取的 SAS 令牌后，可以开始传输文件。

若要查找的示例命令，请参阅以下文章。

- [使用 AzCopy 和 Blob 存储传输数据](storage-use-azcopy-blobs.md)

- [使用 AzCopy 和文件存储传输数据](storage-use-azcopy-files.md)

- [使用 AzCopy 和 Amazon S3 Bucket 传输数据](storage-use-azcopy-s3.md)

## <a name="configure-optimize-and-troubleshoot-azcopy"></a>配置、 优化和故障排除 AzCopy

请参阅[配置，优化和故障排除 AzCopy](storage-use-azcopy-configure.md)

## <a name="use-azcopy-in-storage-explorer"></a>在存储资源管理器中使用 AzCopy

如果你想要利用的 AzCopy，性能优势，但想要使用存储资源管理器而不是命令行以与你的文件，然后启用 AzCopy 在存储资源管理器。

在存储资源管理器中选择**预览版**->**改进了 Blob 上传和下载使用 AzCopy**。

![在 Azure 存储资源管理器中启用 AzCopy 作为传输引擎](media/storage-use-azcopy-v10/enable-azcopy-storage-explorer.jpg)

> [!NOTE]
> 您无需启用此设置，如果已在存储帐户上启用层次结构的命名空间。 这是因为存储资源管理器会自动使用 AzCopy 在已分层命名空间的存储帐户。  

<a id="previous-version" />

## <a name="use-the-previous-version-of-azcopy"></a>使用以前版本的 AzCopy

如果您需要使用以前版本的 AzCopy (AzCopy v8.1)，请参阅以下链接之一：

- [AzCopy on Windows (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy)
- [AzCopy on Linux (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="next-steps"></a>后续步骤

如果你有疑问、 问题或常规反馈，将其提交[GitHub 上](https://github.com/Azure/azure-storage-azcopy)页。
