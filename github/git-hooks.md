To automatically fix errors found in pre-commit, create this file under `.git/hooks/pre-commit`

```bash
#!/usr/bin/env bash
# File modified to auto-stage pre-commit fixes, including non-zero exit codes for fixable hooks

# Path to Python in your virtual environment
INSTALL_PYTHON=/Users/kate.castillo/Library/CloudStorage/OneDrive-insidemedia.net/Desktop/personal-repos/judith-assistance/.venv/bin/python3

# Run pre-commit on staged files
if [ -x "$INSTALL_PYTHON" ]; then
    "$INSTALL_PYTHON" -m pre_commit run --files $(git diff --name-only --cached)
elif command -v pre-commit > /dev/null; then
    pre-commit run --files $(git diff --name-only --cached)
else
    echo '`pre-commit` not found. Did you forget to activate your virtualenv?' 1>&2
    exit 1
fi

# Capture the exit code
EXIT_CODE=$?

# Stage any files modified by pre-commit
MODIFIED_FILES=$(git diff --name-only)
if [ -n "$MODIFIED_FILES" ]; then
    git add $MODIFIED_FILES
fi

# Allow the commit to proceed if the exit code is 0 (no fixes) or 1 (fixes applied)
if [ $EXIT_CODE -eq 0 ] || [ $EXIT_CODE -eq 1 ]; then
    exit 0
else
    echo "Pre-commit checks failed with critical errors. Please review and fix issues manually."
    exit $EXIT_CODE
fi
```

and run this `chmod +x .git/hooks/pre-commit`