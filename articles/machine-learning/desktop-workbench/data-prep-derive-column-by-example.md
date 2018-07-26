---
title: 使用 Azure Machine Learning Workbench 的“按示例派生列”转换
description: “按示例派生列”转换的参考文档
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: 026ffed925606e2fdf31461035c9a0d73ad609e9
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2018
ms.locfileid: "39059219"
---
# <a name="derive-column-by-example-transformation"></a>“按示例派生列”转换

通过“按示例派生列”转换，用户可以使用用户提供的派生结果示例创建一个或多个现有列的派生。 派生可以是受支持的字符串、日期和数字转换的任意组合。 

支持以下字符串、日期和数字转换：

**字符串转换：** 

子字符串包括数字和日期的智能提取、串联、事例操作、映射常数值。

**日期转换：** 

日期格式更改、提取日期部分、将时间映射到时间段。

日期转换相当通用，存在一些明显的限制：
* 不支持时区。
* 不支持的某些常见格式：
    * ISO 8601 每年的某一周格式（例如 “2009-W53-7”） 
    * Unix 纪元时间。
* 所有格式都区分大小写（特别是 “4am” 不被识别为时间，而“4AM” 可以）。

**数字转换：** 

Round、Floor、Ceiling、Binning、用零或空间填充、除以或乘以幂 1000。

**组合转换：** 

字符串、数字或日期转换的任意组合。

## <a name="how-to-use-this-transformation"></a>如何使用此转换

若要执行此转换，请执行以下步骤：
1. 选择你想要从中派生值的一列或多列。 
2. 从“转换”菜单中选择“按示例派生列”。 或者，右键单击任意所选列的标头，然后从上下文菜单中选择“按示例派生列”。 转换编辑器随即打开，新列将被添加到所选列最右列的旁边。 所选列可以通过列标头中的复选框来标识。 可以使用列标头中的复选框来添加和删除选择的列。
3. 对一行键入*输出*的示例，然后按 Enter 键。 此时，Workbench 分析输入列以及提供的输出，以便合成可以将给定输入转换为输出的程序。 对数据网格中的所有行执行合成的程序。 对于不确定的和复杂的情况，可能需要多个示例。 根据你是基本模式还是高级模式，可以通过不同的方式提供多个示例。
4. 查看输出并单击“确定”接受转换。

### <a name="transform-editor-basic-mode"></a>转换编辑器：基本模式

基本模式在数据网格中提供内联编辑体验。 你可以通过导航到感兴趣的单元格并输入值来提供输出示例。 

Workbench 分析数据，并尝试识别应由用户审核的边缘事例。 分析数据时，在转换编辑器的标头中显示“正在分析数据”。 完成分析后，标头中会显示“无建议”或“查看下一个建议行”。 你可以通过单击“查看下一个建议行”来浏览边缘事例。 如果某行的值不正确，应该键入正确的值作为附加示例。 

### <a name="transform-editor-advanced-mode"></a>转换编辑器：高级模式

“高级模式”为“按示例派生列”提供了更丰富的体验。 在一个位置显示所有示例。 还可以通过单击“显示建议的示例”在一个地方查看所有边缘事例。 

在高级模式下，可以通过双击网格中的行来添加任何行作为示例行。 将一行复制为示例行后，还可以编辑源列中的数据以创建合成示例。 如此一来，可以添加示例数据中当前不存在的事例。

用户可以通过单击转换编辑器中的链接在“基本模式”和“高级模式”之间切换。

### <a name="transform-editor-send-feedback"></a>转换编辑器：发送反馈

单击“发送反馈”链接，打开“反馈”对话框，其中注释框已预填充所提供的示例用户。 用户应查看注释框中的内容并提供更多详细信息，帮助我们了解问题。 如果用户不希望与 Microsoft 共享数据，则应在单击“发送反馈”按钮之前删除预填充的示例数据。 

### <a name="editing-existing-transformation"></a>编辑现有的转换

用户可以通过选择转换步骤的“编辑”选项编辑现有的“按示例派生列”转换。 单击“编辑”可在“高级模式”下打开转换编辑器，并显示在创建转换期间提供的所有示例。

## <a name="examples-of-string-transformations-by-example"></a>按示例进行的字符串转换的示例


>[!NOTE] 
>以**粗体**显示的值表示为完成所示数据集中的转换而提供的示例。


### <a name="s1-extracting-file-names-from-file-paths"></a>S1. 从文件路径中提取文件名

该事例所需的示例数：2

|输入|输出|
|:-----|:-----|
|C:\Python35\Tools\pynche\TypeinViewer.py|**TypeinViewer.py**|
|C:\Python35\Tools\pynche\webcolors.txt|webcolors.txt|
|C:\Python35\Tools\pynche\websafe.txt|websafe.txt|
|C:\Python35\Tools\pynche\X\rgb.txt|rgb.txt|
|C:\Python35\Tools\pynche\X\xlicense.txt|xlicense.txt|
|C:\Python35\Tools\Scripts\2to3.py|2to3.py|
|C:\Python35\Tools\Scripts\analyze_dxp.py|**analyze_dxp.py**|
|C:\Python35\Tools\Scripts\byext.py|byext.py|
|C:\Python35\Tools\Scripts\byteyears.py|byteyears.py|
|C:\Python35\Tools\Scripts\checkappend.py|checkappend.py|

### <a name="s2-case-manipulation-during-string-extraction"></a>S2. 字符串提取期间的事例操作

该事例所需的示例数：3

|输入|输出|
|:-----|:-----|
|REINDEER CT & DEAD END;  NEW HANOVER; Station 332; 2015-12-10 \@ 17:10:52;|**New Hanover**|
|BRIAR PATH & WHITEMARSH LN;  HATFIELD TOWNSHIP; Station 345; 2015-12-10 \@ 17:29:21;|Hatfield Township|
|HAWS AVE; NORRISTOWN; 2015-12-10 \@ 14:39:21-Station:STA27;|**Norristown**|
|AIRY ST & SWEDE ST;  NORRISTOWN; Station 308A; 2015-12-10 \@ 16:47:36;|**Norristown**|
|CHERRYWOOD CT & DEAD END;  LOWER POTTSGROVE; Station 329; 2015-12-10 \@ 16:56:52;|Lower Pottsgrove|
|CANNON AVE & W 9TH ST;  LANSDALE; Station 345; 2015-12-10 \@ 15:39:04;|Lansdale|
|LAUREL AVE & OAKDALE AVE;  HORSHAM; Station 352; 2015-12-10 \@ 16:46:48;|Horsham|
|COLLEGEVILLE RD & LYWISKI RD;  SKIPPACK; Station 336; 2015-12-10 \@ 16:17:05;|Skippack|
|MAIN ST & OLD SUMNEYTOWN PIKE;  LOWER SALFORD; Station 344; 2015-12-10 \@ 16:51:42;|Lower Salford|
|BLUEROUTE  & RAMP I476 NB TO CHEMICAL RD; PLYMOUTH; 2015-12-10 \@ 17:35:41;|Plymouth|
|RT202 PKWY & KNAPP RD; MONTGOMERY; 2015-12-10 \@ 17:33:50;|Montgomery|
|BROOK RD & COLWELL LN; PLYMOUTH; 2015-12-10 \@ 16:32:10;|Plymouth|

### <a name="s3-date-format-manipulation-during-string-extraction"></a>S3. 字符串提取期间的日期格式操作

该事例所需的示例数：1

|输入|输出|
|:-----|:-----|
|MONTGOMERY AVE & WOODSIDE RD;  LOWER MERION; Station 313; 2015-12-11 \@ 04:11:35;|**12 Nov 2015 4AM**|
|DREYCOTT LN & W LANCASTER AVE;  LOWER MERION; Station 313; 2015-12-11 \@ 01:29:52;|12 Nov 2015 1AM|
|E LEVERING MILL RD & CONSHOHOCKEN STATE RD; LOWER MERION; 2015-12-11 \@ 07:29:58;|12 Nov 2015 7AM|
|PENN VALLEY RD & MANOR RD;  LOWER MERION; Station 313; 2015-12-10 \@ 20:53:30;|12 Oct 2015 8PM|
|BELMONT AVE & OVERHILL RD; LOWER MERION; 2015-12-10 \@ 23:02:27;|12 Oct 2015 11PM|
|W MONTGOMERY AVE & PENNSWOOD RD; LOWER MERION; 2015-12-10 \@ 19:25:22;|12 Oct 2015 7PM|
|ROSEMONT AVE & DEAD END;  LOWER MERION; Station 313; 2015-12-10 \@ 18:43:07;|12 Oct 2015 6PM|
|AVIGNON DR & DEAD END; LOWER MERION; 2015-12-10 \@ 20:01:29-Station:STA24;|12 Oct 2015 8PM|

### <a name="s4-concatenating-strings"></a>S4. 串联字符串

该事例所需的示例数：1

>[!NOTE] 
>在此示例中，特殊字符表示“输出”列中的空格。

|名字|中间名首字母|姓氏|输出|
|:-----|:-----|:-----|:-----|
|Laquanda||Lohmann|Laquanda·Lohmann|
|Claudio|A|Chew|**Claudio·A·Chew**|
|Sarah-Jane|S|Smith|Sarah-Jane·S·Smith|
|Brandi||Blumenthal|Brandi·Blumenthal|
|Jesusita|R|Journey|Jesusita·R·Journey|
|Hermina||Hults|Hermina·Hults|
|Anne-Marie|W|Jones|Anne-Marie·W·Jones|
|Rico||Ropp|Rico·Ropp|
|Lauren-May||Fullmer|Lauren-May·Fullmer|
|Marc|T|Maine|Marc·T·Maine|
|Angie||Adelman|Angie·Adelman|
|John-Paul||Smith|John-Paul·Smith|
|Song|W|Staller|Song·W·Staller|
|Jill||Jefferies|Jill·Jefferies|
|Ruby-Grace|M|Simmons|Ruby-Grace·M·Simmons|

### <a name="s5-generating-initials"></a>S5. 生成首字母

该事例所需的示例数：2

|全名|输出|
|:-----|:-----|
|Laquanda Lohmann|**L.L.**|
|Claudio Chew|C.C.|
|Sarah-Jane Smith|S.S.|
|Brandi Blumenthal|B.B.|
|Jesusita Journey|J.J.|
|Hermina Hults|H.H.|
|Anne-Marie Jones|A.J.|
|Rico Ropp|R.R.|
|Lauren-May Fullmer|L.F.|
|Marc Maine|M.M.|
|Angie Adelman|A.A.|
|John-Paul Smith|**J.S.**|
|Song Staller|S.S.|
|Jill Jefferies|J.J.|
|Ruby-Grace Simmons|R.S.|


### <a name="s6-mapping-constant-values"></a>S6. 映射常数值

该事例所需的示例数：3

|管理性别|输出|
|:-----|:-----:|
|男|**0**|
|女|**1**|
|Unknown|**2**|
|女|1|
|女|1|
|男|0|
|Unknown|2|
|男|0|
|女|1|

## <a name="examples-of-number-transformations-by-example"></a>按示例进行的数字转换的示例

>[!NOTE] 
>以**粗体**显示的值表示为完成所示数据集中的转换而提供的示例。


### <a name="n1-rounding-to-nearest-10"></a>N1. 舍入到最接近 10

该事例所需的示例数：1

|输入|输出|
|-----:|-----:|
|112|**110**|
|117|120|
|11112|11110|
|11119|11120|
|548|550|

### <a name="n2-rounding-down-to-nearest-10"></a>N2. 向下舍入到最接近 10

该事例所需的示例数：2

|输入|输出|
|-----:|-----:|
|112|**110**|
|117|**110**|
|11112|11110|
|11119|11110|
|548|540|

### <a name="n3-rounding-to-nearest-005"></a>N3. 舍入到最接近 0.05

该事例所需的示例数：2

|输入|输出|
|-----:|-----:|
|-75.5812935|**-75.60**|
|-75.2646799|-75.25|
|-75.3519752|-75.35|
|-75.343513|**-75.35**|
|-75.6033497|-75.60|
|-75.283245|-75.30|

### <a name="n4-binning"></a>N4. Binning

该事例所需的示例数：1

|输入|输出|
|-----:|:-----:|
|20.16|**20-25**|
|14.32|10-15|
|5.44|5-10|
|3.84|0-5|
|3.73|0-5|
|7.36|5-10|

### <a name="n5-scaling-by-1000"></a>N5. 缩放 1000

该事例所需的示例数：1

|输入|输出|
|-----:|-----:|
|-243|**-243000**|
|-12.5|-12500|
|-2345.23292|-2345232.92|
|-1202.3433|-1202343.3|
|1202.3433|1202343.3|

### <a name="n6-padding"></a>N6. 填充

该事例所需的示例数：1

|代码|输出|
|-----:|-----:|
|5828|**05828**|
|44130|44130|
|49007|49007|
|29682|29682|
|4759|04759|
|10029|10029|
|7204|07204|

## <a name="examples-of-date-transformations-by-example"></a>按示例进行的日期转换的示例

>[!NOTE] 
>以**粗体**显示的值表示为完成所示数据集中的转换而提供的示例。


### <a name="d1-extracting-date-parts"></a>D1. 提取日期部分

这些“日期”部分是使用相同数据集上的不同示例转换来提取的。 以粗体显示的字符串表示在各自转换中提供的示例。

|DateTime|星期|日期|月份|年龄|小时|分钟|秒|
|-----:|-----:|-----:|-----:|-----:|-----:|-----:|-----:|
|31-Jan-2031 05:54:18|**Fri**|**31**|**Jan**|**2031**|**5**|**54**|**18**|
|17-Jan-1990 13:32:01|Wed|17|1 月|1990|13|32|01|
|14-Feb-2034 05:36:07|Tue|14|Feb|2034|5|36|07|
|14-Mar-2002 13:16:16|Thu|14|3 月|2002|13|16|16|
|21-Jan-1985 05:44:43|Mon|21|1 月|1985|5|44|**43**|
|16-Aug-1985 01:11:56|Fri|16|Aug|1985|1|11|56|
|20-Dec-2033 18:36:29|Tue|20|Dec|2033|18|36|29|
|16-Jul-1984 10:21:59|Mon|16|Jul|1984|10|21|59|
|13-Jan-2038 10:59:36|Wed|13|1 月|2038|10|59|36|
|14-Aug-1982 15:13:54|Sat|14|Aug|1982|15|13|54|
|22-Nov-2030 08:18:08|Fri|22|11 月|2030|8|18|08|
|21-Oct-1997 08:42:58|Tue|21|10 月|1997|8|42|58|
|28-Nov-2006 14:19:15|Tue|28|11 月|2006|14|19|15|
|29-Apr-2031 04:59:45|Tue|29|4 月|2031|4|59|45|
|29-Jan-2032 02:38:36|Thu|29|1 月|2032|2|38|36|
|11-May-2028 15:31:52|Thu|11|May|2028|15|31|52|
|15-Jul-1977 12:45:39|Fri|15|Jul|1977|12|45|39|
|27-Jan-2029 05:55:41|Sat|27|1 月|2029|5|55|41|
|03-Mar-2024 10:17:49|Sun|3|3 月|2024|10|17|49|
|14-Apr-2010 00:23:13|Wed|14|4 月|2010|0|23|13|

### <a name="d2-formatting-dates"></a>D2. 设置日期格式

这些日期格式是使用相同数据集上的不同示例转换来完成的。 以粗体显示的字符串表示在各自转换中提供的示例。

|DateTime|格式1|格式2|格式3|格式4|格式5|
|-----:|-----:|-----:|-----:|-----:|-----:|
|31-Jan-2031 05:54:18|**1/31/2031**|**Friday, January 31, 2031**|**01312031 5:54**|**31/1/2031 5:54 AM**|**Q1 2031**|
|17-Jan-1990 13:32:01|1/17/1990|Wednesday, January 17, 1990|01171990 13:32|17/1/1990 1:32 PM|Q1 1990|
|14-Feb-2034 05:36:07|2/14/2034|Tuesday, February 14, 2034|02142034 5:36|14/2/2034 5:36 AM|Q1 2034
|14-Mar-2002 13:16:16|3/14/2002|Thursday, March 14, 2002|03142002 13:16|14/3/2002 1:16 PM|Q1 2002
|21-Jan-1985 05:44:43|1/21/1985|Monday, January 21, 1985|01211985 5:44|21/1/1985 5:44 AM|Q1 1985
|16-Aug-1985 01:11:56|8/16/1985|Friday, August 16, 1985|08161985 1:11|16/8/1985 1:11 AM|Q3 1985
|20-Dec-2033 18:36:29|12/20/2033|Tuesday, December 20, 2033|12202033 18:36|20/12/2033 6:36 PM|Q4 2033
|16-Jul-1984 10:21:59|7/16/1984|Monday, July 16, 1984|07161984 10:21|16/7/1984 10:21 AM|Q3 1984
|13-Jan-2038 10:59:36|1/13/2038|Wednesday, January 13, 2038|01132038 10:59|13/1/2038 10:59 AM|Q1 2038
|14-Aug-1982 15:13:54|8/14/1982|Saturday, August 14, 1982|08141982 15:13|14/8/1982 3:13 PM|Q3 1982
|22-Nov-2030 08:18:08|11/22/2030|Friday, November 22, 2030|11222030 8:18|22/11/2030 8:18 AM|Q4 2030
|21-Oct-1997 08:42:58|10/21/1997|Tuesday, October 21, 1997|10211997 8:42|21/10/1997 8:42 AM|Q4 1997
|28-Nov-2006 14:19:15|11/28/2006|Tuesday, November 28, 2006|11282006 14:19|28/11/2006 2:19 PM|Q4 2006
|29-Apr-2031 04:59:45|4/29/2031|Tuesday, April 29, 2031|04292031 4:59|29/4/2031 4:59 AM|Q2 2031
|29-Jan-2032 02:38:36|1/29/2032|Thursday, January 29, 2032|01292032 2:38|29/1/2032 2:38 AM|Q1 2032
|11-May-2028 15:31:52|5/11/2028|Thursday, May 11, 2028|05112028 15:31|11/5/2028 3:31 PM|Q2 2028
|15-Jul-1977 12:45:39|7/15/1977|Friday, July 15, 1977|07151977 12:45|15/7/1977 12:45 PM|Q3 1977
|27-Jan-2029 05:55:41|1/27/2029|Saturday, January 27, 2029|01272029 5:55|27/1/2029 5:55 AM|Q1 2029
|03-Mar-2024 10:17:49|3/3/2024|Sunday, March 3, 2024|03032024 10:17|3/3/2024 10:17 AM|Q1 2024
|14-Apr-2010 00:23:13|4/14/2010|Wednesday, April 14, 2010|04142010 0:23|14/4/2010 12:23 AM|Q2 2010


### <a name="d3-mapping-time-to-time-periods"></a>D3. 将时间映射至时间段

这些日期时间到时间段的映射是使用相同数据集上的不同示例转换完成的。 以粗体显示的字符串表示在各自转换中提供的示例。

|DateTime|时间段(秒)|时间段(分钟)|时间段(两小时)|时间段(30 分钟)|
|-----:|-----:|-----:|-----:|-----:|
|31-Jan-2031 05:54:18|**0-20**|**45-60**|**5AM-7AM**|**5:30-6:00**|
|17-Jan-1990 13:32:01|**0-20**|30-45|1PM-3PM|13:30-14:00|
|14-Feb-2034 05:36:07|0-20|30-45|5AM-7AM|5:30-6:00|
|14-Mar-2002 13:16:16|0-20|15-30|1PM-3PM|13:00-13:30|
|21-Jan-1985 05:44:43|40-60|30-45|5AM-7AM|5:30-6:00|
|16-Aug-1985 01:11:56|40-60|0-15|1AM-3AM|1:00-1:30|
|20-Dec-2033 18:36:29|20-40|30-45|5PM-7PM|18:30-19:00|
|16-Jul-1984 10:21:59|40-60|15-30|9AM-11AM|10:00-10:30|
|13-Jan-2038 10:59:36|20-40|45-60|9AM-11AM|10:30-11:00|
|14-Aug-1982 15:13:54|40-60|0-15|3PM-5PM|15:00-15:30|
|22-Nov-2030 08:18:08|0-20|15-30|7AM-9AM|8:00-8:30|
|21-Oct-1997 08:42:58|40-60|30-45|7AM-9AM|8:30-9:00|
|28-Nov-2006 14:19:15|0-20|15-30|1PM-3PM|14:00-14:30|
|29-Apr-2031 04:59:45|40-60|45-60|3AM-5AM|4:30-5:00|
|29-Jan-2032 02:38:36|20-40|30-45|1AM-3AM|2:30-3:00|
|11-May-2028 15:31:52|40-60|30-45|3PM-5PM|15:30-16:00|
|15-Jul-1977 12:45:39|20-40|45-60|11AM-1PM|12:30-13:00|
|27-Jan-2029 05:55:41|40-60|45-60|5AM-7AM|5:30-6:00|
|03-Mar-2024 10:17:49|40-60|15-30|9AM-11AM|10:00-10:30|
|14-Apr-2010 00:23:13|0-20|15-30|11PM-1AM|0:00-0:30|

## <a name="examples-of-composite-transformations-by-example"></a>按示例进行的组合转换的示例

|过程持续时间|开始时间|起始站 ID|起始站纬度|起始站经度|用户类型|列|
|-----:|-----:|-----:|-----:|-----:|-----:|-----:|
|61|2016-01-08 16:09:32|107|42.3625|-71.08822|订阅者|**订阅者在 2016 年 1 月 8 日下午 4 点左右从 107 号站（纬度和经度分别是 42.363 和 -71.088）选了一辆自行车。过程持续时间为 61 分钟**|
|61|2016-01-17 09:28:10|74|42.373268|-71.118579|客户|客户在 2016 年 1 月 17 日上午 9 点左右从 74 号站（纬度和经度分别是 42.373 和 -71.119）选了一辆自行车。 过程持续时间为 61 分钟|
|62|2016-01-25 08:10:26|176|42.386748020450561|-71.119018793106079|订阅者|订阅者在 2016 年 1 月 25 日上午 8 点左右从 176 号站（纬度和经度分别是 42.387 和 -71.119）选了一辆自行车。 过程持续时间为 62 分钟|
|63|2016-01-08 10:10:29|107|42.3625|-71.08822|订阅者|订阅者在 2016 年 1 月 8 日上午 10 点左右从 107 号站（纬度和经度分别是 42.363 和 -71.088）选了一辆自行车。 过程持续时间为 63 分钟|
|64|2016-01-15 19:42:08|68|42.36507|-71.1031|订阅者|订阅者在 2016 年 1 月 15 日晚上 7 点左右从 68 号站（纬度和经度分别是 42.365 和 -71.103）选了一辆自行车。 过程持续时间为 64 分钟|
|64|2016-01-22 18:16:13|115|42.387995|-71.119084|订阅者|订阅者在 2016 年 1 月 22 日下午 6 点左右从 115 号站（纬度和经度分别是 42.388 和 -71.119）选了一辆自行车。 过程持续时间为 64 分钟|
|68|2016-01-18 09:51:52|178|42.359573201090441|-71.101294755935669|订阅者|订阅者在 2016 年 1 月 18 日上午 9 点左右从 178 号站（纬度和经度分别是 42.360 和 -71.101）选了一辆自行车。 过程持续时间为 68 分钟|
|69|2016-01-14 08:57:55|176|42.386748020450561|-71.119018793106079|订阅者|订阅者在 2016 年 1 月 14 日上午 8 点左右从 176 号站（纬度和经度分别是 42.387 和 -71.119）选了一辆自行车。 过程持续时间为 69 分钟|
|69|2016-01-13 22:12:55|141|42.363560158429884|-71.08216792345047|订阅者|订阅者在 2016 年 1 月 13 日晚上 10 点左右从 141 号站（纬度和经度分别是 42.364 和 -71.082）选了一辆自行车。 过程持续时间为 69 分钟|
|69|2016-01-15 08:13:09|176|42.386748020450561|-71.119018793106079|订阅者|订阅者在 2016 年 1 月 15 日上午 8 点左右从 176 号站（纬度和经度分别是 42.387 和 -71.119）选了一辆自行车。 过程持续时间为 69 分钟|


## <a name="technical-notes"></a>技术说明

### <a name="conditional-transformations"></a>条件转换
在某些情况下，找不到满足给定示例的单个转换。 在这种情况下，“按示例派生列”转换尝试根据某些模式对输入进行分组，并为每个组学习单独的转换。 我们称之为**条件转换**。 仅针对具有单个输入列的转换尝试**条件转换**。 

### <a name="reference"></a>引用
有关“按示例进行的字符串转换”技术的更多信息，可以在[此出版物](https://www.microsoft.com/research/publication/automating-string-processing-spreadsheets-using-input-output-examples/)中找到。
