---
title: "如何使 Media Encoder Standard 通过 .NET 来生成缩略图"
description: "本主题介绍如何使用 .NET 通过 Media Encoder Standard 同时对资产进行编码和生成缩略图。"
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: b8dab73a-1d91-4b6d-9741-a92ad39fc3f7
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: juliako
ms.openlocfilehash: 7b8732a06e54f7828418cba0c0d172e34f1f4ef7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-generate-thumbnails-using-media-encoder-standard-with-net"></a>如何使 Media Encoder Standard 通过 .NET 来生成缩略图

可以使用 Media Encoder Standard 以 [JPEG](https://en.wikipedia.org/wiki/JPEG)、[PNG](https://en.wikipedia.org/wiki/Portable_Network_Graphics) 或 [BMP](https://en.wikipedia.org/wiki/BMP_file_format) 图像文件格式从输入视频生成一个或多个缩略图。 可以提交仅生成图像的任务，或者合并缩略图的生成与编码功能。 本主题针对此类方案提供一些示例 XML 和 JSON 缩略图预设。 本主题的末尾提供了一段[示例代码](#code_sample)，演示如何使用媒体服务 .NET SDK 来完成编码任务。

有关示例预设中使用的元素的更多详细信息，请查看 [Media Encoder Standard 架构](media-services-mes-schema.md)。

请务必仔细阅读[注意事项](media-services-dotnet-generate-thumbnail-with-mes.md#considerations)部分。
    
## <a name="example-of-a-single-png-file-preset"></a>“单个 PNG 文件”预设示例

使用以下 JSON 和 XML 预设可以基于输入视频的前几秒内容生成单个输出 PNG 文件，其中的编码器会尽力尝试查找“相关的”帧。 请注意，输出图像尺寸已设置为 100%，这意味着，这些尺寸将与输入视频的尺寸匹配。 另请注意需要如何指定“Outputs”中“Format”设置，以匹配“Codecs”节中“PngLayers”的用法。 

### <a name="json-preset"></a>JSON 预设

    {
      "Version": 1.0,
      "Codecs": [
        {
          "PngLayers": [
            {
              "Type": "PngLayer",
              "Width": "100%",
              "Height": "100%"
            }
          ],
          "Start": "{Best}",
          "Type": "PngImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "PngFormat"
          }
        }
      ]
    }
    
### <a name="xml-preset"></a>XML 预设

    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <PngImage Start="{Best}">
          <PngLayers>
            <PngLayer>
              <Width>100%</Width>
              <Height>100%</Height>
            </PngLayer>
          </PngLayers>
        </PngImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Index}{Extension}">
          <PngFormat />
        </Output>
      </Outputs>
    </Preset>

## <a name="example-of-a-series-of-jpeg-images-preset"></a>“一系列 JPEG 图像”预设的示例

使用以下 JSON 和 XML 预设可以在输入时间线的时间戳 5%、15% ... 95% 处生成 10 幅图像，其中的图像大小指定为输入视频的四分之一。

### <a name="json-preset"></a>JSON 预设

    {
      "Version": 1.0,
      "Codecs": [
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": "25%",
              "Height": "25%"
            }
          ],
          "Start": "5%",
          "Step": "10%",
          "Range": "96%",
          "Type": "JpgImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        }
      ]
    }

### <a name="xml-preset"></a>XML 预设
    
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <JpgImage Start="5%" Step="10%" Range="96%">
          <JpgLayers>
            <JpgLayer>
              <Width>25%</Width>
              <Height>25%</Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
      </Outputs>
    </Preset>

## <a name="example-of-a-one-image-at-a-specific-timestamp-preset"></a>“在特定时间戳处生成一个图像”预设示例

使用以下 JSON 和 XML 预设可以在输入视频的 30 秒标记处生成单个 JPEG 图像。 此预设预期输入视频的持续时间超过 30 秒（否则作业会失败）。

### <a name="json-preset"></a>JSON 预设

    {
      "Version": 1.0,
      "Codecs": [
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": "25%",
              "Height": "25%"
            }
          ],
          "Start": "00:00:30",
          "Step": "1",
          "Range": "1",
          "Type": "JpgImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        }
      ]
    }
    
### <a name="xml-preset"></a>XML 预设
    
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <JpgImage Start="00:00:30" Step="00:00:01" Range="00:00:01">
          <JpgLayers>
            <JpgLayer>
              <Width>25%</Width>
              <Height>25%</Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
      </Outputs>
    </Preset>
    
## <a name="example-of-a-thumbnails-at-different-resolutions-preset"></a>“不同分辨率的缩略图”预设示例

可使用以下预设在同一个任务中的不同解决方案下生成缩略图。 在该示例中，当输入时间线位于 5%、15%、…、95% 时，编码器将在两个时点各生成一个图像 - 一个是输入视频分辨率的 100% 处，一个是 50% 处。

请注意 FileName 中 {Resolution} 宏的使用；它指示在生成输出图像的文件名时，编码器要使用在预设的“编码”部分中所指定的宽度和高度。 这还能够帮助轻松区分不同图像

### <a name="json-preset"></a>JSON 预设

    {
      "Version": 1.0,
      "Codecs": [
        {
          "JpgLayers": [
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "100%",
          "Height": "100%"
        },
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "50%",
          "Height": "50%"
        }

          ],
          "Start": "5%",
          "Step": "10%",
          "Range": "96%",
          "Type": "JpgImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Resolution}_{Index}{Extension}",
          "Format": {
        "Type": "JpgFormat"
          }
        }
      ]
    }

### <a name="xml-preset"></a>XML 预设

    <?xml version="1.0" encoding="utf-8"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
    <Encoding>
    <JpgImage Start="5%" Step="10%" Range="96%"><JpgImage Start="00:00:01" Step="00:00:15">
      <JpgLayers>
       <JpgLayer>
        <Width>100%</Width>
        <Height>100%</Height>
        <Quality>90</Quality>
       </JpgLayer>
       <JpgLayer>
        <Width>50%</Width>
        <Height>50%</Height>
        <Quality>90</Quality>
       </JpgLayer>
      </JpgLayers>
    </JpgImage>
    </Encoding>
    <Outputs>
      <Output FileName="{Basename}_{Resolution}_{Index}{Extension}">
        <JpgFormat/>
      </Output>
    </Outputs>
    </Preset>
    
## <a name="example-of-generating-a-thumbnail-while-encoding"></a>在编码时生成缩略图的示例

上述所有示例都在讨论如何提交仅生成图像的编码任务，但还可以将视频/音频编码与缩略图生成结合起来。 以下的 JSON 和 XML 预设指示 Media Encoder Standard 在编码过程中生成一个缩略图。

### <a id="json"></a>JSON 预设
有关架构的信息，请参阅[此](https://msdn.microsoft.com/library/mt269962.aspx)主题。

    {
      "Version": 1.0,
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": "true",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 4500,
              "MaxBitrate": 4500,
              "BufferWindow": "00:00:05",
              "Width": 1280,
              "Height": 720,
              "ReferenceFrames": 3,
              "EntropyMode": "Cabac",
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
    
            }
          ],
          "Type": "H264Video"
        },
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": "100%",
              "Height": "100%"
            }
          ],
          "Start": "{Best}",
          "Type": "JpgImage"
        },
        {
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        },
        {
          "FileName": "{Basename}_{Resolution}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

### <a id="xml"></a>XML 预设
有关架构的信息，请参阅[此](https://msdn.microsoft.com/library/mt269962.aspx)主题。
    
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <SceneChangeDetection>true</SceneChangeDetection>
          <H264Layers>
            <H264Layer>
              <Bitrate>4500</Bitrate>
              <Width>1280</Width>
              <Height>720</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>4500</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <AACAudio>
          <Profile>AACLC</Profile>
          <Channels>2</Channels>
          <SamplingRate>48000</SamplingRate>
          <Bitrate>128</Bitrate>
        </AACAudio>
        <JpgImage Start="{Best}">
          <JpgLayers>
            <JpgLayer>
              <Width>100%</Width>
              <Height>100%/Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Resolution}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
      </Outputs>
    </Preset>   

## <a id="code_sample"></a>编码视频并使用 .NET 生成缩略图

以下代码示例使用媒体服务 .NET SDK 执行下列任务：

* 创建编码作业。
* 获取对 Media Encoder Standard 编码器的引用。
* 加载预设 [XML](media-services-dotnet-generate-thumbnail-with-mes.md#xml) 或 [JSON](media-services-dotnet-generate-thumbnail-with-mes.md#json)，其中包含生成缩略图所需的编码预设和信息。 可以在某一文件中保存此 [XML](media-services-dotnet-generate-thumbnail-with-mes.md#xml) 或 [JSON](media-services-dotnet-generate-thumbnail-with-mes.md#json)，并使用以下代码来加载该文件。
  
        // Load the XML (or JSON) from the local file.
        string configuration = File.ReadAllText(fileName);  
* 将一个编码任务添加到该作业。 
* 指定要编码的输入资产。
* 创建将包含所编码资产的输出资产。
* 添加事件处理程序以检查作业进度。
* 提交作业。

有关如何设置开发环境的指导，请参阅[使用 .NET 进行媒体服务开发](media-services-dotnet-how-to-use.md)主题。

        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;

        namespace EncodeAndGenerateThumbnails
        {
        class Program
        {
            // Read values from the App.config file.
            private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AADTenantDomain"];
            private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["MediaServiceRESTAPIEndpoint"];

            private static CloudMediaContext _context = null;

            private static readonly string _mediaFiles =
            Path.GetFullPath(@"../..\Media");

            private static readonly string _singleMP4File =
            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");

            static void Main(string[] args)
            {
            var tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain, AzureEnvironments.AzureCloudEnvironment);
            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Get an uploaded asset.
            var asset = _context.Assets.FirstOrDefault();

            // Encode and generate the thumbnails.
            EncodeToAdaptiveBitrateMP4Set(asset);

            Console.ReadLine();
            }

            static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
            {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Media Encoder Standard Thumbnail Job");
            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Load the XML (or JSON) from the local file.
            string configuration = File.ReadAllText("ThumbnailPreset_JSON.json");

            // Create a task
            ITask task = job.Tasks.AddNew("Media Encoder Standard Thumbnail task",
                processor,
                configuration,
                TaskOptions.None);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);
            // Add an output asset to contain the results of the job. 
            // This output is specified as AssetCreationOptions.None, which 
            // means the output asset is not encrypted. 
            task.OutputAssets.AddNew("Output asset",
                AssetCreationOptions.None);

            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();

            return job.OutputMediaAssets[0];
            }

            private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
            {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);
            switch (e.CurrentState)
            {
                case JobState.Finished:
                Console.WriteLine();
                Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
                break;
                case JobState.Canceling:
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                Console.WriteLine("Please wait...\n");
                break;
                case JobState.Canceled:
                case JobState.Error:

                // Cast sender as a job.
                IJob job = (IJob)sender;

                // Display or log error details as needed.
                break;
                default:
                break;
            }
            }

            private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
            {
            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
            ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

            return processor;
            }
        }


## <a name="considerations"></a>注意事项
请注意以下事项：

* 为 Start/Step/Range 使用的显式时间戳假设输入源的长度至少为 1 分钟。
* Jpg/Png/BmpImage 元素包含 Start、Step 和 Range 字符串属性 – 这些属性解释如下：
  
  * 帧数（如果为非负整数），例如： "Start": "120"；
  * 相对于源持续时间（如果以 % 后缀表示），例如： "Start": "15%"，或者
  * 时间戳（如果以 HH:MM:SS... 格式表示）。 例如 "Start" : "00:01:00"
    
    可以随意混搭使用表示法。
    
    此外，Start 还支持特殊的宏 {Best}，它会尝试判断第一个“有意义”的内容帧。注意：（Start 设置为 {Best} 时，将忽略 Step 与 Range）
  * 默认值：Start:{Best}
* 需要显式提供每个图像格式的输出格式：Jpg/Png/BmpFormat。 MES 会将 JpgVideo（如果已指定）与 JpgFormat 进行匹配，依此类推。 OutputFormat 引入了新的图像编解码器特定宏 {Index}，需要为图像输出格式提供该宏一次（且只需一次）。

## <a name="next-steps"></a>后续步骤

可以在编码作业挂起时检查[作业进度](media-services-check-job-progress.md)。

## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>另请参阅
[媒体服务编码概述](media-services-encode-asset.md)

