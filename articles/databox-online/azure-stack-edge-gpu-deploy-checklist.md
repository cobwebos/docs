---
title: 部署 Azure Stack Edge 设备的预先部署清单 |Microsoft Docs
description: 本文介绍了在部署 Azure Stack Edge 设备之前可以收集的信息。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/29/2020
ms.author: alkohli
ms.openlocfilehash: e1233e17dfe2a56e6ea995190b243ed9c64b8945
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2020
ms.locfileid: "89181351"
---
# <a name="deployment-checklist-for-your-azure-stack-edge-device"></a>Azure Stack Edge 设备的部署清单  

本文介绍可在 Azure Stack Edge 设备的实际部署之前收集的信息。 

使用以下清单可确保你在为 Azure Stack 边缘设备发出订单之后以及接收到设备之前具有此信息。 

## <a name="deployment-checklist"></a>部署清单 

| 阶段                             | 参数                                                                                                                                                                                                                           | 详细信息                                                                                                           |
|-----------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| 设备管理               | <li>Microsoft Azure 订阅</li><li>Azure Active Directory 图形 API</li><li>Microsoft Azure 存储帐户</li>|<li>为 Azure Stack Edge/Data Box Gateway 启用了参与者权限</li><li>确保管理员或用户访问权限</li><li>需要访问凭据</li> |
| 设备安装               | 包中的电源线。 <br>对于我们，会提供一条 SVE 18/3 缆线，其额定为 125 V 和15安培，15P 为 C13 (输入输出) 连接器。                                                                                                                                                                                                          | 随设备一起提供。<br>有关详细信息，请参阅[按国家/地区划分的支持的电源线](azure-stack-edge-technical-specifications-power-cords-regional.md)列表                                                                                        |
|                                   | <li>端口1的至少1个 1-GbE RJ-45 网络电缆  </li><li> 至少1个 25-GbE SFP + 铜缆，适用于端口3、端口4、端口5或端口6</li>| 客户需要购买这些缆线。<br>有关设备网卡支持的网络电缆、交换机和收发器的完整列表，请参阅 [Cavium FastlinQ 41000 系列互操作性矩阵](https://www.marvell.com/documents/xalflardzafh32cfvi0z/) 和 [Mellanox 双重端口 25g ConnectX-4 通道网络适配器兼容产品](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products)。| 
| 设备首次连接      | 端口1具有固定 IP (192.168.100.10/24) 用于初始连接。 <li>需要使用便携式计算机直接连接到端口1，并使用 192.168.100.0/24 网络上的 IP 地址。</li><li> 使用位于以下位置的设备本地 UI： `https://192.168.100.10` 进行进一步配置。</li><li> 初始设置完成后，必须至少为设备使用1个 GbE 交换机。 如果连接的交换机不是至少1个 Gbe，本地 web UI 将无法访问。</li>|                                                                                                                   |
| 设备登录                      | 设备管理员密码必须包含8到16个字符。 <br>它必须包含以下字符中的三项：大写字母、小写字母、数字和特殊字符。                                            | 默认密码为 *Password1* ，它将在首次登录时过期。                                                     |
| 网络设置                  | 设备附带2个 x 1 GbE，4 x 25 GbE 网络端口。 <li>端口1仅用于配置管理设置。 可以连接和配置一个或多个数据端口。 </li><li> 端口 2-端口6中的至少一个数据网络接口需要连接到 Internet (与 Azure) 的连接。</li><li> IP 设置支持 DHCP/静态 IPv4 配置。 | 静态 IPv4 配置需要 IP、DNS 服务器和默认网关。                                                                                                                  |
| 计算网络设置     | <li>需要两个 Kubernetes 节点的静态公共 IP，并且至少需要 1 Azure Stack 个静态 IP 才能访问计算模块。</li><li>需要从 Kubernetes 群集外部访问的每个额外服务或容器需要一个 IP。</li>                                                                                                                       | 仅支持静态 IPv4 配置。                                                                      |
|  (可选) Web 代理设置     | <li>Web 代理服务器 IP/FQDN、端口 </li><li>Web 代理用户名、密码</li>                                                                                                                                                                                                    | 当前不支持计算设置。                                                                     |
| 防火墙和端口设置        | 对于设备 Ip，请使用 [列出的 url 模式和端口](azure-stack-edge-system-requirements.md#networking-port-requirements) 。                                                                                                                                                  |                                                                                                                   |
|  (可选) MAC 地址            | 如果需要将 MAC 地址列入允许列表，请从设备的本地 UI 获取连接端口的地址。 |                                                                                                                   |
|  (可选) 网络交换机端口    | 设备承载用于计算的 Hyper-v Vm。 默认情况下，某些网络交换机端口配置不能容纳这些设置。                                                                                                        |                                                                                                                   |
|  (建议) 时间设置       | 配置时区、主 NTP 服务器、辅助 NTP 服务器。                                                                                                                                                                    | 在本地网络上配置主要和辅助 NTP 服务器。<br>如果本地服务器不可用，则可以配置公共 NTP 服务器。                                                    |
|  (可选) 更新服务器设置 | <li>需要在本地网络上更新服务器 IP 地址，到 WSUS 服务器的路径。 </li> | 默认情况下，使用公共 windows 更新服务器。|
| 设备设置                   | <li>DNS 组织中注册的设备名称 </li><li>DNS 域</li> |                                                                                                                   |
|  (可选) 证书                      | 使用设备生成的证书 <br><br> 如果携带自己的证书，则需要： <li>带有 *.cer*格式的受信任的根签名证书 </li><li>*Pfx*格式的终结点证书</li>|适用于 Azure 资源管理器、Blob 存储、本地 web UI 的端点证书。                                                                                                                   |
| 激活                        | 需要 Azure Stack 边缘/Data Box Gateway 资源中的激活密钥。                                                                                                                                                       | 生成后，密钥将在3天后过期。                                                                        |


## <a name="next-steps"></a>后续步骤

准备部署 [Azure Stack Edge 设备](azure-stack-edge-gpu-deploy-prep.md)。



