# Apple Mac Mini Fleet Provisioning & Automation Repository

Production-grade Ansible infrastructure-as-code repository to provision, hard security baseline, and configure 40 Apple Silicon Mac Minis for iOS build workloads, Android build pipelines, and local LLM inference engines (`mlx` and `omlx`).

## Architecture & Data Flow

```
Controller Machine (Ansible Engine)
  │
  ├── inventory/production/hosts.yml (Defines 40 Mac Minis in logical groups)
  ├── group_vars/ (Configures role variables per target role)
  ├── site.yml (Master playbook triggering roles conditionally)
  └── roles/
       ├── common          ──> Base OS check, Homebrew, CLI tools
       ├── user_management ──> macOS dscl / sysadminctl accounts & SSH
       ├── security        ──> Hostname, Firewall, SSH hardening, Power
       ├── ios_build       ──> Xcode CLT, CocoaPods, Fastlane, Swift
       ├── android_build   ──> OpenJDK, Android SDK, ADB, Gradle
       ├── mlx             ──> Apple Silicon MLX & MLX-LM Python env
       └── omlx            ──> oMLX native inference server & LaunchAgent
```

## Quick Start (First Mac Mini Execution)

1. **Install Ansible on Controller**:
   ```bash
   pip install ansible
   ansible-galaxy collection install -r requirements.yml
   ```

2. **Run Dry Run (Check Mode)**:
   ```bash
   ansible-playbook -i inventory/development/hosts.yml site.yml --check --diff
   ```

3. **Execute Baseline Provisioning**:
   ```bash
   ansible-playbook -i inventory/development/hosts.yml site.yml --tags common,security
   ```

4. **Execute Full Suite**:
   ```bash
   ansible-playbook -i inventory/development/hosts.yml site.yml
   ```

5. **Verify Idempotency**:
   Run the command again to confirm `changed=0`.

## Tags Summary
- `--tags common`: Base system prep, Homebrew, CLI tools.
- `--tags security`: Security hardening, firewall, SSH settings.
- `--tags user`: User management and Directory Service user configuration.
- `--tags ios`: iOS build environment (CocoaPods, Fastlane).
- `--tags android`: Android SDK, JDK 17, Gradle setup.
- `--tags mlx`: Apple Silicon MLX Python runtime environment.
- `--tags omlx`: oMLX inference server setup and service daemon.
