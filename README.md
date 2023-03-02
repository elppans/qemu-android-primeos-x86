# qemu-android-primeos-x86 no Archlinux

### Versões do [PrimeOS](https://www.primeos.in/)

* Classic = Computadores antigos usa kernel x86
* Standard = Computadores moderados, usa Kernel x86_64
* Mainline = Computadores parrudos, usa Kernel x86_64

> Em meus testes, a versão Mainline não funciona, talvez por conta do meu computador

### PrimeOS testados:

#### Classic (x86):  

> Todas as versões, o Google Play loga normalmente

[0.5.5](https://sourceforge.net/projects/primeos/files/Classic/primeOS-classic_0.5.5-20211112.iso)  
 Usa Android 7.1, sem lag. Na tela de login, aparece mensagem sobre Software quebrado, mas dá pra usar normalmente.  

[0.6.0](https://sourceforge.net/projects/primeos/files/Classic/primeOS-classic_0.6.0-20211123.iso)  e [0.6.1](https://sourceforge.net/projects/primeos/files/Classic/primeOS-classic_0.6.1-20211206.iso)  
Usa Android 7.1, sem lag, sem erro na tela de login.  
Após login no GPlay pode aparecer uma mensagem dizendo que algo deu errado, mas é só prosseguir que tá OK.  

#### Standard (x86_64):  

> TODAS - Sem lag, mas o Google Play fica travado na tela de "Verificando Dados"

 > Após logar o GPlay através do Classic e atualizar para Standard (substituindo os arqivos system.sfs, initrd.img e kernel), o GPlay funciona, 
 porém, fica saindo a todo instante impossibilitando o uso.  

#### PrimeOS com Android 11

> Para usar um sistema com base no Android 11, deve criar um arquivo com o nome **ramdisk.img**, senão não funciona:

```
truncate -s 2048K ramdisk.img
```

As seguintes versões do PrimeOS ficam presos em um boot infinito, antes do logo aparecer, portanto não faz o boot.  
Talvez a base deles seria o Mainline

[2.1.0](https://sourceforge.net/projects/primeos/files/64-bit/PrimeOS-2.1.0-64-bit-20220223-BETA.iso)  
[2.1.2](https://sourceforge.net/projects/primeos/files/64-bit/PrimeOS-2.1.2-64-bit-20220531-BETA.iso)  
[2.1.3](https://sourceforge.net/projects/primeos/files/64-bit/PrimeOS-2.1.3-64-bit-20220719-BETA.iso)  

A seguinte versão faz o boot normalmente e com estabilidade. Tudo funciona normalmente.  
Talvez a base dele seja como o Standard  

[2.0.1](https://sourceforge.net/projects/primeos/files/64-bit/PrimeOS-2.0.1-64-bit-20220102-BETA.iso)

## Configurando o PrimeOS 2.0.1 para ser usado no QEMU, no Archlinux

Para funcionar, deve estar instalado o pacote [qemu-android-cm-x86 (AUR)](https://aur.archlinux.org/packages/qemu-android-cm-x86).  
Será baixado o arquivo ISO do site oficial e será descompactado em uma pasta específica. Para descompactar um arquivo ISO sem ter que montá-lo, deve ter o pacote [p7zip](https://archlinux.org/packages/extra/x86_64/p7zip/) instalado.  
O Script e o arquivo **config** estão configurados para usar a pasta `~/.config/android-x86/PrimeOS`, então deverá criar esta pasta antes de tudo.  
O arquivo **config** está configurado para usar 3 GB de RAM na Máquina Virtual, para modificar, edite e vá até a variável `RAM`  
Assim como toda **ISO Android OS versão 11** o **PrimeOS 2.0.1** NÃO tem o arquivo `ramdisk.img`, então também deve criar, ou o boot não funcionará.  
Neste mesmo arquivo, também está ativo a variável `DATASIZE` para para que o Script crie um arquivo com o nome `data.img` com um tamanho de 64,4 GB. Nesta variável, antes de executar o Script pela 1ª vez, se quiser pode modificar o tamanho do arquivo a ser usado adicionando um outro número (em MB) para usar um arquivo maior ou menor.  
Um exemplo, se quer um arquivo menor, como 20 GB, deve fazer um calculo (1024 \* 20). Então a variável deve ser modificada para ficar desta forma:  

> DATASIZE=20480  

Se não quiser que seu Android use um arquivo **data.img**, crie uma pasta com o nome `data` e descomente a variável:  

> DATA="$HOME/.config/android-x86/PrimeOS/data  

Faça estes comandos para criar o necessário para executar o PrimeOS no QEMU:

```
mkdir -p ~/.config/android-x86/PrimeOS
cd ~/Downloads
wget -c https://sourceforge.net/projects/primeos/files/64-bit/PrimeOS-2.0.1-64-bit-20220102-BETA.iso
7z x PrimeOS-2.0.1-64-bit-20220102-BETA.iso -o$HOME/.config/android-x86/PrimeOS initrd.img kernel system.sfs
cd ~/.config/android-x86/PrimeOS
wget -c https://raw.githubusercontent.com/elppans/qemu-android-primeos-x86/main/config
wget -c https://raw.githubusercontent.com/elppans/qemu-android-primeos-x86/main/qemu-android-primeos
chmod +x qemu-android-primeos
truncate -s 2048K ramdisk.img
```

Para testar, faça o comando:

```
$HOME/.config/android-x86/PrimeOS/qemu-android-primeos gui
```

Se quiser criar um atalho **.desktop** para o comando, crie um arquivo em `~/.local/share/applications` com o nome `qemu-android-primeos.desktop` com o seguinte conteúdo:  

```
[Desktop Entry]
Name=qemu-android-primeos
Comment=Android-x86 environment via QEMU and VirGL
Exec=$HOME/.config/android-x86/PrimeOS/qemu-android-primeos gui
Terminal=false
Icon=qemu-android.png
Type=Application
Categories=Development;
```
