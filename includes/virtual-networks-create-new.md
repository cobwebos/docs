---
title: include 文件
description: include 文件
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.topic: include
ms.date: 03/01/2020
ms.author: allensu
ms.custom: include file
ms.openlocfilehash: 81db46b52c9b4fe800f2fbfeadad966995d66e12
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2020
ms.locfileid: "78244985"
---
## <a name="create-the-virtual-network"></a>创建虚拟网络

在本部分，请创建虚拟网络和子网。

1. 在屏幕的左上方选择“创建资源”>“网络”>“虚拟网络”，或者在搜索框中搜索“虚拟网络”。  

2. 在“创建虚拟网络”  的“基本信息”选项卡中输入或选择以下信息  ：

    | **设置**          | **值**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **项目详细信息**  |                                                                 |
    | 订阅     | 选择 Azure 订阅                                  |
    | 资源组   | 选择“新建”  ，输入 **\<resource-group-name>** ，然后选择“确定”，或根据参数选择现有 **\<resource-group-name>** 。 |
    | **实例详细信息** |                                                                 |
    | 名称             | 输入 **\<virtual-network-name>**                                    |
    | 区域           | 选择 **\<region-name>** |

3. 选择“IP 地址”选项卡  ，或选择页面底部的“下一步:  IP 地址”按钮。

4. 在“IP 地址”  选项卡上，输入以下信息：

    | 设置            | 值                      |
    |--------------------|----------------------------|
    | IPv4 地址空间 | 输入 **\<IPv4-address-space>** |

5. 在“子网名称”  下，选择词语“默认”  。

6. 在“编辑子网”中输入以下信息： 

    | 设置            | 值                      |
    |--------------------|----------------------------|
    | 子网名称 | 输入 **\<subnet-name>** |
    | 子网地址范围 | 输入 **\<subnet-address-range>**

7. 选择“保存”。 

8. 选择“查看 + 创建”  选项卡，或选择“查看 + 创建”  按钮。

9. 选择“创建”  。