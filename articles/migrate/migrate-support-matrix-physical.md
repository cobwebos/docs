---
title: 支持 Azure Migrate 的物理服务器评估/迁移
description: 汇总了对 Azure Migrate 的物理服务器评估/迁移的支持。
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: b5b5da6282b1df6c70fd58dcf8c417250de81b73
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196340"
---
# <a name="support-matrix-for-physical-server-assessment-and-migration"></a>用于物理服务器评估和迁移的支持矩阵

你可以使用[Azure Migrate 服务](migrate-overview.md)来评估计算机并将其迁移到 Microsoft Azure 云。 本文总结了用于评估和迁移本地物理服务器的支持设置和限制。



## <a name="physical-server-scenarios"></a>物理服务器方案

下表汇总了物理服务器支持的方案。

**部署** | **详细信息***
--- | ---
**评估本地物理服务器** | [设置](tutorial-prepare-physical.md)第一次评估。<br/><br/> [运行](tutorial-assess-physical.md)评估。
**将物理服务器迁移到 Azure** | [尝试](tutorial-migrate-physical-virtual-machines.md)迁移到 Azure。


## <a name="azure-migrate-projects"></a>Azure Migrate 项目

**支持** | **详细信息**
--- | ---
**Azure 权限** | 你需要订阅中的 "参与者" 或 "所有者" 权限才能创建 Azure Migrate 项目。
**物理服务器** | 在单个项目中最多评估35000个物理服务器。 Azure 订阅中可以有多个项目。 项目可以包括物理服务器、VMware Vm 和 Hyper-v Vm，最高可达评估限制。
**地域** | 你可以在多个地理区域中创建 Azure Migrate 项目。 虽然你可以在特定地理区域创建项目，但你可以为其他目标位置评估或迁移计算机。 项目地域仅用于存储已发现的元数据。

  **地域** | **元数据存储位置**
  --- | ---
  Azure Government | 美国政府弗吉尼亚州
  亚太区 | 东亚或东南亚
  澳大利亚 | 澳大利亚东部或澳大利亚东南部
  巴西 | 巴西南部
  加拿大 | 加拿大中部或加拿大东部
  欧洲 | 欧洲北部或欧洲西部
  法国 | 法国中部
  印度 | 印度中部或印度南部
  日本 |  日本东部或日本西部
  韩国 | 韩国中部或韩国南部
  英国 | 英国南部或英国西部
  美国 | 美国中部或美国西部2


 > [!NOTE]
 > Azure 政府版支持目前仅适用于[较旧版本](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions)的 Azure Migrate。


## <a name="assessment-physical-server-requirements"></a>评估-物理服务器要求

| **支持**                | **详细信息**               
| :-------------------       | :------------------- |
| **物理服务器部署**       | 物理服务器可以是独立服务器，也可以部署到群集中。 |
| **权限**           | **Windows：** 在要包括在发现中的所有 Windows 服务器上设置本地用户帐户。需要将用户帐户添加到这些组-远程桌面用户、性能监视器用户和性能日志用户。 <br/> **Linux：** 需要在要发现的 Linux 服务器上使用根帐户。 |
| **操作系统** | 支持所有[Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)和[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)操作系统，但以下情况除外：<br/> Windows Server 2003 <br/> SUSE Linux|


## <a name="assessment-appliance-requirements"></a>评估-设备要求

对于评估，Azure Migrate 会运行轻型设备来发现物理服务器，并将服务器元数据和性能数据发送到 Azure Migrate。 设备既可以在物理服务器上运行，也可以在 VM 上运行，也可以使用从 Azure 门户下载的 PowerShell 脚本对其进行设置。 下表总结了设备的要求。

| **支持**                | **详细信息**               
| :-------------------       | :------------------- |
| **设备部署**   |  在物理服务器或虚拟机上部署该设备。<br/>  主计算机必须运行 Windows Server 2012 R2 或更高版本。<br/> 主机需要足够的空间来分配 16 GB RAM、8个 vcpu、大约 80 GB 的存储空间，以及设备 VM 的外部交换机。<br/> 设备需要静态或动态 IP 地址以及 internet 访问。
| **Azure Migrate 项目**  |  设备可以与单个项目关联。<br/> 可以将任意数量的设备与单个项目相关联。<br/> 你最多可以在一个项目中评估35000台计算机。
| **发现**              | 单个设备最多可以发现250服务器。
| **评估组**       | 最多可以在一个组中添加35000台计算机。
| **评估**             | 在单个评估中，最多可以评估35000台计算机。


## <a name="assessment-appliance-url-access"></a>评估-设备 URL 访问

若要评估 Vm，Azure Migrate 设备需要连接到 internet。

- 部署设备时，Azure Migrate 会对下表中汇总的 Url 进行连接性检查。
- 如果使用的是基于 URL 的代理，则允许访问表中的 Url，确保代理解析在查找 Url 时收到的任何 CNAME 记录。
- 如果有拦截代理，可能需要将服务器证书从代理服务器导入到设备。


**URL** | **详细信息**  
--- | ---
*.portal.azure.com | 导航到 Azure 门户
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *. microsoft.com <br/> *. live.com  | 登录到 Azure 订阅
*.microsoftonline.com <br/> *.microsoftonline-p.com | 为设备到服务通信创建 Azure Active Directory 应用程序。
management.azure.com | 为设备到服务通信创建 Azure Active Directory 应用程序。
dc.services.visualstudio.com | 日志记录和监视
*.vault.azure.net | 在设备与服务之间通信时管理 Azure Key Vault 中的机密。
aka.ms/* | 允许访问称为 "链接"。
https://download.microsoft.com/download/* | 允许从 Microsoft 下载站点下载。



## <a name="assessment-port-requirements"></a>评估-端口要求

下表汇总了评估的端口要求。

**设备** | **Connection**
--- | ---
**本** | TCP 端口3389上的入站连接，允许到设备的远程桌面连接。<br/> 端口44368上的入站连接，使用以下 URL 远程访问设备管理应用： ``` https://<appliance-ip-or-name>:44368 ```<br/> 端口443、5671和5672上的出站连接将发现和性能元数据发送到 Azure Migrate。
**物理服务器** | **Windows：** 端口443、5989上的入站连接从 Windows 服务器拉取配置和性能元数据。 <br/> **Linux：** 端口22（UDP）上的入站连接，用于从 Linux 服务器拉取配置和性能元数据。 |


## <a name="next-steps"></a>后续步骤

准备物理服务器评估和迁移的[物理服务器评估](tutorial-prepare-physical.md)。
