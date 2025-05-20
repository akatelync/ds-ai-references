## Prerequisites

1. An active EC2 instance running in AWS
2. The private key (.pem) file for your EC2 instance
3. Visual Studio Code installed on your computer
4. Basic knowledge of AWS EC2 and SSH connections

## Step 1: Install the Remote SSH Extension

1. Open VS Code
2. Click on the Extensions icon in the Activity Bar (or press Ctrl+Shift+X)
3. Search for "Remote - SSH"
4. Click "Install" on the "Remote - SSH" extension by Microsoft

## Step 2: Configure Your SSH Key

1. Ensure your private key (.pem file) has the correct permissions:
    - On Mac/Linux: Open Terminal and run `chmod 400 path/to/your-key.pem`
    - On Windows: Right-click the .pem file → Properties → Security → Advanced → Make sure only your user has permissions

## Step 3: Configure SSH in VS Code

1. Press F1 (or Ctrl+Shift+P) to open the Command Palette
2. Type "Remote-SSH: Open Configuration File"
3. Select the SSH configuration file (usually ~/.ssh/config)
4. Add your EC2 configuration:
    
    ```
    Host ec2-nickname  HostName ec2-xx-xx-xx-xx.compute-1.amazonaws.com  User ec2-user  IdentityFile ~/path/to/your-key.pem
    ```
    
    (Replace with your actual EC2 public DNS and key path)

## Step 4: Connect to Your EC2 Instance

1. Press F1 (or Ctrl+Shift+P) again
2. Type "Remote-SSH: Connect to Host"
3. Select your EC2 instance from the list
4. A new VS Code window will open and connect to your instance
5. If prompted, select the platform of your remote machine (Linux)

## Step 5: Start Coding on Your EC2 Instance

1. In the new VS Code window, click "Open Folder"
2. Navigate to the directory you want to work with
3. You can now edit files, use the integrated terminal, and run commands directly on your EC2 instance

## Troubleshooting Tips

- If connection fails, check your EC2 security group to ensure port 22 (SSH) is open
- Verify your instance is running and the public DNS is correct
- Make sure your private key has the correct permissions
- Check that the username is correct (common options: ec2-user, ubuntu, admin)

Would you like me to explain any specific part of this process in more detail?