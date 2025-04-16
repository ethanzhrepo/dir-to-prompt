# dir-to-prompt

**dir-to-prompt** is a command-line tool for scanning a specified directory, selecting text files based on include/exclude rules, and outputting their contents to a single output stream or file. The output is formatted with clear delimiters indicating source file paths, making it ideal for preparing context for large language models (LLMs) or for code analysis.

## 📚 Motivation

When interacting with LLMs, providing sufficient context (such as relevant portions of a codebase or project documentation) is essential. Manually copying and pasting multiple files is tedious and error-prone. `dir-to-prompt` automates this process, allowing you to quickly collect specified project files into a well-structured text block suitable for use as a prompt.

## ✨ Features

* **Recursive Directory Scanning:** Scans the target directory and all of its subdirectories.
* **Flexible File Filtering:** Precisely include or exclude files using glob patterns.
  * Support for multiple patterns, comma-separated.
  * Exclude rules take precedence over include rules.
* **Customizable Output:** Direct the combined text to standard output (stdout) or a specified output file.
* **Clear Formatting:** Adds a header line indicating the relative path before the content of each included file.
* **Token Estimation:** Optional feature to estimate the number of tokens in the output, helping to control prompt size.

## 🚀 Installation

**Using Go Install:**

```bash
go install github.com/ethanzhrepo/dir-to-prompt@latest
```

**From Binary Release:**

Download the binary for your system from the [Releases](https://github.com/ethanzhrepo/dir-to-prompt/releases) page.

## 🛠️ Usage

```bash
dir-to-prompt --dir <directory-path> --include-files <include-patterns> [--exclude-files <exclude-patterns>] [--output <output-file-or-stdout>] [--estimate-tokens]
```

### Parameters

* **--dir \<directory-path\>:** (Required) Path to the root directory to scan.
* **--include-files \<include-patterns\>:** (Required) List of glob patterns to match files to include, comma-separated. Patterns are matched against file paths relative to the --dir directory.
  * Example: `*.go,*.txt,docs/*.md`
* **--exclude-files \<exclude-patterns\>:** (Optional) List of glob patterns to match files to exclude, comma-separated. Files matching these patterns will be ignored, even if they also match an include pattern.
  * Example: `*_test.go,vendor/*,*.tmp`
* **--output \<output-file-or-stdout\>** or **-o \<output-file-or-stdout\>:** (Optional) Specify the output destination.
  * If set to a file path (e.g., `/tmp/output.txt` or `prompt.txt`), the result will be written to that file.
  * If set to `-`, the result will be written to standard output (stdout).
  * If this parameter is omitted, it defaults to standard output (`-`).
* **--estimate-tokens:** (Optional) Estimate and display the number of tokens in the output. This is useful for preparing content for LLMs that have token limits.
  * The token count is estimated using OpenAI's tiktoken tokenizer (using the gpt-3.5-turbo model).
  * The count is displayed on stderr and won't interfere with the output content.

### Examples

Include all `.go` and `.md` files in the `~/myproject` directory, exclude test files, and output to the console:

```bash
dir-to-prompt --dir ~/myproject --include-files "*.go,*.md" --exclude-files "*_test.go"
```

Include Go files in the `src` subdirectory of `~/webapp`, all JavaScript files and text files, exclude temporary files and everything in the `node_modules` directory, and save the result to `context.txt`:

```bash
dir-to-prompt --dir ~/webapp \
              --include-files "src/**/*.go,*.js,*.txt" \
              --exclude-files "*.tmp,node_modules/*" \
              -o context.txt
```

Example with token estimation:

```bash
dir-to-prompt --dir ~/my/project \
              --include-files "*.go,*.txt,*.js,*.java,cmd/*.go" \
              --exclude-files "*.tmp,*.sh,test/*.go" \
              --estimate-tokens \
              -o /tmp/a.txt
```

## 📋 Output Format

The output begins with a tree-like directory structure showing all the files that matched the include/exclude patterns:

```
Directory Structure:

└── ./
    ├── cmd
    │   ├── common.go
    │   ├── config.go
    │   └── ... other files ...
    ├── util
    │   ├── aes.go
    │   ├── utils.go
    │   └── ... other files ...
    └── main.go
```

This is followed by the contents of all matching files concatenated together. Before the content of each file, a header line is inserted:

```
---
File: <relative-path-to-file>
---
```

Where `<relative-path-to-file>` is the path of the file relative to the directory specified by `--dir`.

Example snippet:

```
---
File: cmd/common.go
---

package cmd
// ... content of common.go ...

---
File: cmd/config.go
---

package cmd

import (
    "fmt"
// ... content of config.go ...

---
File: util/utils.go
---

package util
// ... content of utils.go ...

---
File: main.go
---

package main
// ... content of main.go ...
```

## 🤝 Contributing

Contributions are welcome! Please open an issue or submit a pull request for any problems or improvements.

## 📄 License

MIT License