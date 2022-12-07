# Application

Running `pwn checksec boi` shows a lot of mitigations:

![image](https://user-images.githubusercontent.com/115867891/206241665-46daeac0-9702-403e-8f4e-2a70660c2e05.png)

# Dissasembly

IDA shows a vulnerable `read` call and a check to see if the high 32 bits of a 64-bit integer is `0xCAF3BAEE`. It will give us a shell if so.

![image](https://user-images.githubusercontent.com/115867891/206242041-7d4379f2-3d83-47b9-a903-5405ca799a7d.png)

The stack shows 0x10 distance between read variable `buf` and the one that should be overwritten `var_20`. This, plus 0x4 (a DWORD to reach high 32 bits)

![image](https://user-images.githubusercontent.com/115867891/206242236-38492403-fed4-4974-8017-3fb7242dbb0f.png)

This seems strange for me, because the assembly shows that `buf` is at `[rbp-30h]` and the variable that should be overwritten at `[rbp-1Ch]`, and the difference is still 0x14.

# Exploit

![image](https://user-images.githubusercontent.com/115867891/206244599-ccfbcdb6-c311-4b6d-a8d2-78154e3c2ab8.png)

![image](https://user-images.githubusercontent.com/115867891/206244729-a089d2b2-42f3-46ac-a930-7a40f14b4cb8.png)
