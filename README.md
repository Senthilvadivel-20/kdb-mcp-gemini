# 🚀 KDB+ / Gemini MCP Server Integration

This repository provides a step-by-step guide to set up a Model Context Protocol (MCP) server that connects the high-performance time-series database **KDB+** (or **KDB-X**) with the natural language capabilities of **Google Gemini** via the Gemini CLI.

Once configured, you can use the Gemini CLI on your terminal to query your KDB+ database using natural language (e.g., "Give me the latest price of AAPL," "Summarize my trade activity").



---

## 📋 Prerequisites

Ensure your Linux environment has the following installed and configured:

1.  **KDB-X / KDB+:** A running instance of KDB-X or KDB+ with the `s.k` package (or equivalent SQL interface) loaded.
2.  **Repository Cloned:** You have cloned this repository, which should contain the necessary MCP server code (e.g., the `qmcp` server or a custom FastMCP implementation).
3.  **Python 3.8+**
4.  **UV (Recommended Package Manager):**
    ```bash
    curl -LsSf [https://astral.sh/uv/install.sh](https://astral.sh/uv/install.sh) | sh
    # Ensure the installation directory (~/.local/bin) is in your PATH.
    export PATH="$HOME/.local/bin:$PATH"
    ```
5.  **Node.js & Gemini CLI:** The Gemini CLI requires Node.js. **Node v20 or higher is strongly recommended** for full compatibility with modern dependencies.
    ```bash
    npm install -g @google/gemini-cli
    ```
6.  **Gemini API Key:** Obtain your key and prepare to set it as an environment variable.
    * **Get Key:** [https://aistudio.google.com/u/0/api-keys](https://aistudio.google.com/u/0/api-keys)

---

## ⚙️ Setup Procedure

### 1. Start the KDB Session

Start your KDB-X or KDB+ server and ensure it is listening on port `5000` (`-p 5000`). Load your database and any necessary SQL interface packages (e.g., `s.k`).

```bash
q -p 5000

Verification (Inside the q session): Ensure you can query your data using a SQL interface.

q)s)SELECT * FROM TRADE LIMIT 10
time                                     sym  price    size
------------------------------------------------------------
2017.12.09D14:36:36.584022272 AAPL 64.11941 12
2000.05.20D02:48:12.018162648 MSFT 17.24467 40
...
q)
```

### 2. Configure Gemini CLI for Streamable HTTP

The Gemini CLI needs to know how to connect to the KDB MCP server, which is running as a persistent HTTP service (Streamable HTTP transport is highly reliable).

>mkdir -p ~/.gemini
>nano ~/.gemini/settings.json

***Paste the following JSON configuration into ~/.gemini/settings.json. This tells Gemini CLI where to find the server (running on port 8000 in the next step).***

{
  "mcp": {
    "servers": [
      {
        "name": "kdb-x-finance",
        "description": "KDB-X Financial Data Server for time-series and vector search.",
        "command": "npx",
        "args": [
          "streamable-http-client",
          "--url",
          "[http://127.0.0.1:8000](http://127.0.0.1:8000)"
        ]
      }
    ]
  }
}


### 3. Set the API Key

The Gemini CLI must have access to your API key, typically set as an environment variable in your terminal session.

export GEMINI_API_KEY="YOUR_API_KEY_HERE"


### 4. Run the KDB MCP Server

Gemini
