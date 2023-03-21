# LPIC1

* 60 Questões para o exame **101** e 60 questões para o exame **102**.

# Exame 101

* Tópicos 101, 102, 103 e 104

## 101 - Arquitetura de Sistema

### 101.1 - Identificar e editar configurações de hardware

* **BIOS:** Basic Input Output System, firmware da placa mãe
* **POST:** Power-On Self-Test, primeira ação
* **IRQ:** Interrupt Request
  * Sinais enviados ao CPU
  * Localizados em /proc/interrupts
  * 1 Teclado, 3 e  4 Porta Serial, 14 e 15 IDEs
* **Endereços I/O:** endereços de memória usados na comunicação entre CPU e os outros dispositivos
  * localizado em /proc/ioports
* **Endereços DMA:** canal de comunicação direta entre alguns dispositivos e a memória
  * localizado em /proc/dma
* **lspci:** lista os dispositivos conectados via PCI
  * **-s identificador:** mostra somente o dispositivo especificado
  * **-v:** mostra mais detalhes
* **lsusb:** lista os dispositivos conectados via USB
  * **-s bus:device:** mostra somente o dispositivo especificado
  * **lsusb -s bus:device -v:** mostra com detalhes o dispositivo
* **partições virtuais:** criadas dinamicamente quando o sistema está em funcionamento
  * **df:** mostra todas as partições, inclusive as virtuais
  * **/proc:** informações sobre processos e hardware
  * **/sys:** informações sobre hardware **(sysfs)**
  * **/dev:** referências aos dispositivos, feitas pelo **(udev)**
    * **dbus:** faz a comunicação entre os processos e dispositivos

* **ColdPlug:** conexão a frio **~>** precisa desligar a máquina
* **HotPlug:** conexão a quente **~>** máquina ligada
* **Armazenamento:**
  * **ATA:** 2 dispositivos por cabo
    * **master:** hda e hdc
    * **slave:** hdb e hdd

  * **SATA:** 1 dispositivo por cabo
    * sda, sdb...

  * **SCSI:** 7 dispositivos (8bis) ou 15 dispositivos (16bits) + controladora
    * sda, sdb..
    * **/proc/scsi/scsi**

* **módulos:** equivalente a drivers
  * **/lib/modules:** cada conjunto de módulos é associado a um kernel
  * **/etc/modules:** arquivo para o usuário definir módulos a serem carregados
  * **lsmod:** lista os módulos atualmente carregados e que outro módulo está usando (dependência)
    * consulta o conteúdo do **/proc/modules**

  * **rmmod modulo:** remove o módulo que segue (descarrega)
  * **insmod caminho:** carrega o módulo pelo **caminho**
    * não carrega as dependências

  * **modprobe modulo:** carrega o módulo pelo nome e também suas dependências
    * **-r:** remove o módulo que segue junto com suas dependências


### 101.2 - Início (boot) do sistema

* Para computadores com **BIOS**

1. **Bios:** localiza e executa a Master Boot Record
2. **MBR:** Executa o Bootloader
   - 512 bytes no primeiro setor do disco
3. **Bootloader:** Grub ou Lilo, executa o Kernel e o initird
   * 446 bytes da MBR
   * **initird** ou **initramfs:** **/** carregado temporariamente em **RAM**
     * primeira partição a ser montada **~>** /
4. **Kernel:** executa o /sbin/init
   * **/proc/cmdline** contém parâmetros enviados ao kernel
5. **Init:** inicia os programas do runlevel

* **dmesg:** mostra as informações do boot
  * não mostra só do boot, mas outras também relacionadas ao que foi feito após o boot

* Para computadores com **UEFI**

1. **UEFI:** lê a partição **ESP** e carrega o bootloader
   * não lê dados da **MBR**
   * **/boot/efi/ ~>** patição **FAT**
   * partições do tipo **GPT ~>** suporta acima de 2TB e até 128 partições
2. **Bootloader**
3. **Kernel**
4. **Init**

* **journalctl -b:** mostra logs do boot armazenados no systemd-journal

### 101.3 - Alternar runlevels/boot targets, desligar e reiniciar o sistema

* **reboot:** reinicia, **poweroff:** desliga

* **shutdown:** operações de desligamento e reboot

  * avisa os outros usuários

  * envia SIGTERM e depois SIGKILL para todos os processos

  * permite programar desligamento/reinicialização

  * sem argumentos desliga em 1 minuto
  * **-r:** reinicia
  * **-h:** halt, desliga o sistema mas não a máquina
  * **-c:** cancela a operação
  * **+x:** agenda a operação para após **x** minutos
  * **hora:** agenda a operação

* **wall "msg":** broadcast da **msg** para todos os usuários

#### SYSV

* gerenciador de serviços **~>** antigo init (PID 1)
  * **ps -p 1:** mostra se é systemd ou sysv

* **etc/inittab:** configuração principal
  * **id:n:initdefault:** linha que define o run level padrão
  * **caminhoserviço start:** para iniciar um serviço
* **etc/rcn:** diretório do run level **n** com os serviços
  * processos iniciados com **k** são para matar
  * processos iniciados com **s** são para iniciar
* **runlevel:** mostra o nível de execução do sistema
  * **0:** desligamento (FIXO)
  * **1**, **s**, **S:** (single) usuário único (FIXO)
  * **2:** multi usuário sem rede (varia por distro)
  * **3:** multi usuário com rede (varia por distro)
  * **4:** multi usuário definido pelo usuário (varia por distro)
  * **5:** multi usuário e gráfico (varia por distro)
  * **6:** reinicialização (FIXO)
* **init n** ou **telinit n:** muda para o run level **n**

#### SYSTEMD

* gerenciador de serviços compatível com sysv
* não usa run levels **~>** target
* **lib/sys/systemd/system:** diretório com os serviços
  * **target.default:** é o link que aponta para o equivalente ao run level padrão
* **systemctl:** usado para iniciar/encerrar, habilitar/desabilitar ou verificar status  das unidades/serviços
  * **isolate target:** equivalente a mudar de run level para o **target**
  * **get-default:** mostra o runlevel/target padrão

#### UPSTART

* gerenciador de serviços em crescimento compatível com systemd e init
* **initctl:** equivalente ao **systemctl** para upstart

#### ACPI

* gerenciamento de energia
* **acpid:** deamon (serviço em background) que coleta informações de energia, bateria, refrigeração



## 102 - Instalação do Linux e administração de Pacotes

### 102.1 - Dimensionar partições de disco

* **ponto de montagem:** diretório associado à partição
* **partição:** divisão do disco
  * **tipos:** primária, extendida e lógica
  * mínimo de 2 partições **swap** e **/**
    * **/** é a primeira partição montada pelo kernel
    * precisam ser montadas no **/**
      * informações de ponto de montagem **/etc**
      * diretórios de programas/comandos **/bin** e **/sbin**
      * diretórios dinâmicos **/dev**, **/proc** e **sys**
  * impede alguns erros de se projetarem em outras partições
  * facilita backup
  * **0x83:** Linux FileSystem
  * **0x82:** Linux Swap
  
* **Master Boot Record:** limitado a 2 TB por partição
  * limitada a 4 partições primárias **OU** 3 primárias e 1 extendida (extendida não aparece no **df**)
    * partições primárias **sda1** a **sda4**
  * partição extendida pode alocar várias partições lógicas
    * partições lógicas a partir de **sda5**
* **GUID Partition Table:** utilizada pela maioria dos sistemas **EFI**
* **Logical Volume Management**
  * facilita o redimensionamento
  * **Volume Group**
  * **Physical Volume**
  * **Logical Volume**
  * **Physical Extent**
  * **Logical Extent**
* **df:** mostra as partições e seu ponto de montagem
  * **-h:** mostra os tamanhos em KB, MB, GB
  * **-T:** mostra o tipo de FileSystem
    * identificar a partição que está montado o ESP **~>** tipo **VFAT**
    *  **df /boot/efi** tem o mesmo resultado
* **fdisk -l:** lista os dispositivos de armazenamento e partições
* **cat /proc/swaps:** mostra a swap
  * **free** e **top** também mostram a swap
* **efibootmgr:** mostra informações de boot
  * **-v:** detalha as informações

### 102.2 - Instalar o gerenciador de inicialização

* **linux:** parâmetro que indica o kernel a ser carregado
* **/boot/grub/grub.cfg:** principal arquivo de configuração do **GRUB 2**
  * o adm configura os arquivos **/etc/default/grub** e de **/etc/grud.b/**
    * **GRUB_DEFAULT:** parâmetro que indica o sistema iniciado por padrão
  * **/boot/grub/menu.lst:** principal arquivo de configuração **Grub Legacy**
* **update-grub** ou **grub-mkconfig -o /boot/grub/grub.cfg:** gera o arquivo
* **hd0,1:** primeira partição do primeiro disco
  * **hd0,0:** primeira partição do primeiro disco no **Grub Legacy**
* **update-grub --version:** mostra a versão do grub
* **dd if=/dev/sda of=backup.mbr bs=1 count=512:** faz um backup da mbr

### 102.3 - Controle de bibliotecas compartilhadas

* aproveitamento de código já escrito
* **estática:** incorporada na aplicação **~>** ocupa muito espaço em disco
* **dinâmica:** as aplicações apontam para a lib **~>** economia de espaço (compartilhamento)
* **ldd caminho/programa:** mostra as bibliotecas que o **programa** usa
* **/lib** e **/usr/lib:** diretórios que contêm a maioria das bibliotecas
* **ld.so.conf:** principal arquivo que contêm caminhos para libs adicionais
  * **ldconfig:** atualiza o **cache** baseado no **conf**
  * **ldconfig -p:** lista todas as libs carregadas no **cache**
* **export LD_LIBRARY_PATH=caminho:** adiciona lib quando o usuário não é root (temporário)

### 102.4 - Utilização do sistema de pacotes Debian

* **dpkg:** principal gerenciador de pacotes
  * **-l:** (L) lista os pacotes instalados
  * **-I:**  (i) mostra informações sobre o pacote que segue
  * **-s:** mostra o status do pacote que segue
  * **--get-selection | grep pacote:** mostra se o pacote está instalado ou não
  * **-L:** mostra os arquivos associados ao pacote que segue (instalado)
  * **--contents:** mostra os arquivos dentro do pacote que segue
  * **-i:** instala o pacote que segue
  * **-S arquivo:** (source) mostra o pacote que gerou o arquivo que segue
  * **-r:** remove o pacote que segue mantendo informações na base de dados
  * **-P** ou **--purge:** apaga por completo o pacote e suas informações
  * **-reconfigure:** configura com interação com usuário (alguns pacotes)
* **apt-get:** gerenciador de pacotes por fontes
  * **apt-cache show:** mostra informações do pacote que segue
  * **apt-cache depends:** mostra as depedências do pacote que segue
  * **/etc/apt/source.list:** lista de fontes de downloads
  * **update:** atualiza a lista de fontes
  * **upgrade:** baixa as novas versões de pacotes já instalados
    * **dist-upgrade:** baixa somente se não houver conflitos
  * **install:** instala o pacote que segue junto com suas dependências
  * **remove:** remove o pacote mantendo informações na base de dados
    * **purge:** apaga por completo o pacote e suas informações
  * **-d** ou **--download-only:** somente baixa o pacote .deb para **/var/cache/apt/archives**
* **dselect:** mostra um menu com os comandos do apt-get
* **alien:** conversor de rpm para deb
  * **-r:** converte deb para rpm
  * **-i:** instala o pacote rpm usando dpkg
* **apt:** gerenciador de pacotes que reune os principais comandos do apt-get e apt-cache
  * **search:** pesquisa nas fontes pacotes relacionados com o termo que segue
  * **show:** mostra informações do pacote que segue, mesmo que não esteja instalado

### 102.5 - Utilização do sistema de pacotes RPM e YUM

* **rpm:** gerenciador de pacotes equivalente ao dpkg
  * **-q:** (query) consulta
    * **-qR** ou **-q -requires:** mostra as dependências do pacote que segue
    * **-qa:** lista todos os pacotes
      * **-qa pacote:** mostra se o pacote está instalado
    * **-qi:** informações sobre o pacote que segue
    * **-ql:** lista o arquivos que pertencem a instalação do pacote que segue
    * **-qc:** lista os arquivos de configuração do pacote que segue
    * **-qd:** lista os arquivos de documentação do pacote que segue
    * **-qf arquivo:** mostra o pacote responsável pelo **arquivo** que segue
    * **-qlp pacote:** lista os arquivos internos ao arquivo rpm
  * **-i:** instala o pacote rpm que segue
    * **-ivh:** detalha (verbose) e mostra progresso (hash) 
    * **--nodeps:** instala sem se preocupar com as dependências
    * **--force:** sobrescreve arquivos pré existentes
    * **--test:** somente testa a instalação
  * **-U:** atualiza ou instala o pacote que segue
    * mais utilizado que o **-i**
    * mesmos argumentos que o **-i**
  * **-e**: remove o pacote que segue
    * **-evh:** detalha e mostra o progresso
    * **--test:** somente testa a desinstalação
    * **--nodeps:** ignora que o pacote a ser removido é dependência de outro
  * **--verify** ou **-V:** verifica todos os pacotes ou o pacote que segue
    * segundo campo (digest) indica que houve mudança no conteúdo **~>** md5
  * **--checksig:** verifica o md5 do pacote que segue
* **yum:** gerenciador de pacotes equivalente ao apt
  * **/etc/yum.conf:** principal arquivo de configuração
  * **/etc/yum.repos.d:** diretório de repositórios
  * **install:** instala o pacote que segue com as suas dependências
  * **update:** atualiza os pacotes
    * diferente do  apt que somente atualiza os repositórios

  * **upgrade:** atualiza os pacotes e remove os obsoletos
  * **list:** lista todos os pacotes instalados
  * **search:** pesquisa por pacotes que contêm o termo que segue
  * **remove** ou **erase:** remove o pacote que segue
  * **install --downloadonly:** somente baixa o pacote e suas dependências
    * **--downloaddir=/local/desejado:** define o local de download 

* **rpm2cpio pacote > destino:** converte o pacote rpm em cpio
  * **cpio -i --make-directories < destino:** extrai o arquivo criando os diretórios necessários

* **DNF:** baseado do yum, melhor uso de memória e resolução de dependências
  * mesmos comandos do yum

* **zypper:** equivalente ao yum para sistemas **SUSE**
  * **refresh:** atualiza somente a base de dados
  * **update:** atualiza o pacote que segue ou todos


### 102.6 - Linux virtualizado

* **VM:** Hypervisor, SO, libs e apps
  * **HVM:** virtualização completa (réplica)
  * **Paravirtualização** ou **PV:** OS modificado para virtualização
* **Container:** Container Engine, libs e apps
  * utiliza o kernel do host
  * **LXC/LXD** e **Docker**
  * **Imagem:** definição dos recursos
  * **D-BUS MACHINE ID:** identificador único da vm/container
* **cat /proc/cpuinfo | grep hypervisor:** verifica se é vm ou máquina real
  * se não tiver saída é máquina real
  * **dmesg | grep Hypervisor** tem o mesmo resultado
  * **hostnamectl status** também mostra pelo campo Icon name


## 103 - *Comandos GNU e Unix* 

* 26 das 60 questões

### 103.1 - Trabalhar na linha de comando

* **echo:** joga para a saída padrão o que segue
  * **echo $SHELL:** mostra o shell que está sendo usado
* **type:** mostra se o comando seguinte é interno ou externo ao shell
  * **type echo:** mostra que o comando echo é interno
  * **type clear:** mostra que o comando clear é externo e aponta seu caminho
* **PATH:** variável de ambiente, aponta os caminhos para o shell encontrar os comandos externos
  * **PARA EXECUTAR SCRIPT:** 
    1. Executar o caminho absoluto /home/user.../script.sh **OU**
    2. Executar o caminho relativo ao diretório atual /user..script.sh **OU**
    3. Navegar até o local e executar ./script.sh
* **pwd:** mostra o diretório atual
* **cd:** muda para o diretório que segue
  * **-:** volta ao diretório anterior
  * **..:** volta um diretório acima
  * **~:** muda para o home do usuário atual

* **Declaração de Variável Local:** NOME_VARIAVEL=valor
* **Declaração de Variável Global:** export NOME_VARIAVEL=valor
  * todos os processos filhos (iniciados a partir deste shell) têm acesso 

* **set:** mostra todas as variáveis, globais e locais
* **env:** mostra somente as variáveis globais
  * **env variavel=novovalor ./script.sh:** altera o valor da variável somente para a executação do script
* **unset:** apaga a variável que segue
* **HISTFILE:** variável que armazena o local do arquivo de histórico de comandos
* **LOGNAME:** variável que armazena o usuário que fez o login na seção atual
* **TERM:**  variável que armazena o terminal em execução. Saídas possíveis:
  * **xterm:** terminal de interface gráfica
  * **tty:** terminal que foi logado direto sem interface gráfica
* **$$:** variável que armazena o PID do shell atual
* **$!:** variável que armazena o PID do último processo que rodou em background
* **$?:** variável que armazena o código de execução do último comando. Saídas possíveis:
  * **0:** comando executou sem erro
  * **1 ou 2:** comando executou com erro
* **~:** variável que armazena a pasta home do usuário que segue
  * **echo ~root:** aponta para /root
  * **echo ~:** aponta para o home do usuário atual
* **Execução Múltipla de Comandos:** 2 ou mais comandos
  * **; :** executa todos os comandos em sequência, independete de erros
  * **&&:** executa o segundo comando somente se o primeiro não der erro (**E**)
  * **||:** executa o segundo comando somente se o primeiro der erro (**OU**)
* **history:** lista o histórico de comandos executados pelo usuário atual
  * **history -c:** limpa o histórico
  * **.bash_history:** arquivo armazenado na **/home** que armazena o histórico
* **!!:** repete o último comando executado
* **!n:** executa o comando de número **n** do history
* **CTRL+R:** pesquisa por comandos no histórico
* **man:** mostra o manual do comando que segue
  * comandos internos não tem manual, suas informações estão no manual do bash
  * **man -k "palavra":** mostra comandos que tem na descrição a palavra ou frase
  * **apropos "palavra":** mesmo efeito do comando acima
* **info:** mostra informações sobre o comando que segue
* **whatis:** mostra uma pequena descrição do comando que segue
* **uname:** mostra informações do sistema
  * **uname -a:** todas as informações
* **alias:** cria um atalho para o comando que segue, somente para a seção atual
  * **alias dg='sudo apt update && sudo apt upgrade':** cria um atalho para executar apt update e apt upgrade
* **which:** localiza o comando que segue, se contido no PATH
* **Quoting:** proteção de caracteres especiais
  * **'':** protege todos os caracteres especiais de dentro das aspas simples
  * **"":** não protege **$**, **`** e **\\**
  * **\\:** só protege o próximo caractere especial



### 103.2 - Processar fluxos de texto usando filtros

* **cat:** mostra o conteúdo do arquivo de texto que segue
  * **-n:** linhas numeradas
  * **-b:** linhas não nulas numeradas
  * **-A:** destaca caracteres especiais
* **tac:** mostra o conteúdo invertido
* **head:** mostra o cabeçalho do arquivo que segue, geralmente 10 linhas
  * **-nx ou -x:** mostra as primeiras **x** linhas
  * **-cx:** mostra os primeiros **x** bytes
* **tail:** mostra o final do arquivo que segue, geralmente 10 linhas
  * mesmos parâmetros do **head**
  * **-f:** mantém o arquivo aberto aguardando atualizações **~>** LOG
* **less:** mostra o conteúdo do arquivo que segue de forma paginada
  * **/:** pesquisa pela palavra que segue
    *  **n:** vai para a próxima incidência
    * **N:** volta na inciência anterior
  * **q:** fecha o arquivo
  * **|less:** muito usado para paginar a saída de outro comando
* **wc:** mostra a quantidade de linhas, palvras e bytes do arquivo que segue
  * **-l:** somente a quantidade de linhas
  * **-w:** somente quantidade de palavras
  * **-m:** somente a quantidade de caracteres
* **nl:** numera as linhas sem considerar as vazias, igual ao **cat -b**
* **sort:** ordena as linhas do arquivo que segue
  * **-r:** ordem reversa
  * **-kn:** ordena pelo campo de número **n**
* **uniq:** mostra o conteúdo do arquivo que segue, ignorando repetições na sequência
  * **sort arquivo | uniq:** ordena e exclui as repetições globais
  * **-d:** mostra somente o que está duplicado
  * **-c:** conta o número de repetições de cada inciência
* **od:** mostra o conteúdo do arquivo que segue em formato octal
  * **-tx:** mostra em hexadecimal
* **join:** combina arquivos através de índice, por padrão o primeiro campo
  * **join jn arquivo1 arquivo2:** considera o campo **n** como índice
* **paste:** combina arquivos linha a linha
* **split:** gera novos arquivos a partir da divisão do arquivo que segue
  * sem argumentos, divide a cada 1000 linhas
  * **-ln ou n:** divide em razão de **n** linhas
  * **-bn:** divide em razão de **n** bytes
  
* **tr:** substitui ou deleta caracteres do arquivo **~>** uso obrigatório do **|** ou **tr < arquivo**
  * **cat arquivo | tr a-z A-Z:** substitui letras minúsculas por maiúsculas
  * **-d A:** deleta todas ocorrências de **A**
  * **-s "caractere":** deleta as repetições de **caractere**
* **cut:** recorta partes do arquivo que segue
  * **-cn:** mostra apenas os **n** primeiros caracteres de cada linha
  * **-d" ":** define o delimitador de campos como **espaço**
    * **-fn,m:** mostra apenas os campos de número **n** e **m**
    * **-fn-m:** mostra apenas os campos entre **n** e **m** (intervalo)
* **sed:** substitui ou apaga linhas do arquivo que segue
  * **sed 's/a/b/' arquivo:** substitui a primeira ocorrência de **a** por **b** em cada linha
  *  **sed 's/a/b/g' arquivo:** substitui todas as ocorrências a **a** por **b**
  * **sed 'n,m d' arquivo:** apaga o intervalo de linhas entre **n** e **m**
  * **sed '/palavra/d' arquivo:** apaga todas as linhas que contém **palavra**
* **grep:** filtra o arquivo que segue
  * **grep palavra:** mostra as linhas que contém **palavra**
  * **grep -v palavra:** mostra as linhas que **não** contém **palavra**, equivalente a **sed '/palavra/d'**
* **xzcat:** descompacta e mostra o conteúdo do arquivo **xz**
* **bzcat:** descompacta e mostra o conteúdo do arquivo **bz2**
* **zcat:** descompacta e mostra o conteúdo do arquivo **gz**
* **checksum:** gera e compara hashs de arquivos
  * **md5sum**, **sha1sum**, **sha256sum** e **sha512sum**
  * rodar **sha256sum -c arquivosums** na pasta com o arquivo de interesse **E** o arquivosums



### 103.3 - Gerenciamento básico de arquivos

* **ls:** lista arquivos e diretórios 

  * **-a:** lista arquivos e diretórios ocultos
  * **-l:** lista detalhada
    * **d:** diretório **l:** link **nada:** arquivo
  * **-h:** mostra o tamanho em **k**, **m** ou **g**
  * **-R:** executa o comando recursivamente em todos os subdiretórios

  **Modificadores:**

  * ***** **:** substitui qualquer conjunto de caracteres
  * **?:** substitui um único caractere
  * **[ab]:** lista de caracteres
  * **[!ab]:** nega a lista de caracteres
  * **[n-m]:** intervalo de caracteres
  * **{aa,bb}:** sequências de caracteres

* **file:** mostra o tipo do arquivo que segue

* **cp:** copia o arquivo que segue, sobrescreve sem perguntar, atualiza hora de modificação, permissões e usuário

  * **cp arquivo destino**
    * Se o destino for um diretório, mantém o nome original
    * Para alterar o nome é só definir o nome no destino
  * **-v:** detalha as operações
  * **-i:** pergunta se deseja sobrescrever
  * **-r:** copia o diretório que segue
  * **-p:** preserva características do arquivo (hora de modificação, permissões e usuário)

* **mv:** move os arquivos

  * mesmos parâmetros do **cp**, com exceção a **-r** e **-p** (já copia os diretórios)
  * usado para renomear arquivos

* **touch:** cria ou atualiza hora de acesso/modificação do arquivo que segue

  * **-a:** atualiza a hora de acesso
  * **-m:** atualiza a hora de modificação
  * **-t anomesdiahora: **define a data de modificação

* **rm:** remove o arquivo que segue, sem confirmar

  * **-i:** confirma a remoção
  * **-r:** remove o diretório que segue

* **rmdir:** remove diretórios vazios

* **mkdir:** cria o diretório que segue

  * **-p:** cria a estrutura de diretórios que segue

* **find:** procura, no local que segue, arquivos e diretórios

  * **-name:** procura por nome
  * **-user:** procura por usuário
  * **-ctime -n:** procura por tempo de modificação menor que **n** dias
  * **-size +XG:** arquivos maiores que **X** gb (pode ser usado m ou k também)
    * **-YM:** arquivos menores que **Y** mb

* **tar:** aglomera arquivos

  * **tar arquivo.tar arquivos**
  * **c:** cria um aglomerado
  * **x:** extrai um aglomerado
  * **t:** lista os itens de um aglomerado
  * **p:** preserva as características
  * **f:** cria o arquivo com o nome que segue
  * **z:** compacta com **gzip**
  * **j:** compacta com **bzip2**
  * **x:** compacta com **xz**

* **gzip:** compacta o arquivo que segue, substitui o arquivo original pelo compactado

  * **-k:** mantém o arquivo original
  * **-d:** descompacta o arquivo que segue

* **bzip2 e xz:** outros compactadores, mesmos parâmetros do **gzip**

* **cpio:** aglomera arquivos, obrigatório **|** e **>**

  * **-o:** cria o arquivo que segue
    * **ls arquivos | cpio -o > backup.cpio**
  * para compactar
    * **ls arquivos | cpio -o | gzip > backup.cpio.gz**
  * **-i:** extrai o arquivo redirecionado
    * **cpio -i < backup.cpio**
  * **-d:** permite a criação de subdiretório na extração

* **dd:** copia partições byte a byte
  * **if=:** origem
  * **of=:** destino
    * se a origem for um diretório, copia o conteúdo
    * para criar imagem é só definir **.img** no destino

### 103.4 - Fluxos, pipes (canalização) e redirecionamentos de saída

* **STDIN:** entrada padrão (teclado), código **0**
  * **comando < arquivo:** redireciona o **arquivo** para a entrada do **comando**
  * **comando 1 | comando 2:** redireciona a saída do **comando 1** para a entrada do **comando 2**
  * **comando << nome:** redireciona interativamente a entrada que segue para o **comando**
    * Tudo que for inserido no terminal fará parte do "arquivo" de entrada do **comando**
    * o "arquivo" de entrada é finalizado quando se entra com o **nome**
  * **comando <<< arquivo:** redireciona o **arquivo** para a entrada do **comando** como se fosse uma string
    * **tr a-z A-Z <<< arquivo:** resulta literamente em **ARQUIVO**
* **STDOUT:** saída padrão (tela),  código **1**
  * **1> ou >:** redirecionamento, cria ou sobrescreve o arquivo
  * **1>> ou >>:** redirecionamento, cria ou adiciona o conteúdo no final do arquivo (**append**)
* **STDERR:**  erro padrão (tela), código **2**
  * **2>:** redirecionamento, cria ou sobrescreve o arquivo
  * **2>>:** redirecionamento, cria ou adiciona o conteúdo no final do arquivo (**append**)
  * Redirecionamento duplo, arquivos diferentes
    * **comando > saida.out 2> saida-erro.out**
  * Redirecionamento duplo, mesmo arquivo
    * **comando > saida.out 2>&1**
* Ordenar arquivo de texto e substituir original
  1. Redirecionar a saída para arquivo temporário
     * **sort arquivo > arquivo-temp**
  2. Renomear arquivo temp
     * **mv arquivo-temp arquivo**
* **tee:** redireciona saída para arquivo e mostra na tela
  * **comando | tee arquivo**
  * **comando | tee -a arquivo:** adiciona ao final do arquivo (**append**)
* **xargs:** redireciona a saída de um comando para a entrada de outro
  * **find /home -name "arquivos" | xargs ls -l**
* **\`comando\` (crase) ou $(comando):** executa um comando dentro de outro
  * **echo "A versão desse kernel é $(uname -r)"**

### 103.5 - Criar, monitorar e finalizar processos

* **PID:** Process ID, identificador de cada processo
  * cada processo tem o seu processo pai **PPID**
* **init:** sempre o primeiro processo **PID=1**
* **ps:** mostra todos os processos rodando
  * **-u:** mostra o usuário, uso de cpu e memória
  * **-x:** mostra processos iniciados em outros **tty**
  * **-a:** mostra essas informações de todos os usuários logados
  * **-f:** mostra os processos em hierarquia
  * **ps -axu | grep palavra** mostra todos os processos que tem **palavra** no nome
* **pstree:** mostra os processos em hierarquia detalhada
  * **-p:** mostra o **PID**
  * aponta para o **systemd** como processo inicial, que corresponde ao **init**
* **pgrep:** mostra os **PID** dos processos relacionados ao que segue
  * **pgrep bash:** mostra os processos de bash iniciados
  * **-u usuário:** mostra os processos do **usuário**
* **top:** tela interativa de informações sobre processos e uso de recursos
  * **load average:** média de 1min, 5 min e 15min
    * Idealmente abaixo do número de núcleos do processador
  * **SHIFT M:** classifica por uso e memória
  * **SHIFT P:** classifica por uso do processador
  * **u usuário:** mostra somente os processos do **usuário**
  * **? ou h:** mostra o menu ajuda
  * **top -b > arquivo.out:** executa o comando e joga para um arquivo
* **kill:** envia sinal para um processo
  * **-l:** lista todos os sinais possíveis (nome e número)
    * **1 SIGHUP:** termina, reinicia ou faz o processo reler suas configurações
    * **2 SIGINT:** interrompe o processo ~> **CTRL+C**
    * **3 SIGQUIT:** fecha o processo
    * **9 SIGKILL:** mata abruptamente ~> único sinal que não pode ser ignorado por um processo
    * **15 SIGTERM:** solicita ao processo que finalize
  * **PID:** mata o processo com o sinal **SIGTERM** (15)
  * **-u usuario:** envia o sinal para o processo do **usuário** (somente root)
  * **-s sinal PID ou -sinal PID:** envia o **sinal** (nome ou número) para o processo correspondente ao **PID**
  * **kill \`pgrep nomeprocesso\`: ** resolve o pid e joga no kill (não funciona com **|**)
    * **pkill nomeprocesso** tem o mesmo resultado
* **killall:** mata processos pelo nome
  * somente processos do usuário atual
  * se for o root pode matar de qualquer usuário
* **uptime:** hora atual, tempo ligado, número de usuário e load average
* **free:** mostra o usu de memória física e swap
  * **-m ou -g:** mostra em megas ou gigas
  * a memória física em **used** significa que o sistema tem aquela memória alocada disponível, não que está necessariamente usando mesmo
* **screen:** cria abas de terminal e protege os comandos de serem interrompidos por encerramento da seção
  * **CTRL+A + C:** cria uma nova aba
  * **CTRL+A + N:** vai para a próxima aba
  * **CTRL+A + D:** desacopla do screen
  * **CTRL+A + R:** acopla no screen
  * **exit:** fecha a aba
* **nohup comando:** impede que o processo pare quando o usuário desloga 
* **comando &:** executa o comando em background e libera o terminal
  * mostra o id do **job** e o **PID**
  * outra forma:
    1. Executar o comando em foreground
    2. **CTRL+Z** para parar o processo
    3. **bg** para executar o último processo em background 
* **jobs:** mostra os processos rodando em background
  * **-l:** mostra também o seu **PID**
* **bg:** executa a último processo em background
  * **n:** executa o job de número **n** em background
* **fg:** executa o último job em foreground
  * **n:** executa o job de número **n** em foreground
* **watch comando:** executa **comando** a cada 2s
  * **-nx:** altera o tempo para **x** segundos
* **tmux:** semelhante ao screen, mais completo
  * **CTRL+B** para ativar algum comando
    * **c:** cria uma nova janela
    * **,:** renomeia a janela atual
    * **w:** mostra as janelas existentes
    * **n,p:** vai para a próxima (next) ou anterior (previous)
    * **%:** cria um painel vertical e **"** cria horizontal
      * **CTRL+B setas:** navega entre os painéis
    * **d:** desconecta da seção
    * **&:** mata a seção
    * **tmux ls:** mostra as seções
    * **tmux attach -t n:** conecta na seção **n**
    * **tmux new -s nome:** cria uma nova seção **nome**

### 103.6 - Modificar a prioridade de execução de um processo

* **top:** observar o campos
  * **PR:** prioridade definida pelo sistema
  * **NI:** influência do usuário na prioridade (padrão 0)
    * **-20** (maior prioridade) até **+19** (menor prioridade)
    * root pode aumentar/diminuir prioridade de qualquer processo
    * usuários comuns só podem diminuir prioridade e somente dos seus processos
* **ps -la:** mesmo efeito
* **nice:** definir o **NI** na inicialização de um processo (padrão 10)
  * **-n x processo** ou **-x processo:** inicia o **processo** com **NI** de **x**
  * **--adjustment=x:** mesmo efeito 
  * para **NI negativo** fazer **-n -15**,  **--15** ou **--adjustment=-15**
* **renice:** redefinir o **NI** de um processo em execução
  * **-n x PID** ou **x PID**
  * **-n x -u usuario:** define para todos os processos do **usuario**
  * **-n x -g grupo:** define para todos os processos do **grupo**
  * pode ser definido dentro do comando **top** digitando **r** e então o valor do nice

### 103.7 - Procurar em arquivos de texto usando expressões regulares

* **grep:** filtro
  * **palavra arquivo:** mostra as linhas do **arquivo** que contém **palavra** (case sentive)
  * **-c:** conta as ocorrências
  * **-i:** ignora o case sensitive
  * **-r:** executa recursivamente nos subdiretórios
  * **-E:** expande os padrões de RegEx do grep
    * o comando **egrep** tem o mesmo efeito
    * o comando **fgrep** não aceita RegEx
  * **-v:** inverte a seleção, mostra somente as linhas que não atendem ao filtro

* **egrep:** filtro com expansão de RegEx

  * **"{n}"**: número de ocorrências do caractere anterior

    * **"{n,m}":** de **n** a **m** ocorrências
    * **"{,m}":** até **m** ocorrências
    * **"{n,}":** no mínimo **n** ocorrências

  * **"[]":** lista
    * **"b[aei]g" arquivo:** linhas do **arquivo** que contém **bag**, **beg** ou **big**

  * **Âncoras:**

    * **"^":** começo da linha
      * **"^#":** linhas que começam com **# ~> comentários**
      * **-v "^#":** não mostra os comentários

    * **"$":** fim da linha
      * **"palavra$":** linhas que terminam com **palavra**
      * **"^$":** linhas em branco

* **"\b":** começo da palavra (conjunto de caracteres e/ou números separados por _)
  * palavras com **-** são consideradas duas palavras separadas

  * **"*":** 0 ou qualquer repetição do caractere anterior

    * **"b[aei]g*":** linhas que contém **b[aei]qualquercoisa** e **b[aei]gg**, **b[aei]ggg**, etc (além das já mostradas sem o **\***)

  * **"+":** 1 ou mais ocorrências do caractere anterior

  * **"?":** 0 ou 1 ocorrência do caractere anterior **~>** CARACTERE OPCIONAL

  * **".":** somente 1 ocorrência do caractere anterior

  * **"\\":** protege o caractere seguinte **~>** transforma em string caracteres especiais **\***, **?**, etc

* **sed:** substituir ou apagar linhas

  * **'s/RegEx/substituto/s'** ou **'/RegEx/d'**




### 103.8 - Edição básica de arquivos com o vi

* **vi:** editor em modo texto **~>** em geral **vi** é um alias para o **vim**
  * somente **vi** abre o editor com um arquivo em branco sem nome
  * **modos:**
    * **navegação:** modo iniciado por padrão **~>** uso das setas ou **hjkl**
      * **h** &larr;	**j** &darr;	**k** &uarr;	**l** &rarr;	
    * **inserção (i):** edição na posição atual do cursor **~>** **ESC** para voltar a **navegação**
      * **o:** entra no modo edição na linha de baixo
      * **O:** entra no modo edição na linha de cima
      * **a:** entra no modo edição no caractere seguinte
      * **A:** entra no modo edição no final da linha
    * **comandos:** uso do **:**
      * **$:** posiciona o cursor no final do arquivo 
      * **w:** salva o arquivo
      * **w novonome:** para salvar o arquivo com **novonome**
      * **q:** fecha o arquivo (informa se há alterações não salvas)
      * **q!:** fecha sem salvar
      * **wq** ou **x:** salva e fecha
      * **ZZ:** tem o mesmo efeito (sem digitar **:**)
      * **!comandolinux:** executa o **comandolinux** e volta para o **vi** (após ENTER)
        * **:!ls /tmp** por exemplo
      * **e!:** atualiza o arquivo com as alterações feitas por outro usuário/processo após a abertura
  * **/:** pesquisa pela palavra que segue (de cima para baixo)
  * **n** vai para a próxima ocorrência, **N** volta para a anterior
  * **?:** pesquisa pela palavra que segue (de baixo para cima)
  * **dd:** recorta a linha
  * **dnd** ou **ndd:** recorta as próximas **n** linhas
  * **cc:** recorta a linha e entra no modo edição
  * **yy:** copia a linha
  * **yny** ou **nyy:** copia as próximas **n** linhas
  * **p:** cola a linha copiada/recortada
  
* **nano:** comandos na parte inferior da tela
  * **^:** CTRL e **M:** ALT 
  * **ALT + /:** final do arquivo
  * **ALT + \:** começo do arquivo
  * **CTRL + X:** fecha (pergunta se deseja salvar alterações)
  * **CTRL + o:** salva 
  * **CTRL + W:** pesquisa pela pavra que segue
  
* **emacs:** roda tanto em interface gráfica quanto no shell

  * **CTRL + x CTRL + s:** salva
  * **CTRL + x CTRL + c:** fecha o arquivo

  * **CTRL + k:** recorta a partir do cursor
    * deixar cursor no começo da linha para recortar a linha toda
    * armazena em buffer **~>** recortar várias linhas seguidas sem perder

  * **CTRL + y:** cola o conteúdo
  * **CTRL + /:** desfaz o último comando
  * **ALT +x:** define a marcação de sintaxe por linguagem

* **select-editor:** mostra as opções de editores para definir o padrão

* **export $EDITOR=editor:** define o **editor** como padrão 

​	

## 104 - Dispositivos, Sistemas de Arquivos e FHS

### 104.1 - Criar partições e sistemas de arquivos

* **fdisk:** partições MBR e GPT
  * **gdisk:** somente partições GPT

* **fdisk -l:** lista os discos e partições
  * campo **Disk label type: dos** indica partição MBR
* **fdisk /dev/sbd:** entra nas opções do disco **sdb**
  * **m:** mostra o menu
  * **n:** nova partição
    * escolher primária (padrão) ou extendida
    * número da partição
    * primeiro setor
    * último setor ou número de setores ou tamanho
    * **TUDO SALVO EM MEMÓRIA, NADA APLICADO**
  * **p:** lista as partições
  * **q:** sai sem salvar
  * **w:** salva e sai
  * **d:** apaga partição a ser selecionada em seguida
    * partições primárias pode apagar qualquer uma
    * partições lógicas (dentro de uma extendida) são apagadas mas têm o número alterado para ficar sempre em sequência
  * **t:** muda o tipo de partição através do **ID**
  * **g:** cria uma tabela da partição GPT
  * se alterar uma partição que contém o kernel em uso, as alterações sé serão aplicadas após reiniciar o sistema
* **filesystem:** como o sistema vai distribuir os dados na partição
  * **ext2** não tem journal, **ext3** e **ext4** têm
  * **brtfs:** fs desenvolvido pela Oracle, nova geração
  * **vfat:** fs do windows 
  * **journal:** sistema de logs que registra alterações no sistema
    * facilita a recuperação em caso de falha abrupta

* **mkfs -t tipo partição:** cria o filesystem
  * **mkfs.tipo** também pode ser usado

* **mkswap:** cria o fs de swap
  * **swapon** para ativar a swap


- **file -s partição:** mostra o fs da partição
- **parted:** semelhante ao fdisk, mas com mais opções **~>** aumentar partição
- **parted /dev/sdb:** entra nas opções do disco **sdb**
  - **resizepart número novo_final:** muda o tamanho da partição
    - o novo final não pode entrar em outra partição

#### BTRFS - B-Tree File System

* fs moderno e em constante desenvolvimento
* tolerância a falhas
* facilidade de administração e reparo
* suporte a RAID e subvolumes
* compressão transparente
* snapshot
* conversão direta de ext2, ext3 e ext4 em btrfs

#### exFAT - ExtendedFAT ou FAT64

* otimizado para armazenamentos removíveis **~>** pen drives e cartões de memória
* mais leve que NTFS
* permite arquivos maiores que 4gb
* **proprietário** (MS) **~>** implementado através de pacotes exfat-fuse e exfat-utils
* **mkfs.exfat partição**

### 104.2 - Manutenção da integridade de sistemas de arquivos

* **fsck:** checa o filesystem de uma partição não montada
* **xfs_repair:** checa o filysistem de partições xfs
* **xfs_db:** debug de partição xfs
  * **frag:** mostra fragmentação da partição
  * **xfs_fsr:** desfragmenta a partição

* **tune2fs **
  * **-l:** mostra informações da partição que segue
  * **-L nome:** define a label da partição
  * **-i xd:** define checagem automática para **x** dias na partição que segue


#### df 

* partições e pontos de montagem
* **df -h partição:** mostra informações da partição em formato humano
* **-T:** mostra o tipo de partição
* **-t tipo:** mostra somente partições do tipo especificado
* **-i:** mostra a ocupação de inodes (índices)
  * pode ocorrer falta de espaço de inodes mesmo com espaço em disco disponível

#### du

* mostra o tamanho dos arquivos e diretórios
* **--max-depth=x:** limite a busca a x níveis a partir do diretório atual

#### Investigar espaço em disco

1. **df -h:** identificar o ponto de montagem com falta de espaço
2. **du -hs /ponto/de/montagem/*:** identificar o diretório com maior ocupação
3. **du -hs /ponto/de/montagem/diretório/*:** repetir o processo até localizar os diretórios/arquivos a serem apagados/movidos

### 104.3 - Controle da montagem e desmontagem dos sistemas de arquivos

* **mount:** monta temporariamente a partição que segue
  * fstab parâmetro **user:** qualquer usuário pode montar, mas somente quem montou pode desmontar
  * fstab parâmetro **users:** qualquer usuário pode montar e demontar
  * se a partição não estiver no fstab, deve ser definido o ponto de montagem após a partição (somente root)
  * **-a:** monta tudo que está no fstab
  * **--bind:** monta um diretório no outro (associação)

* **unmount:** desmonta temporariamente a partição/ponto de montagem que segue
  * **-a:** desmonta tudo que está no fstab que não está sendo usado no momento
* **/etc/mtab:** arquivo que contém os filesystems atualmente montados
* **/etc/fstab:** arquivo de configuração com informações de montagem **~>** usado pelo **kernel**
  * cada linha uma partição
  * <file system> pode ser o UUID, partição ou filesystem volume name
  * <mount point> diretório a ser montada a partição
    * **swap** não tem ponto de montagem, deixado como **none**
  * <type> tipo de fs, pode ser deixado como **auto**
  * <options> parâmetros adicionais, pode ser deixado como **defaults**
  * <dump> tipo de backup antigo, para não realizar deixar em **0**
  * <pass> é a ordem de fsck, para não realizar no boot deixar em **0**
    * a partição **/** em geral é a primeira a fazer o fsck, tem o pass **1**
* **blkid:** mostra o UUID de cada partição
* **lsblk:** mostra a estrutura de árvore dos discos e partições
  * **-f:** mostra também o fs, UUID e lable

* Por ser usado o **systemctl** para montar uma partição baseado em um arquivo de montagem em **/lib/systemd/system**
  * **systemctl list-units --type=mount:** lista todas as unidades de montagem


### 104.4 - REMOVIDO

### 104.5 - Controlar permissões e propriedades de arquivos

#### Bits de Modos de Acesso

* **ls -l** mostra as permissões no diretório (não mostra os atributos especiais)
* diretório tem que ter **x** para ser acessível
* 12 bits
  * **Atributos Especiais:** SUID, SGID e Stick
  * **Usuário:** leitura, escrita e execução
  * **Grupo:** leitura, escrita e execução
  * **Outros:** leitura, escrita e execução
* **chmod alvos_operações_permissão arquivo:**
  * **alvo:** (u)ser, (g)roup, (o)thers ou (a)ll
  * **operação:** **+**, **-** ou **=**
  * **permissão:** (r)ead, (w)rite ou e(x)ecute
  * **chmod g-w arquivo**
  * **-R:** recursivamente nos arquivos/diretórios internos
* **chmod octal arquivo:**
  * usuário, grupo e outros
  * leitura(4), escrita (2) e execução (1)
  * (rwx)(rwx)(r-x) **~>** 775
  * **-R:** recursivamente nos arquivos/diretórios internos
* **stat:** mostra as permissões do arquivo/diretório que segue através do inode

#### Atributos Especiais

* **SUIG:** executado com as permissões de quem o criou, não de quem está executando
  * aplicado a scripts que precisa de acesso root
  * indicado pelo **s** nas permissões (rw**s**)(rwx)(rw)
  * **chmod u+s arquivo**
* **SGID:** executado com as permissões do grupo, não de quem está executando
  * indicado pelo **s** nas permissões (rwx)(rw**s**)(rw)
  * **chmod g+s arquivo**
  * aplicado a diretórios, todos os arquivos criados dentro pertencem ao grupo do diretório
* **Stick:** 
  * aplicado a um arquivo, só quem o criou pode apagar/renomear
  * aplicado a um diretório, só quem o criou pode apagar/renomear arquivos dentro
  * indicado pelo **t** nas permissões (rwx)(rwx)(rw**t**)
  * **chmod o+t arquivo**
  * diretório **/tmp** é um exemplo
* todos os atributos podem ser aplicados no modo octal
  * SUID(4), SGID(2) e Stick(1)
  * **chmod 4774:** SUID rwxrwxr
* **umask:** subtração da base para criação de arquivo/diretório
  * base arquivo: 666
    * ao criar um arquivo, ele terá as permissões 666-umask
    * se umask for 0002 **~>** arquivo 664 rw-rw-r--
  * base diretório: 777
    * ao criar um diretório, ele terá as permissões 777-umask
    * se umask for 0002 **~>** diretório 775 rwx-rwx-r-x
* **chown:** muda o dono do arquivo/diretório que segue(somente root pode executar)
  * **usuário:** muda o usuário dono
  * **:grupo:** muda o grupo dono (pode ser usado **.** ou **:**)
  * **usuário:grupo** ou **usuário.grupo:** muda o usuário e o grupo
  * **-R:** muda recursivamente nos arquivos/diretórios internos

* **chgrp:** muda somente o grupo dono do arquivo/diretório que segue

### 104.6 - Criar e alterar links simbólicos e hardlinks

* **ln arquivo atalho:** cria um hard link para o arquivo (atalho)
  * **ls -li:** mostra tanto arquivo como atalho apontam para o mesmo inode (primeiro campo)
    * mostra também que para esse inode há mais de uma referência (terceiro campo)
    * diretórios sempre tem 2 por conta do atalho **.** dentro que representa o diretório
  * mesmo excluindo o arquivo original, o atalho continua funcionando
  * só pode criar um link hard para um arquivo da mesma partição
  * não é possível criar hard links para diretórios
* **ln -s arquivo atalho:** cria um link simbólico (arquivo que aponta para outro)
  * é outro inode
  * permissão sempre 777
  * pode apontar para outra partição
  * pode apontar para diretórios
  * o tamanho do link é o número de bits do caminho até o arquivo

### 104.7 - Encontrar arquivos de sistema e conhecer sua localização correta

#### FHS: Hierarquia Padrão de Sistema de Arquivos

* **/bin:** programas críticos de todos usuários
* **/sbin:** programas críticos de administrador
* **/etc:** arquivos de configuração do sistema
* **/lib:** bibliotecas e módulos do kernel
* **/mnt** e **/media:** pontos de montagem de mídias externas
* **/proc** e **/sys:** diretórios dinâmicos de informações de hardware e processos
* **/dev:** dispositivos e arquivos especiais
* **/root:** diretório padrão do root
* **/boot:** arquivos de configuração do bootloader e imagens do kernel e initd
* **/var:** arquivos dinâmicos, logs, caches
* **/opt:** programas não críticos
* **/usr:** programas não críticos
  * **/local:** arquivos que o administrador instala
  * **/bin:** comandos não essenciais de todos os usuários
  * **/sbin:** comandos não essenciais do administrador
  * **/lib:** bibliotecas dos programas instalados
  * **/share/man:** fontes do man

#### Localização de arquivos

* **find:** ver item **103.3**

* **locate:** busca baseada em uma base de dados
  * mais rápido que o find
  * atualizada a cada inicialização do sistema
  * **updatedb** atualiza manualmente
* **whereis:** busca comandos, arquivos de sistema, libs e fontes de man
* **which:** busca somente no PATH a primeira correspondência à busca

# Exame 102

## 105 - Shells e scripts do Shell

### 105.1 - Personalizar e trabalhar no ambiente shell

* **./script:** executa o script em outro shell
  * precisa ter permissão de execução
  * somente variáis globais
* **source script** ou **. script:** executa o script no mesmo shell
  * não precisa ter permissão de execução
  * variáveis locais são consideradas
* **exec ./script:** executa no mesmo shell e fecha a seção
  * precisa ter permissão de execução
* **function:** cria uma função
  * **function funcao1 {comando1; comando2;...}**
  * **funcao1 () {comandos...}** tem o mesmo efeito
* **set:** mostra a definição das funções existentes
* **/etc/profile:** arquivo de configuração aplicado a cada login

  * vai executar também todos os scripts da pasta **/etc/profile.d/**
  * criar a função no final do arquivo para poder ser executada por todos os usuários
  * um alias para todos os usuários pode ser criado aqui
* **/etc/bash.bashrc:** arquivo de configuração aplicado a cada novo bash

  * um novo login também abre um novo bash
  * um alias para todos os usuários pode ser criado aqui
* **/etc/inputrc:** configurações de terminal, atalhos, modos de escrita
* **etc/skel/:** diretório contendo os arquivos que serão criados na home de cada novo usuário

  * esqueleto da home
  * chamar a função no final do arquivo **.profile** para ser executada no login de todos os usuários criados a partir deste momento
* **/home:** arquivos de configuração local (de cada usuário)

  * o sistema vai procurar as configurações nesses arquivos, nessa ordem
    * **.bash_profile**, **.bash_login** e **.profile**
    * chamar a função no final do arquivo para ser executada no login
  * **.bashrc:** para configurações de novos bashs para o mesmo usuário
    * aliases são definidos nesse arquivo
  * **inputrc:** configurações locais de terminal, atalhos, modos de escrita
  * **.bash_logout:** script executado durante o processo de logout do usuário
* **chsh:** muda o shell do usuário

### 105.2 - Editar e escrever scripts simples

* **script.sh:** normalmente salvos com .sh
* adicionar no PATH
  * **vi ~/.profile** adicionar na linha do PATH o caminho para os script
  * será válido após o próximo login
* **She-bang:** interpretador do script
  * definido na primeira linha
  * **#!/bin/bash**
* **./script parâmetro1 parâmetro2:**
  - **$0:** nome do script
  - **$#:** número de parâmetros
  - **$n:** enésimo parâmetro
* comandos
  * **VAR1=valor:** declaração de variável
    * pode receber um comando **VAR1=\`date +%H\`** ou **VAR1=$(date +%H)**
  * **read VAR1:** lê a entrada do usuário e armazena na variável VAR1 (não precisa declarar antes)
  * **if condição; then execução fi:** estrutura condicional do if
    * **[ -f /etc/bash.bashrc ]:** teste se existe o arquivo (atenção aos espaços entre os colchetes)
      * **if test -f /etc/bash.bashrc** tem o mesmo resultado
    * **[-d /etc/profile.d]:** teste se existe o diretório
    * **-r** permissão de leitura **-w** permissão de escrita **-x** permissão de execução
    * **-eq** = **ne** \!= **-gt** > **-lt** < **-ge** >= **-le** <=
  * **case $VAR1 in caso1) execução ;; caso2|caso3) execução ;; *) execução esac**
    * **\* ~>** else
  * **seq n:** faz a sequência de 1 a n
  * **expr expressão:** calcula a expressão
  * **for i in lista do execução done**
  * **while [ condição ] do execução done**
  * **until [condição de parada] do execução done**

## 106 - Interfaces de usuário e Desktops

### 106.1 - Instalar e configurar o X11

* servidor de interface gráfica
* os aplicativos são clientes desse servidor
* **etc/X11/xorg.conf:** principal arquivo de configuração
  * seção **files** contêm os módulos e as fontes usadas pelo X
  * seção **input device** contêm configurações de teclado e mouse
  * se uma aplicação define configurações para o X11, ela usa o diretŕoio **/usr/share/X11/xorg.conf.d**
* **/home/.xsession-errors:** arquivo de informações e logs referentes ao X11
* para gerar o arquivo xorg.conf
  * mudar para ambiente não gráfico **CTRL ALT F1...F6**
  * matar xorg **pkill xorg**
  * mudar de target **systemctl isolate mult-user.target**
  * parar o dm **systemctl stop xfwdm**
  * gerar o arquivo **Xorg -configure**

* **$DISPALY:** variável que armazena o X usado
  * **:0.0** indica monitor 0 local host
  * exibir programa em um segundo computador:
    * (primeiro computador) **export DISPLAY="ip_segundo_computador:0.0"**
    * (segundo computador) **xhost +"ip_primeiro_computador"**
    * (segundo computador) adicionar **xserver-allow-tcp=true** no arquivo xserver-command do diretório lightdm.conf.d
    * **OU**
    * (segundo computador) **xauth list** copiar 2º e 3º campo da última linha (novo a cada inicialização)
    * (primeiro computador) **xauth add campos_copiados**
* **Desktop Enviroment**
  * Conjunto de aplicações
  * Interface gráfica, Window Manager
  * **Display Manager:** resposável pelo login

* **Wayland:** surgiu para substituir do x11

### 106.2 - Desktops gráficos

* **Gnome**

  * segue o projeto **GNU**
  * usa **GTK**

* **KDE**

  * usa **QT**
  * multiplataforma (bsd, windows, macos)

  #### Protocolos de Acesso Remoto

* **XDMCP**
  * nativo do x11
  * sem segurança

* **VNC**
  * senhas criptografadas

* **SPICE**
  * mais seguro

* **RDP**

  * padrão do Windows
  * acessível com Remmina no linux

### 106.3 - Acessibilidade

* **Stick keys:** conjunto de teclas podem ser apertadas uma de cada vez
* **Slow keys:** teclas precisam ser pressionadas por um tempo para serem reconhecidas
* **Bounce keys:** delay na repetição das teclas

#### Reconhecimento de voz

* CMUSphinx, Simon e Julius

## 107 - Tarefas administrativas

### 107.1 - Administrar contas de usuário, grupos e arquivos de sistema relacionados

* **/etc/passwd:** principal arquivo que contém os usuários

  * não é recomendado fazer alterações no arquivo **~>** comandos no terminal
  * toda aplicalção tem um usuário
  * cada linha um usuário
  * **nome:senha:UID:GID:descrição:diretório_padrão:shell_padrão**
  * **senha:** marcado com **x** indica que está salva e hasheada no arquivo **/etc/shadow**

  * **Group ID:** 4º campo, ID do grupo padrão do usuário (poder estar em mais de um grupo)
    * **UID = 0** e **GID=0:** usuário root 
  * **diretório_padrão:** se for humano **~>** /home
  * **shell_padrão:** se for humano **~>** /bin/bash (normalmente)
    * usuário de aplicações não podem fazer login **~>** /bin/false
* **/etc/shadow:** arquivo contendo as senhas hasheadas dos usuários
* **/etc/group:**

  * **nome:senha:GID:usuários_fora_o_padrão**
  * os usuários que aparecerem aqui têm esse grupo como secundário
* **/etc/gshadow:** arquivo contendo as senha hasheadas do grupos
* **etc/login.defs:** arquivo contendo configurações de login

  * **CREATE_HOME:** parâmetro para criar home ao criar um novo usuário
* **useradd usuario:** cria o usuário, cria o grupo com o mesmo nome, porém não define senha
  * sem senha o usuário não pode logar
  * sinal de **!** no lugar da senha hasheada no /etc/shadow
  * **useradd argumento usuário**
  * **-c "descrição":** adiciona descrição ao usuário no passwd
  * **-s caminho/bash:** define o bash padrão do usuário
  * **-g grupo:** define o grupo padrão
  * **-G grupos:** define os grupos adicionais
  * **-m:** força a criação da home, mesmo que não tenha a instrução no login.defs
  * **-M:** inibie a criação da home, mesmo que tenha a instrução no login.defs
  * **-p senha_hasheada:** define a senha **somente** se estiver hasheada
    * ver comando **passwd**

  * **-u numero:** define o UID do usuário especificado
* **usermod argumento usuario:** modifica um usuário existente
  * mesmos argumentos do useradd
  * **-G grupo:** define como **único** grupo secundário
  * **-a -G grupo_secundário:** adiciona o grupo secundário
* **userdel usuario:** apaga o usuário mas mantém a pasta **home**
  * **userdel -r usuario:** apaga o usuário e a pasta **home**
* **passwd usuario:** define a senha para o usuário existente
  * somente **passwd** altera a senha do usuário atual
  * usuário comum só pode trocar a própria senha
* **su usuário:** troca de usuário no bash
* **adduser:** script que cria o usuário, grupo e já pede para definir a senha
  * não considerar para a prova
* **groupadd grupo:** cria o grupo
  * **grupo argumento grupo**
  * **-g numero:** define o GID
* **groupmod argumento grupo:** modifica o grupo
  * **-n nome:** altera o nome
  * **-g numero:** muda o GID
* **newgrp grupo:** assume o grupo (se fizer parte ou ter a senha do grupo)
  * arquivos criados/modificados passam a ser com esse grupo
* **gpasswd grupo:** define senha para o grupo
  * usuários com a senha podem assumir o grupo, mesmo que não façam parte dele
* **id:** mostra o UID, GID e os grupos do usuário atual
  * **id usuario:** mostra essas informações do usuário (não precisa ser root)
* **groups:** mostra os grupos do usuário atual
  * **groups usuario:** mostra os grupos do usuario especificado
* **getent passwd usuario:** mostra a linha do passwd do usuario
  * **getent group grupo:** mostra a linha do group do grupo
* **chage argumento usuario:** somente root
  * essas informações ficar no arquivo **shadow**
  * **-l:** lista informações sobre o usuário, quando alterou senha, expiração de conta e senha
  * **-M dias:** define como dias o prazo para expirar a senha
    * senha expirada **~>** redefine a senha no login
  * **-d data:** define a data da útlima alteração de senha
    * **-d 0:** força o usuário a alterar a senha no próximo login
  * **-E data:** define a data de expiração de conta
    * conta expirada **~>** não loga
  * **-E -1:** ativa novamente a conta com data de expiração **never**

### 107.2 - Automatizar e agendar tarefas administrativas de sistema

#### Cron

* agendamente de atividades recorrentes

* é um deamon **~>** tem que estar rodando para fazer as verificações de tarefas

* **/etc/crontab:** somente root

* **/var/spool/cron/crontabs/:** diretório que ficam armazenados os crons dos usuários

  * um arquivo para cada usuário que tenha crontab

* agendamentos pré definidos na instalação

  * há diretórios que são lidos e têm seus scripts executados **~>** run-parts
  * diretórios **/etc/cron.daily**, **/etc/cron.weekly** e **/etc/cron.monthly**

* **crontab -l:** lista os agendamentos do usuário atual

  * se não tiver agendamentos informa que não há crontab para o usuário

* **crontab -l -u usuario:** lista os agendamentos do usuário especificado (somente root)

* **crontab -e:** entra no crontab em modo de edição

* **crontab -r:** remove os registros do crontab

* **crontab arquivo:** importa as configurações do arquivo

* **minuto hora dia_mês mês dia_semana comando**

  * **\*:** todo minuto/hora/dia...
  * **n,m:** todo minuto/hora/dia n e todo minuto/hora/dia m
  * **n-m:** no intervalo entre n e m minutos/horas/dias
  * **\*/m:** a cada m minutos/horas/dias

  * **segunda:** 1 **terça:** 2 **quarta:** 3 **quinta:** 4 **sexta:** 5 **sábado:** 6 **domingo:** 7 e 0

* arquivos de permissão e proibição do cron

  * não existem por padrão **~>** todos usuários podem usar o cron **DEBIAN**
    * para **RED HAT**, se não existirem, somente o root pode usar o cron
  * **/etc/cron.deny:** arquivo que proibe o uso do cron para os usuários listados
  * **/etc/cron.allow:** arquivo que permite o uso do cron **SOMENTE PARA OS USUÁRIOS LISTADOS**
    * com exceção ao root, que sempre vai poder usar

#### AT

* agendamente de uma atividade uma única vez
* **at now +xhour**, **at hora** ou **at hora diamêsano**
  * entra no prompt do at
  * digite os comandos em cada linha
  * **CTRL + D** para terminar **~>** aparece como **\<EOT>**
* **atq** ou **at -l:** lista os jobs agendados e o seu ID
* **atrm ID** ou **at -d ID:** remove o agendamento
* se não existirem **/etc/at.allow** e **/etc/at.deny**, somente o root pode usar
  * normalmente é criado o **at.deny** automaticamente

#### Systemd Timer

* agendamento de atividades recorrentes
* **systemctl list-timers:** mostra os agendamentos
* **unit:** arquivo .timer
  * onde está definido o agendamento
* **activates:** arquivo .service
  * onde estão os comandos
* **man systemd.time:** manual (time mesmo, não timer)
* **OnBootSec:** seção para definir agendamento em relação ao boot do sistema

#### Systemd Run

* agendamento de uma atividade uma única vez
* **systemd-run --on-active=xs comando**
* a unidade é listada no **systemctl list-timers**

### 107.3 - Localização e internacionalização

* linux trabalha com **UTC**
* **/etc/localtime:** atalho para o arquivo de timezone definido na instalação
  * pode-se alterar o destino do link para alterar o timezone

* **/usr/share/zoneinfo/:** diretório contendo os diferentes fuso horários que podem ser definidos
* **tzselect:** menu para mostrar os timezone
  * **export TZ=timerzone_desejado:** altera o timezone para o usuário atual

* **locale:** exibe informações de idioma e formatos
  * **-a:** mostra os disponíveis
  * **LC_ALL:** variável que sobrescreve todas as outras na definição do formato

* **iconv -f ISO-8859-1 -t UTF-8 arquivo_win:** converte (somente mostrando na tela) o arquivo_win (gerado no windows, com padrão ISO) para UTF-8

## 108 - Serviços essenciais do sistema

### 108.1 - Manutenção da data e hora do sistema

* **hwclock:** horário do hardware
  * **hwclock -w** ou **hwclock --systohc:** ajusta o horário do hardware para ficar igual ao do sistema
  * **hwclock -s** ou **hwclock --hctosys:** ajusta o horário do sistema para ficar igual ao do hardware
  * **hwclock --set --date "mes/dia/ano hora:min":** define a data/hora do hardware
* **date:** data, horário e timezone do sistema
  * **date -u:** hora em UTC
  * **date +%Y:** Ano com 4 dígitos
  * **date +%y:** Ano com 2 dígitos
  * **date +%H:%M:** hora:minutos
  * **date mesdiahoraminutoano:** define a data/hora atual
* **ntp:** Network Time Protocol
  * o computador pode ser cliente de um servidor online e servidor de computadores locais ao mesmo tempo
  * **ntpd:** é o deamon (programa em background) que atualiza o horário constantemente
  * UDP porta 123
  * **/etc/ntp.conf:** principal arquivo que configuração
    * **pool** ou **server:** servidores consultados para a sincronização
  * **nptq -p:** mostra os servidores e estatísticas
* **ntpdate ip_servidor:** atualiza uma única vez usando o servidor especificado
  * **ntpdate -q ip_servidor:** verifica se o servidor está ok para ser utilizado
* **chrony:** outra implementação do ntp
  * **chronyd:** deamon 
  * **/etc/chrony.conf** ou **/etc/chrony/chrony.conf:** arquivo de configuração
  * **chronyc tracking:** mostra informações gerais
  * **chronyc sources:** lista os servidores

### 108.2 - Log do sistema

* **rsyslog:** principal programa de logs, evolução do **syslog**
  * **/etc/rsyslog.conf:** arquivo de configuração
  * **/etc/rsyslog.d/:** diretório contendo arquivos de configuração que programas podem definir
    * no arquivo default:
      * **facility.priority destino:** gerador (programa), prioridade (erro, aviso, info) e arquivo.log
      * ao definir a prioridade, vai logar tudo a partir dela
        * warning **~>** warning, error, crit, alert e emerg
        * crit **~>** crit, alert e emerg
        * debug **~>** todos os tipos
      * **facility.=priority:** somente a prioridade especificada
* **logger -p facility.priority -t [TAG] "teste de log":** gera um log no destino definido com a TAG definida
* **logrotate:** apagar logs antigos
  * não tem deamon **~>** deve ser executado pelo cron
  * **/etc/logrotate.conf:** arquivo de configuração
    * weekly, daily
    * **rotate X:** mantém x arquivos
  * **/etc/logrotate.d:** diretório contendo arquivos de configuração
    * **inetutils-syslogd:** principal arquivo
      * ` /var/log/arquivo.log {` 
      * `rotate 7` logs a manter
      * `daily` frequência de registro
      * `compress` compactar
      * `delaycompress` não compactar o penúltimo
      * `}` 
* **systemd-journal:** registrador de logs
  * trabalha em paralelo ao rsyslog e integrado (socket)
  * **etc/systemd/journald.conf:** principal arquivo de configuração
  * **journalctl:** mostra os logs
  * **journalctl -b:** logs relacionados ao boot
  * **journalctl --since "data":** desde a data
  * **journalctl --since "data_inicial" -until "data_final":** intervalo
  * **journalctl --disk-usage:** mostra o espaço utilizado pelos logs do journal (ativos e arquivados)
  * **journalctl --vacuum-size=xM:** limpa x MB dos logs ARQUIVADOS (os ativos permanecem)
  * **journalctl --verify:** verifica logs corrompidos
  * **journalctl --sync:** grava em disco o que está em memória
  * **systemd-cat comando:** faz a saída do comando ir para o journal (semelhante ao logger)

### 108.3 - Fundamentos de MTA (Mail Transfer Agent)

* protocolo SMTP **~>** porta 25
* sendmail, postfix e exim
* **Open Relay:** configuração que permite qualquer usuário da internet enviar emails (normalmente spam e phishing)
* **mail:** entra no comando e mostra a caixa de entrada do usuário
  * **/var/spool/mail/usuário** ou **/var/mail/usuário:** diretório padrão dos emails
  * digitar o número do email para visualizar
  * **q:** sai e informa que as msgs visualizadas foram para **/home/usuário/mbox**
* **mail -s "Assunto" destino:** sintaxe para enviar email (local ou internet)
  * pede quem vai em cópia e em seguida o corpo do email
  * **CTRL+D** para finalizar
* **echo "corpo do email" | mail -s "Assunto" destino** tem o mesmo efeito
* **mail -s "Assunto" destino < arquivo_corpo_email:** tem o mesmo efeito
* **/etc/aliases:** arquivo que contém os aliases de email
  * **alias:	email**
  * após alterar precisa dar o comando **newaliases**
* **/home/usuário/.forward:** arquivo que pode ser criado por qualquer usuário para redirecionar emails
  * arquivo de texto contendo somente os destinos, um por linha
* **mailq:** mostra a fila de emails
  * **sendmail -bp** tem o mesmo efeito
* **sendmail -q:** tenta reenviar os emails da fila
  * **postqueue -f** tem o mesmo efeito

### 108.4 - Configurar impressoras e impressão

#### CUPS - Common Unix Printing System

* **cupsd:** deamon que precisa estar em execução
* configurações via web **~>** 127.0.0.1:631
  * cada impressora aparece como uma **QUEUE**
  * **/etc/cups/printers.conf:** arquivo de configuração
* **lpinfo -v:** mostra as impressoras disponíveis
* **lpinfo -m:** mostra os drivers de impressoras
* **lpstat -t:** mostra informações gerais do cups (deamon, impressora padrão e status das impressoras)
* **lpstat -a:** mostra o status das impressoras
* **lpq:** mostra a fila da impressora padrão
  * **lpq -P nome_impressora:** mostra a fila da impressora especificada
  * **/var/spool/cups:** diretório dos arquivos
    * começando com **c:** impressos
    * começando com **d:** fila
* **lpadmin -p Nome_Impressora -E -v "device_impressora" -m driver_impressora**
  * instalação de impressora
  * **-E:** ativa (enable)
  * **-v:** indica o device (lpinfo -v)
  * **-m:** indica o driver (lpinfo -m | grep "modelo_impressora")
* **lpoptions -p nome_impressora:** mostra as propriedades da impressora
* **lpoptions -d nome_impressora:** define a impressora como padrão
* **lpr arquivo:** imprime o arquivo na impressora padrão
* **lpr arquivo -P nome_impressora:** imprime o arquivo na impressora definida
* **cat arquivo | lpr** tem o mesmo efeito
  * **echo "corpo do documento" | lpr** tem o mesmo efeito
* **lprm -:** limpa toda a fila de impressão
  * **lprm -P nome_impressora:** limpa a fila da impressora especificada
* **/var/log/cups:** diretório dos logs do cups



## 109 - Fundamentos de Rede

### 109.1 - Fundamentos de protocolos de internet

#### IPv4

* 32 bits

* **Classe A:** primeiro octeto de 1 a 126 **~>** 1.0.0.0 até 126.255.255.255
  * IPs privados de 10.0.0.0 até 10.255.255.255
* **Classe B:** primeiro octeto de 128 a 191 **~>** 128.0.0.0 até 191.255.255.255
  * IPs privados de 172.16.0.0 até 172.31.255.255
* **Classe C:** primeiro octeto de 192 a 223 **~>** 192.0.0.0 até 223.255.255.255
  * IPs privados de 192.168.0.0 até 192.168.255.255

#### Máscara

* mesmo formato do IP
* indica que parte do ip representa a rede e que parte representa o disponitivo
* **CIDR:** indica a rede (/8, /16, /24 ou /32)
  * **255.0.0.0:** primeiro octeto é a rede **~>** /8
  * **255.255.0.0:** primeiro e segundo octetos são a rede **~>** /16
  * **255.255.255.0:** primeiro, segundo e terceiro octetos são a rede **~>** /24
  * **255.255.255.255:** um único host **~>** /32
* o primeiro ip é sempre o ip da rede
* o último ip é sempre o de broadcast
* dividir a rede em 2 **~>** ocupar mais um campo com a rede com metade do range
  * 255.255.255.0 pode ter 254 hosts (/24)
  * 255.255.255.128 pode ter 126 hosts (/25)

#### Protocolos

* **gateway padrão:** faz a conexão entre duas redes/subredes
* **IPv6:** 128 bits
  * 8 grupos de 4 caracteres hexadecimais
  * 4 0 seguidos podem ser abreviados para ::
  * **unicast:** endereço que identifica uma única interface
  * **multicast:** endereço que identifica um conjunto de interfaces e envia o pacote para cada uma
  * **anycast:** endereço que identifica um contjunto de interfaces e envia o pacote para somente uma (normalmente a mais próxima)
* **TCP:** Transmission Control Protocol
  * orientado a conexões **~>** mais confiável
  * garante a entrega dos pacotes e na ordem certa
  * reenvia o pacote em caso de erro
  * utlizado por **FTP**, **SMTP**, **HTTP** entre outros
* **UDP:** User Datagram Protocol
  * não orientado a conexões
  * não garante a entrega dos pacotes
  * melhor performance
  * utilizado por **DNS**, **NFS** e **NTP**
* **ICMP:** Internet Control Message Protocol
  * transmitir informações de controle
  * controle de tráfego
  * detecção de destinos não atingíveis
  * redirecionamento de rotas
  * verificação de hosts remotos
  * utilizado pelo **ping** e **traceroute**

#### Portas e Serviços

* cada serviço roda em uma porta de 16 bits **~>** máximo de 65535 portas em um dispositivo
* **portas reservadas:** até 1024
  * **20** e **21:** FTP
  * **22:** SSH
  * **23:** Telnet
  * **25** e **465:** SMTP e SMTPS
  * **53:** DNS
  * **67** e **68:** DHCP
  * **80** e **443:** HTTP e HTTPS
  * lista em **/etc/services**
* IPv4:porta ou [IPv6]:porta

### 109.2 - Configuração persistente de rede

* **/etc/hostname:** nome da máquina
  * **hostnamectl set-hostname novo_nome:** altera o nome da máquina
* **/etc/hosts:** arquivo de hosts
* **/etc/nsswitch.conf**
  * **hosts: files dns:** procura primeiro nos arquivos internos e depois em um dns
* **/etc/networks:** semelhante ao hosts, mas para redes
* **/etc/resolv.conf:** determina o servidor DNS usado
  * dinamicamente alterado pelo systemd
* **systemctl status NetworkManager:** mostra o serviço de gerenciamento automático de redes
* **/etc/NetworkManager/:** diretório contendo arquivos de rede
* **nmcli:** mostra as interfaces
  * **nmcli device** ou **nmcli d:** resumo das interfaces
    * começando com **e** é cabo, com **w** é wireless
  * **nmcli radio:** mostra se o wifi está habilitado
  * **nmcli network:** mostra se a placa de rede a cabo está habilitada
  * **nmcli connection** ou **nmcli con:** mostra as conexões
  * **nmcli con down "nome_conexão":** encerra a conexão (up reconecta)
  * **nmcli con add type ethernet con-name nome_rede ifname dispositivo ip4 ip_fixo/CIDR gw4 ip_gateway:** cria uma nova conexão ethernet com ip fixo
  * **nmcli device wifi list:** lista os wifi disponíveis com nome, signal, segurança
  * **nmcli device wifi list:** escaneia os wifis disponíveis
  * **nmcli device wifi connect SSID_wifi password senha_wifi:** conecta no wifi
* **HAL:** Hardware Abstraction Layer
* **ifup:** sobre uma interface
  * baseado no arquivo **/etc/network/interfaces**
* **systemctl status systemd-networkd:** mostra o serviço de configuração de rede do systemd
  * desabilitado por padrão pois é usado o NetworkManager

### 109.3 - Soluções para problemas simples de rede

#### IP

* **ip link show:** mostra as interfaces em uso
* **ip address show** ou **ip addr show:** mostra as interfaces e os ips
* **ip route show:** mostra as rotas
  * default é o roteador
* **ip addr add ip_fixo/CIDR dev placa_rede:** adiciona o ip à placa (somente na seção atual)
  * **del** no lugar de **add** retira o ip da placa (somente na seção atual)
* **ip addr flush dev placa_rede:** retira todos os ips da placa (somente na seção atual)
* **ip link set placa_rede down:** desconecta a placa
  * **up** no lugar de **down** reconecta
* **ip route del nome_rota:** apaga a rota
  * **ip route del default:** apaga a rota para o roteador
* **ip route add rede/CIDR via ip:** adiciona a rota para todo pacote enviado a rede passar pelo dispositivo com o ip especificado
  * **ip route add default via ip_roteador:** adiciona novamente a rota para o roteador/internet

#### IFCONFIG

* **ifconfig:** mostra as interfaces ativas e os ips (semelhante ao ip)
  * faz parte do pacote net-tools
  * **RX:** recebimento de pacotes e erros no recebimento
  * **TX:** envio de pacotes e erros no envio
* **ifconfig dispositivo down:** desativa a interface
  * **up** no lugar de **down** ativa a interface

* **ifconfig -a:** mostra todas as interfaces
* **ifconfig dispositivo ip/CIDR:** define o ip fixo para o dispositivo
* **ifconfig dispositivo:x ip/CIDR:** define outros ips (0, 1, 2... no lugar de **x**)

#### ROUTE

* **route:** mostra as rotas (semelhante ao ip route)
  * faz parte do pacote net-tools
* **route -n:** não resolve os nomes (definidos em /etc/hosts e /etc/networks)
* **route del rota:** apaga a rota
  * **route del default:** apaga a rota para o roteador/internet
* **route add -net rede/CIDR gw ip:** adiciona rota
  * **route add default gw ip_roteador:** adiciona a rota para o roteador/internet

#### Solução de problemas

* **hostname -d:** mostra o domínio (definido no hosts)
* **hostname -f:** mostra o **fqdn** (nome completo de domíno)
* **ping ip_destino:** envia um ECHO_REQUEST e recebe um ECHO_REPLY
  * **ping -cx  -iy ip_destino:** envia **x** pacotes a cada **y** segundos e para
  * alguns hosts não respondem o icmp
  * **ping -4 ip_destino:** para especificar IPv4
    * **-6** no lugar do **-4** para especificar IPv6

* **traceroute destino:** mostra a rota até o destino
  * usa UDP
  * **traceroute -I destino:** usa ICMP
  * **traceroute6 destino:** para especificar IPv6

* **tracepath destino:** mais simples que o traceroute
  * não exige privilégios de admin em nenhuma função

* **ss:** mostra todas as conexões da máquina (sockets)
* **ss -tu:** mostra as conexões TCP e UDP estabelecidas
  * ESTAB

* **ss -tua:** mostra todas as conexões TCP e UDP
  * ESTAB, LISTEN e UNCONN

* **ss -tuan:** não resolve os nomes (mostra as portas)
* **ss -tuanp:** mostra o PID dos processos responsáveis por cada conexão
* **netstat:** funciona da mesma forma que o ss
  * faz parte do pacote net-tools

* **netcat destino porta:** testa a conexão
  * **nc destino porta:** tem o mesmo efeito
  * semelhante ao **telnet**

* **nc -l -p numero_porta:** coloca a porta especificada em modo LISTEN
* **nc -vz destino porta:** testa a conexão e mostra succeeded ou connection refused

### 109.4 - Configurar DNS cliente

* **/etc/hosts:** referências de ip, nomes e e domínios 
* **/etc/nsswitch.conf:** define a ordem da busca pelo dns (padrão files e depois dns)
* **/etc/resolv.conf:** indica o servidor DNS consultado
  * nameserver 8.8.8.8 (exemplo)
* **host nome_destino:** resolve o nome e mostra os ips
  * **host -t mx nome_destino:** resolve o registro de emails do destino
  * **host ip_destino:** mostra o nome **~>** DNS reverso
* **dig nome_destino:** mais completo que o host
  * faz a consulta no systemd-resolv
  * **dig nome_destino +short:** mostra somente o ip resolvido
  * **dig nome_destino @ip_DNS:** resolve o nome usando o servidor DNS especificado
  * **dig -x ip_destino:** DNS reverso
  * **dig -x ip_destino +short:** mostra somente o nome resolvido
* **getent:** consulta as bases do nsswitch

## 110 - Segurança

### 110.1 - Tarefas administrativas de segurança

### 110.2 - Segurança do host

### 110.3 - Proteção de dados com criptografia

