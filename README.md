# ft_printf

A small C implementation of `printf`, developed as part of the **42 curriculum**.

The goal of this project is to understand variadic functions, formatted output,
number conversion, and static libraries by recreating a subset of the C standard
library's `printf` functionality.

## Features

The project handles the following format specifiers:

| Specifier | Description | Example |
| --- | --- | --- |
| `%c` | Print a character | `A` |
| `%s` | Print a string | `Hello` |
| `%d` | Print a signed decimal integer | `-42` |
| `%i` | Print a signed integer | `42` |
| `%u` | Print an unsigned decimal integer | `42` |
| `%x` | Print hexadecimal in lowercase | `2a` |
| `%X` | Print hexadecimal in uppercase | `2A` |
| `%p` | Print a pointer address | `0x...` |

> **Note:** This implementation is a work in progress and is not a drop-in
> replacement for the standard `printf`. See [Known limitations](#known-limitations).

## Getting started

### Requirements

- A C compiler (the Makefile uses `cc`)
- `make`
- A Unix-like environment that provides `write()`

### Build

Clone the repository and compile the library:

```bash
git clone https://github.com/hajarsen/printf.git
cd printf
make
```

The build generates the static library **`libftprintf.a`**.

### Use it in a C program

Create a file called `main.c`:

```c
#include "ft_printf.h"

int main(void)
{
    int value = 42;

    ft_printf("Hello, %s!\n", "world");
    ft_printf("Character: %c\n", 'A');
    ft_printf("Number: %d\n", value);
    ft_printf("Unsigned: %u\n", 42u);
    ft_printf("Hexadecimal: %x / %X\n", 42u, 42u);
    ft_printf("Address: %p\n", (void *)&value);
    return (0);
}
```

Compile and run it:

```bash
cc -Wall -Wextra -Werror main.c libftprintf.a -o demo
./demo
```

Example output (the pointer address will vary):

```text
Hello, world!
Character: A
Number: 42
Unsigned: 42
Hexadecimal: 2a / 2A
Address: 0x...
```

## Makefile commands

| Command | Action |
| --- | --- |
| `make` | Build `libftprintf.a` |
| `make all` | Build the library |
| `make clean` | Remove object files |
| `make fclean` | Remove object files and the library |
| `make re` | Rebuild from scratch |

Compilation uses `-Wall -Wextra -Werror`.

## Project files

```text
printf/
├── ft_printf.c          # Format-string parsing and argument dispatch
├── ft_printf.h          # Public declarations
├── ft_print_char.c      # Characters
├── ft_print_str.c       # Strings
├── ft_print_int.c       # Signed integers
├── ft_print_unsigned.c  # Unsigned integers
├── ft_print_hexa.c      # Hexadecimal numbers
├── ft_print_pointer.c   # Pointers
├── Makefile
└── README.md
```

## How it works

`ft_printf(const char *format, ...)` uses `stdarg.h` to read the variable
arguments. The format string is scanned character by character; each recognized
specifier calls a helper function that writes the corresponding value to
standard output using `write()`.

The integer and hexadecimal helpers convert numbers digit by digit, using
recursion to print them in the correct order.

## Known limitations

- Literal characters are displayed but are **not included in the returned
  character count** by the current implementation.
- The `%%` case is not correctly handled: it currently tries to read an
  argument, so do not rely on it to print a percent sign.
- Formatting flags, minimum field width, precision, and length modifiers are
  not implemented.
- This repository does not currently include an automated test suite.

These points are useful next steps if you want to make the implementation
closer to the standard `printf`.

## Author

**hsennane** — 42 project.
