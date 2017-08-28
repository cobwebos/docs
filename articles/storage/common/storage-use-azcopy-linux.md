---
title: "使用 AzCopy on Linux 将数据复制或移到 Azure 存储 | Microsoft Docs"
description: "使用 AzCopy on Linux 实用工具将数据移动或复制到 Blob 和文件内容或从 Blob 和文件内容移动或复制数据。 从本地文件将数据复制到 Azure 存储，或者在存储帐户中或存储帐户之间复制数据。 轻松地将数据迁移到 Azure 存储。"
services: storage
documentationcenter: 
author: seguler
manager: jahogg
editor: tysonn
ms.assetid: aa155738-7c69-4a83-94f8-b97af4461274
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2017
ms.author: seguler
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 441227d84b9c1ec721ae36fdc423ba797654f128
ms.contentlocale: zh-cn
ms.lasthandoff: 08/22/2017

---
# <a name="transfer-data-with-azcopy-on-linux"></a>使用 AzCopy on Linux 传输数据
AzCopy on Linux 是一个命令行实用工具，专用于使用具有优化性能的简单命令将数据复制到 Microsoft Azure Blob 和文件存储以及从这些位置复制数据。 可在存储帐户中将数据从一个对象复制到另一个对象，或者在存储帐户之间复制。

有两种版本的 AzCopy 可供下载。 AzCopy on Linux 是使用 .NET Core Framework 构建的，适用于 Linux 平台，并提供 POSIX 风格的命令行选项。 [AzCopy on Windows](../storage-use-azcopy.md) 是使用 .NET Framework 构建的，并提供 Windows 风格的命令行选项。 本文涉及到 AzCopy on Linux。

## <a name="download-and-install-azcopy"></a>下载并安装 AzCopy
### <a name="installation-on-linux"></a>在 Linux 上安装

AzCopy on Linux 要求在平台上安装 .NET Core 框架。 请参阅 [.NET Core](https://www.microsoft.com/net/core#linuxubuntu) 页上的安装说明。

例如，让我们在 Ubuntu 16.10 上安装 .NET Core。 有关最新的安装指南，请访问 [Linux 上的 .NET Core](https://www.microsoft.com/net/core#linuxubuntu) 安装页。


```bash
sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ yakkety main" > /etc/apt/sources.list.d/dotnetdev.list' 
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
sudo apt-get update
sudo apt-get install dotnet-dev-1.0.3
```

安装 .NET Core 后，请下载并安装 AzCopy。

```bash
wget -O azcopy.tar.gz https://aka.ms/downloadazcopyprlinux
tar -xf azcopy.tar.gz
sudo ./install.sh
```

安装 AzCopy on Linux 后，可以删除提取的文件。 如果你没有超级用户特权，也可以在提取的文件夹中使用 shell 脚本“azcopy”运行 AzCopy。 

### <a name="alternative-installation-on-ubuntu"></a>Ubuntu 上的替代安装

**Ubuntu 14.04**

添加 .Net Core 的 apt 源：

```bash
sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ trusty main" > /etc/apt/sources.list.d/dotnetdev.list' 
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
```

添加 Microsoft Linux 产品存储库的 apt 源并安装 AzCopy：

```bash
curl https://packages.microsoft.com/config/ubuntu/14.04/prod.list > ./microsoft-prod.list
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

```bash
sudo apt-get update
sudo apt-get install azcopy
```

**Ubuntu 16.04**

添加 .Net Core 的 apt 源：

```bash
sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ xenial main" > /etc/apt/sources.list.d/dotnetdev.list' 
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
```

添加 Microsoft Linux 产品存储库的 apt 源并安装 AzCopy：

```bash
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > ./microsoft-prod.list
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

```bash
sudo apt-get update
sudo apt-get install azcopy
```

**Ubuntu 16.10**

添加 .Net Core 的 apt 源：

```bash
sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ yakkety main" > /etc/apt/sources.list.d/dotnetdev.list' 
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
```

添加 Microsoft Linux 产品存储库的 apt 源并安装 AzCopy：

```bash
curl https://packages.microsoft.com/config/ubuntu/16.10/prod.list > ./microsoft-prod.list
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

```bash
sudo apt-get update
sudo apt-get install azcopy
```

## <a name="writing-your-first-azcopy-command"></a>编写第一条 AzCopy 命令
AzCopy 命令的基本语法是：

```azcopy
azcopy --source <source> --destination <destination> [Options]
```

以下示例演示将数据复制到 Microsoft Azure Blob 和文件以及从这些位置复制数据的各种情况。 请参考 `azcopy --help` 菜单了解每个示例中使用的参数的详细说明。

## <a name="blob-download"></a>Blob：下载
### <a name="download-single-blob"></a>下载单个 blob

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --include "abc.txt"
```

如果文件夹 `/mnt/myfiles` 不存在，AzCopy 会创建该文件夹并将 `abc.txt ` 下载到新文件夹中。

### <a name="download-single-blob-from-secondary-region"></a>从次要区域下载单个 blob

```azcopy
azcopy \
    --source https://myaccount-secondary.blob.core.windows.net/mynewcontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --include "abc.txt"
```

请注意，必须已启用读取访问异地冗余存储。

### <a name="download-all-blobs"></a>下载所有 blob

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --recursive
```

假定指定的容器中存在以下 blob：  

```
abc.txt
abc1.txt
abc2.txt
vd1/a.txt
vd1/abcd.txt
```

在下载操作完成后，目录 `/mnt/myfiles` 中将包含以下文件：

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
/mnt/myfiles/vd1/a.txt
/mnt/myfiles/vd1/abcd.txt
```

如果未指定选项 `--recursive`，则不会下载任何 Blob。

### <a name="download-blobs-with-specified-prefix"></a>下载具有指定前缀的 blob

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --include "a" \
    --recursive
```

假定指定的容器中存在以下 blob。 将下载所有以前缀 `a` 开头的 Blob。

```
abc.txt
abc1.txt
abc2.txt
xyz.txt
vd1\a.txt
vd1\abcd.txt
```

下载操作完成后，文件夹 `/mnt/myfiles` 中将包含以下文件：

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
```

前缀适用于虚拟目录，后者构成了 blob 名称的第一个部分。 在上面所示的示例中，虚拟目录与指定的前缀不匹配，因此不会下载 Blob。 此外，如果未指定选项 `--recursive`，则 AzCopy 不会下载任何 Blob。

### <a name="set-the-last-modified-time-of-exported-files-to-be-same-as-the-source-blobs"></a>将已导出文件的上次修改时间设置为与源 blob 相同

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination "/mnt/myfiles" \
    --source-key <key> \
    --preserve-last-modified-time
```

还可以根据 blob 的上次修改时间将其从下载操作中排除 例如，如果想要排除其上次修改时间与目标文件相同或晚于目标文件的 blob，则添加 `--exclude-newer` 选项：

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --preserve-last-modified-time \
    --exclude-newer
```

或者，如果想要排除其上次修改时间与目标文件相同或早于目标文件的 blob，则添加 `--exclude-older` 选项：

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --preserve-last-modified-time \
    --exclude-older
```

## <a name="blob-upload"></a>Blob：上传
### <a name="upload-single-file"></a>上传单个文件

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --include "abc.txt"
```

如果指定的目标容器不存在，AzCopy 将创建它并将文件上传到其中。

### <a name="upload-single-file-to-virtual-directory"></a>将单个文件上传到虚拟目录

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --include "abc.txt"
```

如果指定的虚拟目录不存在，AzCopy 将上传文件以在 Blob 名称中包含虚拟目录（例如上例中的 `vd/abc.txt`）。

### <a name="upload-all-files"></a>上传全部文件

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --recursive
```

指定选项 `--recursive` 会以递归方式将指定目录的内容上传到 Blob 存储，这意味着也会上传所有的子文件夹及其文件。 例如，假定以下文件存在于文件夹 `/mnt/myfiles` 中：

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
/mnt/myfiles/subfolder/a.txt
/mnt/myfiles/subfolder/abcd.txt
```

上传操作完成后，容器中将包含以下文件：

```
abc.txt
abc1.txt
abc2.txt
subfolder/a.txt
subfolder/abcd.txt
```

如果未指定选项 `--recursive`，则只会上传以下三个文件：

```
abc.txt
abc1.txt
abc2.txt
```

### <a name="upload-files-matching-specified-pattern"></a>上传与指定模式相匹配的文件

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --include "a*" \
    --recursive
```

假定以下文件存在于文件夹 `/mnt/myfiles` 中：

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
/mnt/myfiles/xyz.txt
/mnt/myfiles/subfolder/a.txt
/mnt/myfiles/subfolder/abcd.txt
```

上传操作完成后，容器中将包含以下文件：

```
abc.txt
abc1.txt
abc2.txt
subfolder/a.txt
subfolder/abcd.txt
```

如果未指定选项 `--recursive`，AzCopy 将跳过子目录中的文件：

```
abc.txt
abc1.txt
abc2.txt
```

### <a name="specify-the-mime-content-type-of-a-destination-blob"></a>指定目标 blob 的 MIME 内容类型
默认情况下，AzCopy 将目标 blob 的内容类型设置为 `application/octet-stream`。 但是，可以通过选项 `--set-content-type [content-type]` 显式指定内容类型。 此语法会在上传操作中设置所有 blob 的内容类型。

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/myContainer/ \
    --dest-key <key> \
    --include "ab" \
    --set-content-type "video/mp4"
```

如果指定不带任何值的 `--set-content-type` 选项，AzCopy 将根据文件扩展名设置每个 Blob 或文件的内容类型。

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/myContainer/ \
    --dest-key <key> \
    --include "ab" \
    --set-content-type
```

## <a name="blob-copy"></a>Blob：复制
### <a name="copy-single-blob-within-storage-account"></a>在存储帐户内复制单个 blob

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1 \
    --destination https://myaccount.blob.core.windows.net/mycontainer2 \
    --source-key <key> \
    --dest-key <key> \
    --include "abc.txt"
```

如果不使用 --sync-copy 选项复制 Blob，将执行[服务器端复制](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx)操作。

### <a name="copy-single-blob-across-storage-accounts"></a>跨存储帐户复制单个 blob

```azcopy
azcopy \
    --source https://sourceaccount.blob.core.windows.net/mycontainer1 \
    --destination https://destaccount.blob.core.windows.net/mycontainer2 \
    --source-key <key1> \
    --dest-key <key2> \
    --include "abc.txt"
```

如果不使用 --sync-copy 选项复制 Blob，将执行[服务器端复制](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx)操作。

### <a name="copy-single-blob-from-secondary-region-to-primary-region"></a>将单个 blob 从次要区域复制到主要区域

```azcopy
azcopy \
    --source https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1 \
    --destination https://myaccount2.blob.core.windows.net/mynewcontainer2 \
    --source-key <key1> \
    --dest-key <key2> \
    --include "abc.txt"
```

请注意，必须已启用读取访问异地冗余存储。

### <a name="copy-single-blob-and-its-snapshots-across-storage-accounts"></a>跨存储帐户复制单个 blob 及其快照

```azcopy
azcopy \
    --source https://sourceaccount.blob.core.windows.net/mycontainer1 \
    --destination https://destaccount.blob.core.windows.net/mycontainer2 \
    --source-key <key1> \
    --dest-key <key2> \
    --include "abc.txt" \
    --include-snapshot
```

复制操作完成后，目标容器中将包含 Blob 及其快照。 容器包含以下 Blob 及其快照：

```
abc.txt
abc (2013-02-25 080757).txt
abc (2014-02-21 150331).txt
```

### <a name="synchronously-copy-blobs-across-storage-accounts"></a>跨存储帐户同步复制 blob
默认情况下，AzCopy 以异步方式复制两个存储终结点之间的数据。 因此，复制操作使用空闲的带宽容量在后台运行，没有规定 Blob 复制速度的 SLA。 

`--sync-copy` 选项确保复制操作的速度一致。 AzCopy 通过下载 blob，将 blob 从指定的源复制到本地内存，然后将上传到 Blob 存储目标，以实现同步复制。

```azcopy
azcopy \
    --source https://myaccount1.blob.core.windows.net/myContainer/ \
    --destination https://myaccount2.blob.core.windows.net/myContainer/ \
    --source-key <key1> \
    --dest-key <key2> \
    --include "ab" \
    --sync-copy
```

`--sync-copy` 可能会产生额外的数据传出费用，而异步复制则不会。 在与源存储帐户所在的同一区域的 Azure VM 中，建议使用此选项，避免产生数据传出费用。

## <a name="file-download"></a>文件：下载
### <a name="download-single-file"></a>下载单个文件

```azcopy
azcopy \
    --source https://myaccount.file.core.windows.net/myfileshare/myfolder1/ \
    --destination /mnt/myfiles \
    --source-key <key> \
    --include "abc.txt"
```

如果指定的源是 Azure 文件共享，则必须指定确切的文件名（*例如* `abc.txt`）以下载单个文件，或者指定选项 `--recursive` 以递归方式下载该共享中的所有文件。 尝试同时指定文件模式和选项 `--recursive` 会导致错误。

### <a name="download-all-files"></a>下载所有文件

```azcopy
azcopy \
    --source https://myaccount.file.core.windows.net/myfileshare/ \
    --destination /mnt/myfiles \
    --source-key <key> \
    --recursive
```

请注意，不会下载任何空文件夹。

## <a name="file-upload"></a>文件：上传
### <a name="upload-single-file"></a>上传单个文件

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.file.core.windows.net/myfileshare/ \
    --dest-key <key> \
    --include abc.txt
```

### <a name="upload-all-files"></a>上传全部文件

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.file.core.windows.net/myfileshare/ \
    --dest-key <key> \
    --recursive
```

请注意，不会上传任何空文件夹。

### <a name="upload-files-matching-specified-pattern"></a>上传与指定模式相匹配的文件

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.file.core.windows.net/myfileshare/ \
    --dest-key <key> \
    --include "ab*" \
    --recursive
```

## <a name="file-copy"></a>文件：复制
### <a name="copy-across-file-shares"></a>跨文件共享复制

```azcopy
azcopy \
    --source https://myaccount1.file.core.windows.net/myfileshare1/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare2/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
在跨文件共享复制某个文件时，将执行[服务器端复制](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx)操作。

### <a name="copy-from-file-share-to-blob"></a>从文件共享复制到 blob

```azcopy
azcopy \ 
    --source https://myaccount1.file.core.windows.net/myfileshare/ \
    --destination https://myaccount2.blob.core.windows.net/mycontainer/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
将文件从文件共享复制到 Blob 时，将执行[服务器端复制](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx)操作。

### <a name="copy-from-blob-to-file-share"></a>从 blob 复制到文件共享

```azcopy
azcopy \
    --source https://myaccount1.blob.core.windows.net/mycontainer/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
将文件从 Blob 复制到文件共享时，将执行[服务器端复制](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx)操作。

### <a name="synchronously-copy-files"></a>同步复制文件
可以指定选项 `--sync-copy`，以从文件存储到文件存储、从文件存储到 Blob 存储以及从 Blob 存储到文件存储同步复制数据。 AzCopy 通过将源数据下载到本地内存并再将其上传到目标来运行此操作。 在这种情况下，将应用标准传出费用。

```azcopy
azcopy \
    --source https://myaccount1.file.core.windows.net/myfileshare1/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare2/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive \
    --sync-copy
```

当从文件存储复制到 Blob 存储时，默认的 blob 类型是块 blob，用户可以指定选项 `/BlobType:page` 以更改目标 blob 类型。

请注意，`--sync-copy` 可能会产生额外的数据传出费用，而异步复制则不会。 在与源存储帐户所在的同一区域的 Azure VM 中，建议使用此选项，避免产生数据传出费用。

## <a name="other-azcopy-features"></a>AzCopy 的其他功能
### <a name="only-copy-data-that-doesnt-exist-in-the-destination"></a>仅复制目标中不存在的数据
`--exclude-older` 和 `--exclude-newer` 参数分别用于阻止复制较早或较新的源资源。 如果只想复制目标中不存在的源资源，可以在 AzCopy 命令中指定这两个参数：

    --source http://myaccount.blob.core.windows.net/mycontainer --destination /mnt/myfiles --source-key <sourcekey> --recursive --exclude-older --exclude-newer

    --source /mnt/myfiles --destination http://myaccount.file.core.windows.net/myfileshare --dest-key <destkey> --recursive --exclude-older --exclude-newer

    --source http://myaccount.blob.core.windows.net/mycontainer --destination http://myaccount.blob.core.windows.net/mycontainer1 --source-key <sourcekey> --dest-key <destkey> --recursive --exclude-older --exclude-newer

### <a name="use-a-configuration-file-to-specify-command-line-parameters"></a>使用配置文件指定命令行参数

```azcopy
azcopy --config-file "azcopy-config.ini"
```

可以在配置文件中包含任何 AzCopy 命令行参数。 AzCopy 会像处理在命令行上指定的参数一样处理该文件中的参数，使用该文件的内容执行直接替换。

假设有一个名为 `copyoperation` 的配置文件，其中包含以下行： 可以在同一行或多行中指定

    --source http://myaccount.blob.core.windows.net/mycontainer --destination /mnt/myfiles --source-key <sourcekey> --recursive --quiet

每个 AzCopy 参数：

    --source http://myaccount.blob.core.windows.net/mycontainer
    --destination /mnt/myfiles
    --source-key<sourcekey>
    --recursive
    --quiet

如果将参数拆分到两行（如此处所示的 `--source-key` 参数），AzCopy 将会失败：

    http://myaccount.blob.core.windows.net/mycontainer
    /mnt/myfiles
    --sourcekey
    <sourcekey>
    --recursive
    --quiet

### <a name="specify-a-shared-access-signature-sas"></a>指定共享访问签名 (SAS)

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1 \
    --destination https://myaccount.blob.core.windows.net/mycontainer2 \
    --source-sas <SAS1> \
    --dest-sas <SAS2> \
    --include abc.txt
```

此外，还可以在容器 URI 上指定一个 SAS：

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken \
    --destination /mnt/myfiles \
    --recursive
```

请注意，AzCopy 目前仅支持[帐户 SAS](https://docs.microsoft.com/en-us/azure/storage/storage-dotnet-shared-access-signature-part-1)。

### <a name="journal-file-folder"></a>日志文件文件夹
每次向 AzCopy 发出命令时，它都会检查默认文件夹中是否存在日志文件，或者通过此选项指定的文件夹中是否存在日志文件。 如果这两个位置中都不存在日志文件，AzCopy 则会将操作视为新操作并生成一个新的日志文件。

如果存在日志文件，AzCopy 则将检查输入的命令行是否与该日志文件中的命令行相匹配。 如果两个命令行相匹配，AzCopy 则将恢复未完成的操作。 如果它们不匹配，AzCopy 将提示用户是选择覆盖该日志文件以启动新操作，还是取消当前操作。

如果想要为日志文件使用默认位置：

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --resume
```

如果省略了选项 `--resume`，或者指定了选项 `--resume` 但未指定文件夹路径（如上所示），AzCopy 则会在默认位置中创建日志文件，默认位置为 `~\Microsoft\Azure\AzCopy`。 如果日志文件已存在，AzCopy 则将继续根据日志文件恢复操作。

如果想要为日志文件指定自定义位置：

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key key \
    --resume "/mnt/myjournal"
```

如果日志文件尚不存在，此示例将创建日志文件。 如果它已存在，AzCopy 则会根据该日志文件恢复操作。

若要恢复 AzCopy 操作，请重复相同的命令。 然后，AzCopy on Linux 会提示确认：

```azcopy
Incomplete operation with same command line detected at the journal directory "/home/myaccount/Microsoft/Azure/AzCopy", do you want to resume the operation? Choose Yes to resume, choose No to overwrite the journal to start a new operation. (Yes/No)
```

### <a name="output-verbose-logs"></a>输出详细日志

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --verbose
```

### <a name="specify-the-number-of-concurrent-operations-to-start"></a>指定要启动的并发操作的数量
选项 `--parallel-level` 指定并发复制操作的数量。 默认情况下，AzCopy 会启动一定数量的并发操作以提高数据传输吞吐量。 并发操作的数量是你拥有的处理器数的八倍。 如果在低带宽网络中运行 AzCopy，可为 --parallel-level 指定较低的数量以避免由于资源争用所导致的故障。

[!TIP]
>若要查看完整的 AzCopy 参数列表，请查看“azcopy --help”菜单。

## <a name="known-issues-and-best-practices"></a>已知问题和最佳做法
### <a name="error-net-core-is-not-found-in-the-system"></a>错误: 在系统找不到 .NET Core。
如果有错误指出系统中未安装 .NET Core，则原因可能是缺少 .NET Core 二进制文件 `dotnet` 的 PATH。

若要解决此问题，请在系统中找到该 .NET Core 二进制文件：
```bash
sudo find / -name dotnet
```

这会返回 dotnet 二进制文件的路径。 

    /opt/rh/rh-dotnetcore11/root/usr/bin/dotnet
    /opt/rh/rh-dotnetcore11/root/usr/lib64/dotnetcore/dotnet
    /opt/rh/rh-dotnetcore11/root/usr/lib64/dotnetcore/shared/Microsoft.NETCore.App/1.1.2/dotnet

现在，请将此路径添加到 PATH 变量中。 对于 sudo，请编辑 secure_path，以便在 dotnet 二进制文件中包含该路径：
```bash 
sudo visudo
### Append the path found in the preceding example to 'secure_path' variable
```

在此示例中，secure_path 变量显示为：

```
secure_path = /sbin:/bin:/usr/sbin:/usr/bin:/opt/rh/rh-dotnetcore11/root/usr/bin/
```

对于当前用户，请编辑 .bash_profile/.profile，以便在 dotnet 二进制文件的 PATH 变量中包含该路径 
```bash
vi ~/.bash_profile
### Append the path found in the preceding example to 'PATH' variable
```

验证 .NET Core 现在是否在 PATH 中：
```bash
which dotnet
sudo which dotnet
```

### <a name="error-installing-azcopy"></a>安装 AzCopy 时出错
如果安装 AzCopy 时遇到问题，可以尝试在提取的 `azcopy` 文件夹中使用 bash 脚本运行 AzCopy。

```bash
cd azcopy
./azcopy
```

### <a name="limit-concurrent-writes-while-copying-data"></a>限制复制数据时的并发写入
在使用 AzCopy 复制 blob 或文件时，请记住，在复制数据时其他应用程序可能正在修改该数据。 如果可能，请确保要复制的数据在复制操作期间不会被修改。 例如，当复制与 Azure 虚拟机关联的 VHD 时，请确保当前没有其他应用程序正在向该 VHD 进行写入。 执行此操作的一个好方法是租用要复制的资源。 另外，还可以先创建 VHD 的快照，然后复制该快照。

如果在复制 blob 或文件时无法阻止其他应用程序向其进行写入，请记住，在作业完成时，复制的资源可能不再与源资源完全相同。

### <a name="run-one-azcopy-instance-on-one-machine"></a>在一台计算机上运行一个 AzCopy 实例。
AzCopy 旨在最大程度上利用计算机资源来加快数据传输，如果需要更多的并发操作，我们建议在一台计算机上只运行一个 AzCopy 实例并指定选项 `--parallel-level`。 有关详细信息，请在命令行中键入 `AzCopy --help parallel-level`。

## <a name="next-steps"></a>后续步骤
有关 Azure 存储和 AzCopy 的详细信息，请参阅以下资源：

### <a name="azure-storage-documentation"></a>Azure 存储文档：
* [Azure 存储简介](../storage-introduction.md)
* [创建存储帐户](../storage-create-storage-account.md)
* [使用存储资源管理器管理 Blob](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-explorer-blobs)
* [将 Azure CLI 2.0 用于 Azure 存储](../storage-azure-cli.md)
* [如何通过 C++ 使用 Blob 存储](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [如何通过 Java 使用 Blob 存储](../blobs/storage-java-how-to-use-blob-storage.md)
* [如何通过 Node.js 使用 Blob 存储](../blobs/storage-nodejs-how-to-use-blob-storage.md)
* [如何通过 Python 使用 Blob 存储](../blobs/storage-python-how-to-use-blob-storage.md)

### <a name="azure-storage-blog-posts"></a>Azure 存储博客文章：
* [Announcing AzCopy on Linux Preview](https://azure.microsoft.com/en-in/blog/announcing-azcopy-on-linux-preview/)（宣布推出 AzCopy on Linux 预览版）
* [Introducing Azure Storage Data Movement Library Preview](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)（Azure 存储数据移动库预览版简介）
* [AzCopy: Introducing synchronous copy and customized content type](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/01/13/azcopy-introducing-synchronous-copy-and-customized-content-type.aspx)（AzCopy：同步复制和自定义内容类型简介）
* [AzCopy: Announcing General Availability of AzCopy 3.0 plus preview release of AzCopy 4.0 with Table and File support](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)（AzCopy：宣布公开发行支持表和文件的 AzCopy 3.0 增强预览版本 AzCopy 4.0）
* [AzCopy: Optimized for Large-Scale Copy Scenarios](http://go.microsoft.com/fwlink/?LinkId=507682)（AzCopy：针对大规模复制方案进行优化）
* [AzCopy：支持读取访问异地冗余存储](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
* [AzCopy: Transfer data with restartable mode and SAS token](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx)（AzCopy：使用可重启的模式和 SAS 令牌传输数据）
* [AzCopy: Using cross-account Copy Blob](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)（AzCopy：使用跨帐户复制 Blob）
* [AzCopy: Uploading/downloading files for Azure Blobs](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)（AzCopy：为 Azure Blob 上传/下载文件）


