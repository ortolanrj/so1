# Aula 4

## Resumo da última aula

- CPU
- Memória
- Registradores

### Processadores Multi-núcleo

- Problema de invalidação de Cache Nível 1

- Dispositivos
  - Armazenar Dados
  - Interface Homem
  - Comunicação
- Interrupções
  - Gerada pelo Programa
  - Gerada pelo Dispositivo
  - Acionadas explicitamente pelo processador (int 0x80)
  - Interrupções Simultâneas (As interrupções são divididas em alta prioridade e baixa prioridade)

## Modo de Operação

Razoável diferenciar o que um programa comum pode fazer, e um programa específico de um SO.
Se o progama der uma ordem errada a uma placa de vídeo por exemplo, pode vir a existir problemas.

Por isso, que existem níveis de privilégio. Por exemplo, um programa normal (User mode).

Desta forma, existem pelo menos dois modos de operação:
 - Usuário (User level/Userland) - Não privilegiado
 - Núcleo (ou Sistema/Kernel) - Privilegiado

### Restrições do modo não privilegiado

- Executar instruções que fazem comunicação direta com dispositivos (Somente como administrador) -> Se isso acontecer, o processo tipicamente será abortado

- MS-DOS: Não existia restrição de modo de operação. Por exemplo, em jogos, os mesmos tinham acesso direto a Placa de Vídeo. Como um jogo desse consegue rodar hoje?
Tipicamente, o SO abortaria o processo, através de uma interrupção. Porém é possível o SO (Windows) detectar que o programa foi feito para MS-DOS, e o SO fazer uma intermediação de comunicação entre o programa e o Hardware (Emulação/Simulação).

- Esse mecanismo também é um elemento importante para a construção de Máquinas Virtuais.

- Acessar uma parte da memória não permitida (MMU gera a interrupção)

- Qualquer interrupção muda o modo de operação

## Programa e Processo

Programa é um arquivo (source code) e processo é este programa em execução.

A CPU só executa código que está em memória.

- SO Tradicional:
  - Cardinalidade de Relacionamento: Qual a quantidade de entidades pode se ter em cada parte do relacionamento. Ex: 1-N, ou M-N.

  Relacionamento Programa-Processo: 1-N

- UNIX:
  Relacionamento Programa-Processo: M-N

Cada processo tem um número de identificação (PID - Process Identifier Number)

- SO que executa um processo por vez: Vários processos eram executados um de cada vez.

- Computador com vários processadores: A execução dos processos ocorre de forma simultânea

- Computador com um Processador só: Não tem execução simultânea. Parte dos processos são executados em diferentes intervalos de tempo.

Se em um intervalo de tempo, um programa estava sendo executado pela CPU 1 começa a ser executado pela CPU 2, pode haver invalidação do cache.


## Processos do ponto de vista do usuário

- Interface de Linha de Comando

"$ nome_do_programa parametro1 parametro2 ..."

Ex: "$ cat a.txt"

 - Saída Padrão - Normalmente a tela
 - Entrada Padrão - Normalmente o teclado
 - Saída de erro padrão - Normalmente a tela. Para imprimir mensagens de erro

Redirecionar a entrada padrão

Ex: "sort < c.txt"

Se for executado com "sort < c.txt > resultado.txt &", vai rodar em background. Ou seja, executado em paralelo com o prompt.

Geralmente executado com o redirecionamento da saída para um outro arquivo. Senão ele mostrará os resultados no próprio prompt.

Uso de pipe: Redirecionar a saída padrão de um programa para a entrada padrão de outro programa. Podem ser executados em paralelo.

Ex: "$ cat a.txt b.txt | sort". -> O sort espera o arquivo acabar.

