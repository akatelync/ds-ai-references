# Complete Guide to Syncing Obsidian Configurations with Git

## Part 1: Creating the Configuration Repository

1. Create a folder for your Obsidian configuration:
    
    ```bash
    mkdir ~/obsidian-config
    cd ~/obsidian-config
    ```
    
2. Initialize the Git repository:
    
    ```bash
    git init
    ```
    
3. Create a simple `.gitignore` file:
    
    ```bash
    echo ".obsidian/workspace.json
    .obsidian/workspace*
    .obsidian/cache/" > .gitignore
    ```
    
4. Copy your existing configuration:
    
    ```bash
    cp -r ~/Documents/repos/obsidian-vaults/kate-and-her-stuff/.obsidian .
    ```
    
5. Force add all files to Git:
    
    ```bash
    git add --force .obsidian
    ```
    
6. Verify files are staged:
    
    ```bash
    git status
    ```
    
7. Commit the configuration:
    
    ```bash
    git commit -m "Initial Obsidian configuration"
    ```
    
8. Create a new repository on GitHub (without README)
    
9. Connect to GitHub and push:
    
    ```bash
    git remote add origin git@github.com:yourusername/obsidian-config.git
    git push -u origin main
    ```
    

## Part 2: Applying to a Different Vault

1. Navigate to your target vault:
    
    ```bash
    cd ~/Documents/repos/obsidian-vaults/ds-ai-references
    ```
    
2. Back up any existing configuration:
    
    ```bash
    if [ -d ".obsidian" ]; then
      mv .obsidian .obsidian-backup-$(date +%Y%m%d)
    fi
    ```
    
3. Create a new `.obsidian` folder:
    
    ```bash
    mkdir -p .obsidian
    ```
    
4. Clone your configuration repository to a temporary location:
    
    ```bash
    git clone git@github.com:yourusername/obsidian-config.git ~/temp-obsidian-config
    ```
    
5. Copy configuration files from the repository:
    
    ```bash
    cp -r ~/temp-obsidian-config/.obsidian/* .obsidian/
    ```
    
6. Clean up the temporary repository:
    
    ```bash
    rm -rf ~/temp-obsidian-config
    ```
    
7. Open Obsidian and verify the configuration is working.
    

## Part 3: Updating Configuration When Needed

### When you make changes in your primary vault:

1. Navigate to your configuration repository:
    
    ```bash
    cd ~/obsidian-config
    ```
    
2. Copy the updated configuration:
    
    ```bash
    rm -rf .obsidian
    cp -r ~/Documents/repos/obsidian-vaults/kate-and-her-stuff/.obsidian .
    ```
    
3. Force add all files to Git:
    
    ```bash
    git add --force .obsidian
    ```
    
4. Commit and push changes:
    
    ```bash
    git commit -m "Update Obsidian configuration"
    git push
    ```
    

### To update your secondary vault:

1. Navigate to your secondary vault:
    
    ```bash
    cd ~/Documents/repos/obsidian-vaults/ds-ai-references
    ```
    
2. Clone the repository to a temporary location:
    
    ```bash
    git clone git@github.com:yourusername/obsidian-config.git ~/temp-obsidian-config
    ```
    
3. Backup your workspace file if you want to preserve it:
    
    ```bash
    if [ -f ".obsidian/workspace.json" ]; then
      cp .obsidian/workspace.json ~/workspace.json.backup
    fi
    ```
    
4. Copy updated configuration files:
    
    ```bash
    cp -r ~/temp-obsidian-config/.obsidian/* .obsidian/
    ```
    
5. Restore your workspace file if needed:
    
    ```bash
    if [ -f "~/workspace.json.backup" ]; then
      cp ~/workspace.json.backup .obsidian/workspace.json
      rm ~/workspace.json.backup
    fi
    ```
    
6. Clean up the temporary repository:
    
    ```bash
    rm -rf ~/temp-obsidian-config
    ```
    
7. Open Obsidian to verify the updated configuration.
    

This method gives you complete control over your Obsidian configuration and makes it easy to apply the same setup across multiple vaults!