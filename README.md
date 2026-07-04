# Flattrade MCP — Getting Started Guide

> Connect your AI assistant (Claude Desktop, or any other MCP-compatible client) to your Flattrade trading account.
> No coding or installation of developer tools required — just download a single binary file for your operating system.

---

## 1. Download the Binary

Download the file that matches your computer from [DOWNLOAD_LINK]:

| Your OS                          | File Name                         |
| --------------------------------- | --------------------------------- |
| Windows (most PCs)                | `flattrade-mcp-windows-amd64.exe` |
| Windows ARM (Surface Pro X etc.)  | `flattrade-mcp-windows-arm64.exe` |
| macOS Apple Silicon (M1/M2/M3/M4) | `flattrade-mcp-macos-m1`          |
| macOS Intel (older Macs)          | `flattrade-mcp-macos-intel`       |
| Linux 64-bit                      | `flattrade-mcp-linux-amd64`       |
| Linux ARM                         | `flattrade-mcp-linux-arm64`       |

> **Not sure which Mac you have?** Apple menu → About This Mac. "Apple M1/M2/M3/M4" → use the macOS Apple Silicon binary. "Intel" → use the macOS Intel binary.
>
> **Not sure which Windows you have?** Almost certainly `flattrade-mcp-windows-amd64.exe`.

---

## 2. What You'll Need

1. The binary file you just downloaded
2. Your **Flattrade Client ID** (e.g. `FT028416`)
3. A **Flattrade API Key** and **API Secret** (see Step 3 if you don't have these yet)
4. **Claude Desktop** or another MCP-compatible AI assistant installed

---

## 3. Get Your API Key (One-Time Setup)

Skip this step if you already have an API Key and Secret.

1. Log in to [Wall](https://wall.flattrade.in)
2. Go to **Pi** (top menu) → click **CREATE NEW API KEY**

   ![Pi menu — Create New API Key](https://flattrade.s3.ap-south-1.amazonaws.com/promo/pi20.png)

3. Choose your order volume:

   | Will you place more than 10 orders per second? | Select |
   | ------------------------------------------------ | ------ |
   | No (most users)                                   | **No** |
   | Yes (high-frequency / algo use)                   | **Yes** |

   ![Order volume selection](https://flattrade.s3.ap-south-1.amazonaws.com/pidoc/Selectmorethan10OrLessThan10.png)

### If you selected "No" (most users)

![Walkthrough — less than 10 orders per second](https://flattrade.s3.ap-south-1.amazonaws.com/pidoc/needToGif.gif)

1. Enter your **Primary IP Address** (your computer's public IP, numbers only — e.g. `103.45.67.89`). Secondary IP is optional.
2. Fill in the app details:
   - **App Name / Short Name:** any name you choose
   - **Redirect URL:** `http://localhost:7080`
   - **Postback URL:** leave blank (not needed for this MCP server)
   - **Description:** a short note about what the app is for
3. Review the summary, accept the Terms & Conditions, and submit

### If you selected "Yes" (more than 10 orders/sec)

1. Same **IP Configuration** and **App/URL Configuration** as above
2. Additionally upload your strategy details:

   | Field   | Description                              |
   | ------- | ----------------------------------------- |
   | Strategy | Your strategy for this API key           |
   | Segment  | The market segment for this API key      |
   | File     | Supporting file for the selected segment |

3. Review the summary, accept the Terms & Conditions, and submit

### Both paths — after submitting

1. Your request shows as **Pending Approval**:

   ![Pending approval](https://flattrade.s3.ap-south-1.amazonaws.com/pidoc/programPending.png)

2. Once approved, your API key is generated:

   ![API key approved](https://flattrade.s3.ap-south-1.amazonaws.com/pidoc/programKey.png)

3. Click the **eye icon** to reveal your Secret Key:

   ![Reveal secret key](https://flattrade.s3.ap-south-1.amazonaws.com/pidoc/programSecret.png)

4. Copy your **API Key** and **API Secret** — you'll need both in Step 5

---

## 4. Place the Binary File

Put the binary somewhere permanent on your computer.

**Windows**

1. Create a folder, e.g. `C:\Tools\`
2. Move the downloaded `.exe` into it
3. Final path example: `C:\Tools\flattrade-mcp-windows-amd64.exe`

**macOS**

```bash
mkdir -p ~/flattrade
mv ~/Downloads/flattrade-mcp-macos-m1 ~/flattrade/flattrade-mcp
chmod +x ~/flattrade/flattrade-mcp
```

_(Replace `flattrade-mcp-macos-m1` with the file you downloaded)_

**Linux**

```bash
mkdir -p ~/flattrade
mv ~/Downloads/flattrade-mcp-linux-amd64 ~/flattrade/flattrade-mcp
chmod +x ~/flattrade/flattrade-mcp
```

---

## 5. Configure Your AI Assistant

Pick whichever option matches how you use Claude.

### Option A: Claude Desktop (Edit Config File)

1. Open the config file:
   - **Windows:** `%APPDATA%\Claude\claude_desktop_config.json`
   - **macOS:** `~/Library/Application Support/Claude/claude_desktop_config.json` — quickly open it with:
     ```bash
     open ~/Library/Application\ Support/Claude/claude_desktop_config.json
     ```
     If the file doesn't exist yet, create it (e.g. in TextEdit or VS Code) with the JSON block below as its full content.
   - **Linux:** `~/.config/Claude/claude_desktop_config.json`

2. Add the block below, using the path where you placed the binary and your own credentials:

```json
{
  "mcpServers": {
    "flattrade": {
      "command": "/path/to/flattrade-mcp",
      "env": {
        "FLATTRADE_USER_ID": "YOUR_CLIENT_ID",
        "FLATTRADE_API_KEY": "YOUR_API_KEY",
        "FLATTRADE_API_SECRET": "YOUR_API_SECRET"
      }
    }
  }
}
```

- Windows example `command`: `"C:\\Tools\\flattrade-mcp-windows-amd64.exe"`
- macOS/Linux example `command`: `"/Users/yourname/flattrade/flattrade-mcp"`

3. Save the file, then **completely quit Claude Desktop (Cmd+Q / close from the system tray)** and reopen it
4. Go to **Settings → Developer** and confirm the `flattrade` server is listed

### Option B: Claude Code (Terminal Command)

If you use the Claude Code CLI instead of (or in addition to) Claude Desktop, you can add the server with a single command — no manual JSON editing needed.

**Prerequisites**

Claude Code requires Node.js.

1. Download and install it from [nodejs.org](https://nodejs.org/dist/v22.17.1/node-v22.17.1.pkg)
2. Open Command Prompt / Terminal and verify with:
   ```bash
   node --version
   ```

**macOS / Linux (bash/zsh)**

```bash
claude mcp add flattrade \
  --scope user \
  --env FLATTRADE_USER_ID="YOUR_CLIENT_ID" \
  --env FLATTRADE_API_KEY="YOUR_API_KEY" \
  --env FLATTRADE_API_SECRET="YOUR_API_SECRET" \
  -- /path/to/flattrade-mcp
```

**Windows — PowerShell**

Use the backtick (`` ` ``) for line continuation instead of `\`:

```powershell
claude mcp add flattrade `
  --scope user `
  --env FLATTRADE_USER_ID="YOUR_CLIENT_ID" `
  --env FLATTRADE_API_KEY="YOUR_API_KEY" `
  --env FLATTRADE_API_SECRET="YOUR_API_SECRET" `
  -- "C:\Tools\flattrade-mcp-windows-amd64.exe"
```

**Windows — Command Prompt (cmd.exe)**

`cmd.exe` doesn't support line continuation the same way — put everything on one line:

```cmd
claude mcp add flattrade --scope user --env FLATTRADE_USER_ID=YOUR_CLIENT_ID --env FLATTRADE_API_KEY=YOUR_API_KEY --env FLATTRADE_API_SECRET=YOUR_API_SECRET -- "C:\Tools\flattrade-mcp-windows-amd64.exe"
```

Use whichever version matches your terminal, and replace the binary path with the actual path where you placed it (Step 4), along with your own Client ID, API Key, and API Secret.

- **Windows example path:** `C:\Tools\flattrade-mcp-windows-amd64.exe`
- **macOS/Linux example path:** `/Users/yourname/flattrade/flattrade-mcp` or `/home/yourname/flattrade/flattrade-mcp`

Verify it was added:

```bash
claude mcp list
```

> **Note:** Claude Code (terminal) and Claude Desktop use separate config files, so you can set up both at the same time without conflict.

### Other MCP-Compatible Assistants

Most MCP clients use the same `mcpServers` JSON structure shown above, in their own config file. Check your assistant's documentation for the exact config file location, then add the same `flattrade` block with your binary path and credentials.

---

## 6. Log In to Flattrade

In your AI assistant, type:

```
Login to Flattrade
```

1. The assistant replies with a login URL — open it in your browser
2. Log in with your Flattrade username and password
3. Review the permissions screen and click **Allow**
4. Your browser shows a "Connected" page — you can close the tab
5. Back in your AI assistant, type:

```
Check if login is done
```

If it replies **"Authenticated with Flattrade"**, you're ready to go.

---

## 7. Start Trading

Try a few things:

```
What is the current price of NIFTY?
```

```
Show me my positions
```

```
How much cash do I have available?
```

```
Buy 1 share of Infosys at market price
```

```
Show me the NIFTY option chain at 24000
```

---

## Daily Login

Your session is saved for the day.

- Still logged in? Just start using it.
- Session expired? Type `Login to Flattrade` again and repeat the browser login.

---

## What Can You Do?

| Action            | Just Say...                                          |
| ----------------- | ----------------------------------------------------- |
| Check stock price | "What is the price of [STOCK]?"                       |
| View positions    | "Show my positions"                                   |
| View holdings     | "Show my holdings"                                    |
| Check funds       | "How much margin do I have?"                          |
| Place buy order   | "Buy [QTY] [STOCK] at [PRICE]"                        |
| Place sell order  | "Sell [QTY] [STOCK] at [PRICE]"                       |
| Cancel order      | "Cancel order [ORDER ID]"                             |
| View order book   | "Show my orders"                                      |
| Live prices       | "Subscribe to live prices for NIFTY"                  |
| Set GTT order     | "Set a GTT to buy [STOCK] if price drops to [PRICE]"  |
| Option chain      | "Show NIFTY option chain at [STRIKE]"                 |
| Historical data   | "Show daily chart for INFY from 01-01-2026"           |
| Logout            | "Logout of Flattrade"                                 |

---

## Troubleshooting

**"Not authenticated — call login first"**
Your session expired. Type `Login to Flattrade` and complete the browser login again.

**Browser shows "This site can't be reached" on localhost:7080**
The MCP server isn't running. Close and reopen your AI assistant, then try logging in again.

**"Port 7080 busy"**
Something else on your computer is using port 7080. Close the other application (or restart your computer) and try again.

**The assistant doesn't recognize Flattrade commands**
The MCP server may not be connected. Restart the AI assistant and confirm your config file is saved correctly.

**I entered the wrong API Key**
Edit the config file with the correct key and restart the AI assistant.

**(macOS) "Apple could not verify 'flattrade-mcp' is free of malware..."**
macOS blocks the binary because it isn't signed/notarized by Apple — normal for developer tools distributed outside the App Store. Only proceed if you trust the source of the binary.

1. Try running it once from Terminal: `/path/to/flattrade-mcp`
2. You'll see the warning — open **System Settings → Privacy & Security**
3. Scroll to the bottom for a message like *"flattrade-mcp" was blocked to protect your Mac* → click **Allow Anyway**
4. Run the binary again: `/path/to/flattrade-mcp`
5. This time, click **Open** in the confirmation dialog

Once allowed, Claude Desktop / Claude Code will be able to launch it normally.

---

## Important Notes

- **Keep your API Key and Secret private** — never share them or commit them to a public repository
- This binary only communicates with Flattrade's official servers
- Your session is stored locally at `~/.flattrade/session.json` on your own machine
- Sessions expire daily — log in again each trading day
- Always confirm orders before letting the AI place them — ask for a margin check first

---

## Need Help?

Email **support@flattrade.in** with:

- Your operating system (Windows / macOS / Linux)
- The exact error message your AI assistant shows
- A screenshot of the browser page (if login fails)
