---
author: manish-shukla01
ms.author: manshuk
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 08-03-2018
ms.openlocfilehash: 41216fe12e10f72f76043f1a8bc361b538259ac1
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39720889"
---
# <a name="virtual-machine-size-flexibility-with-reserved-vm-instances"></a>虚拟机预留实例的虚拟机大小灵活性

使用优化的可以灵活调节实例大小的虚拟机预留实例时，所购买的预留可以应用到同一大小系列组中的虚拟机 (VM) 大小。 例如，如果购买在 DSv2 系列表中列出的某个 VM 大小（例如 Standard_DS5_v2）的预留，则预留折扣可以应用到在该表中列出的其他四个大小：

- Standard_DS1_v2
- Standard_DS2_v2
- Standard_DS3_v2
- Standard_DS4_v2

但是，该预留折扣不适用于在其他表中列出的 VM 大小，例如高内存 DSv2 系列表中的 VM 大小 Standard_DS11_v2、Standard_DS12_v2 等。

在大小系列组中，预留折扣适用于多少 VM 取决于在购买预留时选取的 VM 大小， 也取决于运行的 VM 的大小。 在下述表中列出的比率列比较了每个 VM 大小在该组中的相对占用量。 请使用比率值来计算如何将预留折扣应用到运行的 VM。

## <a name="examples"></a>示例

以下示例使用 DSv2 系列表中的大小和比率。

 你购买一个大小为 Standard_DS4_v2 的虚拟机预留实例，其中的比率或相对于该系列中其他大小的占用量为 8。

- 方案 1：运行 8 个比率为 1 的 Standard_DS1_v2 大小的 VM。 预留折扣适用于这所有 8 个 VM。
- 方案 2：运行 2 个 Standard_DS2_v2 大小的 VM，每个的比率为 2。 另外，运行一个比率为 4 的 Standard_DS3_v2 大小的 VM。 总占用量为 2+2+4=8。 因此，预留折扣适用于这所有 3 个 VM。
- 方案 3：运行 1 个比率为 16 的 Standard_DS5_v2 大小的 VM。 预留折扣适用于该 VM 的半数计算费用。

以下部分介绍了在购买优化的可以灵活调整实例大小的虚拟机预留实例时，同一大小系列组中有哪些大小。

## <a name="b-series"></a>B 系列

| 大小 | 比率|
|---|---|
| Standard_B1s | 1 |
|Standard_B2s|4|

有关详细信息，请参阅 [B 系列可突增虚拟机大小](../articles/virtual-machines/windows/b-series-burstable.md)。

## <a name="b-series-high-memory"></a>高内存 B 系列

| 大小 | 比率|
|---|---|
| Standard_B1ms |1|
|Standard_B2ms|4|
|Standard_B4ms|8|
|Standard_B8ms|16|

有关详细信息，请参阅 [B 系列可突增虚拟机大小](../articles/virtual-machines/windows/b-series-burstable.md)。

## <a name="d-series"></a>D 系列

| 大小 | 比率|
|---|---|
| Standard_D1|1|
|Standard_D2|2|
|Standard_D3|4|
|Standard_D4|8|

有关详细信息，请参阅[前几代虚拟机大小](../articles/virtual-machines/windows/sizes-previous-gen.md)。

## <a name="d-series-high-memory"></a>高内存 D 系列

| 大小 | 比率|
|---|---|
| Standard_D11|1|
|Standard_D12|2|
|Standard_D13|4|
|Standard_D14|8|

有关详细信息，请参阅[前几代虚拟机大小](../articles/virtual-machines/windows/sizes-previous-gen.md)。

## <a name="ds-series"></a>Ds 系列

| 大小 | 比率|
|---|---|
|Standard_DS1|1|
|Standard_DS2|2|
|Standard_DS3|4|
|Standard_DS4|8|

有关详细信息，请参阅[前几代虚拟机大小](../articles/virtual-machines/windows/sizes-previous-gen.md)。

## <a name="ds-series-high-memory"></a>高内存 Ds 系列

| 大小 | 比率|
|---|---|
|Standard_DS11|1|
|Standard_DS12|2|
|Standard_DS13|4|
|Standard_DS14|8|

有关详细信息，请参阅[前几代虚拟机大小](../articles/virtual-machines/windows/sizes-previous-gen.md)。

## <a name="dsv2-series"></a>DSv2-series

| 大小 | 比率|
|---|---|
|Standard_DS1_v2|1|
|Standard_DS2_v2|2|
|Standard_DS3_v2|4|
|Standard_DS4_v2|8|
|Standard_DS5_v2|16|

有关详细信息，请参阅[常规用途虚拟机大小](../articles/virtual-machines/windows/sizes-general.md#dv2-series)。

## <a name="dsv2-series-high-memory"></a>高内存 DSv2 系列

| 大小 | 比率|
|---|---|
|Standard_DS11_v2|1|
|Standard_DS12_v2|2|
|Standard_DS13_v2|4|
|Standard_DS14_v2|8|
|Standard_DS15_v2|10|

有关详细信息，请参阅[内存优化虚拟机大小](../articles/virtual-machines/windows/sizes-memory.md#dsv2-series-11-15)。

## <a name="dsv3-series"></a>DSv3 系列

| 大小 | 比率|
|---|---|
|Standard_D2s_v3|1|
|Standard_D4s_v3|2|
|Standard_D8s_v3|4|
|Standard_D16s_v3|8|
|Standard_D32s_v3|16|
|Standard_D64s_v3|32|

有关详细信息，请参阅[常规用途虚拟机大小](../articles/virtual-machines/windows/sizes-general.md#dsv3-series-sup1sup)。

## <a name="dv2-series"></a>Dv2 系列

| 大小 | 比率|
|---|---|
|Standard_D1_v2|1|
|Standard_D2_v2|2|
|Standard_D3_v2|4|
|Standard_D4_v2|8|
|Standard_D5_v2|16|

有关详细信息，请参阅[常规用途虚拟机大小](../articles/virtual-machines/windows/sizes-general.md#dv2-series)。

## <a name="dv2-series-high-memory"></a>高内存 Dv2 系列

| 大小 | 比率|
|---|---|
|Standard_D11_v2|1|
|Standard_D12_v2|2|
|Standard_D13_v2|4|
|Standard_D14_v2|8|
|Standard_D15_v2|10|

有关详细信息，请参阅[内存优化虚拟机大小](../articles/virtual-machines/windows/sizes-memory.md#dv2-series-11-15)。

## <a name="dv3-series"></a>Dv3 系列

| 大小 | 比率|
|---|---|
| Standard_D2_v3|1|
|Standard_D4_v3|2|
|Standard_D8_v3|4|
|Standard_D16_v3|8|
|Standard_D32_v3|16|
|Standard_D64_v3|32|

有关详细信息，请参阅[常规用途虚拟机大小](../articles/virtual-machines/windows/sizes-general.md#dv3-series-sup1sup)。

## <a name="esv3-series"></a>ESv3 系列

| 大小 | 比率|
|---|---|
| Standard_E2s_v3|1|
|Standard_E4s_v3|2|
|Standard_E8s_v3|4|
|Standard_E16s_v3|8|
|Standard_E32s_v3|16|
|Standard_E64s_v3|32|

有关详细信息，请参阅[内存优化虚拟机大小](../articles/virtual-machines/windows/sizes-memory.md#esv3-series)。

## <a name="ev3-series"></a>Ev3 系列

| 大小 | 比率|
|---|---|
| Standard_E2_v3|1|
|Standard_E4_v3|2|
|Standard_E8_v3|4|
|Standard_E16_v3|8|
|Standard_E32_v3|16|
|Standard_E64_v3|32|

有关详细信息，请参阅[内存优化虚拟机大小](../articles/virtual-machines/windows/sizes-memory.md#ev3-series)。

## <a name="f-series"></a>F 系列

| 大小 | 比率|
|---|---|
| Standard_F1|1|
|Standard_F2|2|
|Standard_F4|4|
|Standard_F8|8|
Standard_F16|16|

有关详细信息，请参阅[计算优化虚拟机大小](../articles/virtual-machines/windows/sizes-compute.md#f-series)。

## <a name="fs-series"></a>FS 系列

| 大小 | 比率|
|---|---|
| Standard_F1s|1|
|Standard_F2s|2|
|Standard_F4s|4|
|Standard_F8s|8|
|Standard_F16s|16|

有关详细信息，请参阅[计算优化虚拟机大小](../articles/virtual-machines/windows/sizes-compute.md#fs-series-sup1sup)。

## <a name="fsv2-series"></a>Fsv2 系列

| 大小 | 比率|
|---|---|
|Standard_F2s_v2|1|
|Standard_F4s_v2|2|
|Standard_F8s_v2|4|
|Standard_F16s_v2|8|
|Standard_F32s_v2|16|
|Standard_F64s_v2|32|
|Standard_F72s_v2|36|

有关详细信息，请参阅[计算优化虚拟机大小](../articles/virtual-machines/windows/sizes-compute.md#fsv2-series-sup1sup)。

## <a name="h-series"></a>H 系列

| 大小 | 比率|
|---|---|
| Standard_H8|1|
|Standard_H16|2|

有关详细信息，请参阅[高性能计算 VM 大小](../articles/virtual-machines/windows/sizes-hpc.md)。

## <a name="h-series-high-memory"></a>高内存 H 系列

| 大小 | 比率|
|---|---|
| Standard_H8m|1|
|Standard_H16m|2|

有关详细信息，请参阅[高性能计算 VM 大小](../articles/virtual-machines/windows/sizes-hpc.md)。

## <a name="ls-series"></a>Ls 系列

| 大小 | 比率|
|---|---|
| Standard_L4s|1|
|Standard_L8s|2|
|Standard_L16s|4|
|Standard_L32s|8|

有关详细信息，请参阅[存储优化虚拟机大小](../articles/virtual-machines/windows/sizes-storage.md)。

## <a name="m-series"></a>M 系列

| 大小 | 比率|
|---|---|
| Standard_M64s|1|
|Standard_M128s|2|

有关详细信息，请参阅[内存优化虚拟机大小](../articles/virtual-machines/windows/sizes-memory.md#m-series)。

## <a name="m-series-fractional"></a>分布式 M 系列

| 大小 | 比率|
|---|---|
| Standard_M16s|1|
|Standard_M32s|2|

有关详细信息，请参阅[内存优化虚拟机大小](../articles/virtual-machines/windows/sizes-memory.md#m-series)。

## <a name="m-series-fractional-high-memory"></a>分布式高内存 M 系列

| 大小 | 比率|
|---|---|
| Standard_M8ms|1|
|Standard_M16ms|2|
|Standard_M32ms|4|

有关详细信息，请参阅[内存优化虚拟机大小](../articles/virtual-machines/windows/sizes-memory.md#m-series)。

## <a name="m-series-fractional-large"></a>分布式大型 M 系列

| 大小 | 比率|
|---|---|
| Standard_M32ls|1|
|Standard_M64ls|2|

有关详细信息，请参阅[内存优化虚拟机大小](../articles/virtual-machines/windows/sizes-memory.md#m-series)。

## <a name="m-series-high-memory"></a>高内存 M 系列

| 大小 | 比率|
|---|---|
| Standard_M64ms|1|
|Standard_M128ms|2|

有关详细信息，请参阅[内存优化虚拟机大小](../articles/virtual-machines/windows/sizes-memory.md#m-series)。

## <a name="nc-series"></a>NC 系列

| 大小 | 比率|
|---|---|
| Standard_NC6|1|
|Standard_NC12|2|
|Standard_NC24|4|

有关详细信息，请参阅 [GPU 优化虚拟机大小](../articles/virtual-machines/windows/sizes-gpu.md)。

## <a name="ncv2-series"></a>NCv2 系列

| 大小 | 比率|
|---|---|
| Standard_NC6s_v2|1|
|Standard_NC12s_v2|2|
|Standard_NC24s_v2|4|

有关详细信息，请参阅 [GPU 优化虚拟机大小](../articles/virtual-machines/windows//sizes-gpu.md#ncv2-series)。

## <a name="ncv3-series"></a>NCv3 系列

| 大小 | 比率|
|---|---|
| Standard_NC6s_v3|1|
|Standard_NC12s_v3|2|
|Standard_NC24s_v3|4|

有关详细信息，请参阅 [GPU 优化虚拟机大小](../articles/virtual-machines/windows//sizes-gpu.md#ncv3-series)。

## <a name="nd-series"></a>ND 系列

| 大小 | 比率|
|---|---|
| Standard_ND6s|1|
|Standard_ND12s|2|
|Standard_ND24s|4|

有关详细信息，请参阅 [GPU 优化虚拟机大小](../articles/virtual-machines/windows//sizes-gpu.md#nd-series)。

## <a name="nv-series"></a>NV 系列

| 大小 | 比率|
|---|---|
| Standard_NV6|1|
|Standard_NV12|2|
|Standard_NV24|4|

有关详细信息，请参阅 [GPU 优化虚拟机大小](../articles/virtual-machines/windows//sizes-gpu.md#nv-series)。


