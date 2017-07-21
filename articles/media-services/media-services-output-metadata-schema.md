---
title: "Azure 媒体服务输出元数据架构 | Microsoft Docs"
description: "本主题概述了 Azure 媒体服务输出元数据架构。"
author: Juliako
manager: erikre
editor: 
services: media-services
documentationcenter: 
ms.assetid: 1ed84c88-eea5-4a24-9c4f-f2428157d08a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: juliako
ms.translationtype: Human Translation
ms.sourcegitcommit: c3c5a3ee123fe021444370583c7f37737a03edce
ms.openlocfilehash: 2e21c8df29a78da77505b6f67434d97698f7a212
ms.contentlocale: zh-cn
ms.lasthandoff: 11/17/2016

---
# <a name="output-metadata"></a>输出元数据
## <a name="overview"></a>概述
编码作业与要在其上执行某些编码任务的输入资产（或资产）相关联。 例如，将 MP4 文件编码为 H.264 MP4 自适应比特率集；创建缩略图；创建叠加。 完成任务后，会生成一个输出资产。  该输出资产包含视频、音频、缩略图等。输出资产还包含提供输出资产相关元数据的文件。 元数据 XML 文件的名称采用下列格式：&lt;source_file_name&gt;_manifest.xml（例如，BigBuckBunny_manifest.xml）。  

如果想要检查元数据文件，可以创建 **SAS** 定位器并将文件下载到本地计算机。  

本主题讨论作为输出元数据 (&lt; source_file_name &gt; _manifest.xml) 的基础的 XML架构的元素和类型。 若要了解包含有关输入资产元数据的文件信息，请参阅 [输入元数据](media-services-input-metadata-schema.md)。  

> [!NOTE]
> 可以在本主题末尾找到完整的架构代码和 XML 示例。  
>
>

## <a name="AssetFiles "></a>AssetFiles 根元素
编码作业的 AssetFile 条目集合。  

### <a name="child-elements"></a>子元素
| Name | 说明 |
| --- | --- |
| **AssetFile**<br/><br/> minOccurs="0" maxOccurs="1" |[AssetFile 元素](media-services-output-metadata-schema.md)是 AssetFiles 集合的一部分。 |

## <a name="AssetFile "></a>AssetFile 元素
可以找到 XML 示例 [XML 示例](media-services-output-metadata-schema.md#xml)。  

### <a name="attributes"></a>属性
| Name | 类型 | 说明 |
| --- | --- | --- |
| **Name**<br/><br/> 必选 |**xs:string** |媒体资产文件名。 |
| **大小**<br/><br/> minInclusive ="0"<br/><br/> 必选 |**xs:long** |以字节为单位的资产文件大小。 |
| **持续时间**<br/><br/> 必选 |**xs:duration** |内容播放持续时间。 |

### <a name="child-elements"></a>子元素
| Name | 说明 |
| --- | --- |
| **源** |为生成此 AssetFile 而处理的输入/源媒体文件集合。 有关详细信息，请参阅[源元素](media-services-output-metadata-schema.md)。 |
| **VideoTracks**<br/><br/> minOccurs="0" maxOccurs="1" |每个物理 AssetFile 都可包含交错成适当容器格式的零个或多个视频轨道。 这是所有这些视频轨道的集合。 有关详细信息，请参阅 [VideoTracks 元素](media-services-output-metadata-schema.md)。 |
| **AudioTracks**<br/><br/> minOccurs="0" maxOccurs="1" |每个物理 AssetFile 都可包含交错成适当容器格式的零个或多个音频轨道。 这是所有这些音频轨道的集合。 有关详细信息，请参阅 [AudioTracks 元素](media-services-output-metadata-schema.md)。 |

## <a name="Sources "></a> 源元素
为生成此 AssetFile 而处理的输入/源媒体文件集合。  

可以找到 XML 示例 [XML 示例](media-services-output-metadata-schema.md#xml)。  

### <a name="child-elements"></a>子元素
| Name | 说明 |
| --- | --- |
| **源**<br/><br/> minOccurs="1" maxOccurs="unbounded" |生成此资产时所使用的输入/源文件。 有关详细信息，请参阅[源元素](media-services-output-metadata-schema.md)。 |

## <a name="Source "></a> 源元素
生成此资产时所使用的输入/源文件。  

可以找到 XML 示例 [XML 示例](media-services-output-metadata-schema.md#xml)。  

### <a name="attributes"></a>属性
| Name | 类型 | 说明 |
| --- | --- | --- |
| **Name**<br/><br/> 必选 |**xs:string** |输入源文件名称。 |

## <a name="VideoTracks "></a>VideoTracks 元素
每个物理 AssetFile 都可包含交错成适当容器格式的零个或多个视频轨道。 这是所有这些视频轨道的集合。  

可以找到 XML 示例 [XML 示例](media-services-output-metadata-schema.md#xml)。  

### <a name="child-elements"></a>子元素
| Name | 说明 |
| --- | --- |
| **VideoTrack**<br/><br/> minOccurs="1" maxOccurs="unbounded" |父级 AssetFile 中某个特定视频轨道。 有关详细信息，请参阅 [VideoTrack 元素](media-services-output-metadata-schema.md#VideoTrack)。 |

## <a name="VideoTrack"></a>VideoTrack 元素
父级 AssetFile 中某个特定视频轨道。  

可以找到 XML 示例 [XML 示例](media-services-output-metadata-schema.md#xml)。  

### <a name="attributes"></a>属性
| Name | 类型 | 说明 |
| --- | --- | --- |
| **Id**<br/><br/> minInclusive ="0"<br/><br/> 必选 |**xs:int** |此视频轨道的从零开始的索引。 **注意：**这不一定是 MP4 文件中使用的 TrackID。 |
| **FourCC**<br/><br/> 必选 |**xs:string** |视频编解码器 FourCC 代码。 |
| **配置文件** |**xs:string** |H264 配置文件（仅适用于 H264 编解码器）。 |
| **级别** |**xs:string** |H264 级别（仅适用于 H264 编解码器）。 |
| **宽度**<br/><br/> minInclusive ="0"<br/><br/> 必选 |**xs:int** |以像素为单位的编码视频宽度。 |
| **高度**<br/><br/> minInclusive ="0"<br/><br/> 必选 |**xs:int** |以像素为单位的编码视频高度。 |
| **DisplayAspectRatioNumerator**<br/><br/> minInclusive ="0"<br/><br/> 必选 |**xs:double** |视频显示纵横比分子。 |
| **DisplayAspectRatioDenominator**<br/><br/> minInclusive ="0"<br/><br/> 必选 |**xs:double** |视频显示纵横比分母。 |
| **Framerate**<br/><br/> minInclusive ="0"<br/><br/> 必选 |**xs:decimal** |采用 .3f 格式测量的视频帧速率。 |
| **TargetFramerate**<br/><br/> minInclusive ="0"<br/><br/> 必选 |**xs:decimal** |采用 .3f 格式预设的目标视频帧速率。 |
| **Bitrate**<br/><br/> minInclusive ="0"<br/><br/> 必选 |**xs:int** |由 AssetFile 计算的平均视频比特率，以千比特/秒为单位。 只计算基本流有效负载，并且不包括打包开销。 |
| **TargetBitrate**<br/><br/> minInclusive ="0"<br/><br/> 必选 |**xs:int** |通过编码预设请求的此视频轨道的目标平均比特率，以千比特/秒为单位。 |
| **MaxGOPBitrate**<br/><br/> minInclusive ="0" |**xs:int** |此视频轨道的最大 GOP 平均比特率，以千比特/秒为单位。 |

## <a name="AudioTracks "></a>AudioTracks 元素
每个物理 AssetFile 都可包含交错成适当容器格式的零个或多个音频轨道。 这是所有这些音频轨道的集合。  

可以找到 XML 示例 [XML 示例](media-services-output-metadata-schema.md#xml)。  

### <a name="child-elements"></a>子元素
| Name | 说明 |
| --- | --- |
| **AudioTrack**<br/><br/> minOccurs="1" maxOccurs="unbounded" |父级 AssetFile 中某个特定音频轨道。 有关详细信息，请参阅 [AudioTrack 元素](media-services-output-metadata-schema.md)。 |

## <a name="AudioTrack "></a>AudioTrack 元素
父级 AssetFile 中某个特定音频轨道。  

可以找到 XML 示例 [XML 示例](media-services-output-metadata-schema.md#xml)。  

### <a name="attributes"></a>属性
| Name | 类型 | 说明 |
| --- | --- | --- |
| **Id**<br/><br/> minInclusive ="0"<br/><br/> 必选 |**xs:int** |此音频轨道从零开始的索引。 **注意：**这不一定是 MP4 文件中使用的 TrackID。 |
| **编解码器** |**xs:string** |音频轨道编解码器字符串。 |
| **EncoderVersion** |**xs:string** |可选的编码器版本字符串，对于 EAC3 是必需的。 |
| **通道**<br/><br/> minInclusive ="0"<br/><br/> 必选 |**xs:int** |音频通道数。 |
| **SamplingRate**<br/><br/> minInclusive ="0"<br/><br/> 必选 |**xs:int** |音频采样速率，以采样数/秒或 Hz 为单位。 |
| **Bitrate**<br/><br/> minInclusive ="0"<br/><br/> 必选 |**xs:int** |由 AssetFile 计算的平均音频比特率（以比特/秒为单位）。 只计算基本流有效负载，并且不包括打包开销。 |
| **BitsPerSample**<br/><br/> minInclusive ="0"<br/><br/> 必选 |**xs:int** |wFormatTag 格式类型的每个样本的位数。 |

### <a name="child-elements"></a>子元素
| Name | 说明 |
| --- | --- |
| **LoudnessMeteringResultParameters**<br/><br/> minOccurs="0" maxOccurs="1" |Loudness metering result parameters. 有关详细信息，请参阅 [LoudnessMeteringResultParameters 元素](media-services-output-metadata-schema.md)。 |

## <a name="LoudnessMeteringResultParameters "></a>LoudnessMeteringResultParameters 元素
Loudness metering result parameters.  

可以找到 XML 示例 [XML 示例](media-services-output-metadata-schema.md#xml)。  

### <a name="attributes"></a>属性
| Name | 类型 | 说明 |
| --- | --- | --- |
| **DPLMVersionInformation** |**xs:string** |**Dolby** 专业响度测量开发工具包版本。 |
| **DialogNormalization**<br/><br/> minInclusive="-31" maxInclusive="-1"<br/><br/> 必选 |**xs:int** |通过 DPLM 生成的 DialogNormalization，设置 LoudnessMetering 时需要使用 |
| **IntegratedLoudness**<br/><br/> minInclusive="-70" maxInclusive="10"<br/><br/> 必选 |**xs:float** |集成响度 |
| **IntegratedLoudnessUnit**<br/><br/> 必选 |**xs:string** |集成响度单位。 |
| **IntegratedLoudnessGatingMethod**<br/><br/> 必选 |**xs:string** |选通标识符 |
| **IntegratedLoudnessSpeechPercentage**<br/><br/> minInclusive ="0" maxInclusive="100" |**xs:float** |程序中的语音内容，百分比形式。 |
| **SamplePeak**<br/><br/> 必选 |**xs:float** |自重置或自上次清零以来的峰值绝对采样值，按通道计算。  单位为 dBFS。 |
| **SamplePeakUnit**<br/><br/> fixed="dBFS"<br/><br/> 必选 |**xs:anySimpleType** |采样峰值单位。 |
| **TruePeak**<br/><br/> 必选 |**xs:float** |自重置或自上次清零以来按照 ITU-R BS.1770-2 得到的最大真实峰值，按通道计算。 单位为 dBTP。 |
| **TruePeakUnit**<br/><br/> fixed="dBTP"<br/><br/> 必选 |**xs:anySimpleType** |真实峰值单位。 |

## <a name="schema-code"></a>架构代码
    <?xml version="1.0" encoding="utf-8"?>  
    <xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns:msdata="urn:schemas-microsoft-com:xml-msdata" version="1.2"  
               xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2013/05/mediaencoder/metadata"  
               targetNamespace="http://schemas.microsoft.com/windowsazure/mediaservices/2013/05/mediaencoder/metadata"  
               elementFormDefault="qualified">  
      <xs:element name="AssetFiles">  
        <xs:annotation>  
          <xs:documentation>Collection of AssetFile entries for the encoding job</xs:documentation>  
        </xs:annotation>  
        <xs:complexType>  
          <xs:sequence>  
            <xs:element name="AssetFile" minOccurs="1" maxOccurs="unbounded">  
              <xs:annotation>  
                <xs:documentation>asset file</xs:documentation>  
              </xs:annotation>  
              <xs:complexType>  
                <xs:sequence>  
                  <xs:element name="Sources">  
                    <xs:annotation>  
                      <xs:documentation>Collection of input/source media files, that was processed in order to produce this AssetFile</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="Source" minOccurs="1" maxOccurs="unbounded">  
                          <xs:annotation>  
                            <xs:documentation>An input/source file used when generating this asset</xs:documentation>  
                          </xs:annotation>  
                          <xs:complexType>  
                            <xs:attribute name="Name" type="xs:string" use="required">  
                              <xs:annotation>  
                                <xs:documentation>input source file name</xs:documentation>  
                              </xs:annotation>  
                            </xs:attribute>  
                          </xs:complexType>  
                        </xs:element>  
                      </xs:sequence>  
                    </xs:complexType>  
                  </xs:element>  
                  <xs:element name="VideoTracks" minOccurs="0">  
                    <xs:annotation>  
                      <xs:documentation>Each physical AssetFile can contain in it zero or more video tracks interleaved into an appropriate container format. This is the collection of all those video tracks</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="VideoTrack" maxOccurs="unbounded">  
                          <xs:annotation>  
                            <xs:documentation>A specific video track in the parent AssetFile</xs:documentation>  
                          </xs:annotation>  
                          <xs:complexType>  
                            <xs:attribute name="Id" use="required">  
                              <xs:annotation>  
                                <xs:documentation>zero-based index of this video track. Note: this is not necessarily the TrackID as used in an MP4 file</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:int">  
                                  <xs:minInclusive value="0"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                            <xs:attribute name="FourCC" type="xs:string" use="required">  
                              <xs:annotation>  
                                <xs:documentation>video codec FourCC code</xs:documentation>  
                              </xs:annotation>  
                            </xs:attribute>  
                            <xs:attribute name="Profile" type="xs:string">  
                              <xs:annotation>  
                                <xs:documentation>H264 profile (only appliable for H264 codec)</xs:documentation>  
                              </xs:annotation>  
                            </xs:attribute>  
                            <xs:attribute name="Level" type="xs:string">  
                              <xs:annotation>  
                                <xs:documentation>H264 level (only appliable for H264 codec)</xs:documentation>  
                              </xs:annotation>  
                            </xs:attribute>  
                            <xs:attribute name="Width" use="required">  
                              <xs:annotation>  
                                <xs:documentation>encoded video width in pixels</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:int">  
                                  <xs:minInclusive value="0"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                            <xs:attribute name="Height" use="required">  
                              <xs:annotation>  
                                <xs:documentation>encoded video height in pixels</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:int">  
                                  <xs:minInclusive value="0"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                            <xs:attribute name="DisplayAspectRatioNumerator" use="required">  
                              <xs:annotation>  
                                <xs:documentation>video display aspect ratio numerator</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:double">  
                                  <xs:minInclusive value="0"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                            <xs:attribute name="DisplayAspectRatioDenominator" use="required">  
                              <xs:annotation>  
                                <xs:documentation>video display aspect ratio denominator</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:double">  
                                  <xs:minInclusive value="0"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                            <xs:attribute name="Framerate" use="required">  
                              <xs:annotation>  
                                <xs:documentation>measured video frame rate in .3f format</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:decimal">  
                                  <xs:minInclusive value="0"/>  
                                  <xs:fractionDigits value="3"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                            <xs:attribute name="TargetFramerate" use="required">  
                              <xs:annotation>  
                                <xs:documentation>preset target video frame rate in .3f format</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:decimal">  
                                  <xs:minInclusive value="0"/>  
                                  <xs:fractionDigits value="3"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                            <xs:attribute name="Bitrate" use="required">  
                              <xs:annotation>  
                                <xs:documentation>average video bit rate in kilobits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:int">  
                                  <xs:minInclusive value="0"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                            <xs:attribute name="TargetBitrate" use="required">  
                              <xs:annotation>  
                                <xs:documentation>target average bitrate for this video track, as requested via the encoding preset, in kilobits per second</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:int">  
                                  <xs:minInclusive value="0"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                            <xs:attribute name="MaxGOPBitrate">  
                              <xs:annotation>  
                                <xs:documentation>Max GOP average bitrate for this video track, in kilobits per second</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:int">  
                                  <xs:minInclusive value="0"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                          </xs:complexType>  
                        </xs:element>  
                      </xs:sequence>  
                    </xs:complexType>  
                  </xs:element>  
                  <xs:element name="AudioTracks" minOccurs="0">  
                    <xs:annotation>  
                      <xs:documentation>each physical AssetFile can contain in it zero or more audio tracks interleaved into an appropriate container format. This is the collection of all those audio tracks</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="AudioTrack" maxOccurs="unbounded">  
                          <xs:annotation>  
                            <xs:documentation>a specific audio track in the parent AssetFile</xs:documentation>  
                          </xs:annotation>  
                          <xs:complexType>  
                            <xs:sequence>  
                              <xs:element name="LoudnessMeteringResultParameters" minOccurs="0" maxOccurs="1">  
                                <xs:annotation>  
                                  <xs:documentation>Loudness Metering Result Parameters</xs:documentation>  
                                </xs:annotation>  
                                <xs:complexType>  
                                  <xs:attribute name="DPLMVersionInformation" type="xs:string">  
                                    <xs:annotation>  
                                      <xs:documentation>Dolby Professional Loudness Metering Development Kit Version</xs:documentation>  
                                    </xs:annotation>  
                                  </xs:attribute>  
                                  <xs:attribute name="DialogNormalization" use="required">  
                                    <xs:annotation>  
                                      <xs:documentation> DialogNormalization generated through DPLM, required when LoudnessMetering is set</xs:documentation>  
                                    </xs:annotation>  
                                    <xs:simpleType>  
                                      <xs:restriction base="xs:int">  
                                        <xs:minInclusive value="-31"/>  
                                        <xs:maxInclusive value="-1"/>  
                                      </xs:restriction>  
                                    </xs:simpleType>  
                                  </xs:attribute>  
                                  <xs:attribute name="IntegratedLoudness" use="required">  
                                    <xs:annotation>  
                                      <xs:documentation>Integrated loudness</xs:documentation>  
                                    </xs:annotation>  
                                    <xs:simpleType>  
                                      <xs:restriction base="xs:float">  
                                        <xs:minInclusive value="-70"/>  
                                        <xs:maxInclusive value="10"/>  
                                      </xs:restriction>  
                                    </xs:simpleType>  
                                  </xs:attribute>  
                                  <xs:attribute name="IntegratedLoudnessUnit" use="required" type="xs:string">  
                                  </xs:attribute>  
                                  <xs:attribute name="IntegratedLoudnessGatingMethod" use="required" type="xs:string">  
                                    <xs:annotation>  
                                      <xs:documentation>Gating identifier</xs:documentation>  
                                    </xs:annotation>  
                                  </xs:attribute>  
                                  <xs:attribute name="IntegratedLoudnessSpeechPercentage">  
                                    <xs:annotation>  
                                      <xs:documentation>Speech content over the program, as a percentage.</xs:documentation>  
                                    </xs:annotation>  
                                    <xs:simpleType>  
                                      <xs:restriction base="xs:float">  
                                        <xs:minInclusive value="0"/>  
                                        <xs:maxInclusive value="100"/>  
                                      </xs:restriction>  
                                    </xs:simpleType>  
                                  </xs:attribute>  
                                  <xs:attribute name="SamplePeak" use="required" type="xs:float">  
                                    <xs:annotation>  
                                      <xs:documentation>Peak absolute sample value, since reset or since it was last cleared, per channel.  Units are dBFS.</xs:documentation>  
                                    </xs:annotation>  
                                  </xs:attribute>  
                                  <xs:attribute name="SamplePeakUnit" use="required" fixed="dBFS">  
                                  </xs:attribute>  
                                  <xs:attribute name="TruePeak" use="required" type="xs:float">  
                                    <xs:annotation>  
                                      <xs:documentation>Maximum True Peak value, as per ITU-R BS.1770-2, since reset or since it was last cleared, per channel.  Units are dBTP.</xs:documentation>  
                                    </xs:annotation>  
                                  </xs:attribute>  
                                  <xs:attribute name="TruePeakUnit" use="required" fixed="dBTP">  
                                  </xs:attribute>  
                                </xs:complexType>  
                              </xs:element>  
                            </xs:sequence>  
                            <xs:attribute name="Id" use="required">  
                              <xs:annotation>  
                                <xs:documentation>zero-based index of this audio track. Note: this is not necessarily the TrackID as used in an MP4 file</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:int">  
                                  <xs:minInclusive value="0"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                            <xs:attribute name="Codec" type="xs:string">  
                              <xs:annotation>  
                                <xs:documentation>audio track codec string</xs:documentation>  
                              </xs:annotation>  
                            </xs:attribute>  
                            <xs:attribute name="EncoderVersion" type="xs:string">  
                              <xs:annotation>  
                                <xs:documentation>optional encoder version string, required for EAC3</xs:documentation>  
                              </xs:annotation>  
                            </xs:attribute>  
                            <xs:attribute name="Channels" use="required">  
                              <xs:annotation>  
                                <xs:documentation>number of audio channels</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:int">  
                                  <xs:minInclusive value="0"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                            <xs:attribute name="SamplingRate" use="required">  
                              <xs:annotation>  
                                <xs:documentation>audio sampling rate in samples/sec or Hz</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:int">  
                                  <xs:minInclusive value="0"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                            <xs:attribute name="Bitrate" use="required">  
                              <xs:annotation>  
                                <xs:documentation>average audio bit rate in bits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:int">  
                                  <xs:minInclusive value="0"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                            <xs:attribute name="BitsPerSample" use="required">  
                              <xs:annotation>  
                                <xs:documentation>Bits per sample for the wFormatTag format type</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:int">  
                                  <xs:minInclusive value="0"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                          </xs:complexType>  
                        </xs:element>  
                      </xs:sequence>  
                    </xs:complexType>  
                  </xs:element>  
                </xs:sequence>  
                <xs:attribute name="Name" type="xs:string" use="required">  
                  <xs:annotation>  
                    <xs:documentation>the media asset file name</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="Size" use="required">  
                  <xs:annotation>  
                    <xs:documentation>size of file in bytes</xs:documentation>  
                  </xs:annotation>  
                  <xs:simpleType>  
                    <xs:restriction base="xs:long">  
                      <xs:minInclusive value="0"/>  
                    </xs:restriction>  
                  </xs:simpleType>  
                </xs:attribute>  
                <xs:attribute name="Duration" use="required">  
                  <xs:annotation>  
                    <xs:documentation>content play back duration</xs:documentation>  
                  </xs:annotation>  
                  <xs:simpleType>  
                    <xs:restriction base="xs:duration"/>  
                  </xs:simpleType>  
                </xs:attribute>  
              </xs:complexType>  
            </xs:element>  
          </xs:sequence>  
        </xs:complexType>  
      </xs:element>  
    </xs:schema>  



## <a name="xml"></a>XML 示例
 下面是输出元数据文件的示例。  

    <AssetFiles xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema"   
                xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2013/05/mediaencoder/metadata">  
      <AssetFile Name="BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4" Size="4646283" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <VideoTracks>  
          <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.2" Width="1280" Height="720" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="4250" TargetBitrate="3400" MaxGOPBitrate="5514"/>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4" Size="3166728" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <VideoTracks>  
          <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.1" Width="960" Height="540" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="2846" TargetBitrate="2250" MaxGOPBitrate="3630"/>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4" Size="2205095" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <VideoTracks>  
          <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.1" Width="960" Height="540" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="1932" TargetBitrate="1500" MaxGOPBitrate="2513"/>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4" Size="1508567" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <VideoTracks>  
          <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.0" Width="640" Height="360" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="1271" TargetBitrate="1000" MaxGOPBitrate="1527"/>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4" Size="1057155" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <VideoTracks>  
          <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.0" Width="640" Height="360" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="843" TargetBitrate="650" MaxGOPBitrate="1086"/>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4" Size="699262" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <VideoTracks>  
          <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="1.3" Width="320" Height="180" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="503" TargetBitrate="400" MaxGOPBitrate="661"/>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_AAC_und_ch2_96kbps.mp4" Size="166780" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_AAC_und_ch2_56kbps.mp4" Size="124576" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="53" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
    </AssetFiles>  

## <a name="next-steps"></a>后续步骤
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

