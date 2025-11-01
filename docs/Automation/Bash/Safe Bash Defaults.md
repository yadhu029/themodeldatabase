
---

# Safe Bash Defaults — `set -euo pipefail`

## Overview
In Bash scripting, the combination `set -euo pipefail` enforces **strict error handling** and **predictable execution**.  
It ensures that your script fails fast, loudly, and for the right reasons — essential when automating cron jobs, backups, or server tasks.

---

## What Each Option Does

### **`set -e` → Exit on Error**
Stops the script immediately if **any command fails** (non-zero exit code).

```bash
set -e
echo "Starting..."
cp /backup/source.txt /backup/dest/   # If file missing, script stops here
echo "Done!"  # Never reached if cp fails
````

**Use Case:**  
Avoids cascading errors when an early step (like a copy or query) fails silently.

---

### **`set -u` → Treat Undefined Variables as Errors**

If the script tries to use a variable that hasn’t been defined, Bash will throw an error and exit.

```bash
set -u
echo "Home: $HOME"
echo "Tmp: $TMP_DIR"  # If TMP_DIR isn’t set → error
```

**Use Case:**  
Catches typos like `$HMOE` instead of `$HOME`.

---

### **`set -o pipefail` → Catch Failures in Pipelines**

Without `pipefail`, Bash ignores failures from all but the **last command** in a pipeline.  
With it, Bash treats any failure within a pipeline as an overall failure.

```bash
set -o pipefail
grep "pattern" file.txt | sort | uniq > output.txt
```

If `grep` fails (file missing), the pipeline now reports a failure correctly.

**Use Case:**  
Accurate error handling in multi-stage operations (log parsing, monitoring, data processing).

---

## Safe Script Template

```bash
#!/bin/bash
set -euo pipefail
IFS=$'\n\t'  # Optional: safer word splitting

LOGFILE="/var/log/safe-script.log"
{
  echo "=== Script started at $(date) ==="
  cp /important/data.txt /backup/
  echo "Backup complete!"
} >> "$LOGFILE" 2>&1
```

**Behavior:**

- Script stops on any error.
    
- Undefined variables trigger failure.
    
- Pipeline errors are caught.
    
- Output is logged with timestamps.
    

---

## Why It Matters

|Problem|Without `set -euo pipefail`|With `set -euo pipefail`|
|---|---|---|
|Missing file|Script continues silently|Script stops immediately|
|Typo in variable|Expands to empty string|Script aborts|
|Pipeline failure|Ignored|Properly detected|

This makes your scripts **reliable and reproducible**, especially under automation frameworks like `cron`, `systemd`, or Nagios plugins.

---

## Pro Tips

- Always include this at the start of production scripts:
    
    ```bash
    #!/bin/bash
    set -euo pipefail
    IFS=$'\n\t'
    ```
    
- When a command may fail intentionally, handle it explicitly:
    
    ```bash
    grep "pattern" file.txt || true
    ```
    
- Log important steps with timestamps:
    
    ```bash
    echo "$(date): Task completed" >> /var/log/your-script.log
    ```
    
- Test interactively before scheduling via cron.
    

---

## References

- [GNU Bash Manual: The Set Builtin](https://www.gnu.org/software/bash/manual/bash.html#set-Builtin)
    
- [Bash Pitfalls: Error Handling](https://mywiki.wooledge.org/BashPitfalls)
    
- [ShellCheck Linter](https://www.shellcheck.net/)
    

--- 
