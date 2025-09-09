---

# The Many Faces of DNF: From YUM to DNF5

If you’ve been around the RHEL/Fedora ecosystem for a while, you’ve probably noticed a confusing mix of package managers: **yum**, **dnf**, **dnf-3**, **dnf4**, **dnf5**, even **microdnf**. On Fedora 41 today, all of these names can exist side by side in `/usr/bin`. Let’s untangle what’s really going on.

---

## A Quick History

* **YUM (Yellowdog Updater, Modified)**
  The classic Python package manager used up to RHEL 7. It handled dependencies but had a reputation for being slow.

* **DNF (aka DNF4)**
  Introduced in Fedora 18 and adopted fully by Fedora 22 and RHEL 8. A rewrite of yum in Python 3, using the new **libdnf** backend. Faster, cleaner, better API support.

* **microdnf**
  A tiny, dependency-free variant of DNF4 for minimal container images. Good for scripted installs, but very limited compared to full dnf.

* **DNF5**
  A complete rewrite in **C++** with the **libdnf5** backend. Smaller footprint, faster execution, and meant to replace both DNF4 and microdnf. Fedora 41 makes this the default.

---

## What You’ll See in `/usr/bin`

On Fedora 41, a directory listing reveals the transition in action:

* `dnf → dnf5` → the default command is now the C++ version.
* `dnf5` → the actual binary (\~1.5 MB ELF, no Python needed).
* `dnf4 → dnf-3` → legacy symlink, still calling the Python-based DNF4.
* `dnf-3` → the DNF4 implementation in Python 3.
* `yum → dnf5` → yum is just an alias for dnf5.
* `microdnf → dnf5` → microdnf is fully retired.
* `dnf-utils` helpers (`repoclosure`, `yumdownloader`, etc.) → still small Python wrappers.

In other words: you really only have **two implementations** left—Python-based DNF4 (for compatibility) and C++-based DNF5 (for everything new).

---

## Key Differences in DNF5

1. **Performance & Footprint**

   * Written in C++, starts faster and avoids Python dependencies.
   * Unifies what used to be `dnf`, `yum`, and `microdnf`.

2. **Command Behavior Changes**

   * Some options are stricter: short options no longer take `=`, global flags moved to per-command scope.
   * `dnf updateinfo` → `dnf5 advisory`.
   * `dnf history <id>` → `dnf5 history info <id>`.
   * `dnf config-manager --add-repo URL` → `dnf5 config-manager addrepo URL`.
   * `--downloaddir` → `--destdir`.
   * `--sec-severity` → `--advisory-severities`.

3. **Removed or Changed Features**

   * Deltarpm support is gone.
   * `best=true` is now the default.
   * Metadata caching handled differently (`dnf5-makecache.timer`).
   * `dnf-automatic` replaced by `dnf5-automatic`.

4. **System Upgrade Built-in**

   * No plugin required. `dnf5 system-upgrade download --releasever=42` is the new workflow.

---

## Cheat Sheet: Old vs New

| Task              | Old (dnf/yum)                              | Fedora 41 (dnf5)                              |
| ----------------- | ------------------------------------------ | --------------------------------------------- |
| Install pkg       | `dnf install foo`                          | `dnf5 install foo`                            |
| Remove pkg        | `dnf remove foo`                           | `dnf5 remove foo`                             |
| Upgrade all       | `dnf upgrade`                              | `dnf5 upgrade [--minimal]`                    |
| Distro sync       | `dnf distro-sync`                          | `dnf5 distro-sync`                            |
| Advisory list     | `dnf updateinfo list`                      | `dnf5 advisory list`                          |
| Security severity | `--sec-severity=…`                         | `--advisory-severities=…`                     |
| Add repo          | `dnf config-manager --add-repo URL`        | `dnf5 config-manager addrepo URL`             |
| Disable repo      | `dnf config-manager --set-disabled fedora` | `dnf5 config-manager setopt fedora.enabled=0` |
| Dump config       | `dnf config-manager --dump`                | `dnf5 --dump-main-config`                     |
| Offline staging   | (N/A)                                      | `dnf5 <cmd> --offline`                        |
| System upgrade    | `dnf system-upgrade` (plugin)              | `dnf5 system-upgrade` (built-in)              |

---

## Where We Are Now

* On **RHEL 8/9**: `yum` and `dnf` both mean **DNF4 (Python)**.
* On **Fedora 41**: `dnf`, `yum`, and `microdnf` all mean **DNF5 (C++)**.
* `dnf-3`/`dnf4` remain for backward compatibility, but the future is DNF5.

---

## Conclusion

What used to be a tangle of names is consolidating fast. With Fedora 41, **DNF5 is the one package manager to rule them all**—faster, cleaner, and consistent across desktop, server, and containers. If you’re writing scripts or documentation, start using **`dnf5` syntax** now. The old commands are still there for compatibility, but their days are numbered.

---
