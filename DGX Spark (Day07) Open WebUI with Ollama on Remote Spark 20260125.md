<sub><sup>This is an extension of my previous article on the Server/Client connection method for DGX Spark: [Day05: REMOTE ACCESS - Mastering Tailscale to Easily Replace WireGuide+Termius](https://github.com/Sniper711/DGX-Spark-Day05-REMOTE-ACCESS-Mastering-Tailscale-to-Easily-Replace-WireGuide-and-Termius-20260116/blob/main/DGX%20Spark%20(Day05)%20REMOTE%20ACCESS%20-%20Mastering%20Tailscale%20to%20Easily%20Replace%20WireGuide%2BTermius%2020260116.md). Here, I'll adapt the official NVIDIA steps (which rely on NVIDIA SYNC app) for setting up Open WebUI on an NVIDIA DGX Spark, without using NVIDIA SYNC app connections. I hope this gives you more options for reference.</sup></sub>
# DGX Spark (Day07) Open WebUI with Ollama on Remote Spark 20260125
## 🟩 English
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


---

## Based on NVIDIA DGX Spark Official Steps of [Open WebUI with Ollama : Set up WebUI on Remote Spark with NVIDIA Sync](https://build.nvidia.com/spark/open-webui/sync)
On this page,
## Step 1. Configure Docker permissions
## Step 2. Verify Docker setup and pull container
(no change for Step 1~2)

---

## Step 3. Open NVIDIA SYNC Settings
(Don't do it)

---

## Step 4. Add Open WebUI custom port configuration
(Don't do it - follow the modified step below instead)
## Modified Step 4. Add Open WebUI custom port configuration 
### Modified Step 4-1. Add Open WebUI custom port configuration
- On DGX Spark terminal, run command:
  ```
  docker run -d \
    --gpus all \
    -p 3000:8080 \
    # Note: Replace the entire <admin_email_address> (including the angle brackets) hereunder, with the email address you will use to log in to Ollama, to ensure that this login has administrator privileges.
    -e WEBUI_ADMIN_EMAIL=<admin_email_address> \ 
    -v ollama:/root/.ollama \
    -v open-webui:/app/backend/data \
    --name open-webui \
    --restart unless-stopped \
    ghcr.io/open-webui/open-webui:ollama
  ```
  - Command explainations
    - **docker** Docker command
    - **run -d** Run the container in detached mode (in the background, without showing logs in the terminal).
    - **--gpus all** Grant the container access to all NVIDIA GPUs on the DGX Spark host.
    - **-p 3000:8080** Publish (map) port 8080 inside the container to port 3000 on the DGX Spark host. (* Note: The 3000 port on DGX Spark can be changed.)
    - **-e WEBUI_ADMIN_EMAIL=<admin_email_address>** Replace the entire <admin_email_address> (including the angle brackets) **with the email address you will use to log in to Ollama, to ensure that this login has administrator privileges**, thereby enabling access to advanced Ollama applications, such as embedding ComfyUI image generation and video generation services in the background of Ollama text conversations, and more.
    - **-v ollama:/root/.ollama** Mount the Docker named volume `ollama` stored on the DGX Spark host (typically under /var/lib/docker/volumes/ollama/_data) to `/root/.ollama` inside the container.
    - **-v open-webui:/app/backend/data** Mount the Docker named volume `open-webui` stored on the DGX Spark host (typically under /var/lib/docker/volumes/open-webui/_data) to `/app/backend/data` inside the container.
    - **--name open-webui** Name the container open-webui.
    - **--restart unless-stopped** Automatically restart the container on system boot (or after crashes), unless it was explicitly stopped with docker stop.
(*Note: Can be changed to --restart always to always restart regardless of how it was stopped.)
    - **ghcr.io/open-webui/open-webui:ollama** Use the Docker image ghcr.io/open-webui/open-webui:ollama to create the container.

- On DGX Spark terminal, run command:
  ```
  exit
  ```

### Modified Step 4-2. MAC/PC/Tablet/Phone Client launches Tailscale VPN, to be in the same virtual intranet IP 100.x.x.x environment as DGX Spark.
**Important⚠️：Prior to `Step 4-2`, please finish the setup of article [DGX Spark (Day05) REMOTE ACCESS - Mastering Tailscale to Easily Replace WireGuard+Termius 20260116 🟩 English](https://github.com/Sniper711/DGX-Spark-Day05-REMOTE-ACCESS-Mastering-Tailscale-to-Easily-Replace-WireGuide-and-Termius-20260116/blob/main/DGX%20Spark%20(Day05)%20REMOTE%20ACCESS%20-%20Mastering%20Tailscale%20to%20Easily%20Replace%20WireGuide%2BTermius%2020260116.md)**
- If you like to use Ollama locally on DGX Spark:
  - On DGX Spark Server
    - No extra step needs
- If you like to remotely operate DGX Spark Server's Ollama service from Mac/PC/Tablet/Phone Client:
  - On MAC/PC/Tablet/Phone Client
    - Launches Tailscale VPN, to be in the same virtual intranet IP 100.x.x.x environment as DGX Spark Server.
    - **Find out the DGX Spark Server Tailscale IP `100.a.b.c`**

  <sub><sup>＊After rebooting, to use Ollama locally on DGX Spark, just run `Step 5` - it's super easy.</sup></sub>

  <sub><sup>＊After rebooting, to remotely operate DGX Spark Server's Ollama service from Mac/PC/Tablet/Phone Client, just run `Step 4-2` and `Step 5` — it's super easy.</sup></sub>

---

## Step 5. Launch Open WebUI
(Don't do it - follow the modified step below instead)
- If you like to use Ollama locally on DGX Spark:
  - On DGX Spark browser
    - Use `http://localhost:12000` to connect Ollama locally.
- If you want to remotely operate DGX Spark Server's Ollama service:
  - On MAC/PC/Tablet/Phone Client
    - use `http://100.a.b.c:12000` to connect DGX Spark Server's Ollama service remotely.
    - `100.a.b.c` is the DGX Spark Server's Tailscale IP on `Step 4-2`.

  <sub><sup>＊After rebooting, to use Ollama locally on DGX Spark, just run `Step 5` - it's super easy.</sup></sub>

  <sub><sup>＊After rebooting, to remotely operate DGX Spark Server's Ollama service from Mac/PC/Tablet/Phone Client, just run `Step 4-2` and `Step 5` — it's super easy.</sup></sub>

---

## Step 6. Create administrator account
(no change) (However, the email address used to create the account **must be the same as the email address specified in the Step 4-1 command** in order to obtain admin privileges.) (This is very important, especially for future advanced setups where ComfyUI image generation is invoked directly from within the Ollama text chat interface.)

---

## Step 7. Download and configure a model
(no change)

---

## Step 8. Test the model
(no change)

---

## That's it! All set and successful!!

---

## Step 9. Stop the Open WebUI
(Don't do it - follow the modified step below instead)
## Modified Step 9. Stop the Open WebUI
In the terminal window where you ran Step 4-3 (the SSH tunnel command), press `Ctrl+C` to exit.

*This will terminate the SSH tunnel, stopping the local port forwarding and closing access to the Open WebUI port on the DGX Spark server.

---

## Step 10. Next steps
(no change)

---

## Step 11. Cleanup and rollback
(no change) **Caution: DO NOT casually cleanup and rollback**, and the following commands should be run directly on the DGX Spark Server (not on your Mac/PC client).

---

# Congratulations - Now you can run Ollama via your Mac/PC browser — powered by DGX Spark's GPU!
<sub><sup>* After rebooting, simply have the Mac/PC Client run `Step 4-3` and `Step 5` - it's super easy.</sup></sub>

---
