# Application

![image](https://user-images.githubusercontent.com/115867891/206287900-66297fb3-29a2-4103-9911-59cc41256ef1.png)

64-bit, no canary.

# Dissasembly

![image](https://user-images.githubusercontent.com/115867891/206287941-fdadf8f9-ceb8-464b-8b52-acac07e97ea1.png)

Basically we can write 64 bytes using `gets`. How is this helpful? We see the app intentionally leaking the address of a function that prints the flag,
called `easy()`

![image](https://user-images.githubusercontent.com/115867891/206288062-e2798624-9ff4-4202-a7bb-aad226382885.png)

If we look at the stack, we see the buffer for `gets` at `[rbp-40h]`, therefore there are 0x48 bytes from that buffer to the return address.
We can also see this using the following trick in `gdb`:

- run the code, and add as input a random number like `7654321`
- do `search-pattern 7654321` -> it will show the address in stack
- do `info frame` and check `rip` address
- calculate `rip - search_pattern_stack_addr`

![image](https://user-images.githubusercontent.com/115867891/206288431-f8e15c1f-89a6-4d3d-ac79-404f47d7ec6f.png)

`search-pattern 7654321` is at `0x7fffffffde00` and `rip` is at `0x7fffffffde48` => `0x48`.

# Exploit

![image](https://user-images.githubusercontent.com/115867891/206288710-dbeed49b-dd26-4c69-8620-775c1e719987.png)

![image](https://user-images.githubusercontent.com/115867891/206288806-c8e7d72e-f175-4f70-a432-91e13729560a.png)
