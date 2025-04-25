## 📦 Phase 1: Environment Setup & Argo CD Installation

### Objective: Set up our environment for GitOps!

### Step 1: Generate SSH Key & Add to GitHub

Check for existing SSH keys:
Before you generate an SSH key, you can check to see if you have any existing SSH keys: [https://docs.github.com/en/authentication/connecting-to-github-with-ssh/checking-for-existing-ssh-keys]

Visit:
Setting up SSH-Agent in Windows

- [https://interworks.com/blog/2021/09/15/setting-up-ssh-agent-in-windows-for-passwordless-git-authentication/]
- [https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent?platform=windows]

Setting up SSH-Agent in MacOS:

- [https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent?platform=mac]

Windows (PowerShell - Run in Admin)
Installation:

```bash
Get-Service ssh-agent | Set-Service -StartupType Automatic -PassThru | Start-Service
```

Avoid restarting your system:

```bash
start-ssh-agent.cmd
```

Set up SSH Key Pair to Access a Git Remote Provider:

```bash
ssh-keygen -t ed25519 -C "your_primary_email_in_GitHub@example.com"
# Copy the contents of ~/.ssh/id_ed25519.pub to GitHub under Settings > SSH and GPG keys

Enter file in which to save the key (C:\Users\name/.ssh/id_ed25519): C:\Users\name/.ssh/id_ed25519

```

- Leave password blank → Click Enter
- You will then be shown the key’s randomart image to confirm creation
- Copy the contents of the public key to your clipboard. You can read this key with the following command:

```bash
cat path\to\ssh\key.pub
# For example: cat C:\Users\chastie\.ssh\id_ed25519.pub
# Locate SSH key in your folder and Copy As Path
```

Output: Copy the ENTIRE provided ssh key to paste to Github, including your email

```bash
ssh-ed25519-XXXXXXXXXXXXXXXXXXXXXXX your-git-email@email.com
```

Add SSH Key to GitHub

- Open your GitHub Account
- Click on your Profile Icon
- Select Settings
- Navigate to your sidebar and select SSH and GPG keys
- Select New SSH key
- Enter the following information:
  -- Title SSH key: auth_argo_cd_demo
  -- Key: Paste the ENTIRE provided ssh key from your terminal including your email

### Step 2: Create a GitHub Personal Access Token (PAT)

- Go to: Settings > Developer Settings > Tokens (classic)
- Generate a token with the following scopes:
  - `repo`
  - `workflow`

### Step 3: Create a Local K8s Cluster

```bash
kind create cluster --name argocd-demo
```

### Step 4: Configure K8s Context

In PowerShell (Run as Admin) or Terminal:

```bash
cat ~/.kube/config
```

```bash
kubectl config use-context kind-argocd-demo
```

```bash
# Windows (Run in Admin)
set KUBE_CONFIG="~/.kube/config"

# macOS/Linux
export KUBE_CONFIG="~/.kube/config"
```

### Step 5: Deploy Helm Chart with Terraform

Return to VS Code terminal with argocd_demo repo:

```bash
terraform init
```

```bash
terraform plan
```

```bash
terraform apply
# Confirm with 'yes'
```

### Step 6: Install OpenLens

- **Windows**:
  ```bash
  choco install -y openlens
  ```
- **macOS**:
  ```bash
  brew install --cask openlens
  ```

### Step 7: Use OpenLens

- Open OpenLens OR in VS Code terminal of argocd_demo,connect to `kind-argocd-demo` cluster via Terminal

- Run port forwarding:
  ```bash
  kubectl port-forward svc/argocd-server -n argocd 8080:80
  ```

### Step 8: Access Argo CD

- Open web browser
- Visit: [http://localhost:8080](http://localhost:8080)
- Username: `admin`
- Password Options:

Option 1:

- Go to OpenLens
- Config → Secrets
- In the top right, change Namespace from default to argocd
- Click on the argocd-initial-admin-secret row
- password box → expose password

Option 2:

```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

- Login to ArgoCD

### Phase 1 Complete! We successfully set up our environment for GitOps!

---
