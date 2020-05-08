---
title: Azure Integration Runtime IP 地址
description: 了解必须允许来自哪些 IP 地址的入站流量，以便正确配置防火墙来保护对数据存储的网络访问。
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/06/2020
ms.openlocfilehash: e3fae34d0bb1aaaa2471f1cc9fd55ef9b4b73dfd
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594281"
---
# <a name="azure-integration-runtime-ip-addresses"></a>Azure Integration Runtime IP 地址

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure 集成运行时使用的 IP 地址取决于 Azure 集成运行时所在的区域。 同一区域中的所有 Azure 集成运行时都使用相同的 IP 地址范围。 

> [!IMPORTANT]  
> 数据流不支持使用固定 IP 范围。
>
> 可以将这些 IP 范围用于数据移动、管道和外部活动执行。 这些 IP 范围可用于数据存储/网络安全组（NSG）/防火墙中的允许列表，以便从 Azure 集成运行时进行入站访问。 

## <a name="azure-integration-runtime-ip-addresses-specific-regions"></a>Azure Integration Runtime IP 地址：特定区域

允许来自列出资源的特定 Azure 区域中的 Azure 集成运行时的 IP 地址的流量：

|                | 区域              | IP 地址                                                 |
| -------------- | ------------------- | ------------------------------------------------------------ |
| 亚洲           | 东亚           | 20.189.104.128/25， </br>20.189.106.0/26， </br>13.75.39.112/28 |
| &nbsp;         | 东南亚      | 20.43.128.128/25， </br>20.43.130.0/26， </br>40.78.236.176/28 |
| 澳大利亚      | 澳大利亚东部      | 20.37.193.0/25，</br>20.37.193.128/26，</br>13.70.74.144/28    |
| &nbsp;         | 澳大利亚东南部 | 20.42.225.0/25，</br>20.42.225.128/26，</br>13.77.53.160/28    |
| 巴西         | 巴西南部        | 191.235.224.128/25，</br>191.235.225.0/26，</br>191.233.205.160/28 |
| Canada         | 加拿大中部      | 52.228.80.128/25，</br>52.228.81.0/26，</br>13.71.175.80/28    |
| 中国          | 中国东部 2        | 40.73.172.48/28，</br>52.130.0.128/25，</br>52.130.1.0/26      |
| 欧洲         | 北欧        | 20.38.82.0/23，</br>20.38.80.192/26，</br>13.69.230.96/28      |
| &nbsp;         | 西欧         | 40.74.26.0/23，</br>40.74.24.192/26，</br>13.69.67.192/28      |
| 法国         | 法国中部      | 20.43.40.128/25，</br>20.43.41.0/26，</br>40.79.132.112/28     |
| 印度          | 印度中部       | 52.140.104.128/25，</br>52.140.105.0/26，</br>20.43.121.48/28  |
| 日本          | 日本东部          | 20.43.64.128/25，</br>20.43.65.0/26，</br>13.78.109.192/28     |
| 韩国          | 韩国中部       | 20.41.64.128/25，</br>20.41.65.0/26，</br>52.231.20.64/28      |
| 南非   | 南非北部  | 102.133.124.104/29，</br>102.133.216.128/25，</br>102.133.217.0/26 |
| United Kingdom | 英国南部            | 51.104.24.128/25，</br>51.104.25.0/26，</br>51.104.9.32/28     |
| United States  | 美国中部          | 20.37.154.0/23，</br>20.37.156.0/26，</br>20.44.10.64/28       |
|                | 美国东部             | 20.42.2.0/23，</br>20.42.4.0/26，</br>40.71.14.32/28           |
|                | 美国东部 2            | 20.41.2.0/23，</br>20.41.4.0/26，</br>20.44.17.80/28           |
|                | 美国东部 2 EUAP      | 20.39.8.128/26，</br>20.39.8.96/27，</br>40.75.35.144/28       |
|                | 美国中北部    | 40.80.185.0/24，</br>40.80.186.0/25，</br>52.162.111.48/28      |
|                | 美国中南部    | 40.119.9.0/25，</br>40.119.9.128/26，</br>13.73.244.32/28      |
|                | 美国中西部     | 52.150.137.128/25，</br>52.150.136.192/26，</br>13.71.199.0/28 |
|                | 美国西部             | 40.82.250.0/23，</br>40.82.249.64/26，</br>13.86.219.208/28    |
|                | 美国西部 2            | 20.42.132.0/23，</br>20.42.129.64/26，</br>13.66.143.128/28    |
|                | US Gov 弗吉尼亚州     | 52.127.45.96/28，</br>52.127.48.128/25，</br>52.127.49.0/26    |

## <a name="known-issue-with-azure-storage"></a>Azure 存储的已知问题

* 连接到 Azure 存储帐户时，IP 网络规则不会影响源自 Azure 集成运行时的请求与存储帐户所在的区域。 有关更多详细信息，请[参阅此文](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range)。 

  相反，我们建议[在连接到 Azure 存储时使用受信任的服务](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993)。 

## <a name="next-steps"></a>后续步骤

* [Azure 数据工厂中数据移动的安全注意事项](data-movement-security-considerations.md)
