# Setting Up SSH for GitHub

This guide will help you set up SSH authentication for GitHub, allowing you to securely push and pull without entering your password.

## Step 1: Check for Existing SSH Keys
```bash
ls -la ~/.ssh
```
Look for files named `id_rsa.pub`, `id_ed25519.pub`, or similar.

## Step 2: Generate a New SSH Key
If you don't have existing keys, generate new ones:

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```

When prompted, press Enter to accept the default file location. Optionally, add a secure passphrase.

## Step 3: Start the SSH Agent
```bash
eval "$(ssh-agent -s)"
```

## Step 4: Add Your SSH Key to the Agent
```bash
ssh-add ~/.ssh/id_ed25519
```

## Step 5: Copy Your Public Key
```bash
# macOS
pbcopy < ~/.ssh/id_ed25519.pub

# Alternative if pbcopy doesn't work
cat ~/.ssh/id_ed25519.pub
```

## Step 6: Add the Key to GitHub
1. Go to GitHub → Settings → SSH and GPG keys
2. Click "New SSH key"
3. Add a descriptive title (e.g., "MacBook Pro")
4. Paste your public key into the "Key" field
5. Click "Add SSH key"

## Step 7: Test Your Connection
```bash
ssh -T git@github.com
```
You should see a message: "Hi username! You've successfully authenticated..."

## Step 8: Configure Git to Use SSH
For a new repository:
```bash
git remote add origin git@github.com:username/repository.git
```

For an existing repository:
```bash
git remote set-url origin git@github.com:username/repository.git
```

## Step 9: Verify Remote URL
```bash
git remote -v
```
It should show: `origin git@github.com:username/repository.git`

## Step 10: Set global configuration
```shell
git config --global user.name "your_username"
git config --global user.email "your_email@example.com"
```
