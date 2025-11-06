# 🚀 Gensyn CodeAssist — CPU VPS Setup (Unofficial Guide by 0xAkey)

![banner](https://github.com/user-attachments/assets/placeholder-banner-codeassist-cpu.jpg)

<p align="center">
  <a href="https://github.com/gensyn-ai/codeassist">
    <img src="https://img.shields.io/badge/Base-Gensyn%20CodeAssist-7B3FE4?style=for-the-badge&logo=gensyn&logoColor=white" alt="Base Project">
  </a>
  <a href="https://hub.docker.com/">
    <img src="https://img.shields.io/badge/Docker-Ready-2496ED?style=for-the-badge&logo=docker&logoColor=white" alt="Docker Ready">
  </a>
  <img src="https://img.shields.io/badge/Build-CPU%20Only-orange?style=for-the-badge" alt="CPU Build">
  <img src="https://img.shields.io/badge/Integration-HuggingFace-yellow?style=for-the-badge&logo=huggingface" alt="HuggingFace">
  <img src="https://img.shields.io/badge/Guide-Unofficial-red?style=for-the-badge" alt="Unofficial Guide">
</p>

---

## 🧠 About

> This is a fully functional **CPU-only setup guide** for [Gensyn CodeAssist](https://gensyn.ai),  
> tested on Ubuntu 22.04 VPS instances with zero GPU dependencies.  
> CodeAssist is an AI coding assistant that trains on your local workflow —  
> learning your reasoning style and evolving through interaction.

---

## ⚙️ 1️⃣ System Update & Dependencies

```bash
sudo apt update -y && sudo apt upgrade -y
sudo apt install -y git python3 python3-venv python3-pip curl ca-certificates gnupg lsb-release
```

---

## 🐋 2️⃣ Install Docker

Remove any old installations:

```bash
sudo apt remove -y containerd containerd.io docker docker.io docker-ce docker-ce-cli || true
```

Add the official Docker repository:

```bash
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo   "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg]   https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"   | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

Install Docker:

```bash
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo systemctl enable docker
sudo systemctl start docker
sudo docker run hello-world
```

---

## 🧠 3️⃣ Fix Docker Permissions

```bash
sudo usermod -aG docker $USER
newgrp docker
```

---

## 🧹 4️⃣ Full Docker Cleanup

If you previously had Docker containers or ran an old CodeAssist instance:

```bash
sudo systemctl stop docker
sudo rm -rf /var/lib/docker
sudo systemctl start docker
```

---

## 🧰 5️⃣ Install CodeAssist

```bash
git clone https://github.com/gensyn-ai/codeassist.git
cd codeassist
python3 -m venv .venv
source .venv/bin/activate
pip install -U pip uv
uv sync || pip install -r requirements.txt
```

---

## 🔐 6️⃣ Add Hugging Face Credentials

Generate a token at 👉 [https://huggingface.co/settings/tokens](https://huggingface.co/settings/tokens)  
→ Select “**Write**” permission.  

Then export your credentials:

```bash
export HF_TOKEN="your_huggingface_token"
export HF_USER="your_huggingface_username"
```

(Optional — make it permanent):

```bash
echo 'export HF_TOKEN="your_huggingface_token"' >> ~/.bashrc
echo 'export HF_USER="your_huggingface_username"' >> ~/.bashrc
```

---

## 🧩 7️⃣ Port 3000 Quick Fix (Before Running)

Make sure port **3000** isn’t locked:

```bash
sudo ss -ltnp | grep 3000 && echo "⚠️ Port 3000 occupied"
sudo kill -9 $(sudo ss -ltnp | grep 3000 | awk '{print $NF}' | sed 's/.*pid=//;s/,.*//') 2>/dev/null || true
sudo systemctl restart docker
```

If it still shows `⚠️ Port 3000 occupied`, run manually:

```bash
sudo kill -9 2324546
sudo ss -ltnp | grep 3000 || echo "✅ Port 3000 now free"
sudo systemctl restart docker
```

---

## 🧭 8️⃣ Launch CodeAssist

```bash
cd ~/codeassist
source .venv/bin/activate
uv run run.py
```

Wait for:

```
CodeAssist started at http://localhost:3000
```

---

## 🌐 9️⃣ Access the Web UI

If running on a remote VPS, forward the ports with SSH:

```bash
ssh -L 3000:localhost:3000 -L 8000:localhost:8000 -L 8001:localhost:8001 ubuntu@<your-vps-ip>
```

Then open:
👉 [http://localhost:3000](http://localhost:3000)

---

## 📚 Additional Resources

- 📘 [Official Docs](https://docs.gensyn.ai/testnet/codeassist/getting-started)
- 🧩 [GitHub Repo](https://github.com/gensyn-ai/codeassist)
- 🧠 [Gensyn Blog Post](https://blog.gensyn.ai/introducing-codeassist/)
- 🪄 [Leaderboard](https://dashboard.gensyn.ai)

---

## 💬 Credits

Created by **[@0xAkey](https://github.com/0xAkey)**  
Made by [@0xAkey on X](https://x.com/0xAkey) 💫  
Based on the official [Gensyn CodeAssist](https://github.com/gensyn-ai/codeassist) repository.  
This version focuses on **CPU VPS deployment and HuggingFace-linked operation**.

---
