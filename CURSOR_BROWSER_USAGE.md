# 🚀 Guide: Using the Cursor Browser Agent
**Target Audience:** Junior Developers & AI-Pairing Enthusiasts  
**Goal:** Learn how to let Cursor "test" your app automatically so you don't have to manually click through every bug.

---

## 1. What is the Browser Agent?
The **Browser Agent** is an AI tool inside Cursor that allows the editor to control a real web browser. Unlike a simple preview window, the agent can:
* **Navigate** to your `localhost` or any URL.
* **Click** elements and **fill out** forms.
* **Read Console Logs** and **Network Errors** directly.
* **Take Screenshots** to understand the visual layout.

---

## 2. How to Start the Browser
You don't "open" a browser tab like you do in Chrome. Instead, you **ask for it** inside **Composer** (`Cmd + I`) or **Chat** (`Cmd + L`).

### The Easiest Way to Open it:
1. Open **Composer** (`Cmd + I`).
2. Type `@browser` followed by your request.
   * *Example:* `@browser Open http://localhost:3000 and tell me if the login page loads.`

---

## 3. Top 3 Ways to Use it for Testing

### A. Automatic Bug Hunting (Console Logs)
Instead of copying and pasting errors from your Chrome DevTools into Cursor, just tell the agent to look for them.
* **Prompt:** `@browser Open my app and check the console for any Red errors. If you find any, fix the code in my project.`
* **Why it’s cool:** The agent reads the exact line of code causing the crash and can apply a fix immediately.

### B. Visual Testing (The "Eye" Icon)
Cursor can "see" your UI. If a button is overlapping text, the agent can detect it via screenshots.
* **Prompt:** `@browser Navigate to the dashboard and tell me if the sidebar looks correctly aligned on mobile vs desktop.`
* **Pro Tip:** You can also use the **Visual Editor** toggle in the browser window to click an element and say "Make this blue."

### C. End-to-End (E2E) Flow Testing
You can ask the agent to perform a sequence of actions to verify a feature works.
* **Prompt:** `@browser Go to the signup page. Create a dummy user, submit the form, and verify that I am redirected to the /welcome page.`

---

## 4. Understanding the Controls
When the browser opens, you will see a small toolbar at the top of the browser tab:

| Feature | What it does |
| :--- | :--- |
| **Visual Editor** | Allows you to click an element on the page to highlight its code in the editor. |
| **Apply** | After you use the Visual Editor to tweak styles, hit "Apply" to let the AI write the CSS/Code for you. |
| **Console/Network** | Shows you what the AI is "reading" in the background (errors, API calls). |

---

## 5. Common "Junior Dev" Pitfalls
1. **The Server Must Be Running:** The agent cannot "start" your server for you unless you ask it to run `npm run dev` in the terminal first. Always ensure your app is live at the URL you give it.
2. **Permissions:** The agent will often ask: *"Can I navigate to...?"* or *"Can I click...?"* You must click **Approve** in the chat for it to continue.
3. **Session State:** The browser agent usually starts fresh. If your page requires a login, you’ll need to give the agent credentials or tell it to "Sign in as a guest."

---

## 6. Pro Prompts to Try Right Now
* *"@browser verify all the links in my footer work and don't lead to 404s."*
* *"@browser check if the 'Submit' button is disabled when the email input is empty."*
* *"@browser take a screenshot of the homepage and compare it to my mockup.png file."*

---

**Next Step:** Try running your local dev server and asking Composer:  
> *"@browser open my localhost and tell me one thing I could improve about the UI layout."*