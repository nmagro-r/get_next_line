*This project has been created as part of the 42 curriculum by nmagro-r.*

# get_next_line

A C function that returns one line from a file descriptor each time it is called, however many bytes it has to read to get there.

```c
char	*get_next_line(int fd);
```

## Description

It returns the next line, including its newline if there is one, or `NULL` when there is nothing left to read or an error occurs. The caller has to free each line.

The difficulty is that `read` knows nothing about lines. It may stop in the middle of one, or return the end of one and the start of the next, so whatever is left over has to survive until the following call.

The bonus version keeps a separate position for each file descriptor, so several files can be read in turns.

## Instructions

The functions are meant to be added to another project, so there is no Makefile. The read size is set at compile time with `BUFFER_SIZE`, and defaults to 42.

```c
#include "get_next_line.h"

int	main(void)
{
	int		fd;
	char	*line;

	fd = open("file.txt", O_RDONLY);
	line = get_next_line(fd);
	while (line)
	{
		printf("%s", line);
		free(line);
		line = get_next_line(fd);
	}
	close(fd);
	return (0);
}
```

```bash
cc -Wall -Wextra -Werror -D BUFFER_SIZE=42 main.c get_next_line.c get_next_line_utils.c
```

For the bonus version, include `get_next_line_bonus.h` and compile `get_next_line_bonus.c` and `get_next_line_utils_bonus.c` instead.

## How it works

Everything rests on one `static` variable, which keeps its value between calls and holds the bytes already read but not yet returned.

1. **Read until there is a full line.** `make_line` keeps reading blocks of `BUFFER_SIZE` bytes and adding them to the stash until it contains a newline or the file ends.
2. **Split the stash.** `take_the_rest` cuts everything up to the newline into a new string, which is returned, and keeps the rest for the next call.

In the bonus version, the static variable becomes an array with one stash per file descriptor, for up to 1024 of them.

## Tested behaviour

| Test | Result |
|---|---|
| `BUFFER_SIZE` of 1, 42 and 10,000,000 | Same lines every time |
| Empty line in the middle of a file | Returned as a line with only a newline |
| Last line without a newline | Returned correctly |
| Bonus: two files read in turns | Each file keeps its own position |

## Resources

### References

- [read(2)](https://man7.org/linux/man-pages/man2/read.2.html)
- [Static variables on cppreference](https://en.cppreference.com/w/c/language/storage_duration)

### AI Usage

AI assistants were used for one task, always with me reviewing and testing the result:

- **Documentation:** this README, checked against the code.

The design decisions were mine, and I can explain every part of the code.
