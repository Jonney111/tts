## TTS 服务 TTS 服务

一个简单易用的文本转语音 (TTS) 服务，基于 Microsoft Azure 语音服务，提供高质量的语音合成能力。

#### 功能特点 功能特点

-- 支持多种语言和声音
-- 可调节语速和语调
-- 支持多种输出音频格式
-- 兼容 OpenAI TTS API
-- 支持长文本自动分割与合并
-- 提供 Web UI 和 RESTful API

#### 快速开始 快速开始

###### Docker 部署 Docker 部署

``````shell
docker run -d -p 8080:8080 --name=tts zuoban/zb-ttsdocker run -d -p 8080:8080 --name=tts zuoban/zb-tts
```

部署完成后，访问 `http://localhost:8080` 使用 Web 界面，或通过 `http://localhost:8080/api-doc` 查看 API 文档。部署完成后，访问 `http://localhost:8080` 使用 Web 界面，或通过 `http://localhost:8080/api-doc` 查看 API 文档。

### Cloudflare Worker 部署

1. 创建一个新的 Cloudflare Worker
2. 复制以下脚本内容到 Worker
   [worker.js](https://github.com/zuoban/tts/blob/main/workers/src/index.js)
3. 修改 API_KEY 配置

## API 使用示例

### 基础 API

```shell
# 基础文本转语音# 基础文本转语音
curl "http://localhost:8080/tts?t=你好，世界&v=zh-CN-XiaoxiaoNeural"curl "http://localhost:8080/tts?t=你好，世界&v=zh-CN-XiaoxiaoNeural"

# 调整语速和语调
curl "http://localhost:8080/tts?t=你好，世界&v=zh-CN-XiaoxiaoNeural&r=20&p=10"

# 使用情感风格
curl "http://localhost:8080/tts?t=今天天气真好&v=zh-CN-XiaoxiaoNeural&s=cheerful"
```

### OpenAI 兼容 API

```shell
curl -X POST "http://localhost:8080/v1/audio/speech" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "tts-1",
    "input": "你好，世界！",
    "voice": "zh-CN-XiaoxiaoNeural"
  }'
```

## 配置选项

您可以通过环境变量或配置文件自定义 TTS 服务：

```shell
# 使用自定义端口
docker run -d -p 9000:9000 -e PORT=9000 --name=tts zuoban/zb-tts

# 使用配置文件
docker run -d -p 8080:8080 -v /path/to/config.yaml:/configs/config.yaml --name=tts zuoban/zb-tts
```

### 配置文件详解

TTS 服务使用 YAML 格式的配置文件，默认位置为 `/app/config.yaml`。以下是配置文件的主要选项：

```yaml
server:
  port: 8080                # 服务监听端口
  read_timeout: 30          # HTTP 读取超时时间（秒）
  write_timeout: 30         # HTTP 写入超时时间（秒）
  base_path: ""             # API 基础路径前缀，如 "/api"

tts:
  region: "eastasia"        # Azure 语音服务区域
  default_voice: "zh-CN-XiaoxiaoNeural"  # 默认语音
  default_rate: "0"         # 默认语速，范围 -100 到 100
  default_pitch: "0"        # 默认语调，范围 -100 到 100
  default_format: "audio-24khz-48kbitrate-mono-mp3"  # 默认音频格式
  max_text_length: 65535    # 最大文本长度
  request_timeout: 30       # 请求 Azure 服务的超时时间（秒）
  max_concurrent: 10        # 最大并发请求数
  segment_threshold: 300    # 文本分段阈值
  min_sentence_length: 200  # 最小句子长度
  max_sentence_length: 300  # 最大句子长度
  api_key: '替换为您的密钥'  # (可选, /tts 接口使用)

  # OpenAI 到微软 TTS 中文语音的映射
  voice_mapping:
    alloy: "zh-CN-XiaoyiNeural"       # 中性女声
    echo: "zh-CN-YunxiNeural"         # 年轻男声
    fable: "zh-CN-XiaochenNeural"     # 儿童声
    onyx: "zh-CN-YunjianNeural"       # 成熟男声
    nova: "zh-CN-XiaohanNeural"       # 活力女声
    shimmer: "zh-CN-XiaomoNeural"     # 温柔女声

openai:
  api_key: '替换为您的密钥'               # OpenAI API 密钥（可选，api 兼容接口使用）
```

您可以根据自己的需求修改这些配置选项。 其中，`api_key` 为接口认证密钥，若不设置，则不需要认证。

以上配置均可通过环境变量进行覆盖，如 `SERVER_PORT`、`OPENAI_API_KEY` 等。

使用环境变量时，变量名需转换为大写并使用下划线代替点号。


## 本地构建与运行

要从源码构建和运行：

```shell
# 克隆仓库
git clone https://github.com/zuoban/tts.git
cd tts

# 构建
go build -o tts ./cmd/api

# 运行
./tts
```

## 支持的音频格式

- MP3: `audio-24khz-48kbitrate-mono-mp3`（默认）
- MP3: `audio-24khz-96kbitrate-mono-mp3`
- MP3: `audio-24khz-160kbitrate-mono-mp3`
- WAV: `riff-24khz-16bit-mono-pcm`
- OGG: `ogg-24khz-16bit-mono-opus`

更多格式请参考 API 文档。

## 许可证

MIT
