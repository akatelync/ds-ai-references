g# Pushing a Local Folder to a New GitHub Repository

## Step 1: Navigate to your folder
```bash
cd /path/to/your/folder
```

## Step 2: Initialize Git (if not already done)
```bash
git init
```

## Step 3: Stage your files
```bash
git add .
```

## Step 4: Commit your files
```bash
git commit -m "Initial commit"
```

## Step 5: Create a GitHub repository and push in one command
```bash
gh repo create your-repo-name --private --source=. --push
```

## If you get an error about the remote "origin" already existing:

### Alternative Step 5: Create the repository first, then set up remote
```bash
# Create the repository without pushing
gh repo create your-repo-name --private

# Remove any existing origin remote
git remote remove origin

# Add the new repository as origin
git remote add origin https://github.com/yourusername/your-repo-name.git

# Push to the new repository
git push -u origin main
```

## If the repository already exists on GitHub
If you've already created the repository and need to connect your local folder:

```bash
# Remove any existing origin remote
git remote remove origin

# Add the GitHub repository as origin
git remote add origin https://github.com/yourusername/your-repo-name.git

# Push your code
git push -u origin main
```