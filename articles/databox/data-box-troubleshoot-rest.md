---
title: 排查使用 REST 接口时的 Azure Data Box 问题 | Microsoft Docs
description: 介绍如何排查通过 REST 接口复制数据时在 Azure Data Box 中出现的问题。
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: troubleshooting
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: b950f80ba8c2bdbaf7a515dc1ce127b934723177
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85558557"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-blob-storage"></a>排查与 Azure Data Box Blob 存储相关的问题

本文详细说明如何排查使用 Data Box Blob 存储通过 Data Box 上的 REST 接口复制数据时可能出现的问题。 将 Data Box Blob 存储与其他应用程序或客户端库（例如 Azure 存储资源管理器、AzCopy 或用于 Python 的 Azure 存储库）配合使用时，这些问题会出现。

## <a name="errors-seen-in-azure-storage-explorer"></a>在 Azure 存储资源管理器中看到的错误

本部分详述了将 Azure 存储资源管理器与 Data Box Blob 存储配合使用时会遇到的一些问题。

|错误消息  |建议的操作 |
|---------|---------|
|无法检索子资源。 其中一个 HTTP 标头的值的格式不正确。|在“编辑”菜单中，选择“目标 Azure Stack API”。  <br>重启Azure 存储资源管理器。|
|`getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com` |检查是否已在以下路径将终结点名称 `<accountname>.blob.<serialnumber>.microsoftdatabox.com` 添加到 hosts 文件： <li>`C:\Windows\System32\drivers\etc\hosts`（在 Windows 上），或者 </li><li> `/etc/hosts`（在 Linux 上）。</li>|
|无法检索子资源。 <br>详细信息：自签名证书 |将设备的 TLS/SSL 证书导入 Azure 存储资源管理器： <li>从 Azure 门户下载证书。 有关详细信息，请转到[下载证书](data-box-deploy-copy-data-via-rest.md#download-certificate)。</li><li>在“编辑”菜单中，选择“SSL 证书”，然后选择“导入证书”。  </li>|

## <a name="errors-seen-in-azcopy-for-windows"></a>在 Windows 的 AzCopy 中看到的错误

本部分详述了将 Windows 的 AzCopy 与 Data Box Blob 存储配合使用时会遇到的一些问题。

|错误消息  |建议的操作 |
|---------|---------|
|AzCopy 命令在显示以下错误之前，似乎停止了响应，时间长达一分钟： <br>无法枚举目录 https://… 无法解析远程名称 `<accountname>.blob.<serialnumber>.microsoftdatabox.com`|检查是否已在 `C:\Windows\System32\drivers\etc\hosts` 将终结点名称 `<accountname>.blob.<serialnumber>.microsoftdatabox.com` 添加到 hosts 文件。|
|AzCopy 命令在显示以下错误之前，似乎停止了响应，时间长达一分钟： <br>分析源位置出错。 基础连接已关闭:无法建立 SSL/TLS 安全通道的信任关系。|将设备的 TLS/SSL 证书导入系统的证书存储。 有关详细信息，请转到[下载证书](data-box-deploy-copy-data-via-rest.md#download-certificate)。|


## <a name="errors-seen-in-azcopy-for-linux"></a>在 Linux 的 AzCopy 中看到的错误

本部分详述了将 Linux 的 AzCopy 与 Data Box Blob 存储配合使用时会遇到的一些问题。

|错误消息  |建议的操作 |
|---------|---------|
|AzCopy 命令在显示以下错误之前，似乎停止了响应，时间长达 20 分钟： <br>分析源位置 `https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>` 出错。 没有此类设备或地址|检查是否已在 `/etc/hosts` 将终结点名称 `<accountname>.blob.<serialnumber>.microsoftdatabox.com` 添加到 hosts 文件。|
|AzCopy 命令在显示以下错误之前，似乎停止了响应，时间长达 20 分钟： <br>分析源位置出错… 无法建立 SSL 连接。|将设备的 TLS/SSL 证书导入系统的证书存储。 有关详细信息，请转到[下载证书](data-box-deploy-copy-data-via-rest.md#download-certificate)。|

## <a name="errors-seen-in-azure-storage-library-for-python"></a>在用于 Python 的 Azure 存储库中看到的错误

本部分详细介绍了在使用 Linux 客户端进行数据复制时部署 Data Box Disk 所遇到的一些常见问题。

|错误消息  |建议的操作 |
|---------|---------|
|其中一个 HTTP 标头的值的格式不正确。 |Data Box 不支持用于 Python 的 Microsoft Azure 存储库的已安装版本。 请查看 Azure Data Box Blob 存储要求，了解支持的版本。|
|… [SSL:CERTIFICATE_VERIFY_FAILED] …|在运行 Python 之前，请将 REQUESTS_CA_BUNDLE 环境变量设置为 Base64 编码的 TLS 证书文件的路径（请参阅[如何下载证书](data-box-deploy-copy-data-via-rest.md#download-certificate)）。 <br>例如：<br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer` <br>`python` <br>也可将证书添加到系统的证书存储，然后将此环境变量设置为该存储的路径。 <br> 例如，在 Ubuntu 上为： <br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt` <br>`python`|


## <a name="common-errors"></a>常见错误

这些错误不特定于任何应用程序。

|错误消息  |建议的操作 |
|---------|---------|
|连接超时。 |登录到 Data Box 设备，检查其是否已解锁。 设备在重启后会保持锁定状态，直到有人登录为止。|

## <a name="next-steps"></a>后续步骤

- 了解 [Data Box Blob 存储系统要求](data-box-system-requirements-rest.md)。
