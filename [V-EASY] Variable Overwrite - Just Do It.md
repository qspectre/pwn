# Application

![image](https://user-images.githubusercontent.com/115867891/206281566-ba5eb80a-fbb0-4b36-9ac8-3736f737994a.png)

32 bit, no PIE => we can get static (non-changeable) addresses

# Dissasembly

We see it asking for a password which is `P@SSW0RD`.

![image](https://user-images.githubusercontent.com/115867891/206281704-3b252e6a-14e7-4754-ad17-33da2d36fb89.png)

But it just prints a message, not the flag.

We could look at the stack here:

![image](https://user-images.githubusercontent.com/115867891/206282060-a5cac5e1-278e-4fc8-8f82-809736b34a7e.png)

The `puts` call which outputs the success message could be overwritten with the non-changable address of `flag`:

![image](https://user-images.githubusercontent.com/115867891/206282243-5b1fab91-2918-44e9-9a54-604f45558d79.png)

The difference between our input at `[ebp-20h]` and success_message at `[ebp-Ch]` is 0x20-0xC = 0x14. Therefore,
we could give 0x14 * null_byte + the address of `flag` which is `0x0804A080`.

# Exploit

![image](https://user-images.githubusercontent.com/115867891/206282569-7edfbbb5-6244-45d5-b6e4-01fd0b3b5b72.png)

![image](https://user-images.githubusercontent.com/115867891/206282594-312ed242-51e9-4416-82dc-1a9312c17cdc.png)

