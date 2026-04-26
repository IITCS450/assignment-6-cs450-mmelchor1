# CS450 - Assignment 6: Symbolic Links (xv6)

## Overview
This assignment adds support for symbolic links to the xv6 operating system.Key features include the implementation of a new system call, `symlink`, and modifications to `sys_open` to enable recursive path resolution with cycle detection.

## Implementation Details

### 1. Inode Modifications
* **Added Inode Type**: Defined `#define T_SYMLINK 4` in `fs.h` and `stat.h` to represent symbolic links
* **Storage**: Symbolic link inodes store the target path as a string within their data blocks

### 2. System Call: `symlink`
* **Registration**: Assigned `SYS_symlink` to syscall number 22 in `syscall.h`
* **Implementation**: Added `sys_symlink` to `sysfile.c`, which uses `create()` to generate a `T_SYMLINK` inode and `writei()` to save the target path
* **Interface**: Exposed the API in `user.h` and created the system call stub in `usys.S`

### 3. Path Resolution in `sys_open`
* **Detection**: Updated `sys_open` to detect when an inode is of type `T_SYMLINK`
* **Resolution**: The kernel reads the target path using `readi()` and resolves it via `namei()`
* **Cycle Detection**: Enforced a maximum depth of 10 jumps to prevent infinite loops during resolution
* **O_NOFOLLOW**: Implemented the flag to ensure links are not followed when specified.

## Testing
Functionality was verified using the provided `testsymlink.c` program
* **Basic Resolution**: Verified that reading through a symlink returns the target's contents
* **Loop Prevention**: Confirmed that circular link structures trigger a failure once the depth limit is reached

**Results:**
```text
$ testsymlink
PASS: symlink created
PASS: read through symlink
PASS: loop detected (open failed)
testsymlink done