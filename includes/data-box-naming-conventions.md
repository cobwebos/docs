---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: d965b89659a9e3dc01035221a729f094c336eb5b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244639"
---
| 实体                                       | 约定   |
|----------------------------------------------|-----------------------------------------------------|
| 块 Blob 和页 Blob 的容器名称 | 必须是有效的 DNS 名称，长度为 3 到 63 个字符。 <br>  必须以字母或数字开头。 <br> 只能包含小写字母、数字和连字符 (-)。 <br> 每个连字符 (-) 字符的前后必须紧接字母或数字。 <br> 名称中不允许连续的连字符。 |
| Azure 文件的共享名                  | 同上                                          |
| Azure 文件的目录和文件名     |<li> 保留大小写，不区分大小写，并且长度不能超过 255 个字符。 </li><li> 不能以正斜杠 (/) 结尾。 </li><li>如有，则将自动删除。 </li><li> 不允许使用以下字符：<code>" \\ / : \| < > * ?</code></li><li> 必须正确地对保留的 URL 字符进行转义。 </li><li> 不允许使用非法的 URL 路径字符。 码位等\\uE000 不是有效的 Unicode 字符。 某些 ASCII 或 Unicode 字符，如控制字符 (0x00 到 0x1F \\u0081，等等)，也不允许。 有关管理 HTTP/1.1 中 Unicode 字符串的规则，请参阅第 2.2 节 RFC 2616：基本规则和 RFC 3987。 </li><li> 不允许使用以下文件名：LPT1、LPT2、LPT3、LPT4、LPT5、LPT6、LPT7、LPT8、LPT9、COM1、COM2、COM3、COM4、COM5、COM6、COM7、COM8、COM9、PRN、AUX、NUL、CON、CLOCK$、圆点字符 (.) 和两个圆点字符 (..)。</li>|
| 块 Blob 和页 Blob 的 Blob 名称      | </li><li>Blob 名称区分大小写，只能包含字符的任意组合。 </li><li>Blob 名称的长度必须为 1 到 1,024 个字符。 </li><li>必须正确地对保留的 URL 字符进行转义。 </li><li>构成 Blob 名称的路径段数目不能超过 254 个。 路径段是指对应于虚拟目录名称的相邻分隔符（例如，正斜杠“/”）之间的字符串。</li> |
