# CloudLinux PHP Security Patch Verifier

A read-only Bash utility for auditing PHP security patch evidence on CloudLinux and cPanel/WHM hosting servers.

## Overview

PHP security vulnerabilities can affect websites running PHP-FPM, LiteSpeed, Apache, and other PHP execution environments. Updating only the system's default PHP installation may leave additional PHP versions unpatched.

This project helps server administrators inventory installed PHP packages and identify security patch evidence for:

- CVE-2026-109618
- CVE-2026-109619

The utility is designed to support security audits and remediation planning without automatically modifying production servers.

## Features

- Detect installed EasyApache 4 (`ea-php`) packages.
- Detect installed CloudLinux PHP Selector (`alt-php`) packages.
- Inspect RPM package changelogs for relevant CVE references.
- Report packages with matching evidence, missing evidence, or inconclusive results.
- Support manual verification of vendor security backports.
- Provide a foundation for hosting-server security audit reports.

## Supported environments

- CloudLinux 8 and compatible RPM-based environments
- cPanel/WHM with EasyApache 4
- CloudLinux PHP Selector
- Servers using Apache, LiteSpeed, or PHP-FPM

Actual compatibility depends on the installed operating system, package manager, vendor repositories, and PHP distribution.

## Repository structure

```text
cloudlinux-php-security-patch-verifier/
├── README.md
├── scripts/
│   └── verify-php-patches.sh
└── docs/
    ├── remediation-guide.md
    └── validation-checklist.md
```

## Requirements

- Bash
- RPM package manager
- Access to installed package metadata
- Root or suitable read-only privileges for complete package inventory
- Vendor security advisories for interpreting package results

## Quick start

Clone the repository:

```bash
git clone https://github.com/YOUR-USERNAME/cloudlinux-php-security-patch-verifier.git
```

Enter the directory:

```bash
cd cloudlinux-php-security-patch-verifier
```

Review the script before executing it:

```bash
less scripts/verify-php-patches.sh
```

Make the script executable:

```bash
chmod +x scripts/verify-php-patches.sh
```

Run the audit:

```bash
./scripts/verify-php-patches.sh
```

Replace `YOUR-USERNAME` with your GitHub username after creating the repository.

## Manual package inventory

List installed EasyApache PHP packages:

```bash
rpm -qa | grep -E '^ea-php[0-9]+'
```

List installed CloudLinux PHP Selector packages:

```bash
rpm -qa | grep -E '^alt-php'
```

Inspect a package's changelog for the affected CVEs:

```bash
rpm -q --changelog PACKAGE_NAME |
grep -E 'CVE-2026-109618|CVE-2026-109619'
```

Replace `PACKAGE_NAME` with an installed package name.

An empty result does not necessarily mean the package is vulnerable. The vendor may document the fix differently, provide a separate advisory, or backport a patch without including the CVE identifier in the package changelog.

## Understanding audit results

| Result | Meaning | Recommended action |
|---|---|---|
| Patch evidence found | Matching CVE evidence exists in the inspected package metadata | Confirm the package release against the vendor advisory |
| No evidence found | The inspected metadata did not contain the expected CVE reference | Check vendor advisories and package release information |
| Package unavailable | The requested package could not be queried | Confirm installation and package-manager access |
| Manual verification required | Available metadata cannot establish patch status | Consult the vendor's security bulletin |

**Important:** A changelog match is evidence, not definitive proof that every active PHP runtime is secure.

## Remediation workflow

### 1. Inventory PHP runtimes

Identify all installed PHP distributions and versions, including versions used by individual cPanel accounts.

### 2. Check vendor advisories

Consult the official PHP, CloudLinux, and cPanel security documentation to determine the applicable fixed package releases.

### 3. Update affected packages

Use the appropriate vendor-supported update process. For cPanel servers, review EasyApache 4 package updates through WHM or the supported package-management workflow.

For CloudLinux PHP Selector, verify the applicable `alt-php` packages and their vendor-provided security fixes.

Review the package transaction before confirming any changes.

### 4. Verify the update

After updating, confirm that the installed package release includes the relevant vendor security fix. Check every PHP distribution used by hosted websites.

### 5. Validate production services

Test representative websites and review:

- HTTP 500 and 503 errors
- PHP-FPM or LiteSpeed PHP handler failures
- Apache and LiteSpeed service health
- PHP extension compatibility
- Application and PHP error logs
- Websites assigned to older PHP versions

Restart or reload services only when required by the vendor's update instructions.

## Production safety

This utility is intended for auditing, not automatic remediation.

- Do not automatically upgrade or downgrade PHP packages.
- Do not restart production services without an approved maintenance plan.
- Do not assume an upstream PHP version number proves whether a vendor-backported fix is installed.
- Do not remove legacy PHP versions before checking account dependencies.
- Maintain a verified backup and rollback plan before package changes.
- Test important applications after security updates.
- Do not expose audit reports publicly if they contain sensitive infrastructure information.

## Limitations

This project does not guarantee that a server is free of vulnerabilities or malware.

A package-level audit may not detect:

- PHP runtimes installed outside the RPM package manager
- Custom-compiled PHP binaries
- Containerized PHP environments
- Unpatched libraries or extensions outside the inspected package
- Vulnerabilities in application code or third-party plugins
- Security fixes documented outside package changelogs

The audit results must be interpreted alongside current vendor advisories.

## Official references

- [PHP Security Releases](https://www.php.net/archive/2026.php)
- [CloudLinux Blog](https://blog.cloudlinux.com/)
- [cPanel Security Advisories](https://support.cpanel.net/hc/en-us/sections/360007115854-Security)
- [cPanel EasyApache 4 Documentation](https://docs.cpanel.net/ea4/)
- [CloudLinux Documentation](https://docs.cloudlinux.com/)

## Contributing

Contributions are welcome. Please include reproducible test cases and documentation for any new package-detection logic.

Before submitting changes, verify that the script does not modify packages, restart services, or alter customer websites.

## License

Distributed under the MIT License. Add a `LICENSE` file containing the full MIT License text before publishing the repository as MIT-licensed.

## Disclaimer

This project is provided for educational and administrative purposes. Always verify security status against the applicable vendor advisories before making production changes.
