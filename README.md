# smartedu-dl-go

国家中小学智慧教育平台资源下载工具，基于 Go 和 Fyne 实现图形界面。当前版本支持从 `smartedu.cn` 页面链接、资源直链、教材/课程列表中解析并批量下载资源。

本版本重点增强了国家智慧教育平台“人工智能教育”相关内容的下载能力，适合下载和整理“人工智能百问”“人工智能通识”“科普知识”等 AI 教育视频资源。

关键词：国家中小学智慧教育平台、国家智慧教育平台、智慧教育平台下载、人工智能教育、人工智能百问、人工智能通识、AI 教育视频、科普知识视频、smartedu 下载器。

## 功能

- 支持输入智慧教育平台页面链接并自动解析资源。
- 支持教材 PDF、课程课件、图片、字幕、白板、配套音频下载。
- 支持课程视频下载，视频源为 `m3u8` 时会自动下载分片。
- 支持“人工智能教育”视频详情页：
  `https://basic.smartedu.cn/AIEducation/detail?...`
- 支持“人工智能教育”列表页：
  `https://basic.smartedu.cn/AIEducation/list?...`
- 点击“仅下载视频”时，如果解析到多个视频，会弹出列表让用户勾选要下载的内容。
- 对 AI 教育列表页会自动抓取当前分类下的全部视频，并按每页 12 个标注页码，例如 `第2页 013.`。
- 支持多线程下载视频，线程数可通过 `--threads` 设置。
- 支持保存下载日志，日志文件名为 `log-smartedudl.txt`。
- 支持环境变量 `SMARTEDU_TOKEN` 或系统 Keyring 保存登录信息。

## 支持的链接

可以直接粘贴以下类型链接到“输入链接”页：

```text
https://basic.smartedu.cn/tchMaterial/detail?contentType=assets_document&contentId=...
https://basic.smartedu.cn/syncClassroom/classActivity?activityId=...
https://basic.smartedu.cn/syncClassroom/prepare/detail?resourceId=...
https://basic.smartedu.cn/qualityCourse?courseId=...
https://basic.smartedu.cn/AIEducation/detail?contentType=assets_video&contentId=...
https://basic.smartedu.cn/AIEducation/list?content_id=...&defaultTag=...
https://.../edu_product/esp/assets/...
```

AI 教育列表页示例：

```text
https://basic.smartedu.cn/AIEducation/list?content_id=1423337d-b3bd-4b92-855e-e137f330619a&defaultTag=68122750-eba8-4561-92a5-8edc2f9b6ce7
```

普通解析默认按页面分页取资源；点击“仅下载视频”时，程序会自动展开 AI 教育列表页下的全部视频并弹窗选择。

## 运行环境

Windows 下运行 Fyne 程序需要 Go 和 GCC。

建议环境：

- Go 1.26 或与 `go.mod` 兼容的版本
- MSYS2 UCRT64 GCC
- Windows PowerShell

如果 `go run` 提示找不到 `gcc`，把 Go 和 MSYS2 UCRT64 加到当前 PowerShell：

```powershell
$env:Path='C:\Program Files\Go\bin;C:\msys64\ucrt64\bin;' + $env:Path
```

如果下载依赖很慢，可以设置 Go 代理：

```powershell
$env:GOPROXY='https://goproxy.cn,direct'
```

## 启动

在项目目录执行：

```powershell
go run . --debug --threads 10
```

参数说明：

- `--debug`：输出调试日志，排查解析失败时建议开启。
- `--threads 10`：视频分片并发下载数，电脑或网络较慢时可改小。
- `--local`：优先使用本地数据文件。

## 登录信息

部分教材、课程和视频需要登录信息。程序接受两种写法：

- 完整 `X-ND-AUTH`
- Access Token

优先级：

1. 环境变量 `SMARTEDU_TOKEN`
2. 系统 Keyring 中保存的 Token
3. 界面“登录信息”输入框

推荐把 Access Token 写入当前用户环境变量：

```powershell
[Environment]::SetEnvironmentVariable('SMARTEDU_TOKEN', '你的AccessToken', 'User')
```

设置后重新打开 PowerShell，再运行程序。

也可以直接把 Token 粘贴到界面底部“登录信息”输入框，程序会在回车或失焦时保存到系统 Keyring。

## 使用方法

### 下载普通资源

1. 打开程序。
2. 进入“输入链接”。
3. 粘贴一个或多个资源页面链接，每行一个。
4. 选择保存目录。
5. 勾选要下载的资源类型，例如 PDF、MP3、字幕。
6. 点击“下载已选择资源”。

### 下载视频

1. 粘贴课程视频详情页、AI 教育详情页或 AI 教育列表页。
2. 选择保存目录。
3. 点击“仅下载视频”。
4. 如果解析到多个视频，会弹出选择窗口。
5. 勾选要下载的视频，点击“开始下载”。

人工智能教育列表页如果视频较多，点击“仅下载视频”后解析需要一点时间。程序底部会显示等待提示，请耐心等待弹窗出现。

AI 教育列表页常见情况是浏览器点“下一页”后地址栏不变。现在不需要复制第二页地址，程序会把当前分类下的视频全部解析出来，并在弹窗中按 `第1页 001-012`、`第2页 013-024` 这样的顺序展示。

### 只下载第二页视频

以 AI 教育列表页为例：

1. 粘贴第一页地址。
2. 点击“仅下载视频”。
3. 弹窗出现后点“全不选”。
4. 勾选 `第2页 013.` 到 `第2页 024.` 的视频。
5. 点击“开始下载”。

## 视频格式

程序解析到的视频格式通常是 `m3u8`。下载时会抓取全部分片并保存为 `.ts` 文件。

如需转成 `.mp4`，可以使用 FFmpeg：

```powershell
ffmpeg -i input.ts -c copy output.mp4
```

## 常见问题

### go run 卡在 downloading

第一次运行会下载依赖，Fyne 依赖较多，可能需要几分钟。可以先设置代理：

```powershell
$env:GOPROXY='https://goproxy.cn,direct'
go mod download
go run . --debug --threads 10
```

### 提示 gcc 找不到

安装 MSYS2 后确认存在：

```text
C:\msys64\ucrt64\bin\gcc.exe
```

然后在 PowerShell 加入 PATH：

```powershell
$env:Path='C:\Program Files\Go\bin;C:\msys64\ucrt64\bin;' + $env:Path
```

### 下载失败或 401

通常是 Token 失效。重新登录智慧教育平台后获取新的 Access Token，再更新 `SMARTEDU_TOKEN` 或界面里的登录信息。

### AI 教育列表页只想下载当前第一页

普通“下载已选择资源”会按列表 URL 的分页参数解析；“仅下载视频”会为了方便选择，自动展开全部视频。如果只要第一页，可以在弹窗里只保留 `第1页 001-012`。

## 开发

格式化：

```powershell
gofmt -w internal
```

测试：

```powershell
go test ./... -count=1
```

本地运行：

```powershell
go run . --debug --threads 10
```

## 生成 Windows exe

在项目目录执行：

```powershell
New-Item -ItemType Directory -Force dist | Out-Null
go build -trimpath -ldflags "-H=windowsgui -s -w" -o dist\smartedu-dl-go.exe .
Compress-Archive -Path dist\smartedu-dl-go.exe,README.md,CHANGELOG.md,LICENSE -DestinationPath dist\smartedu-dl-go-windows-amd64.zip -Force
```

生成结果：

- `dist\smartedu-dl-go.exe`：可直接双击运行的 Windows 程序。
- `dist\smartedu-dl-go-windows-amd64.zip`：适合发给别人使用的压缩包。

这个 exe 不会包含任何人的 Token。其他用户第一次运行后，需要在界面“登录信息”中填写自己的 Access Token 或 `X-ND-AUTH`。

## 说明

本项目仅用于学习和个人资源整理。下载内容应遵守智慧教育平台的使用规则和相关版权要求。
