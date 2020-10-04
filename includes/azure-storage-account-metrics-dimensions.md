---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 6bda702a90d1204de6f2b80ced9a4704f1e8426d
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2020
ms.locfileid: "91711160"
---
| 维度名称 | 说明 |
| ------------------- | ----------------- |
| **GeoType** | 来自主要或辅助群集的事务。 可用值包括 **Primary** 和 **Secondary**。 从辅助租户读取对象时，该维度会应用到读取访问异地冗余存储 (RA-GRS)。 |
| **ResponseType** | 事务响应类型。 可用的值包括： <br/><br/> <li>**ServerOtherError**：除描述的错误以外的其他所有服务器端错误 </li> <li>**ServerBusyError**：已经过身份验证的请求返回了 HTTP 503 状态代码。 </li> <li>**ServerTimeoutError**：已经过身份验证的超时请求返回了 HTTP 500 状态代码。 由于服务器错误而发生超时。 </li> <li>**AuthorizationError**：由于未经授权访问数据或者授权失败，经过身份验证的请求失败。 </li> <li>**NetworkError**：由于网络错误，经过身份验证的请求失败。 往往发生于客户端在超时失效之前提前关闭了连接时。 </li><li>**ClientAccountBandwidthThrottlingError**：因为超出了[存储帐户可伸缩性限制](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account?toc=/azure/storage/blobs/toc.json)，在带宽方面对请求进行了限制。</li><li>**ClientAccountRequestThrottlingError**：因为超出了[存储帐户可伸缩性限制](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account?toc=/azure/storage/blobs/toc.json)，在请求速率方面对请求进行了限制。<li>**ClientThrottlingError**：其他客户端限制错误。 不包括 ClientAccountBandwidthThrottlingError 和 ClientAccountRequestThrottlingError。</li> <li>**ClientTimeoutError**：已经过身份验证的超时请求返回了 HTTP 500 状态代码。 如果将客户端的网络超时或请求超时设置为比存储服务预期值更小的值，则预期会发生此超时。 否则，会报告为 ServerTimeoutError。 </li> <li>**ClientOtherError**：除描述的错误以外的其他所有客户端错误。 </li> <li>**成功**：请求成功</li> <li> **SuccessWithThrottling**：请求成功，具体表现在：头一次或头几次尝试时，SMB 客户端会被限制，但重试后会成功。</li> |
| **ApiName** | 操作的名称。 
| **身份验证** | 事务中所用的身份验证类型。 可用的值包括： <br/> <li>**AccountKey**：事务通过存储帐户密钥进行身份验证。</li> <li>**SAS**：事务通过共享访问签名进行身份验证。</li> <li>**OAuth**：事务通过 OAuth 访问令牌进行身份验证。</li> <li>**Anonymous**：事务以匿名方式请求。 不包括预检请求。</li> <li>**AnonymousPreflight**：事务为预检请求。</li> |