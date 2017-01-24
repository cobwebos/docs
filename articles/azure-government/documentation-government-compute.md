---
title: "Azure 政府版计算 | Microsoft Docs"
description: "这里提供了针对 Azure 政府版的应用程序开发使用到的功能和指南对比"
services: Azure-Government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
editor: 
ms.assetid: fb11f60c-5a70-46a9-82a0-abb2a4f4239b
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 11/18/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: ca01baebd755e92aff1ef080af1fcd4606b9a2ec
ms.openlocfilehash: 200caac4b480495b0bbb8f10cc89fdd73c42dbe2


---
# <a name="azure-government-compute"></a>Azure 政府版计算
## <a name="virtual-machines"></a>虚拟机
有关此服务以及如何使用此服务的详细信息，请参阅 [Azure 虚拟机大小](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

### <a name="variations"></a>变体
Azure 政府版中提供以下产品 (VM)：

| 产品 (VM) | 美国政府弗吉尼亚州 | 美国政府艾奥瓦州 |
| --- | --- | --- |
| A0-A7 |Y |Y |
| D 系列 |Y |N |
| Dv2 系列 |Y |Y |
| DS 系列 |Y |N |
| F 系列 |Y |Y |
| FS 系列 |N |N |
| G 系列 |Y |N |
| GS 系列 |Y |N |

### <a name="data-considerations"></a>数据注意事项
以下信息标识 Azure 虚拟机的 Azure 政府版边界：

| 允许的管制/控制数据 | 不允许的管制/控制数据 |
| --- | --- |
| 在 VM 内输入、存储及处理的数据可包含导出控制数据。 在 Azure 虚拟机中运行的二进制文件。 静态身份验证器，例如用于访问 Azure 平台组件的密码和智能卡 PIN。 用于管理 Azure 平台组件的证书私钥。 SQL 连接字符串。  其他安全信息/机密信息，例如证书、加密密钥、主密钥和 Azure 服务中存储的存储密钥。 |元数据不允许包含导出控制数据。 此元数据包括创建和维护 Azure 虚拟机时输入的所有配置数据。  请勿将管制/控制数据输入到以下字段：租户角色名称、资源组、部署名称、资源名称、资源标记 |

## <a name="next-steps"></a>后续步骤
有关补充信息和更新，请订阅 <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure 政府博客</a>。




<!--HONumber=Dec16_HO1-->


