<h1 align='center'>GOSIP</h1>
<h4 align='center'>Binary Exploitation (UNSOLVED)</h4>

### - Deskripsi

Author     : grb<br>
Point      : 500<br>
Attachment : chall.zip (libc.so.6)(ld-linux-x86-64.so.2)(chall)<br>

*gosip's little sibling went home crying — gosip rolled into town instead. it
listens to everything: your name, your secrets, even your last words.*

### - Analisis
let's check for the protection of the `chall` file.<br>

<img width="626" height="191" alt="image" src="https://github.com/user-attachments/assets/9037cb0d-2606-4ee2-a491-e0d0b4f1b0a4" />

As we can see, the `chall` file has all the protection, mean we can't execute our code via stack, GOT Overwrite or buffer overflow.<br>
After this, let's check all function inside `chall` file.<br>

<img width="602" height="492" alt="image" src="https://github.com/user-attachments/assets/8c55f736-177c-410d-8c82-7aaf6ea47064" />

There are bunch of function, but our primary focus on `main` function, and as we can see, there is no more interesting function, so let's inspect `main` function.<br>
Because there are bunch of instruction in `main` function, I will present the necessary one.

<img width="837" height="443" alt="image" src="https://github.com/user-attachments/assets/0c9ef161-ca0c-40c6-8eec-55d5430284e9" />

Okey, in that photo, I want us focus on the `printf()` function, what is interesting part on that function?<br>
Yes, totally right, `printf()` function only take one argument, actually there is no problem in how much printf take argument, but the problem come when `printf()` function try to display a value from variable, and there is no specifier in it, so the `printf()` function just confused, what it will display on the monitor, so to finish it tasks, it just grab anything in the stack to be displayed, no matter it is a trash or valueable thing, that's why, that's called `format string attack`.<br>
So, what advantage can we take from `format string attack`, as you know if `format string attack` will display anything from the stack to fulfill its task, so it can reveal anything or maybe....something valuable, like canaries value, since the value of canary was stored in `rbp-0x08`, so it's interesting, so how do we reach `rbp-0x08`?<br>
First thing, we should take control of `format string attack`, how so? let me show you something interesting

<img width="1595" height="292" alt="image" src="https://github.com/user-attachments/assets/d158004e-ea24-4cf6-affb-94b71bca751f" />

Yep, what is interesting thing you can see? EXACTLY..
