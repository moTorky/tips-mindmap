```
:Cut & Paste these lines to your workstation's shell to retain access:
######################################################################
cat >~/.ssh/id_sf-adm-segfault-net <<'__EOF__'
-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAAAMwAAAAtzc2gtZW
QyNTUxOQAAACBJFPbVdK92w4fHGb7vaG2c7XhlRTdZErUPW3Ag7lqVJAAAAIjxT/9z8U//
cwAAAAtzc2gtZWQyNTUxOQAAACBJFPbVdK92w4fHGb7vaG2c7XhlRTdZErUPW3Ag7lqVJA
AAAEA9D69K0YYMDbh6ZcWfmcZ1CdYk6Yryx28zc5ira9opkEkU9tV0r3bDh8cZvu9obZzt
eGVFN1kStQ9bcCDuWpUkAAAAAAECAwQF
-----END OPENSSH PRIVATE KEY-----
__EOF__
cat >>~/.ssh/config <<'__EOF__'
host glidebecause
    User root
    HostName adm.segfault.net
    IdentityFile ~/.ssh/id_sf-adm-segfault-net
    SetEnv SECRET=roMmbReULGMqEVFmRbOHmMQv
    LocalForward 5900 0:5900
__EOF__
chmod 600 ~/.ssh/config ~/.ssh/id_sf-adm-segfault-net
######################################################################
Thereafter use these commands:
--> ssh  glidebecause
--> sftp glidebecause
--> scp  glidebecause:stuff.tar.gz ~/
--> sshfs -o reconnect glidebecause:/sec ~/sec
----------------------------------------------------------------------
Token             : No See https://thc.org/segfault/token
Your workstation  : 104.28.235.125  (Cairo/Egypt)
Reverse Port      : Type curl sf/port for reverse port.
Exit CryptoStorm  : 83.143.242.45   (Finland)
Exit Mullvad      : 193.138.7.156   (Helsinki/Finland)
TOR Proxy         : 172.20.0.111:9050
Shared storage    : /everyone/GlideBecause     (encrypted)
Your storage      : /sec                       (encrypted)
Your Onion WWW    : /onion                     (encrypted)
Your Web Page     : http://2xyr7jug4b5uhndzelsf7vgrxygttutc6h5mqzpwp7y6blk6owhxliqd.onion/glidebecause/
SSH               : ssh -o "SetEnv SECRET=roMmbReULGMqEVFmRbOHmMQv" root@adm.segfault.net
SSH (TOR)         : torsocks ssh -o "SetEnv SECRET=roMmbReULGMqEVFmRbOHmMQv" root@w5wc42fbltkdxpycsurj4zwxouhb3es3t2334lyte6euewrebjx4ryid.onion
SSH (gsocket)     : gsocket -s NGExNzFhNMYm ssh -o "SetEnv SECRET=roMmbReULGMqEVFmRbOHmMQv" root@adm.segfault.gsocket
SECRET            : roMmbReULGMqEVFmRbOHmMQv <<<  WRITE THIS DOWN  <<<
```