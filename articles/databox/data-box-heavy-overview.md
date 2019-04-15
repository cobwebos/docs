---
title: Microsoft Azure Data Box Heavy 概述 | Microsoft Docs
description: 介绍 Azure Data Box - 一种用于将大量数据传输到 Azure 的混合解决方案
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: overview
ms.custom: ''
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: 0a5b7f93f9ac6cc5b1076881727a42fd5b95ff4b
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2018
ms.locfileid: "51235150"
---
# <a name="what-is-azure-data-box-heavy-preview"></a>什么是 Azure Data Box Heavy？ （预览版）

使用 Microsoft Azure Data Box 混合解决方案可以通过快速、经济、可靠的方式将数百 TB 的数据发送到 Azure。 通过向你运送专有的存储设备（存储容量为 1 PB）来加速安全数据传输。 该设备具有坚固的外壳，可在运输过程中保护数据。

Data Box Heavy 目前处于预览状态，可通过 Azure 门户注册以请求设备。 在数据中心收到设备后，可使用本地 Web UI 进行设置。 将数据从服务器复制到设备，然后将设备发回 Azure。 在 Azure 数据中心内，数据将从设备自动上传到 Azure。 通过 Azure 门户中的 Data Box 服务对整个过程进行端到端跟踪。


> [!IMPORTANT]
> - Data Box Heavy 以预览版提供。 在部署此解决方案之前，请查看 [Azure 预览版服务的条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 
> - 若要请求设备，请在[预览门户](https://aka.ms/)中注册。
> - 在预览期，Data Box Heavy 可以寄送到美国和欧盟的客户。 有关详细信息，请参阅[适用区域](#region-availability)。

## <a name="use-cases"></a>用例

Data Box Heavy 非常适合在无网络连接到有限网络连接的情况下传输大于 500 TB 的数据。 数据移动的方式可以是一次性的、定期的，或者先执行批量数据传输，再定期传输。 以下是 Data Box Heavy 可用于数据传输的各种方案。

 - **一次性迁移** - 需要将大量本地数据移到 Azure 时。 
     - 将媒体库从脱机磁带移动到 Azure 以创建联机媒体库。
     - 将 VM 场、SQL server 和应用程序迁移到 Azure
     - 将历史数据移至 Azure，以便使用 HDInsight 进行深入分析和报告

 - **初始批量传输** - 需要先使用 Data Box Heavy（种子）执行初始批量传输，然后通过网络执行增量传输时。 
     - 例如，备份解决方案合作伙伴（如 Commvault 和 Data Box Heavy）用于将初始大型历史备份移至 Azure。 完成后，增量数据将通过网络传输到 Azure 存储。

 - **定期上传** - 定期生成大量数据，并需要将这些数据移到 Azure 时。 例如，能源勘探领域生成有关钻井平台和风力发电农场的视频内容。      

## <a name="benefits"></a>优点

Data Box Heavy 用于在几乎不影响网络的情况下将大量数据移到 Azure。 该解决方案具有以下优点：

- **速度** - Data Box Heavy 使用高性能 40 Gbps 网络接口。

- **安全** - Data Box Heavy 为设备、数据和服务提供内置安全保护。
    - 该设备具有坚固设备外壳，通过防破坏螺钉和防破坏贴纸固定。 
    - 设备上的数据始终使用 AES 256 位加密进行保护。
    - 只能使用 Azure 门户中提供的密钥解锁设备。
    - 服务受 Azure 安全功能的保护。
    - 将数据上传到 Azure 后，会根据 NIST 800-88r1 标准完全擦除设备上的磁盘数据。


<!--## Features and specifications

The Data Box Heavy device has the following features in this release.

| Specifications                                          | Description              |
|---------------------------------------------------------|--------------------------|
| Weight                                                  | < 50 lbs.                |
| Dimensions                                              | Device - Width: 309.0 mm Height: 430.4 mm Depth: 502.0 mm |            
| Rack space                                              | 7 U when placed in the rack on its side (cannot be rack-mounted)|
| Cables required                                         | 1 X power cable (included) <br> 2 RJ45 cables <br> 2 X SFP+ Twinax copper cables|
| Storage capacity                                        | 100 TB <br> 80 TB usable capacity after RAID 5 protection|
| Network interfaces                                      | 2 X 1 GbE interface - MGMT, DATA 3. <br> MGMT - for management, not user configurable, used for initial setup <br> DATA3 - for data, user configurable, and is dynamic by default <br> MGMT and DATA 3 can also work as 10 GbE <br> 2 X 10 GbE interface - DATA 1, DATA 2 <br> Both are for data, can be configured as dynamic (default) or static |
| Data transfer media                                     | RJ45, SFP+ copper 10 GbE Ethernet  |
| Security                                                | Rugged device casing with tamper-proof custom screws <br> Tamper-evident stickers placed at the bottom of the device|
| Data transfer rate                                      | Up to 80 TB in a day over 10 GbE network interface        |
| Management                                              | Local web UI - one-time initial setup and configuration <br> Azure portal - day-to-day device management        |-->

## <a name="components"></a>组件

Data Box Heavy 包括以下组件：

* **Data Box Heavy 设备** - 具有坚固外壳的物理设备，可安全存储数据。 该设备的可用存储容量为 800 TB。 

    
* **Data Box 设备** - Azure 门户的扩展，使用该扩展可以通过 Web 界面（可从不同的地理位置访问该界面）管理 Data Box Heavy 设备。 使用 Data Box 服务执行的 Data Box Heavy 设备的日常管理。 服务任务包括如何创建和管理订单、查看和管理警报以及管理共享。  

* **本地 Web 用户界面** - 基于 Web 的UI，用于配置设备，以便它可以连接到本地网络，并向 Data Box 服务注册该设备。 此外还可以使用本地 Web UI 关闭和重启该设备、查看复制日志，并联系 Microsoft 支持部门以提交服务请求。


## <a name="the-workflow"></a>工作流

典型的流包括以下步骤：

1. **订购** - 在 Azure 门户中创建订单，提供发货信息和数据的目标 Azure 存储帐户。 如果设备可用，则 Azure 会准备，然后寄送设备并随附发货跟踪 ID。

2. **接收** - 交付设备后，使用指定的电缆为设备连接网络和电源。 打开并连接到设备。 在要从中复制数据的主机上配置设备网络并装载共享。

3. **数据复制** - 将数据复制到 Data Box Heavy 共享。

4. **返回** - 准备、关闭并将设备发回 Azure 数据中心。

5. **上传** - 将设备中的数据自动复制到 Azure。 根据美国国家标准和技术协会 (NIST) 的准则安全擦除设备磁盘数据。

在整个过程中，你会收到有关所有状态更改的电子邮件通知。 

## <a name="region-availability"></a>上市区域

Data Box Heavy 可以根据部署服务的区域、设备的发货国家/地区以及传输数据的目标 Azure 存储帐户传输数据。 

- **服务可用性** - 对于此版本，Data Box Heavy 可以在以下区域获得：
    - 美国的所有公有云区域 - 美国中西部、美国西部 2 区、美国西部、美国中南部、美国中部、美国中北部，美国东部和美国东部 2 区。
    - 欧盟 - 西欧和北欧。
    - 英国 - 英国南部和英国西部。
    - 法国 - 法国中部和法国南部。

- **目标存储帐户** - 存储数据的存储帐户可在服务可用的所有 Azure 区域中获得。 

## <a name="sign-up"></a>注册

Data Box Heavy 以预览版提供，需要进行注册。 执行以下步骤注册 Data Box Heavy：

1. 登录到 Azure 门户： https://aka.ms/azuredatabox。
2. 单击“+”以新建资源。 搜索“Azure Data Box”。 选择“Azure Data Box”服务。

    <!--![The Data Box Heavy sign up 1]()-->

3. 单击“创建”。

    <!--![The Data Box Heavy sign up 2]()-->

4. 选择要用于 Data Box Heavy 预览版的订阅。 选择要部署 Data Box Heavy 资源的区域。 在“Data Box Heavy”选项中，单击“注册”。

   <!--![The Data Box Heavy sign up 3]()-->

5. 回答有关数据所在国家/地区、时间范围、用于数据传输的目标 Azure 服务、网络带宽和数据传输频率的问题。 查看“隐私和条款”，然后选中“Microsoft 可以使用你的电子邮件地址与你取得联系”复选框。

    <!--![The Data Box Heavy sign up 4]()-->

注册并为预览版启用订阅后，即可订购 Data Box Heavy。




