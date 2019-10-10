---
title: 使用 AzCopy v10 将数据复制或移到 Azure 存储 | Microsoft Docs
description: AzCopy 是一个命令行实用工具，可用于在存储帐户之间复制数据。 本文将帮助你下载 AzCopy，连接到你的存储帐户，然后传输文件。
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 980156284b27478163760b1f833a91ba7cddec21
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244785"
---
# <a name="get-started-with-azcopy"></a>AzCopy 入门

AzCopy 是一个命令行实用工具，可用于在存储帐户中复制 blob 或文件。 本文将帮助你下载 AzCopy，连接到你的存储帐户，然后传输文件。

> [!NOTE]
> AzCopy **V10**是当前支持的 AzCopy 版本。
>
> 如果需要使用 AzCopy 的 **，请参阅**本文的[使用 AzCopy 的早期版本](#previous-version)部分。

<a id="download-and-install-azcopy" />

## <a name="download-azcopy"></a>下载 AzCopy

首先，将 AzCopy V10 可执行文件下载到计算机上的任何目录中。 AzCopy V10 只是一个可执行文件，因此没有要安装的内容。

- [Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

这些文件被压缩为 zip 文件（Windows 和 Mac）或 tar 文件（Linux）。

你可以使用这些命令在 Linux 上下载并解压缩 tar 文件。

```bash
wget -O azcopy.tar.gz https://aka.ms/downloadazcopy-v10-linux
tar -xf azcopy.tar.gz
```

> [!NOTE]
> 如果要将数据复制到[Azure 表存储服务和从 Azure 表存储](https://docs.microsoft.com/azure/storage/tables/table-storage-overview)服务复制数据，请安装[AzCopy 版本 7.3](https://aka.ms/downloadazcopynet)。


## <a name="run-azcopy"></a>运行 AzCopy

为方便起见，请考虑将 AzCopy 可执行文件的目录位置添加到系统路径，以方便使用。 这样，你就可以在系统上的任何目录中键入 `azcopy`。

如果选择不将 AzCopy 目录添加到路径，则必须将目录更改为 AzCopy 可执行文件的位置，然后在 Windows PowerShell 命令提示符中键入 `azcopy` 或 `.\azcopy`。

若要查看命令列表，请键入 `azcopy -h`，然后按 ENTER 键。

若要了解特定命令，只需包含命令的名称（例如： `azcopy list -h`）。

![内联帮助](media/storage-use-azcopy-v10/azcopy-inline-help.png)

> [!NOTE] 
> 作为你的 Azure 存储帐户的所有者，你不会自动分配访问数据的权限。 你需要决定如何向存储服务提供身份验证凭据，然后才能对 AzCopy 执行任何有意义的操作。 

## <a name="choose-how-youll-provide-authorization-credentials"></a>选择如何提供授权凭据

可以通过使用 Azure Active Directory （AD）或使用共享访问签名（SAS）令牌提供授权凭据。

使用此表作为指南：

| 存储类型 | 当前支持的授权方法 |
|--|--|
|**Blob 存储** | Azure AD & SAS |
|**Blob 存储（分层命名空间）** | Azure AD & SAS |
|**文件存储** | 仅 SAS |

### <a name="option-1-use-azure-active-directory"></a>选项 1：使用 Azure Active Directory

通过使用 Azure Active Directory，你可以提供一次凭据，而无需向每个命令追加 SAS 令牌。  

> [!NOTE]
> 在当前版本中，如果计划在存储帐户之间复制 blob，则必须向每个源 URL 追加一个 SAS 令牌。 只能从目标 URL 中省略 SAS 令牌。 有关示例，请参阅[在存储帐户之间复制 blob](storage-use-azcopy-blobs.md)。

你需要的授权级别取决于你是要上载文件还是只是下载文件。

如果你只想下载文件，请验证是否已将[存储 Blob 数据读取器](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader)分配给你的用户标识、托管标识或服务主体。

> 用户标识、托管标识和服务主体都是一种*安全主体*，因此我们将在本文的其余部分中使用术语 "*安全主体*"。

如果要上传文件，请验证是否已将其中一个角色分配给了安全主体：

- [存储 Blob 数据参与者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [存储 Blob 数据所有者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

可以将这些角色分配到以下任何作用域中的安全主体：

- 容器（文件系统）
- 存储帐户
- 资源组
- 订阅

若要了解如何验证和分配角色，请参阅[在 Azure 门户中使用 RBAC 授予对 Azure blob 和队列数据的访问权限](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

> [!NOTE]
> 请记住，RBAC 角色分配可能需要长达五分钟才能传播。

如果将安全主体添加到目标容器或目录的访问控制列表（ACL），则无需将这些角色中的一个分配给安全主体。 在 ACL 中，安全主体需要对目标目录具有写入权限，并且对容器和每个父目录具有 execute 权限。

若要了解详细信息，请参阅[Azure Data Lake Storage Gen2 中的访问控制](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。

#### <a name="authenticate-a-user-identity"></a>对用户标识进行身份验证

验证用户标识是否已获得所需的授权级别后，打开命令提示符，键入以下命令，然后按 ENTER 键。

```azcopy
azcopy login
```

如果你属于多个组织，请包括存储帐户所属组织的租户 ID。

```azcopy
azcopy login --tenant-id=<tenant-id>
```

将 `<tenant-id>` 占位符替换为存储帐户所属组织的租户 ID。 若要查找租户 ID，请在 Azure 门户中选择 " **Azure Active Directory > 属性 > 目录 ID** "。

此命令返回身份验证代码和网站的 URL。 打开网站，提供代码，然后选择“下一步”按钮。

![创建容器](media/storage-use-azcopy-v10/azcopy-login.png)

此时会出现登录窗口。 在该窗口中，使用 Azure 帐户凭据登录到 Azure 帐户。 成功登录后，可以关闭浏览器窗口，开始使用 AzCopy。

<a id="service-principal" />

#### <a name="authenticate-a-service-principal"></a>对服务主体进行身份验证

如果你计划在运行无需用户交互的脚本中使用 AzCopy，尤其是在本地运行时，这是一个很好的选择。 如果计划在 Azure 中运行的 Vm 上运行 AzCopy，托管服务标识将更易于管理。 若要了解详细信息，请参阅本文的对[托管标识进行身份验证](#managed-identity)部分。

运行脚本之前，必须至少以交互方式登录一次，以便可以向 AzCopy 提供服务主体的凭据。  这些凭据存储在受保护的加密文件中，因此您的脚本无需提供敏感信息。

你可以使用客户端机密或使用与服务主体的应用注册关联的证书的密码登录到你的帐户。

若要了解有关创建服务主体的详细信息，请参阅 [How to：使用门户创建可访问资源的 Azure AD 应用程序和服务主体](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)。

若要了解有关服务主体的详细信息，请参阅[Azure Active Directory 中的应用程序和服务主体对象](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)

##### <a name="using-a-client-secret"></a>使用客户端机密

首先将 `AZCOPY_SPA_CLIENT_SECRET` 环境变量设置为服务主体的应用注册的客户端机密。

> [!NOTE]
> 请确保在命令提示符下设置此值，而不是在操作系统的环境变量设置中设置。 这样，该值仅对当前会话可用。

此示例演示如何在 PowerShell 中执行此操作。

```azcopy
$env:AZCOPY_SPA_CLIENT_SECRET="$(Read-Host -prompt "Enter key")"
```

> [!NOTE]
> 请考虑使用本示例中所示的提示。 这样，你的密码就不会出现在控制台的命令历史记录中。  

接下来，键入以下命令，然后按 ENTER 键。

```azcopy
azcopy login --service-principal --application-id <application-id> --tenant-id=<tenant-id>
```

将 `<application-id>` 占位符替换为服务主体的应用注册的应用程序 ID。 将 `<tenant-id>` 占位符替换为存储帐户所属组织的租户 ID。 若要查找租户 ID，请在 Azure 门户中选择 " **Azure Active Directory > 属性 > 目录 ID** "。 

##### <a name="using-a-certificate"></a>使用证书

如果你想要使用自己的凭据进行授权，则可以将证书上传到应用注册，然后使用该证书登录。

除了将证书上传到应用注册，还需要将证书副本保存到运行 AzCopy 的计算机或 VM。 此证书副本应在中。PFX 或。PEM 格式，必须包含私钥。 私钥应受密码保护。 如果使用的是 Windows，且证书仅存在于证书存储中，请确保将该证书导出到 PFX 文件（包括私钥）。 有关指南，请参阅[get-pfxcertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate?view=win10-ps)

接下来，将 `AZCOPY_SPA_CERT_PASSWORD` 环境变量设置为证书密码。

> [!NOTE]
> 请确保在命令提示符下设置此值，而不是在操作系统的环境变量设置中设置。 这样，该值仅对当前会话可用。

此示例演示如何在 PowerShell 中执行此任务。

```azcopy
$env:AZCOPY_SPA_CERT_PASSWORD="$(Read-Host -prompt "Enter key")"
```

接下来，键入以下命令，然后按 ENTER 键。

```azcopy
azcopy login --service-principal --certificate-path <path-to-certificate-file> --tenant-id=<tenant-id>
```

将 @no__t 0 占位符替换为证书文件的相对或完全限定路径。 AzCopy 保存此证书的路径，但它并不保存证书的副本，因此请确保将该证书保留下来。 将 `<tenant-id>` 占位符替换为存储帐户所属组织的租户 ID。 若要查找租户 ID，请在 Azure 门户中选择 " **Azure Active Directory > 属性 > 目录 ID** "。

> [!NOTE]
> 请考虑使用本示例中所示的提示。 这样，你的密码就不会出现在控制台的命令历史记录中。 

<a id="managed-identity" />

#### <a name="authenticate-a-managed-identity"></a>对托管标识进行身份验证

如果计划在运行无需用户交互的脚本中使用 AzCopy，并且该脚本从 Azure 虚拟机（VM）运行，则这是一个不错的选择。 使用此选项时，不需要在 VM 上存储任何凭据。

你可以使用已在 VM 上启用的系统范围的托管标识登录到你的帐户，也可以使用已分配给 VM 的用户分配的托管标识的客户端 ID、对象 ID 或资源 ID 登录到你的帐户。

若要了解有关如何启用系统范围的托管标识或创建用户分配的托管标识的详细信息，请参阅[使用 Azure 门户在虚拟机上配置 Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)。

##### <a name="using-a-system-wide-managed-identity"></a>使用系统范围的托管标识

首先，请确保已在 VM 上启用了系统范围的托管标识。 请参阅[系统分配的托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity)。

然后，在命令控制台中，键入以下命令，然后按 ENTER 键。

```azcopy
azcopy login --identity
```

##### <a name="using-a-user-assigned-managed-identity"></a>使用用户分配的托管标识

首先，请确保已在 VM 上启用用户分配的托管标识。 请参阅[用户分配的托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#user-assigned-managed-identity)。

然后，在命令控制台中，键入以下任意命令，然后按 ENTER 键。

```azcopy
azcopy login --identity --identity-client-id "<client-id>"
```

将 `<client-id>` 占位符替换为用户分配的托管标识的客户端 ID。

```azcopy
azcopy login --identity --identity-object-id "<object-id>"
```

将 `<object-id>` 占位符替换为用户分配的托管标识的对象 ID。

```azcopy
azcopy login --identity --identity-resource-id "<resource-id>"
```

将 `<resource-id>` 占位符替换为用户分配的托管标识的资源 ID。

### <a name="option-2-use-a-sas-token"></a>选项 2：使用 SAS 令牌

可以将 SAS 令牌追加到在 AzCopy 命令中使用的每个源或目标 URL。

此示例命令将数据从本地目录递归复制到 blob 容器。 将一个虚构的 SAS 令牌追加到容器 URL 的末尾。

```azcopy
azcopy copy "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

若要了解有关 SAS 令牌以及如何获取 SAS 令牌的详细信息，请参阅[使用共享访问签名（SAS）](https://docs.microsoft.com/azure/storage/common/storage-sas-overview)。

## <a name="transfer-files"></a>传输文件

验证身份或获取 SAS 令牌后，可以开始传输文件。

若要查找示例命令，请参阅这些文章中的任何一篇。

- [使用 AzCopy 和 Blob 存储传输数据](storage-use-azcopy-blobs.md)

- [使用 AzCopy 和文件存储传输数据](storage-use-azcopy-files.md)

- [使用 AzCopy 和 Amazon S3 Bucket 传输数据](storage-use-azcopy-s3.md)

- [通过 AzCopy 和 Azure Stack 存储传输数据](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-transfer#azcopy)

## <a name="use-azcopy-in-a-script"></a>在脚本中使用 AzCopy

### <a name="obtain-a-static-download-link"></a>获取静态下载链接

随着时间的推移，AzCopy[下载链接](#download-and-install-azcopy)将指向新版本的 AzCopy。 如果脚本下载 AzCopy，则在较新版本的 AzCopy 修改脚本所依赖的功能时，脚本可能会停止工作。

若要避免这些问题，请获取 AzCopy 当前版本的静态（无变化）链接。 这样一来，你的脚本每次运行时都将下载相同的 AzCopy 版本。

若要获取该链接，请运行以下命令：

| 操作系统  | Command |
|--------|-----------|
| **Linux** | `curl -v https://aka.ms/downloadazcopy-v10-linux` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).RawContent` |

> [!NOTE]
> 对于 Linux，`tar` 命令 `--strip-components=1` 删除包含版本名称的顶层文件夹，而改为将二进制文件直接提取到当前文件夹中。 这样，只需要更新 @no__t URL，就可以使用 @no__t 的新版本更新脚本。

该 URL 显示在此命令的输出中。 然后，你的脚本可以通过使用该 URL 来下载 AzCopy。

| 操作系统  | Command |
|--------|-----------|
| **Linux** | `wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

### <a name="escape-special-characters-in-sas-tokens"></a>转义 SAS 令牌中的特殊字符

在具有 `.cmd` 扩展的批处理文件中，必须对 SAS 令牌中显示的 `%` 字符进行转义。 为此，可以在 SAS 令牌字符串中的现有 `%` 字符旁添加一个加法 `%` 字符。

## <a name="use-azcopy-in-storage-explorer"></a>在存储资源管理器中使用 AzCopy

如果要利用 AzCopy 的性能优势，但更喜欢使用存储资源管理器而不是命令行来与文件交互，请在存储资源管理器中启用 AzCopy。

在存储资源管理器中，选择 "**预览**" ->**使用 AzCopy 进行改进的 Blob 上传和下载**。

![在 Azure 存储资源管理器中启用 AzCopy 作为传输引擎](media/storage-use-azcopy-v10/enable-azcopy-storage-explorer.jpg)

> [!NOTE]
> 如果在存储帐户上启用了分层命名空间，则不需要启用此设置。 这是因为存储资源管理器会在具有分层命名空间的存储帐户上自动使用 AzCopy。  

存储资源管理器使用帐户密钥来执行操作，因此登录到存储资源管理器后，无需提供其他授权凭据。

<a id="previous-version" />

## <a name="use-the-previous-version-of-azcopy"></a>使用以前版本的 AzCopy

如果需要使用早期版本的 AzCopy （AzCopy），请参阅以下链接之一：

- [Windows 上的 AzCopy （v8）](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy)

- [Linux 上的 AzCopy （v8）](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="configure-optimize-and-troubleshoot-azcopy"></a>配置、优化 AzCopy 并对其进行故障排除

请参阅[配置、优化 AzCopy 并对其进行故障排除](storage-use-azcopy-configure.md)

## <a name="next-steps"></a>后续步骤

如果你有疑问、问题或一般性反馈，请[在 GitHub 页上](https://github.com/Azure/azure-storage-azcopy)提交。
