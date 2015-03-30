<properties 
	pageTitle="Media Encoder Premium Workflow 格式和编解码器" 
	description="本主题概述 Media Encoder Premium Workflow 的格式和编解码器" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>










# Media Encoder Premium Workflow 格式和编解码器


**请注意**，本主题中所述的 Media Encoder Premium Workflow 媒体处理器在中国不可用。

本文档包含 **Media Encoder Premium Workflow** 公共预览版编码器支持的输入和输出文件格式及编解码器列表。

[Media Encoder Premium Workflow 输入格式和编解码器](#input_formats)

[Media Encoder Premium Workflow 输出格式和编解码器](#output_formats)

**Media Encoder Premium Workflow** 支持[此部分](#closed_captioning) 中所述的隐藏字幕。 


## <a id="input_formats"></a>Media Encoder Premium Workflow 输入格式和编解码器

以下部分列出了导入操作支持的编解码器和文件格式。

### 输入容器/文件格式

- Adobe(r) Flash(r) F4V
- MXF/SMPTE 377M
- GXF
- Mpeg-2 传输流
- MPEG-2 节目流
- MPEG-4/MP4
- Windows Media/ASF
- AVI（8 位/10 位未压缩）

### 输入视频编解码器

- AVC 8 位/10 位，最高支持 4:2:2，包括 AVCIntra
- Avid DNxHD（MXF 格式）
- DVCPro/DVCProHD（MXF 格式）
- JPEG2000
- Mpeg-2（最高支持 422 Profile 和 High Level；包括 XDCAM、XDCAM HD、XDCAM IMX、CableLabs(r) 和 D10 等变体）
- MPEG-1
- Windows Media 视频/VC-1
- Canopus HQ/HQX

### 输入音频编解码器

- AES（SMPTE 331M 和 302M、AES3-2003）
- Dolby(r) E
- Dolby(r) Digital (AC3)
- AAC(AAC-LC、AAC-HE 和 AAC-HEv2；最高支持 5.1）
- MPEG Layer 2
- MP3 (MPEG-1 Audio Layer 3)
- Windows Media 音频
- WAV/PCM
 
## <a id="output_format"></a>Media Encoder Premium Workflow 输出格式和编解码器

### 输出容器/文件格式

- Adobe(r) Flash(r) F4V
- MXF（OP1a、XDCAM 和 AS02）
- DPP（包括 AS11）
- GXF
- MPEG-4/MP4
- Windows Media/ASF
- AVI（8 位/10 位未压缩）
- 平滑流文件格式 (PIFF 1.3)


### 输出视频编解码器

- AVC（H.264；8 位；最高支持 High Profile、Level 5.2；4K Ultra HD；AVC Intra）
- Avid DNxHD（MXF 格式）
- DVCPro/DVCProHD（MXF 格式）
- Mpeg-2（最高支持 422 Profile 和 High Level；包括 XDCAM、XDCAM HD、XDCAM IMX、CableLabs(r) 和 D10 等变体）
- MPEG-1
- Windows Media 视频/VC-1
- JPEG 缩图创建

### 输出音频编解码器

- AES（SMPTE 331M 和 302M、AES3-2003）
- Dolby(r) Digital (AC3)
- Dolby(r) Digital Plus (E-AC3)，最高支持 7.1
- AAC(AAC-LC、AAC-HE 和 AAC-HEv2；最高支持 5.1）
- MPEG Layer 2
- MP3 (MPEG-1 Audio Layer 3)
- Windows Media 音频

## <a id="closed_captioning"></a>隐藏字幕支持

当前，**Media Encoder Premium Workkflow** 媒体处理器支持内插于源文件或输入媒体文件中的隐藏字幕：本质上，是作为用户数据携载的 CEA-608/CEA-708（H.264 基本流的 SEI 消息，ATSC/53，SCTE20），或作为 MXF/GXF 文件中的辅助数据携载的隐藏字幕。

输出后，可以使用以下选项来重新打包输入中的 CEA 608/708：

- CEA-608 到 CEA-708 的转换
- CEA-608/CEA-708 传递（内嵌在 H.264 基本流的 SEI 消息中，或作为 MXF 文件中的辅助数据携载）
- SCC
- SMPTE 时序文本（来自符合 SMPTE RP2052 的源 CEA-608；包括 DFXP 文件创建）
- SRT 字幕文件
- DVB 字幕流

注意：不一定支持通过 Azure Media Services 中的流式传输来传送上述所有输出格式。
