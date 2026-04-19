\# Engineering Guide: Fitting Gemma 4 31B on 24GB VRAM (RTX 3090)



\## 1. The Problem: Memory Over-Allocation (The "Spill")

When running a 31B parameter model on a single 24GB card, the system often defaults to settings that exceed the physical VRAM. In April 2026, Gemma 4 defaults to a 128k context window, which is the primary cause of failure.



\### The Math of the Failure

\- Model Weights (Q4\_K\_M): \~19 GB

\- KV Cache (128k context): \~8-10 GB

\- Windows Overhead ("Windows Tax"): \~1.5 - 2.5 GB

\- Total Required: \~29 GB (Exceeds 24 GB capacity)



When the total exceeds 24 GB, Ollama offloads the surplus to System RAM (CPU). This results in a "Processor" readout like 18%/82% CPU/GPU. The result is massive latency, causing timeouts and the "confused face" (😰) in Slack.



\---



\## 2. Windows Server Configuration

To achieve 100% GPU usage, we must compress the conversation memory (KV Cache) and cap the context window.



\### Step 1: Set Global Environment Variables

These variables tell the Ollama engine how to handle memory. Set these via "Edit System Environment Variables" in Windows or run these PowerShell commands:



\[SET VARIABLE] OLLAMA\_KV\_CACHE\_TYPE = q4\_0

(Reduces context memory footprint by 75%)



\[SET VARIABLE] OLLAMA\_FLASH\_ATTENTION = 1

(Required engine optimization for memory compression)



\[SET VARIABLE] OLLAMA\_CONTEXT\_LENGTH = 32768

(Caps the memory "scratchpad" to 32k instead of 128k)



\[SET VARIABLE] OLLAMA\_HOST = 0.0.0.0

(Allows the Mac/OpenClaw to connect to the Windows machine)



\[SET VARIABLE] OLLAMA\_ORIGINS = \*

(Ensures external requests aren't blocked by CORS)



\### Step 2: The Clean Restart

Environment variables are only loaded when the server starts. You must kill the background instance entirely.



1\. Right-click the Llama icon in the System Tray and select "Quit".

2\. Run this command to kill any "ghost" processes:

&#x20;  \[RUN] stop-process -name "ollama\*" -force

3\. Start the server manually to monitor for errors:

&#x20;  \[RUN] ollama serve



\---



\## 3. Mac-Side / OpenClaw Sync

The client (OpenClaw) must match the server's settings to prevent "watchdog" timeouts.



\### Step 1: Increase Timeouts

Local models take time to "prefill." We must increase the time OpenClaw waits for the first word.



\[RUN] openclaw config set agents.defaults.llm.idleTimeoutSeconds 300



\### Step 2: Set the Client Context Window

Ensure OpenClaw doesn't try to send a prompt larger than the 32k we set on the server.



\[RUN] openclaw config set "models.configurations\[id=ollama/gemma4:31b-it-q4\_K\_M].contextWindow" 32768



\---



\## 4. Verification \& Diagnostics

Use these tools to ensure the fix is active:



\### Verification Tool: Ollama PS

While the model is running (send a test message first), run this:

\[RUN] ollama ps



SUCCESS LOOKS LIKE:

\- PROCESSOR: 100% GPU

\- SIZE: \~21 GB to 23 GB

\- CONTEXT: 32768



\### Log Analysis

If the size is still 26GB+, the quantization is inactive. Check the logs:

\- Path: %LOCALAPPDATA%\\Ollama\\server.log

\- Look for: "kv\_cache\_type=q4\_0"

\- If it says "f16", your environment variables were not picked up.



\### Network Check

If you cannot connect from the Mac, run this on the Mac terminal:

\[RUN] curl http://10.0.0.233:11434/api/tags

\- If it returns JSON, the connection is open.

\- Check the "X-Ollama-Version" header to verify the server version (should be 0.20.7 or higher).

