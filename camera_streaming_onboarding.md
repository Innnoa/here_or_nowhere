# ZLMediaKit 摄像头推流/拉流快速上手

- 日期：2026-03-04
- 执行者：Codex
- 适用范围：`/home/toast/Projects/ZLMediaKit/ZLMediaKit`

## 1. 这个项目是做什么的

ZLMediaKit 是一个 C++ 流媒体服务框架，核心能力是把不同协议的音视频流接入、分发、转协议和控制。  
对“摄像头推流/拉流控制”来说，它的价值是：

- 摄像头可以直接推到 ZLM（RTSP/RTMP/RTP 等）。
- ZLM 可以主动拉摄像头（比如拉海康/大华 RTSP 地址）。
- 拉进来的流可以一份输入，多协议输出（RTSP/RTMP/HTTP-FLV/HLS/WebRTC 等）。
- 提供 HTTP API，可以用业务系统做自动化控制（新增拉流、关断流、查询在线状态、转推上级平台等）。

## 2. 关键目录（先认路）

- `README.md`：功能总览、常见入口、Docker 快速启动命令。
- `conf/config.ini`：端口、协议开关、API 密钥、按需转协议等核心配置。
- `server/WebApi.cpp`：HTTP API 的真实实现入口（`/index/api/*`）。
- `www/swagger/openapi.json`：本地 Swagger 文档定义。
- `postman/ZLMediaKit.postman_collection.json`：可直接导入 Postman 的 API 集合。
- `webrtc/USAGE.md`：WebRTC 推拉流（含 WHIP/WHEP）说明。
- `www/webrtc/index.html`：自带 WebRTC 测试页面。

## 3. 先跑起来（最快路径）

### 3.1 方式一：Docker（最快）

```bash
docker run -id \
  -p 1935:1935 \
  -p 8080:80 \
  -p 8443:443 \
  -p 8554:554 \
  -p 10000:10000 \
  -p 10000:10000/udp \
  -p 8000:8000/udp \
  -p 9000:9000/udp \
  zlmediakit/zlmediakit:master
```

访问：

- 欢迎页：`http://<服务器IP>:8080/`
- Swagger：`http://<服务器IP>:8080/swagger/`
- WebRTC 示例页：`http://<服务器IP>:8080/webrtc/`

### 3.2 方式二：源码编译运行

```bash
cd /home/toast/Projects/ZLMediaKit/ZLMediaKit
mv -f .gitmodules_github .gitmodules
git submodule sync
git submodule update --init

cmake -S . -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j"$(nproc)"

cd release/linux/Release
./MediaServer
```

说明：

- 构建产物默认会放在 `release/<os>/<BuildType>/`。
- `conf/config.ini` 会在构建时拷贝到运行目录；实际生效的是运行目录里的 `config.ini`。

## 4. 默认端口速查（按 `conf/config.ini`）

- HTTP：`80`
- HTTPS：`443`
- RTMP：`1935`
- RTSP：`554`
- RTP 代理：`10000`
- WebRTC 传输：`8000`（UDP/TCP）
- WebRTC 信令：`3000`
- SRT：`9000`

## 5. 你做“摄像头控制”最常用的三种链路

### 5.1 摄像头主动推流到 ZLM

常见目标地址：

- RTSP 推流：`rtsp://<zlm_ip>:554/live/cam01`
- RTMP 推流：`rtmp://<zlm_ip>:1935/live/cam01`

本地 USB 摄像头示例（Linux，FFmpeg 推到 RTMP）：

```bash
ffmpeg -f v4l2 -i /dev/video0 \
  -c:v libx264 -preset veryfast -tune zerolatency \
  -f flv rtmp://127.0.0.1:1935/live/cam01
```

### 5.2 ZLM 主动拉摄像头（最常用于 IPC/安防设备）

用 API 创建拉流代理：

```bash
curl -X POST "http://127.0.0.1/index/api/addStreamProxy" \
  -d "secret=035c73f7-bb6b-4889-a715-d9eb2d1925cc" \
  -d "vhost=__defaultVhost__" \
  -d "app=live" \
  -d "stream=cam01" \
  -d "url=rtsp://admin:password@192.168.1.64:554/Streaming/Channels/101"
```

说明：

- `secret` 默认在 `config.ini` 的 `[api]` 段。
- 通过 `127.0.0.1` 访问时，默认配置可免 `secret`，但建议保留，便于脚本统一。

### 5.3 ZLM 转推到上级平台（级联）

把已有流转推到目标 RTMP：

```bash
curl -X POST "http://127.0.0.1/index/api/addStreamPusherProxy" \
  -d "secret=035c73f7-bb6b-4889-a715-d9eb2d1925cc" \
  -d "schema=rtmp" \
  -d "vhost=__defaultVhost__" \
  -d "app=live" \
  -d "stream=cam01" \
  -d "dst_url=rtmp://upstream.example.com/live/cam01"
```

## 6. 拉流播放地址模板（落地时最常用）

假设 `app=live`、`stream=cam01`：

- RTSP：`rtsp://<zlm_ip>:554/live/cam01`
- RTMP：`rtmp://<zlm_ip>:1935/live/cam01`
- HTTP-FLV：`http://<zlm_ip>/live/cam01.live.flv`
- HLS：`http://<zlm_ip>/live/cam01/hls.m3u8`
- HTTP-TS：`http://<zlm_ip>/live/cam01.live.ts`
- WebRTC（API 方式）：`http://<zlm_ip>/index/api/webrtc?app=live&stream=cam01&type=play`

浏览器调试 WebRTC：

- 打开：`http://<zlm_ip>/webrtc/`
- 将 url 填成 `http://<zlm_ip>/index/api/webrtc?app=live&stream=cam01&type=play`

## 7. 控制面 API 清单（建议先掌握）

- 查询 API 列表：`/index/api/getApiList`
- 查询流列表：`/index/api/getMediaList`
- 流在线检测：`/index/api/isMediaOnline`
- 关断流：`/index/api/close_stream`
- 新增拉流代理：`/index/api/addStreamProxy`
- 关闭拉流代理：`/index/api/delStreamProxy`
- 新增转推代理：`/index/api/addStreamPusherProxy`
- 关闭转推代理：`/index/api/delStreamPusherProxy`
- 新增 FFmpeg 拉流代理：`/index/api/addFFmpegSource`
- RTP 服务器控制：`/index/api/openRtpServer`、`/index/api/startSendRtp`、`/index/api/stopSendRtp`

本地查看方式：

- Swagger：`http://<zlm_ip>/swagger/`
- Postman 集合：`postman/ZLMediaKit.postman_collection.json`

## 8. Wiki 与官方文档入口（你后续重点看这些）

- Wiki 首页：<https://github.com/ZLMediaKit/ZLMediaKit/wiki>
- 快速开始：<https://github.com/ZLMediaKit/ZLMediaKit/wiki/快速开始>
- MediaServer HTTP API：<https://github.com/ZLMediaKit/ZLMediaKit/wiki/MediaServer支持的HTTP-API>
- MediaServer HTTP HOOK API：<https://github.com/ZLMediaKit/ZLMediaKit/wiki/MediaServer支持的HTTP-HOOK-API>
- 文档站：<https://docs.zlmediakit.com>

## 9. 建议你的下一步实施顺序

1. 先跑通一条 RTSP 摄像头拉流代理（`addStreamProxy`）。
2. 用 `getMediaList` + `isMediaOnline` 做在线检测。
3. 在业务服务里封装“新增/删除拉流代理、关断流、查询状态”四个接口。
4. 再接入 WebHook（上线后再做事件驱动扩展）。

## 10. 常见坑位

- 改了 `conf/config.ini` 但不生效：你改错文件了，运行时读的是 `release/.../config.ini`。
- 拉流成功但首屏慢：检查 `protocol.*_demand` 是否按需转协议开启。
- WebRTC 失败：优先检查 `rtc.externIP`、端口映射、防火墙和是否 HTTPS 场景。
- API 401/鉴权失败：先核对 `api.secret` 与请求参数 `secret` 是否一致。

