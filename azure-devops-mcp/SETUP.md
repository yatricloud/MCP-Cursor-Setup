# Azure DevOps MCP Setup Guide

This guide will help you set up the Azure DevOps Model Context Protocol (MCP) server in Kiro from scratch on a new machine across different operating systems.

## Prerequisites

- Kiro IDE
- Access to an Azure DevOps organization
- Node.js (for npx command)

## Step 1: Install Azure CLI

### macOS

Using Homebrew (recommended):
```bash
# Install Azure CLI
brew install azure-cli

# Verify installation
az --version
```

Alternative - Using installer:
```bash
# Download and install
curl -L https://aka.ms/InstallAzureCli | bash
```

### Windows

**Option 1: Using winget (Windows 10/11)**
```powershell
# Install Azure CLI
winget install Microsoft.AzureCLI

# Verify installation
az --version
```

**Option 2: Using Chocolatey**
```powershell
# Install Chocolatey first if not installed
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))

# Install Azure CLI
choco install azure-cli

# Verify installation
az --version
```

**Option 3: MSI Installer**
1. Download the MSI installer from: https://aka.ms/installazurecliwindows
2. Run the installer
3. Restart your terminal/PowerShell
4. Verify: `az --version`

### Linux

**Ubuntu/Debian:**
```bash
# Update package index
sudo apt-get update

# Install required packages
sudo apt-get install ca-certificates curl apt-transport-https lsb-release gnupg

# Add Microsoft signing key
curl -sL https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/microsoft.gpg > /dev/null

# Add Azure CLI repository
AZ_REPO=$(lsb_release -cs)
echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ $AZ_REPO main" | sudo tee /etc/apt/sources.list.d/azure-cli.list

# Update and install
sudo apt-get update
sudo apt-get install azure-cli

# Verify installation
az --version
```

**CentOS/RHEL/Fedora:**
```bash
# Import Microsoft repository key
sudo rpm --import https://packages.microsoft.com/keys/microsoft.asc

# Add Microsoft repository
echo -e "[azure-cli]
name=Azure CLI
baseurl=https://packages.microsoft.com/yumrepos/azure-cli
enabled=1
gpgcheck=1
gpgkey=https://packages.microsoft.com/keys/microsoft.asc" | sudo tee /etc/yum.repos.d/azure-cli.repo

# Install Azure CLI
sudo yum install azure-cli
# OR for newer versions: sudo dnf install azure-cli

# Verify installation
az --version
```

**Arch Linux:**
```bash
# Install from AUR
yay -S azure-cli
# OR
sudo pacman -S azure-cli

# Verify installation
az --version
```

## Step 2: Authenticate with Azure

Authenticate using device code flow (recommended for MFA-enabled accounts):

```bash
# Login with device code
az login --use-device-code
```

Follow the instructions to:
1. Open the provided URL in your browser
2. Enter the device code
3. Complete authentication
4. Select your desired subscription when prompted

## Step 3: Install Azure DevOps Extension

The Azure DevOps CLI extension will be installed automatically when first used, but you can install it manually:

```bash
# Configure Azure DevOps (this will prompt to install the extension)
az devops configure --defaults organization=https://dev.azure.com/YOUR_ORG_NAME
```

Replace `YOUR_ORG_NAME` with your actual Azure DevOps organization name.

## Step 4: Configure Kiro MCP Server

Create the MCP configuration file in your workspace:

### macOS/Linux
```bash
# Create the directory structure
mkdir -p .kiro/settings
```

### Windows (PowerShell)
```powershell
# Create the directory structure
New-Item -ItemType Directory -Force -Path ".kiro\settings"
```

### Windows (Command Prompt)
```cmd
# Create the directory structure
mkdir .kiro\settings
```

Create `.kiro/settings/mcp.json` with the following content:

```json
{
    "mcpServers": {
        "azure-devops": {
            "command": "npx",
            "args": [
                "-y",
                "@azure-devops/mcp",
                "YOUR_ORG_NAME"
            ],
            "disabled": false,
            "autoApprove": []
        }
    }
}
```

Replace `YOUR_ORG_NAME` with your Azure DevOps organization name.

## Step 5: Restart Kiro

Restart Kiro IDE or reconnect the MCP server from the MCP Server view in the Kiro feature panel.

## Step 6: Test the Connection

Once Kiro restarts, you can test the MCP connection by asking Kiro to:

```
get list of ado projects
```

## Troubleshooting

### Authentication Issues

If you encounter authentication errors:

1. **Multi-Factor Authentication**: Use device code flow instead of regular login:
   
   **macOS/Linux:**
   ```bash
   az login --use-device-code
   ```
   
   **Windows (PowerShell/CMD):**
   ```powershell
   az login --use-device-code
   ```

2. **Token Expiration**: Re-authenticate:
   
   **macOS/Linux:**
   ```bash
   az login --use-device-code
   ```
   
   **Windows (PowerShell/CMD):**
   ```powershell
   az login --use-device-code
   ```

3. **Wrong Tenant**: Specify tenant explicitly:
   
   **macOS/Linux:**
   ```bash
   az login --tenant YOUR_TENANT_ID
   ```
   
   **Windows (PowerShell/CMD):**
   ```powershell
   az login --tenant YOUR_TENANT_ID
   ```

### MCP Server Issues

1. **Server Not Starting**: Check the MCP logs in Kiro
2. **Organization Not Found**: Verify the organization name in the config
3. **Package Not Found**: The `@azure-devops/mcp` package will be downloaded automatically via `npx`

### Common Error Messages

- **"ChainedTokenCredential authentication failed"**: Run `az login --use-device-code`
- **"Azure CLI could not be found"**: Install Azure CLI with `brew install azure-cli`
- **"Not enough non-option arguments"**: Check that organization name is specified in MCP config

## Available MCP Tools

Once configured, you'll have access to tools for:

- **Projects**: List and manage projects
- **Repositories**: Browse repos, branches, pull requests
- **Work Items**: Create, update, and query work items
- **Builds**: View build definitions and runs
- **Releases**: Manage release pipelines
- **Wiki**: Read and write wiki pages
- **Search**: Search across code, work items, and wiki

## Configuration Files

### Workspace MCP Config
Location: `.kiro/settings/mcp.json`
- Project-specific configuration
- Takes precedence over user-level config

### User MCP Config (Optional)
Location: `~/.kiro/settings/mcp.json`
- Global configuration across all workspaces
- Useful for commonly used MCP servers

## OS-Specific Notes

### macOS
- Homebrew is the recommended package manager
- Zsh completions are automatically installed with Azure CLI
- Use Terminal or iTerm2 for command execution

### Windows
- PowerShell is recommended over Command Prompt
- Windows Terminal provides the best experience
- Some commands may require running as Administrator
- Path separators use backslashes (`\`) instead of forward slashes (`/`)

### Linux
- Package manager varies by distribution
- Bash completions are typically installed automatically
- May need to reload shell or source completion files
- Some distributions may require additional dependencies

## Security Notes

- The MCP server uses your Azure CLI authentication
- No passwords or tokens are stored in the configuration
- Authentication is handled through Azure's standard OAuth flow
- Permissions are based on your Azure DevOps access level

## Example Usage

After setup, you can use commands like:

- "get list of ado projects"
- "show me repositories in project X"
- "create a work item"
- "list my pull requests"
- "search for code containing 'function'"

## Support

If you encounter issues:

1. Check MCP logs in Kiro
2. Verify Azure CLI authentication: 
   - **macOS/Linux:** `az account show`
   - **Windows:** `az account show`
3. Test Azure DevOps access:
   - **macOS/Linux:** `az devops project list`
   - **Windows:** `az devops project list`
4. Restart Kiro IDE
5. Reconnect MCP server from Kiro's MCP Server view

## Platform-Specific Troubleshooting

### macOS
- If Homebrew installation fails, try: `xcode-select --install`
- For permission issues: `sudo chown -R $(whoami) /opt/homebrew`

### Windows
- If winget is not available, update Windows or install App Installer from Microsoft Store
- For PowerShell execution policy issues: `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser`
- If MSI installer fails, try running as Administrator

### Linux
- For Ubuntu/Debian, if GPG key import fails: `curl -sL https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -`
- For CentOS/RHEL, if repository issues occur: `sudo yum clean all && sudo yum makecache`
- For permission issues: Check that your user has sudo privileges