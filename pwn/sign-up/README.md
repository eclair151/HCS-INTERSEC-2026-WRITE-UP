<h1 align='center'>SIGN-UP</h1>
<h4 align='center'>Binary Exploitation (SOLVED)</h4>

### - Deskripsi
<br>
Author: Lylera<br>
Point: 481<br>
Attachment: chall<br>
<br>
<i>Lylera: Hey, let me sign for this Yui. This looks interesting. Yui: Take notes from lylera Lylera: HEI, GIVE ME THAT NOTESS!!! COME HERE YOU Yui: Nuh uh, i wouldn't give you this. WLEEE!!!!! Lylera: HOW DARE YOU YOU STUP- Slap_Yui GIVE ME THAT!!! Yui: Uwaa- g-g-g-gomennn... Ah here you go! ><. Don't slap me again pweasee ><.</i>
<br>

### - Analisis
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


