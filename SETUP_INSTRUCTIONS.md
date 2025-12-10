# Setup Instructions for github-repo-standards-test

This document provides step-by-step instructions to complete the setup of the test instance.

## Step 1: Create GitHub Apps

You need to create **two GitHub Apps** for the compliance framework to work.

### App 1: github-repo-standards-bot (Cross-Repository Scanner)

**Purpose:** Scans all repositories and creates compliance issues.

1. Go to: https://github.com/organizations/labrats-work/settings/apps/new
2. Configure:
   - **Name:** `github-repo-standards-bot`
   - **Description:** `Test instance - Scans repositories for compliance with standards`
   - **Homepage URL:** `https://github.com/labrats-work/github-repo-standards-test`
   - **Webhook:** Uncheck "Active"
   - **Permissions:**
     - Repository permissions:
       - `Administration`: Read-only
       - `Contents`: Read-only
       - `Issues`: Read and write
       - `Metadata`: Read-only (automatically selected)
   - **Where can this GitHub App be installed?:** "Only on this account"
3. Click **Create GitHub App**
4. On the app page, click **Generate a private key** and download it
5. Note the **App ID** (shown at the top of the page)
6. Click **Install App** in the left sidebar
7. Select **All repositories** or choose specific repositories to scan
8. Click **Install**

**Save these for later:**
- App ID: `____________`
- Private Key: (downloaded .pem file)

### App 2: github-repo-standards-test-automation (Report Management)

**Purpose:** Commits reports and manages PRs in the test repository.

1. Go to: https://github.com/organizations/labrats-work/settings/apps/new
2. Configure:
   - **Name:** `github-repo-standards-test-automation`
   - **Description:** `Test instance - Manages compliance reports and PRs`
   - **Homepage URL:** `https://github.com/labrats-work/github-repo-standards-test`
   - **Webhook:** Uncheck "Active"
   - **Permissions:**
     - Repository permissions:
       - `Contents`: Read and write
       - `Pull requests`: Read and write
       - `Metadata`: Read-only (automatically selected)
   - **Where can this GitHub App be installed?:** "Only on this account"
3. Click **Create GitHub App**
4. On the app page, click **Generate a private key** and download it
5. Note the **App ID** (shown at the top of the page)
6. Click **Install App** in the left sidebar
7. Select **Only select repositories**
8. Choose **github-repo-standards-test**
9. Click **Install**

**Save these for later:**
- App ID: `____________`
- Private Key: (downloaded .pem file)

## Step 2: Configure GitHub Secrets

Navigate to: https://github.com/labrats-work/github-repo-standards-test/settings/secrets/actions

Add the following **Repository secrets:**

1. **REPO_STANDARDS_APP_ID**
   - Value: App ID from github-repo-standards-bot (Step 1, App 1)

2. **REPO_STANDARDS_APP_PRIVATE_KEY**
   - Value: Contents of the .pem file from github-repo-standards-bot
   - Format: Copy the entire contents including headers
   ```
   -----BEGIN RSA PRIVATE KEY-----
   [Your private key content]
   -----END RSA PRIVATE KEY-----
   ```

3. **INTERNAL_AUTOMATION_APP_ID**
   - Value: App ID from github-repo-standards-test-automation (Step 1, App 2)

4. **INTERNAL_AUTOMATION_APP_PRIVATE_KEY**
   - Value: Contents of the .pem file from github-repo-standards-test-automation
   - Format: Same as above

## Step 3: Configure GitHub Variables (Optional)

Navigate to: https://github.com/labrats-work/github-repo-standards-test/settings/variables/actions

These are optional with sensible defaults:

1. **STANDARDS_REPO_NAME**: `github-repo-standards-test`
2. **MAX_PARALLEL_JOBS**: `10`
3. **ARTIFACT_RETENTION_DAYS**: `30`
4. **DEFAULT_BRANCH**: `main`
5. **COMPLIANCE_LABEL**: `compliance`
6. **CRITICAL_LABEL**: `critical`

## Step 4: Test the Setup

Once you've completed Steps 1-3, run:

```bash
# In the github-repo-standards-test directory
gh workflow run compliance-check.yml --repo labrats-work/github-repo-standards-test
```

Or manually trigger via GitHub UI:
1. Go to: https://github.com/labrats-work/github-repo-standards-test/actions
2. Click on "Repository Compliance Check (Matrix)"
3. Click "Run workflow"
4. Click the green "Run workflow" button

## Step 5: Verify Success

The workflow should:
1. ✅ Discover all repositories where github-repo-standards-bot is installed
2. ✅ Run compliance checks on each repository
3. ✅ Create/update compliance issues in repositories with failures
4. ✅ Generate compliance reports
5. ✅ Create a PR with the reports
6. ✅ Auto-merge the PR

Check the workflow run at:
https://github.com/labrats-work/github-repo-standards-test/actions

## Troubleshooting

### "Resource not accessible by integration"
- **Cause:** GitHub App doesn't have required permissions or isn't installed on the repository
- **Fix:** Review permissions in Step 1 and verify installation

### "A JSON web token could not be decoded"
- **Cause:** Private key not configured correctly in secrets
- **Fix:** Ensure the entire .pem file contents are copied, including header/footer

### "Issues are disabled"
- **Cause:** Target repository has issues disabled
- **Fix:** Enable issues in repository settings

### Workflow doesn't start
- **Cause:** Secrets not configured
- **Fix:** Verify all 4 secrets are added in Step 2

## Next Steps

Once the workflow runs successfully:
1. Review the generated compliance reports in `reports/`
2. Check for compliance issues created in scanned repositories
3. Verify the auto-merged PR with compliance results

---

**Questions?** See [docs/setup.md](docs/setup.md) for more detailed information.
