# Aula 12

## Aula passada

### Escalonamento de Threads/Processos

Processos/Threads
    - CPU Bound
    - IO Bound

Métodos para avaliação de Algoritmos 
de Escalonamento

Custo de Troca de Contexto

Algoritmos
    - Não preemptivo
    - Preemptivo

Implementações 
    - FIFO
    - Shortest Job First
    - SJF Preemptivo

## Round Roubin - Algoritmo de Escalonamento

Cada processo/thread tem um tempo máximo de execução(Time Slice)

Duração do Time Slice
 - Muito comprido - Se assemelha ao FIFO
 - Muito curto - Custo da alta troca de contexto

Comparando FIFO e Round-Robin (Processos CPU-Bound / Sem bloqueio de IO)

## Round-Robin:


| Tempo | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | N | 21| 22| 23|
|-------|---|---|---|---|---|---|---|---|---|---|---|---|
|  P1   | x | x |   |   |   |   |   |   |   | x |   |   |
|  P2   |   | x | x |   |   |   |   |   |   |   | x |   |
|  P3   |   |   | x | x |   |   |   |   |   |   |   | x |
|  P4   |   |   |   | x | x |   |   |   |   |   |   |   |
|  P5   |   |   |   |   | x | x |   |   |   |   |   |   |



## FIFO

| Tempo | 0 | 1 | 10 | 15 | 20 | 25 |  |  |
|-------|---|---|----|----|----|---|---|---|
|  P1   | x | x |    |    |    |   |   |   |
|  P2   |   | x | x  |    |    |   |   |   |
|  P3   |   |   | x  |  x |    |   |   |   |
|  P4   |   |   |    |  x | x  |   |   |   |
|  P5   |   |   |    |    | x  | x |   |   |

### Tempo (FIFO):

P1: 5 - 0 = 5

P2: (10 - 1) = 9

P3: (15 - 2) = 13

P4: (20 - 3) = 17

P5: (25 - 4) = 21

Media: 13


### Tempo (Round Robin): 

P1: (21 - 0) = 21

P2: (22 - 1) = 21

P3: (23 - 2) = 21

P4: (24 - 3) = 21

P5: (25 - 4) = 21

Media: 21



### Uso de 1 Processos I/O Bound e 2 CPU Bound

| Processos    | 0 | 10 | 110 | 210 | 20 | 25 |   |   |
|--------------|---|----|-----|-----|----|----|---|---|
|  I/O Bound   | x | x  |     |     |    |    |   |   |
|  CPU Bound 1 |   | x  |  x  |     |    |    |   |   |
|  CPU Bound 2 |   |    |  x  |  x  |    |    |   |   |
|

### Implementação + Eficiente do Escalonamento c/ Prioridade


### Multiplas Filas de Processos Prontos 

### Escalonamento de Loteria

Vantagens: Diminui a chances de ter um starving muito
longo -> Isto pode também ser alcançado reorganizando as filas de processos prontos

Cada processo/thread recebe uma quantidade de bilhetes.

## Mais de um Core ou CPU 

### Linux

Para cada CPU, existe uma estrutura de Filas

### Windows

Assumindo 4 CPUs

CPU 1 e CPU 2 -> Estrutura de Filas

CPU 3 e CPU 4 -> Estrutura de Filas

Vantagens: Menor chances de ter uma estrutura vazia.
Contensão: Semáforo de Mutex, para impedir que uma CPU entre em uma estrutura que já está sendo usada por outra CPU