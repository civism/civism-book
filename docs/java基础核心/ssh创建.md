# ssh生成
```
cd ~
    mkdir .ssh
    chmod 700 .ssh
产生公钥，秘钥
    ssh-keygen -t rsa
    一路回车就可以了
   cd .ssh
   cp id_rsa.pub authorized_keys
   chmod 600 authorized_keys
然后把你的.pub文件内容写到authorized_keys 这个文件里
```
