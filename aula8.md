# Aula 8

## Estrutura de Controle da Thread

Problema de Condição de Corrida 

Ex: Alteração simultânea de um saldo

- Seção (trecho) crítico
Ex:
```
Inicio do trecho critico
Trecho Crítico
Fim do trecho crítico
```
- Implementações de Seção Crítica:
    - Desabilitando Interrupções -> Não funciona am mais de 1 CPU. 
    Mesmo que eu desabilite as interrupções de todas as CPUs, o software é executado em paralelo naturalmente.
    - Solução por Software Complexa p/ mais de dois processos
    - Instruções Atômicas:
        - Ex: testAndSet, swap
        - Implementação de Spin Lock

    

## Semáforos
V - Up
P - Down

### Thread 1
```
DOWN (semaforo);

saldo = saldo + 100;

UP (semaforo);
```

### Thread 2
```
DOWN (semaforo);

saldo = saldo  + 300;

UP (semaforo);
```
Semaforo == 1

O Down decrementa o valor do semáforo para zero na Thread T1, e não bloqueia o processo.
Quando o Semaforo vai para zero, a outra thread T2 tenta acessar o trecho crítico, 
e como o valor é zero, esta thread fica bloqueada. Em seguida, depois que o TC da primeira
thread terminar de executar, o valor do semaforo é atualizado pelo UP, e a thread T2 é liberada.

### Implementação do Down

```
Down(s):
    s.valor = s.valor - 1;
    if(s.valor < 0) {
        BloqueiaThreadAtual();
    }
```

```
UP(s):
    s.valor = s.valor + 1;
    if(s.valor <= 0){ // Quer dizer que tem alguma thread bloqueada
        Desbloquear(t);
    }
```

Existe um outro campo, s.list, que trata-se de listas encadeadas de TCBs, 
registrando as threads que estão bloqueadas.
Você pode ter mais de 1 semáforo, já que se pode ter mais de um TC.
Cada semáforo da lista de semáforos, podem ter prioridades diferentes.
O semáforo é vantajoso em relação ao Spin Lock, porque ele tira de execução o processo que tenta acessar um TC que outra thread está utilizado.

Vantagem do Semáforos além de trechos críticos: Uso de recursos


### Problema de Produtor-Consumidor

  PA <- PB <- PC <- PB

  PA, PB, ..., PZ -> Produtos

  P1 e P2 -> Processos Produtores -> Colocam na Fila
  C1 e C2 -> Retiram da Fila

  Se o consumidor tentar tirar algo de uma lista vazia, é bloqueado até que o produtor 
  adicione um produto na fila.

  Eu posso colocar também os produtos em um array.

  O ponteiro inicio sempre aponta pro lugar que vou colocar um novo produto, e o 
  ponteiro fim, sempre vai ser o lugar que será retirado.

  Na implementação de Array não posso colocar novos elementos em uma lista cheia.

  Neste caso, o produtor ficará bloqueado, até que um consumidor tire alguém da fila.


### Implementação com 3 semáforos
  ```c
    // Código do Produtor
    // Cria um item (novo_item)
    // 3 Semáforos: Mutex (TC) - Binário, Vazio (Lista vazia) - Quantos espaços vazios, 
    // Cheio (Lista cheia) - Quantos espaços cheios

    down(vazio); // Se for zero, bloqueia o Produtor
    down(mutex); // Começar a mexer nas variáveis. Entra no TC 
    a[inicio] = novo_item;
    inicio = (inicio + 1) % 5;
    up(mutex); // Sai do TC
    up(cheio); // Adiciona +1 na quantidade de cheios
  ```


  ```c
    // Código do Consumidor

    down(cheio); // Bloqueia o Consumidor se a fila estiver cheia
    down(mutex);
    item = a[fim];
    fim  = (fim + 1) % 5;
    up(mutex);
    up(vazio); 
  ```


  ## Problema dos Filósofos

  Imagine uma mesma redonda vista de cima, onde cada filósofo sentado, precisa
  de dois garfos para se alimentar, sendo que existe somente 1 garfo entre dois
  filósofos adjacentes. 

  Link: (https://blog.pantuza.com/artigos/o-jantar-dos-filosofos-problema-de-sincronizacao-em-sistemas-operacionais)


  ```c
    Semaforo garfo[5];
    Filosofo(i) 
    while(true) {
        Pensa();
        Down(garfo[i]); // Baixo a disponibilidade do garfo
        Down(garfo[(i+1) % 5]); // Cada garfo guarda ou 1 ou 0 para sua disponibilidade
        Comer();
        UP(garfo[i]);
        UP(grafo[(i+1) % 5]);
    }
  ```

  Se todos os filósofos ao mesmo tempo, em 5 cores, pegarem seus garfos ao mesmo tempo,
  todos os filósofos conseguirão pegar o garfo direito, porém o lado esquerdo ninguém conseguirá.

  Esta situação tem o nome de Deadlock!

  Se duas threads com dois semáforos, se cada uma pega um semáforo, e depende do outro para continuar executando, você tem um deadlock. Já que cada uma espera a outra, ficando bloqueadas indefinidamente.

```c
  Filosofo(i)
  while (true) {
      Pensar();
      PegarOsGarfos(i);
      Comer();
      LargarOsGarfos(i);
  }


  PegarOsGarfos(i) {
      DOWN(mutex);
      estado[i] = Faminto; // Pensando, Comendo ou Faminto
      CofirmaSePodeComer(i);
      UP(mutex);
      DOWN(PodeComer[i]); // Esse Down controla se vou ser bloqueado se não posso comer. Ou se posso comer, caso o valor antigo seja 1.
  }


  ConfirmaSePodeComer(i) {
      
      // Verifica que os vizinhos não estão comendo
      // Só roda 1 filósofo nesse TC
      if(estado[i] == FAMINTO && 
         estado[esquerda_de(i)] != COMENDO && 
         estado[direita_de(i)] != COMENDO) {
             UP(PodeComer[i])

         }
  }
```

```c
LargarOsGarfos(i)
    DOWN(mutex);
    estado[i] = Pensando;
    ConfirmaSePodeComer(esquerda);
    ConfirmaSePodeComer(direita);
    UP(mutex);
```
1 semáforo só para cada filósofo. Com 1 semáforo, não há deadlock.

### Problema Escritor-Leitor

Você pode ter n leitores, e somente um único escritor.

## Monitor

  É um módulo de uma linguagem, onde cada rotina tem exclusão mútua. Somente 1 thread dentro do monitor. Se 1 thread chama 1 rotina do monitor, as outras ficarão bloqueadas ao acessar qualquer outra rotina do monitor.

  O monitor tem um semáforo mutex de exclusão mútua embutido.

  - Rotinas dentro do monitor que executam com exclusão mútua

  Ex Real: synchronized em Java.

  O monitor tem variáveis de condição.

  Rotinas para usar com variáveis de condição:

wait(c); -> C é a variável de condição

signal(c); -> Desbloqueia uma das threads que está bloqueada na condição C.

É como se o wait fosse o DOWN e o signal o UP.