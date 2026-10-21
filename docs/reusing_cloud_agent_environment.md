# Reusing the Cloud Agent environment in another repository

This repository defines a **repository-managed** Cloud Agent environment in [`.cursor/environment.json`](../.cursor/environment.json). The important reusable piece is the **`snapshot`** field: it points at a prepared disk image that already includes **Node.js v22.23.4**, **npm**, **npx**, and npm registry access suitable for later **Expo / React Native** work.

You can reuse that base in another Git repository on the **same Cursor account** that created the original environment build.

---

## Option A — Commit `environment.json` in the other repo (recommended)

1. **Create the file** in the target repository:

   ```text
   other-repo/
   └── .cursor/
       └── environment.json
   ```

2. **Copy the JSON** from this repo’s [`.cursor/environment.json`](../.cursor/environment.json), or start from this template:

   ```json
   {
     "name": "Expo-ready Node 22 base",
     "user": "ubuntu",
     "snapshot": "bld-20261021-589d8ec2-062e-4bcd-89a7-202e7f4277bd",
     "install": "node -v && npm -v && npx -v && npm ping && npm view create-expo-app version"
   }
   ```

3. **Adjust only what the other repo needs:**

   | Field | Usually keep | Change when |
   |--------|----------------|-------------|
   | `snapshot` | Same build ID | You create a **new** warmed build with a different Node/toolchain |
   | `user` | `ubuntu` | Your custom image uses another user |
   | `name` | Any label | Optional; rename for clarity in the dashboard |
   | `install` | Verification-only (empty / docs-only repo) | The repo has **`package.json`**: add e.g. `npm ci` **before or after** the checks |

   Example after the other repo has an Expo app and lockfile:

   ```json
   {
     "name": "My Expo app",
     "user": "ubuntu",
     "snapshot": "bld-20261021-589d8ec2-062e-4bcd-89a7-202e7f4277bd",
     "install": "npm ci"
   }
   ```

4. **Commit and push** `.cursor/environment.json` on the target repo’s default branch (or the branch you use for Cloud Agents).

5. **Start a Cloud Agent** on that repository. Cursor will associate the repo with this config and run **environment builds** that:

   - Boot from the **snapshot** (fast; Node/npm already on disk).
   - Clone the **target** repo and run **`install`** on that repo’s default branch.

6. **Confirm a successful build** in the dashboard: **Cloud Agents → Environments → Builds**. Agents should use the latest successful build for that environment.

7. **Optional — long-running web preview:** This demo config has **no `start`** script. After you have an Expo app, you may add something like:

   ```json
   "start": "exec npx expo start --web --port 8081"
   ```

   and use Cursor **Forwarded Ports** for port `8081`. Add `start` only when you want the dev server up on every agent boot.

---

## Option B — Dashboard (no commit in the other repo yet)

If you prefer a **personal / dashboard-managed** environment first:

1. Open [Cloud Agents → Environments](https://cursor.com/agents).
2. Create or open an environment linked to the **other** repository.
3. Reuse the same base by selecting the existing successful build / snapshot from the source environment ([environment `6928b2b5-cd94-11f1-89ee-7aa6f0729b11`](https://cursor.com/dashboard/cloud-agents/environments/e/6928b2b5-cd94-11f1-89ee-7aa6f0729b11), build [`bld-20261021-589d8ec2-062e-4bcd-89a7-202e7f4277bd`](https://cursor.com/dashboard/cloud-agent/builds/bld-20261021-589d8ec2-062e-4bcd-89a7-202e7f4277bd)) when the UI allows, or duplicate the saved setup from that environment.
4. Later, copy the saved **install** / **snapshot** settings into the other repo’s `.cursor/environment.json` if you want the config versioned in Git.

---

## Do not copy into the other repo

These belong to **this** demo/coordinator project, not the shared Node base:

- [`.cursor/rules/demo-*.mdc`](../.cursor/rules/) — parallel agent demo rules
- Sketches, coordinator docs, and other app-specific sources
- Git remotes, tokens, or environment **names** tied to `cursor-coordinator-kid-demo-test`

The **`snapshot` ID is not secret**, but it is **account-scoped**: another Cursor user or team may need their own setup run and build before the same ID works for them.

---

## Quick verification agent prompt

After copying the file, run a Cloud Agent on the **other** repo with:

> Do not modify the repository.  
> Verify Node.js, npm, npx, npm registry access, and that `create-expo-app` is reachable via npx. Report versions only.

Expected: Node **v22.23.4**, npm **10.x**, registry OK, `create-expo-app` version reported.

---

## Reference IDs (this prepared environment)

| Item | ID |
|------|-----|
| Source environment | `6928b2b5-cd94-11f1-89ee-7aa6f0729b11` |
| Snapshot / build (in `environment.json`) | `bld-20261021-589d8ec2-062e-4bcd-89a7-202e7f4277bd` |

Update `snapshot` in the other repo only when you intentionally replace the base image with a newer build.
