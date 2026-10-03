# 550W AI去字幕去水印

ComfyUI 原生节点支持图片去文字去水印、本地视频去字幕去水印，以及抖音、快手、哔哩哔哩、微博等分享链接解析。从对应 App 或网站通过分享操作复制链接。视频默认全屏擦除，可在高级 rect 参数填写 [x1,y1,x2,y2] 像素坐标。

## 安装与接入

将目录放入 custom_nodes，使用 ComfyUI 的 Python 安装 requirements.txt 后重启。Python 3.11+；torch、numpy、Pillow 复用宿主，不另装 GPU 环境。

支持 OAuth＋API Key。旧工作流默认 api_key，配置环境变量 FIFTYW_API_KEY / FIFTYW_USER_NO 即可。新工作流也可显式选择 oauth，不会静默切换到 API Key 账户。节点不保存凭据到工作流。

OAuth：从 custom_nodes 目录使用宿主 Python 执行 `python -m 550w-media.oauth_cli connect`，模块名替换为实际安装目录名。必须在同一台机器打开授权浏览器并接收临时 127.0.0.1 回调；五分钟超时后关闭，不对外开放。远程或无桌面的宿主优先使用 API Key。授权入口使用国内区域，不随界面语言变化。

使用动态公共客户端注册、S256 PKCE 和 state 校验；凭据只保存到 macOS 钥匙串、Windows 凭据管理器或 Linux Secret Service，不允许明文降级。`status` 查看连接状态，`disconnect` 撤销刷新令牌并清理连接；撤销失败保留连接方便重试。请求前刷新过期令牌，不自动重试收费任务。每个区域连接使用一个 ComfyUI 进程。

这是宿主账户级连接，不是浏览器用户级隔离；不要将已授权的 ComfyUI 实例开放给不受信任的用户。

API Key 获取与管理：https://qzm.550wai.cn/api-keys

积分充值：https://qzm.550wai.cn/purchase?tab=speed

网页处理：https://qzm.550wai.cn/

## 工作流

单张 IMAGE 接入原生图片擦除，或选择已上传图片。本地 MP4/MOV 放入 ComfyUI input 目录后选择。提交必须显式 confirm_charge，并保留稳定操作 ID（OAuth 最长 64 字符）。

异步返回 JSON 后查询现有任务。成功图片 JSON 接图片结果节点，再接 Preview Image / Save Image；视频输出结果链接，不承诺原生 VIDEO 解码。分享解析成功但无法下载时保留解析得到的视频链接，供用户浏览器下载。超时结果未知时先查询，不自动重新付费提交。

图片最大 50 MiB，视频最大 1 GiB。仅处理自己拥有或合法获准编辑的素材。选中素材及必要凭据通过固定 HTTPS 服务传输；临时 IMAGE 转换文件用后清理，不新增遥测请求。业务事实沿用服务端已有公共入口。

国内节点 ID 为 node-550w-media-cn，海外为 node-550w-media，分别使用独立介绍和授权入口。此说明不代表 Registry 已上架，提交状态另行核实。MIT 许可证见 LICENSE。支持邮箱：support@550wai.com。
