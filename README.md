# LPIC1

* 60 Questões para o exame **101** e 60 questões para o exame **102**.

# Exame 101

* Tópicos 101, 102, 103 e 104

## 101

## 102

## 103 (26/60)

### 103.1

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



### 103.2

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



### 103.3

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

  * mesmos parâmetros do **cp**, com exceção a **-r** e **-p**
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

* **tar:** aglomera arquivos

  * **tar arquivo.tar arquivos**
  * **c:** cria um aglomerado
  * **x:** extrai um aglomerado
  * **t:** lista os itens de um aglomerado
  * **p:** preserva as características
  * **f:** cria o arquivo com o nome que segue
  * **z:** compacta com **gzip**
  * **j:** compacta com **bzip2**
  * **J:** compacta com **xz**

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
    * se o destino for um diretório, copia o conteúdo
    * para criar imagem é só definir **.img** no destino

### 103.4

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
* **xargs:** redireciona a saída de um comando para a entrada de outro
  * **find /home -name "arquivos" | xargs ls -l**
* **\`comando\` (crase) ou $(comando):** executa um comando dentro de outro
  * **echo "A versão desse kernel é $(uname -r)"**

### 103.5

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
