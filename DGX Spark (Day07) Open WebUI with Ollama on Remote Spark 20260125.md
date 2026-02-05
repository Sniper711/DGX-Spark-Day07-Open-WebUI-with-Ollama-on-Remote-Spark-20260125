<sub><sup>This is an extension of my previous article on the Server/Client connection method for DGX Spark: [Day05: REMOTE ACCESS - Mastering Tailscale to Easily Replace WireGuide+Termius](https://github.com/Sniper711/DGX-Spark-Day05-REMOTE-ACCESS-Mastering-Tailscale-to-Easily-Replace-WireGuide-and-Termius-20260116/blob/main/DGX%20Spark%20(Day05)%20REMOTE%20ACCESS%20-%20Mastering%20Tailscale%20to%20Easily%20Replace%20WireGuide%2BTermius%2020260116.md). Here, I'll adapt the official NVIDIA steps (which rely on NVIDIA SYNC app) for setting up Open WebUI on an NVIDIA DGX Spark, without using NVIDIA SYNC app connections. I hope this gives you more options for reference.</sup></sub>
# DGX Spark (Day07) Open WebUI with Ollama on Remote Spark 20260125
## 🟩 English
> ## Scenarios & Advantages
> **Mac/PC Client browser uses the Open WebUI interface → through the self-established remote connections → to run Ollama on DGX Spark Server**
> - **Based on the interconnection methods of DGX Spark: [Day05: REMOTE ACCESS - Mastering Tailscale to Easily Replace WireGuide+Termius](https://github.com/Sniper711/DGX-Spark-Day05-REMOTE-ACCESS-Mastering-Tailscale-to-Easily-Replace-WireGuide-and-Termius-20260116/blob/main/DGX%20Spark%20(Day05)%20REMOTE%20ACCESS%20-%20Mastering%20Tailscale%20to%20Easily%20Replace%20WireGuide%2BTermius%2020260116.md)**. 
>   - Guaranteed stability through the self-estabilished remote connections
>   - No reliance on NVIDIA SYNC
> - **Minor modifications to the NVIDIA official steps. Ensure Administrator Privileges for Enabling Advanced Ollama Applications** 
>   - The official steps are built around NVIDIA SYNC connections; only three steps need to be changed to match the self-established remote connections.
>   - The modified command in step 4-1 ensures that the logged-in user has administrator privileges, thereby allowing access to advanced Ollama applications, such as embedding ComfyUI image generation and video generation services in the background of Ollama text conversations, and more.
> - Simple one-line **SSH** command **login to DGX Spark**
>   - After rebooting, simply have the Mac/PC Client run `Step 4-3` and `Step 5` - it's super easy.

---

## Based on NVIDIA DGX Spark Official Steps of [Open WebUI with Ollama : Set up WebUI on Remote Spark with NVIDIA Sync](https://build.nvidia.com/spark/open-webui/sync)
On this page,
## Step 1. Configure Docker permissions
(no change)

## Step 2. Verify Docker setup and pull container
(no change)

---

## Step 3. Open NVIDIA SYNC Settings
(Don't do it - follow the modified step below instead)
## Modified Step 3. On Mac/PC Client, temporarily login to DGX Spark Server (The port number for Open WebUI has not been assigned yet)
On Mac/PC Client, open the Terminal app, run the following command:
###### After executing the following command, you will notice the terminal prompt changes — from your <client_user>@<client_Mac/PC_name>% on the Mac/PC Client to <server_user>@Spark-xxxx:$ on the DGX Spark Server. This indicates a successful login.
```
# Remove `<DGX Spark username>`, and replace it with the username used to log in after DGX Spark boots
# Remove `<192.168.x.x>`, and replace it with DGX Spark intranet IP address (192.168.x.x)
ssh <DGX Spark username>@<192.168.x.x>
```

---

## Step 4. Add Open WebUI custom port configuration
(Don't do it - follow the modified step below instead)
## Modified Step 4. Add Open WebUI custom port configuration 
### Modified Step 4-1. Add Open WebUI custom port configuration
On Mac/PC Client, continue on the Terminal app, run the following command:
```
docker run -d \
  --gpus all \
  -p 3000:8080 \
  -e WEBUI_ADMIN_EMAIL=<admin_email_address> \ # Note: Replace the entire <admin_email_address> (including the angle brackets) with the email address you will use to log in to Ollama, to ensure that this login has administrator privileges.
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
  - **-p 3000:8080** Publish (map) port 8080 inside the container to port 3000 on the DGX Spark host.
(*Note: The 3000 port on DGX Spark can be changed.)
  - **-e WEBUI_ADMIN_EMAIL=<admin_email_address>** Replace the entire <admin_email_address> (including the angle brackets) with the email address you will use to log in to Ollama, to ensure that this login has administrator privileges, thereby enabling access to advanced Ollama applications, such as embedding ComfyUI image generation and video generation services in the background of Ollama text conversations, and more.
  - **-v ollama:/root/.ollama** Mount the Docker named volume `ollama` stored on the DGX Spark host (typically under /var/lib/docker/volumes/ollama/_data) to `/root/.ollama` inside the container.
  - **-v open-webui:/app/backend/data** Mount the Docker named volume `open-webui` stored on the DGX Spark host (typically under /var/lib/docker/volumes/open-webui/_data) to `/app/backend/data` inside the container.
  - **--name open-webui** Name the container open-webui.
  - **--restart unless-stopped** Automatically restart the container on system boot (or after crashes), unless it was explicitly stopped with docker stop.
(*Note: Can be changed to --restart always to always restart regardless of how it was stopped.)
  - **ghcr.io/open-webui/open-webui:ollama** Use the Docker image ghcr.io/open-webui/open-webui:ollama to create the container.

### Modified Step 4-2. On Mac/PC Client, log out from the temporary DGX Spark Server session established in Step 3 (no Open WebUI communication port in Client site was specified yet)
On Mac/PC Client, continue on the Terminal app, run the following command:
###### After executing the command, you will notice the terminal prompt changes — from the DGX Spark Server's format (e.g., <server-user>@Spark-xxxx:$) back to your local Mac/PC Client's prompt (e.g., <local-user>@<local-machine>%). This indicates a successful logout.
```
exit
```

### Modified Step 4-3. On Mac/PC Client, re-login to DGX Spark Server (this time specifying the Open WebUI communication port as 12000 in Client site)
On Mac/PC Client, open the Terminal app, run the following command:
```
# Remove `<DGX Spark username>`, and replace it with the username used to log in after DGX Spark boots
# Remove `<192.168.x.x>`, and replace it with DGX Spark intranet IP address (192.168.x.x)
ssh -4 -N -L 12000:0.0.0.0:3000 <DGX Spark username>@<192.168.x.x>
```
<sub><sup>＊After rebooting, simply have the Mac/PC Client run `Step 4-3` and `Step 5` - it's super easy.</sup></sub>

---

## Step 5. Launch Open WebUI
(no change)

<sub><sup>＊After rebooting, simply have the Mac/PC Client run `Step 4-3` and `Step 5` - it's super easy.</sup></sub>

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
