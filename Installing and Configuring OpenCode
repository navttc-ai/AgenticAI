Welcome to the world of agentic coding! This guide will walk you through setting up **OpenCode**, a powerful AI-driven coding assistant, and integrating it with your **Git** workflow and **Python** development environment.

Whether you are working on complex **TensorFlow Federated** research or building local AI tools, this tutorial will help you transform your terminal into a collaborative workspace.

---

## 1. Setting Up the Foundation: WSL (Windows Subsystem for Linux)

### The "Why" and "What"
Think of **WSL** as a "translator" or a "secret passage" that allows you to run a full Linux environment directly inside your Windows computer. For developers, Linux is the "native language" of most AI tools and Git workflows. While you can use native Windows, OpenCode recommends WSL because it is faster, more stable, and handles Python dependencies (like those in TensorFlow) much more effectively.

### Installation Steps
1.  Open **PowerShell** as an Administrator (Right-click > Run as Administrator).
2.  Type the following command to install the Linux kernel:
    ```powershell
    # This command installs the Ubuntu Linux distribution by default
    wsl --install
    ```
3.  **Restart your computer.**
4.  Once restarted, open your Start menu, type "Ubuntu," and open it. To verify it’s working, type:
    ```bash
    wsl
    ```

### Micro-Project: The Linux Handshake
Inside your new Ubuntu terminal, try creating your first Linux folder:
`mkdir my-first-repo && cd my-first-repo`. Then type `pwd` to see your "Path to the Working Directory."

### Official Sources & Documentation
*   [Microsoft WSL Documentation](https://learn.microsoft.com/en-us/windows/wsl/install)
*   [OpenCode Official Docs](https://opencode.ai/docs)

---

## 2. Preparing the Environment: Node.js & Updates

### The "Why" and "What"
Before installing OpenCode, your Linux system needs to be up to date, and it needs **Node.js**. Node.js is a runtime environment that allows OpenCode (which is built with JavaScript/TypeScript) to run on your machine. We use **npm** (Node Package Manager) to "download and install" the tool.

### Code Examples
Run these inside your **WSL/Ubuntu terminal**:

```bash
# 1. Update the list of available software packages
sudo apt update

# 2. Upgrade the software currently installed
sudo apt upgrade -y

# 3. Install 'curl' (a tool to download files from the internet)
sudo apt install curl -y

# 4. Download the Node.js installation script (LTS version)
curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash -

# 5. Install Node.js
sudo apt install nodejs -y

# 6. Verify the installation
node -v
npm -v
```

### Micro-Project: Version Check
Run `node -v`. If it returns a number (like `v20.x.x`), you have successfully built the engine that will run your AI tools!

---

## 3. Installing OpenCode (The CLI Agent)

### The "Why" and "What"
**OpenCode** is an "Agentic Coding Tool." Unlike a standard chat window, it can "see" your files, understand your Git history, and actually write code directly into your project. Installing the **CLI (Command Line Interface)** version is superior for AI developers because it lives exactly where your code lives.

### Code Examples
**Option A: Recommended (WSL/Linux)**
```bash
# Install OpenCode globally on your system
npm install -g opencode-ai

# Verify the installation
opencode --version
```

**Option B: Native Windows (Alternative)**
If you prefer not to use WSL, you can use Windows-based package managers:
```powershell
# Using Chocolatey
choco install opencode

# OR using Scoop
scoop install opencode
```

### Micro-Project: First Launch
Type `opencode` in your terminal. You should see a professional interactive UI appear in your command prompt.

### Official Sources & Documentation
*   [OpenCode GitHub Repository](https://github.com/anomalyco/opencode)

---

## 4. Connecting the "Brain": AI Models

### The "Why" and "What"
OpenCode is the "body" (the interface), but it needs a "brain" (an AI Model) to think. You can connect it to industry leaders like **OpenAI (GPT-4/5)**, **Anthropic (Claude)**, or even run local models via **Ollama** to keep your research private.

### Code Examples
Inside the OpenCode interactive terminal, use these slash commands:

```text
# Step 1: Open the connection menu
/connect

# Step 2: Choose your provider (e.g., OpenAI, Anthropic, Gemini, or Ollama)
# Step 3: Select your specific model
/model
```
*Note: If using OpenAI or Claude, you will be prompted to paste your API Key.*

### Micro-Project: The Theme Switch
Customize your workspace to your liking! Try typing `/theme` inside OpenCode to cycle through visual styles.

---

## 5. Mastering the Git & AI Workflow

### The "Why" and "What"
This is where the magic happens for an AI developer. OpenCode understands **Git**. You can ask it to summarize changes, find bugs in a specific branch, or even write your commit messages.

### Sufficient Code Examples
Navigate to your research project (e.g., a TensorFlow Federated project) and launch the agent:

```bash
# Navigate to your project folder
cd /mnt/d/Research/FederatedLearning

# Launch OpenCode
opencode
```

**Typical Prompts to use inside OpenCode:**
*   `"Explain this project."` — Great for jumping into a complex TFF codebase.
*   `"Find bugs and suggest fixes."` — It will scan your Python logic.
*   `"Refactor the TensorFlow code for better GPU performance."` — Targeted optimization.
*   `"Commit my recent changes with a descriptive message."` — Automates the Git process.

### Understanding Agents
*   **Plan Agent (Tab key):** Use this for exploration, asking questions, and "thinking" about the architecture.
*   **Build Agent (Tab key):** Use this when you want the AI to actually write, edit, or delete code in your files.

### Micro-Project: The Automated Readme
Navigate to a small project, open `opencode`, and type: `"Analyze this repo and write a detailed README.md file."` Watch as it creates the file for you!

---

## 6. Pro Integration: OpenCode + VS Code

### The "Why" and "What"
You don't have to choose between your favorite editor and the AI agent. You can run them together. This allows you to watch the code change in real-time in VS Code while the AI works in the terminal.

### Step-by-Step Breakdown
1.  Open your project folder in **VS Code**.
2.  Open the **Integrated Terminal** (`Ctrl + ` ` or backtick).
3.  Ensure your terminal is set to **WSL/Ubuntu**.
4.  Type `opencode`.
5.  Now, you can use the AI to generate code, and you'll see the files update instantly in the VS Code editor window.

---

## Summary Cheat Sheet

| Command | Purpose |
| :--- | :--- |
| `/help` | List all available AI commands |
| `/connect` | Link your API keys (OpenAI, Gemini, etc.) |
| `/model` | Switch between models (e.g., Claude 3.5 to GPT-4o) |
| `Tab` | Switch between **Build** (Writing code) and **Plan** (Analysis) |
| `opencode` | Start the agent in any directory |

**For your AI Research:** Use OpenCode to handle the "boilerplate" tasks (Dockerfiles, Unit tests, Git commits) so you can focus on the high-level **Federated Learning** logic and **TensorFlow** model architecture.
