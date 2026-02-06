<sub><sup>This is an extension of my previous article on the Server/Client connection method for DGX Spark: [Day05: REMOTE ACCESS - Mastering Tailscale to Easily Replace WireGuide+Termius](https://github.com/Sniper711/DGX-Spark-Day05-REMOTE-ACCESS-Mastering-Tailscale-to-Easily-Replace-WireGuide-and-Termius-20260116/blob/main/DGX%20Spark%20(Day05)%20REMOTE%20ACCESS%20-%20Mastering%20Tailscale%20to%20Easily%20Replace%20WireGuide%2BTermius%2020260116.md). Here, I'll adapt the official NVIDIA steps (which rely on NVIDIA SYNC app) for setting up Open WebUI on an NVIDIA DGX Spark, without using NVIDIA SYNC app connections. I hope this gives you more options for reference.</sup></sub>

<sub><sup>這是我前面文章 DGX Spark : [第05天: 遠端操作 - 學會用 Tailscale 輕鬆取代 WireGuard+Termius](https://github.com/Sniper711/DGX-Spark-Day05-REMOTE-ACCESS-Mastering-Tailscale-to-Easily-Replace-WireGuide-and-Termius-20260116/blob/main/DGX%20Spark%20(%E7%AC%AC05%E5%A4%A9)%20%E9%81%A0%E7%AB%AF%E6%93%8D%E4%BD%9C%20-%20%E5%AD%B8%E6%9C%83%E7%94%A8%20Tailscale%20%E8%BC%95%E9%AC%86%E5%8F%96%E4%BB%A3%20WireGuard%2BTermius%2020260116.md) 建立 Server/Client 連線方式的延伸文章。以下，我將在不使用 NVIDIA SYNC app 做連線的前提，修改 DGX Spark 建立 Open WebUI 的 NVIDIA官方步驟。希望能給你更多方式參考。</sup></sub>
# DGX Spark (Day07) Open WebUI with Ollama on Remote Spark 20260125 🟩 [English](https://github.com/Sniper711/DGX-Spark-Day07-Open-WebUI-with-Ollama-on-Remote-Spark-20260125/blob/main/DGX%20Spark%20(Day07)%20Open%20WebUI%20with%20Ollama%20on%20Remote%20Spark%2020260125.md)
# DGX Spark (第07天) 用 Open WebUI 介面 遠端操作 DGX Spark 上的 Ollama 20260125 🟩 [中文版](https://github.com/Sniper711/DGX-Spark-Day07-Open-WebUI-with-Ollama-on-Remote-Spark-20260125/blob/main/DGX%20Spark%20(%E7%AC%AC07%E5%A4%A9)%20%E7%94%A8%20Open%20WebUI%20%E4%BB%8B%E9%9D%A2%20%E9%81%A0%E7%AB%AF%E6%93%8D%E4%BD%9C%20DGX%20Spark%20%E4%B8%8A%E7%9A%84%20Ollama%2020260125.md)


> ## Scenarios & Advantages
> **Mac/PC/Tablet/Phone Client browser uses the Open WebUI interface → through the self-established remote connections → to run Ollama on DGX Spark Server**
> - **Based on the interconnection methods of DGX Spark: [Day05: REMOTE ACCESS - Mastering Tailscale to Easily Replace WireGuide+Termius](https://github.com/Sniper711/DGX-Spark-Day05-REMOTE-ACCESS-Mastering-Tailscale-to-Easily-Replace-WireGuide-and-Termius-20260116/blob/main/DGX%20Spark%20(Day05)%20REMOTE%20ACCESS%20-%20Mastering%20Tailscale%20to%20Easily%20Replace%20WireGuide%2BTermius%2020260116.md)**. 
>   - Guaranteed stability through the self-estabilished remote connections
>   - No reliance on NVIDIA SYNC
> - **Minor modifications to the NVIDIA official steps. Ensure Administrator Privileges for Enabling Advanced Ollama Applications** 
>   - The official steps are built around NVIDIA SYNC connections; only three steps need to be changed to match the self-established remote connections.
>   - The modified command in `step 4-1` ensures that the logged-in user has administrator privileges, thereby allowing access to advanced Ollama applications, such as embedding ComfyUI image generation and video generation services in the background of Ollama text conversations, and more.
> - Can use Ollama **locally** on DGX Spark, and also **remotelly** operate DGX Spark Server's Ollama service from MAC/PC/Tablet/Phone Client.
>   - After rebooting, to use Ollama **locally** on DGX Spark, just run `Step 5` - it's super easy.
>   - After rebooting, to **remotely** operate DGX Spark Server's Ollama service from Mac/PC/Tablet/Phone Client, just run `Step 4-2` and `Step 5` — it's super easy.
### Congratulations - Now you can run Ollama via your Mac/PC/Tablet/Phone browser — powered by DGX Spark's GPU!
<br>

> ## 適用情境 與 優點
> **電腦/平板/手機 Client 開瀏覽器在 Open WebUI 介面上 → 透過自己建立的遠端連線 → 用 DGX Spark Server 的算力跑 Ollama**
> - **基於前面文章 [第05天: 遠端操作 - 學會用 Tailscale 輕鬆取代 WireGuard+Termius](https://github.com/Sniper711/DGX-Spark-Day05-REMOTE-ACCESS-Mastering-Tailscale-to-Easily-Replace-WireGuide-and-Termius-20260116/blob/main/DGX%20Spark%20(%E7%AC%AC05%E5%A4%A9)%20%E9%81%A0%E7%AB%AF%E6%93%8D%E4%BD%9C%20-%20%E5%AD%B8%E6%9C%83%E7%94%A8%20Tailscale%20%E8%BC%95%E9%AC%86%E5%8F%96%E4%BB%A3%20WireGuard%2BTermius%2020260116.md) 建立 Server/Client 的連線方式**
>   - **100% 連線成功率與穩定度，自己掌握 Server/Client 連線的設定細節**
>   - 不使用 NVIDIA SYNC app 的連線方式
> - **小修改 NVIDIA官方步驟，確保擁有管理者權限能打開 Ollama 更高階應用** 
>   - 官方步驟是基於 NVIDIA SYNC app 連線的，只修改三個步驟就能匹配 自己建立的遠端連線
>   - 修改的 `Step 4-1` 指令，能確保這個登入者擁有管理者身份，從而能打開 Ollama 更高階應用，例如在 Ollama 文字對話背景嵌入 ComfyUI 生圖與生影片服務等等。
> - **既能 DGX Spark 本機使用 Ollama，也能在 電腦/平板/手機 Client 遠端操作 DGX Spark Server 的 Ollama 服務**
>   - 重開機之後，在 DGX Spark **本機使用** Ollama，只要執行 `Step 5`，超級簡單
>   - 重開機之後，在 電腦/平板/手機 Client **遠端操作** DGX Spark Server 的 Ollama 服務，只要執行 `Step 4-2` 與 `Step 5`，超級簡單
### 恭喜你！從此你能在 電腦/平板/手機，用 DGX Spark 的 GPU 算力，開網頁跑 Ollama 了！
<br>

---

## 喜歡這個專案嗎？ 如果對您有幫助，請給一個 ★ Star 吧！這對我非常重要！

## If you find this project helpful, please give it a Star ★! Your support means a lot to me!

---
Davis Lin (Sniper711) .
Unauthorized article copying, distribution, or modification is prohibited.
