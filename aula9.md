# Aula 9

## Resumo última aula

[Slides UFPE Concorrencia](https://www.cin.ufpe.br/~jcbf/if677/2015-1/slides/Aula_06_Concorrencia.pdf)

[Java Semaphores](https://baptiste-wicht.com/posts/2010/08/java-concurrency-part-4-semaphores.html)

[Java Monitors](https://dzone.com/articles/java-concurrency-%E2%80%93-part-5)

## Semáforo - Operação Atômica

DOWN(s) ou P(s)
UP(s) ou D(s)

Solução do Problema Produtor Consumidor com Semáforos

Solução errada do Problema dos Filósofos com Semáforo

Solução do Problema dos Filósofos com Semáforo

Obs: O Spin Lock não bloqueia a thread, diferentemente do semáforo, assim, 
gastando mais CPU.

[Spin Lock](https://en.wikipedia.org/wiki/Spinlock)
    
5 semáforos por filósofo -> Se todos os filósofos ao mesmo tempo pegam 
todos os garfos da direita. -> Deadlock!

## Monitor

[Monitor](https://pt.wikipedia.org/wiki/Monitor_(concorr%C3%AAncia))

Evitar erros de programação, automatizando a implementação 
de trechos críticos (existe uma exclusão mútua implícita nas rotinas do monitor).

OBS.: O monitor só implementa o trecho crítico, porém não implementa a resolução 
de outros problemas como controlar a quantidade de recursos.
Desta forma, não posso utilizar um semáforo dentro de um monitor.
Se uma thread fizesse DOWN em um semáforo zerado, a thread continuaria bloqueada 
dentro do monitor, e nenhuma outra thread poderia entrar na rotina do monitor.

Então, como controlar recursos usando monitor?

Existe o conceito de variável de condiao que controla os recursos
dentro do monitor.

Duas rotinas para usar variáveis de condição.
  WAIT(var cond) -> Atomicamente libera o trecho crítico e 
  bloqueia a thread que chamou

  A thread que está sendo executada que faz WAIT para ela própria ser bloqueada 
  para outras threads poderem entrar no monitor

  SIGNAL(var cond) -> Libera uma thread bloqueada
  (nas variavel de condição) executar.

  BROADCAST(var cond) -> Libera para executar todas
  as threads bloqueadas nessa variável de condição


```c
const int TAM_MAX = 10;

Monitor ProdutorConsumidor {
    int in = 0, out = 0, qtd = 0; 

    int a[TAM_MAX];

    VariavelDeCondicao vazia, cheia;

    int get() {
        int item;
        if(qtd == 0) {
            wait(vazia); // Guardar a thread consumidora na fila de threads vazias
        }

        qtd = qtd - 1;
        item = a[out];
        out = (out + 1) % TAM_MAX;
        if(qtd == TAM_MAX -1) {
            signal(cheia);
        }
    }

    void Put(int item) {
        if(qnt == TAM_MAX) {
            wait(cheio);
        }

        qtd = qtd + 1;

        a[in] = item;
        in = (in + 1) % TAM_MAX;
        if(qnt == 1) {
            signal(vazia);
        }
    }

}
```

## Problemas de Execução

T1 é consumidor e faz wait, e a execução continua na T2 que é produtora. Porém, imagine que T3
é uma terceira thread consumidora que rode e depois T1 que também é consumidora rode em seguida.
A quantidade vai ficar -1. O problema é que T1 já passou do ponto de wait(vazia) do código, então se ela 
não fosse executada em seguida após T2, temos um problema. Do contrário, se a ordem de execução fosse
T1 -> T2 -> T1 -> T3, não haveria problema de quantidade negativa.

Executar imediatamente a thread que recebeu o sinal (T1) e bloquear
a thread que gerou o sinal(T2).

Solução: Ao invés de if, usar while

Antes:
```c
if(qtd == 0)

if(qtd == TAM_MAX)
```

Depois:
```c
while(qtd == 0)

while(qtd == TAM_MAX)
```

T3 volta com a quantidade zero para o início do while, sem sair do loop.

### Diferenças entre Semáforo e Variável de Condição

- Semáforo guarda o passado, variável de condição não
- As operações de UP e DOWN (em um semáforo) são comutativas, variável de 
condição não.
- Wait em variável de condição sempre bloqueia a thread. O semáforo não, depende 
do valor do semáforo. 
- Variáveis de condição PRECISAM estar dentro de um trecho crítico

## Troca de mensagens

Envio de mensagens

Envio de um conteúdo de um processo para outro

Um processo faz: 
  - send(identificador, mensagem)
Outro processo faz:
  - receive(mensagem)

Tendo ou não armazenamento:

Se o processo 1 faz receive antes de outro processo
fazer send, o processo 1 é bloqueado.

Armazenamento
Sim:  O SO armazena temporariamente msgs enviadas 
      mas ainda não recebidas. Se o SO for capaz de 
      armazenar mais de uma msg então há o enfileiramento de 
      msgs.

Não: Não tendo armazenamento -> Se o processo 2 faz send antes
do outro processo fazer receive, o processo 2 é bloqueado.

Identificador (do Send)
 - Identificador do Processo
 - Identificador de um Terceiro (Caixa Postal) => Vantagem: Mais de 1 processo
 pode ter acesso as mensagens

Existe ou não uma unidade de comunicação:

Sim: O recebimento e envio é sempre de 
uma mensagem.

Não: O envio é de alguns bytes e o recebimeto
é de alguns bytes.

No Unix, o mais comum é utilizar chamadas ao SO que não tem 
conceito de unidade:
 - Pipes: Processos que se comunicam devem ser parentes. -> Unidirecional
 - Pipes Nomeadas: Processos que se comunicam não precisam ser parentes. -> Tipicamente unidirecional
 - Sockets -> Bidirecional

 Memória compartilhada é mais rápida que troca de mensagens

 MacOS - OS/X (Baseado no Mach)

 A troca de mensagens do Mach, tipicamente, NÃO faz cópia de mensagens. 

  ### Copy On Write

  A mensagem não é copiada de fato, somente quando um dos processos 
  tenta alterar o conteúdo da mensagem.
