# Aula 3

## Resumo da última aula
SO fornece serviços adicionais ao hardware.
Administração de recursos, facilitando esse acesso.
Compartilhamento de recursos entre diferentes processos.
Dividir o uso dos recursos ao longo do tempo e do espaço.

História dos SOs e as gerações.

## Estrutura interna dos SOs

SO é um software, que é dividido internamente.

### Núcleo do SO pode ser Monolítico

    - Não tem uma divisão clara da divisão interna do software

### Dividir o SO em Camadas

    - Device Drivers - Módulos do SO que fazem a comunicação com o dispositivo
    Ex: Impressora, Disco Magnético, Placa de Rede, Disco Rígido

    - Camada de Rede 
    
### Microkernel
    - Tem partes do SO que devem fazer parte do núcleo
    - Porém tem outras partes que podem ser externas    
    - Processo de gerenciamento de arquivos poderia ser externo ao núcleo: Permissões
    para leitura e escrita
    - Divisão da memória dos processos poderia ficar fora do núcleo (Teórico: Empresas não implementam)
    - Vantagem: Diminuir a dependência entre as partes do sistema e facilitar a troca de versão
    - Desvantagem: Perda de Desempenho (Mudança do modo de operação)

    - Interface Gráfica do Usuário do Linux: Fora do núcleo. Porém no windows é dentro do núcleo.
    - Unix não é microkernel
    - X Window System

    - Impressão de Arquivos também fica fora do kernel. Em Mainframes ficava dentro do kernel.
       
    - Nem GUI e Impressão de Arquivos são suficientes para caracterizar um SO Microkernel!

### Separação dos SOs 
    - SOs de Servidores -> Sem uso forte de GUI, e foco em desempenho.
    - SOs de Máquinas Clientes
    - SOs de Celulares/Tablets -> Foco em Economia de Energia -> Sem uso, "desligamento" da CPU.
    - SOs embarcados -> Realizam uma função específica. Ex: Roteador, Máquinas Fotográficas
    - SOs de Máquinas Simples -> Associados a um sensor simples.
    - SOs Real-Time (Tempo Real) -> Poucos Processos. Focada em Resposta em Tempo Real. 
    Garantia de execução em tempos determinados e claros. Ex: Máquinas de Batimento Cardíaco, e Aviação. 
    Escalonamento de Processos por si só não garante a existência de um SO Real-Time.

## Revisão de Organização de Computadores
    - Processor (CPU)        
    - Memória 
    - Registradores: Propósito Geral e Específico
        - Program Counter (PC): Só é acessado de forma indireta.
        - Registrador de Estados (Instruções de Comparação e Estruturas de Controle): Também só é acessado
        de forma indireta.
        - Stack Pointer (SP) -> Guarda o topo da pilha. Conseguimos acessar diretamente e indiretamente.

O SO tem que conhecer os registradores, pois eles precisam ser preservados na Troca de Contexto.
Também acontece nas interrupções da CPU.

### i = i + 5

```as
mov eax, [5000]
add eax, 5
mov [5000], eax <- Interrupção
```

### Rotina tratadora da interrupção 

```as
in eax, 90 -- Recebe a tecla digitada
mov [80000], eax
iret
```

### Barramento

CPU <--> MEM ==> Barramento de Memória

A CPU para quando faz a requisição a memória.
Custa alguns ciclos da CPU, na ordem de dezenas.

Memória Cache: Memória intermediária entre a CPU e Memória.

O Hardware se preocupa em guardar na memória cache os bytes da memória principal.

A alteração na memória afeta o cache e a memória.

- Presença de Níveis de Memória Cache (apenas uma memória cache não seria suficiente):
    - L1, L2 e L3

### Chip Multicore

Imagine um Chip de Processador com 4 Cores.

Para cada CPU, tem um cache nível 1
Porém o cache nível 2 é compartilhado.

Problema: Alteração do Endereço [5000]
Vai ser atualizada a cache L1 específica de uma CPU, a cache L2, e a memória.

Os Caches L1 das outras CPUs, "observam" a atualização de registradores de outras CPUs no cache L2.

Os SOs quando for possível, evitam que a mesma variável seja usada por CPUs diferentes.

Invalidação pelo Cache L2 causa perda de desempenho.

Esse mesmo problema pode causar problemas se houver mais chips de CPU.

## Interrupções
    - Eventos que desviam o fluxo de execução da CPU
    - A CPU para temporariamente de executar (o que estava executando)
    e executa uma rotina tratadora do tipo de interrupção
    - Quando a rotina tratadora acaba a instrução que estava executando é reiniciada
    - Para cada tipo de interrupção existe uma rotina tratadora diferente
    - Tem vários números que representam o tipo de interrupção

### Tipos de interrupção
    - Gerado por Dispositivo: 2 lógicas. Receber um dado do dispositivo. Escrita no dispositivo (neste caso, o dispositivo é capaz de receber um dado novo). Na maioria das vezes o dispositivo não está fazendo nada (idle). Ex: Escrita em disco magnético, placa de rede, teclado.

    - Interrupção de Relógio: 100x por segundo. Se o meu processo roda mais de um décimo de segundo, ele é interrompido, e acontece a troca de contexto. -> Preempção

    - Gerada pelo próprio programa. 
        - Exceptions como divisão por zero. 
        - Ou acesso indevido a memória.

        - Interrupção de Software
            - Simulação da interrupção de hardware. Usada para chamar o SO.


    - Interrupções "Simultâneas"
        - Durante a rotina de interrupção, ocorre outra interrupção.
        - Prioridade da interrupção: Cada tipo de interrupção 
        tem uma prioridade.
        - A priorização das interrupções é feita pelo SO, enquanto a interrupção em si é 
        feita pelo hardware.

    - Chamadas ao SO
        - O SO não é um programa convencional composto por rotinas que chamam umas as outras.
        - Existem diferentes pontos de entrada do SO, que rodam diferentes rotinas para diferentes funções
        (Lembra muito a estrutura de um Processo-Servidor).