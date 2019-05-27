---
author: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/09/2018
ms.author: nitinme
ms.openlocfilehash: 1d8340054ace25a0cdc36eef9c3d5b4238a6f99b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "66124671"
---
服务和订阅类型决定了每秒可以发出的查询数 (QPS)。 请确保应用程序包含防止超出配额限制的逻辑。 如果达到或超出 QPS 限制，则请求会失败，系统会返回 HTTP 429 状态代码。 响应包含 `Retry-After` 标头，该标头指示需等待多久才能发送另一请求。

## <a name="denial-of-service-versus-throttling"></a>拒绝服务与限制

该服务区分拒绝服务 (DoS) 攻击和 QPS 违规。 如果该服务怀疑存在 DoS 攻击，则请求会成功（HTTP 状态代码为“200 正常”）。 但是，响应正文为空。