## 1. Update your Ubuntu VM

```bash
sudo apt update && sudo apt upgrade -y
```

---

## 2. Install Docker (official method, not the outdated apt one)

```bash
# Remove old versions if any
sudo apt remove docker docker-engine docker.io containerd runc -y

# Install dependencies
sudo apt install ca-certificates curl gnupg -y

# Add Docker GPG key
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo tee /etc/apt/keyrings/docker.asc > /dev/null
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add repo
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo $VERSION_CODENAME) stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install Docker
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
```

---

## 3. Verify Docker

```bash
sudo systemctl status docker
sudo docker run hello-world
```

If this fails, stop here—don’t proceed blindly.

---

## 4. (Optiona) Run Docker without sudo

```bash
sudo usermod -aG docker $USER
newgrp docker
```

---

## 5. Run Nginx container

Here’s the simplest correct command:

```bash
docker run -d \
  --name nginx-server \
  -p 80:80 \
  nginx
```

What this does:

* `-d` → runs in background
* `-p 80:80` → maps VM port 80 → container port 80
* `nginx` → pulls official image from Docker Hub

---

## 6. Verify container is running

```bash
docker ps
```

You should see `nginx-server` running.

---

## 7. Test locally (inside VM)

```bash
curl http://localhost
```

You should get Nginx HTML.

---

## 8. Test externally 

Use:

```
http://<your-vm-public-ip>
```

If it doesn’t work, check:

### Firewall issues

#### Ubuntu firewall:

```bash
sudo ufw allow 80
sudo ufw enable
```
