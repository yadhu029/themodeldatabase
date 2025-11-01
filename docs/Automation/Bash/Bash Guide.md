
---
## Introduction to Shell Scripting

A **shell** is a command-line interface that allows users to interact with the operating system by typing commands. It acts as a bridge between the user and the kernel.

### Types of Shells

- **Bash** (Bourne Again Shell)
- **C Shell** (`csh`)
- **Korn Shell** (`ksh`)
- **T C Shell** (`tcsh`)
- **Z Shell** (`zsh`)

> This guide focuses on **Bash**, which is the default shell in most Linux distributions.

---

## Why Write Shell Scripts?

- Automate repetitive tasks
- Schedule background jobs
- Manage system operations
- Rapidly prototype command pipelines

---

## Writing Your First Bash Script

1. **Create a script file** with `.sh` extension:
    ```bash
    vim hello.sh
    ```

2. **Add a shebang** (defines the interpreter):
    ```bash
    #!/bin/bash
    echo "Hello, World"
    ```

3. **Make it executable**:
    ```bash
    chmod +x hello.sh
    ```

4. **Run the script**:
    ```bash
    ./hello.sh
    ```

**Output:**
````

Hello, World

````

---

## Variables in Bash

### Defining Variables

```bash
name="Chronos"
echo "Hello, $name"
````

> Note: No spaces before or after the `=` sign.

---

## Arithmetic Expressions

### Basic Operators

| Operator | Description         |
| -------- | ------------------- |
| `+`      | Addition            |
| `-`      | Subtraction         |
| `*`      | Multiplication      |
| `/`      | Division            |
| `**`     | Exponentiation      |
| `%`      | Modulus (remainder) |

### Examples

Using `expr`:

```bash
expr 11 + 1
expr 16 % 11
```

Using `$(( ))` for inline evaluation:

```bash
result=$((3 + 9))
echo $result
```

---

## Reading User Input

### Syntax

```bash
read variable_name
read -p "Prompt: " variable_name
```

### Example

```bash
#!/bin/bash
echo "Enter a number:"
read a

read -p "Enter another number: " b

sum=$((a + b))
echo "Sum is: $sum"
```

---

## Conditional Statements

### Numeric Comparison

| Operator | Description              |
| -------- | ------------------------ |
| `-eq`    | Equal                    |
| `-ne`    | Not equal                |
| `-gt`    | Greater than             |
| `-lt`    | Less than                |
| `-ge`    | Greater than or equal to |
| `-le`    | Less than or equal to    |

### Syntax

```bash
if [ condition ]; then
    # commands
fi
```

### Example

```bash
read x
read y

if [ $x -gt $y ]; then
    echo "X is greater than Y"
elif [ $x -lt $y ]; then
    echo "X is less than Y"
else
    echo "X is equal to Y"
fi
```

---

## Logical Operators

* `-a` → AND
* `-o` → OR

### Example: Triangle Type

```bash
read a
read b
read c

if [ $a -eq $b -a $b -eq $c ]; then
    echo "EQUILATERAL"
elif [ $a -eq $b -o $b -eq $c -o $a -eq $c ]; then
    echo "ISOSCELES"
else
    echo "SCALENE"
fi
```

---

## Looping Constructs

### For Loop (Numbers)

```bash
for i in {1..5}; do
    echo $i
done
```

### For Loop (Strings)

```bash
for fruit in apple orange grape; do
    echo $fruit
done
```

### While Loop

```bash
i=1
while [[ $i -le 10 ]]; do
    echo $i
    ((i++))
done
```

---

## Reading Files Line-by-Line

```bash
#!/bin/bash

LINE=1
while read -r CURRENT_LINE; do
    echo "$LINE: $CURRENT_LINE"
    ((LINE++))
done < "text-file.txt"
```

---

## Command Substitution

### Using Backticks

```bash
var=`df -h | grep tmpfs`
```

> Prefer `$(command)` over backticks for readability:

```bash
var=$(df -h | grep tmpfs)
```

---

## Passing Arguments to Scripts

Use `$@` to refer to all positional arguments.

### Example

```bash
#!/bin/bash

for arg in "$@"; do
    echo "Argument: $arg"
done
```

**Execution:**

```bash
$ ./script.sh 1 2 3
Argument: 1
Argument: 2
Argument: 3
```

---

## Automating with Cron

**Cron** is used to schedule scripts or commands.

### Crontab Syntax

```cron
* * * * * command
│ │ │ │ │
│ │ │ │ └─ Day of Week (0–7, Sunday=0/7)
│ │ │ └─── Month (1–12)
│ │ └───── Day of Month (1–31)
│ └─────── Hour (0–23)
└───────── Minute (0–59)
```

### Examples

* `10 1 * 1 *` → Every **Jan 1st at 01:10**
* `0 12 * * *` → Every **day at 12:00 PM**
* `0 22 * * 7` → Every **Sunday at 10:00 PM**

### Editing Cron Jobs

```bash
crontab -e
```

### Example Entry

```cron
0 6 * * * /home/user/scripts/backup.sh
```

---

## Best Practices

* Always start with `#!/bin/bash`
* Use `set -e` to stop script on first error
* Quote variables: `"$var"` to prevent globbing and word splitting
* Validate input when reading from user or CLI
* Use comments generously to document code
* Avoid hardcoding paths; use variables
* Log output to files for long-running cron jobs

---
