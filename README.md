# Istalação Driver Nvidia Optimus no Debian 10

Laptop:
Dell Vostro 5470

Verifique se teu laptop suporta a tecnologia Optimus:  
```lspci | egrep 'VGA|3D'```

**00:02.0 VGA compatible controller: Intel Corporation Haswell-ULT Integrated Graphics Controller (rev 09)  
09:00.0 3D controller: NVIDIA Corporation GK208M [GeForce GT 740M] (rev ff)**

Observe que na saída do comando acima foi listado a controladora Intel e Nvidia.

 

limpeza do sistema:  
```sudo apt remove --purge nvidia* libnvidia* bumblebee* primus* -y```

 

Adicionando o driver nouveau na blacklist:  
```sudo nano /etc/modprobe.d/blacklist.conf```

blacklist nouveau


Reinicie o sistema:  
```sudo reboot```


Preparando a sources.list no Debian 10:  
```sudo nano /etc/apt/sources.list```

```
#
deb http://deb.debian.org/debian/ buster main non-free contrib
deb-src http://deb.debian.org/debian/ buster main non-free contrib

deb http://security.debian.org/debian-security buster/updates main contrib non-free
deb-src http://security.debian.org/debian-security buster/updates main contrib non-free

deb http://deb.debian.org/debian/ buster-updates main contrib non-free
deb-src http://deb.debian.org/debian/ buster-updates main contrib non-free

deb http://deb.debian.org/debian/ buster-backports main contrib non-free
deb-src http://deb.debian.org/debian/ buster-backports main contrib non-free
#
```

Habilite suporte para arquitetura i386 (X86):  
```sudo dpkg --add-architecture i386```


Atualização dos pacotes do sistema:  
```sudo apt update && sudo apt upgrade -y```

 

Instalação do Bumblebee e o driver Nvidia:


Instalação nvidia-detect:  
```sudo apt install nvidia-detect```

Rode o comando abaixo para identificar o driver_recomendado para sua GPU Nvidia:  
```nvidia-detect```


Altere driver_recomendado pelo driver indicado pelo nvidia-detect.   
```sudo apt install```


Instalação do Bumblebee:  
```sudo apt install bumblebee-nvidia primus primus-libs:i386 mesa-utils -y```


Instalação do VirtualGL:  
```
wget https://sourceforge.net/projects/virtualgl/files/2.6.2/virtualgl_2.6.2_amd64.deb -P /tmp/
sudo dpkg -i /tmp/virtualgl_*.deb
```

Caso ocorra erros:  
```sudo apt -f install```

Crie o link simbólico do binário glxspheres64:  
```sudo ln -s /opt/VirtualGL/bin/glxspheres64 /usr/local/bin/```


Configuração:

Adicione seu usuário ao grupo "bumblebee":  
```sudo adduser $USER bumblebee```


Em seguida, reinicie o sistema:  
```sudo reboot```


TESTES:

Listar todos os pacotes que foram instalados:  
```dpkg --list | grep -i "nvidia\|primus"```


Testes na placa Intel Corporation Haswell-ULT:  
```
glxinfo | grep render
glxinfo | grep direct
glxinfo | grep OpenGL
glxgears
glxspheres64
```


Testes na placa NVIDIA Corporation GK208M [GeForce GT 740M]:
A partir de agora, para ativar o gerenciamento gráfico da Nvidia, é necessário digitar o comando "optirun":  
```
optirun glxinfo | grep render
optirun glxinfo | grep direct
optirun glxinfo | grep OpenGL
optirun -vvv glxgears
vblank_mode=0 primusrun glxgears -info
optirun glxspheres64
```

 

Gerenciamento do painel Nvidia:  
```optirun -b none nvidia-settings -c :8```
