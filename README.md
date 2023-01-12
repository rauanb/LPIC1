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

## 104



