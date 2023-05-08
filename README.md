# ChatGPT Web

> 聲明：此專案只發佈於 GitHub，基於 MIT 協定，免費且作為開源學習使用。並且不會有任何形式的賣號、付費服務、討論群、討論群組等行為。謹防受騙。

更多功能：[chatgpt-web-plus](https://github.com/Chanzhaoyu/chatgpt-web-plus)

![cover](./docs/c1.png)
![cover2](./docs/c2.png)

- [ChatGPT Web](#chatgpt-web)
	- [介紹](#介紹)
	- [待實現路線](#待實現路線)
	- [前置要求](#前置要求)
		- [Node](#node)
		- [PNPM](#pnpm)
		- [填寫金鑰](#填寫金鑰)
	- [安裝依賴](#安裝依賴)
		- [後端](#後端)
		- [前端](#前端)
	- [測試環境運行](#測試環境運行)
		- [後端服務](#後端服務)
		- [前端網頁](#前端網頁)
	- [環境變數](#環境變數)
	- [打包](#打包)
		- [使用 Docker](#使用-docker)
			- [Docker 參數示例](#docker-參數示例)
			- [Docker build \& Run](#docker-build--run)
			- [Docker compose](#docker-compose)
			- [防止爬蟲抓取](#防止爬蟲抓取)
		- [使用 Railway 部署](#使用-railway-部署)
			- [Railway 環境變數](#railway-環境變數)
		- [手動打包](#手動打包)
			- [後端服務](#後端服務-1)
			- [前端網頁](#前端網頁-1)
	- [常見問題](#常見問題)
	- [參與貢獻](#參與貢獻)
	- [贊助](#贊助)
	- [License](#license)
## 介紹

支援雙模型，提供了兩種非官方 `ChatGPT API` 方法

| 方式                                          | 免費？ | 可靠性     | 品質 |
| --------------------------------------------- | ------ | ---------- | ---- |
| `ChatGPTAPI(gpt-3.5-turbo-0301)`                           | 否     | 可靠       | 相對較笨 |
| `ChatGPTUnofficialProxyAPI(網頁 accessToken)` | 是     | 相對不可靠 | 聰明 |

對比：
1. `ChatGPTAPI` 使用 `gpt-3.5-turbo` 通過 `OpenAI` 官方 `API` 調用 `ChatGPT`
2. `ChatGPTUnofficialProxyAPI` 使用非官方代理伺服器訪問 `ChatGPT` 的後端`API`，繞過`Cloudflare`（依賴於協力廠商伺服器，並且有速率限制）

警告：
1. 你應該首先使用 `API` 方式
2. 使用 `API` 時，如果網路不通，那是國內被牆了，你需要自建代理，絕對不要使用別人的公開代理，那是危險的。
3. 使用 `accessToken` 方式時反向代理將向協力廠商暴露您的訪問權杖，這樣做應該不會產生任何不良影響，但在使用這種方法之前請考慮風險。
4. 使用 `accessToken` 時，不管你是國內還是國外的機器，都會使用代理。預設代理為 [pengzhile](https://github.com/pengzhile) 大佬的 `https://ai.fakeopen.com/api/conversation`，這不是後門也不是監聽，除非你有能力自己翻過 `CF` 驗證，用前請知悉。[社區代理](https://github.com/transitive-bullshit/chatgpt-api#reverse-proxy)（注意：只有這兩個是推薦，其他協力廠商來源，請自行甄別）
5. 把專案發佈到公共網路時，你應該設置 `AUTH_SECRET_KEY` 變數添加你的密碼存取權限，你也應該修改 `index.html` 中的 `title`，防止被關鍵字搜索到。

切換方式：
1. 進入 `service/.env.example` 檔，複製內容到 `service/.env` 文件
2. 使用 `OpenAI API Key` 請填寫 `OPENAI_API_KEY` 欄位 [(獲取 apiKey)](https://platform.openai.com/overview)
3. 使用 `Web API` 請填寫 `OPENAI_ACCESS_TOKEN` 欄位 [(獲取 accessToken)](https://chat.openai.com/api/auth/session)
4. 同時存在時以 `OpenAI API Key` 優先

環境變數：

全部參數變數請查看或[這裡](#環境變數)

```
/service/.env.example
```

## 待實現路線
[✓] 雙模型

[✓] 多會話儲存和上下文邏輯

[✓] 對代碼等消息類型的格式化美化處理

[✓] 存取權限控制

[✓] 數據導入、匯出

[✓] 保存消息到本地圖片

[✓] 介面多語言

[✓] 介面主題

[✗] More...

## 前置要求

### Node

`node` 需要 `^16 || ^18 || ^19` 版本（`node >= 14` 需要安裝 [fetch polyfill](https://github.com/developit/unfetch#usage-as-a-polyfill)），使用 [nvm](https://github.com/nvm-sh/nvm) 可管理本地多個 `node` 版本

```shell
node -v
```

### PNPM
如果你沒有安裝過 `pnpm`
```shell
npm install pnpm -g
```

### 填寫金鑰
獲取 `Openai Api Key` 或 `accessToken` 並填寫本地環境變數 [跳轉](#介紹)

```
# service/.env 文件

# OpenAI API Key - https://platform.openai.com/overview
OPENAI_API_KEY=

# change this to an `accessToken` extracted from the ChatGPT site's `https://chat.openai.com/api/auth/session` response
OPENAI_ACCESS_TOKEN=
```

## 安裝依賴

> 為了簡便 `後端開發人員` 的瞭解負擔，所以並沒有採用前端 `workspace` 模式，而是分資料夾存放。如果只需要前端頁面做二次開發，刪除 `service` 資料夾即可。

### 後端

進入資料夾 `/service` 運行以下命令

```shell
pnpm install
```

### 前端
根目錄下運行以下命令
```shell
pnpm bootstrap
```

## 測試環境運行
### 後端服務

進入資料夾 `/service` 運行以下命令

```shell
pnpm start
```

### 前端網頁
根目錄下運行以下命令
```shell
pnpm dev
```

## 環境變數

`API` 可用：

- `OPENAI_API_KEY` 和 `OPENAI_ACCESS_TOKEN` 二選一
- `OPENAI_API_MODEL`  設置模型，可選，預設：`gpt-3.5-turbo`
- `OPENAI_API_BASE_URL` 設置介面位址，可選，默認：`https://api.openai.com`
- `OPENAI_API_DISABLE_DEBUG` 設置介面關閉 debug 日誌，可選，預設：empty 不關閉

`ACCESS_TOKEN` 可用：

- `OPENAI_ACCESS_TOKEN`  和 `OPENAI_API_KEY` 二選一，同時存在時，`OPENAI_API_KEY` 優先
- `API_REVERSE_PROXY` 設置反向代理，可選，預設：`https://ai.fakeopen.com/api/conversation`，[社區](https://github.com/transitive-bullshit/chatgpt-api#reverse-proxy)（注意：只有這兩個是推薦，其他協力廠商來源，請自行甄別）

通用：

- `AUTH_SECRET_KEY` 存取權限金鑰，可選
- `MAX_REQUEST_PER_HOUR` 每小時最大請求次數，可選，默認無限
- `TIMEOUT_MS` 超時，單位毫秒，可選
- `SOCKS_PROXY_HOST` 和 `SOCKS_PROXY_PORT` 一起時生效，可選
- `SOCKS_PROXY_PORT` 和 `SOCKS_PROXY_HOST` 一起時生效，可選
- `HTTPS_PROXY` 支持 `http`，`https`, `socks5`，可選
- `ALL_PROXY` 支持 `http`，`https`, `socks5`，可選

## 打包

### 使用 Docker

#### Docker 參數示例

![docker](./docs/docker.png)

#### Docker build & Run

```bash
docker build -t chatgpt-web .

# 前臺運行
docker run --name chatgpt-web --rm -it -p 127.0.0.1:3002:3002 --env OPENAI_API_KEY=your_api_key chatgpt-web

# 後臺運行
docker run --name chatgpt-web -d -p 127.0.0.1:3002:3002 --env OPENAI_API_KEY=your_api_key chatgpt-web

# 運行位址
http://localhost:3002/
```

#### Docker compose

[Hub 地址](https://hub.docker.com/repository/docker/chenzhaoyu94/chatgpt-web/general)

```yml
version: '3'

services:
  app:
    image: chenzhaoyu94/chatgpt-web # 總是使用 latest ,更新時重新 pull 該 tag 鏡像即可
    ports:
      - 127.0.0.1:3002:3002
    environment:
      # 二選一
      OPENAI_API_KEY: sk-xxx
      # 二選一
      OPENAI_ACCESS_TOKEN: xxx
      # API介面位址，可選，設置 OPENAI_API_KEY 時可用
      OPENAI_API_BASE_URL: xxx
      # API模型，可選，設置 OPENAI_API_KEY 時可用，https://platform.openai.com/docs/models
      # gpt-4, gpt-4-0314, gpt-4-32k, gpt-4-32k-0314, gpt-3.5-turbo, gpt-3.5-turbo-0301, text-davinci-003, text-davinci-002, code-davinci-002
      OPENAI_API_MODEL: xxx
      # 反向代理，可選
      API_REVERSE_PROXY: xxx
      # 存取權限金鑰，可選
      AUTH_SECRET_KEY: xxx
      # 每小時最大請求次數，可選，默認無限
      MAX_REQUEST_PER_HOUR: 0
      # 超時，單位毫秒，可選
      TIMEOUT_MS: 60000
      # Socks代理，可選，和 SOCKS_PROXY_PORT 一起時生效
      SOCKS_PROXY_HOST: xxx
      # Socks代理埠，可選，和 SOCKS_PROXY_HOST 一起時生效
      SOCKS_PROXY_PORT: xxx
      # HTTPS 代理，可選，支援 http，https，socks5
      HTTPS_PROXY: http://xxx:7890
```
- `OPENAI_API_BASE_URL`  可選，設置 `OPENAI_API_KEY` 時可用
- `OPENAI_API_MODEL`  可選，設置 `OPENAI_API_KEY` 時可用

#### 防止爬蟲抓取

**nginx**

將下面配置填入nginx設定檔中，可以參考 `docker-compose/nginx/nginx.conf` 檔中添加反爬蟲的方法

```
    # 防止爬蟲抓取
    if ($http_user_agent ~* "360Spider|JikeSpider|Spider|spider|bot|Bot|2345Explorer|curl|wget|webZIP|qihoobot|Baiduspider|Googlebot|Googlebot-Mobile|Googlebot-Image|Mediapartners-Google|Adsbot-Google|Feedfetcher-Google|Yahoo! Slurp|Yahoo! Slurp China|YoudaoBot|Sosospider|Sogou spider|Sogou web spider|MSNBot|ia_archiver|Tomato Bot|NSPlayer|bingbot")
    {
      return 403;
    }
```

###  使用 Railway 部署

[![Deploy on Railway](https://railway.app/button.svg)](https://railway.app/new/template/yytmgc)

#### Railway 環境變數

| 環境變數名稱          | 必填                   | 備註                                                                                               |
| --------------------- | ---------------------- | -------------------------------------------------------------------------------------------------- |
| `PORT`                | 必填                   | 默認 `3002`
| `AUTH_SECRET_KEY`          | 可選                   | 存取權限金鑰                                        |
| `MAX_REQUEST_PER_HOUR`          | 可選                   | 每小時最大請求次數，可選，默認無限                                        |
| `TIMEOUT_MS`          | 可選                   | 超時時間，單位毫秒                                                                             |
| `OPENAI_API_KEY`      | `OpenAI API` 二選一    | 使用 `OpenAI API` 所需的 `apiKey` [(獲取 apiKey)](https://platform.openai.com/overview)            |
| `OPENAI_ACCESS_TOKEN` | `Web API` 二選一       | 使用 `Web API` 所需的 `accessToken` [(獲取 accessToken)](https://chat.openai.com/api/auth/session) |
| `OPENAI_API_BASE_URL`   | 可選，`OpenAI API` 時可用 |  `API`介面位址  |
| `OPENAI_API_MODEL`   | 可選，`OpenAI API` 時可用 |  `API`模型  |
| `API_REVERSE_PROXY`   | 可選，`Web API` 時可用 | `Web API` 反向代理位址 [詳情](https://github.com/transitive-bullshit/chatgpt-api#reverse-proxy)    |
| `SOCKS_PROXY_HOST`   | 可選，和 `SOCKS_PROXY_PORT` 一起時生效 | Socks代理    |
| `SOCKS_PROXY_PORT`   | 可選，和 `SOCKS_PROXY_HOST` 一起時生效 | Socks代理埠    |
| `SOCKS_PROXY_USERNAME`   | 可選，和 `SOCKS_PROXY_HOST` 一起時生效 | Socks代理用戶名    |
| `SOCKS_PROXY_PASSWORD`   | 可選，和 `SOCKS_PROXY_HOST` 一起時生效 | Socks代理密碼    |
| `HTTPS_PROXY`   | 可選 | HTTPS 代理，支援 http，https, socks5    |
| `ALL_PROXY`   | 可選 | 所有代理 代理，支援 http，https, socks5    |

> 注意: `Railway` 修改環境變數會重新 `Deploy`

### 手動打包
#### 後端服務
> 如果你不需要本專案的 `node` 介面，可以省略如下操作

複製 `service` 資料夾到你有 `node` 服務環境的伺服器上。

```shell
# 安裝
pnpm install

# 打包
pnpm build

# 運行
pnpm prod
```

PS: 不進行打包，直接在伺服器上運行 `pnpm start` 也可

#### 前端網頁

1、修改根目錄下 `.env` 檔中的 `VITE_GLOB_API_URL` 為你的實際後端介面位址

2、根目錄下運行以下命令，然後將 `dist` 資料夾內的檔複製到你網站服務的根目錄下

[參考資訊](https://cn.vitejs.dev/guide/static-deploy.html#building-the-app)

```shell
pnpm build
```

## 常見問題
Q: 為什麼 `Git` 提交總是報錯？

A: 因為有提交資訊驗證，請遵循 [Commit 指南](./CONTRIBUTING.md)

Q: 如果只使用前端頁面，在哪裡改請求介面？

A: 根目錄下 `.env` 文件中的 `VITE_GLOB_API_URL` 欄位。

Q: 檔保存時全部爆紅?

A: `vscode` 請安裝專案推薦外掛程式，或手動安裝 `Eslint` 外掛程式。

Q: 前端沒有打字機效果？

A: 一種可能原因是經過 Nginx 反向代理，開啟了 buffer，則 Nginx 會嘗試從後端緩衝一定大小的資料再發送給流覽器。請嘗試在反代參數後添加 `proxy_buffering off;`，然後重載 Nginx。其他 web server 配置同理。

## 參與貢獻

貢獻之前請先閱讀 [貢獻指南](./CONTRIBUTING.md)

感謝所有做過貢獻的人!

<a href="https://github.com/Chanzhaoyu/chatgpt-web/graphs/contributors">
  <img src="https://contrib.rocks/image?repo=Chanzhaoyu/chatgpt-web" />
</a>

## 贊助

如果你覺得這個項目對你有説明，並且情況允許的話，可以給我一點點支持，總之非常感謝支持～

<div style="display: flex; gap: 20px;">
	<div style="text-align: center">
		<img style="max-width: 100%" src="./docs/wechat.png" alt="微信" />
		<p>WeChat Pay</p>
	</div>
	<div style="text-align: center">
		<img style="max-width: 100%" src="./docs/alipay.png" alt="支付寶" />
		<p>Alipay</p>
	</div>
</div>

## License
MIT © [ChenZhaoYu](./license)
