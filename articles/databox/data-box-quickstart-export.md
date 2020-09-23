---
title: 快速入门：导出 Microsoft Azure Data Box 的数据
description: 了解如何在 Azure 门户中快速导出 Azure Data Box 数据
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: quickstart
ms.date: 07/17/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 01fdaa95dd396e5545b65bada2a9d6410169230b
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2020
ms.locfileid: "90052454"
---
# <a name="quickstart-get-started-with-azure-data-box-to-export-data-from-azure"></a>快速入门：开始使用 Azure Data Box 从 Azure 导出数据

本快速入门介绍如何使用 Azure 门户将数据从 Azure 导出到你的位置中。 步骤包括如何布线、配置以及如何从 Azure 复制数据。 快速入门在 Azure 门户以及设备的本地 Web UI 中完成。

有关详细的分步部署和跟踪说明，请转到[教程：创建 Azure Data Box 的导出订单](data-box-deploy-export-ordered.md)

## <a name="prerequisites"></a>先决条件

开始之前：

* 确保用于 Data Box 服务的订阅是下述类型之一：
  * Microsoft 企业协议 (EA)。 详细了解 [EA 订阅](https://azure.microsoft.com/pricing/enterprise-agreement/)。
  * 云解决方案提供商 (CSP)。 详细了解 [Azure CSP 计划](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)。
  * Microsoft Azure 赞助。 详细了解 [Azure 赞助计划](https://azure.microsoft.com/offers/ms-azr-0036p/)。

* 确保有订阅的所有者或参与者访问权限，以便创建 Data Box 订单。
* 查看 [Data Box 的安全指南](data-box-safety.md)。
* 你拥有可将数据移动到你的位置的 Azure Data Box 设备。 该主机必须
  * 运行[支持的操作系统](data-box-system-requirements.md)。
  * 连接到高速网络。 强烈建议你至少建立一个 10-GbE 连接。 如果 10-GbE 连接不可用，则可使用 1-GbE 数据链路，但复制速度会受影响。
* 必须找到一个可以放置 Data Box 的平面。 如需将设备置于标准的机架上，则需要数据中心机架中的一个 7U 槽。 可以将设备平放或直放在机架中。
* 你已经采购了以下电缆，用于将 Data Box 连接到主机。
  * 两根 10-GbE SFP+ Twinax 铜线（用于 DATA 1、DATA 2 网络接口）
  * 一根 RJ-45 CAT 6 网线（用于管理 (MGMT) 网络接口）
  * 一根 RJ-45 CAT 6A 网线或一根 RJ-45 CAT 6 网线（用于 DATA 3 网络接口，分别配置为 10 Gbps 或 1 Gbps）

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="order"></a>订单

此步骤大约需要 10 分钟。

1. 在 Azure 门户中创建新的 Azure Data Box 资源。
2. 选择为此服务启用的现有订阅，然后选择“导出到 Azure”作为传输类型。 提供数据所驻留的源 Azure 区域，以及数据传输的目标国家/地区 。
3. 选择“Data Box”。 最大可用容量为 80 TB；如果数据大小更大，则可创建多个订单。
4. 选择“添加存储帐户和导出类型”，然后选择“导出选项” 。
5. 输入订单详细信息和发货信息。 如果该服务在你所在的区域中可用，请提供通知电子邮件地址、查看摘要，然后创建订单。

创建订单后，Microsoft 会准备需发货的设备。

## <a name="cable"></a>电缆

此步骤大约需要 10 分钟。

收到 Data Box 后，请执行以下步骤，完成设备的布线、连接和启用。 此步骤大约需要 10 分钟。

1. 如果有证据表明设备已被串改或受损，请勿继续。 请联系 Microsoft 支持部门，让其寄送更换设备。
2. 进行设备布线时，请确保有以下电缆：

   * 接地电源线（已包括），规格为 10 安或更高，一端有 IEC60320 C-13 连接器，用于连接到设备。
   * 一根 RJ-45 CAT 6 网线（用于管理 (MGMT) 网络接口）
   * 两根 10 GbE SFP+ Twinax 铜线（用于 10 Gbps DATA 1、DATA 2 网络接口）
   * 一根 RJ-45 CAT 6A 网线或一根 RJ-45 CAT 6 网线（用于 DATA 3 网络接口，分别配置为 10 Gbps 或 1 Gbps）

3. 在平面上移除和放置设备。

4. 进行设备布线，如下所示。  

    ![已连接了电缆的 Data Box 设备底板](media/data-box-deploy-set-up/data-box-cabled-dhcp.png)  

    1. 将电源线连接到设备。
    2. 使用 RJ-45 CAT 6 网线将主机连接到设备上的 MGMT 端口。
    3. 使用 SFP+ Twinax 铜线连接至少一个 10 Gbps（首选超过 1 Gbps）的网络接口，DATA 1 或 DATA 2 用于数据。
    4. 打开设备。 电源按钮位于设备的前面板上。

## <a name="connect"></a>连接

此步骤大约需要 5-7 分钟才能完成。

1. 若要获取设备密码，请在 [Azure 门户](https://portal.azure.com)中转到“常规” > “设备详细信息” 。
2. 在用于连接到 Data Box 的计算机上，将静态 IP 地址 192.168.100.5 和子网 255.255.255.0 分配给以太网适配器。 通过 `https://192.168.100.10` 访问设备的本地 Web UI。 打开设备后，进行连接可能需要长达 5 分钟的时间。
3. 从 Azure 门户使用密码登录。 此时会出现一个错误，指出网站的安全证书有问题。 按照特定于浏览器的说明转到该网页。
4. 默认情况下，10 Gbps（或 1 Gbps）数据接口的网络设置被配置为 DHCP。 如果需要，可以将该接口配置为静态，并提供一个 IP 地址。

## <a name="copy-data"></a>复制数据

完成此操作所需的时间取决于数据大小和网络速度。

1. 如果使用 Windows 客户端，请使用兼容 SMB 的文件复制工具（如 Robocopy）。 对于 NFS 主机，请使用 `cp` 命令或 `rsync` 来复制数据。 若要详细了解如何使用 Robocopy 来复制数据，请参阅 [Robocopy](https://technet.microsoft.com/library/ee851678.aspx)。
2. 连接到设备共享并开始将数据复制到主机。
<!-- 1. Connect to the device shares using the path:`\\<IP address of your device>\ShareName`. To get the share access credentials, go to the **Connect & copy** page in the local web UI of the Data Box. --> 

## <a name="ship-to-azure"></a>寄送到 Azure

此操作大约需要 10-15 分钟来完成。

1. 在本地 Web UI 中转到“准备交付”页面，开始发货准备。
2. 通过本地 Web UI 关闭设备。 移除设备的电缆。
3. 将设备随附的电源线卷好并安全地放在设备后面。
4. 退货标签应该在电子墨水显示器上可见。 如果电子墨水显示器未显示标签，请转到 Azure 门户中的“概述” > “下载发货标签” 。 下载发货标签，将其贴在设备上。
5. 如果退回设备，请安排区域承运人提货。
6. 承运人提取 Data Box 并进行扫描后，门户中的订单状态将更新为“已提货”。 此外还会显示一个跟踪 ID。

## <a name="clean-up-resources"></a>清理资源

此步骤需要 2-3 分钟才能完成。

* 在订单尚未处理的情况下，可以在 Azure 门户中取消 Data Box 订单。 订单进行处理后，将无法取消订单。 订单处理会不断进行，直到完成。 若要取消订单，请转到“概况”，然后在命令栏中选择“取消” 。

* 在 Azure 门户中的状态显示为“已完成”或“已取消”后，即可删除订单。 若要删除订单，请转到“概况”，然后在命令栏中选择“删除” 。

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何将导出订单从 Azure 部署到 Azure Data Box 中。 若要详细了解 Azure Data Box 的管理，请转到以下教程：

> [!div class="nextstepaction"]
> [使用 Azure 门户管理 Data Box](data-box-portal-admin.md)
