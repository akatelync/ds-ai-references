# Complete Step-by-Step Guide: Auto-Commit Multiple Git Repositories Before Sleep

## Step 1: Create the Multi-Repository Commit Script

1. Create a new file named `multi-repo-commit.sh` in a location of your choice (e.g., `~/scripts/`):

```bash
#!/bin/bash

# Function to commit and push changes for a repository
commit_and_push() {
    REPO_PATH="$1"
    REPO_NAME="$2"
    
    echo "Processing $REPO_NAME repository..."
    
    # Change to the repository directory
    cd "$REPO_PATH" || { echo "Failed to access $REPO_PATH"; return 1; }
    
    # Check if this is a git repository
    if [ ! -d ".git" ]; then
        echo "Error: $REPO_PATH is not a git repository"
        return 1
    fi
    
    # Get current branch name
    BRANCH=$(git rev-parse --abbrev-ref HEAD)
    
    # Add all changes
    git add .
    
    # Commit changes if there are any
    if git diff --cached --quiet; then
        echo "No changes to commit in $REPO_NAME"
    else
        git commit -m "Auto-commit before sleep: $(date '+%Y-%m-%d %H:%M:%S')"
        echo "Changes committed in $REPO_NAME"
    fi
    
    # Push changes
    if git push origin "$BRANCH"; then
        echo "Successfully pushed changes for $REPO_NAME"
    else
        echo "Failed to push changes for $REPO_NAME"
    fi
    
    echo "Finished processing $REPO_NAME repository"
    echo "----------------------------------------"
}

# Repository 1 - Obsidian Vault
REPO1_PATH="/Users/kate/Documents/repos/obsidian-vaults/kate-and-her-stuff"
REPO1_NAME="kate-and-her-stuff"

# Repository 2 - Your Second Repo
REPO2_PATH="/Users/kate/Documents/repos/obsidian-vaults/ds-ai-references"
REPO2_NAME="ds-ai-references"

# Process both repositories
commit_and_push "$REPO1_PATH" "$REPO1_NAME"
commit_and_push "$REPO2_PATH" "$REPO2_NAME"

echo "Auto-commit process completed for all repositories"
```

2. Replace the paths in the script with your actual repository paths:
    
    - `REPO1_PATH`: Full path to your first repository (Obsidian vault)
    - `REPO2_PATH`: Full path to your second repository
3. Make the script executable:
    

```bash
chmod +x ~/scripts/multi-repo-commit.sh
```

4. Test the script manually to ensure it works:

```bash
~/scripts/multi-repo-commit.sh
```

## Step 2: Set Up Pre-Sleep Trigger (macOS)

### Using sleepwatcher (recommended for macOS):

1. Install sleepwatcher using Homebrew:

```bash
brew install sleepwatcher
```

2. Set up sleepwatcher to start automatically:

```bash
brew services start sleepwatcher
```

3. Create a `.sleep` file in your home directory:

```bash
echo '~/scripts/multi-repo-commit.sh >> ~/logs/git-sleep.log 2>&1' > ~/.sleep
```

4. Make the `.sleep` file executable:

```bash
chmod +x ~/.sleep
```

5. Create the logs directory:

```bash
mkdir -p ~/logs
```

## Step 2 (Alternative): Set Up Pre-Sleep Trigger for Linux

1. Create a systemd service file:

```bash
sudo nano /etc/systemd/system/pre-sleep.service
```

2. Add the following content:

```
[Unit]
Description=Commands to run before sleep
Before=sleep.target

[Service]
Type=oneshot
User=yourusername
ExecStart=/home/yourusername/scripts/multi-repo-commit.sh

[Install]
WantedBy=sleep.target
```

3. Replace `yourusername` with your actual username
    
4. Enable the service:
    

```bash
sudo systemctl enable pre-sleep.service
```

## Step 2 (Alternative): Set Up Pre-Sleep Trigger for Windows

1. Create a batch file `run-git-commit.bat`:

```batch
@echo off
echo Running Git commit script at %date% %time% >> C:\logs\git-sleep.log
C:\path\to\bash.exe -c "/path/to/multi-repo-commit.sh" >> C:\logs\git-sleep.log 2>&1
```

2. Open Task Scheduler:
    - Create a new task
    - In Triggers tab, click "New"
    - Select "On an event"
    - For Log, select "System"
    - For Source, select "Power-Troubleshooter"
    - For Event ID, enter 42
    - In Actions tab, set it to start the batch file

## Step 3: Test the Setup

1. Make some changes in both repositories
    
2. Put your computer to sleep (close the lid or use the sleep option)
    
3. Wake up your computer after a few seconds
    
4. Check if the changes were committed and pushed by examining:
    
    - The log file (`~/logs/git-sleep.log` or equivalent)
    - The Git history in both repositories:
        
        ```bash
        cd /path/to/repogit log -1
        ```
        

## Step 4: Troubleshooting

If the script doesn't run before sleep:

1. Check the log files for errors
    
2. Ensure the paths in your script are correct (use absolute paths)
    
3. Make sure Git is configured properly in both repositories:
    
    ```bash
    cd /path/to/repo
    git config --get remote.origin.url
    ```
    
4. Verify your SSH or Git credentials are set up to work without manual intervention
    
5. For macOS, check if sleepwatcher is running:
    
    ```bash
    brew services list | grep sleepwatcher
    ```
    

Your setup should now automatically commit and push changes from both repositories whenever your computer goes to sleep!