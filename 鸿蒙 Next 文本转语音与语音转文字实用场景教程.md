> 在数字化快速发展的今天，语音技术在提升交互体验和辅助特殊人群方面发挥着重要作用。鸿蒙 Next 的 Core Speech Kit 提供了文本转语音（TTS）和语音识别（ASR）功能，为开发者打造无障碍交互应用提供了有力支持,本文将深入探讨其实用场景。

语音技术演进与鸿蒙实现优势
-------------

### 核心能力升级

**鸿蒙 Core Speech Kit 提供 双引擎驱动模式**

* 在线模式：依托华为云语音服务实现高精度识别与自然语音合成（支持 15+ 语种）
* 离线模式：内置轻量级引擎保障无网环境基础功能（需下载 200MB 语音包 **性能优化策略**
* 端侧计算加速：利用 NPU 芯片实现 200ms 级低延迟响应
* 多模态融合：结合设备传感器数据（如运动状态）动态调整拾音策略

一、文本转语音（TTS）应用场景
----------------

### （一）无障碍辅助

* **视障人士阅读** ：视障人士在使用手机、平板等设备时，难以通过视觉获取屏幕信息。借助 TTS 功能，系统应用可实现屏幕朗读，如阅读新闻、浏览社交动态、查看消息提醒等，让视障人士 "听" 到文字内容，畅享信息获取的便利。
* **长辈模式** ：部分长辈对小字体阅读感到困难，TTS 能将文章、电子书等内容转化为语音播报，方便长辈收听新闻资讯、小说故事等，丰富他们的精神生活。

### （二）智能语音助手

* **智能家居控制** ：在智能家居场景中，用户可通过语音指令控制设备，如 "打开窗帘""开启空调"。TTS 可用于确认指令和反馈设备状态，如 "窗帘已打开"，提升交互自然度。
* **智能客服** ：在应用内客服场景，TTS 可将常见问题解答、操作指南等文本内容转化为语音，为用户提供更便捷的咨询方式，尤其适用于驾驶等不便阅读的场景。

二、语音转文字（ASR）应用场景
----------------

### （一）听障人士辅助

* **音频内容获取** ：听障人士难以通过听觉获取音频信息，ASR 可将语音会议、讲座、广播等内容转为文字，方便他们获取关键信息，提升学习和工作效率。
* **社交互动** ：在社交聊天中，ASR 可将语音消息转为文字，让听障人士更好地参与对话，增进与他人的情感交流。

### （二）高效办公与学习

* **会议记录** ：在会议中，ASR 可实时将发言转为文字，生成会议纪要，节省人工记录时间，提高办公效率。
* **课堂笔记** ：学生可利用 ASR 将老师讲课内容转为文字，方便课后复习，尤其适用于大量信息需要记录的场景。

三、开发语音交互
--------

### （一）开发环境准备

确保已安装鸿蒙开发工具，并创建 HarmonyOS 项目，添加 Core Speech Kit 相关依赖。

### （二）文本转语音开发步骤

1. **引入模块** ：在代码文件中引入 textToSpeech 模块。

   ```javascript
   import { textToSpeech } from '@kit.CoreSpeechKit';
   import { BusinessError } from '@kit.BasicServicesKit';
   ```

2. **创建引擎** ：调用 createEngine 接口创建 TextToSpeechEngine 实例，设置语言、音色等参数。

   ```javascript
   let ttsEngine: textToSpeech.TextToSpeechEngine;
   let extraParam: Record<string, Object> = {"style": 'interaction-broadcast', "locate": 'CN', "name": 'EngineName'};
   let initParamsInfo: textToSpeech.CreateEngineParams = { language: 'zh-CN', person: 0, online: 1, extraParams: extraParam };
   textToSpeech.createEngine(initParamsInfo, (err: BusinessError, textToSpeechEngine: textToSpeech.TextToSpeechEngine) => {
     if (!err) {
       ttsEngine = textToSpeechEngine;
     } else {
       console.error(`Failed to create engine. Code: ${err.code}, message: ${err.message}.`);
     }
   });
   ```

3. **设置播报参数与监听器** ：实例化 SpeakParams 对象和 SpeakListener 对象，设置播报策略、音量、语速等参数，并监听播报事件。

   ```javascript
   let speakListener: textToSpeech.SpeakListener = { /* 监听器方法实现 */ };
   ttsEngine.setListener(speakListener);
   let originalText: string = 'Hello HarmonyOS';
   let extraParam: Record<string, Object> = {"queueMode": 0, "speed": 1, "volume": 2, "pitch": 1, "languageContext": 'zh-CN', "audioType": "pcm", "soundChannel": 3, "playType": 1 };
   let speakParams: textToSpeech.SpeakParams = { requestId: '123456', extraParams: extraParam };
   ttsEngine.speak(originalText, speakParams);
   ```

### （三）语音转文字开发步骤

1. **引入模块** ：引入 speechRecognizer 模块。

   ```javascript
   import { speechRecognizer } from '@kit.CoreSpeechKit';
   import { BusinessError } from '@kit.BasicServicesKit';
   ```

2. **创建引擎** ：调用 createEngine 方法创建 SpeechRecognitionEngine 实例，设置语言、识别模式等参数。

   ```javascript
   let asrEngine: speechRecognizer.SpeechRecognitionEngine;
   let sessionId: string = '123456';
   let extraParam: Record<string, Object> = {"locate": "CN", "recognizerMode": "short"};
   let initParamsInfo: speechRecognizer.CreateEngineParams = { language: 'zh-CN', online: 1, extraParams: extraParam };
   speechRecognizer.createEngine(initParamsInfo, (err: BusinessError, speechRecognitionEngine: speechRecognizer.SpeechRecognitionEngine) => {
     if (!err) {
       asrEngine = speechRecognitionEngine;
     } else {
       console.error(`Failed to create engine. Code: ${err.code}, message: ${err.message}.`);
     }
   });
   ```

3. **设置监听器** ：实例化 RecognitionListener 对象，设置回调方法，接收语音识别结果等信息。

   ```javascript
   let setListener: speechRecognizer.RecognitionListener = { /* 回调方法实现 */ };
   asrEngine.setListener(setListener);
   ```

4. **开始识别** ：根据音频来源（文件或麦克风），设置识别参数，调用 startListening 方法开始识别。

   ```javascript
   private startListeningForWriteAudio() {
     let recognizerParams: speechRecognizer.StartParams = { sessionId: this.sessionId, audioInfo: { audioType: 'pcm', sampleRate: 16000, soundChannel: 1, sampleBit: 16 } };
     asrEngine.startListening(recognizerParams);
   }
   ```

5. **写入音频流** ：对于音频文件识别，读取文件并写入音频流；对于麦克风识别，获取音频流后写入。

   ```javascript
   let uint8Array: Uint8Array = new Uint8Array();
   asrEngine.writeAudio(sessionId, uint8Array);
   ```

四、注意事项
------

1. **权限配置** ：使用语音识别功能时，需在 module.json5 配置文件中添加 ohos.permission.MICROPHONE 权限。

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/639bdd17265444859e9c01fdc00a78a9~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg57qv54ix5o6M6Zeo5Lq6:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjg0OTU0ODM0MjQwMzQ1NCJ9&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1741751663&x-orig-sign=iMHWetphk2GyjCAV4rhRa9ZpnbY%3D)

2. **音频格式要求** ：确保音频文件格式符合要求，如 pcm 格式、采样率等参数设置正确。
3. **网络状态** ：部分功能可能依赖网络，在无网环境下可能无法使用，需根据实际需求进行处理。

五、实战案例
------

### （一）文本转语音

```javascript
import { textToSpeech } from '@kit.CoreSpeechKit';
import { BusinessError } from '@kit.BasicServicesKit';
let ttsEngine: textToSpeech.TextToSpeechEngine;
function createEngine() {
  let extraParam: Record<string, Object> = {"style": 'interaction-broadcast', "locate": 'CN', "name": 'EngineName'};
  let initParamsInfo: textToSpeech.CreateEngineParams = { language: 'zh-CN', person: 0, online: 1, extraParams: extraParam };
  textToSpeech.createEngine(initParamsInfo, (err: BusinessError, textToSpeechEngine: textToSpeech.TextToSpeechEngine) => {
    if (!err) {
      ttsEngine = textToSpeechEngine;
    } else {
      console.error(`Failed to create engine. Code: ${err.code}, message: ${err.message}.`);
    }
  });
}
function speak(text: string) {
  let speakListener: textToSpeech.SpeakListener = { /* 监听器方法实现 */ };
  ttsEngine.setListener(speakListener);
  let extraParam: Record<string, Object> = {"queueMode": 0, "speed": 1, "volume": 2, "pitch": 1, "languageContext": 'zh-CN', "audioType": "pcm", "soundChannel": 3, "playType": 1 };
  let speakParams: textToSpeech.SpeakParams = { requestId: '123456', extraParams: extraParam };
  ttsEngine.speak(text, speakParams);
}
```

### （二）语音转文字

```javascript
import { speechRecognizer } from '@kit.CoreSpeechKit';
import { BusinessError } from '@kit.BasicServicesKit';
let asrEngine: speechRecognizer.SpeechRecognitionEngine;
let sessionId: string = '123456';
function createEngine() {
  let extraParam: Record<string, Object> = {"locate": "CN", "recognizerMode": "short"};
  let initParamsInfo: speechRecognizer.CreateEngineParams = { language: 'zh-CN', online: 1, extraParams: extraParam };
  speechRecognizer.createEngine(initParamsInfo, (err: BusinessError, speechRecognitionEngine: speechRecognizer.SpeechRecognitionEngine) => {
    if (!err) {
      asrEngine = speechRecognitionEngine;
    } else {
      console.error(`Failed to create engine. Code: ${err.code}, message: ${err.message}.`);
    }
  });
}
function startRecognition() {
  let setListener: speechRecognizer.RecognitionListener = { /* 回调方法实现 */ };
  asrEngine.setListener(setListener);
  let recognizerParams: speechRecognizer.StartParams = { sessionId: sessionId, audioInfo: { audioType: 'pcm', sampleRate: 16000, soundChannel: 1, sampleBit: 16 } };
  asrEngine.startListening(recognizerParams);
}
function writeAudio(audioData: Uint8Array) {
  asrEngine.writeAudio(sessionId, audioData);
}
```

