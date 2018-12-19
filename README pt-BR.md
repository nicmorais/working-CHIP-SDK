# CHIP-SDK
Tudo que é preciso para desenvolver software para o C.H.I.P - agora funcionando.
## Instalação

### VirtualBox
1. Instale o [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
2. Instale o [Pacote de extensão para o Oracle VM (Extension Pack)](https://www.virtualbox.org/wiki/Downloads) para o hospedeiro - isso é necessário para gravar o C.H.I.P de dentro da máquina virtual. 
3. Sobre o sistema operacional:
   - Se você usar Windows, você precisa adicionar o diretório de instalação do VirtualBox para o seu PATH
   - Se você usar Ubuntu, adicione seu usuário ao grupo vboxusers

### Vagrant
Você precisará instalar o Vagrant. Aqui estão algumas opções:

* Baixe do [site do Vagrant](https://www.vagrantup.com/downloads.html)
* No Mac, você pode usar o [homebrew](http://brew.sh) package manager: 
    1. Você precisará do [Cask](http://caskroom.io), então, se você não o tem: `brew install caskroom/cask/brew-cask`
    2. depois, `brew cask install vagrant`

### Git
A instalação do Git depende do seu sistema operacional:
* No Windows, leia: https://git-scm.com/download/win
* Em linux baseado em Debian, você pode rodar: `sudo apt-get install git`
* No Mac OS, a forma mais conveniente é com o [homebrew](http://brew.sh): `brew install git`

### Clone o repositório Git do CHIP-SDK
Considerando que você tenha `git` no seu PATH, abra o terminal e digite:

    git clone https://github.com/nicmorais/working-CHIP-SDK CHIP-SDK

### Iniciando a máquina virtual:
Em um terminal no hospedeiro, mude para o diretório do CHIP-SDK e inicie a máquina virtual:

    cd CHIP-SDK
    vagrant up
Algumas notas para quem tem a visão turva: se você obter um erro como:

    error: The guest machine entered an invalid state while waiting for it to boot.

Provavelmente significa que a versão do seu VirtualBox precisa ser atualizada e/ou precisa do [Pacote de extensão (Extension Pack)](https://www.virtualbox.org/wiki/Downloads). Atualize e tente rodar `vagrant up` novamente.

Se você obter o erro: 

    error: Couldn't open file /Volumes/Satellite/gitbins/CHIP-SDK/base
    
quer dizer que vocẽ não rodou `cd CHIP-SDK`.

### Login
No mesmo terminal do hospedeiro digite o seguinte:

    vagrant ssh

Se tudo ocorrer bem você deve ver o seguinte: 

    vagrant@vagrant-ubuntu-trusty-32:~$

Agora você está pronto para gravar seu C.H.I.P pelo SDK!

## Preparando seu C.H.I.P. para a gravação
Primeiro prepare o C.H.I.P. com um fio "jumper" entre o pino **FEL** e **GND**.
Aqui está um diagrama que rotula os pinos:

![Image of CHIP](https://toonormal.com/wp-content/uploads/2016/08/chip_pinouts.jpg "Layout dos inos do CHIP")

E aqui está uma foto do C.H.I.P. com o fio ligado no FEL e no GND:

![Image of CHIP](https://i.imgur.com/qyufcbz.jpg "Imagem do CHIP no modo FEL")

É válido notar que este jumper deve estar presente somente quando o C.H.I.P. é ligado na energia. Se por alguma razão o fio é desconectado após você tê-lo ligado, não há nenhum problema ou necessidade de entrar em pânico.

Agora conecte o C.H.I.P. no seu computador com um cabo micro-USB. Os dois LEDs vão ligar.

É hora de começar! Abra o terminal no seu computador, e vamos iniciar uma máquina virtual.

    cd CHIP-SDK
    vagrant up
    vagrant ssh

Se tudo ocorrer bem, você deverá ver o seguinte:

    vagrant@vagrant-ubuntu-trusty-32:~$
   
Agora estamos prontos para gravar o C.H.I.P.

## Ajuda
Se você está familiarizado com a antiga versão dos scripts, essas opções devem soar bastante familiares para você, mas algumas das opções foram movidas. Então nós adicionamos uma seção de ajuda no script de gravação:

    vagrant@vagrant-ubuntu-trusty-32:~$ ./chip-update-firmware.sh -h
    
    == Help ==

      -s  --  Server             [Debian + Headless]        
      -g  --  GUI                [Debian + XFCE]            
      -p  --  PocketCHIP         [CHIP on the go!]          
      -b  --  Buildroot          [Tiny, but powerful]       
      -f  --  Force clean        [re-download if applicable]
      -n  --  No limit           [enable greater power draw]
      -r  --  Reset              [reset device after flash] 
      -B  --  Branch             [eg. -B testing]           
      -N  --  Build#             [eg. -N 150]               
      -F  --  Format             [eg. -F Toshiba_4G_MLC]    
      -L  --  Local              [eg. -L ../img/buildroot/] 

## Gravando um novo C.H.I.P. com a imagem sem interface gráfica (headless)
Se você quer gravar o C.H.I.P. com uma imagem customizada, vá para o final da página... Se você estiver satisfeito com a versão atual sem interface gráfica, continue lendo!

Com a máquina virtual rodando, vamos iniciar no nosso terminal *trusty*

    vagrant@vagrant-ubuntu-trusty-32:~$

Agora vamos baixar o mais último firmware (ex. um kernel Linux, U-Boot e um sistema de arquivos raíz, tudo construído com buildroot) e gravar isso no C.H.I.P.

    cd ~/CHIP-tools
    ./chip-update-firmware.sh

Isso pode demorar um pouco - por favor seja paciente.

Se tudo ocorrer bem, você pode agora ligar o seu CHIP e conectar digitando:

    screen /dev/ttyACM0 115200

Você agora pode fazer login no CHIP como **chip** ou se sente-se mais poderoso como **root**. Em ambos os casos a senha é **chip**. Agora vamos fazer um teste de hardware...

    hwtest

Se tudo tiver passado, seu CHIP está pronto para rodar! Divirta-se!

## Gravando um C.H.I.P. com Debian + Interface gráfica

Em complemento às instruções acima, para gravar uma imagem do Debian com a interface oficial do C.H.I.P., vocẽ terá de inserir uma outra opção no comando. A opção `-g` vai gravar o Debian com interface gráfica

    ./chip-update-firmware.sh -g


## Gravando o C.H.I.P. com Buildroot

    ./chip-update-firmware.sh -b
Você pode fazer login como **chip** ou se sente-se mais poderoso como **root**. Em ambos os casos a senha é **chip**.

## Para gravar o C.H.I.P. com sua imagem customizada...

#### Inicie o processo de construção

Feito o login na máquina virtual, começando no terminal *trusty*:

    vagrant@vagrant-ubuntu-trusty-32:~$

Vamos entrar lá e fazer alguma coisa.

    cd ~/CHIP-buildroot
    make chip_defconfig
    make nconfig

Daqui em diante, você pode navegar no menu e selecionar o que vocẽ quer gravar no seu C.H.I.P. e o que vocẽ não quer. Detalhar a construção de imagens customizadas está fora do escopo desse tutorial. Se você estiver curioso, leia a maravilhosa [documentação da Free Electrons](http://buildroot.uclibc.org/docs.html) sobre buildroot.

Quando você tiver terminado sua seleção, saia pressionando a tecla F9, que vai automaticamente salvar seu buildroot customizado em ...  

    /home/vagrant/CHIP-buildroot/.config
   
**NOTA:** *Você pode salvar uma build alternativa ao apertar a tecla F6, mas somente a imagem salva na pasta especificada acima será gravada no CHIP*

Agora vamos construir nosso buildroot

    make

Isso vai demorar um pouco. Dependendo do seu computador, talvez uma hora. Melhor ir tomar um café...

#### Gravando sua própria imagem buildroot


Com a máquina virtual rodando, vamos iniciar no nosso terminal *trusty*

    vagrant@vagrant-ubuntu-trusty-32:~$

digite... 

    cd ~/CHIP-tools
    sudo ./chip-create-nand-images.sh ../CHIP-buildroot/output/build/uboot-nextthing_2016.01_next ../CHIP-buildroot/output/images/rootfs.tar my-new-images
    sudo chown -R vagrant:vagrant my-new-images
    ./chip-flash-nand-images.sh my-new-images

Ok, tem um monte de informações aí, mas o que está acontecendo?

chip-create-nand-images.sh sabe não só onde encontrar os binários do uboot como também o rootfs. De lá, ele formata esses binários e cria imagens apropriadas para os diferentes tipos de memória NAND do CHIP e do CHIP Pro. Esse script deve ser rodado como root para manter certas permissões no rootfs. Após isso, nós podemos rodar o chown no diretório onde as imagens estão, e então gravá-las com chip-flash-nand-images.sh.

## Desligar

Para sair da sua máquina virtual a qualquer momento, digite:

    exit
   
A máquina virtual vai continuar rodando. Para desligá-la, digite:

    vagrant halt

## Resolução de problemas

Caso você tenha problemas por que o kernel na máquina virtual foi atualizado e a pasta compartilhada vagrant não puder mais ser montada, atualize os complementos do  convidado digitando o seguinte no diretório CHIP-SDK no hospedeiro:

    vagrant plugin install vagrant-vbguest

Também dê uma olhada [nesse texto](http://kvz.io/blog/2013/01/16/vagrant-tip-keep-virtualbox-guest-additions-in-sync/)

Caso você obtenha o erro

    `ERROR: You don't have permission to access Allwinner USB FEL device`
    
Você precisará rodar `./chip-update-firmware.sh` como `sudo`:

    sudo ./chip-update-firmware.sh

## Atualizando o CHIP-SDK

Se você já o tem instalado e quer atualizá-lo, siga esses passos:

No hospedeiro, baixe as últimas mudanças desse repositório Git.
Isso pode ser feito mudando para o diretório do CHIP-SDK e rodando git pull:

    cd ~/CHIP-SDK
    git pull

Certifique de que a máquina virtual está desligada e reaprovisionada:

    vagrant halt
    vagrant provision
    vagrant up

Isso deve resolver - entre por ssh na máquina virtual:

    vagrant ssh

Quando você estiver no terminal trusty, você já pode começar a desenvolver!

    vagrant@vagrant-ubuntu-trusty-32:~$
