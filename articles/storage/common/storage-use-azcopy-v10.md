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
ms.openlocfilehash: 94aca33b2f12c1c39297221a856296dcca052b0f
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565802"
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

> [!NOTE] 
> 作为你的 Azure 存储帐户的所有者，不会自动分配权限来访问数据。 您可以执行任何意义，使用 AzCopy 操作之前，您需要决定将提供到存储服务的授权凭据的方式。 

## <a name="choose-how-youll-provide-authorization-credentials"></a>选择将提供授权凭据的方式

使用 Azure Active Directory (AD)，或使用共享访问签名 (SAS) 令牌，可以提供授权凭据。

使用此表作为指南：

| 存储类型 | 当前受支持的方法的授权 |
|--|--|
|**Blob 存储** | Azure AD & SAS |
|**Blob 存储 （分层命名空间）** | Azure AD & SAS |
|**文件存储** | 仅 SAS |

### <a name="option-1-use-azure-ad"></a>选项 1：使用 Azure AD

通过使用 Azure AD，可以提供一次而无需将 SAS 令牌追加到每个命令的凭据。  

所需的授权级别取决于你是否计划将文件上传或只需下载它们。

如果只是想要下载文件，然后确认[存储 Blob 数据读取器](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader)已分配给你的用户标识或服务主体。 

> [!NOTE]
> 用户标识和服务主体是每一种*安全主体*，因此我们将使用术语*安全主体*这篇文章的其余部分。

如果你想要将文件上传，然后验证，这些角色之一的已分配给安全主体：

- [存储 Blob 数据参与者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [存储 Blob 数据所有者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

这些角色可以分配给你在任何这些作用域中的身份：

- 容器 （文件系统）
- 存储帐户
- 资源组
- 订阅

若要了解如何验证并分配角色，请参阅[授予对 Azure blob 和队列数据使用 RBAC 在 Azure 门户中访问](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

> [!NOTE] 
> 请记住，RBAC 角色分配可能需要最多五分钟的时间进行传播。

不需要有一个分配给安全主体，如果安全主体添加到目标容器或目录的访问控制列表 (ACL) 这些角色。 在 ACL 中你的安全主体需要在目标目录中，写入权限和容器和每个父目录上执行权限。

若要了解详细信息，请参阅[Azure 数据湖存储第 2 代中的访问控制](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。

#### <a name="authenticate-a-user-identity"></a>对用户标识进行身份验证

验证您的用户标识已被授予必要的授权级别后，打开命令提示符、 键入以下命令，然后按 ENTER 键。

```azcopy
azcopy login
```

如果您属于多个组织，包括存储帐户所属的组织的租户 ID。

```azcopy
azcopy login --tenant-id=<tenant-id>
```

替换为`<tenant-id>`与存储帐户所属的组织的租户 ID 的占位符。 若要查找租户 ID，请选择**Azure Active Directory > 属性 > 目录 ID**在 Azure 门户中。

此命令返回身份验证代码和网站的 URL。 打开网站，提供代码，然后选择“下一步”按钮。 

![创建容器](media/storage-use-azcopy-v10/azcopy-login.png)

此时会出现登录窗口。 在该窗口中，使用 Azure 帐户凭据登录到 Azure 帐户。 成功登录后，可以关闭浏览器窗口，开始使用 AzCopy。

<a id="service-principal" />

#### <a name="authenticate-a-service-principal"></a>服务主体进行身份验证

如果你打算在运行无需用户交互的脚本内使用 AzCopy，这是很好的选择。 

在运行该脚本之前，必须登录以交互方式至少一次，以便可以将 AzCopy 提供的服务主体凭据。  这些凭据存储在受保护和加密文件，以便脚本无需提供该敏感信息。

通过使用客户端密码或通过使用与服务主体的应用注册相关联的证书的密码，可以登录到你的帐户。 

若要了解有关创建服务主体的详细信息，请参阅[如何：使用门户创建可访问资源的 Azure AD 应用程序和服务主体](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)。

若要在一般情况下，了解有关服务主体的详细信息，请参阅[应用程序和 Azure Active Directory 中的服务主体对象](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)

##### <a name="using-a-client-secret"></a>使用客户端机密

首先设置`AZCOPY_SPA_CLIENT_SECRET`环境变量为你的服务主体的客户端机密的应用注册。 

> [!NOTE]
> 请确保将此值设置从命令提示符，并不在环境变量设置的操作系统。 这样一来，值为仅供当前会话。

此示例演示如何可以在 PowerShell 中此。

```azcopy
$env:AZCOPY_SPA_CLIENT_SECRET="$(Read-Host -prompt "Enter key")"
```

> [!NOTE]
> 请考虑使用一条提示，在此示例中所示。 这样一来，客户端密钥不会显示在控制台的命令历史记录。 

接下来，键入以下命令，然后按 ENTER 键。

```azcopy
azcopy login --service-principal --application-id <application-id>
```

替换为`<application-id>`服务主体的应用程序 id 的占位符的应用注册。

##### <a name="using-a-certificate"></a>使用证书

如果想要使用您自己的凭据进行授权，您可以将证书上传到应用注册，以及如何将登录到该证书。

除了将证书上传到应用注册，还需要有一份证书保存到计算机或 VM 运行 AzCopy。 应为此证书的副本。PFX 或。PEM 格式，并且必须包含私钥。 应将私钥受密码保护。 如果您使用的 Windows，并仅在证书存储区中存在证书，请确保该证书导出到 PFX 文件 （包括私钥）。 有关指南，请参阅[Export-pfxcertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate?view=win10-ps)

接下来，设置`AZCOPY_SPA_CERT_PASSWORD`证书密码的环境变量。

> [!NOTE]
> 请确保将此值设置从命令提示符，并不在环境变量设置的操作系统。 这样一来，值为仅供当前会话。

此示例演示如何可以在 PowerShell 中此。

```azcopy
$env:AZCOPY_SPA_CERT_PASSWORD="$(Read-Host -prompt "Enter key")"
```

接下来，键入以下命令，然后按 ENTER 键。

```azcopy
azcopy login --service-principal --certificate-path <path-to-certificate-file>
```

替换为`<path-to-certificate-file>`占位符替换为证书文件的相对或完全限定路径。 AzCopy 将路径保存到此证书，但它不会保存一份证书，因此请确保将该证书保存在位置。

> [!NOTE]
> 请考虑使用一条提示，在此示例中所示。 这样一来，你的密码不会显示在控制台的命令历史记录。 

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

- [使用 AzCopy 和 Azure Stack 存储传输数据](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-transfer#azcopy)

## <a name="use-azcopy-in-a-script"></a>在脚本中使用 AzCopy

在运行该脚本之前，必须登录以交互方式至少一次，以便可以将 AzCopy 提供的服务主体凭据。  这些凭据存储在受保护和加密文件，以便脚本无需提供该敏感信息。 有关示例，请参阅[进行身份验证服务主体](#service-principal)本文的部分。

随着时间推移，AzCopy[下载链接](#download-and-install-azcopy)将指向新版本的 AzCopy。 如果您的脚本下载 AzCopy，脚本可能会停止工作如果较新版本的 AzCopy 修改取决于您的脚本的功能。 

若要避免这些问题，获取当前版本的 AzCopy 的静态 （未更改） 链接。 这样一来，您的脚本下载完全相同的版本的 AzCopy 运行每个时间。

若要获取的链接，请运行以下命令：

| 操作系统  | 命令 |
|--------|-----------|
| **Linux** | `curl -v https://aka.ms/downloadazcopy-v10-linux` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).RawContent` |

> [!NOTE]
> 对于 Linux，`--strip-components=1`上`tar`命令将删除包含的版本名称，并改为在当前文件夹中直接提取二进制文件的顶级文件夹。 这样，要使用的新版本更新的脚本`azcopy`通过仅更新`wget`URL。

URL 显示在此命令的输出。 您的脚本然后可以使用该 URL 下载 AzCopy。

| 操作系统  | 命令 |
|--------|-----------|
| **Linux** | `wget -O azcopyv10.tar https://azcopyvnext.azureedge.net/release20190301/azcopy_linux_amd64_10.0.8.tar.gz tar -xf azcopyv10.tar --strip-components=1 ./azcopy` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

## <a name="use-azcopy-in-storage-explorer"></a>在存储资源管理器中使用 AzCopy

如果你想要利用的 AzCopy，性能优势，但想要使用存储资源管理器而不是命令行以与你的文件，然后启用 AzCopy 在存储资源管理器。 

在存储资源管理器中选择**预览版**->**改进了 Blob 上传和下载使用 AzCopy**。

![在 Azure 存储资源管理器中启用 AzCopy 作为传输引擎](media/storage-use-azcopy-v10/enable-azcopy-storage-explorer.jpg)

> [!NOTE]
> 您无需启用此设置，如果已在存储帐户上启用层次结构的命名空间。 这是因为存储资源管理器会自动使用 AzCopy 在已分层命名空间的存储帐户。  

存储资源管理器使用你的帐户密钥来执行操作，因此登录到存储资源管理器后，您无需提供额外的授权凭据。

<a id="previous-version" />

## <a name="use-the-previous-version-of-azcopy"></a>使用以前版本的 AzCopy

如果您需要使用以前版本的 AzCopy (AzCopy v8.1)，请参阅以下链接之一：

- [AzCopy on Windows (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy)

- [AzCopy on Linux (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="configure-optimize-and-troubleshoot-azcopy"></a>配置、 优化和故障排除 AzCopy

请参阅[配置，优化和故障排除 AzCopy](storage-use-azcopy-configure.md)

## <a name="next-steps"></a>后续步骤

如果你有疑问、 问题或常规反馈，将其提交[GitHub 上](https://github.com/Azure/azure-storage-azcopy)页。
