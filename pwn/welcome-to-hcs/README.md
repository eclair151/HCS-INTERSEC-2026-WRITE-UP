<h1 align='center'>WELCOME-TO-HCS</h1>
<h4 align='center'>Binary Exploitation (SOLVED)</h4>

### - Description
Author: TSakuyaiba<br>
Point: (FORGET)<br>
<br>
<i>Just a little welcome for new welcomers ^^</i>
<br>

### - Analysis
So as usual we will check the protection of `chall` file.<br>
<br>
<img width="707" height="157" alt="image" src="https://github.com/user-attachments/assets/8897e566-d3c6-43f7-ba0c-423bea759e65" />
<br>
As we can see, there is only `NX` that's enabled, mean we can't execute our code in the stack.<br>
Let's check the function inside `chall` file.<br>
<br>
<img width="557" height="566" alt="image" src="https://github.com/user-attachments/assets/56a18791-87ea-498e-ab39-b44b40ad7cd9" />
<br>
WOW! So interesting, as we can see, there is `vuln` function also `win` function, so it's clear, our goal is to reach `win` function, let's check for `vuln` function first.<br>
<br>
<img width="766" height="603" alt="image" src="https://github.com/user-attachments/assets/f77bd88c-1572-43c3-bab3-b6557710ccaa" />
<br>
Ohh...if look closely the `vuln` function take out input via `gets()` function and as we know `gets()` function can be overflowed, so we will take the advantage of that trait to control the flow of execution.<br>
So the idea is so simple, we will take control the execution flows by changing the `RIP` register into our goal that's `win` function.<br>
Okey, because `gets()` function put our input in `rbp-0x20` mean we need `20 bytes` to reach `rbp` and `8 bytes` to change `saved RBP`.<br>
<br>

- payload (20 bytes)<br>
- saved RBP (8 bytes)<br>
- total (28 bytes before the saved RIP)<br>

<br>
Good, so let's jump to exploit.<br>

### - Exploit
Here's full the exploit.<br>
<br>
<img width="945" height="422" alt="image" src="https://github.com/user-attachments/assets/9c535545-f309-4179-aedb-1912948418d7" />
<br>
And here's the result.<br>
<br>
<img width="1072" height="392" alt="image" src="https://github.com/user-attachments/assets/3449496b-2733-4074-a2cd-77d8cf5b86d2" />
<br>
As you can see, `flag.txt not found` it happened because I run the exploit in local env. that's all, it doesn't effect the success our exploit as long as the env is same.<br>

