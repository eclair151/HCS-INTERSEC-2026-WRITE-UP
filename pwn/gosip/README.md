<h1 align='center'>GOSIP</h1>
<h4 align='center'>Binary Exploitation (UPSOLVED)</h4>

### - Deskripsi

Author     : grb<br>
Point      : 500<br>
Attachment : chall.zip (libc.so.6)(ld-linux-x86-64.so.2)(chall)<br>

*gosip's little sibling went home crying — gosip rolled into town instead. it
listens to everything: your name, your secrets, even your last words.*

### - Analisis
let's check for the protection of the `chall` file.<br>
<br>
<img width="626" height="191" alt="image" src="https://github.com/user-attachments/assets/9037cb0d-2606-4ee2-a491-e0d0b4f1b0a4" />
<br>
As we can see, the `chall` file has all the protection, mean we can't execute our code via stack, GOT Overwrite or buffer overflow.<br>
After this, let's check all function inside `chall` file.<br>
<br>
<img width="602" height="492" alt="image" src="https://github.com/user-attachments/assets/8c55f736-177c-410d-8c82-7aaf6ea47064" />
<br>
There are bunch of function, but our primary focus on `main` function, and as we can see, there is no more interesting function, so let's inspect `main` function.<br>
Because there are bunch of instruction in `main` function, I will present the necessary one.
<br>
<img width="837" height="443" alt="image" src="https://github.com/user-attachments/assets/0c9ef161-ca0c-40c6-8eec-55d5430284e9" />
<br>
Okey, in that photo, I want us focus on the `printf()` function, what is interesting part on that function?<br>
Yes, totally right, `printf()` function only take one argument, actually there is no problem in how much printf take argument, but the problem come when `printf()` function try to display a value from variable, and there is no specifier in it, so the `printf()` function just confused, what it will display on the monitor, so to finish it tasks, it just grab anything in the stack to be displayed, no matter it is a trash or valueable thing, that's why, that's called `format string attack`.<br>
So, what advantage can we take from `format string attack`, as you know if `format string attack` will display anything from the stack to fulfill its task, so it can reveal anything or maybe....something valuable, like canaries value, since the value of canary was stored in `rbp-0x08`, so it's interesting, so how do we reach `rbp-0x08`?<br>
First thing, we should take control of `format string attack`, how so? let me show you something interesting
<br>
<img width="1595" height="292" alt="image" src="https://github.com/user-attachments/assets/d158004e-ea24-4cf6-affb-94b71bca751f" />
<br>
Yep, what is interesting thing you can see? EXACTLY.., we can see there is weird hex `0x4141414141414141`, in eighth position, if we look it clearly, `0x4141414141414141` was place in the bottom of `buffer`, how so? if we look at the screenshot, `read()` was pointed to `rbp-0x80` so it will start writting from `rbp-0x80`, because in first byte we type `AAAAAAAA` so `rbp-0x80` and 7 bytes ahead will be filled by `A` letter or in hex `0x41` and in `format string attack` we found `0x4141414141414141` in eight position, mean in eight position we just revelead `rbp-0x80` until `rbp-0x78`.<br>
The interesting thing is, the next offset of `format string attack` will be `rbp-0x78` until `rbp-0x60` and so on, and it mean, at some point we can reveal `rbp-0x08` where  canaries value stored. and how we do know the offset in `format string attack`.<br>
keep in mind, `rbp-0x80` was in eight position in format string attack, so, to know what offset `rbp-0x08`, we just need to subtract `0x80` with `0x08` and the result will be divided with `8`, since one offset in `format string attack` will take `8 bytes`<br>
<br>
`(0x80 - 0x08) / 8 = 0xf` or in decimal it mean `15` so, the `rbp-0x08` lays 15 offset ahead after eighth offset, mean `15 + 8 = 23`, so offset 23 was `rbp-0x08`.<br>
let's prove it, `%23$p`<br>
<br>
<img width="632" height="190" alt="image" src="https://github.com/user-attachments/assets/2fc3e9e3-c3d0-4294-9ca1-bdfa08d3d5bb" />
<br>
PROVED, `0x3ce48b0e608b0f00` is the canary, how do I know, just see the `LSB` it was zero.<br>
<br>
**LEAK CANARIES SOLVED**<br>
<br>
So, after this, let's leak libc address, we just do same, give `%p %p %p`.<br>
<br>
<img width="815" height="186" alt="image" src="https://github.com/user-attachments/assets/172f78ea-face-4fcd-b86e-389562eb22f9" />
<br>
We got something interesting. `0x7ffff7fb1643` and `0x7ffff7ecf6b6`, let's check it out.<br>
<br>
<img width="890" height="58" alt="image" src="https://github.com/user-attachments/assets/5ae7c88e-6535-4094-bab1-5d2350762902" />
<br>
Oho, so first address was reveal `_IO_2_1_stdout_+131`, it's function from libc, we just leak it.<br>
<br>
<img width="585" height="56" alt="image" src="https://github.com/user-attachments/assets/5005fe7b-ed58-40cb-8976-9f3f8617d804" />
<br>
And also, it's `write` and it's from libc as well, we got 2 libc address, so we just need pick one to get libc base address, but I prefer `write` one.<br>
<br>
**LEAK LIBC ADDRESS SOLVED**<br>

### - Exploit
Because we already know the `libc address` and the `canaries` everything is going to be easy.<br>
<br>
<img width="1177" height="395" alt="image" src="https://github.com/user-attachments/assets/122e62f7-8719-4d5d-9477-c0b54131d388" />
<br>
<img width="1157" height="398" alt="image" src="https://github.com/user-attachments/assets/6c3587b8-5e8e-4478-b490-e4c5c6e1a9d8" />
<br>
Here we go, we will catcht the output, and now to get `system` and `/bin/sh` address is easy, now because `NX` enable, we need `ROP` to get the shell.<br>
<br>
<img width="1537" height="280" alt="image" src="https://github.com/user-attachments/assets/e7e502f5-b5ea-4813-867f-af77366f523c" />
<br>
WAIT, there is no `pop rdi, ret` and other.....Don't worry bro, we can use `gadget` from libc, that's why, in screenshot, we use `rop = ROP(gadget)`.<br>
now, let's find the offset to overwrite the `RIP`.<br>
<br>
<img width="1102" height="337" alt="image" src="https://github.com/user-attachments/assets/61b4fb3e-da88-4464-bad3-13a28d826971" />
<br>
in the last `read()` function, the `read()` function store the input in `rbp-0x40` and if you see the `edx` register, it store `0x200` mean we can do overflow in here.<br>
Because, the first input will be stored in `rbp-0x40` so we need `40 bytes` to reach the `rbp` and add `8 bytes` to overwrite the `rbp` so total payload we need is `48 byte` with the next `8 byte` is for overwritting `RIP`. Oh..Wait, if you look at the screenshoot again.<br>
there is instruction to `sub rbp-0x08, canaries` and if it's not same, it will error, so let's repair the offset, okey because `rbp-0x08` was occured a checking for canaries, so to reach `rbp-0x08` from `rbp-0x40` we need:<br>
<br>
`0x40 - 0x08 = 0x38`.<br>
<br>
yap, our padding is `0x38` and the next `8 bytes`for canaries, the next `8 bytes` for RBP, so here the full payload.<br>
<br>
$padding(0x38) + canaries(0x08) + padding(0x08) + savedRIP(0x08)$
<br>
that's it, so here's the full payload.<br>
<br>
<img width="936" height="185" alt="image" src="https://github.com/user-attachments/assets/83607e74-c83c-44cb-b222-dca8393664c7" />
<br>
And here's the result.<br>
<br>
<img width="883" height="710" alt="image" src="https://github.com/user-attachments/assets/ef59d46e-1c73-4114-a192-86ab192aab04" />
<br>
#### Done






