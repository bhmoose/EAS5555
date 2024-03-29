(1) cd to the directory that contains your DATA, wpsv4.1, and wrfv4.1 directories.\
(2) run `vim .bash_profile`\
(3) edit the blank file that opens and add the following lines:\
`if [ -f ~/.bashrc ]; then\
        `. ~/.bashrc`\
 `fi`\

PATH=$PATH:.
export PATH`
