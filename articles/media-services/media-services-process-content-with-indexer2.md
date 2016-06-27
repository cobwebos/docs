<properties
	pageTitle="使用 Azure Media Indexer 2 预览版为媒体文件编制索引"
	description="使用 Azure Media Indexer，可以使媒体文件内容可供搜索，并为隐藏的字幕和关键字生成全文本脚本。本主题演示如何使用 Media Indexer 2 预览版。"
	services="media-services"
	documentationCenter=""
	authors="Juliako"
	manager="erikre"
	editor=""/>

<tags
	ms.service="media-services"
	ms.date="04/22/2016"   
	wacn.date=""/>


# 使用 Azure Media Indexer 2 预览版为媒体文件编制索引

##概述

**Azure Media Indexer 2 预览版**媒体处理器 (MP) 让你能够使媒体文件和内容可供搜索，以及生成隐藏字幕跟踪。与 [Azure Media Indexer](/documentation/articles/media-services-index-content) 以前的版本相比，**Azure Media Indexer 2 预览版**执行编制索引的速度更快，并提供更广泛的语言支持。支持的语言包括英语、西班牙语、法语、德语、意大利语、汉语、葡萄牙语和阿拉伯语。

**Azure Media Indexer 2 预览版** MP 目前为预览版。

本主题演示如何使用 **Azure Media Indexer 2 预览版**创建索引作业。

>[AZURE.NOTE]请注意以下事项：
>
>Indexer 2 在 Azure China 和 Azure Government 中不受支持。
>
>预览版限制为大约 10 分钟的处理时间，但对所有客户免费。
>
>在编制内容的索引时，请确保使用语音极其清晰的媒体文件（没有背景音乐、噪音、特效音或麦克风电流嘶嘶声）。适当内容的某些示例包括：录制的会议、讲座或演示内容。以下内容可能不适合用于编制索引：电影、电视剧、混合了音频和声音特效的任何内容、带有背景噪音（电流嘶嘶声）的不当录制内容。


本主题提供有关 **Azure Media Indexer 2 预览版**的详细信息，并演示如何将它与适用于 .NET 的媒体服务 SDK 配合使用

##输入和输出文件

###输入文件

音频或视频文件

###输出文件

索引作业可以生成以下格式的隐藏字幕文件：

- **SAMI**
- **TTML**
- **WebVTT**

采用这些格式的隐藏字幕 (CC) 文件可用于使有听力障碍的用户能够访问音频和视频文件。

##任务配置（预设）

在使用 **Azure Media Indexer 2 预览版**创建索引任务时，必须指定配置预设值。

下面的 JSON 设置可用的参数。

	{
	  "version":"1.0",
	  "Features":
	    [
	       {
	       "Options": {
	            "Formats":["WebVtt","ttml"],
	            "Language":"enUs",
	            "Type":"RecoOptions"
	       },
	       "Type":"SpReco"
	    }]
	}

##支持的语言  

Azure Media Indexer 2 预览版支持以下语言的语音转换为文本功能（在任务配置中指定语言名称时，使用括号中的 4 个字符的代码，如下所示）：

- 英语 [EnUs]
- 西班牙语 [EsEs]
- 中文 [ZhCn]
- 法语 [FrFr]
- 德语 [DeDe]
- 意大利语 [ItIt]
- 葡萄牙语 [PtBr]
- 阿拉伯语（埃及）[ArEg]


## 代码示例

以下程序演示如何：

1. 创建资产并将媒体文件上传到资产。
1. 使用基于包含以下 json 预设值的配置文件的索引任务，创建一个作业。
			
		{
		  "version":"1.0",
		  "Features":
		    [
		       {
		       "Options": {
		            "Formats":["WebVtt","ttml"],
		            "Language":"enUs",
		            "Type":"RecoOptions"
		       },
		       "Type":"SpReco"
		    }]
		}

1. 下载输出文件。
	
		using System;
		using System.Configuration;
		using System.IO;
		using System.Linq;
		using Microsoft.WindowsAzure.MediaServices.Client;
		using System.Threading;
		using System.Threading.Tasks;
		
		namespace IndexContent
		{
		    class Program
		    {
		        // Read values from the App.config file.
		        private static readonly string _mediaServicesAccountName =
		            ConfigurationManager.AppSettings["MediaServicesAccountName"];
		        private static readonly string _mediaServicesAccountKey =
		            ConfigurationManager.AppSettings["MediaServicesAccountKey"];
		
		        // Field for service context.
		        private static CloudMediaContext _context = null;
		        private static MediaServicesCredentials _cachedCredentials = null;
		
		        static void Main(string[] args)
		        {
		
		            // Create and cache the Media Services credentials in a static class variable.
		            _cachedCredentials = new MediaServicesCredentials(
		                            _mediaServicesAccountName,
		                            _mediaServicesAccountKey);
		            // Used the cached credentials to create CloudMediaContext.
		            _context = new CloudMediaContext(_cachedCredentials);
		
		            // Run indexing job.
		            var asset = RunIndexingJob(@"C:\supportFiles\Indexer\BigBuckBunny.mp4",
		                                        @"C:\supportFiles\Indexer\config.json");
		
		            // Download the job output asset.
		            DownloadAsset(asset, @"C:\supportFiles\Indexer\Output");
		        }
		
		        static IAsset RunIndexingJob(string inputMediaFilePath, string configurationFile)
		        {
		            // Create an asset and upload the input media file to storage.
		            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
		                "My Indexing Input Asset",
		                AssetCreationOptions.None);
		
		            // Declare a new job.
		            IJob job = _context.Jobs.Create("My Indexing Job");
		
		            // Get a reference to Azure Media Indexer 2 Preview.
		            string MediaProcessorName = "Azure Media Indexer 2 Preview";
		
		            var processor = GetLatestMediaProcessorByName(MediaProcessorName);
		
		            // Read configuration from the specified file.
		            string configuration = File.ReadAllText(configurationFile);
		
		            // Create a task with the encoding details, using a string preset.
		            ITask task = job.Tasks.AddNew("My Indexing Task",
		                processor,
		                configuration,
		                TaskOptions.None);
		
		            // Specify the input asset to be indexed.
		            task.InputAssets.Add(asset);
		
		            // Add an output asset to contain the results of the job.
		            task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);
		
		            // Use the following event handler to check job progress.  
		            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);
		
		            // Launch the job.
		            job.Submit();
		
		            // Check job execution and wait for job to finish.
		            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
		
		            progressJobTask.Wait();
		
		            // If job state is Error, the event handling
		            // method for job progress should log errors.  Here we check
		            // for error state and exit if needed.
		            if (job.State == JobState.Error)
		            {
		                ErrorDetail error = job.Tasks.First().ErrorDetails.First();
		                Console.WriteLine(string.Format("Error: {0}. {1}",
		                                                error.Code,
		                                                error.Message));
		                return null;
		            }
		
		            return job.OutputMediaAssets[0];
		        }
		
		        static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
		        {
		            IAsset asset = _context.Assets.Create(assetName, options);
		
		            var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
		            assetFile.Upload(filePath);
		
		            return asset;
		        }
		
		        static void DownloadAsset(IAsset asset, string outputDirectory)
		        {
		            foreach (IAssetFile file in asset.AssetFiles)
		            {
		                file.Download(Path.Combine(outputDirectory, file.Name));
		            }
		        }
		
		        static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
		        {
		            var processor = _context.MediaProcessors
		                .Where(p => p.Name == mediaProcessorName)
		                .ToList()
		                .OrderBy(p => new Version(p.Version))
		                .LastOrDefault();
		
		            if (processor == null)
		                throw new ArgumentException(string.Format("Unknown media processor",
		                                                           mediaProcessorName));
		
		            return processor;
		        }
		
		        static private void StateChanged(object sender, JobStateChangedEventArgs e)
		        {
		            Console.WriteLine("Job state changed event:");
		            Console.WriteLine("  Previous state: " + e.PreviousState);
		            Console.WriteLine("  Current state: " + e.CurrentState);
		
		            switch (e.CurrentState)
		            {
		                case JobState.Finished:
		                    Console.WriteLine();
		                    Console.WriteLine("Job is finished.");
		                    Console.WriteLine();
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
		                    // LogJobStop(job.Id);
		                    break;
		                default:
		                    break;
		            }
		        }
		    }
		}




## 相关链接

[Azure Media Services Analytics Overview（Azure 媒体服务分析概述）](/documentation/articles/media-services-analytics-overview)

[Azure 媒体分析演示](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)
<!---HONumber=Mooncake_0620_2016-->