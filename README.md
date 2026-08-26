<div align="center">
  <img src=".assets/banner.png" width="100%" alt="libft banner" />

  <p>
    <b>A from-scratch reimplementation of the C standard library — the dependency every later 42 project builds on.</b>
  </p>

  <p>
    <a href="https://42lausanne.ch"><img src="https://img.shields.io/badge/42-Lausanne-000000?style=for-the-badge&logo=42&logoColor=white" alt="42 Lausanne" /></a>
    <img src="https://img.shields.io/badge/C-00599C?style=for-the-badge&logo=c&logoColor=white" alt="C" />
    <img src="https://img.shields.io/badge/Norminette-passing-2ea44f?style=for-the-badge" alt="Norm passing" />
    <img src="https://img.shields.io/badge/grade-125%2F100-5ec8c5?style=for-the-badge" alt="Grade 125/100" />
  </p>

  <p>
    <a href="#-overview">Overview</a> •
    <a href="#-highlights">Highlights</a> •
    <a href="#-build--usage">Build & Usage</a> •
    <a href="#-design-notes">Design Notes</a> •
    <a href="#-library-structure">Library Structure</a> •
    <a href="#-function-reference">Function Reference</a> •
    <a href="#-result">Result</a>
  </p>
</div>

---

## 💡 Overview

No `strlen`, no `malloc` wrapper, no `memcpy` — just the man pages and a blank file. `libft` rebuilds the core of `string.h`, `stdlib.h`, and `ctype.h` byte by byte, then adds a linked-list toolkit and a `printf` engine on top. Every later project in this repo links against it.

The interesting part isn't reimplementing the easy 90% — it's the edge cases: what `strlcpy` returns versus what it actually copies, how `memmove` has to reason about overlapping memory instead of just looping, why `atoi` has to eat leading whitespace and a sign before it sees a digit. All of it under `-Wall -Wextra -Werror`, zero warnings tolerated.

> **[📖 Read the full subject](.assets/Libft.en.pdf)**

---

## ✨ Highlights

- 🧱 **90+ functions, one per file** — character, string, memory, conversion, and output primitives, split exactly as the Norm requires.
- 🛡️ **Zero warnings, zero shortcuts** — `-Wall -Wextra -Werror`, no `for` loops, one instruction per line.
- 💾 **No leaks, ever** — every allocator handles `malloc` failure without leaking what it already grabbed.
- 🔗 **`get_next_line` + `ft_printf` built in** — not bolted on afterward, first-class parts of the library.

---

## 🚀 Build & Usage

```bash
make        # builds libft.a (mandatory part + get_next_line + printf helper)
make bonus  # also builds the linked-list functions
make clean  # removes object files
make fclean # removes object files and libft.a
make re     # fclean + all
```

To use the library in another project:

```c
#include "libft.h"
```

```bash
cc main.c -Ipath/to/libft -Lpath/to/libft -lft -o program
```

---

## 🧠 Design Notes

`libft` is where several recurring 42 constraints first show up, and they shape how every function is written.

> [!NOTE]
> **No `for` loops.** The Norm only allows `while`, which changes how iteration and index bookkeeping are structured throughout the library.

> [!IMPORTANT]
> **One `malloc` per resource, one matching `free`.** Any function that allocates (`ft_strdup`, `ft_split`, `ft_substr`, `ft_lstnew`, ...) must also handle the failure path (`malloc` returning `NULL`) without leaking whatever it already allocated — this is where `ft_split_free` and the list's destructor callbacks come from.

> [!TIP]
> **Overlap safety in `ft_memmove`.** `ft_memcpy` assumes non-overlapping regions (as the standard allows); `ft_memmove` has to detect overlap and copy in the right direction to stay correct — reasoning about memory layout rather than just "copying bytes."

> [!NOTE]
> **BSD-style return values.** `ft_strlcpy`/`ft_strlcat` return the length they *would have needed*, not the length they wrote — mirroring the real `strlcpy`/`strlcat` contract so truncation can be detected by the caller.

---

## 🏗️ Library Structure

| File               | Role                                                                    |
| ------------------ | ------------------------------------------------------------------------ |
| [`libft.h`](libft.h)     | Public header — prototypes, includes, and the `t_list` struct            |
| [`Makefile`](Makefile)   | Build rules (`all`, `bonus`, `clean`, `fclean`, `re`)                     |
| `ft_*.c`            | One function per file, as required by the Norm                           |
| [`GNL/`](GNL)            | `get_next_line`, developed as a separate 42 project and folded in here   |

<details>
<summary>📂 Full project tree</summary>

```text
LIBFT/
├── libft.h                    # Public header
├── Makefile                   # all / bonus / clean / fclean / re
├── ft_*.c                     # 60 files, one function each — see Function Reference below
├── GNL/
│   ├── get_next_line.c
│   ├── get_next_line_bonus.c
│   └── get_next_line_utils.c
└── .assets/                   # Subject PDF, icon, grade screenshot
```

</details>

---

## 📚 Function Reference

<details>
<summary><b>🔍 Character classification</b></summary>

| Function | Purpose |
|:---|:---|
| [`ft_isalpha`](ft_isalpha.c) | Alphabetic character check |
| [`ft_isdigit`](ft_isdigit.c) | Decimal digit check |
| [`ft_isalnum`](ft_isalnum.c) | Alphanumeric check |
| [`ft_isascii`](ft_isascii.c) | 7-bit ASCII range check |
| [`ft_isprint`](ft_isprint.c) | Printable character check |
| [`ft_isnumber`](ft_isnumber.c) | Whether a whole string represents a number |

</details>

<details>
<summary><b>📝 String manipulation</b></summary>

| Function | Purpose |
|:---|:---|
| [`ft_strlen`](ft_strlen.c) | String length |
| [`ft_strlcpy`](ft_strlcpy.c) | Bounded copy, BSD-style return value |
| [`ft_strlcat`](ft_strlcat.c) | Bounded concatenation, BSD-style return value |
| [`ft_strchr`](ft_strchr.c) / [`ft_strrchr`](ft_strrchr.c) | First / last occurrence of a character |
| [`ft_strncmp`](ft_strncmp.c) / [`ft_strcmp`](ft_strcmp.c) | Lexicographic comparison |
| [`ft_strnstr`](ft_strnstr.c) | Bounded substring search |
| [`ft_strdup`](ft_strdup.c) | Heap-allocated duplicate |
| [`ft_substr`](ft_substr.c) | Extract a substring into a new allocation |
| [`ft_strjoin`](ft_strjoin.c) | Concatenate two strings into a new allocation |
| [`ft_strtrim`](ft_strtrim.c) | Trim a set of characters from both ends |
| [`ft_split`](ft_split.c) | Split a string on a delimiter into a `NULL`-terminated array |
| [`ft_strmapi`](ft_strmapi.c) | Apply a function to each character, index-aware, into a new string |
| [`ft_striteri`](ft_striteri.c) | Apply a function to each character in place, index-aware |

</details>

<details>
<summary><b>💾 Memory management</b></summary>

| Function | Purpose |
|:---|:---|
| [`ft_memset`](ft_memset.c) | Fill a memory area with a byte value |
| [`ft_bzero`](ft_bzero.c) | Zero a memory area |
| [`ft_memcpy`](ft_memcpy.c) | Copy a non-overlapping memory area |
| [`ft_memmove`](ft_memmove.c) | Copy a memory area, safe for overlap |
| [`ft_memchr`](ft_memchr.c) | Find a byte in a memory area |
| [`ft_memcmp`](ft_memcmp.c) | Compare two memory areas |
| [`ft_calloc`](ft_calloc.c) | Allocate zero-initialized memory |

</details>

<details>
<summary><b>🔄 Conversions</b></summary>

| Function | Purpose |
|:---|:---|
| [`ft_atoi`](ft_atoi.c) | String to `int` |
| [`ft_atol`](ft_atol.c) | String to `long` |
| [`ft_itoa`](ft_itoa.c) | `int` to a heap-allocated string |
| [`ft_tolower`](ft_tolower.c) / [`ft_toupper`](ft_toupper.c) | Case conversion |

</details>

<details>
<summary><b>📤 Output (file-descriptor based)</b></summary>

| Function | Purpose |
|:---|:---|
| [`ft_putchar_fd`](ft_putchar_fd.c) | Write a character |
| [`ft_putstr_fd`](ft_putstr_fd.c) | Write a string |
| [`ft_putendl_fd`](ft_putendl_fd.c) | Write a string followed by `\n` |
| [`ft_putnbr_fd`](ft_putnbr_fd.c) | Write an `int` in base 10 |
| `ft_putchar` / `ft_putstr` / `ft_putnbr` | Convenience wrappers writing to `stdout` |
| [`ft_putunbr`](ft_putunbr.c) | Write an `unsigned int` |
| [`ft_puthex`](ft_puthex.c) | Write a value in hexadecimal |
| [`ft_putptr`](ft_putptr.c) | Write a pointer address |

</details>

<details>
<summary><b>🔗 Linked list (bonus)</b></summary>

| Function | Purpose |
|:---|:---|
| [`ft_lstnew`](ft_lstnew_bonus.c) | Allocate a new node |
| [`ft_lstadd_front`](ft_lstadd_front_bonus.c) / [`ft_lstadd_back`](ft_lstadd_back_bonus.c) | Insert at the head / tail |
| [`ft_lstsize`](ft_lstsize_bonus.c) | Count nodes |
| [`ft_lstlast`](ft_lstlast_bonus.c) | Return the last node |
| [`ft_lstdelone`](ft_lstdelone_bonus.c) | Free one node with a custom destructor |
| [`ft_lstclear`](ft_lstclear_bonus.c) | Free an entire list |
| [`ft_lstiter`](ft_lstiter_bonus.c) | Apply a function to every node |
| [`ft_lstmap`](ft_lstmap_bonus.c) | Build a new list by mapping a function over an existing one |

</details>

<details>
<summary><b>✨ Extras (beyond the subject)</b></summary>

| Function | Purpose |
|:---|:---|
| [`ft_swap`](ft_swap.c) | Swap two integers |
| [`ft_split_free`](ft_split_free.c) | Free a `NULL`-terminated array produced by `ft_split` |
| [`get_next_line`](GNL/get_next_line.c) | Read a file descriptor line by line ([separate 42 project](../GNL)) |
| [`ft_printf`](ft_printf.c) | Minimal `printf`-style formatter, precursor to the full [`ft_printf`](../FT_PRINTF) project |

</details>

---

## 🎓 Skills Developed

| Learning Outcome | Piscine Skill Area |
|---|---|
| C fundamentals: pointers, arrays, string handling | Algorithms & AI |
| Manual memory management and leak-free allocation patterns | Imperative Programming |
| Reimplementing standard-library contracts precisely | Unix |
| Working under a strict style checker (Norm) and a zero-warning build | Rigor |

---

## 🏁 Result

<div align="center">
  <img src=".assets/Note.png" alt="libft grade" width="220px" />
  <br/>
  <sup><i>Validated on October 11, 2024 — ~70 hours</i></sup>
</div>

---

<div align="center">

<sub>42 Lausanne — Common Core</sub>

</div>
