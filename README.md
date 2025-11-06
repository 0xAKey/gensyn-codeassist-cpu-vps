# gensyn-codeassist-cpu-vps
Unofficial setup guide for running Gensyn CodeAssist on CPU-only VPS (Ubuntu 22.04+)

# 🧩 Gensyn CodeAssist — CPU-Only VPS Setup (Unofficial Enhanced Guide)

> 🧠 **Train your own AI coding assistant locally, sync with Hugging Face, and join the Gensyn Testnet — fully on CPU.**  
> Works on any Ubuntu 22.04+ VPS.

---

## ⚡ Overview

This repo walks you through a **complete one-click installation** of [Gensyn CodeAssist](https://github.com/gensyn-ai/codeassist) on a **CPU-only VPS** — no GPU required.  
Every session trains your local model and pushes reward updates to your Hugging Face account.

---

### 🧠 Requirements

| Component | Minimum |
|------------|----------|
| OS | Ubuntu 22.04 LTS (64-bit) |
| Python | ≥ 3.10 |
| RAM | 8 GB+ |
| Disk | 10 GB free |
| Internet | Stable connection |
| Hugging Face | Token with **Write** permission |

---

## 🪄 Quick Start

### ⚙️ 1️⃣ System Update & Dependencies
```bash
sudo apt update -y && sudo apt upgrade -y
sudo apt install -y git python3 python3-venv python3-pip curl ca-certificates gnupg lsb-release

🐋 2️⃣ Install Docker
sudo apt remove -y containerd containerd.io docker docker.io docker-ce docker-ce-cli || true
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" \
  | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo systemctl enable docker
sudo systemctl start docker
sudo docker run hello-world

🧠 3️⃣ Fix Docker Permissions
sudo usermod -aG docker $USER
newgrp docker

🧹 4️⃣ Full Docker Cleanup (optional)
sudo systemctl stop docker
sudo rm -rf /var/lib/docker
sudo systemctl start docker

🧰 5️⃣ Install CodeAssist
git clone https://github.com/gensyn-ai/codeassist.git
cd codeassist
python3 -m venv .venv
source .venv/bin/activate
pip install -U pip uv
uv sync || pip install -r requirements.txt

🔐 6️⃣ Add Hugging Face Credentials
Get your token 👉 https://huggingface.co/settings/tokens
Create one with Write permission.
export HF_TOKEN="hf_xxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
export HF_USER="your_huggingface_username"
echo 'export HF_TOKEN="hf_xxxxxxxxxxxxxxxxxxxxxxxxxxxxx"' >> ~/.bashrc
echo 'export HF_USER="your_huggingface_username"' >> ~/.bashrc

🧹 7️⃣ Port 3000 Quick Fix (Before Running)
sudo ss -ltnp | grep 3000 && echo "⚠️ Port 3000 occupied"
sudo kill -9 $(sudo ss -ltnp | grep 3000 | awk '{print $NF}' | sed 's/.*pid=//;s/,.*//') 2>/dev/null || true
sudo systemctl restart docker

If it still says ⚠️ Port 3000 occupied, manually:
sudo kill -9 2324546
sudo ss -ltnp | grep 3000 || echo "✅ Port 3000 now free"
sudo systemctl restart docker

🚀 8️⃣ Launch CodeAssist
cd ~/codeassist
source .venv/bin/activate
uv run run.py

Wait for:
<img width="505" height="114" alt="image" src="https://github.com/user-attachments/assets/cb6e2fbb-51cb-422c-a1dc-049ae504a1f8" />

🌐 9️⃣ Access the Web UI
If remote VPS (replace with your IP): (in your local terminal)
ssh -L 3000:localhost:3000 -L 8000:localhost:8000 -L 8001:localhost:8001 ubuntu@<your-vps-ip>

Then open
👉 http://localhost:3000


🚀 CodeAssist is fully running in your browser.

🧩 Common Issues & Fixes
Problem	Fix
❌ address already in use	Run the quick-fix port cleaner above
🐋 no container engine found	sudo systemctl start docker
🔄 OTP page not showing	Refresh UI or restart containers
🧾 Upload fails to HF	Generate a new token with write scope
⛔ SSH disconnects stop app	Use tmux new -s codeassist to keep it alive

🧠 Tips
Press Ctrl + C after coding to end a session → triggers model training + upload.
Your Hugging Face page will show checkpoints like
oxakey/codeassist-run-<timestamp>.

Edit instead of deleting the AI’s code instantly to give better feedback signals.

💚 Credits

Gensyn AI
 — for the original CodeAssist framework

Hugging Face
 — for model hosting and sync

Community contributors improving CPU deployments

⭐ Star this repo if it helped you.
🧵 Share your setup on X — tag @gensynai
 and @huggingface
!


