# Aula 6

## Aula passada
 - Término de Processos
 - Criação de Processos
    - Mudança de Prioridade 
    - Arquivos Iniciais
    - Diretório Corrente de um Processo 
    - Chamada fork no Linux: Sem parâmetro nenhum

    Upon successful completion, fork() returns 0 to the child process and returns the process ID of the child process to the parent process.  

    - Chamada de término do processo:
        - 0 -> Sem problema algum
        - Diferente de 0 -> Algum tipo de erro
    
    - Janela de espera: Pai esperar pelo filho


## Estágios de um Processo

3 Processos: p1, p2 e p3

 - Tempo T1: Inicialmente: Estado pronto para execução
 - Escalonamento de Processos: Escolha de qual processo vai ser o próximo 
 a ser executado.
 - Tempo T2: P2 começa a ser executado
 - Tempo T3: P2 vai para o Estado bloqueado -> Está esperando uma entrada do usuário
 que ainda não foi executada
 - Tempo T4: Como nenhum processo está sendo executado nesse instante, então é hora de 
 ser feito um novo escalonamento. Desta forma, escolhe-se o P3 para ir do estado de 
 "Pronto" para "Executando"
 - Tempo T5: P3 pede ao SO para receber um dado de outro processo. P3 também
 fica bloqueado se ainda não recebeu nenhum dado.
 - Tempo T6: Já que P2 e P3 continuam bloqueados, o SO escalona o processo P1. 
 Pronto -> Execução
 - Tempo T7: O usuário digita o dado que P2 estava esperando. Então P2 vai para
 o estado de "Pronto". 
 Digamos que P1 está executando um cálculo muito demorado. Então, todos
 os outros processos ficam esperando.
 - Tempo T8: P1 pode vir a "ceder a vez" para o processo P2. P1 então volta para o estado
 de "Pronto". -> (Multiprogramação Cooperativa)
 
 2) O SO ter um tempo limite para cada processo executar (Time-slice)
    O SO decide passar o P1 para pronto, pelo tempo de time-slice estourar.
    (Preempção)

    - Existe uma interrupção de relógio (que sempre acontece em intervalos fixos). Através disso,
    o SO sabe a quantidade de tempo que o processo está executando.

 - Tempo T9: SO escalona P2
 - Tempo T10: O Processo P3 passa de bloqueado para Pronto. Recebe dado.
 - Tempo T11: O processo P3 passa a ser executado, por ter  mais prioridade. -> Preempção 
 por prioridade.
 
Outras formas de bloqueio: Disco
Horas e Minutos -> Bloqueio por falta de atualização de tempo.
Processo espera pelo término de outro processo.

## PCB (Process Control Block)
   - Process 
   - Número do processo pai
   - Guardar os valores dos registradores (Só preechido quando o processo para)
   - Registrador que guarda o endereço da instrução que está sendo executada
   - Localização das áreas de memória do processo
   - Informações para o escalonamento do processo. Ex: Prioridade
   - Código de retorno do Processo
   - Diretório Raiz -> Mudança do diretório raiz (chroot)
   - Ponteiros para outros PCB

## Threads
Motivação: É muito custoso a chamada de SO.

A) Como consigo parar um calculo que um processo
que um processo está fazendo? 
(sem matar o processo)

B) Como consigo fazer uma consulta a um servidor
remoto e ao mesmo tempo interagir com o usuário?

### Possíveis soluções
    
 1) Solicitar ao SO conteúdo de várias "entradas" 
    diferentes. (Multiplexação de E/S)
    (SO trata o caso B) - Chamada select do Unix 

2) Chamadas de E/S Assíncronas -> Se o dado
    está disponível o SO retorna imediatamente dizendo 
    isso. (Resolve o caso A e B)

3) Uso de thread - Mesmo processo, porém diferentes pontos de execução. Além disso, se beneficia do fato da máquina
ter mais de uma CPU.


Exemplo sem Threads:
```c 
#include <stdio.h>

long int somaTudo() {
    int i;
    long soma = 0;
    for(i = 1; i == 10000000; i++) {
        soma = soma + i;
    }

    return soma;
}

int main() {
    printf("O resultado da soma é %l", somaTudo());
}
```

Exemplo com Threads:
```c
long soma[2];

void somaParte1() {
    int i;
    soma [0] = 0;
    for(int i = 0; i < 5000000; i++) {
        soma[0] = soma[0] + i;
    }
}

void somaParte2() {
    int i;
    soma[1] = 0;
    for(int i = 5000001; i <= 10000000; i++) {
        soma[1] = soma[1] + i;
    }
}

int main() {
    int t1, t2;

    pthread_create(&t1, NULL, somaParte1, NULL);
    pthread_create(&t2, NULL, somaParte2, NULL);

    pthread_join(t1, NULL); // Se não houver join, o programa termina e aborta as threads
    pthread_join(t2, NULL);

    printf("A soma é %l", soma[0] + soma[1]);
}
```

```c
    long soma[4];
    void somaParte(int qual) {
        int i, inicio, fim;

        long acc = 0;
        inicio = 1 + qual * 2500000; // Primeiro Loop = 0
        fim = (1 + qual) * 2500000;  // Primeiro Loop = 2500000

        for(i = inicio; i <= fim; i++) {
            acc = acc + i;
        }
        soma[qual] = acc;
    }

    int main() {
        int t1, t2, t3, t4;
        pthread_create(&t1, NULL, somaParte, 0);
        pthread_create(&t2, NULL, somaParte, 1);
        pthread_create(&t3, NULL, somaParte, 2);
        pthread_create(&t4, NULL, somaParte, 3);

        pthread_join(t1, NULL);
        pthread_join(t2, NULL);
        pthread_join(t3, NULL);
        pthread_join(t4, NULL);

        printf("A soma é %l", soma[0] + soma[1] + soma[2] + soma[3]);
    }

```

### Little OS Book
http://littleosbook.github.io/

