# Cursor Cloud Agent Pre-Demo Setup Guide

## Purpose

Prepare a **ready Cursor Cloud Agent environment** for the kid demo without creating the Expo application itself.

The goal is to remove boring infrastructure/setup work from the live demo while preserving the main story:

```text
Kid / You
   ↓
Grok Bot
   ↓
Cursor Cloud Agents
   ↓
create / modify / test the app
   ↓
browser preview
   ↓
final APK installation on the kid's Android phone
```

Before the demo, we prepare only the Cloud Agent development environment and verify browser port forwarding.

We deliberately **do not** create the React Native / Expo app yet.

---

# 1. Create an empty demo Git repository

Create a repository such as:

```text
calendar-buddy-demo
```

Use whichever source-control provider you want Cursor Cloud Agents to access.

The repository should initially contain only something minimal, for example:

```text
calendar-buddy-demo/
└── README.md
```

Do **not** create yet:

```text
package.json
Expo project files
React Native source code
TypeScript application code
EAS configuration
```

Commit and push the repository's default branch.

The purpose is to let Grok Bot and Cursor Cloud Agents visibly create the application from scratch during the demo.

---

# 2. Create a Cursor Cloud Agent environment

Open the Cursor Cloud Agents dashboard:

```text
https://cursor.com/agents
```

Then:

1. Open **Environments**.
2. Create a **new environment**.
3. Select the repository:

```text
calendar-buddy-demo
```

4. Use the normal saved/personal environment setup.

For this demo, avoid introducing a custom Dockerfile or committed `.cursor/environment.json` unless Cursor specifically requires it.

The environment should be associated with the demo repository so future Cursor Cloud Agents can reuse the prepared environment.

---

# 3. Start the agent-driven environment setup

Use Cursor's guided / agent-driven environment setup.

If the environment already exists, use **New Setup Run**.

Give the setup agent the following prompt:

> Prepare this Cursor Cloud Agent environment for development of an Expo React Native application using TypeScript.
>
> Do not create the application and do not add application source code to the repository.
>
> Ensure Node.js, npm and npx are available and usable.
>
> The future application must be able to use Expo, React Native and React Native Web, and we will run it with `npx expo start --web`.
>
> Verify access to the public npm registry.
>
> Do not install Android Studio, Android SDK, JDK, Gradle, Android emulator, backend tooling or EAS tooling.
>
> Keep the development environment minimal.
>
> At the end, report the installed Node/npm versions and the checks you performed.

No application credentials or secrets should be required for this setup.

---

# 4. Watch and verify the setup run

Watch the setup agent's terminal while it works.

You should expect checks equivalent to:

```bash
node --version
npm --version
npx --version
npm view create-expo-app version
```

If Node.js is missing or unsuitable, allow the setup agent to install/configure it **inside the Cursor Cloud Agent environment**.

Do not install Node.js or Android tooling manually on the Windows laptop for this demo.

The expected result is:

```text
Node.js       OK
npm           OK
npx           OK
npm registry  reachable
```

Important:

If the setup agent starts scaffolding the real Expo application, stop that part. The application itself must remain uncreated until the live demo.

---

# 5. Confirm that a successful Build exists

After the setup finishes, open the environment's **Builds** section.

Confirm that there is a successful/active Build.

Conceptually, the prepared Build should contain:

```text
Cursor Cloud Agent Build
├── Ubuntu environment
├── Git access
├── calendar-buddy-demo repo
├── Node.js
├── npm
└── npx
```

It should **not** contain:

```text
✗ React Native application
✗ Expo project
✗ Android SDK
✗ Android emulator
✗ backend
✗ EAS configuration
```

The successful Build is the reusable development environment that future Cloud Agents can start from.

---

# 6. Run a clean verification Cloud Agent

Start a normal Cursor Cloud Agent against:

```text
calendar-buddy-demo
```

Give it this prompt:

> Do not modify the repository.
>
> Verify that this Cloud Agent environment is ready to create an Expo React Native TypeScript application later.
>
> Check:
> - Node.js
> - npm
> - npx
> - access to the public npm registry
> - that `create-expo-app` is available through npx
>
> Report the results only. Do not scaffold the application.

The desired result is something simple such as:

```text
Node.js              OK
npm                  OK
npx                  OK
npm registry         OK
create-expo-app      reachable
```

Do not proceed with application creation.

---

# 7. Test Cursor port forwarding once

Before the live demo, verify that an HTTP service running inside the Cloud Agent VM can be displayed locally in Cursor's browser.

In the same verification Cloud Agent, ask:

> Start a temporary HTTP server on port 8081 that displays the text:
>
> `Cursor Cloud Agent browser preview works`
>
> Do not modify the repository.

A simple Node.js command can be used:

```bash
node -e "require('http').createServer((q,r)=>r.end('Cursor Cloud Agent browser preview works')).listen(8081,'0.0.0.0')"
```

Then, on your laptop:

1. Open the **Cursor desktop app**.
2. Open the **Agents Window**.
3. Find **Forwarded Ports**.
4. Confirm that port `8081` appears under detected ports.
5. Forward it if necessary.
6. Choose **Open in internal browser**.

You should see:

```text
Cursor Cloud Agent browser preview works
```

The HTTP server is running inside the Cursor Cloud Agent VM, while the page is displayed locally through Cursor's port forwarding.

After confirming this works, stop the temporary server.

---

# Stop here before the demo

Do not create the actual application yet.

Your pre-demo state should be:

```text
Git repo             ✓
Cursor Cloud env     ✓
Successful Build     ✓
Node/npm/npx         ✓
npm access           ✓
Port forwarding      ✓

Expo application     ✗
React Native code    ✗
EAS account/login    ✗
APK                   ✗
```

This preserves the intended live-demo sequence:

```text
PRE-DEMO
boring infrastructure readiness

LIVE DEMO
Kid / You
   ↓
Grok Bot
   ↓
Cursor Cloud Agent
   ↓
creates Expo / React Native app
   ↓
runs web preview
   ↓
browser testing and iteration
   ↓
EAS build
   ↓
APK
   ↓
install on kid's Android phone
```

The important point is that the development environment is prepared in advance, but the **actual application is still created by Grok Bot + Cursor Cloud Agents during the demo**.

---

# Reuse this environment in another repository

This repository now includes [`.cursor/environment.json`](../.cursor/environment.json) (snapshot-backed Node 22 / npm / npx base).

To copy that setup into a **different** Git repo (same Cursor account), follow:

**[Reusing the Cloud Agent environment in another repository](./reusing_cloud_agent_environment.md)**
