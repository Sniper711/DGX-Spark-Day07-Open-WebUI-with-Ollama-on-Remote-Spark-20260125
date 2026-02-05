<sub><sup>這是我前面文章 DGX Spark : [第05天: 遠端操作 - 學會用 Tailscale 輕鬆取代 WireGuard+Termius](https://github.com/Sniper711/DGX-Spark-Day05-REMOTE-ACCESS-Mastering-Tailscale-to-Easily-Replace-WireGuide-and-Termius-20260116/blob/main/DGX%20Spark%20(%E7%AC%AC05%E5%A4%A9)%20%E9%81%A0%E7%AB%AF%E6%93%8D%E4%BD%9C%20-%20%E5%AD%B8%E6%9C%83%E7%94%A8%20Tailscale%20%E8%BC%95%E9%AC%86%E5%8F%96%E4%BB%A3%20WireGuard%2BTermius%2020260116.md) 建立 Server/Client 連線方式的延伸文章。以下，我將在不使用 NVIDIA SYNC app 做連線的前提，修改 DGX Spark 建立 Open WebUI 的 NVIDIA官方步驟。希望能給你更多方式參考。</sup></sub>

# DGX Spark (第07天) 用 Open WebUI 介面 遠端操作 DGX Spark 上的 Ollama 20260125
## 🟩 中文版
> ## 適用情境 與 優點
> **Mac/PC Client 開瀏覽器在 Open WebUI 介面上 → 透過自己建立的遠端連線 → 用 DGX Spark Server 的算力跑 Ollama**
> - **基於前面文章 [第05天: 遠端操作 - 學會用 Tailscale 輕鬆取代 WireGuard+Termius](https://github.com/Sniper711/DGX-Spark-Day05-REMOTE-ACCESS-Mastering-Tailscale-to-Easily-Replace-WireGuide-and-Termius-20260116/blob/main/DGX%20Spark%20(%E7%AC%AC05%E5%A4%A9)%20%E9%81%A0%E7%AB%AF%E6%93%8D%E4%BD%9C%20-%20%E5%AD%B8%E6%9C%83%E7%94%A8%20Tailscale%20%E8%BC%95%E9%AC%86%E5%8F%96%E4%BB%A3%20WireGuard%2BTermius%2020260116.md) 建立 Server/Client 的連線方式**
>   - **100% 連線成功率與穩定度，自己掌握 Server/Client 連線的設定細節**
>   - 不使用 NVIDIA SYNC app 的連線方式
> - **小修改 NVIDIA官方步驟，確保擁有管理者權限能打開 Ollama 更高階應用** 
>   - 官方步驟是基於 NVIDIA SYNC app 連線的，只修改三個步驟就能匹配 自己建立的遠端連線
>   - 修改的 `Step 4-1` 指令，能確保這個登入者擁有管理者身份，從而能打開 Ollama 更高階應用，例如在 Ollama 文字對話背景嵌入 ComfyUI 生圖與生影片服務等等。
> - **既能 DGX Spark 本機使用 Ollama，也能 Tablet/Phone 或 MAC/PC 遠端操作 DGX Spark 的 Ollama 服務**
>   - 重開機之後，在 DGX Spark 本機使用 Ollama，只要執行 `Step 5`，超級簡單
>   - 重開機之後，在 Tablet/Phone 或 MAC/PC 遠端操作 DGX Spark 的 Ollama 服務，只要執行 `Step 4-2` 與 `Step 5`，超級簡單

---

## 打開 NVIDIA DGX Spark 網頁 [Open WebUI with Ollama : Set up WebUI on Remote Spark with NVIDIA Sync](https://build.nvidia.com/spark/open-webui/sync)
網頁中
## Step 1. 配置 Docker 權限
## Step 2. 驗證 Docker 設定，並拉取容器
(步驟1~2不變)(以上均在 DGX Spark 上執行)
## Step 3. 打開 NVIDIA SYNC 軟體的設定畫面
(不要做)

## Step 4. 新增 Open WebUI 自訂埠配置
(不要做)(改成以下步驟)


## 改為 Step 4. 新增 Open WebUI 自訂埠配置 

### 改為 Step 4-1. 新增 Open WebUI 自訂埠配置
- 在 DGX Spark Server 的終端機上，執行：
  ```
  docker run -d \
    --gpus all \
    -p 3000:8080 \
  # 注意：把下方整個<admin_email_address>包括括弧，替換成 將來Ollama登入 用的 email address，以確保這個登入者擁有管理者身份，從而能打開 Ollama 更高階應用，例如在 Ollama 文字對話背景嵌入 ComfyUI 生圖與生影片服務等等。
    -e WEBUI_ADMIN_EMAIL=<admin_email_address> \ 
    -v ollama:/root/.ollama \
    -v open-webui:/app/backend/data \
    --name open-webui \
    --restart unless-stopped \
    ghcr.io/open-webui/open-webui:ollama
  ```
  - 解釋指令
    - **docker** 用 docker 指令
    - **run -d** 跑 containner 但別在terminal上顯示
    - **--gpus all** 用 NVIDIA DGX Spark 的 GPU 高速運算
    - **-e WEBUI_ADMIN_EMAIL=<admin_email_address>** 把整個<admin_email_address>包括括弧，替換成 將來Ollama登入 用的 email address，以確保這個登入者擁有管理者身份，從而能打開 Ollama 更高階應用，例如在 Ollama 文字對話背景嵌入 ComfyUI 生圖與生影片服務等等。
    - **-p 3000:8080** 把實體 DGX Spark 的 3000 port 對應到 虛擬容器 container 的 8080 port (*註：DGX Spark 的 3000 port 這數字可以修改)
    - **-v ollama:/root/.ollama** 把實體 DGX Spark 的 ollama 目錄 掛載到 虛擬容器 container 的 /root/.ollama 目錄 (*註：實體 DGX Spark 目錄通常在 /var/lib/docker/volumes/...之下)
    - **-v open-webui:/app/backend/data** 把實體 DGX Spark 的 open-webui 目錄 掛載到 虛擬容器 container 的 /app/backend/data 目錄 (*註：實體 DGX Spark 目錄通常在 /var/lib/docker/volumes/...之下)
    - **--name open-webui** 命名容器為 open-webui
    - **--restart unless-stopped** 預設開機後自動啟動 container。但若關機前刻意下 docker stop 指令停止 container，則下次開機後不再自動啟動 container。(*註：亦可改成--restart always. 永遠自動啟動)
    - **ghcr.io/open-webui/open-webui:ollama** 使用 Docker image ghcr.io/open-webui/open-webui:ollama 來運行容器

- 接著在 DGX Spark Server 的終端機上，繼續執行：
  ```
  exit
  ```

### 改為 Step 4-2. MAC/PC/Tablet/Phone Client 啟動 Tailscale VPN，進入與 DGX Spark 相同的 Tailscale VPN 虛擬內網 IP 100.x.x.x 環境
**重要⚠️：先確定你已經完成文章 [DGX Spark (第05天) 遠端操作 - 學會用 Tailscale 輕鬆取代 WireGuard+Termius 20260116 🟩 中文版](https://github.com/Sniper711/DGX-Spark-Day05-REMOTE-ACCESS-Mastering-Tailscale-to-Easily-Replace-WireGuide-and-Termius-20260116/blob/main/DGX%20Spark%20(%E7%AC%AC05%E5%A4%A9)%20%E9%81%A0%E7%AB%AF%E6%93%8D%E4%BD%9C%20-%20%E5%AD%B8%E6%9C%83%E7%94%A8%20Tailscale%20%E8%BC%95%E9%AC%86%E5%8F%96%E4%BB%A3%20WireGuard%2BTermius%2020260116.md) 的安裝步驟**
- 在 DGX Spark Server 上，若你要在 DGX Spark 本機使用 Ollama：
  - 不需要此步驟。
- 在 MAC/PC/Tablet/Phone Client 上，若你要在 MAC/PC/Tablet/Phone Client 遠端操作 DGX Spark Server 的 Olllama 服務：
  - 啟動 Tailscale APP，讓 MAC/PC/Tablet/Phone Client 進入與 DGX Spark 相同的 Tailscale VPN 虛擬內網 IP 100.x.x.x 環境。
  - 紀錄 「DGX Spark 在 Tailscale VPN 虛擬內網的 IP 位置」 100.a.b.c 
  <sub><sup>＊重開機之後，若要在 DGX Spark 本機使用 Ollama，只要執行 `Step 5`，超級簡單。</sup></sub>
  <sub><sup>＊重開機之後，若要在 Tablet/Phone/MAC/PC Client 遠端操作 DGX Spark Server 的 Ollama 服務，只要執行 `Step 4-2` 與 `Step 5`，超級簡單。</sup></sub>
---

## Step 5. 啟動 Open WebUI
(不要做)(改成以下步驟)
- 在 DGX Spark Server 上，若你要在 DGX Spark 本機使用 Ollama：
  - 在 DGX Spark 用 `http://localhost:12000` 網址，本機連上 Ollama.
- 在 MAC/PC/Tablet/Phone Client 上，若你要在 MAC/PC/Tablet/Phone Client 遠端操作 DGX Spark Server 的 Olllama 服務：
  - 在 MAC/PC/Tablet/Phone Client 用 `http://100.a.b.c:12000` 網址，遠端連上 DGX Spark 的 Ollama 服務。
  - 其中，`100.a.b.c` 是在步驟 `Step 4-2` 紀錄的 「DGX Spark 在 Tailscale VPN 虛擬內網的 IP 位置」
    <sub><sup>＊重開機之後，在 DGX Spark 本機使用 Ollama，只要執行 `Step 5`，超級簡單。</sup></sub>
    <sub><sup>＊重開機之後，在 Tablet/Phone 或 MAC/PC 遠端操作 DGX Spark 的 Ollama 服務，只要執行 `Step 4-2` 與 `Step 5`，超級簡單。</sup></sub>

---

## Step 6. 創建管理員帳戶
(步驟不變)(但是創建帳戶的email必須與步驟4-1指令內的email address相同，才能擁有admin權限)(這很重要，尤其將來做Ollama文字對話框內直接呼叫ComfyUI生圖的進階做法時需要)

---

## Step 7. 下載並配置模型
(步驟不變)

---

## Step 8. 測試模型
(步驟不變)

---

## 至此，大功告成！！！

---

## Step 9. 停止 Open WebUI
(不要做)(改成以下步驟)
## 改為 Step 9. 停止 Open WebUI
在 Step 4-3 的終端機機畫面按 `Ctrl+C` 退出

*這將終止 SSH 隧道，停止本地埠轉發，並關閉對 DGX Spark 伺服器上 Open WebUI 埠的存取。

--

## Step 10. 下一步
(步驟不變)

---

## Step 11. 清除與還原
(步驟不變) **警告：不可隨意清除與還原**，且 Step 11 是在 DGX Spark Server 上執行的命令.

---

# **恭喜你！從此你能在 Mac/PC，用 DGX Spark 的 GPU 算力，開網頁跑 Ollama 了！**
<sub><sup>＊重開機之後，只要 Mac/PC (Client) 執行 `Step 4-3` 與 `Step 5`，超級簡單。</sup></sub>

---
