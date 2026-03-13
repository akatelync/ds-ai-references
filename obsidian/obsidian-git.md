# Initializing Repo
Creating a Git repository for your Obsidian vault is a great way to track changes and back up your notes. Here's how to do it:

## 1. Initialize Git in your Obsidian vault

Navigate to your Obsidian vault folder in your terminal/command prompt:

```bash
cd path/to/your/obsidian/vault
```

Initialize the Git repository:

```bash
git init
```

## 2. Create a .gitignore file

Some Obsidian files like the workspace cache don't need to be tracked. Create a `.gitignore` file:

```bash
touch .gitignore
```

Add these common Obsidian files to ignore:

```
# Obsidian files to ignore
.obsidian/workspace.json
.obsidian/workspace
.obsidian/cache
.trash/
.DS_Store
```

## 3. Make your first commit

Add your files and commit them:

```bash
git add .
git commit -m "Initial commit of my Obsidian vault"
```

## 4. Connect to a remote repository (optional)

If you want to back up to GitHub, GitLab, etc.:

```bash
# Create a new repository on GitHub/GitLab first, then:
git remote add origin https://github.com/yourusername/your-vault-name.git
git branch -M main
git push -u origin main
```

## Tips for using Git with Obsidian

1. Commit regularly after making significant changes
2. Consider using a Git client like GitHub Desktop if you prefer a GUI
3. Be careful about syncing sensitive information if using a public repository