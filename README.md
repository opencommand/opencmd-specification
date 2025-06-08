## What is the OpenCmd Specification?

The **OpenCmd Specification (OCS)** defines a standardized, command-agnostic interface description for command-line tools. It enables both humans and machines to discover and understand the functionality of a command **without needing to execute it**, consult external documentation, or analyze error outputs. When properly described using OpenCmd, a consumer can gain a clear understanding of a command’s structure, capabilities, and usage.

## Why is this standard necessary?

Command-line interfaces vary significantly across platforms such as Linux and Windows. For instance, **flag syntax differs**:

* On **Linux**, flags are typically prefixed with `-` or `--`
* On **Windows**, flags often use `/`

These inconsistencies can cause confusion and hinder cross-platform tooling. OpenCmd aims to bridge this gap by providing a unified format that abstracts away platform-specific syntax, making command interfaces easier to interpret, document, and integrate.

## 1. OpenCmd Specification

### 1.1 Version 1.0.0

This document is licensed under [The MIT License](LICENSE).

### Command Identifier

The command identifier is the primary name used to invoke a command-line tool. To ensure compatibility across platforms and tooling systems, the command name must follow a strict but practical naming convention.

✅ **Naming Rules**

* Must begin with an English alphabetic character (`A-Z`, `a-z`)
* May contain:
    * Alphanumeric characters (`A-Z`, `a-z`, `0-9`)
    * Hyphens (`-`) as word separators
    * Underscores (`_`) if needed for legacy compatibility
* Should not begin with a digit, symbol, or non-English character

📌 **Examples**

| Identifier | Valid? |	Reason |
| -----| ----- | ----- |
| 1Hello | ❌ | Begins with a digit |
| 你好-world | ❌ | Begins with non-English characters |
| Say-Hello | ✔ | Valid: English word, uses - |
| My_Echo2 | ✔ | Valid: uses underscore and numbers |
| echo-world |	✔ |	Common lowercase format with - |
| _start | ❌ | Begins with underscore |

🔍 **Best Practices**
* Prefer **lowercase** for consistency (`say-hello`, `my-echo`)
* Use `-` to separate words for readability (`generate-report`, `convert-pdf`)
* Avoid names that conflict with reserved system commands (`cd`, `ls`, `dir`, etc.)

## Handling Duplicate Command Names

In some cases, multiple vendors may define commands with the same identifier (e.g., `git`) but different implementations, behaviors, or purposes. To support coexistence and precise resolution, the OpenCmd Specification defines the following disambiguation mechanism.

✅ **Disambiguation Strategies**

Every command should have a globally unique identifier formed as:

**1. Fully Qualified Name (FQN)**
```sh
<vendor>::<name>[@<version>]
```

Examples:

* `git-scm::git@2.44.0` – Official Git
* `acme-tools::git@1.0.0` – Vendor-customized Git-like tool

This ensures commands with the same visible name are distinguishable by tooling and users.

**2. Metadata Field: `vendor`**

In YAML or JSON schema, always include a field to declare the origin of the command.

```yaml
name: git
version: 2.44.0
vendor: git-scm
```

## Options

The `options` section defines the flags and parameters accepted by a command. OpenCmd adopts a Unix-style flag design with support for short, long, and grouped boolean flags.

**✅ Flag Forms**

**1. Short Options**

* Single-character prefixed with a single dash (`-`)
* Example: `-a`, `-v`, `-h`

**2. Long Options**

* Words or phrases, prefixed with double dashes (`--`)
* Use kebab-case for multi-word names
* Example: `--all`, `--dry-run`, `--output-format`

**3. Grouped Short Boolean Flags**

* Multiple short flags that take no value can be grouped together
* Example: `-a -b -c` ⇒ `-abc`

**4. Option with Value**
Options can accept values of specific types, such as strings, numbers, booleans, enums, paths, and more. This enables commands to expose rich, typed interfaces for configuration.

* Short form: `-o value`
* Long form: `--output value`

**Value Types:**

| Type | Description | Example Input |
| ---- | ---- | ---- |
| str | Free-form text | `--name "Alice"` |
| num | numbers | `--count 5`, `--ratio 0.75` |
| bool | Boolean values (true/false); often implicit with flags | `--verbose` |
| enum |
| list | Comma-separated values or multiple use | `--tag "a" --tag "b`, --tag ["a", "b"] |
| map | 
| date | Date strings in a defined format | `--start "2024-01-01"` |
| command | 
| object |

**📌 Naming Convention**

* Use lowercase letters only (`a`–`z`)
* Multi-word long flags use **kebab-case**
    * ✅ `--dry-run`
    * ❌ `--dry_run`, `--DryRun`

**🧪 Examples**

```sh
mycmd -a --verbose --output "result.txt"
mycmd -abc            # Equivalent to -a -b -c
mycmd --dry-run
mycmd -ao "result.txt"   # Equivalent to -a -o "result.txt"
```
