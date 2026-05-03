# Setup (Local) AI

## Retrieving and using models using Ollama
Purpose: Ollama allows you to host your own local LLM's. Interestingly, they also offer competing subscriptions for hosting cloud LLM's, which might be interesting if we want to kick out Claude entirely, but keep on using high performing models.

### Prerequisites

Windows 10/11
NVIDIA GPU with up to date drivers (recommended)
Windows Terminal (recommended for best experience)


### 1. Install Ollama
Download the installer from ollama.com and run it. Ollama installs as a background service and adds itself to the system tray.
Verify the installation:
```powershell
ollama --version
```

### 2. Configure Ollama to listen on all interfaces
By default Ollama only listens on localhost. If you plan to use it from WSL or other tools, set it to listen on all interfaces:
```powershell
[System.Environment]::SetEnvironmentVariable("OLLAMA_HOST", "0.0.0.0", "Machine")
```
Fully quit Ollama from the system tray and relaunch it for the change to take effect.
Verify it is listening correctly:
```powershell
netstat -ano | findstr "11434"
```
You should see `0.0.0.0:11434` in the output.

### 3. Pull a model
```powershell
ollama pull gemma4:e4b
```
Verify it downloaded:
```powershell
ollama list
```

### 4. Create a custom model with expanded context
Ollama defaults to 4096 tokens which is too small for agentic tool use. Create a custom model with a larger context window:
```powershell
"FROM gemma4:e4b`nPARAMETER num_ctx 32768`nPARAMETER num_predict 4096" | Out-File -Encoding utf8 $env:USERPROFILE\Modelfile-gemma4
ollama create gemma4-32k -f $env:USERPROFILE\Modelfile-gemma4
```
Verify the custom model appears:
```powershell
ollama list
```

### 5. Test the model
Quick sanity check that inference works:
```powershell
ollama run gemma4-32k "say hello"
```
Type /bye to exit the interactive session.

### 6. Verify the API is accessible
Ollama exposes an OpenAI-compatible REST API on port 11434. Test it:
```powershell
curl http://localhost:11434/api/tags
```
You should see a JSON response listing your models. This is the endpoint OpenCode and other tools connect to.

### Troubleshooting
ollama command not found — Reopen PowerShell after installation. Ollama adds itself to PATH but requires a fresh shell session.
GPU not detected — Update your NVIDIA drivers. Run ollama run gemma4-32k and check the Ollama logs in the system tray for GPU layer offloading confirmation.
Port 11434 not reachable from WSL — Make sure OLLAMA_HOST is set to 0.0.0.0 and Ollama was fully restarted after setting the variable. See the OpenCode setup guide for WSL networking details.
Model runs slowly — Check netstat -ano | findstr "11434" shows 0.0.0.0 not 127.0.0.1. If still slow, another process may be competing for VRAM — close browsers and other GPU-heavy applications.


## Open source agentic coding using OpenCode on Windows
Purpose: OpenCode is an open-source CLI agentic tool to replace Claude Code if you want to power it with local LLM's.

### Prerequisites
Windows 10/11 with Windows Terminal installed
Ollama installed and running (ollama.com)
Node.js installed (nodejs.org)
A model pulled in Ollama — e.g. ollama pull gemma4:e4b


### 1. Install OpenCode
Open Windows Terminal (PowerShell) and run:
```powershell
npm install -g opencode-ai
```
Find where npm installed it:
```powershell
npm config get prefix
```
Run OpenCode directly to verify it works:
```powershell
C:\Users\<you>\AppData\Roaming\npm\opencode.cmd
```
Add npm to PATH so you can just type opencode going forward:
```powershell
$npmPrefix = npm config get prefix
[System.Environment]::SetEnvironmentVariable("PATH", $env:PATH + ";$npmPrefix", "User")
```
Reopen PowerShell, then opencode should work directly.

### 2. Configure Ollama to accept connections
By default Ollama only listens on localhost. Set it to listen on all interfaces:
```powershell
[System.Environment]::SetEnvironmentVariable("OLLAMA_HOST", "0.0.0.0", "Machine")
```
Fully quit Ollama from the system tray and relaunch it.

### 3. Create the OpenCode config file
```powershell
mkdir $env:USERPROFILE\.config\opencode
notepad $env:USERPROFILE\.config\opencode\opencode.json
```
Paste and save:
```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "ollama": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "Ollama (local)",
      "options": {
        "baseURL": "http://localhost:11434/v1"
      },
      "models": {
        "gemma4-32k": { "tools": true }
      }
    }
  },
  "model": "ollama/gemma4-32k"
}
```

### 4. Add the auth placeholder
OpenCode expects an auth entry even for local models:
```powershell
mkdir $env:USERPROFILE\AppData\Local\opencode
'{"ollama": {"type": "api", "key": "ollama"}}' | Out-File -Encoding utf8 $env:USERPROFILE\AppData\Local\opencode\auth.json
```


### 5. Create a custom model with expanded context
Ollama defaults to 4096 tokens which breaks agentic tool use. Create a custom model with a larger context window:
```powershell
"FROM gemma4:e4b`nPARAMETER num_ctx 32768`nPARAMETER num_predict 4096" | Out-File -Encoding utf8 $env:USERPROFILE\Modelfile-gemma4
ollama create gemma4-32k -f $env:USERPROFILE\Modelfile-gemma4
```
Verify it appears:

```powershell
ollama list
```


### 6. Launch OpenCode
Navigate to your project and launch:
```powershell
cd C:\path\to\your\project
opencode
```
Important: Always use Windows Terminal or VS code Terminal, not the default PowerShell console. The TUI requires proper terminal support.
Inside OpenCode, use Shift+Enter to send messages (Enter adds a newline).
Type /models to confirm your Ollama model is available.

### Troubleshooting
opencode: command not found — Run source ~/.bashrc if in WSL, or reopen PowerShell after updating PATH.
Model not found / API connection error — Check Ollama is running (ollama list in PowerShell). Verify OLLAMA_HOST is set to 0.0.0.0 and Ollama was restarted after setting it.
Agent thinks but doesn't act — The model's context window is too small. Recreate your custom model with a higher num_ctx value.
TUI not responding — Switch to Windows Terminal. The default WSL or PowerShell console has limited TUI support.

## Installing support agents

### Review agents (Claude Code)


