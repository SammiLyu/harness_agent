## Coding Agent Workspace Provisioning & Manual Setup Guide (see skills/deployment/SKILL.md)

This guide details the step-by-step process of manually installing, configuring, and verifying the coding agent environment on [PLATFORM_NAME], ensuring compliance with [ORGANIZATION_NAME] security and directory policies.

---

### Step 0: Install and Configure Package Managers

Ensure your package manager ([PACKAGE_MANAGER_TYPE], e.g. Mamba, Conda, Poetry, npm, or pip) is installed on your system and properly configured to route through the organization's repository proxies.

#### 1. Configure Repository Proxy mirrors (Proxy-Aware Setup)
If operating behind an enterprise firewall, standard domains like `npmjs.org` or `anaconda.org` may be blocked. Set up the local repository proxies to map registry requests securely:

```bash
# Example proxy setup command:
[PACKAGE_MANAGER_CMD] config set registry "[INTERNAL_PROXY_URL]"
```

*Note: Reference [INTERNAL_WIKI_URL] for detailed proxy mappings and certificate chain instructions.*

---

### Step 1: Create the Agent Environment

Create a project-specific workspace environment specifying all core dependencies and configuration variables.

#### 1. Define Environment Configuration
Create an environment file named `[ENVIRONMENT_YML_OR_JSON]` with the following properties:

```yaml
# Example Configuration Specification:
name: [ENV_NAME]
channels:
  - [DEFAULT_CHANNEL_NAME]
dependencies:
  - [DEPENDENCY_NAME_A] >= [MIN_VERSION_A]
  - [DEPENDENCY_NAME_B] >= [MIN_VERSION_B]
variables:
  [PROVIDER_PROJECT_ID]: '[PROJECT_ID_PLACEHOLDER]'
  [PROVIDER_LOCATION]: '[COMPUTE_LOCATION_PLACEHOLDER]'
  [AGENT_HOME_VAR]: '[PERSISTENT_DIR_DEFAULT]/[ENV_NAME]'
  [AGENT_MODEL_VAR]: '[MODEL_NAME_DEFAULT]'
  [DISABLE_BROWSER_LOGIN]: 'true'
```

*Note: Ensure all critical variables are declared natively inside the environment configuration. If any of these essential environment variables are missing during agent launch, the CLI may automatically fall back to prompting for raw API Keys, which is strictly prohibited by security policies.*

#### 2. Build the Environment
Instantiate the environment from your configuration spec:
```bash
[ENV_CREATE_CMD] --file [ENVIRONMENT_YML_OR_JSON]
```

---

### Step 2: Secure Interactive Authentication

To prevent credentials from being committed, our organization strictly recommends and mandates utilizing interactive Single Sign-On (SSO) login flows rather than persistent, plaintext API Keys or files.

1. Activate your newly created environment:
   ```bash
   [ENV_ACTIVATE_CMD] [ENV_NAME]
   ```
2. Execute the interactive login flow:
   ```bash
   # Run the login script (it will prompt with an auth-URL or browser redirection):
   [SSO_LOGIN_CMD]
   ```
3. Complete the authentication in your browser using your authorized corporate credentials. This will generate a temporary, securely stored access session.

---

### Step 3: Configure Agent Rules and Assets

To protect your restricted home directory from filling up with large history buffers, configurations, and cache files, we keep agent resources in your persistent storage space and symlink them back to your `$HOME` directory.

1. Create the persistent configuration directories in your dedicated persistent storage folder:
   ```bash
   mkdir -p "[PERSISTENT_DIR_DEFAULT]/[AGENT_CONFIG_DIR_NAME]"
   ```
2. If configuration files already exist in your `$HOME`, back them up first:
   ```bash
   mv "$HOME/[AGENT_CONFIG_DIR_NAME]" "$HOME/[AGENT_CONFIG_DIR_NAME]_backup"
   ```
3. Establish a symbolic link from your persistent directory back to your home folder:
   ```bash
   ln -s "[PERSISTENT_DIR_DEFAULT]/[AGENT_CONFIG_DIR_NAME]" "$HOME/[AGENT_CONFIG_DIR_NAME]"
   ```
4. Verify the link is healthy:
   ```bash
   ls -la "$HOME" | grep "[AGENT_CONFIG_DIR_NAME]"
   ```
