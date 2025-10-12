## RPM Package Management

### Understanding RPM
RPM (Red Hat Package Manager) is the native package format for Red Hat Enterprise Linux. RPM packages have `.rpm` extensions and contain compiled software, metadata, and installation scripts.

### Configuring Access to RPM Repositories

#### Repository Configuration Files
Repository configurations are stored in `/etc/yum.repos.d/` with `.repo` extension.

**Basic repository file structure:**
```ini
[repository-id]
name=Repository Name
baseurl=http://repo.example.com/path/
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-redhat-release
```

**Key parameters:**
- `[repository-id]` - unique identifier for the repository
- `name` - human-readable description
- `baseurl` - URL to repository location (can also use `mirrorlist`)
- `enabled` - 1 to enable, 0 to disable
- `gpgcheck` - 1 to verify package signatures, 0 to skip
- `gpgkey` - location of GPG key for signature verification

#### Managing Repositories with DNF/YUM

**List all repositories:**
```bash
dnf repolist all
```

**Enable a repository:**
```bash
dnf config-manager --enable repository-id
```

**Disable a repository:**
```bash
dnf config-manager --disable repository-id
```

**Add a new repository:**
```bash
dnf config-manager --add-repo http://repo.example.com/repo.repo
```

**View repository information:**
```bash
dnf repoinfo repository-id
```

**Clean repository cache:**
```bash
dnf clean all
```

**Rebuild repository cache:**
```bash
dnf makecache
```

### Installing and Removing RPM Software Packages

#### Using DNF (Default in RHEL 8/9)

**Install a package:**
```bash
dnf install package-name
```

**Install multiple packages:**
```bash
dnf install package1 package2 package3
```

**Install a specific version:**
```bash
dnf install package-name-version
```

**Install from local RPM file:**
```bash
dnf install /path/to/package.rpm
```

**Install a package group:**
```bash
dnf group install "Development Tools"
```

**Remove a package:**
```bash
dnf remove package-name
```

**Remove package and unused dependencies:**
```bash
dnf autoremove package-name
```

#### Searching and Querying Packages

**Search for a package:**
```bash
dnf search keyword
```

**Get package information:**
```bash
dnf info package-name
```

**List installed packages:**
```bash
dnf list installed
```

**List available packages:**
```bash
dnf list available
```

**Check for updates:**
```bash
dnf check-update
```

**Update a specific package:**
```bash
dnf update package-name
```

**Update all packages:**
```bash
dnf update
```

**Show package dependencies:**
```bash
dnf deplist package-name
```

**Find which package provides a file:**
```bash
dnf provides /path/to/file
# or
dnf whatprovides */filename
```

#### Using RPM Command Directly

**Query installed package:**
```bash
rpm -q package-name
```

**Query all installed packages:**
```bash
rpm -qa
```

**Get package information:**
```bash
rpm -qi package-name
```

**List files in a package:**
```bash
rpm -ql package-name
```

**List documentation files:**
```bash
rpm -qd package-name
```

**List configuration files:**
```bash
rpm -qc package-name
```

**Find which package owns a file:**
```bash
rpm -qf /path/to/file
```

**Query an uninstalled RPM file:**
```bash
rpm -qip package.rpm
rpm -qlp package.rpm
```

**Install RPM directly (not recommended - use DNF instead):**
```bash
rpm -ivh package.rpm
```

**Remove RPM directly:**
```bash
rpm -e package-name
```

**Verify package integrity:**
```bash
rpm -V package-name
```

---

## Flatpak Package Management

### Understanding Flatpak
Flatpak is a universal package format that provides sandboxed applications with their own dependencies. It's useful for desktop applications and provides better security isolation.

### Configuring Access to Flatpak Repositories

#### Installing Flatpak
```bash
dnf install flatpak
```

#### Adding Flatpak Repositories (Remotes)

**Add Flathub repository (most common):**
```bash
flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
```

**Add a custom repository:**
```bash
flatpak remote-add remote-name https://example.com/repo.flatpakrepo
```

**Add repository for system-wide access:**
```bash
flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
```

**Add repository for user only:**
```bash
flatpak remote-add --user --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
```

#### Managing Flatpak Repositories

**List configured remotes:**
```bash
flatpak remotes
```

**List remotes with more details:**
```bash
flatpak remote-list --show-details
```

**Modify a remote:**
```bash
flatpak remote-modify remote-name --url=https://new-url.com/repo
```

**Delete a remote:**
```bash
flatpak remote-delete remote-name
```

**Enable a remote:**
```bash
flatpak remote-modify --enable remote-name
```

**Disable a remote:**
```bash
flatpak remote-modify --disable remote-name
```

### Installing and Removing Flatpak Software Packages

#### Installing Flatpak Applications

**Install an application system-wide:**
```bash
flatpak install flathub org.application.Name
```

**Install for current user only:**
```bash
flatpak install --user flathub org.application.Name
```

**Install from a specific remote:**
```bash
flatpak install remote-name org.application.Name
```

**Install from a flatpakref file:**
```bash
flatpak install https://example.com/app.flatpakref
```

**Install from local file:**
```bash
flatpak install /path/to/app.flatpakref
```

#### Removing Flatpak Applications

**Remove an application:**
```bash
flatpak uninstall org.application.Name
```

**Remove user installation:**
```bash
flatpak uninstall --user org.application.Name
```

**Remove unused runtimes and dependencies:**
```bash
flatpak uninstall --unused
```

**Remove everything (applications and runtimes):**
```bash
flatpak uninstall --all
```

#### Searching and Querying Flatpak Packages

**Search for applications:**
```bash
flatpak search keyword
```

**List installed applications:**
```bash
flatpak list
```

**List all applications (installed and available):**
```bash
flatpak list --app
```

**List runtimes:**
```bash
flatpak list --runtime
```

**Get application information:**
```bash
flatpak info org.application.Name
```

**Show application details from remote:**
```bash
flatpak remote-info flathub org.application.Name
```

#### Running Flatpak Applications

**Run an application:**
```bash
flatpak run org.application.Name
```

**Run with custom command:**
```bash
flatpak run --command=bash org.application.Name
```

#### Updating Flatpak Applications

**Update a specific application:**
```bash
flatpak update org.application.Name
```

**Update all applications:**
```bash
flatpak update
```

**Check for available updates:**
```bash
flatpak remote-ls --updates
```

#### Managing Flatpak Permissions

**Show application permissions:**
```bash
flatpak info --show-permissions org.application.Name
```

**Override permissions:**
```bash
flatpak override --filesystem=home org.application.Name
```

**Reset overrides:**
```bash
flatpak override --reset org.application.Name
```

---

## RHCSA Exam Tips

### For RPM Management
1. Know how to create and modify `.repo` files manually
2. Understand the difference between `dnf` and `rpm` commands
3. Practice enabling/disabling repositories without GUI
4. Be familiar with package groups and their installation
5. Know how to troubleshoot dependency issues
6. Remember that `dnf` resolves dependencies, `rpm` does not

### For Flatpak Management
1. Understand the difference between system and user installations
2. Know how to add Flathub repository from command line
3. Practice installing and removing Flatpak applications
4. Understand the Flatpak application ID format (org.name.App)
5. Be familiar with listing remotes and installed applications
6. Know basic flatpak commands without relying on software center

### Key Differences: RPM vs Flatpak

| Feature | RPM | Flatpak |
|---------|-----|---------|
| Scope | System packages | Desktop applications |
| Dependencies | System-wide shared | Bundled with app |
| Security | Traditional permissions | Sandboxed |
| Updates | Via dnf/yum | Via flatpak |
| Installation location | System directories | User or system Flatpak dirs |
| Dependency management | DNF resolves | Self-contained |

### Common Exam Scenarios

**Scenario 1: Configure BaseOS and AppStream repositories**
```bash
# Edit or create repository files
vi /etc/yum.repos.d/rhel.repo

[BaseOS]
name=Red Hat Enterprise Linux BaseOS
baseurl=http://content.example.com/rhel9/BaseOS
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-redhat-release

[AppStream]
name=Red Hat Enterprise Linux AppStream
baseurl=http://content.example.com/rhel9/AppStream
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-redhat-release
```

**Scenario 2: Install a package group**
```bash
# List available groups
dnf group list

# Install development tools
dnf group install "Development Tools"
```

**Scenario 3: Setup Flatpak and install an application**
```bash
# Install flatpak
dnf install flatpak

# Add Flathub
flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo

# Install an application
flatpak install flathub org.gimp.GIMP
```

### Practice Commands

Test your knowledge with these commands:
```bash
# Repository management
dnf repolist
dnf config-manager --enable repo-name
dnf clean all && dnf makecache

# Package operations
dnf install httpd
dnf remove httpd
dnf update
dnf search web server
dnf info httpd
dnf provides /usr/sbin/httpd

# RPM queries
rpm -qa | grep httpd
rpm -ql httpd
rpm -qf /usr/sbin/httpd

# Flatpak operations
flatpak remotes
flatpak search firefox
flatpak install flathub org.mozilla.firefox
flatpak list
flatpak uninstall org.mozilla.firefox
```
