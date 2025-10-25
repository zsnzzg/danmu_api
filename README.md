<div align="center">
  <img src="https://i.mji.rip/2025/09/27/eedc7b701c0fa5c1f7c175b22f441ad9.jpeg" alt="Clash" width="128" style="border-radius: 16px;" />
</div>

<h2 align="center">
LogVar 弹幕 API 服务器
</h2>

[![GitHub](https://img.shields.io/badge/-GitHub-181717?logo=github)](https://github.com/huangxd-/danmu_api)
![GitHub forks](https://img.shields.io/github/forks/huangxd-/danmu_api)
![GitHub Repo stars](https://img.shields.io/github/stars/huangxd-/danmu_api)
![GitHub License](https://img.shields.io/github/license/huangxd-/danmu_api)
![Docker Image Version](https://img.shields.io/docker/v/logvar/danmu-api?sort=semver)
![Docker Pulls](https://img.shields.io/docker/pulls/logvar/danmu-api)
[![telegram](https://img.shields.io/static/v1?label=telegram&message=telegram_channel&color=blue)](https://t.me/logvar_danmu_channel)
[![telegram](https://img.shields.io/static/v1?label=telegram&message=telegram_group&color=blue)](https://t.me/logvar_danmu_group)

---

一个人人都能部署的基于 js 的弹幕 API 服务器，支持爱优腾芒哔人韩巴弹幕直接获取，兼容弹弹play的搜索、详情查询和弹幕获取接口规范，并提供日志记录，支持vercel/netlify/edgeone/cloudflare/docker/claw等部署方式，不用提前下载弹幕，没有nas或小鸡也能一键部署。

本项目仅为个人爱好开发，代码开源。如有任何侵权行为，请联系本人删除。

有问题提issue或 [私信机器人](https://t.me/ddjdd_bot) 都ok。

新加了 [tg频道](https://t.me/logvar_danmu_channel) ，方便发送更新通知，以及群组，太多人私信咨询了，索性增加一个 [互助群](https://t.me/logvar_danmu_group) ，大家有问题可以在群里求助。

> 请不要在国内媒体平台宣传本项目！

## 功能
- **API 接口**：
  - `GET /api/v2/search/anime?keyword=${queryTitle}`：根据关键字搜索动漫。
  - `POST /api/v2/match`：根据关键字匹配动漫，用于自动匹配。（已支持在match接口中通过@语法动态指定平台优先级，如`赴山海 S01E28 @qiyi`；已支持从网盘资源命名，如`无忧渡.S01E01.2160p.WEB-DL.H265.DDP.5.1`中提取 title/season/episode）
  - `GET /api/v2/search/episodes`：根据关键词搜索所有匹配的剧集信息。
  - `GET /api/v2/bangumi/:animeId`：获取指定动漫的详细信息。
  - `GET /api/v2/comment/:commentId?withRelated=true&chConvert=1`：获取指定弹幕评论，支持返回相关评论和字符转换。
  - `POST /api/v2/comment/by-url`：通过视频URL直接获取弹幕。
  - `GET /api/logs`：获取最近的日志（最多 500 行，格式为 `[时间戳] 级别: 消息`）。
- **弹幕格式输出**：支持 JSON 和 XML 两种格式输出，通过以下方式配置：
  - 环境变量：`DANMU_OUTPUT_FORMAT=json|xml`（默认：json）
  - 查询参数：`?format=xml` 或 `?format=json`（优先级最高）
  - 优先级：查询参数 > 环境变量 > 默认值
  - 示例：`GET /api/v2/comment/10001?format=xml` 返回 XML 格式弹幕
  - **XML 格式说明**：完全遵循 Bilibili 标准格式，8字段标准弹幕属性
- **日志记录**：捕获 `console.log`（info 级别）和 `console.error`（error 级别），JSON 内容格式化输出。
- **智能缓存管理**：支持内存缓存搜索结果和弹幕数据，避免短期内重复的不必要API请求。包括：
  - 搜索结果缓存（可通过 `SEARCH_CACHE_MINUTES` 配置，默认1分钟）
  - 弹幕缓存（可通过 `COMMENT_CACHE_MINUTES` 配置，默认5分钟）
  - 用户偏好记录（可通过 `MAX_LAST_SELECT_MAP` 配置，默认100条）
  - Redis 分布式缓存支持（可选）
- **部署支持**：支持本地运行、Docker 容器化、Vercel 一键部署、Netlify 一键部署、Edgeone 一键部署、Cloudflare 一键部署、Claw部署和 Docker 一键启动。
- **手动选择记忆**：支持记住之前搜索title时手动选择的anime，并在后续的match自动匹配时优选该anime【实验性】。
- **手动搜索支持输入播放链接获取弹幕**：支持手动搜索的播放器输入爱优腾芒哔播放链接可获取弹幕，如`senplayer`。
- **弹幕转换功能**：支持通过环境变量配置弹幕转换规则，包括：
  - 将顶部和底部弹幕转换为浮动弹幕（`CONVERT_TOP_BOTTOM_TO_SCROLL`）
  - 将彩色弹幕转换为纯白弹幕（`CONVERT_COLOR_TO_WHITE`）
  - 解决部分播放器不支持顶部/底部弹幕和彩色弹幕的问题

## 前置条件
- Node.js（v18.0.0 或更高版本；理论兼容更低版本，请自行测试）
- npm
- Docker（可选，用于容器化部署）

## 本地运行
1. **克隆仓库**：
   ```bash
   git clone <仓库地址>
   cd <项目目录>
   ```

2. **安装依赖**：
   ```bash
   npm install
   ```

3. **启动服务器**：
   ```bash
   npm start
   ```
   服务器将在 `http://{ip}:9321` 运行，默认token是`87654321`。

   **热更新支持**：修改 `.env` 文件后，应用会自动检测并重新加载配置（无需重启应用）。

   或者使用下面的命令
   ```bash
   # 启动
   node ./danmu_api/server.js
   # 测试
   node --test ./danmu_api/worker.test.js
   ```

4. **测试 API**：
   使用 Postman 或 curl 测试：
   - `GET http://{ip}:9321/87654321`
   - `GET http://{ip}:9321/87654321/api/v2/search/anime?keyword=生万物`
   - `POST http://{ip}:9321/87654321/api/v2/match`
   - `GET http://{ip}:9321/87654321/api/v2/search/episodes?anime=生万物`
   - `GET http://{ip}:9321/87654321/api/v2/bangumi/1`
   - `GET http://{ip}:9321/87654321/api/v2/comment/1?withRelated=true&chConvert=1`
   - `POST http://{ip}:9321/87654321/api/v2/comment/by-url`
   - `GET http://{ip}:9321/87654321/api/logs`

## 使用 Docker 运行
1. **构建 Docker 镜像**：
   ```bash
   docker build -t danmu-api .
   ```

2. **运行容器**：
   ```bash
   docker run -d -p 9321:9321 --name danmu-api -e TOKEN=87654321 danmu-api
   ```
   - 使用`-e TOKEN=87654321`设置`TOKEN`环境变量，覆盖Dockerfile中的默认值。
   - 或使用 `--env-file .env` 加载 .env 文件中的所有环境变量：`docker run -d -p 9321:9321 --name danmu-api --env-file .env danmu-api`

   **热更新支持**：如需支持环境变量热更新（修改 `.env` 文件后无需重启容器），请使用 Volume 挂载：
   ```bash
   docker run -d -p 9321:9321 --name danmu-api -v $(pwd)/.env:/app/.env --env-file .env danmu-api
   ```

   > **推荐**：使用 docker compose 部署可以更方便地管理配置和支持热更新，详见下方"Docker 一键启动"部分。

3. **测试 API**：
   使用 `http://{ip}:9321/{TOKEN}` 访问上述 API 接口。

## Docker 一键启动 【推荐】
1. **拉取镜像**：
   ```bash
   docker pull logvar/danmu-api:latest
   ```

2. **运行容器**：
   ```bash
   docker run -d -p 9321:9321 --name danmu-api -e TOKEN=87654321 logvar/danmu-api:latest
   ```
   - 使用`-e TOKEN=87654321`设置`TOKEN`环境变量。
   - 或使用 `--env-file .env` 加载 .env 文件中的所有环境变量：`docker run -d -p 9321:9321 --name danmu-api --env-file .env logvar/danmu-api:latest`

   **热更新支持**：如需支持环境变量热更新（修改 `.env` 文件后无需重启容器），请使用 Volume 挂载：
   ```bash
   docker run -d -p 9321:9321 --name danmu-api -v $(pwd)/.env:/app/.env --env-file .env logvar/danmu-api:latest
   ```

   或使用 docker compose 部署（**推荐，支持环境变量热更新**）：
   ```yaml
   services:
     danmu-api:
       image: logvar/danmu-api:latest
       ports:
         - "9321:9321"
       # 热更新支持：挂载 .env 文件，修改后容器会自动重新加载配置（无需重启容器）
       volumes:
         - ./.env:/app/.env
       restart: unless-stopped
   ```

   可以使用 watchtower 监控有新版本自动更新：
   ```yaml
   services:
     watchtower:
       image: containrrr/watchtower
       container_name: watchtower-gx
       restart: always
       volumes:
         - /var/run/docker.sock:/var/run/docker.sock
       environment:
         - TZ=Asia/Shanghai  # 保持时区正确
       command:
         - --cleanup         # 更新后清理旧镜像
         - --interval        # 间隔参数
         - "12600"           # 30分钟（1800秒），适合测试
         - danmu-api         # 监控的目标容器名
   ```

3. **测试 API**：
   使用 `http://{ip}:9321/{TOKEN}` 访问上述 API 接口。

## 部署到 Vercel 【推荐】

### 一键部署
点击以下按钮即可将项目快速部署到 Vercel：

[![Deploy with Vercel](https://vercel.com/button)](https://vercel.com/new/clone?repository-url=https://github.com/zsnzzg/danmu_api&repository-name=danmu_api)

**注意**：请将按钮链接中的 `https://github.com/huangxd-/danmu_api` 替换为你的实际 Git 仓库地址。编辑 `README.md` 并更新链接后，推送到仓库，点击按钮即可自动克隆和部署。
- **设置环境变量**：部署后，在 Vercel 仪表板中：
  1. 转到你的项目设置。
  2. 在“Environment Variables”部分添加 `TOKEN` 变量，输入你的 API 令牌值。
  3. 保存更改并重新部署。
- 示例请求：`https://{your_domain}.vercel.app/87654321/api/v2/search/anime?keyword=子夜归`

### 优化点
- Settings > Functions > Advanced Setting > Function Region 切换为 Hong Kong，能提高访问速度，体验更优
  > hk有可能访问不了360或其他源，可以尝试切其他region，如新加坡等
- vercel在国内被墙，请配合代理使用或绑定自定义域名

## 部署到 Netlify 【推荐】

### 一键部署
点击以下按钮即可将项目快速部署到 Netlify：

<a href="https://app.netlify.com/start/deploy?repository=https://github.com/huangxd-/danmu_api"><img src="https://www.netlify.com/img/deploy/button.svg"></a>

> 默认访问domain：https://{你的部署项目名}.netlify.app

- **设置环境变量**：部署后，在 Netlify 仪表板中：
  1. 点击Project configuration。
  2. 在“Environment variables”部分点击 “Add a variable” 添加 `TOKEN` 变量，输入你的 API 令牌值。
  3. 保存更改并重新部署。

## 部署到 腾讯云 edgeone pages

### 一键部署
[![使用 EdgeOne Pages 部署](https://cdnstatic.tencentcs.com/edgeone/pages/deploy.svg)](https://console.cloud.tencent.com/edgeone/pages/new?template=https://github.com/huangxd-/danmu_api&project-name=danmu-api&root-directory=.%2F&env=TOKEN)

> 注意：部署时请在环境变量配置区域填写你的TOKEN值，该变量将用于API服务的身份验证相关功能
> 
> 示例请求：`https://{your_domain}/{TOKEN}/api/v2/search/anime?keyword=子夜归`确认是否部署成功
>
> 部署的时候项目加速区域最好设置为"全球可用区（不含中国大陆）"，不然不绑定自定义域名貌似只能生成3小时的预览链接？[相关文档](https://edgeone.cloud.tencent.com/pages/document/175191784523485184)
> 
> 也可直接用国际站的部署按钮一键部署，默认选择"全球可用区（不含中国大陆）" [![使用 EdgeOne Pages 部署](https://cdnstatic.tencentcs.com/edgeone/pages/deploy.svg)](https://edgeone.ai/pages/new?template=https://github.com/huangxd-/danmu_api&project-name=danmu-api&root-directory=.%2F&env=TOKEN)
> 
<img src="https://i.mji.rip/2025/09/17/3a675876dabb92e4ce45c10d543ce66b.png" style="width:400px" />

> 如果每次访问都遇到404等问题，可能是edgeone pages修改了访问策略，每次接口请求都转发到了新的环境，没有缓存，导致获取不到对应的弹幕，推荐用vercel部署。
> 
> 解决方法：请配置环境变量`UPSTASH_REDIS_REST_URL`和`UPSTASH_REDIS_REST_TOKEN`，开启upstash redis存储

## 部署到 Cloudflare

### 一键部署
点击以下按钮即可将项目快速部署到 Cloudflare：

[![Deploy to Cloudflare Workers](https://deploy.workers.cloudflare.com/button)](https://deploy.workers.cloudflare.com/?url=https://github.com/huangxd-/danmu_api)

**注意**：请将按钮链接中的 `https://github.com/huangxd-/danmu_api` 替换为你的实际 Git 仓库地址。编辑 `README.md` 并更新链接后，推送到仓库，点击按钮即可自动克隆和部署。
- **设置环境变量**：部署后，在 Cloudflare 仪表板中：
  1. 转到你的 Workers 项目。
  2. 转到“Settings” > “Variables”。
  3. 添加 `TOKEN` 环境变量，输入你的 API 令牌值。
  4. 保存并部署。
- 示例请求：`https://{your_domain}.workers.dev/87654321/api/v2/search/anime?keyword=子夜归`

### 手动部署
创建一个worker，将`danmu_api/worker.js`里的代码直接拷贝到你创建的`worker.js`里，然后点击部署。

> cf部署可能不稳定，推荐用vercel/netlify部署。

## API食用指南
支持 forward/senplayer/hills/小幻/yamby/eplayerx/afusekt 等支持弹幕API的播放器。

以`senplayer`为例：
1. 获取到部署之后的API地址，如 `http://192.168.1.7:9321/87654321` ，其中`87654321`是默认token，如果有自定义环境变量TOKEN，请替换成相应的token
2. 将API地址填入自定义弹幕API，在`设置 - 弹幕设置 - 自定义弹幕API`
3. 播放界面点击`弹幕按钮 - 搜索弹幕`，选择你的弹幕API，会根据标题进行搜索，等待一段时间，选择剧集就行。
<img src="https://i.mji.rip/2025/09/14/1dae193008f23e507d3cc3733a92f0a1.jpeg" style="width:400px" />
<img src="https://i.mji.rip/2025/09/14/506fd7810928088d7450be00f67f27e6.png" style="width:400px" />
<img src="https://i.mji.rip/2025/09/14/e206ab329c232d8bed225c6a9ff6f506.jpeg" style="width:400px" />
<img src="https://i.mji.rip/2025/09/14/80aa5205d49a767447f61938f2dada20.jpeg" style="width:400px" />
<img src="https://i.mji.rip/2025/09/14/9fdf945fb247994518042691f60d7849.jpeg" style="width:400px" />
<img src="https://i.mji.rip/2025/09/14/dbacc0cf9c8a839f16b8960de1f38f11.jpeg" style="width:400px" />
4. 现已支持手动搜索标题输入爱优腾芒哔播放链接获取弹幕。

### XML 格式说明

API 支持返回 Bilibili 标准 XML 格式的弹幕数据，通过查询参数 `?format=xml` 指定。

**XML 格式示例**：
```xml
<?xml version="1.0" ?>
<i>
    <d p="5.0,5,25,16488046,1751533608,0,0,13190629936">有 162 条弹幕来袭~请做好准备🔥！</d>
    <d p="4.0,5,25,13818234,1751533608,0,0,84261947057">阿姐我来啦！[打call了]</d>
    <d p="5.0,1,25,16488046,1751533608,0,0,33648506749">2025-07-02打卡</d>
</i>
```

**属性 `p` 字段说明**（8个字段，逗号分隔）：
1. **时间**：弹幕出现时间（秒）
2. **类型**：1=滚动, 4=底部, 5=顶部
3. **字体**：字体大小（25=中, 18=小）
4. **颜色**：RGB 转十进制（16777215=白色）
5. **时间戳**：Unix 时间戳（秒）
6. **弹幕池**：弹幕池编号（通常为0）
7. **用户Hash**：用户唯一标识（数字格式）
8. **弹幕ID**：弹幕唯一编号（11位数字）

**使用示例**：
- 获取 JSON 格式：`GET /api/v2/comment/10001`
- 获取 XML 格式：`GET /api/v2/comment/10001?format=xml`
- 通过 URL 获取 XML：`POST /api/v2/comment/by-url?format=xml` （body: `{"videoUrl":"..."}`）

> 注意：
>
> ~~小幻在填写API的时候需要在API后面加上/api/v2，如http://192.168.1.7:9321/87654321/api/v2~~
> 
> （已对小幻做兼容，`/api/v2`可加可不加都可以正确处理）
> 
> 小幻在使用时可能出现掉匹配无法加载弹幕的问题，详见[这个issue](https://github.com/huangxd-/danmu_api/issues/33)，可以通过配置环境变量`UPSTASH_REDIS_REST_URL`和`UPSTASH_REDIS_REST_TOKEN`，开启upstash redis存储解决
> 
> 有很多人问FW能不能用，FW推荐直接使用插件，如果非要使用，则可以配合 `https://raw.githubusercontent.com/huangxd-/ForwardWidgets/refs/heads/main/widgets.fwd` 里的`danmu_api`插件使用

## 环境变量列表
| 变量名称      | 描述 |
| ----------- | ----------- |
| TOKEN      | 【可选】自定义用户token，不填默认为`87654321`       |
| OTHER_SERVER   | 【可选】兜底第三方弹幕服务器，不填默认为`https://api.danmu.icu`       |
| VOD_SERVERS      | 【可选】VOD服务器列表，支持多个服务器并发查询，格式：`名称@URL,名称@URL,...`，示例：`金蝉@https://zy.jinchancaiji.com,789@https://www.caiji.cyou,听风@https://gctf.tfdh.top`，不填默认为`金蝉@https://zy.jinchancaiji.com,789@https://www.caiji.cyou,听风@https://gctf.tfdh.top`       |
| VOD_RETURN_MODE      | 【可选】VOD返回模式，可选值：`all`（返回所有站点结果）、`fastest`（只返回最快的站点结果），默认为`fastest`。当配置多个VOD站点时，`all`模式会返回所有站点的结果（结果较多），`fastest`模式只返回首先响应成功的站点结果（结果较少，避免重复）       |
| VOD_REQUEST_TIMEOUT      | 【可选】VOD服务器单个请求超时时间（毫秒），防止慢速或失效的采集站阻塞搜索，默认为`5000`（5秒），建议值：`3000-10000`       |
| BILIBILI_COOKIE      | 【可选】b站cookie（填入后能抓取完整弹幕），如 `buvid3=E2BCA ... eao6; theme-avatar-tip-show=SHOWED`，请自行通过浏览器或抓包工具抓取，热心网友测试后，实际最少只需取 `SESSDATA=xxxx` 字段    |
| YOUKU_CONCURRENCY    | 【可选】youku弹幕请求并发数，用于加快youku弹幕请求速度，不填默认为`8`，最高`16`       |
| SOURCE_ORDER    | 【可选】源排序，用于按源对返回资源的排序（注意：先后顺序会影响自动匹配最终的返回），默认是`360,vod,renren,hanjutv`，表示360数据排在最前，hanjutv数据排在最后，示例：`360,renren`：只返回360数据和renren数据，且360数据靠前；当前可选择的源字段有 `360,vod,tencent,renren,hanjutv,bahamut`       |
| PLATFORM_ORDER    | 【可选】自动匹配优选平台，按顺序优先返回指定平台弹幕，默认为空，即返回第一个满足条件的平台，示例：`bilibili1,qq`，表示如果有b站的播放源，则优先返回b站的弹幕，否则就返回腾讯的弹幕，两者都没有，则返回第一个满足条件的平台；当前可选择的平台字段有 `qiyi, bilibili1, imgo, youku, qq, renren, hanjutv, bahamut`  |
| EPISODE_TITLE_FILTER    | 【可选】剧集标题正则过滤，按正则关键字对剧集或综艺的集标题进行过滤，适用于过滤一些预告或综艺非正式集，只支持match自动匹配，默认值如下 |
| ENABLE_EPISODE_FILTER    | 【可选】是否在手动选择接口中启用集标题过滤，默认为`false`（禁用），启用后 GET /api/v2/bangumi/{id} 和 GET /api/v2/search/anime 接口会过滤掉预告、花絮等特殊集，以及名称包含特殊关键词的动漫。       |
| BLOCKED_WORDS    | 【可选】弹幕屏蔽词列表，默认为空，示例如下       |
| GROUP_MINUTE    | 【可选】合并去重分钟数，表示按n分钟分组后对弹幕合并去重，默认为1，最大值为30，0表示不去重       |
| CONVERT_TOP_BOTTOM_TO_SCROLL    | 【可选】是否将顶部和底部弹幕转换为浮动弹幕，默认为`false`（不转换），启用后顶部弹幕（ct=5）和底部弹幕（ct=4）会被转换为浮动弹幕（ct=1），可选值：`true`、`false`       |
| CONVERT_COLOR_TO_WHITE    | 【可选】是否将彩色弹幕转换为纯白弹幕，默认为`false`（不转换），启用后所有非白色的弹幕颜色会被转换为纯白色（16777215），可选值：`true`、`false`       |
| DANMU_OUTPUT_FORMAT    | 【可选】弹幕输出格式，默认为`json`，可选值：`json`（JSON格式）、`xml`（XML格式），支持通过查询参数`?format=xml`或`?format=json`覆盖此设置，优先级：查询参数 > 环境变量 > 默认值       |
| PROXY_URL    | 【可选】代理地址，示例: `http://127.0.0.1:7897` ，目前只对巴哈姆特生效（注意：如果巴哈姆特请求不通，会拖慢搜索返回速度，所以除vercel/netlify/cloudflare之外默认不开启bahamut源，开启请先在SOURCE_ORDER环境变量中添加`bahamut`）如果你使用docker部署并且访问不了bahamut源，请配置代理地址；vercel/netlify/cf中理应都自然能联通，不用填写       |
| TMDB_API_KEY    | 【可选】TMDB API Key地址，目前只对巴哈姆特生效，配置后当巴哈第一次搜索无结果时自动从TMDB获取日语原名搜索，可以解决巴哈译名不同导致的搜索无结果问题，例如大陆常用译名`间谍过家家`在巴哈译名为`間諜家家酒`，正常搜索无法搜索到，配置后可以解决这一问题但会稍微影响请求速度，[TMDBAPI](https://www.themoviedb.org/settings/api)获取方法参考：[TMDB API Key申请 - 绿联NAS私有云](https://www.ugnas.com/tutorial-detail/id-226.html)       |
| RATE_LIMIT_MAX_REQUESTS    | 【可选】限流配置：1分钟内同一IP最大请求次数，默认为`3`，设置为`0`表示不限流       |
| LOG_LEVEL    | 【可选】日志级别，默认为`info`，可选值：`error`（仅错误）、`warn`（错误和警告）、`info`（所有日志），生产环境建议使用`warn`，调试时使用`info`       |
| SEARCH_CACHE_MINUTES    | 【可选】搜索结果缓存时间（分钟），默认为`1`，避免短期内重复的不必要API请求，同时保证获取最新的结果列表，可根据需要调整：Vercel/Cloudflare建议`1-5`分钟，Docker可设置`5-30`分钟，设置为`0`表示不缓存       |
| COMMENT_CACHE_MINUTES    | 【可选】弹幕缓存时间（分钟），默认为`1`，弹幕数据的缓存时间，独立于搜索结果缓存       |
| MAX_LAST_SELECT_MAP    | 【可选】最后选择映射缓存大小限制，默认为`100`，lastSelectMap最多保存的条目数，超过限制时删除最早的条目（FIFO），用于存储查询关键字上次选择的animeId       |
| UPSTASH_REDIS_REST_URL    | 【可选】Upstash redis url，需配合UPSTASH_REDIS_REST_TOKEN使用，用于持久化存储，不会因为冷启动而丢失过去的查询信息（在cf/eo/claw上配置后应该能更稳定点，也能解决小幻掉匹配的问题，但会稍微影响请求速度），获取方法请参考：`https://cloud.tencent.cn/developer/article/2424508`       |
| UPSTASH_REDIS_REST_TOKEN    | 【可选】Upstash redis token，需配合UPSTASH_REDIS_REST_URL使用，用于持久化存储，不会因为冷启动而丢失过去的查询信息（在cf/eo/claw上配置后应该能更稳定点，也能解决小幻掉匹配的问题，但会稍微影响请求速度），获取方法请参考：`https://cloud.tencent.cn/developer/article/2424508`       |

```regex
# EPISODE_TITLE_FILTER 默认值
(特别|惊喜|纳凉)?企划|合伙人手记|超前(营业|vlog)?|速览|vlog|reaction|纯享|加更(版|篇)?|抢先(看|版|集|篇)?|抢鲜|预告|花絮(独家)?|特辑|彩蛋|专访|幕后(故事|花絮|独家)?|直播(陪看|回顾)?|未播(片段)?|衍生|番外|会员(专享|加长|尊享|专属|版)?|片花|精华|看点|速看|解读|影评|解说|吐槽|盘点|拍摄花絮|制作花絮|幕后花絮|未播花絮|独家花絮|花絮特辑|先导预告|终极预告|正式预告|官方预告|彩蛋片段|删减片段|未播片段|番外彩蛋|精彩片段|精彩看点|精彩回顾|精彩集锦|看点解析|看点预告|NG镜头|NG花絮|番外篇|番外特辑|制作特辑|拍摄特辑|幕后特辑|导演特辑|演员特辑|片尾曲|插曲|高光回顾|背景音乐|OST|音乐MV|歌曲MV|前季回顾|剧情回顾|往期回顾|内容总结|剧情盘点|精选合集|剪辑合集|混剪视频|独家专访|演员访谈|导演访谈|主创访谈|媒体采访|发布会采访|采访|陪看(记)?|试看版|短剧|精编|Plus|独家版|特别版|短片|发布会|解忧局|走心局|火锅局|巅峰时刻|坞里都知道|福持目标坞民|.{3,}篇|(?!.*(入局|破冰局|做局)).{2,}局|观察室|上班那点事儿|周top|赛段|直拍|REACTION|VLOG|全纪录|开播|先导|总宣|展演|集锦|旅行日记|精彩分享|剧情揭秘

# 如果你想新增过滤词，请自定义EPISODE_TITLE_FILTER，示例如下，每个词用'|'隔开，增加的词都会追加到默认值后面
测试|test
```

```regex
# BLOCKED_WORDS 示例值
/.{20,}/,/^\d{2,4}[-/.]\d{1,2}[-/.]\d{1,2}([日号.]*)?$/,/^(?!哈+$)([a-zA-Z\u4e00-\u9fa5])\1{2,}/,/[0-9]+\.*[0-9]*\s*(w|万)+\s*(\+|个|人|在看)+/,/^[a-z]{6,}$/,/^(?:qwertyuiop|asdfghjkl|zxcvbnm)$/,/^\d{5,}$/,/^(\d)\1{2,}$/,/\d{1,4}/,/(20[0-3][0-9])/,/(0?[1-9]|1[0-2])月/,/\d{1,2}[.-]\d{1,2}/,/[@#&$%^*+\|/\-_=<>°◆◇■□●○★☆▼▲♥♦♠♣①②③④⑤⑥⑦⑧⑨⑩]/,/[一二三四五六七八九十百\d]+刷/,/第[一二三四五六七八九十百\d]+/,/(全体成员|报到|报道|来啦|签到|刷|打卡|我在|来了|考古|爱了|挖坟|留念|你好|回来|哦哦|重温|复习|重刷|再看|在看|前排|沙发|有人看|板凳|末排|我老婆|我老公|撅了|后排|周目|重看|包养|DVD|同上|同样|我也是|俺也|算我|爱豆|我家爱豆|我家哥哥|加我|三连|币|新人|入坑|补剧|冲了|硬了|看完|舔屏|万人|牛逼|煞笔|傻逼|卧槽|tm|啊这|哇哦)/

# 注释如下：
/.{20,}/  # 屏蔽20字符及以上的弹幕
/^\d{2,4}[-/.]\d{1,2}[-/.]\d{1,2}([日号.])?$/  # 屏蔽日期弹幕
/^(?!哈+$)([a-zA-Z\u4e00-\u9fa5])\1{2,}/  # 屏蔽单个汉字或者字母连续出现3次及以上的弹幕（排除纯“哈”重复）
/[0-9]+.[0-9]\s(w|万)+\s*(\+|个|人|在看)+/  # 屏蔽几点几万在看的弹幕
/^[a-z]{6,}$/  # 屏蔽6个及以上连续小写字母的弹幕
/^(?:qwertyuiop|asdfghjkl|zxcvbnm)$/  # 屏蔽键盘连续行的弹幕
/^\d{5,}$/  # 屏蔽5位及以上纯数字的弹幕
/^(\d)\1{2,}$/  # 屏蔽三个及以上相同数字重复的弹幕
/\d{1,4}/  # 屏蔽1-4位数字的弹幕
/(20[0-3][0-9])/  # 屏蔽2000-2039年份相关的弹幕
/(0?[1-9]|1[0-2])月/  # 屏蔽月份表述的弹幕
/\d{1,2}[.-]\d{1,2}/  # 屏蔽类似时间或日期分隔的数字弹幕
/[@#&$%^*+\|/\-_=<>°◆◇■□●○★☆▼▲♥♦♠♣①②③④⑤⑥⑦⑧⑨⑩]/  # 屏蔽特殊符号或表情符号的弹幕
/[一二三四五六七八九十百\d]+刷/  # 屏蔽数字或汉字数字后跟“刷”的弹幕
/第[一二三四五六七八九十百\d]+/  # 屏蔽“第几”序号相关的弹幕
/(全体成员|报到|报道|来啦|签到|刷|打卡|我在|来了|考古|爱了|挖坟|留念|你好|回来|哦哦|重温|复习|重刷|再看|在看|前排|沙发|有人看|板凳|末排|我老婆|我老公|撅了|后排|周目|重看|包养|DVD|同上|同样|我也是|俺也|算我|爱豆|我家爱豆|我家哥哥|加我|三连|币|新人|入坑|补剧|冲了|硬了|看完|舔屏|万人|牛逼|煞笔|傻逼|卧槽|tm|啊这|哇哦)/  # 屏蔽常见互动、报到或口语化弹幕词汇
```

## 采集源及对应平台列表
| 采集源      | 平台列表 |
| ----------- | ----------- |
| 360      | qiyi, bilibili1, imgo, youku, qq |
| vod      | qiyi, bilibili1, imgo, youku, qq |
| tencent  | qq |
| renren   | renren |
| hanjutv  | hanjutv |
| bahamut  | bahamut |

## 项目结构
```
danmu_api/
├── .github/
│   └── workflows/
│       ├── docker-image.yml
│       └── sync_fork.yml # vercel自动同步配置文件
├── danmu_api/
│   ├── esm-shim.js     # Node.js低版本兼容层
│   ├── server.js       # 本地node启动脚本
│   ├── worker.js       # 主 API 服务器代码
│   ├── worker.test.js  # 测试文件
├── netlify/
│   └── functions/
│       └── api.js      # netlify 中间处理逻辑
├── node-functions/
│   ├── [[...path]]..js # edgeone pages 所有路由跳转指向index
│   └── index.js        # edgeone pages 中间处理逻辑
├── .env.example
├── .gitignore
├── Dockerfile
├── edgeone.json        # edgeone pages 配置文件
├── LICENSE
├── netlify.toml        # netlify 配置文件
├── package.json
├── README.md
├── vercel.json         # vercel 配置文件
└── wrangler.toml       # cloudflare worker 配置文件
```

## 注意事项

### 热更新相关
- **本地运行**：修改 `.env` 文件后，应用会自动检测并重新加载配置（无需重启应用）。
- **Docker 部署**：需要使用 Volume 挂载 `.env` 文件才能支持热更新。推荐使用 docker compose 部署（见"Docker 一键启动"部分），配置 Volume 后修改 `.env` 文件容器会自动重新加载配置。
- **Vercel/Netlify/Cloudflare**：需要在平台的环境变量设置中修改，然后重新部署才能生效。

### 其他注意事项
- 日志存储在内存中，服务器重启后会清空。
- `/api/logs` 中的 JSON 日志会格式化显示，带缩进以提高可读性。
- 搜索结果和弹幕数据存储在内存中，服务器重启后会清空，可通过配置 `UPSTASH_REDIS_REST_URL` 和 `UPSTASH_REDIS_REST_TOKEN` 启用 Redis 持久化存储。
- 搜索结果缓存默认时间为 1 分钟，可通过环境变量 `SEARCH_CACHE_MINUTES` 调整（设置为 0 表示不缓存）。
- 确保 `package.json` 中包含 `node-fetch` 依赖。
- 一键部署需要将项目推送到公开的 Git 仓库（如 GitHub），并更新按钮中的仓库地址。
- 运行 Docker 容器时，需通过 `-e TOKEN=87654321` 传递 `TOKEN` 环境变量。
- cloudflare貌似被哔风控了。
- 如果想更换兜底第三方弹幕服务器，请添加环境变量`OTHER_SERVER`，示例`https://api.danmu.icu`。
- 如果想更换vod站点，请添加环境变量`VOD_SERVERS`，示例`金蝉@https://zy.jinchancaiji.com,789@https://www.caiji.cyou,听风@https://gctf.tfdh.top`（支持多个服务器并发查询）。
- 当配置多个VOD站点时，可通过`VOD_RETURN_MODE`环境变量控制返回结果方式：`all`（返回所有站点结果）或`fastest`（默认，只返回最快的站点结果，避免结果过多）。
- 推荐vercel/netlify部署，cloudflare/edgeone/claw不稳定，当然最稳定还是自己本地docker部署最佳。
- /api/v2/comment接口默认限流：1分钟内同一IP只能请求3次，可通过环境变量`RATE_LIMIT_MAX_REQUESTS`调整（设置为0表示不限流）。

### 关联项目
[喂饭教程1：danmu_api vercel 自动同步部署方案 - 永远保持最新版本！实时同步原作者更新](https://github.com/xiaoyao20084321/log-var-danmu-deployment-guide)

[喂饭教程2：logvar弹幕搭建教程（docker/claw）](https://blog.tencentx.de/p/logvar%E5%BC%B9%E5%B9%95%E6%90%AD%E5%BB%BA%E6%95%99%E7%A8%8B%E5%96%82%E9%A5%AD%E7%89%88/)

### 部署完成后在播放器填写后弹幕未生效自主排查步骤
以API示例 `http://192.168.1.7:9321/87654321` 为例
1. 首先确认你的api部署成功 访问 `http://192.168.1.7:9321/87654321` 有json输出
2. 检查你在播放器的填写是否正确，有无多余空格等
3. 播放器请求后，查看 `http://192.168.1.7:9321/87654321/api/logs` 日志，看请求是否有报错，比如有用户在自己软路由上搭建，但走了全局代理，导致人人等访问不了，请确保走直连
4. 如果你播放的影片片名不规范，很可能搜不到，请确保片名规范

### 贡献者
<a href="https://github.com/huangxd-/danmu_api/graphs/contributors">
  <img src="https://contrib.rocks/image?repo=huangxd-/danmu_api" alt="contributors" />
</a>

### 📈项目 Star 数增长趋势
#### Star History
[![Star History Chart](https://api.star-history.com/svg?repos=huangxd-/danmu_api&type=Date)](https://www.star-history.com/#huangxd-/danmu_api&Date)
