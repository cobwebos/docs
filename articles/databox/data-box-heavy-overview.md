---
title: Microsoft Azure Data Box Heavy 概述 | Microsoft Docs
description: 介绍 Azure Data Box - 一种用于将大量数据传输到 Azure 的混合解决方案
services: databox
documentationcenter: NA
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: overview
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 0f4657cdd71a104ca111f62a6e9757b5a33b46e8
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592297"
---
# <a name="what-is-azure-data-box-heavy"></a>什么是 Azure Data Box Heavy？

使用 Azure Data Box Heavy 能够以快速、廉价且可靠的方式将数百 TB 的数据发送到 Azure。 将数据转移到 Azure 的方式是这样的：我们向你寄送一个存储容量为 1 PB 的 Data Box Heavy 设备，然后你将数据填充到其中并将其寄回 Microsoft。 该设备具有坚固的外壳，可在运输过程中保护数据。

在数据中心收到设备后，请使用本地 Web UI 对其进行设置。 将数据从服务器复制到设备，然后将设备发回 Azure。 在 Azure 数据中心，数据将上传到 Azure 存储帐户。 可以在 Azure 门户中跟踪整个端到端过程。


> [!IMPORTANT]
> - 若要请求设备，请在 [Azure 门户](https://portal.azure.com)中注册。


## <a name="use-cases"></a>用例

Data Box Heavy 最适合数据大小为数百 TB 的情况，这种情况下通过网络将数据上传到 Azure 是很难做到的。 数据移动的方式可以是一次性的、定期的，或者先执行批量数据传输，再定期传输。 以下是 Data Box Heavy 可用于数据传输的各种方案。

 - **一次性迁移** - 需要将大量本地数据移到 Azure 时。
     - 将媒体库从脱机磁带移动到 Azure 以创建联机媒体库。
     - 将 VM 场、SQL server 和应用程序迁移到 Azure。
     - 将历史数据移至 Azure，以便使用 HDInsight 进行深入分析和报告。

 - **初始批量传输** - 需要先使用 Data Box Heavy（种子）执行初始批量传输，然后通过网络执行增量传输时。
     - 例如，Data Box Heavy 和备份解决方案合作伙伴用于将初始大型历史备份移至 Azure。 完成后，增量数据将通过网络传输到 Azure 存储。

 - **定期上传** - 定期生成大量数据，并需要将这些数据移到 Azure 时。 例如，能源勘探领域生成有关钻井平台和风力发电农场的视频内容。

## <a name="benefits"></a>优点

Data Box Heavy 用于在几乎不影响网络的情况下将大量数据移到 Azure。 该解决方案具有以下优点：

- **速度** - Data Box Heavy 使用高性能 40 Gbps 网络接口。

- **安全** - Data Box Heavy 为设备、数据和服务提供内置安全保护。
    - 该设备具有坚固设备外壳，通过防破坏螺钉和防破坏贴纸固定。
    - 设备上的数据始终使用 AES 256 位加密进行保护。
    - 只能使用 Azure 门户中提供的密钥解锁设备。
    - 服务受 Azure 安全功能的保护。
    - 将数据上传到 Azure 后，会根据美国国家标准和技术协会 (NIST) 800-88r1 标准完全擦除设备上的磁盘数据。


## <a name="features-and-specifications"></a>功能和规格

此版本中的 Data Box Heavy 设备具有以下功能。

| 规范                                          | 说明              |
|---------------------------------------------------------|--------------------------|
| 重量                                                  | 约 500 磅。 <br>用于运输的锁定轮上的装置|
| 维度                                              | 宽度：26 英寸 高度：28 英寸 长度：48 英寸 |
| 机架空间                                              | 无法进行机架安装|
| 所需的电缆                                         | 包括 4 根接地的120 V/10 A 电源线 (NEMA 5-15) <br> 设备支持最高 240 V 电源，提供 C-13 电源插座 <br> 使用兼容 [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html) 的网络电缆  |
| 幂                                                    | 4 个内置电源 (PSU)，在两个设备节点之间共享 <br> 1,200 瓦特典型功耗|
| 存储容量                                        | 约 1-PB（原始），每个包含 70 个 14 TB 的磁盘 <br> 770 TB 可用容量|
| 节点数                                          | 每个设备 2 个独立节点（每个 500 TB） |
| 每个节点的网络接口数                             | 每个节点 4 个网络接口 <br><br> MGMT、DATA3 <ul><li> 2 X 1-GbE 接口 </li><li> MGMT 用于管理和初始设置，用户不可配置 </li><li> 默认情况下，DATA3 是用户可配置的动态主机配置协议 (DHCP)</li><li>1-GbE 网络接口也可配置为 10-GbE 接口</li></ul>DATA1、DATA2 数据接口 <ul><li>2 X 40-GbE 接口 </li><li> 用户可配置为 DHCP（默认）或静态</li></ul>|


## <a name="components"></a>组件

Data Box Heavy 包括以下组件：

* **Data Box Heavy 设备** - 具有坚固外壳的物理设备，可安全存储数据。 该设备的可用存储容量为 770 TB。
    
* **Data Box 设备** - Azure 门户的扩展，使用该扩展可以通过 Web 界面（可从不同的地理位置访问该界面）管理 Data Box Heavy 设备。 使用 Data Box 服务管理 Data Box Heavy 设备。 服务任务包括如何创建和管理订单、查看和管理警报以及管理共享。  

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

有关 Data Box Heavy 的区域可用性的最新信息，请访问[按区域划分的 Azure 产品](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)。

## <a name="sign-up"></a>注册

按照以下步骤注册 Data Box Heavy：

1. 登录到 Azure 门户： https://portal.azure.com 。
2. 单击“+ 创建资源”，创建新的资源  。 搜索“Azure Data Box”  。 选择“Azure Data Box”服务  。
3. 单击“创建”。 
4. 选择要用于 Data Box Heavy 的订阅。 选择要部署 Data Box Heavy 资源的区域。 在“Data Box Heavy”选项中，单击“注册”   。
5. 回答有关数据所在国家/地区、时间范围、数据要传输到的目标 Azure 服务、网络带宽和数据传输频率的问题。 查看“隐私和条款”，然后选中“Microsoft 可以使用你的电子邮件地址与你取得联系”复选框。

注册后，即可订购 Data Box Heavy。

    
