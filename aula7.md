# Aula 7

## Aula passada

Estados de um processo: Executando, Bloqueado, Pronto

Process Control Block  (PCB)

Troca de Contexto

Threads 
 - Criação, Término
 - Espera pelo término


## PCB
- Número do Processo (PID) (P)
- Número do Processo Pai   (P)
- Valor dos Registadores   (T) -> Troca de Contexto é entre threads. Porém, no Linux antigamente,
não existia o conceito de threads, então a troca de contexto era entre processos.
- Estado da Thread         (T)
- Localização das Áreas de Memória do Processo (P)
- Informações p/ Escalonamento (Por Exemplo: Prioridade) (T)
- Informações sobre Recursos Externos sendo utilizados (P)
- Código de Retorno (P)
- Diretório Atual   (P)
- Diretório Raiz    (P)

O Cálculo na aula passada poderia ser feito entre processos.

Vai existir uma estrutura de controle para cada thread também, 
assim como o Processo.

1 processo pode ter n threads
Porém no Linux, o PCB inteiro é por thread. Aquilo que é comum em duas threads (Ex: Diretorio Raiz,
Numero do Processo, etc),
fica em uma área externa.

Quando duas threads acessam a mesma variável ao mesmo tempo,
pode vir a existir problemas:

Thread 1 

saldo = saldo + 100

Thread 2

saldo = saldo + 300

Se o saldo inicial fosse 500, espera-se que depois das 2 threads rodarem, o saldo final fosse 900.

```as
mov eax, [5000]
add eax, 100
mov [5000], eax
```

Mesmo que duas threads estejam usando o mesmo registrador, ainda podemos ter problemas,
porque na troca de contexto entre threads, o valor antigo do registrador é restaurado.

Condição de Corrida (Race Condition): Região de memória compartilhada entre threads.

## Como evitar que Race Conditions aconteçam?

Conceito de Seção ou Trecho Crítico

Existem partes do código que não podem executar ao mesmo tempo.
Ex: "saldo = saldo + 100;" e "saldo = saldo + 300;"

  - Apenas um processo (ou thread) pode executar em um 
trecho crítico
  (Exclusão Mútua) 
  - Um processo (ou thread) fora do seu
  trecho crítico não pode impedir que
  outro processo (ou thread) entre no seu 
  trecho crítico (processo)
  - Nenhum processo (ou thread) pode esperar um tempo arbitrariamente longo p/ entrar no
  seu trecho crítico (Espera limitada)

  Soluções:
   - Desabilitar as interrupções (Disable Interruptions on ASM): 
      Exemplo: 

        ### Thread 1:

        Desabilitar as Interrupções
        saldo = saldo + 100;
        Habilitar as interrupções

        ### Thread 2:

        Desabilitar as Interrupções
        saldo = saldo + 300;
        Habilitar as interrupções
    
Porque essa solução é inviável? 
  - Pedir permissão privilegiada ao Núcleo
  - Não tem como impedir as interrupções em um processador com mais de 1 CPU.
  

Possíveis soluções:

Solução ingênua:

  Thread 1 
    
```c
    while (vez != 1)     // Inicio do TC
    saldo = saldo + 100; // Trecho critico
    vez = 2;             // Fim do TC
```

  Thread 2
  
```c
    while (vez != 2)     // Inicio do TC
    saldo = saldo + 300; // Trecho critico
    vez = 1;             // Fim do TC
```

Problema: Eu não posso executar uma mesma thread 2 vezes seguidas.

Solução Genérica:

O vetor interessado se refere ao interesse da thread de entrar no trecho crítico. 

Thread 1

```c
    interessado[1] = true;
    vez = 2;

    while(interessado[2] && vez == 2);

    saldo = saldo + 100;
    interessado[1] = false;
```

Thread 2

```c
    interessado[2] = true;
    vez = 1;

    while(interessado[1] && vez == 1);

    saldo = saldo + 300;
    interessado[2] = false;
```

Limitação: A implementação não é muito boa para muitas threads

### Instrução Atômica

Instrução em Linguagem de Máquina: 

1) Test and Set
2) Swap

```as
    tsl eax, [2000]
```
(Na realidade a CPU Intel não tem test and set, ela tem swap)


Implementação em C:

```c
int testAndSet (int p_valor) {
    int ret = *p_valor;
    *p_valor = true;
    return ret;
}
```
p_valor indica se está no trecho crítico ou não

Thread 1

```c
    while(testAndSet(&lock)); // Inicio do TC
        /* não faz nada */

    saldo = saldo + 100;

    lock = false; // Fim do TC
```

Thread 2

```c
    while(testAndSet(&lock));

    saldo = saldo + 300;

    lock = false;
```

O TSL atualiza o valor na memória, e depois atualiza o valor antigo no registrador


Todas as soluções acima tem um problema:
Gasto de CPU executando o loop, enquanto a thread não entra no trecho crítico.

Mecanismo de Loop: Spin Lock