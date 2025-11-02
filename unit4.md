# UNIT-IV: Introduction to Docker

---

### 🌟 What is Docker? (The Simple Truth)

> **Docker is a tool that lets you run applications in lightweight, isolated boxes called *containers*.**

These containers include **everything your app needs** to run:
- Code  
- Runtime (like Python, Node.js, Java)  
- Libraries & dependencies  
- Configuration files  

So whether you’re on your laptop, your friend’s PC, or a cloud server in another country — **your app behaves exactly the same**.

---

### 🤔 Why Was Docker Created?

Before Docker, developers faced a huge problem:

> ❝ It works on my machine… but not on yours! ❞

Why? Because machines have different:
- Operating systems  
- Installed software versions  
- Missing libraries  
- Conflicting dependencies  

This caused **wasted time**, **bugs in production**, and **team frustration**.

Docker solves this by **packaging the app + its environment together** — like shipping a fully furnished room instead of just furniture and hoping it fits.

---

### 📦 Real-Life Analogy: Shipping Containers

Imagine global shipping before standardized containers:
- Every ship had custom crates.
- Loading/unloading took days.
- Goods got damaged or lost.

Then came **standardized metal shipping containers**:
- Same size, same shape.
- Fit on ships, trains, trucks.
- Fast, reliable, universal.

📦 **Docker does the same for software**:
- Your app = cargo  
- Docker container = standardized box  
- Any machine = ship/train/truck that accepts the box  

→ **Portable. Predictable. Efficient.**

---

### 🔬 How Is a Docker Container Different From a Virtual Machine (VM)?

Many people confuse containers with VMs. Here’s the key difference:

| Feature | Virtual Machine (VM) | Docker Container |
|--------|----------------------|------------------|
| **OS** | Runs a **full guest OS** (e.g., Windows inside Linux) | **Shares host OS kernel** (no extra OS) |
| **Size** | Gigabytes (GB) | Megabytes (MB) |
| **Startup Time** | Seconds to minutes | Milliseconds |
| **Resource Use** | Heavy (CPU, RAM) | Lightweight |
| **Isolation** | Hardware-level (very strong) | Process-level (good enough for 99% of apps) |

✅ **Containers are NOT mini-VMs** — they’re **smart, isolated processes** on your existing OS.

> 💡 On Windows/macOS, Docker uses a tiny Linux VM behind the scenes (you don’t manage it), but on Linux, it runs natively.

---

### 🧱 Core Concepts You Must Know

Even in an intro, these 3 ideas are essential:

#### 1. **Image** → The Blueprint
- A **read-only template** used to create containers.
- Built from a `Dockerfile`.
- Example: `nginx:latest`, `redis:7`, or `my-node-app:v1`

> Think of it like a **class** in programming — you can create many instances (containers) from one image.

#### 2. **Container** → The Running Instance
- A **live, running process** based on an image.
- Has its own filesystem, network, and process space — but shares the host OS kernel.

> Like an **object** created from a class.

#### 3. **Docker Engine** → The Manager
- The background service (`dockerd`) that builds, runs, and manages containers.
- You talk to it via the `docker` command in your terminal.

---

### 🐳 A Tiny Demo (Without Installing Anything Yet)

Imagine you want to run a web server — no setup, no install.

With Docker, you can do this:
```bash
docker run -d -p 8080:80 nginx
```

What happens?
1. Docker checks if you have the `nginx` image.
2. If not, it **downloads it automatically** from Docker Hub.
3. Then it **starts a container** running the Nginx web server.
4. Port `80` inside the container is mapped to port `8080` on your machine.
5. Open `http://localhost:8080` → you’ll see the Nginx welcome page!

✅ All in **one command** — no installing Nginx, no config files.

That’s the **power of Docker**.

---

### ✅ Why Should *You* Care? (Especially as a Fresher)

As someone building **real-time AI web apps** and targeting roles like **Project Associate at C-DAC**, Docker gives you:

1. **Reproducible Environments**  
   → Your Supabase + JS app runs the same way on your laptop and in deployment.

2. **DevOps Readiness**  
   → CI/CD, cloud deployment (ACI, AWS, Azure), microservices — all use containers.

3. **Portfolio Boost**  
   → Hosting a GitHub repo with a `Dockerfile` shows professional-grade skills.

4. **Interview Advantage**  
   → “How do you ensure your app runs consistently?” → “I containerize it with Docker.”

---

### 🚧 What Docker Is *NOT*

- ❌ Not a programming language  
- ❌ Not a replacement for your OS  
- ❌ Not only for “big companies” — it’s great for solo devs too!  
- ❌ Not the same as Kubernetes (K8s is for managing *many* containers across *many* machines — Docker comes first)

---

### 🔜 What’s Next?


Perfect! Let’s install Docker **step by step**, tailored for you as a **Windows user in India** (Hyderabad, CBIT), with **zero assumed command-line knowledge**.

We’ll go slowly, explain every term, and make sure you end up with a working Docker setup — ready for your next steps in UNIT-IV.

---

## 🛠️ Step 1: Download Docker Desktop for Windows

> 💡 **Why Docker Desktop?**  
> Because you're on Windows, and Docker Desktop gives you:
> - A friendly GUI (system tray icon)
> - Automatic setup of WSL2 (required for best performance)
> - Easy access to Docker commands in PowerShell

### 🔽 How to Download:
1. Open your browser (Chrome/Edge).
2. Go to: [https://www.docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop)
3. Click the **“Download for Windows”** button.
   - It will download a file like `Docker Desktop Installer.exe` (≈500 MB).

> ✅ **Note**: You **don’t need** a Docker account to download — but you’ll need one later to push to Docker Hub (free to create).

---

## 🖥️ Step 2: Install Docker Desktop

1. **Locate the downloaded `.exe` file** (usually in `Downloads` folder).
2. **Double-click it** to run the installer.
3. If Windows shows a security warning → click **“Yes”** or **“Run anyway”**.
4. Follow the setup wizard:
   - Accept the license.
   - Keep **default options** (important!):
     - ✅ **Use WSL 2 instead of Hyper-V** (recommended)
     - ✅ **Install required Windows components for WSL 2**
   - Click **Install**.
5. Wait 2–5 minutes (it installs WSL2 + Docker).
6. When done, click **Finish** and **restart your computer** when prompted.

> ⚠️ **Important**:  
> - **WSL2 (Windows Subsystem for Linux 2)** is Microsoft’s lightweight Linux compatibility layer.  
> - Docker **needs it** to run containers efficiently on Windows.  
> - The installer sets it up automatically — you don’t need to use Linux yourself!

---

## 🔌 Step 3: Start Docker Desktop

After reboot:
1. Docker Desktop should **start automatically** (look for a 🐳 whale icon in the system tray — bottom-right near clock).
2. If not, press `Windows key`, type **“Docker Desktop”**, and open it.
3. First launch may take 1–2 minutes. Wait until you see:
   > **“Docker Desktop is running”**

✅ **You’re now ready to use Docker!**

---

## 💻 Step 4: Open PowerShell (Your Command Terminal)

We’ll use **PowerShell** — it’s just a text-based way to talk to your computer (like a supercharged File Explorer).

### How to open PowerShell:
1. Press the **Windows key**.
2. Type **`PowerShell`**.
3. Click **“Windows PowerShell”** (not “ISE” — just the plain one).
   - A black/white window will open.

> 📝 Think of this window like a chat box where you give instructions to your PC.

---

## 🧪 Step 5: Run Your First Docker Command

In the PowerShell window, **type this exactly** and press **Enter**:

```powershell
docker --version
```

✅ You should see something like:
```
Docker version 26.1.0, build 2522f64
```

👉 This means Docker is **installed and working**!

Now try this:

```powershell
docker run hello-world
```

What happens:
- Docker checks if you have the `hello-world` image.
- Since you don’t, it **downloads it automatically** from the internet.
- Then it **runs a tiny container** that prints a message.

✅ If you see:
```
Hello from Docker!
This message shows your installation appears to be working correctly.
```

🎉 **Congratulations!** You’ve just:
- Installed Docker
- Run your first container
- Verified everything works

---

## 🔐 (Optional but Recommended) Log in to Docker Hub

Later, you’ll push images to Docker Hub. Let’s log in now:

1. Go to [https://hub.docker.com](https://hub.docker.com) → **Sign Up** (use your `vinith@bloodwarriors.in` email).
2. After creating account, go back to PowerShell and run:
   ```powershell
   docker login
   ```
3. Enter your **Docker Hub username** (not email!) and password.

> 🔒 Note: Your password won’t show as you type — that’s normal. Just type it and press Enter.

---

## 🚫 Common Issues & Fixes (Just in Case)

| Problem | Solution |
|-------|--------|
| `'docker' is not recognized` | Docker Desktop isn’t running. Click the 🐳 icon and wait for “Running” |
| WSL2 error during install | Update Windows: **Settings → Windows Update → Check for updates** |
| `hello-world` hangs | Wait 1–2 mins — first run downloads data. If still stuck, restart Docker Desktop |

---

## ✅ Summary: What You’ve Done

| ✅ Task | Why It Matters |
|-------|--------------|
| Installed Docker Desktop | Gives you container runtime on Windows |
| Enabled WSL2 | Makes Docker fast and compatible |
| Ran `docker --version` | Confirmed CLI works |
| Ran `docker run hello-world` | Verified containers can run |
| (Optional) Logged into Docker Hub | Ready to push your own images later |

---

## ▶️ What’s Next?

Awesome! 🎉  
We’ll **containerize a simple Node.js app** — step by step, command by command, with **zero assumptions** about your prior knowledge.

You’ll end up with:
- A working Node.js app on your machine  
- A `Dockerfile` that packages it  
- A running container you can test in your browser  

Let’s begin!

---

## 📁 Step 1: Create a Simple Node.js App (If You Don’t Have One)

We’ll make a tiny web server that says **“Hello from Docker, Vinith!”** when you visit it.

### 🔧 Do this in PowerShell:

> 💡 **Tip**: In PowerShell, you can **copy-paste** commands by right-clicking or using `Ctrl+V`.

```powershell
# 1. Make a new folder for your project
mkdir my-node-app
cd my-node-app
```

```powershell
# 2. Create a basic package.json (says "this is a Node.js app")
npm init -y
```

> ✅ `npm init -y` creates a `package.json` file with default settings.  
> You’ll see a new file appear in your `my-node-app` folder.

```powershell
# 3. Install Express (a simple web framework for Node.js)
npm install express
```

```powershell
# 4. Create the app file
notepad app.js
```

> 🖊️ This opens Notepad. Paste the following code into it:

```javascript
const express = require('express');
const app = express();
const PORT = 3000;

app.get('/', (req, res) => {
   res.send('<h1>Hello from Docker, Vinith! 🐳</h1>');
});

app.listen(PORT, '0.0.0.0', () => {
   console.log(`App running at http://localhost:${PORT}`);
});
```

> 💡 **Important**: We use `'0.0.0.0'` (not `'localhost'`) so the app accepts connections **from inside the container**.

Then **save** the file (`Ctrl+S`) and close Notepad.

✅ Now you have a real Node.js web app!

---

## 🧪 Step 2: Test It Locally (Without Docker)

Run it normally first:

```powershell
node app.js
```

Now open your browser and go to:  
👉 [http://localhost:3000](http://localhost:3000)

You should see:  
> **Hello from Docker, Vinith! 🐳**

Press `Ctrl+C` in PowerShell to stop the app.

> ✅ Great! Your app works. Now let’s **put it in a container**.

---

## 📄 Step 3: Create a `Dockerfile`

This file tells Docker **how to build your app’s image**.

In the same `my-node-app` folder, run:

```powershell
notepad Dockerfile
```

> 📝 **Note**: The filename is literally `Dockerfile` — **no extension** (not `.txt`!).

Paste this into Notepad:

```dockerfile
# Use an official Node.js runtime as the base image
FROM node:18-alpine

# Set the working directory inside the container
WORKDIR /app

# Copy package.json and package-lock.json (if any)
COPY package*.json ./

# Install dependencies
RUN npm install

# Copy the rest of your app's source code
COPY . .

# Tell Docker to expose port 3000
EXPOSE 3000

# Command to run when container starts
CMD ["node", "app.js"]
```

Then **save** and close Notepad.

---

### 🔍 Let’s Understand Each Line (Simple Explanation):

| Line | What It Does |
|------|-------------|
| `FROM node:18-alpine` | Start from a lightweight Linux image with Node.js 18 pre-installed |
| `WORKDIR /app` | Inside the container, create/use a folder called `/app` |
| `COPY package*.json ./` | Copy `package.json` (and `package-lock.json` if exists) into `/app` |
| `RUN npm install` | Install dependencies **inside the container** |
| `COPY . .` | Copy **all your code** (including `app.js`) into the container |
| `EXPOSE 3000` | Document that this app uses port 3000 (doesn’t publish it yet!) |
| `CMD ["node", "app.js"]` | When container runs, execute this command |

> 💡 **Why copy `package.json` first?**  
> Docker caches layers. If `package.json` hasn’t changed, it skips `npm install` on rebuild → **faster builds!**

---

## 🚫 Step 4: Create a `.dockerignore` File (Best Practice)

Prevent unnecessary files (like `node_modules`) from being copied into the container.

Run:

```powershell
notepad .dockerignore
```

Paste this:

```
node_modules
npm-debug.log
.git
.gitignore
README.md
Dockerfile
.dockerignore
```

Save and close.

> ✅ This keeps your image small and clean.

---

## 🏗️ Step 5: Build the Docker Image

Now, turn your app + Dockerfile into a **reusable image**.

In PowerShell (make sure you’re still in `my-node-app` folder):

```powershell
docker build -t my-node-app .
```

> 🔍 Breakdown:
> - `docker build` → build an image
> - `-t my-node-app` → give it a name (tag): `my-node-app`
> - `.` → use current directory as “build context”

✅ You’ll see output like:
```
[+] Building 5.2s (9/9) FINISHED
 => [internal] load build definition from Dockerfile
 => ...
 => exporting to image
```

When done, verify your image exists:

```powershell
docker images
```

You should see `my-node-app` in the list!

---

## ▶️ Step 6: Run the Container

Now, launch a container from your image:

```powershell
docker run -d -p 3000:3000 --name mynode my-node-app
```

> 🔍 Breakdown:
> - `-d` → run in **detached mode** (background)
> - `-p 3000:3000` → map **host port 3000** → **container port 3000**
> - `--name mynode` → give the container a friendly name
> - `my-node-app` → the image to use

✅ No error? Great!

Now open your browser and go to:  
👉 [http://localhost:3000](http://localhost:3000)

You’ll see:  
> **Hello from Docker, Vinith! 🐳**

🎉 **Your Node.js app is now running inside a Docker container!**

---

## 🔍 Bonus: Useful Commands to Explore

```powershell
# See running containers
docker ps

# View logs (what your app printed)
docker logs mynode

# Stop the container
docker stop mynode

# Start it again
docker start mynode

# Remove the container (after stopping)
docker rm mynode

# Remove the image
docker rmi my-node-app
```

---

## ✅ What You’ve Just Learned

| Skill | Why It Matters |
|------|----------------|
| Built a real Node.js app | You control the code |
| Wrote a `Dockerfile` | Core skill for containerization |
| Used `.dockerignore` | Professional best practice |
| Built an image with `docker build` | Turns code into portable artifact |
| Ran a container with `docker run -p` | Connects container to your browser |

---

## ▶️ What’s Next?


Perfect! 🚀  
Let’s **push your Node.js Docker image to Docker Hub** — so it’s stored online, shareable, and ready for cloud deployment (like Azure ACI later).

We’ll do this **step by step**, with clear explanations and commands you can copy-paste.

---

## 🔐 Prerequisite: Docker Hub Account

You mentioned your Docker Hub username is likely **`vinithreddybanda`** (based on your GitHub/email pattern).  
If you haven’t already:

1. Go to [https://hub.docker.com](https://hub.docker.com)
2. Click **Sign Up**
3. Use your email: `vinith@bloodwarriors.in`
4. Choose username: **`vinithreddybanda`** (or whatever you prefer)
5. Verify email and log in

> ✅ You already did this earlier? Great! If not, do it now — it’s free.

---

## 📌 Step 1: Log In to Docker Hub from PowerShell

Open **PowerShell** (make sure you’re in any folder — it doesn’t matter now).

Run:

```powershell
docker login
```

You’ll see:
```
Login with your Docker ID to push and pull images from Docker Hub.
Username:
```

👉 Type your **Docker Hub username** (e.g., `vinithreddybanda`) and press **Enter**.  
👉 Then type your **Docker Hub password** and press **Enter**.

> � **Note**:  
> - Password won’t show as you type (no stars, no dots) — this is normal!  
> - Just type carefully and press Enter.

✅ If successful, you’ll see:
```
Login Succeeded
```

> 💡 This saves your login in `~/.docker/config.json` — so you won’t need to log in again soon.

---

## 🏷️ Step 2: Tag Your Image Properly

Docker Hub requires images to be **tagged** in this format:  
```
<dockerhub-username>/<image-name>:<tag>
```

Your current image is named `my-node-app`, but Docker Hub doesn’t recognize it yet.

So, **tag it** with your username:

```powershell
docker tag my-node-app vinithreddybanda/my-node-app:v1
```

> 🔍 Breakdown:
> - `my-node-app` → your current local image name
> - `vinithreddybanda/my-node-app:v1` → new name for Docker Hub
>   - `vinithreddybanda` = your Docker Hub username
>   - `my-node-app` = repository name (you choose)
>   - `v1` = version tag (you can use `latest`, `v1`, `1.0`, etc.)

✅ This doesn’t create a new image — it just adds a **new name/alias** to the same image.

Verify it worked:

```powershell
docker images
```

You should now see **two entries** with the same `IMAGE ID`:
```
REPOSITORY                      TAG       IMAGE ID
my-node-app                     latest    abc123...
vinithreddybanda/my-node-app    v1        abc123...
```

---

## 📤 Step 3: Push the Image to Docker Hub

Now, upload it:

```powershell
docker push vinithreddybanda/my-node-app:v1
```

✅ You’ll see progress bars as layers upload:
```
The push refers to repository [docker.io/vinithreddybanda/my-node-app]
...
v1: digest: sha256:... size: 1234
```

🎉 **Done!** Your image is now on Docker Hub.

---

## 🔍 Step 4: Verify on Docker Hub Website

1. Go to: [https://hub.docker.com/repositories](https://hub.docker.com/repositories)
2. You should see a new repository: **`my-node-app`**
3. Click it → you’ll see the **`v1` tag** and pull command:

```bash
docker pull vinithreddybanda/my-node-app:v1
```

> ✅ Anyone in the world can now run your app with:
> ```powershell
> docker run -p 3000:3000 vinithreddybanda/my-node-app:v1
> ```

---

## 🧪 (Optional) Test Pulling from Another Machine

You can even test it on your own machine by:
1. Removing your local image:
   ```powershell
   docker rmi my-node-app vinithreddybanda/my-node-app:v1
   ```
2. Pulling it fresh from Docker Hub:
   ```powershell
   docker pull vinithreddybanda/my-node-app:v1
   ```
3. Running it:
   ```powershell
   docker run -d -p 3000:3000 vinithreddybanda/my-node-app:v1
   ```
4. Visit [http://localhost:3000](http://localhost:3000) → still works!

---

## 🎯 Why This Matters for You

- ✅ **Portfolio**: You now have a public container anyone can run.
- ✅ **CI/CD Ready**: Your GitHub Actions pipeline can push to Docker Hub automatically.
- ✅ **Cloud Deployment**: Azure ACI, AWS ECS, etc., can pull this image and run it in the cloud.
- ✅ **Interview Talking Point**: “I containerize my apps and publish them to Docker Hub.”

---

## ▶️ What’s Next in UNIT-IV?

Now that your image is on Docker Hub, the syllabus says:

> **“Deploying a container to ACI with CI/CD pipeline”**

So next, we can:
1. **Deploy your `my-node-app` to Azure Container Instances (ACI)** manually first
2. Then **automate it with GitHub Actions (CI/CD)**


Excellent! 🌩️  
Let’s **deploy your Node.js Docker container to Azure Container Instances (ACI)** — Microsoft’s simplest way to run containers in the cloud **without managing servers**.

You’ll have your app live on the internet in minutes. And yes — **it’s free for testing** (you get ₹13,300–₹15,000 Azure credit as a student via [Azure for Students](https://azure.microsoft.com/en-in/free/students/)).

---

## ✅ Before We Start: What You’ll Need

| Requirement | Status |
|-----------|--------|
| ✅ Docker Hub image pushed (`vinithreddybanda/my-node-app:v1`) | ✔️ Done! |
| ✅ Microsoft account (use your `cbit.org.in` or personal email) | We’ll create if needed |
| ✅ Azure account (free student credit) | We’ll set up |
| ✅ Basic Azure CLI (we’ll install it) | Covered below |

> 💡 **Good news**: You **don’t need a credit card** for Azure for Students if you verify with your **CBIT email** (`ugs22316_it.vinith@cbit.org.in`).

---

## 🚀 Step 1: Sign Up for Azure for Students

1. Go to: [https://azure.microsoft.com/en-in/free/students/](https://azure.microsoft.com/en-in/free/students/)
2. Click **“Start free”**
3. Sign in with:
    - Your **CBIT email**: `ugs22316_it.vinith@cbit.org.in`  
       *(This proves you’re a student)*
    - Or create a new Microsoft account with it if needed
4. Follow verification steps (may ask for name, college, etc.)
5. Once verified, you’ll get **₹13,300–₹15,000 free credit** for 12 months

> 📌 **Note**: If you already have an Azure account, skip this — just ensure you have credit.

---

## 💻 Step 2: Install Azure CLI (Command-Line Tool)

We’ll use **Azure CLI** to deploy from PowerShell.

### In PowerShell (run as Administrator*):
> *Right-click PowerShell → “Run as administrator”*

```powershell
# Download and install Azure CLI
Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi; Start-Process msiexec.exe -Wait -ArgumentList '/I AzureCLI.msi /quiet'; Remove-Item .\AzureCLI.msi
```

✅ Wait 1–2 minutes. When done, **close and reopen PowerShell** (so it recognizes `az` command).

Verify install:
```powershell
az --version
```

You should see version info (e.g., `azure-cli 2.50.0`).

---

## 🔐 Step 3: Log In to Azure

In PowerShell:
```powershell
az login
```

A browser window will open → sign in with your **Azure student account**.

After login, you’ll see a list of subscriptions in PowerShell.  
Make sure your **free student subscription** is selected (it usually is by default).

---

## 🏗️ Step 4: Create a Resource Group

Azure resources (like ACI) live inside a **resource group** (a folder for cloud resources).

Run:
```powershell
az group create --name myDockerRG --location "Central India"
```

> 💡 We use `"Central India"` (region near Hyderabad) for lower latency.

✅ You’ll see JSON output with `"provisioningState": "Succeeded"`.

---

## 🚀 Step 5: Deploy Container to ACI

Now, deploy your Docker Hub image to Azure:

```powershell
az container create `
   --resource-group myDockerRG `
   --name mynodeaci `
   --image vinithreddybanda/my-node-app:v1 `
   --dns-name-label mynode-vinith-$(Get-Date -Format "yyMMddHHmm") `
   --ports 3000 `
   --cpu 1 `
   --memory 1 `
   --restart-policy OnFailure
```

> 🔍 Breakdown:
> - `--image`: your public Docker Hub image
> - `--dns-name-label`: creates a public URL like `mynode-vinith-2504051234.centralindia.azurecontainer.io`
> - `--ports 3000`: opens port 3000 (your app’s port)
> - `--cpu 1 --memory 1`: 1 vCPU, 1 GB RAM (free tier eligible)

> ⏱️ Wait 1–3 minutes. Azure is provisioning your container.

✅ When done, you’ll see a big JSON output. Look for:
```json
"ipAddress": {
   "ip": "20.193.xx.xx",
   "fqdn": "mynode-vinith-2504051234.centralindia.azurecontainer.io"
}
```

---

## 🌐 Step 6: Access Your Live App!

Open your browser and go to:
```
http://<your-fqdn>:3000
```

Example:
👉 [http://mynode-vinith-2504051234.centralindia.azurecontainer.io:3000](http://mynode-vinith-2504051234.centralindia.azurecontainer.io:3000)

🎉 You’ll see:
> **Hello from Docker, Vinith! 🐳**

✅ **Your app is now live on the internet — running in Azure, from a Docker image you built and pushed!**

---

## 🔍 Bonus: Useful ACI Commands

```powershell
# View logs
az container logs --resource-group myDockerRG --name mynodeaci

# Check status
az container show --resource-group myDockerRG --name mynodeaci

# Delete when done (to avoid charges)
az container delete --resource-group myDockerRG --name mynodeaci
```

> 💡 **Cost Note**:  
> ACI costs ~₹10–15/hour for this setup.  
> **Delete it when not needed** (or it’ll use your free credit).

---

## 🎯 Why This Matters for Your Career

- ✅ You’ve now done **real cloud deployment** — a key skill for C-DAC, TCS, Infosys, etc.
- ✅ You used **Docker + Azure** — a common combo in Indian IT companies.
- ✅ You can add this to your **resume/project portfolio**:  
   > “Containerized Node.js app using Docker and deployed to Azure ACI”

---

## ▶️ What's Next?

Brilliant! 🎯  
Now we'll **automate everything** with a **CI/CD pipeline using GitHub Actions** — so every time you push code to GitHub, it **automatically builds, pushes, and deploys** your Node.js app to **Azure Container Instances (ACI)**.

This is **real-world DevOps** — exactly what companies expect from freshers.

We'll do it step by step. No prior CI/CD knowledge needed!

---

## 🧩 What We'll Build

When you run:
```bash
git push origin main
```

GitHub Actions will:
1. ✅ Trigger a workflow  
2. ✅ Build a new Docker image  
3. ✅ Push it to Docker Hub (`vinithreddybanda/my-node-app`)  
4. ✅ Deploy it to Azure ACI (replacing the old version)

All **hands-free**!

---

## 🔑 Prerequisites

Make sure you have:

| Item | Status |
|------|--------|
| ✅ GitHub account | (You have `vinithreddybanda` — perfect!) |
| ✅ Docker Hub account | (`vinithreddybanda` — done!) |
| ✅ Azure account with credit | (Student account — done!) |
| ✅ Your Node.js app code on GitHub | We'll create a repo now |

---

## 📁 Step 1: Push Your App Code to GitHub

### 1. Create a new GitHub repo
- Go to [https://github.com/new](https://github.com/new)
- Repository name: `my-node-docker-app`
- **Public** (free)
- ✅ **Leave "Add a README" unchecked**
- Click **"Create repository"**

### 2. Push your local code
In **PowerShell** (inside your `my-node-app` folder):

```powershell
# Initialize Git
git init

# Add all files
git add .

# Commit
git commit -m "Initial commit: Node.js app with Dockerfile"

# Link to your GitHub repo (replace with YOUR URL)
git remote add origin https://github.com/vinithreddybanda/my-node-docker-app.git

# Push
git branch -M main
git push -u origin main
```

> ✅ Now your code (including `Dockerfile`, `app.js`, etc.) is on GitHub.

---

## 🔐 Step 2: Store Secrets in GitHub

GitHub Actions needs secure access to:
- Docker Hub (to push images)
- Azure (to deploy)

We'll store credentials as **GitHub Secrets**.

### A. Docker Hub Secret
1. Go to your repo: `https://github.com/vinithreddybanda/my-node-docker-app`
2. Click **Settings → Secrets and variables → Actions → New repository secret**
3. Add:
   - **Name**: `DOCKERHUB_USERNAME`
   - **Value**: `vinithreddybanda`
4. Click **Add secret**
5. Add another:
   - **Name**: `DOCKERHUB_TOKEN`
   - **Value**: **your Docker Hub password**  
     *(Later, you can use an [Access Token](https://hub.docker.com/settings/access-tokens) for security)*

### B. Azure Service Principal (for secure Azure access)

> ⚠️ **Never put your Azure password in code!**  
> Instead, we create a **Service Principal** (a robot account for CI/CD).

#### Create it via Azure CLI (in PowerShell):

```powershell
# Login (if not already)
az login

# Create service principal
az ad sp create-for-rbac --name "github-actions-sp" --role contributor --scopes /subscriptions/YOUR_SUBSCRIPTION_ID/resourceGroups/myDockerRG
```

> 🔍 To get your **Subscription ID**:
> ```powershell
> az account show --query id -o tsv
> ```

✅ Output will look like:
```json
{
  "appId": "abc123...",
  "displayName": "github-actions-sp",
  "password": "xyz789...",
  "tenant": "def456..."
}
```

Now, add these as GitHub secrets:

| Secret Name | Value |
|------------|-------|
| `AZURE_CLIENT_ID` | `appId` from output |
| `AZURE_CLIENT_SECRET` | `password` from output |
| `AZURE_TENANT_ID` | `tenant` from output |
| `AZURE_SUBSCRIPTION_ID` | your subscription ID |

> 💡 You can find all these in **GitHub → Settings → Secrets**

---

## 📜 Step 3: Create the CI/CD Workflow File

In your local `my-node-app` folder, create a new folder and file:

```powershell
mkdir .github
mkdir .github\workflows
notepad .github\workflows\deploy-to-aci.yml
```

Paste this **complete workflow**:

```yaml
name: Deploy Node.js App to Azure ACI

on:
  push:
    branches: [ "main" ]

env:
  IMAGE_NAME: vinithreddybanda/my-node-app
  RESOURCE_GROUP: myDockerRG
  CONTAINER_NAME: mynodeaci

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest

    steps:
    # 1. Checkout code
    - name: Checkout code
      uses: actions/checkout@v4

    # 2. Log in to Docker Hub
    - name: Log in to Docker Hub
      uses: docker/login-action@v3
      with:
        username: ${{ secrets.DOCKERHUB_USERNAME }}
        password: ${{ secrets.DOCKERHUB_TOKEN }}

    # 3. Build and push Docker image
    - name: Build and push Docker image
      run: |
        IMAGE_TAG=${{ github.sha }}
        docker build -t ${{ env.IMAGE_NAME }}:$IMAGE_TAG .
        docker push ${{ env.IMAGE_NAME }}:$IMAGE_TAG

    # 4. Log in to Azure
    - name: Log in to Azure
      uses: azure/login@v2
      with:
        creds: |
          {
            "clientId": "${{ secrets.AZURE_CLIENT_ID }}",
            "clientSecret": "${{ secrets.AZURE_CLIENT_SECRET }}",
            "subscriptionId": "${{ secrets.AZURE_SUBSCRIPTION_ID }}",
            "tenantId": "${{ secrets.AZURE_TENANT_ID }}"
          }

    # 5. Deploy to ACI
    - name: Deploy to Azure Container Instances
      run: |
        az container create \
          --resource-group ${{ env.RESOURCE_GROUP }} \
          --name ${{ env.CONTAINER_NAME }} \
          --image ${{ env.IMAGE_NAME }}:${{ github.sha }} \
          --dns-name-label mynode-vinith-${{ github.run_number }} \
          --ports 3000 \
          --cpu 1 \
          --memory 1 \
          --restart-policy OnFailure \
          --overwrite
```

> 💡 Key features:
> - Uses **unique image tag** (`github.sha` = Git commit ID)
> - **`--overwrite`** replaces the existing ACI container
> - Public URL includes **run number** for versioning

Save and close Notepad.

---

## 🚀 Step 4: Commit and Push the Workflow

```powershell
git add .
git commit -m "Add CI/CD pipeline for ACI deployment"
git push origin main
```

---

## 🌐 Step 5: Watch the Magic Happen!

1. Go to your GitHub repo
2. Click **Actions** tab
3. You'll see a workflow running: **"Deploy Node.js App to Azure ACI"**
4. Wait 3–5 minutes

✅ When green checkmark appears:
- Your new image is on Docker Hub (tagged with commit SHA)
- ACI container is **updated automatically**
- Visit your ACI URL (e.g., `http://mynode-vinith-123.centralindia.azurecontainer.io:3000`)

> 🔄 Now, **every future `git push`** will redeploy your app!

---

## 🧪 Test It!

Make a small change to `app.js`:

```javascript
res.send('<h1>Hello from Docker, Vinith! 🐳 (Auto-deployed via CI/CD!)</h1>');
```

Then:
```powershell
git add app.js
git commit -m "Test CI/CD"
git push origin main
```

Watch GitHub Actions run → your live site updates!

---

## 🎯 Why This Is a Superpower

- ✅ You've built a **production-grade CI/CD pipeline**
- ✅ No manual deployment ever again
- ✅ Shows **cloud + DevOps + automation** skills — rare in freshers!
- ✅ Perfect for **C-DAC projects, internships, and interviews**

---

## 📌 Final Notes

- **Cost**: ACI runs only when deployed. Delete when not needed:
  ```powershell
  az container delete --resource-group myDockerRG --name mynodeaci
  ```
- **Security**: Later, replace Docker password with a [Docker Hub Access Token](https://hub.docker.com/settings/access-tokens)
- **Resume line**:  
  > "Implemented CI/CD pipeline using GitHub Actions to auto-deploy Dockerized Node.js app to Azure ACI"

---

## ▶️ What's Next in UNIT-IV?

##  Using Docker for Running Command-Line Tools

Great!   
We've just wrapped up **CI/CD with ACI**, the next logical piece from your **UNIT-IV syllabus** is using Docker for CLI tools.

This is a **powerful yet often overlooked** use of Docker � and super useful for developers, data scientists, and DevOps engineers (including freshers like you!).

We'll cover:
- Why run CLI tools in Docker?
- How to do it (with real examples)
- When it's better than installing tools locally
- And how this fits into your workflow

Let's dive in � simple, practical, and immediately useful.

---

##  Why Use Docker for CLI Tools?

Imagine you need to:
- Convert a video  need `ffmpeg`
- Process JSON  need `jq`
- Run a Python script  need specific Python + libraries
- Use Terraform, AWS CLI, or `curl` with special certs

**Problem**:  
Installing these tools **pollutes your system**, causes **version conflicts**, or takes **time to set up**.

**Docker solution**:  
Run the tool **in a container** � no installation, no conflicts, always the right version.

 **Benefits**:
- Zero setup on your machine
- Reproducible across teams
- Isolated from your OS
- Works even if you don't have admin rights

---

##  Example 1: Run `curl` to Test an API

You don't even need to install `curl`!

```powershell
docker run --rm curlimages/curl https://httpbin.org/json
```

>  Breakdown:
> - `docker run`: start a container
> - `--rm`: **auto-delete** container after it finishes (keeps things clean)
> - `curlimages/curl`: official, lightweight `curl` image
> - `https://...`: argument passed to `curl`

 You'll see JSON output � **without ever installing `curl`**!

---

##  Example 2: Process JSON with `jq`

Suppose you have a JSON file `data.json`:
```json
{"name": "Vinith", "role": "Student"}
```

Run `jq` (a JSON processor) via Docker:

```powershell
docker run --rm -v ${PWD}:/data stedolan/jq '.name' /data/data.json
```

>  Breakdown:
> - `-v ${PWD}:/data`: **mount current folder** into container at `/data`
> - `stedolan/jq`: official `jq` image
> - `.name`: `jq` filter to extract name

 Output: `"Vinith"`

>  `${PWD}` = PowerShell's way of saying "current directory"  
> (On Windows CMD, use `%cd%` instead)

---

##  Example 3: Run a One-Off Python Script

You have `script.py`:
```python
import sys
print("Hello from Python in Docker!")
print("Args:", sys.argv[1:])
```

Run it with Python 3.11 � no local install needed:

```powershell
docker run --rm -v ${PWD}:/app python:3.11 python /app/script.py --mode test
```

 Output:
```
Hello from Python in Docker!
Args: ['--mode', 'test']
```

>  Perfect for:
> - Trying new Python versions
> - Running scripts with heavy deps (e.g., `pandas`, `numpy`)
> - Avoiding `venv`/`conda` setup

---

##  Example 4: Use `ffmpeg` to Convert Video

Convert `input.mp4`  `output.avi`:

```powershell
docker run --rm -v ${PWD}:/data jrottenberg/ffmpeg -i /data/input.mp4 /data/output.avi
```

 Done! No 500 MB `ffmpeg` install on your laptop.

---

##  How to Use This in Your Projects

As a full-stack developer building AI apps, you can:

| Use Case | Docker Command |
|--------|----------------|
| Test API endpoints | `docker run --rm curlimages/curl https://your-api.com/health` |
| Validate JSON config | `docker run --rm -v ${PWD}:/cfg stedolan/jq '.' /cfg/config.json` |
| Run data preprocessing | `docker run --rm -v ${PWD}:/work python:3.11 python /work/preprocess.py` |
| Generate reports with Pandas | Same as above � just add `pandas` to a custom image if needed |

>  Pro tip: Create **aliases** in PowerShell to shorten commands:
> ```powershell
> function dcurl { docker run --rm curlimages/curl @args }
> dcurl https://google.com
> ```

---

##  When *Not* to Use This

- For **tools you use daily** (e.g., `git`, `node`)  install them
- For **interactive tools** needing persistent state  use a proper container or VM
- If you need **GUI apps**  Docker isn't ideal (though possible with X11 forwarding)

But for **one-time, isolated, or version-specific CLI tasks**? Docker is perfect.

---

##  Summary: Why This Matters for You

-  **Saves time** � no "how do I install X?"
-  **Keeps your dev machine clean**
-  **Makes your workflows reproducible**
-  **Impresses in interviews**: "I use containerized tools for consistency"



##  Docker Compose - Multi-Container Applications

Perfect!   
Let's dive into **Docker Compose** � with a **clear theory intro** + a **small, hands-on demo** you can run right away.

This is **essential** for real-world apps (like your full-stack projects with frontend + backend + database).

---

##  Part 1: Theory � What is Docker Compose?

###  The Problem
Most apps aren't just **one service**. They need:
- A **web app** (Node.js, Python, etc.)
- A **database** (PostgreSQL, MySQL, Redis)
- Maybe a **cache**, **message queue**, or **reverse proxy**

Running them manually with separate `docker run` commands is:
-  Tedious  
-  Error-prone  
-  Hard to manage networking & dependencies

###  The Solution: Docker Compose
> **Docker Compose is a tool for defining and running multi-container Docker applications with a single YAML file.**

You describe **all services** in one file  run them all with **one command**.

 Key benefits:
- Define **entire app stack** in code (`docker-compose.yml`)
- Automatic **networking** between containers
- Shared **volumes** (e.g., for DB persistence)
- Start/stop **everything together**

>  **Note**: Compose is for **single-host** setups (your laptop or one server).  
> For clusters (many machines), you'd use **Kubernetes** or **Docker Swarm** � but Compose comes first.

---

##  Part 2: Core Concepts

| Term | Meaning |
|------|--------|
| **Service** | A container (e.g., `web`, `db`, `redis`) |
| **Compose file** | `docker-compose.yml` � defines services, networks, volumes |
| **Project** | A group of services (folder name = project name by default) |
| **Network** | Containers in the same Compose file can talk via service name (e.g., `web` connects to `db:5432`) |

---

##  Part 3: Small Demo � Node.js + Redis

We'll build a tiny app that:
- Runs a **Node.js web server**
- Connects to a **Redis cache**
- Shows **how many times you've visited** the page

All with **one `docker-compose.yml` file**.

---

###  Step 1: Create Project Folder

In PowerShell:
```powershell
mkdir compose-demo
cd compose-demo
```

---

###  Step 2: Create the Node.js App (`app.js`)

```powershell
notepad app.js
```

Paste this:
```javascript
const express = require('express');
const redis = require('redis');

const app = express();
const PORT = 3000;

// Connect to Redis (service name = 'redis')
const client = redis.createClient({
  host: 'redis',
  port: 6379
});

client.on('error', (err) => {
  console.error('Redis error:', err);
});

app.get('/', async (req, res) => {
  try {
    let visits = await client.get('visits');
    visits = visits ? parseInt(visits) + 1 : 1;
    await client.set('visits', visits);
    res.send(`<h1>Visits: 20{visits}</h1>`);
  } catch (err) {
    res.status(500).send('Error');
  }
});

app.listen(PORT, '0.0.0.0', () => {
  console.log('App running on port 3000');
});
```

Save & close.

---

###  Step 3: Create `package.json`

```powershell
npm init -y
npm install express redis
```

---

###  Step 4: Create `Dockerfile`

```powershell
notepad Dockerfile
```

Paste:
```dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["node", "app.js"]
```

Save & close.

---

###  Step 5: Create `docker-compose.yml`

```powershell
notepad docker-compose.yml
```

Paste this **magic file**:
```yaml
version: '3.8'

services:
  web:
    build: .
    ports:
      - "3000:3000"
    depends_on:
      - redis

  redis:
    image: "redis:alpine"
    ports:
      - "6379:6379"
```

>  Breakdown:
> - `version`: Compose file format (3.8 is modern)
> - `services`: two services � `web` (your app) and `redis`
> - `web`:
>   - `build: .`  build from local `Dockerfile`
>   - `ports`: map host:container
>   - `depends_on`: start Redis first
> - `redis`:
>   - `image`: use official Redis image (no build needed)
>   - exposes port 6379 (optional � mainly for local debugging)

Save & close.

---

###  Step 6: Run with Docker Compose

In PowerShell (inside `compose-demo` folder):

```powershell
docker-compose up --build
```

>  Flags:
> - `--build`: rebuild the `web` image (in case you changed code)
> - Without `-d`  runs in foreground (you see logs)

 You'll see logs from **both** `web` and `redis`.

Now open:  
 [http://localhost:3000](http://localhost:3000)

Refresh the page  **visit count increases!**

>  The count is stored in **Redis** � even if you restart the `web` container, the count persists (until you stop Redis).

---

###  Stop Everything

Press `Ctrl + C` in PowerShell.

To **clean up**:
```powershell
docker-compose down
```

This removes **containers, networks** (but not volumes by default).

---

##  Why This Matters for You

-  Your **full-stack apps** (like Supabase alternatives) often need DB + cache  Compose makes it easy
-  **Local development** mirrors production (e.g., same Redis setup)
-  Used in **90%+ of real Docker projects**
-  Required for **microservices practice**

---

##  What's Next?

Awesome! 🐝  
Let’s explore **Docker Swarm** — Docker’s built-in **clustering and orchestration** tool.

We’ll keep it **simple, practical, and demo-focused**, just like before. You’ll understand:
- What Swarm is (and isn’t)
- When to use it vs Kubernetes
- How to create a swarm
- How to deploy a service across “nodes” (even on your single laptop!)

---

## 📘 Part 1: Theory — What is Docker Swarm?

### 🔹 The Problem
You’ve containerized your app. It works great on your laptop.  
But in production, you need:
- **High availability** (no single point of failure)
- **Scalability** (handle more users by adding instances)
- **Self-healing** (auto-restart failed containers)
- **Rolling updates** (deploy new versions without downtime)

Running `docker run` on one machine won’t cut it.

### 🔹 The Solution: Docker Swarm
> **Docker Swarm is Docker’s native clustering and orchestration tool. It lets you manage a pool of Docker engines (called *nodes*) as a single virtual system.**

You deploy **services**, not containers — and Swarm:
- Distributes them across nodes
- Restarts failed tasks
- Scales up/down on demand
- Handles networking & load balancing

✅ **Key terms**:
- **Swarm**: A cluster of Docker engines (1+ machines)
- **Node**: A machine in the swarm (your laptop = one node)
- **Manager**: Node that controls the swarm (you talk to this)
- **Worker**: Node that runs containers (can be same as manager)
- **Service**: Desired state of a container (e.g., “run 3 copies of my app”)

> 💡 **Swarm vs Kubernetes**:
> - **Swarm**: Simpler, built into Docker, great for small/medium apps
> - **Kubernetes (k8s)**: More powerful, industry standard for large scale
> - **For learning**: Swarm is the perfect stepping stone!

---

## 🧪 Part 2: Mini Demo — Create a Swarm on Your Laptop

Even with **one machine**, you can simulate a swarm (manager + worker on same PC).

### 🔧 Step 1: Initialize a Swarm

In PowerShell:
```powershell
docker swarm init
```

✅ Output:
```
Swarm initialized: current node (abc123) is now a manager.
...
docker swarm join --token SWMTKN-1-... 192.168.x.x:2377
```

> 🎉 You now have a **1-node swarm** (your laptop is the **manager**).

---

### 📦 Step 2: Deploy a Service

Instead of `docker run`, we use `docker service create`:

```powershell
docker service create --name myweb --publish 8080:3000 vinithreddybanda/my-node-app:v1
```

> 🔍 Breakdown:
> - `--name myweb`: service name
> - `--publish 8080:3000`: expose port 8080 on host → 3000 in container
> - Uses your **public image** from Docker Hub

✅ Swarm creates **1 replica** of your app.

---

### 🔍 Step 3: Check the Service

```powershell
docker service ls
```

Output:
```
ID             NAME    MODE         REPLICAS   IMAGE                              PORTS
xyz789         myweb   replicated   1/1        vinithreddybanda/my-node-app:v1    *:8080->3000/tcp
```

```powershell
docker service ps myweb
```
→ Shows which “node” (your laptop) is running the task.

---

### 🌐 Step 4: Access Your App

Open:  
👉 [http://localhost:8080](http://localhost:8080)

You’ll see: **“Hello from Docker, Vinith! 🐳”**

✅ Your app is now running as a **Swarm service**!

---

### 📈 Step 5: Scale the Service

Want to handle more traffic? Scale to **3 replicas**:

```powershell
docker service scale myweb=3
```

Check:
```powershell
docker service ps myweb
```

You’ll see **3 running tasks** (all on your single node — in real life, they’d spread across machines).

> 💡 Swarm **load-balances** requests across replicas automatically!

---

### 🔄 Step 6: Update the Service (Rolling Update)

Push a new image to Docker Hub first (e.g., `v2` with updated message), then:

```powershell
docker service update --image vinithreddybanda/my-node-app:v2 myweb
```

Swarm will:
1. Stop 1 replica
2. Start new one with `v2`
3. Repeat until all are updated  
→ **Zero downtime!**

---

### 🧹 Step 7: Clean Up

Remove the service:
```powershell
docker service rm myweb
```

Leave the swarm (optional):
```powershell
docker swarm leave --force
```

> ⚠️ **Don’t leave** if you want to keep experimenting!

---

## 🎯 Why This Matters for You

- ✅ You now understand **orchestration basics** — a key DevOps concept
- ✅ Swarm is used in **real Indian startups & mid-sized companies**
- ✅ Great foundation before learning **Kubernetes**
- ✅ Shows depth in your **C-DAC/project interviews**

---

## 📌 Summary: Swarm vs Compose vs Plain Docker

| Tool | Use Case | Scale |
|------|--------|------|
| `docker run` | Single container | Local dev |
| **Docker Compose** | Multi-container app on **one host** | Local dev, small prod |
| **Docker Swarm** | Multi-container app across **many hosts** | Production clusters |
| Kubernetes | Large-scale, complex apps | Enterprise |

> ✅ **Learning path**: Docker → Compose → Swarm → Kubernetes

---

## 🎉 You’ve Completed UNIT-IV!

Let’s recap what you’ve now mastered:
1. ✅ **Installed Docker**  
2. ✅ **Created Dockerfiles**  
3. ✅ **Built & ran containers locally**  
4. ✅ **Pushed images to Docker Hub**  
5. ✅ **Deployed to Azure ACI**  
6. ✅ **Built CI/CD pipeline with GitHub Actions**  
7. ✅ **Used Docker for CLI tools**  
8. ✅ **Docker Compose for multi-container apps**  
9. ✅ **Docker Swarm for orchestration**

This is **more than most freshers know** — and exactly what C-DAC, TCS, Infosys, etc., look for!

---

## ▶️ What’s Next?

Would you like to:
- 🗂️ **Package this into a resume/project write-up**?
- 🧪 **Try a Kubernetes basics intro** (since you now know Swarm)?
- 🎯 **Focus on interview questions from UNIT-IV**?

Just let me know! 😊