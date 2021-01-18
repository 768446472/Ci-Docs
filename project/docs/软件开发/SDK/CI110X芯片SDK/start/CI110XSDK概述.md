# SDK概述

***

## 1. 概述
为了应对不同的应用场景，同时简化SDK的配置和使用复杂度，目前SDK提供3个版本。分别是：  

- 纯离线SDK：CI110X_SDK_ASR_Offline
- 算法SDK：CI110X_SDK_ALG_Application
- 离在线SDK:CI110X_SDK_Combine_Cloud

***

## 2. SDK版本介绍

### 2.1. CI110X_SDK_ASR_Offline
主要针对简单应用场景，例如语音按摩枕、语音台灯等，支持的音频前端算法有：  

- AEC 回声消除，[《回声消除使用说明》](../components/回声消除使用说明.md)
- Denoise 降噪，[《语音降噪使用说明》](../components/语音降噪使用说明.md)

### 2.2. CI110X_SDK_ALG_Application
主要针对需要更多音频前端算法的应用场景，例如语音跑步机、抽油烟机等，支持的前端算法有：  

- AEC 回声消除，[《回声消除使用说明》](../components/回声消除使用说明.md)
- Denoise 降噪，[《语音降噪使用说明》](../components/语音降噪使用说明.md)
- 语音增强，[《DOA和语音增强使用说明》](../components/语音增强使用说明.md)
- 离线声纹识别  
- 离线命令词自学习 ,[《离线命令词自学习》](../components/离线命令词自学习使用说明.md)

### 2.2. CI110X_SDK_Combine_Cloud
主要针对云端识别、本地识别与云端识别结合的应用场景，例如智能语音音箱等，支持的前端算法有：  

- AEC 回声消除，[《回声消除使用说明》](../components/回声消除使用说明.md) 
- Denoise 降噪，[《语音降噪使用说明》](../components/语音降噪使用说明.md)
- 语音增强，[《DOA和语音增强使用说明》](../components/语音增强使用说明.md)
