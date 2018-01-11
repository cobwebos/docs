---
title: "Azure 堆栈的基础结构备份服务最佳实践 |Microsoft 文档"
description: "当你在部署和管理 Azure 堆栈数据中心，以帮助减轻数据损失，如果出现灾难性故障时，你可以遵循一组最佳做法。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 221FDE40-3EF8-4F54-A075-0C4D66EECE1A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/27/2017
ms.author: mabrigg
ms.openlocfilehash: b9438f3bab92f40a5c79ce7b7a572195c182be45
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2018
---
# <a name="infrastructure-backup-service-best-practices"></a>基础结构备份服务的最佳做法

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

当你在部署和管理 Azure 堆栈数据中心，以帮助减轻数据损失出现灾难性故障时，你可以遵循最佳做法。

在按固定间隔，以验证你的安装仍在法规遵从性操作流发生更改时，应查看最佳实践。 你应在实施这些最佳方案时遇到任何问题，联系 Microsoft 支持部门获取帮助。

## <a name="configuration-best-practices"></a>配置最佳实践

### <a name="deployment"></a>部署

每个 Azure 堆栈云部署后启用基础结构备份。 使用 AzureStack 工具可以计划从任何具有访问权限的客户端/服务器到运算符管理 API 终结点的备份。

### <a name="networking"></a>网络

路径的通用命名约定 (UNC) 字符串必须使用完全限定的域名 (FQDN)。 如果名称解析不可能可能 IP 地址。 UNC 字符串指定的资源，如共享的文件或设备的位置。

### <a name="encryption"></a>加密

加密密钥用于加密导出到外部存储的备份数据。 可以使用 AzureStack 工具生成密钥。 

![AzureStack 工具](media\azure-stack-backup\azure-stack-backup-encryption1.png)

密钥必须存储在安全的位置 （例如，公共 Azure 密钥保管库密钥）。 在重新部署 Azure 堆栈的过程，必须使用此密钥。 

![密钥的安全位置的存储。](media\azure-stack-backup\azure-stack-backup-encryption2.png)

## <a name="operational-best-practices"></a>操作最佳实践

### <a name="backups"></a>备份

 - 基础结构备份控制器需要按照需要来触发。 建议将至少两次每日备份。
 - 系统正在运行，因此没有停机时间缩到的管理体验或用户应用程序时，将执行备份作业。 预期的备份作业需要的解决方案的合理负载的 20-40 分钟时间。
 - 使用 OEM 提供指令，手动备份的网络交换机和硬件生命周期主机 (HLH) 应存储在其中基础结构备份控制器存储控制平面备份数据的同一备份共享。 请考虑将交换机和 HLH 配置存储的区域文件夹中。 如果你有多个 Azure 堆栈实例在同一区域中，则考虑使用扩展单元所属的每个配置标识符。

### <a name="folder-names"></a>文件夹名称

 - 基础结构将自动创建 MASBACKUP 文件夹。 这是 Microsoft 管理的共享。 你可以在与 MASBACKUP 相同的级别创建共享。 建议不要创建文件夹或存储数据内 MASBACKUP Azure 堆栈不会创建。 
 -  用户 FQDN 和文件夹名称中的区域来区分来自不同云的备份数据。 Azure 堆栈部署和终结点的完全限定的域名 (FQDN) 是区域参数和外部域 Name 参数的组合。 有关详细信息，请参阅[Azure 堆栈数据中心集成的 DNS](azure-stack-integrate-dns.md)。

例如，备份的共享是 AzSBackups fileserver01.contoso.com 上托管。在该文件共享可能是每个 Azure 堆栈部署使用的外部域名和使用的区域名称的子文件夹的文件夹。 

FQDN: contoso.com  
区域： nyc


    \\fileserver01.contoso.com\AzSBackups
    \\fileserver01.contoso.com\AzSBackups\contoso.com
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\MASBackup

MASBackup 文件夹是 Azure 堆栈存储其备份数据的地方。 不应使用此文件夹来存储你自己的数据。 OEM 不应使用此文件夹可以存储任何备份数据。 

Oem 建议用于存储备份数据的区域文件夹下其组件。 每个网络交换机、 硬件生命周期主机 (HLH) 等可以存储在它自己的子文件夹。 例如：

    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\HLH
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Switches
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\DeploymentData
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Registration

### <a name="monitoring"></a>监视

系统支持的以下警报：

| 警报                                                   | 说明                                                                                     | 补救                                                                                                                                |
|---------------------------------------------------------|-------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| 备份失败，因为文件共享是超出能力 | 文件共享是超出能力和备份控制器无法将备份文件导出到的位置。 | 添加更多存储容量，然后重试备份。 删除 （首次开始从旧） 的现有备份以释放空间。                    |
| 备份由于连接问题而失败。             | 网络之间 Azure 堆栈和文件共享遇到了问题。                          | 解决网络问题，然后重试备份。                                                                                            |
| 由于在路径中的错误而失败的备份                | 无法解析文件共享路径                                                          | 映射不同的计算机，以确保共享是可访问的共享。 你可能需要更新的路径，如果将不再有效。       |
| 备份由于身份验证问题而失败               | 可能使用的凭据的问题或网络问题影响身份验证。    | 映射不同的计算机，以确保共享是可访问的共享。 你可能需要更新凭据，如果它们将不再有效。 |
| 由于常规故障导致备份失败                    | 失败的请求可能由于间歇性问题。 重试备份。                    | 致电支持人员                                                                                                                               |

## <a name="next-steps"></a>后续步骤

 - 查看的参考资料[基础结构备份服务](azure-stack-backup-reference.md)。  
 - 启用[基础结构备份服务](azure-stack-backup-enable-backup-console.md)。