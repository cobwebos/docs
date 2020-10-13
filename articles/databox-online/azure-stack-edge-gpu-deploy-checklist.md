---
title: 部署 Azure Stack Edge Pro GPU 设备的预先部署清单 |Microsoft Docs
description: 本文介绍了在部署 Azure Stack Edge Pro GPU 设备之前可以收集的信息。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 09/29/2020
ms.author: alkohli
ms.openlocfilehash: 251b15f72c3216ad3ff4b2983c8cbbd2d9ec6579
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91618244"
---
# <a name="deployment-checklist-for-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU 设备的部署清单  

本文介绍可在 Azure Stack Edge Pro 设备的实际部署之前收集的信息。 

使用以下清单可确保在下一 Azure Stack Edge Pro 设备的订单之后以及收到设备之前，你具有此信息。 

## <a name="deployment-checklist"></a>部署清单 

| 阶段                             | 参数                                                                                                                                                                                                                           | 详细信息                                                                                                           |
|-----------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| 设备管理               | <li>Azure 订阅</li><li>已注册资源提供程序</li><li>Azure 存储帐户</li>|<li>为 Azure Stack Edge Pro/Data Box Gateway、所有者或参与者访问启用。</li><li>在 Azure 门户中，请在 **订阅 > 资源提供程序 > 中转到 Home > 订阅**。 搜索 `Microsoft.DataBoxEdge` 和注册。 `Microsoft.Devices`如果部署 IoT 工作负荷，请重复此步骤。</li><li>需要访问凭据</li> |
| 设备安装               | 包中的电源线。 <br>对于我们，会提供一条 SVE 18/3 缆线，其额定为 125 V 和15安培，15P 为 C13 (输入输出) 连接器。 | 有关详细信息，请参阅[按国家/地区划分的支持的电源线](azure-stack-edge-technical-specifications-power-cords-regional.md)列表  |
|                                   | <li>端口1的至少1个 1-GbE RJ-45 网络电缆  </li><li> 至少1个 25-GbE SFP + 铜缆，适用于端口3、端口4、端口5或端口6</li>| 客户需要购买这些缆线。<br>有关设备网卡支持的网络电缆、交换机和收发器的完整列表，请参阅 [Cavium FastlinQ 41000 系列互操作性矩阵](https://www.marvell.com/documents/xalflardzafh32cfvi0z/) 和 [Mellanox 双重端口 25g ConnectX-4 通道网络适配器兼容产品](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products)。| 
| 设备首次连接      | <li>可以更改其 IPv4 设置的便携式计算机。 此便携式计算机通过交换机或 USB 连接到以太网适配器连接到端口1。  </li><!--<li> A minimum of 1 GbE switch must be used for the device once the initial setup is complete. The local web UI will not be accessible if the connected switch is not at least 1 Gbe.</li>-->|   |
| 设备登录                      | 设备管理员密码，长度为8到16个字符，其中包含以下三项：大写、小写、数字和特殊字符。                                            | 默认密码为 *Password1* ，它将在首次登录时过期。                                                     |
| 网络设置                  | 设备附带2个 x 1 GbE，4 x 25 GbE 网络端口。 <li>端口1仅用于配置管理设置。 可以连接和配置一个或多个数据端口。 </li><li> 端口 2-端口6中的至少一个数据网络接口需要连接到 Internet (与 Azure) 的连接。</li><li> 支持 DHCP 和静态 IPv4 配置。 | 静态 IPv4 配置需要 IP、DNS 服务器和默认网关。   |
| 计算网络设置     | <li>需要2个免费的、静态的、连续 Ip 的 Kubernetes 节点和1个 IoT Edge 服务的静态 IP。</li><li>你将部署的每个额外服务或模块需要一个额外的 IP。</li>| 仅支持静态 IPv4 配置。|
|  (可选) Web 代理设置     | <li>Web 代理服务器 IP/FQDN、端口 </li><li>Web 代理用户名、密码</li> | 计算设置不支持 Web 代理。 |
| 防火墙和端口设置        | 如果使用防火墙，请确保为设备 Ip 允许 [列出的 url 模式和端口](azure-stack-edge-system-requirements.md#networking-port-requirements) 。 |  |
|  (建议) 时间设置       | 配置时区、主 NTP 服务器、辅助 NTP 服务器。 | 在本地网络上配置主要和辅助 NTP 服务器。<br>如果本地服务器不可用，则可以配置公共 NTP 服务器。                                                    |
|  (可选) 更新服务器设置 | <li>需要在本地网络上更新服务器 IP 地址，到 WSUS 服务器的路径。 </li> | 默认情况下，使用公共 windows 更新服务器。|
| 设备设置 | <li>设备完全限定的域名 (FQDN)  </li><li>DNS 域</li> | |
|  (可选) 证书  | 若要测试非生产工作负荷，请使用 " [生成证书" 选项](azure-stack-edge-gpu-deploy-configure-certificates.md#generate-device-certificates) <br><br> 如果使用自己的证书，包括签名链，则以适当的格式[添加证书](azure-stack-edge-gpu-deploy-configure-certificates.md#bring-your-own-certificates)。| 仅当更改设备名称和/或 DNS 域时才配置证书。 |
| 激活  | 需要 Azure Stack Edge Pro/Data Box Gateway 资源中的激活密钥。    | 生成后，密钥将在3天后过期。 |

<!--
| (Optional) MAC Address            | If MAC address needs to be whitelisted, get the address of the connected port from local UI of the device. |                                                                                                                   |
| (Optional) Network switch port    | Device hosts Hyper-V VMs for compute. Some network switch port configurations don’t accommodate these setups by default.                                                                                                        |                                                                                                                   |-->


## <a name="next-steps"></a>后续步骤

准备部署 [Azure Stack Edge Pro 设备](azure-stack-edge-gpu-deploy-prep.md)。
