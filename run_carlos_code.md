(1) cd to the directory that contains your DATA, wpsv4.1, and wrfv4.1 directories.\
(2) run `vim .bash_profile`\
(3) edit the blank file that opens and add the following lines:\
`if [ -f ~/.bashrc ]; then\
        `. ~/.bashrc`\
 `fi`\
![image](https://github.com/bhmoose/EAS5555/assets/143351355/b33a9abc-5b62-46c9-b24a-70b630f4daea)

PATH=$PATH:.
export PATH`
