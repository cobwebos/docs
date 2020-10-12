---
title: 管理 Azure 支持请求
description: 介绍如何查看支持请求，发送消息，更改请求严重性级别，与 Azure 支持共享诊断信息，重新打开已关闭的支持请求，并上传文件。
author: mgblythe
tags: billing
ms.assetid: 86697fdf-3499-4cab-ab3f-10d40d3c1f70
ms.service: azure-supportability
ms.topic: how-to
ms.date: 06/30/2020
ms.author: mblythe
ms.openlocfilehash: f3b4806bf46750d74a54f68bd2ab58e402e75091
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85852224"
---
# <a name="manage-an-azure-support-request"></a>管理 Azure 支持请求

[创建 Azure 支持请求](how-to-create-azure-support-request.md)后，可以在[Azure 门户](https://portal.azure.com)中对其进行管理，本文将对此进行介绍。 还可使用 [Azure 支持票证 REST API](/rest/api/support) 以编程方式创建和管理请求。

## <a name="view-support-requests"></a>查看支持请求

转到 "**帮助 + 支持**  >   **所有支持请求**"，查看支持请求的详细信息和状态。

:::image type="content" source="media/how-to-manage-azure-support-request/all-requests-lower.png" alt-text="所有支持请求":::

在此页上，你可以搜索、筛选和排序支持请求。 选择支持请求以查看详细信息，包括其严重性以及与请求关联的任何消息。

## <a name="send-a-message"></a>发送消息

1. 在 " **所有支持请求** " 页上，选择支持请求。

1. 在 " **支持请求** " 页上，选择 " **新建消息**"。

1. 输入消息，然后选择 " **提交**"。

## <a name="change-the-severity-level"></a>更改严重性级别

> [!NOTE]
> 最大严重性级别取决于你的 [支持计划](https://azure.microsoft.com/support/plans)。
>

1. 在 " **所有支持请求** " 页上，选择支持请求。

1. 在 " **支持请求** " 页上，选择 " **更改**"。

    :::image type="content" source="media/how-to-manage-azure-support-request/change-severity.png" alt-text="所有支持请求" **更改**"。

        :::image type="content" source="media/how-to-manage-azure-support-request/unassigned-can-change-severity.png" alt-text="所有支持请求":::

    - 如果你的请求已分配，你会看到如下所示的屏幕。 选择 **"确定"**，然后创建 [新消息](#send-a-message) 以请求严重性级别的更改。

        :::image type="content" source="media/how-to-manage-azure-support-request/assigned-cant-change-severity.png" alt-text="所有支持请求":::

## <a name="share-diagnostic-information-with-azure-support"></a>与 Azure 支持共享诊断信息

创建支持请求时，默认情况下会选择 " **共享诊断信息** " 选项。 这允许 Azure 支持人员收集 Azure 资源中的 [诊断信息](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) ：

* 创建请求后，不能清除此选项。

* 如果在创建请求时清除了该选项，则可以在创建请求后选择它。

    1. 在 " **所有支持请求** " 页上，选择支持请求。
    
    1. 在 " **支持请求** " 页上，选择 " **授予权限**"，然后选择 **"是** " 和 **"确定"**。
    
        :::image type="content" source="media/how-to-manage-azure-support-request/grant-permission-manage.png" alt-text="所有支持请求":::

## <a name="upload-files"></a>上传文件

您可以使用 "文件上传" 选项来上载诊断文件或您认为与支持请求相关的任何其他文件。

1. 在 " **所有支持请求** " 页上，选择支持请求。

1. 在 " **支持请求** " 页上，浏览查找文件，然后选择 " **上传**"。 如果有多个文件，请重复该过程。

    :::image type="content" source="media/how-to-manage-azure-support-request/file-upload.png" alt-text="所有支持请求":::

### <a name="file-upload-guidelines"></a>文件上传指南

使用 "文件上传" 选项时，请遵循以下准则：

* 为保护隐私，请不要在上传内容中含入任何个人信息。
* 文件名不得超过 110 个字符。
* 不能上载多个文件。
* 文件大小不能超过 4 MB。
* 所有文件必须具有文件扩展名，如 *.docx* 或 *.xlsx*。 下表显示了允许上载的文件扩展名。

| 0-9, A-C    | D-G   | H-M         | N-P   | R-T      | U-W        | X-Z     |
|-------------|-------|-------------|-------|----------|------------|---------|
| .7z         | .dat  | .hwl        | .odx  | .rar     | .tdb       | .xlam   |
| .a          | .db   | .ics        | .oft  | .rdl     | .tdf       | .xlr    |
| .abc        | .DMP  | .ini        | .old  | .rdlc    | .text      | .xls    |
| .adm        | .do_  | .java       | .one  | .re_     | .thmx      | .xlsb   |
| .aspx       | .doc  | .jpg        | .osd  | .reg     | .tif       | .xlsm   |
| .ATF        | .docm | .LDF        | .OUT  | .remove  | .trc       | .xlsx   |
| .b          | .docx | .letterhead | P1   | .ren     | .TTD       | .xlt    |
| .ba_        | .dotm | .lnk        | .pcap | .rename  | .tx_       | .xltx   |
| .bak        | .dotx | .lo_        | .pdb  | .rft     | .txt       | .xml    |
| .bat        | .dtsx | .log        | .pdf  | .rpt     | .uccapilog | .xmla   |
| .blg        | .eds  | .lpk        | .piz  | .rte     | .uccplog   | .xps    |
| .CA_        | .emf  | .manifest   | .pmls | .rtf     | .udcx      | .xsd    |
| .CAB        | .eml  | .master     | .png  | .run     | .vb_       | .xsn    |
| .cap        | .emz  | .mdmp       | .potx | .saz     | .vbs_      | .xxx    |
| .catx       | .err  | .mof        | .ppt  | .sql     | .vcf       | .z_     |
| .CFG        | .etl  | .mp3        | .pptm | .sqlplan | .vsd       | .z01    |
| .compressed | .evt  | .mpg        | .pptx | .stp     | .wdb       | .z02    |
| .Config     | .evtx | .ms_        | .prn  | .svclog  | .wks       | .zi     |
| .cpk        | .EX   | .msg        | .psf  |   -       | .wma       | .zi_    |
| .cpp        | .ex_  | .msi        | .pst  |  -        | .wmv       | .zip    |
| .cs         | .ex0  | .mso        | .pub  | -         | .wmz       | .zip_   |
| .CSV        | .FRD  | .msu        | -      |-          | .wps       | .zipp   |
| .cvr        | .gif  | .nfo        | -      |-          | .wpt       | .zipped |
| -            | .guid | -            | -      | -         | .wsdl      | .zipped  |
| -            | .gz   | -            | -      | -         | .wsp       | .zipx   |
| -            | -      | -            | -      | -         | .wtl       | .zit    |
| -            | -      | -            | -      | -         |     -       | .zix    |
| -            | -      | -            | -      | -         |  -          | .zzz    |

## <a name="reopen-a-closed-request"></a>重新打开已关闭的请求

如果需要重新打开已关闭的支持请求，请创建一 [条新消息](#send-a-message)，该消息将自动重新打开请求。

## <a name="next-steps"></a>后续步骤

[如何创建 Azure 支持请求](how-to-create-azure-support-request.md)

[Azure 支持票证 REST API](/rest/api/support)
