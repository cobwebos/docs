---
title: Microsoft Azure Data Box Heavy 快速入门 | Microsoft Docs
description: 了解如何在 Azure 门户中快速部署 Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: quickstart
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 9d007f4fa8721214a7c97595fa297ef44199119f
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83199132"
---
::: zone target = "docs"

# <a name="quickstart-deploy-azure-data-box-heavy-using-the-azure-portal"></a>快速入门：使用 Azure 门户部署 Azure Data Box Heavy

本快速入门介绍如何使用 Azure 门户部署 Azure Data Box Heavy。 步骤包括如何对 Data Box Heavy 进行布线、配置和数据复制操作，以便将数据上传到 Azure。 快速入门在 Azure 门户以及设备的本地 Web UI 中完成。

有关详细的分步部署和跟踪说明，请转到[教程：订购 Azure Data Box Heavy](data-box-heavy-deploy-ordered.md)

## <a name="prerequisites"></a>先决条件

请先完成下述适用于安装场地、Data Box 服务和设备的配置先决条件，然后部署设备。

### <a name="for-installation-site"></a>安装场地

在开始之前，请确保：

- 设备可以通过所有入口通道。 设备规格：宽度26 英寸，长度48 英寸，高度28 英寸。
- 如果你打算在第一层以外的楼层上安装设备，必须能够通过升降机或活动梯来接触设备。
- 安排两名人员来搬运设备。 设备重约 500 磅。 并配备滚轮。
- 在数据中心内的平坦场地上操作，该场地靠近可用的网络连接，并能够放得下 Data Box Heavy 大小的设备。

### <a name="for-service"></a>对于服务

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

### <a name="for-device"></a>对于设备

在开始之前，请确保：

- 已查看 [Data Box Heavy 安全准则](data-box-safety.md)。
- 将一个主机连接到数据中心网络。 Data Box Heavy 将从此计算机复制数据。 主机必须运行[受支持的操作系统](data-box-heavy-system-requirements.md)。
- 使用一台配备 RJ-45 线缆的笔记本电脑连接到本地 UI 并配置设备。 使用笔记本电脑配置设备的每个节点一次。
- 数据中心已开通高速网络，以及至少一条 10 GbE 连接线路。
- 需要为每个设备节点使用一条 40-Gbps 线缆或 10-Gbps 线缆。 选择与 Mellanox MCX314A-BCCT 网络接口兼容的线缆：
    - 40-Gbps 线缆的设备端需是 QSFP+。
    - 对于 10-Gbps 线缆，需要使用 SFP+ 线缆，其一端插入 10-G 交换机，插入设备的另一端配备 QSFP+ 转 SFP+ 适配器（或 QSA 适配器）。
- 设备背面的托盘中提供了电源线。

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="order"></a>订单

此步骤大约需要 5 分钟。

1. 在 Azure 门户中创建新的 Azure Data Box 资源。
2. 选择为此服务启用的现有订阅，然后选择“导入”作为传输类型。  提供数据所在的**来源国家/地区**，以及数据要传输到的 **Azure 目标区域**。
3. 选择“Data Box Heavy”。  最大可用容量为 770 TB；如果数据大小更大，可创建多个订单。
4. 输入订单详细信息和发货信息。 如果该服务在你所在的区域中可用，请提供通知电子邮件地址、查看摘要，然后创建订单。

创建订单后，Microsoft 会准备需发货的设备。

::: zone-end

::: zone target = "chromeless"

## <a name="cable-and-connect-to-your-device"></a>为设备连接电缆并连接到它

检查先决条件后，为设备连接电缆并连接到设备。

::: zone-end

## <a name="cable-for-power"></a>排布电源线

此步骤大约需要 5 分钟。

收到 Data Box Heavy 后，请执行以下步骤排布电源线，然后打开设备。

1. 如果有证据表明设备已被串改或受损，请勿继续。 请联系 Microsoft 支持部门，让其寄送更换设备。
2. 将设备移到安装场地并锁定背面的滚轮。
3. 将所有四条电源线连接到设备背面的电源接口。
4. 使用前面板上的电源按钮打开设备节点。

## <a name="cable-first-node-for-network"></a>为第一个网络节点布线

完成此步骤大约需要 10-15 分钟。

1. 使用 RJ-45 CAT 6 网线将主机连接到设备上的管理端口 (MGMT)。
2. 使用 Twinax QSFP+ 铜缆连接到至少一个 40 Gbps（比 1 Gbps 更好）网络接口，DATA 1 或 DATA 2 是数据接口。 如果使用 10-Gbps 交换机，请使用 Twinax SFP+ 铜缆和 QSFP+ 转 SFP+ 适配器（QSA 适配器）连接 40-Gbps 数据网络接口。
3. 进行设备布线，如下所示。  

    ![Data Box Heavy 已布线](media/data-box-heavy-quickstart-portal/data-box-heavy-ports-cabled.png)  

## <a name="configure-first-node"></a>配置第一个节点

此步骤大约需要 5-7 分钟才能完成。

1. 若要获取设备密码，请在 [Azure 门户](https://portal.azure.com)中转到“常规”>“设备详细信息”。  在设备的两个节点中使用的密码相同。
2. 在用于连接到 Data Box Heavy 的计算机上，将静态 IP 地址 192.168.100.5 和子网 255.255.255.0 分配给以太网适配器。 通过 `https://192.168.100.10` 访问设备的本地 Web UI。 打开设备后，进行连接可能需要长达 5 分钟的时间。
3. 从 Azure 门户使用密码登录。 此时会出现一个错误，指出网站的安全证书有问题。 按照特定于浏览器的说明转到该网页。
4. 默认情况下，接口（不包括 MGMT）的网络设置配置为 DHCP。 如果需要，可将这些接口配置为静态，并提供一个 IP 地址。

## <a name="cable-and-configure-the-second-node"></a>对第二个节点进行布线和配置

完成此步骤大约需要 15-20 分钟。

遵循对第一个节点使用的步骤，对设备上的第二个节点进行布线和配置。  


::: zone target = "docs"

## <a name="copy-data"></a>复制数据

完成此操作所需的时间取决于数据大小以及复制数据时的网络速度。
 
1. 同时使用两个 40-Gbps 数据接口将数据复制到两个设备节点。

    - 如果使用 Windows 主机，请使用 SMB 兼容的文件复制工具，例如 [Robocopy](https://technet.microsoft.com/library/ee851678.aspx)。
    - 对于 NFS 主机，请使用 `cp` 命令或 `rsync` 来复制数据。
2. 使用路径 `\\<IP address of your device>\ShareName` 连接到设备上的共享。 若要获取共享访问凭据，请在 Data Box Heavy 的本地 Web UI 中转到“连接和复制”页面。 
3. 请确保共享和文件夹名称以及数据遵循 [Azure 存储和 Data Box Heavy 服务限制](data-box-heavy-limits.md)中所述的准则。

## <a name="prepare-to-ship"></a>准备交付

完成此操作所需的时间取决于数据大小。

1. 正常完成数据复制后，在本地 Web UI 中转到“准备交付”页面，并开始准备交付。 
2. 在两个节点上成功完成“准备交付”后，通过本地 Web UI 关闭设备。 

## <a name="ship-to-azure"></a>寄送到 Azure

完成此操作大约需要 15-20 分钟。

1. 拔下线缆，将其放回到设备背面的托盘中。
2. 安排区域快递公司取件。
3. 联系 [Data Box Operations](mailto:DataBoxOps@microsoft.com)，通知收件相关事宜并获取退件发货标签。
4. 应将退件发货标签贴在设备正面透明面板上的显眼位置。

## <a name="verify-data"></a>验证数据

完成此操作所需的时间取决于数据大小。

1. 将 Data Box Heavy 设备连接到 Azure 数据中心网络后，数据会自动上传到 Azure。
2. Data Box 服务会通过 Azure 门户通知数据复制已完成。

    1. 检查错误日志中是否包含任何错误，并采取相应的措施。
    2. 从源中删除数据之前，请确认数据已存储在存储帐户中。

## <a name="clean-up-resources"></a>清理资源

此步骤需要 2-3 分钟才能完成。

- 在订单尚未处理的情况下，可以在 Azure 门户中取消 Data Box Heavy 订单。 订单处理后，无法取消订单。 订单处理会不断进行，直到完成。 若要取消订单，请转到“概况”，然后在命令栏中单击“取消”。  

- 在 Azure 门户中的状态显示为“已完成”或“已取消”后，即可删除订单。   若要删除订单，请转到“概况”，然后在命令栏中单击“删除”。  

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何部署 Data Box Heavy，以帮助将数据导入 Azure。 若要详细了解 Azure Data Box Heavy 的管理，请继续学习以下教程：

> [!div class="nextstepaction"]
> [使用 Azure 门户管理 Data Box Heavy](data-box-portal-admin.md)

::: zone-end
