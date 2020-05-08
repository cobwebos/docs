---
title: 使用 AzCopy v10 将数据复制或移到 Azure 存储 | Microsoft Docs
description: AzCopy 是一个命令行实用工具，可用于向/从存储帐户复制数据，或者在存储帐户之间复制数据。 本文将帮助你下载 AzCopy，连接到存储帐户，然后传输文件。
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 6e6bd55fbb73113dfbcd01e94753c4fb21219c14
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82780872"
---
# <a name="get-started-with-azcopy"></a>AzCopy 入门

AzCopy 是一个命令行实用工具，可用于向/从存储帐户复制 Blob 或文件。 本文将帮助你下载 AzCopy，连接到存储帐户，然后传输文件。

> [!NOTE]
> AzCopy **V10** 是当前支持的 AzCopy 版本。
>
> 如果需要使用早期版本的 AzCopy，请参阅本文的[使用 AzCopy 的早期版本](#previous-version)部分。

<a id="download-and-install-azcopy" />

## <a name="download-azcopy"></a>下载 AzCopy

首先，将 AzCopy V10 可执行文件下载到计算机上的任意目录。 AzCopy V10 是一个免安装的可执行文件。

- [Windows 64 位](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Windows 32 位](https://aka.ms/downloadazcopy-v10-windows-32bit) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

这些文件压缩成 zip 文件（Windows 和 Mac）或 tar 文件（Linux）。 要在 Linux 上下载并解压缩 tar 文件，请参阅 Linux 分发文档。

> [!NOTE]
> 若要向/从 [Azure 表存储](https://docs.microsoft.com/azure/storage/tables/table-storage-overview)服务复制数据，请安装 [AzCopy 版本 7.3](https://aka.ms/downloadazcopynet)。


## <a name="run-azcopy"></a>运行 AzCopy

为方便使用，请考虑将 AzCopy 可执行文件的目录位置添加到系统路径。 这样就可以在系统上的任何目录中键入 `azcopy`。

如果不将 AzCopy 目录添加到系统路径，则必须将目录切换到 AzCopy 可执行文件所在的位置，然后在 Windows PowerShell 命令提示符中键入 `azcopy` 或 `.\azcopy`。

若要查看命令列表，请键入 `azcopy -h` 并按 ENTER 键。

若要了解特定的命令，只需包含该命令的名称（例如：`azcopy list -h`）。

![联机帮助](media/storage-use-azcopy-v10/azcopy-inline-help.png)

若要查找每个命令和命令参数的详细参考文档，请参阅 [azcopy](storage-ref-azcopy.md)

> [!NOTE] 
> 系统不会自动向 Azure 存储帐户的所有者分配数据访问权限。 在使用 AzCopy 执行任何有意义的操作之前，需确定如何向存储服务提供身份验证凭据。 

## <a name="choose-how-youll-provide-authorization-credentials"></a>选择如何提供授权凭据

可以使用 Azure Active Directory (AD) 或共享访问签名 (SAS) 令牌来提供授权凭据。

请参考下表：

| 存储类型 | 当前支持的授权方法 |
|--|--|
|**Blob 存储** | Azure AD 和 SAS |
|**Blob 存储（分层命名空间）** | Azure AD 和 SAS |
|**文件存储** | 仅限 SAS |

### <a name="option-1-use-azure-active-directory"></a>选项 1：使用 Azure Active Directory

使用 Azure Active Directory 可以一次性提供凭据，而无需向每个命令追加 SAS 令牌。  

> [!NOTE]
> 在当前版本中，如果你打算在存储帐户之间复制 Blob，必须向每个源 URL 追加一个 SAS 令牌。 只能在目标 URL 中省略 SAS 令牌。 有关示例，请参阅[在存储帐户之间复制 Blob](storage-use-azcopy-blobs.md)。

所需的授权级别取决于是要上传文件，还是只下载文件。

如果你只想下载文件，请验证是否已将[存储 Blob 数据读取者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader)角色分配到你的用户标识、托管标识或服务主体。

> 用户标识、托管标识和服务主体都是安全主体的一种类型，因此本文的余下内容将使用“安全主体”这一术语。  

若要上传文件，请验证是否已将以下角色之一分配到了你的安全主体：

- [存储 Blob 数据参与者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor)
- [存储 Blob 数据所有者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

可在以下任何范围内将这些角色分配到安全主体：

- 容器（文件系统）
- 存储帐户
- 资源组
- 订阅

若要了解如何验证和分配角色，请参阅[在 Azure 门户中使用 RBAC 授予对 Azure Blob 和队列数据的访问权限](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

> [!NOTE]
> 请记住，RBAC 角色分配可能需要最多五分钟的时间进行传播。

如果安全主体已添加到目标容器或目录的访问控制列表 (ACL)，则无需将这些角色之一分配到安全主体。 在 ACL 中，安全主体需要对目标目录拥有写入权限，并对容器和每个父目录拥有执行权限。

有关详细信息，请参阅 [Azure Data Lake Storage Gen2 中的访问控制](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。

#### <a name="authenticate-a-user-identity"></a>对用户标识进行身份验证

验证你的用户标识已获得所需的授权级别后，打开命令提示符，然后键入以下命令并按 ENTER 键。

```azcopy
azcopy login
```

如果你属于多个组织，请包含存储帐户所属组织的租户 ID。

```azcopy
azcopy login --tenant-id=<tenant-id>
```

请将 `<tenant-id>` 占位符替换为存储帐户所属组织的租户 ID。 若要查找租户 ID，请在 Azure 门户中选择“Azure Active Directory”>“属性”>“目录 ID”。 

此命令返回身份验证代码和网站的 URL。 打开网站，提供代码，然后选择“下一步”按钮。 

![创建容器](media/storage-use-azcopy-v10/azcopy-login.png)

此时会出现登录窗口。 在该窗口中，使用 Azure 帐户凭据登录到 Azure 帐户。 成功登录后，可以关闭浏览器窗口，开始使用 AzCopy。

<a id="service-principal" />

#### <a name="authenticate-a-service-principal"></a>对服务主体进行身份验证

如果你打算在无需用户交互即可运行（尤其是在本地运行）的脚本中使用 AzCopy，此选项非常有用。 如果你打算在 Azure 中运行的 VM 上运行 AzCopy，则最好是使用托管服务标识，因为它更易于管理。 有关详细信息，请参阅本文的[对托管标识进行身份验证](#managed-identity)部分。

在运行脚本之前，必须至少以交互方式登录一次，以便可为 AzCopy 提供服务主体的凭据。  这些凭据存储在受保护的已加密文件中，因此，脚本无需提供敏感信息。

可以使用客户端机密或使用与服务主体应用注册关联的证书的密码登录到帐户。

若要详细了解如何创建服务主体，请参阅[如何：使用门户创建可访问资源的 Azure AD 应用程序和服务主体](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)。

若要了解有关服务主体的一般性详细信息，请参阅 [Azure Active Directory 中的应用程序和服务主体对象](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)。

##### <a name="using-a-client-secret"></a>使用客户端机密

首先将 `AZCOPY_SPA_CLIENT_SECRET` 环境变量设置为服务主体应用注册的客户端机密。

> [!NOTE]
> 确保在命令提示符下设置此值，而不要在操作系统的环境变量设置中进行设置。 这样，该值就只能在当前会话中使用。

本示例演示如何在 PowerShell 中执行此操作。

```azcopy
$env:AZCOPY_SPA_CLIENT_SECRET="$(Read-Host -prompt "Enter key")"
```

> [!NOTE]
> 考虑使用本示例中所示的提示符。 这样，密码就不会显示在控制台的命令历史记录中。  

接下来键入以下命令，然后按 ENTER 键。

```azcopy
azcopy login --service-principal --application-id <application-id> --tenant-id=<tenant-id>
```

请将 `<application-id>` 占位符替换为服务主体应用注册的应用程序 ID。 请将 `<tenant-id>` 占位符替换为存储帐户所属组织的租户 ID。 若要查找租户 ID，请在 Azure 门户中选择“Azure Active Directory”>“属性”>“目录 ID”。  

##### <a name="using-a-certificate"></a>使用证书

如果你偏好使用自己的凭据进行授权，可将证书上传到应用注册，然后使用该证书登录。

除了将证书上传到应用注册以外，还需要在运行 AzCopy 的计算机或 VM 中保存该证书的副本。 此证书副本应采用 .PFX 或 .PEM 格式，且必须包含私钥。 该私钥应通过密码予以保护。 如果使用的是 Windows，且证书仅在证书存储中，请确保将该证书导出到 PFX 文件（包括私钥）。 有关指导，请参阅 [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate?view=win10-ps)

接下来，将 `AZCOPY_SPA_CERT_PASSWORD` 环境变量设为证书密码。

> [!NOTE]
> 确保在命令提示符下设置此值，而不要在操作系统的环境变量设置中进行设置。 这样，该值就只能在当前会话中使用。

本示例演示如何在 PowerShell 中执行此任务。

```azcopy
$env:AZCOPY_SPA_CERT_PASSWORD="$(Read-Host -prompt "Enter key")"
```

接下来键入以下命令，然后按 ENTER 键。

```azcopy
azcopy login --service-principal --certificate-path <path-to-certificate-file> --tenant-id=<tenant-id>
```

请将 `<path-to-certificate-file>` 占位符替换为证书文件的相对路径或完全限定的路径。 AzCopy 将保存此证书的路径，但并不会保存此证书的副本，因此，请务必将此证书放在原有位置。 请将 `<tenant-id>` 占位符替换为存储帐户所属组织的租户 ID。 若要查找租户 ID，请在 Azure 门户中选择“Azure Active Directory”>“属性”>“目录 ID”。 

> [!NOTE]
> 考虑使用本示例中所示的提示符。 这样，密码就不会显示在控制台的命令历史记录中。 

<a id="managed-identity" />

#### <a name="authenticate-a-managed-identity"></a>对托管标识进行身份验证

如果你打算在无需用户交互即可运行的脚本以及从 Azure 虚拟机 (VM) 运行的脚本中使用 AzCopy，此选项非常有用。 使用此选项时，无需在 VM 上存储任何凭据。

可以使用已在 VM 上启用的系统范围托管标识登录到帐户，或者，使用已分配到 VM 的、用户分配的托管标识的客户端 ID、对象 ID 或资源 ID 登录到帐户。

若要详细了解如何启用系统范围的托管标识或创建用户分配的托管标识，请参阅[使用 Azure 门户为 VM 上的 Azure 资源配置托管标识](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)。

##### <a name="using-a-system-wide-managed-identity"></a>使用系统范围的托管标识

首先，确保已在 VM 上启用系统范围的托管标识。 请参阅[系统分配的托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity)。

然后，在命令控制台中，键入以下命令并按 ENTER 键。

```azcopy
azcopy login --identity
```

##### <a name="using-a-user-assigned-managed-identity"></a>使用用户分配的托管标识

首先，确保已在 VM 上启用用户分配的托管标识。 请参阅[用户分配的托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#user-assigned-managed-identity)。

然后，在命令控制台中，键入以下任意命令并按 ENTER 键。

```azcopy
azcopy login --identity --identity-client-id "<client-id>"
```

请将 `<client-id>` 占位符替换为用户分配的托管标识的客户端 ID。

```azcopy
azcopy login --identity --identity-object-id "<object-id>"
```

请将 `<object-id>` 占位符替换为用户分配的托管标识的对象 ID。

```azcopy
azcopy login --identity --identity-resource-id "<resource-id>"
```

请将 `<resource-id>` 占位符替换为用户分配的托管标识的资源 ID。

### <a name="option-2-use-a-sas-token"></a>选项 2：使用 SAS 令牌

可将 SAS 令牌追加到在 AzCopy 命令中使用的每个源或目标 URL。

此示例命令以递归方式将本地目录中的数据复制到 Blob 容器。 一个虚构的 SAS 令牌已追加到容器 URL 的末尾。

```azcopy
azcopy copy "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

若要详细了解 SAS 令牌及其获取方式，请参阅[使用共享访问签名 (SAS)](https://docs.microsoft.com/azure/storage/common/storage-sas-overview)。

## <a name="transfer-files"></a>传输文件

对标识进行验证身份或获取 SAS 令牌后，可以开始传输文件。

若要查找示例命令，请参阅以下文章中的任何一篇。

- [使用 AzCopy 和 Blob 存储传输数据](storage-use-azcopy-blobs.md)

- [使用 AzCopy 和文件存储传输数据](storage-use-azcopy-files.md)

- [使用 AzCopy 和 Amazon S3 Bucket 传输数据](storage-use-azcopy-s3.md)

- [使用 AzCopy 和 Azure Stack 存储传输数据](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-transfer#azcopy)

## <a name="use-azcopy-in-a-script"></a>在脚本中使用 AzCopy

### <a name="obtain-a-static-download-link"></a>获取静态下载链接

在一段时间后，AzCopy [下载链接](#download-and-install-azcopy)将指向新版 AzCopy。 如果脚本会下载 AzCopy，而更高版本的 AzCopy 会修改该脚本所依赖的功能，则该脚本可能会停止工作。

若要避免此类问题，请获取当前 AzCopy 版本的静态（不会更改）链接。 这样，每次运行脚本时，它都会下载相同的 AzCopy 版本。

若要获取该链接，请运行以下命令：

| 操作系统  | 命令 |
|--------|-----------|
| **Linux** | `curl -s -D- https://aka.ms/downloadazcopy-v10-linux | grep ^Location` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).headers.location` |

> [!NOTE]
> 对于 Linux，`tar` 命令中的 `--strip-components=1` 会删除包含版本名称的顶级文件夹，并改为将二进制文件直接提取到当前文件夹中。 这样，只需要更新 `wget` URL，即可使用 `azcopy` 的新版本更新脚本。

该 URL 显示在此命令的输出中。 然后，脚本可以使用该 URL 下载 AzCopy。

| 操作系统  | 命令 |
|--------|-----------|
| **Linux** | `wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

### <a name="escape-special-characters-in-sas-tokens"></a>转义 SAS 令牌中的特殊字符

在扩展名为 `.cmd` 的批处理文件中，必须转义 SAS 令牌中显示的 `%` 字符。 可以通过在 SAS 令牌字符串中的`%`现有`%`字符旁添加其他字符来实现此目的。

### <a name="run-scripts-by-using-jenkins"></a>使用 Jenkins 运行脚本

如果你打算使用 [Jenkins](https://jenkins.io/) 运行脚本，请确保将以下命令放在脚本的开头。

```
/usr/bin/keyctl new_session
```

## <a name="use-azcopy-in-azure-storage-explorer"></a>在 Azure 存储资源管理器中使用 AzCopy

[存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)使用 AzCopy 来执行其所有数据传输操作。 如果你想要利用 AzCopy 的性能优势，但同时又偏好使用图形用户界面而不是命令行来与文件进行交互，则可以使用[存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)。

存储资源管理器使用帐户密钥执行操作，因此在登录到存储资源管理器后，无需额外提供授权凭据。

<a id="previous-version" />

## <a name="use-the-previous-version-of-azcopy"></a>使用旧版 AzCopy

如果需要使用早期版本的 AzCopy，请参阅以下链接之一：

- [Windows 上的 AzCopy (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy)

- [Linux 上的 AzCopy （v7）](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="configure-optimize-and-troubleshoot-azcopy"></a>对 AzCopy 进行配置、优化和故障排除

请参阅[对 AzCopy 进行配置、优化和故障排除](storage-use-azcopy-configure.md)

## <a name="next-steps"></a>后续步骤

如有任何疑问、问题或一般反馈，请在 [GitHub](https://github.com/Azure/azure-storage-azcopy) 页上提交。
