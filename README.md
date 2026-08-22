<div align="center">

# 🐉 Kali Linux Installation & Setup in VirtualBox

**A beginner-friendly, step-by-step guide to installing, configuring, updating,
and verifying Kali Linux for cybersecurity learning and authorized security
testing.**

[![OS](https://img.shields.io/badge/OS-Kali%20Linux-557C94?style=for-the-badge&logo=kalilinux&logoColor=white)](https://www.kali.org/)
[![Virtualization](https://img.shields.io/badge/Virtualization-Oracle%20VirtualBox-183A61?style=for-the-badge&logo=virtualbox&logoColor=white)](https://www.virtualbox.org/)
[![Level](https://img.shields.io/badge/Level-Beginner--Friendly-2EA043?style=for-the-badge)](#choose-your-path)
[![License](https://img.shields.io/badge/License-MIT-2EA043?style=for-the-badge)](LICENSE)

</div>

---

A practical, security-conscious guide for building a Kali Linux learning lab in
Oracle VirtualBox. It favors reversible changes, official downloads, and
isolated networking so you can learn without putting your host or other people
at risk.

> Kali Linux is a specialized Debian-based distribution for penetration
> testing, digital forensics, and security research. Use it only against
> systems you own or have explicit permission to test.

## What this guide provides

- A quick path using Kali's official pre-built VirtualBox image
- A manual ISO-install path when you need custom partitions or a custom disk
- Download and checksum verification before booting an image
- Sensible VirtualBox resource and display settings
- A network decision guide for internet access and isolated labs
- First-boot account, update, backup, and snapshot practices
- Optional developer and security-tool setup, including Ollama
- A compact troubleshooting and cleanup guide

## Choose your path

| Goal | Recommended path |
| --- | --- |
| Start learning quickly | Import the pre-built VirtualBox image |
| Customize disk layout or desktop | Install from the official ISO |
| Build a multi-VM target lab | Kali plus Host-Only or Internal Network |
| Run only disposable experiments | NAT for setup, then an isolated lab network |

This guide uses placeholders such as `<version>` and `<vm-name>` deliberately.
Kali releases and VirtualBox menus change over time; follow the current official
download and import pages linked below for release-specific details.

## 1. Before you begin

### Host requirements

These are comfortable starting points, not hard minimums:

| Resource | Starting point |
| --- | --- |
| CPU | 64-bit processor with Intel VT-x or AMD-V enabled |
| Host memory | 8 GB; 16 GB is more comfortable |
| VM memory | 4 GB; use 6-8 GB when the host can spare it |
| VM processors | 2; use 4 for heavier tools |
| Free disk | 40 GB for a general-purpose VM |
| Network | Internet access for download and updates |

Leave enough CPU, memory, and disk for the host. A VM that consumes nearly all
host resources will be slow and can make the host unstable.

### Enable hardware virtualization

Check the host firmware for one of these settings:

- Intel VT-x or Intel Virtualization Technology
- AMD-V or SVM Mode

On Windows, Task Manager > Performance > CPU normally shows whether
virtualization is enabled. On Linux, this quick check reports whether the CPU
advertises virtualization flags:

```bash
grep -Eoc '(vmx|svm)' /proc/cpuinfo
```

A result greater than zero is a useful indication, but VirtualBox must still be
able to access the feature. If it is disabled, enable it in UEFI/BIOS and reboot.

## 2. Download trusted software

Use the official sources:

- [Kali downloads](https://www.kali.org/get-kali/)
- [Kali image verification](https://www.kali.org/docs/introduction/download-images-securely/)
- [VirtualBox downloads](https://www.virtualbox.org/wiki/Downloads)
- [Kali VirtualBox import instructions](https://www.kali.org/docs/virtualization/import-premade-virtualbox/)

Avoid third-party mirrors, repacked images, and commands copied from comments.
Do not boot an image until its checksum or signature verification succeeds.

### Verify an ISO or archive

Download the matching `SHA256SUMS` file from the same official Kali release
location. Then calculate the local hash and compare it exactly with the
published value.

Linux:

```bash
sha256sum kali-linux-<version>-installer-amd64.iso
```

macOS:

```bash
shasum -a 256 kali-linux-<version>-installer-amd64.iso
```

Windows PowerShell:

```powershell
Get-FileHash .\kali-linux-<version>-installer-amd64.iso -Algorithm SHA256
```

For stronger assurance, verify Kali's signed `SHA256SUMS` file as described in
the official image-verification guide. A mismatch means: delete the file and
download it again from an official source. Never work around a failed check.

## 3. Install VirtualBox

Install the current VirtualBox release for your host from Oracle's official
download page. If the installer offers an extension pack, read its license and
download it only from the matching official release page.

## 4. Path A: import the pre-built image

This is the fastest path for most learners.

1. Download Kali's VirtualBox image from the Kali downloads page.
2. Verify the archive before extracting it.
3. Extract the archive with a tool that supports `.7z` files.
4. In VirtualBox, choose **Machine > Add** and select the extracted `.vbox`
	file. Do not select the `.vdi` directly when the package includes a `.vbox`.
5. Open the VM settings and apply the resource and network guidance below.

Kali's current pre-built image documentation contains the release-specific
default login and import details. Treat any published default credential as
temporary: change it immediately after the first login.

## 5. Path B: install from an ISO

Use this path when you need a custom disk layout, desktop, or installation
profile.

1. Create a new Linux/Debian 64-bit VM in VirtualBox.
2. Give it at least 4 GB RAM, 2 CPUs, and a dynamically allocated 40 GB disk.
3. Mount the verified Kali installer ISO as the optical disk.
4. Boot the VM and follow Kali's installer prompts.
5. Create a unique, long user password. Do not reuse a host password.
6. Eject the ISO after installation, then boot from the virtual disk.

The installer labels can differ between Kali releases. When a choice is unclear,
use the current Kali installation documentation rather than guessing at a
partition or bootloader setting.

## 6. Configure the VM deliberately

### Recommended baseline

Start with:

- 4096 MB memory
- 2 virtual CPUs
- 40 GB or larger virtual disk
- 3D acceleration disabled unless you specifically need it and have tested it
- NAT as the first network adapter during setup

Increase memory or CPU only when the host remains responsive. Take a snapshot
before major changes, but remember that snapshots are not backups.

### Choose the network mode

| Mode | Use it for | Main risk |
| --- | --- | --- |
| NAT | Updates, package downloads, ordinary web access | Less convenient VM-to-VM access |
| Bridged | A controlled VM that must appear on the physical LAN | The VM can reach LAN devices |
| Host-Only | Host and intentionally vulnerable VMs | Usually no internet by itself |
| Internal Network | VM-to-VM labs isolated from the host and LAN | Easy to lose internet access |

For a target lab, a strong default is two adapters:

1. Adapter 1: NAT, enabled only when the lab needs updates or downloads.
2. Adapter 2: Host-Only or Internal Network for Kali-to-target traffic.

Disable the NAT adapter before testing an intentionally vulnerable target if the
lab does not require internet access. Never scan an unknown bridged network.

## 7. First boot checklist

Open a terminal in Kali and complete these steps in order:

```bash
# Confirm the release and current user.
cat /etc/os-release
id

# Change the current user's password if this was a pre-built image.
passwd

# Refresh package metadata, then apply the full distribution upgrade.
sudo apt update
sudo apt full-upgrade -y

# Reboot if the kernel or core services were upgraded.
sudo reboot
```

After reboot, confirm the machine has the expected identity and resources:

```bash
hostnamectl
uname -r
free -h
df -h /
ip -br address
ip route
```

Do not paste commands into a privileged shell unless you understand what they
do. Read package prompts and review proposed removals before accepting an
unexpected change.

## 8. Make a clean baseline

Once the system boots, updates successfully, and has the network you expect:

1. Power the VM off cleanly.
2. In VirtualBox, take a snapshot named `clean-updated-install`.
3. Record the VM's network modes and intended lab targets.
4. Export the VM or copy its virtual disk to separate storage if you need a
	recoverable backup.

Before a risky exercise, take another snapshot. Restore or delete old snapshots
regularly because long snapshot chains consume storage and can reduce disk
performance.

## 9. Verify networking without scanning anything

Run only local connectivity checks first:

```bash
ip -br address
ip route
ping -c 4 1.1.1.1
getent hosts kali.org
```

Interpretation:

- No address: check the adapter is enabled and the selected mode provides DHCP.
- No default route: inspect the adapter mode and host network connection.
- IP works but DNS fails: inspect resolver settings and captive portals.
- Host-Only or Internal Network: no internet is expected unless a separate,
  deliberately configured path exists.

## 10. Useful optional packages

Kali includes many tools, but installing everything is unnecessary. Add only
what supports your current course or lab:

```bash
sudo apt install -y git curl tmux shellcheck ripgrep jq
```

Keep projects in your home directory and use a separate lab notes directory:

```bash
mkdir -p ~/lab/{notes,tools,artifacts}
chmod 700 ~/lab
```

Do not store credentials, private keys, or sensitive capture files in a public
Git repository. Treat packet captures and scan output as potentially sensitive.

### Optional: Ollama for local models

Ollama can run local language models inside Kali, but it is not required for
Kali itself. Install it only if you have enough disk, memory, and a clear use
case such as offline note search or scripting assistance.

Use Ollama's current official Linux installation instructions, then verify the
service and choose a model appropriate for your hardware. Keep its API bound to
localhost unless you intentionally secure and expose it:

```bash
curl -fsSL https://ollama.com/install.sh | sh
ollama --version
ollama pull llama3.2
ollama run llama3.2
```

The install script and model names can change. Review official documentation
before running them, and do not send confidential lab data to a model or expose
the Ollama API to a bridged network without authentication and firewall rules.

## 11. Small lab safety rules

- Get written authorization and define the target scope before testing.
- Keep training targets on Host-Only or Internal Network when possible.
- Never use real credentials or personal data in a practice lab.
- Do not run destructive modules against systems you do not own.
- Keep snapshots and backups separate from the VM's working disk.
- Update the host, VirtualBox, and guest regularly.
- Remove unused adapters, shared folders, clipboard sharing, and drag-and-drop
  integration when they are not needed.
- Stop the VM when you are done; do not leave an exposed bridged VM unattended.

## 12. Troubleshooting

### Virtualization is unavailable

Enable VT-x/AMD-V/SVM in UEFI/BIOS. On some hosts, Hyper-V or another
hypervisor may claim the hardware virtualization feature. Follow the host OS and
VirtualBox documentation for the supported coexistence configuration.

### The VM is slow

Check host memory pressure first. Close heavy host applications, use 2-4 CPUs,
avoid allocating all host RAM, and confirm the virtual disk is on fast storage.

### The display is black or unstable

Power off the VM and try conservative display settings: one virtual monitor,
moderate video memory, and 3D acceleration disabled. Update VirtualBox and its
guest additions according to the current Kali guidance.

### Kali has no network

Confirm the adapter is enabled, start with NAT, and check `ip -br address` and
`ip route`. If only a Host-Only or Internal Network adapter is enabled, lack of
internet is expected.

### Package updates fail

Check the date and DNS, confirm the Kali repository configuration matches the
current official documentation, then retry `sudo apt update`. Do not add random
repositories to work around a package error.

## 13. Remove or reset the lab

To reset an experiment, restore the appropriate snapshot while the VM is powered
off. To retire the lab, export anything you are authorized to keep, remove
shared folders and secrets, then delete the VM and its files through VirtualBox.

## Official references

- [Kali Linux documentation](https://www.kali.org/docs/)
- [Kali installation guide](https://www.kali.org/docs/installation/)
- [Kali VirtualBox import guide](https://www.kali.org/docs/virtualization/import-premade-virtualbox/)
- [Secure Kali image downloads](https://www.kali.org/docs/introduction/download-images-securely/)
- [Oracle VirtualBox manual](https://www.virtualbox.org/manual/UserManual.html)
- [Ollama documentation](https://docs.ollama.com/)

## License

This guide is released under the [MIT License](LICENSE).