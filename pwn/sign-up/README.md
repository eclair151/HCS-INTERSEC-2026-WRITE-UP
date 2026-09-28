<h1 align='center'>SIGN-UP</h1>
<h4 align='center'>Binary Exploitation (SOLVED)</h4>

### - Description
Author: Lylera<br>
Point: 481<br>
Attachment: chall<br>
<br>
<i>Lylera: Hey, let me sign for this Yui. This looks interesting. Yui: Take notes from lylera Lylera: HEI, GIVE ME THAT NOTESS!!! COME HERE YOU Yui: Nuh uh, i wouldn't give you this. WLEEE!!!!! Lylera: HOW DARE YOU YOU STUP- Slap_Yui GIVE ME THAT!!! Yui: Uwaa- g-g-g-gomennn... Ah here you go! ><. Don't slap me again pweasee ><.</i>
<br>

### - Analysis
As usual, we will check the protection for the `chall` file.<br>
<br>
<img width="700" height="211" alt="image" src="https://github.com/user-attachments/assets/99c09c0a-8d0a-494f-948b-e819a45d35b1" />
<br>
As we can see, only `FULL RELRO`, mean we can't overwrite the GOT.<br>
And then after we check for the protection, let's see what function in the `chall` file.<br>
<br>
<img width="597" height="430" alt="image" src="https://github.com/user-attachments/assets/73d5d186-a6c0-45f4-8d72-d114ed5953f4" />
<br>
WOW! Interesting, there is `vuln` function and `main` function, but there is no `win` function, how do we get the flag?<br>
Before we jump into conclusion and decide how do we get the flag, let's check `vuln` function, because inside that function we will know how we get the flag.<br>
<br>
<img width="891" height="348" alt="image" src="https://github.com/user-attachments/assets/30525326-d219-47ff-b294-c736f1a3d617" />
<br>
As we can see, the `vuln` function take an input from us using `gets()` function and stored in `rbp-0x20`. So we know that `gets()` function can be overflowed.<br>
Moreover, since there is no `NX` protection, which is we can put the shellcode in the stack and control saved RIP to point at the stack so it will execute our shellcode in stack instead.<br>
To do that, we need some requirements, if we wanna jump to the stack, we need stack address, but there is no method to leak stack address, so how do we jump to the stack.<br>
WAIT A SECOND! if we look closely, we will realize that `gets()` function will return our input and store it in the `RAX` registes.<br> 
<br>
<img width="1193" height="417" alt="image" src="https://github.com/user-attachments/assets/5e818bb9-7c88-40f4-8058-2540e95a2970" />
<br>
So, there is revision, we don't need jump to stack, we can jump to the `rax` since it returns our input, now we can arrange our payload.<br>
<br>
- shellcode (< 0x28)<br>
- address to jmp rax (0x08)<br>
- total (0x30)
<br>
So that's the calculation, since the system start writting our input from `rbp-0x20` and so on, so we only get `20 bytes` or `28 bytes` if we involve `saved rbp`.<br>
Hmm, but the problem is, shellcode 64-bit is at least `~44 bytes`, but maybe there is a way to get shortened shellcode, but I have my own way to solve this.<br>
Since the lenght of shellcode is too big whereas we need under 28 bytes maximum, so here's my solution, instead I put the shellcode in the buffer, I prefer put it after `saved RIP ` that has more space for our shellcode, how so?<br>
So the idea is in the end of program, `rsp` will point to `saved RIP + 8` which is that's where my shellcode lays, so we need to jump to rsp.<br>
So the requirement is we need `jmp rsp` gadget, let's check it.<br>
<br>
<img width="650" height="101" alt="image" src="https://github.com/user-attachments/assets/613ee74e-e399-402d-9eed-b4be7373bd56" />
<br>
Oh no, we don't have `jmp rsp` so how? don't worry. since `rax` return our input and there is `jmp rax` gadget, the solution is to make `jmp rsp` by ourself by wrtting the opcode of `jmp rsp` in the first two byte out input.<br>
<br>

- opcode jmp rsp (0x02)<br>
- payload (0x26)<br>
- jmp rax address (0x08)<br>
- shellcode (~)<br>

<br>
So that's good idea.<br>
<br>

### - Exploit
Base on our discussion above, here's my full exploit.<br>
<br>
<img width="1466" height="351" alt="image" src="https://github.com/user-attachments/assets/67412dd5-3de8-4216-9d36-1c3e54d5d201" />
<br>
And here's the result to prove it's worked.<br>
<br>
<img width="747" height="442" alt="image" src="https://github.com/user-attachments/assets/1107384e-9bbd-4c39-9e4c-8e77798fce48" />
<br>
DONE..We got the shell




