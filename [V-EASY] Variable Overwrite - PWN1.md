# Application

![image](https://user-images.githubusercontent.com/115867891/206247435-d25b8aa0-82e1-4bc4-a7aa-7ce45c534ca9.png)

# Dissasembly

We see a lot of checks, all in cleartext, so we can see the code wants the following 3 inputs:

- Sir Lancelot of Camelot
- To seek the Holy Grail.
- `0xDEA110C8`

Basically it wants us to overwrite the variable that makes the finaly check which will call `print_flag`.

![image](https://user-images.githubusercontent.com/115867891/206247846-d043b63a-1e87-423f-83a8-99a9ed3138e9.png)

If `cmp` sets ZF, then it will jump here.

![image](https://user-images.githubusercontent.com/115867891/206247955-417460ce-e30a-44a3-a2fa-f1f79f9ba245.png)

In the check we see `_gets` with a buffer at `[ebp-3Bh]` and is compared with a buffer at `[ebp-10h]`. Therefore we must overwrite `0x2b` bytes
to reach our desired buffer.

# Exploit

![image](https://user-images.githubusercontent.com/115867891/206248763-9f776b8b-8bb1-4cb4-ab4e-f5c2b6a3cdf0.png)

![image](https://user-images.githubusercontent.com/115867891/206248807-844f032a-6a5f-4999-b5b2-50f426a537c8.png)
