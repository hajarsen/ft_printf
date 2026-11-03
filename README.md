*This project has been created as part of the 42 curriculum by hsennane.*

# ft_printf

## Description

**ft_printf** is a C project that recreates a subset of the standard `printf()` function. It introduces **variadic functions**, **formatted output**, **base conversion**, and **static libraries**.

The goal is to take a format string, process supported conversion specifiers, and print the corresponding values to standard output.

## Instructions

### Building the Project

You need a C compiler, `make`, and a Unix-like environment with `write()`.

```bash
git clone https://github.com/hajarsen/printf.git
cd printf
make
```

The Makefile produces **`libftprintf.a`**, a static library compiled with `-Wall -Wextra -Werror`.

### Using the Library

Create a `main.c` file:

```c
#include "ft_printf.h"

int main(void)
{
    int number = 42;

    ft_printf("Hello, %s!\n", "world");
    ft_printf("Character: %c\n", 'A');
    ft_printf("Signed: %d\n", -42);
    ft_printf("Unsigned: %u\n", 42u);
    ft_printf("Hex: %x / %X\n", 42u, 42u);
    ft_printf("Pointer: %p\n", (void *)&number);
    return (0);
}
```

Compile and run:

```bash
cc -Wall -Wextra -Werror main.c libftprintf.a -o demo
./demo
```

Sample output (pointer addresses vary):

```text
Hello, world!
Character: A
Signed: -42
Unsigned: 42
Hex: 2a / 2A
Pointer: 0x...
```

### Makefile Commands

| Command | Action |
|---|---|
| `make` / `make all` | Compile the library |
| `make clean` | Remove object files |
| `make fclean` | Remove objects and the library |
| `make re` | Rebuild from scratch |

### Supported Conversions

| Specifier | Meaning | Example |
|---|---|---|
| `%c` | Character | `A` |
| `%s` | String | `Hello` |
| `%d` / `%i` | Signed decimal integer | `-42` |
| `%u` | Unsigned decimal integer | `42` |
| `%x` | Lowercase hexadecimal | `2a` |
| `%X` | Uppercase hexadecimal | `2A` |
| `%p` | Pointer address | `0x...` |

### Project Files

```text
printf/
├── ft_printf.c          # Parse the format and dispatch conversions
├── ft_printf.h          # Function declarations
├── ft_print_char.c      # Characters
├── ft_print_str.c       # Strings
├── ft_print_int.c       # Signed integers
├── ft_print_unsigned.c  # Unsigned integers
├── ft_print_hexa.c      # Hexadecimal numbers
├── ft_print_pointer.c   # Pointer addresses
├── Makefile
└── README.md
```

### Evaluation Notes

- This is a **subset** of the standard C `printf`, not a complete replacement.
- The current implementation prints ordinary characters but does **not count them correctly** in the return value.
- The `%%` case currently reads a variadic argument incorrectly; do not rely on percent escaping until it is fixed.
- Widths, precision, flags, and length modifiers are not implemented.
- This repository does not contain an automated test suite.

## Resources

The main ideas behind this project are explained below with simplified diagrams and examples.

---

## 🧩 Variadic Functions

Unlike a function with a fixed number of arguments, a **variadic function** can receive additional values after its named parameters.

`ft_printf` uses `<stdarg.h>`:

| Macro / type | Purpose |
|---|---|
| `va_list` | Stores the argument-reading state |
| `va_start` | Initializes that state |
| `va_arg` | Reads the next argument as a specified type |
| `va_end` | Finishes processing the list |

### How the Format String is Read

```text
ft_printf("Name: %s, age: %d", "Sam", 21)
          │         │              │     │
          │         │              │     └─ value for %d
          │         │              └─────── value for %s
          │         └────────────────────── format specifiers
          └──────────────────────────────── format string

         ┌─────────────────┐
         │ Scan character  │
         └────────┬────────┘
                  │
          Is it a '%'?
             /         \
           no           yes
           │             │
    print literal   read specifier
           │             │
           │       select helper
           │             │
           └──────┬──────┘
                  ▼
          next character
```

**Important:** Reading arguments with the wrong type (or reading one when none exists) can cause undefined behavior. This is why correctly implementing `%%` matters.

---

## 🔢 Number Bases

Decimal and hexadecimal are different ways of representing the same numeric value.

```text
Decimal 42
    │
    ├── Base 10  → 42
    │
    └── Base 16  → 2a  (lowercase)
                   2A  (uppercase)

42 ÷ 16 = 2, remainder 10 → 'a'
 2 ÷ 16 = 0, remainder  2 → '2'
Read remainders backward: 2a
```

### Recursive Digit Printing

The number-printing helpers work on the higher digits first:

```text
print(123)
   └── print(12)
         └── print(1) → '1'
         └──              '2'
   └──                    '3'

Result: 123
```

### Signed and Unsigned Integers

- A signed decimal integer may be negative, so `%d` and `%i` need sign handling.
- An unsigned integer is displayed without a negative sign.
- Pointer representation uses a hexadecimal address with a `0x` prefix for non-null pointers in this implementation.

---

## 🛠️ Static Libraries

The Makefile compiles each `.c` file into an object file and combines the objects into a `.a` archive.

```text
ft_printf.c ──────────► ft_printf.o ───────┐
ft_print_int.c ───────► ft_print_int.o ────┤
other ft_print_*.c ───► other .o files ────┤
                                          ▼
                                   libftprintf.a
                                          │
                                      linked with
                                          │
                                        main.c
                                          ▼
                                      executable
```

This lets other C programs reuse `ft_printf` without copying the implementation into every project.

---

## 🎯 Testing Tips

Try comparing visible output with the standard `printf` for **supported** conversions, then check edge cases such as zero, negative numbers, empty strings, and null pointers.

For return-value tests, keep the known literal-character counting issue in mind. Do not assume identical behavior to the standard library until that issue and percent escaping are fixed.

### Useful Links

- [C variadic arguments — cppreference](https://en.cppreference.com/w/c/variadic)
- [printf — cppreference](https://en.cppreference.com/w/c/io/fprintf)
- [GNU Make manual](https://www.gnu.org/software/make/manual/)

### AI Usage

AI assistance was used to **organize the README, clarify explanations, and prepare illustrative diagrams**. No source-code changes were made as part of this documentation update.
