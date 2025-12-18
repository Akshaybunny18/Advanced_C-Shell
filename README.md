# C-Shell 🐚

A feature-rich Unix shell implementation in C with advanced process management, I/O redirection, piping, and networking capabilities.

[![Language](https://img.shields.io/badge/Language-C-blue.svg)](https://en.wikipedia.org/wiki/C_(programming_language))
[![Standard](https://img.shields.io/badge/Standard-POSIX-green.svg)](https://pubs.opengroup.org/onlinepubs/9699919799/)
[![Tests](https://img.shields.io/badge/Tests-108%2F125-brightgreen.svg)](autograder_logs_final/summary.json)

> A comprehensive implementation of a Unix shell developed as part of the Operating Systems and Networks course at IIIT Hyderabad.

## 📋 Table of Contents

- [Features](#-features)
- [Architecture](#-architecture)
- [Getting Started](#-getting-started)
- [Usage](#-usage)
- [Built-in Commands](#-built-in-commands)
- [Advanced Features](#-advanced-features)
- [Networking Component](#-networking-component)
- [Testing](#-testing)
- [Project Structure](#-project-structure)
- [Implementation Details](#-implementation-details)

## ✨ Features

### Core Shell Functionality
- **Custom Prompt**: Displays username, hostname, and current directory with tilde expansion
- **Command Parsing**: Robust CFG-based parser supporting complex command chains
- **Process Management**: Foreground and background process execution with job control
- **I/O Redirection**: Full support for input (`<`), output (`>`), and append (`>>`) redirection
- **Piping**: Multi-level command piping with proper file descriptor management

### Built-in Commands
- `hop` - Advanced directory navigation with history
- `reveal` - Enhanced file listing with multiple flags
- `log` - Command history with persistence and execution
- `activities` - Real-time process monitoring
- `ping` - Signal sending to processes
- `fg/bg` - Foreground/background job control

### Process Control
- **Signal Handling**: Proper handling of SIGINT (Ctrl+C), SIGTSTP (Ctrl+Z), and EOF (Ctrl+D)
- **Job Management**: Background job tracking with completion notifications
- **Process Groups**: Correct process group management for job control

### Networking
- **SHAM Protocol**: Custom reliable protocol built on UDP
- **File Transfer**: Efficient file transfer with checksums
- **Flow Control**: Window-based flow control implementation
- **Retransmission**: Timeout-based packet retransmission

## 🏗 Architecture

```
┌─────────────────────────────────────────┐
│           User Input Layer              │
├─────────────────────────────────────────┤
│  Tokenizer → Parser → Command Executor  │
├─────────────────────────────────────────┤
│  Built-in Commands  │  System Commands  │
├─────────────────────┼───────────────────┤
│  Process Manager    │   Signal Handler  │
├─────────────────────┴───────────────────┤
│           POSIX System Calls            │
└─────────────────────────────────────────┘
```

The shell is structured in a modular fashion with separate components for:
- **Input Processing**: Tokenization and parsing
- **Command Execution**: Built-in and external command handling
- **Process Management**: Job tracking and control
- **Signal Handling**: Asynchronous event processing

## 🚀 Getting Started

### Prerequisites
- GCC compiler with C99 support
- POSIX-compliant operating system (Linux, macOS, WSL)
- OpenSSL development libraries (for networking component)

### Installation

1. **Clone the repository**
   ```bash
   git clone https://github.com/Akshaybunny18/C-Shell.git
   cd C-Shell
   ```

2. **Build the shell**
   ```bash
   cd shell
   make all
   ```

3. **Run the shell**
   ```bash
   ./shell.out
   ```

### Building the Networking Component

```bash
cd networking
make
./server    # In one terminal
./client    # In another terminal
```

## 💻 Usage

### Basic Commands

```bash
# Navigate directories
<user@host:~> hop /path/to/directory
<user@host:/path/to/directory> hop ~
<user@host:~> hop -    # Go to previous directory

# List files
<user@host:~> reveal
<user@host:~> reveal -a    # Show hidden files
<user@host:~> reveal -l    # Line-by-line format
<user@host:~> reveal -al   # Combined flags

# View command history
<user@host:~> log
<user@host:~> log execute 1    # Execute most recent command
<user@host:~> log purge        # Clear history
```

### I/O Redirection

```bash
# Input redirection
<user@host:~> cat < input.txt

# Output redirection (overwrite)
<user@host:~> ls > output.txt

# Output redirection (append)
<user@host:~> echo "Hello" >> output.txt

# Combined redirection
<user@host:~> sort < input.txt > sorted.txt
```

### Piping

```bash
# Simple pipe
<user@host:~> ls | grep "file"

# Multi-level piping
<user@host:~> cat file.txt | grep "pattern" | sort | uniq

# Pipe with redirection
<user@host:~> cat < input.txt | sort > output.txt
```

### Background Execution

```bash
# Run in background
<user@host:~> sleep 10 &
[1] 12345

# Sequential execution
<user@host:~> echo "First" ; echo "Second" ; echo "Third"

# Mixed execution
<user@host:~> echo "Foreground" ; sleep 5 & ; echo "Done"
```

### Job Control

```bash
# List active jobs
<user@host:~> activities

# Send signal to process
<user@host:~> ping 12345 9

# Bring background job to foreground
<user@host:~> fg 1

# Resume stopped job in background
<user@host:~> bg 1

# Stop foreground process
<user@host:~> sleep 100
^Z
[1] Stopped sleep
```

## 🛠 Built-in Commands

### `hop [paths...]`
Navigate through directories with support for special paths.

**Arguments:**
- `~` or no args: Home directory
- `.`: Current directory (no-op)
- `..`: Parent directory
- `-`: Previous directory
- `path`: Relative or absolute path

**Examples:**
```bash
hop ~
hop /usr/local/bin
hop folder1 .. folder2
hop -
```

### `reveal [flags] [path]`
List directory contents with customizable output.

**Flags:**
- `-a`: Show hidden files
- `-l`: Line-by-line format
- `-al` or `-la`: Combined (all files, one per line)

**Examples:**
```bash
reveal
reveal -l ~/Documents
reveal -al .
```

### `log [purge | execute <index>]`
Manage command history (persists across sessions).

**Features:**
- Stores up to 15 commands
- Skips sequential duplicates
- 1-indexed from newest to oldest

**Examples:**
```bash
log                # Show history
log execute 1      # Execute most recent
log purge          # Clear history
```

### `activities`
Display all shell-spawned processes.

**Output Format:**
```
[pid] : command_name - State
```
States: `Running`, `Stopped`

### `ping <pid> <signal>`
Send signals to processes.

**Example:**
```bash
ping 12345 9    # Send SIGKILL
ping 12345 19   # Send SIGSTOP
```

### `fg [job_number]` / `bg [job_number]`
Control background and stopped jobs.

**Examples:**
```bash
fg        # Bring most recent job to foreground
fg 2      # Bring job #2 to foreground
bg 1      # Resume job #1 in background
```

## 🎯 Advanced Features

### Command Parsing Grammar

The shell implements a context-free grammar parser:

```
shell_cmd  →  cmd_group ((& | ;) cmd_group)* &?
cmd_group  →  atomic (| atomic)*
atomic     →  name (name | input | output)*
input      →  < name
output     →  > name | >> name
name       →  [^|&><;\s]+
```

### Signal Handling

| Signal | Action |
|--------|--------|
| **Ctrl+C** | Terminate foreground process (shell continues) |
| **Ctrl+Z** | Stop foreground process, move to background |
| **Ctrl+D** | Kill all child processes and exit shell |

### Process Management

- **Foreground processes**: Shell waits for completion
- **Background processes**: Shell immediately returns
- **Job tracking**: Automatic cleanup with notifications
- **Process groups**: Proper management for signal delivery

## 🌐 Networking Component

### SHAM Protocol (Simple Hybrid Assured Messaging)

A custom reliable transport protocol built on UDP featuring:

- **Three-way handshake**: SYN/ACK connection establishment
- **Sequence numbers**: Ordered packet delivery
- **Acknowledgments**: Reliable delivery confirmation
- **Flow control**: Window-based transmission control
- **Retransmission**: Timeout-based packet resending
- **Checksums**: MD5-based file integrity verification

### Server Features

```bash
# Start server
./server [port]

# Commands
upload <filename>      # Receive file from client
download <filename>    # Send file to client
```

### Client Features

```bash
# Connect to server
./client <server_ip> <port>

# Commands
upload <filename>      # Send file to server
download <filename>    # Receive file from server
```

## 🧪 Testing

### Automated Testing

The project includes comprehensive automated testing:

```bash
# Results: 108/125 tests passed (86.4%)
```

**Test Coverage:**
- ✅ Shell prompt and input handling
- ✅ Command parsing and syntax validation
- ✅ Built-in commands (hop, reveal, log)
- ✅ I/O redirection and piping
- ✅ Process execution and management
- ✅ Background/foreground execution
- ✅ Signal handling
- ⚠️ Some edge cases in job control

### Running Tests

Test results are available in:
- `autograder_logs_final/summary.json` - Final test results
- `autograder_logs_final/tests/` - Individual test outputs

## 📁 Project Structure

```
C-Shell/
├── shell/                      # Main shell implementation
│   ├── src/
│   │   ├── main.c             # Entry point and main loop
│   │   ├── prompt.c           # Prompt display
│   │   ├── input.c            # Input handling
│   │   ├── tokenizer.c        # Input tokenization
│   │   ├── parser.c           # Grammar-based parsing
│   │   ├── executor.c         # Command execution
│   │   ├── hop.c              # Directory navigation
│   │   ├── reveal.c           # File listing
│   │   ├── log.c              # Command history
│   │   ├── jobs.c             # Job management
│   │   ├── ping.c             # Signal sending
│   │   └── signals.c          # Signal handling
│   ├── include/               # Header files
│   └── Makefile
│
├── networking/                 # SHAM protocol implementation
│   ├── server.c               # Server implementation
│   ├── client.c               # Client implementation
│   ├── sham.c                 # Protocol implementation
│   ├── sham.h                 # Protocol definitions
│   └── Makefile
│
├── xv6/                       # xv6 scheduler implementation
│   ├── readcount.c            # System call implementation
│   ├── report.md              # Implementation report
│   └── xv6_modifications.patch
│
├── autograder_logs_final/     # Test results
├── question.md                # Original assignment
└── README.md                  # This file
```

## 🔧 Implementation Details

### Memory Management
- Dynamic allocation for command structures
- Proper cleanup of resources
- No memory leaks in core functionality

### File Descriptor Management
- Proper closing of unused pipe ends
- Restoration of stdin/stdout after redirection
- Prevention of file descriptor leaks

### Process Groups
- Each job runs in its own process group
- Correct signal delivery to process groups
- Foreground process group management

### Concurrency
- Signal-safe code in handlers
- Proper handling of race conditions
- Asynchronous background job cleanup

### Error Handling
- Comprehensive error checking
- Informative error messages
- Graceful degradation

## 🔍 Key Algorithms

### Command Execution Pipeline

```c
1. Read input from user
2. Check for completed background jobs
3. Tokenize input string
4. Parse tokens using CFG
5. For each command group:
   a. Set up I/O redirections
   b. Create pipes if needed
   c. Fork child processes
   d. Execute command
   e. Wait if foreground, track if background
6. Clean up resources
7. Display prompt
```

### Job Management

```c
1. Maintain job list with PIDs and states
2. On SIGCHLD, reap zombie processes
3. Update job states (Running/Stopped)
4. Print completion messages
5. Remove finished jobs from list
```

## 📊 Performance

- **Parsing**: O(n) time complexity for token-based parsing
- **Job lookup**: O(n) for job management operations
- **Memory**: Efficient use with minimal overhead
- **Latency**: Near-instant response for built-in commands

## 🐛 Known Issues & Future Work

### Current Limitations
- Some edge cases in command history indexing
- Empty input handling could be improved
- Background process cleanup timing

### Future Enhancements
- [ ] Command auto-completion
- [ ] History search (Ctrl+R)
- [ ] Colored output
- [ ] Configuration file support
- [ ] Alias support
- [ ] Tab completion
- [ ] Command substitution
- [ ] Environment variable expansion

## 📝 Compilation Flags

```makefile
CFLAGS = -std=c99 \
         -D_POSIX_C_SOURCE=200809L \
         -D_XOPEN_SOURCE=700 \
         -Wall -Wextra -Werror \
         -Wno-unused-parameter \
         -fno-asm
```

## 🤝 Contributing

This project was developed as a course assignment. The implementation demonstrates:
- Strong understanding of Unix system calls
- Process management expertise
- Signal handling proficiency
- Network programming skills
- Clean, modular code design

## 📜 License

Educational project - IIIT Hyderabad Operating Systems and Networks course.

## 👨‍💻 Author

**Chanda Akshay Kumar**
- GitHub: [@Akshaybunny18](https://github.com/Akshaybunny18)
- Institution: IIIT Hyderabad
- Course: Operating Systems and Networks (Monsoon 2024)

## 🙏 Acknowledgments

- IIIT Hyderabad for the comprehensive assignment
- Course instructors for detailed specifications
- POSIX documentation for system call references
- The Unix philosophy for design inspiration

---

<div align="center">

**Built with ❤️ using C and POSIX APIs**

</div>
