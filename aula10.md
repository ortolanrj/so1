# Aula 10

## Resumo da última aula

- Monitores - Exclusão Mútua Implícita

- Variável de Controle

- Variação de Monitor

HOARE - Execução passa diretamente no processo
que recebeu o sinal

Sem regra de ordem de execução
(JAVA) - Necessário alterar o código do 
processo que recebe o sinal 

Troca de mensagens
  - Identificação Destinatário
    - Processo
    - Caixa Postal

  Unidade de Transmissão (Sim ou Não)
  
  Armazenamento de msg (Sim ou Não) 
  
  Crítica a troca de mensagem - Duplicação
  do gasto de memória, tempo gasto para cópia

  Solução: Copy on Write

## Deadlock

Quando ocorre: Processos (Threads) competem por
acesso a recursos limitados.

A sincronização de processos não está programada
corretamente

Definição: Um deadlock existe em um conjunto de 
processos (ou threads) QUANDO CADA PROCESSO está 
esperando por um evento que apenas pode ser gerado 
por outro processo no conjunto

Processo -> Recurso (O processo está esperando o recurso)

Recurso -> Processo (O processo está utilizando o recurso)

Garfo 0 -> Filosofo 0 -> Garfo 1 -> Filosofo 1 -> Garfo 0 


- Quatro condições necessárias para ocorrer um deadlock:
 - 1) Exclusão mútua no acesso ao recurso
 - 2) Hold and Wait - Um processo está acessando um recurso e 
 está esperando para acessar outro recurso que está sendo acessado por 
 outro processo
 - 3) Não preempção do recurso - O recurso só é liberado
 voluntariamente pelo processo.
 - 4) Espera Circular

- Quatro abordagens p/ o deadlock:
 - 1) Não fazer nada de forma automática
    Supondo que a probabilidade de ocorrência seja baixa. -> Utilizar uma intervenção manual.
 - 2) Prevenção: Eliminar uma das 4 condições
    - 2.1) Spool - Recurso virtual  - O processo utiliza um recurso virtualizado 
    achando que está usando um recurso verdadeiro.
    - 2.2) Eliminar o Hold and Wait - Solução: Um processo só pode pedir recursos,
    quando não está utilizandonenhum e precisa pegar os recursos todos de uma vez
      Crítica: Por ser ineficiente no uso dos recursos. Um recurso alocado a um processo
      pode passar um tempo razoável sem ser utilizado.

    - 2.3) Eliminar a não preempção do recurso
        Só pode ser usado se existir um mecanismo de 
        preservação/restauração do estado do recurso

    - 2.4) Eliminar a espera circular 
        Solução: Atribuir um número inteiro a cada tipo de recurso, e 
        eu só posso acessar o recurso numéricamente (Inverter a ordem do Filósofo 3 de pegar o garfo)


  - 3) Evitar deadlocks 
       O SO apenas deve permitir a alocação de um recurso quando ela 
        for segura.

        Estado seguro: Existe uma sequência em que todos 
        os processos terminam sem que ocorra deadlock

        Para o SO implementar o algoritmo de concessão de recursos
        
        O processo informa previamente a quantidade total de 
        recursos de cada tipo que ele vai precisar.


      Exemplo:  Um computador de 12 unidades de fita

      O SO não dá recursos para processos que possam vir a dar deadlock no futuro
      Super conservador -> Não existe uso prático

      Algoritmo do Banqueiro

  - 4) Detecção e resolução de deadlock
   
      O SO de tempos em tempos executa o algoritmo de detecção de deadlock,
      e caso exista um deadlock o SO tenta resolver, por exemplo, terminando um dos processos 
      envolvidos no deadlock.

      Pode causar estado inconsistente. Matar o processo não é bom.
      Ex: Matar um processo no meio da escrita de um arquivo. 

      Banco de Dados -> Guarda estados anteriores, para se um processo morrer, ele 
      restaura através de transactions


      Como detectar se existe deadlock? 

      Instâncias diferentes de um mesmo recurso -> Não vai a ver deadlock,
      se cada processo utilizar instâncias diferentes.


### Algoritmo de detecção de deadlock

Disponível: Vetor com um elemento para cada tipo de recurso

Alocação Atual: Matriz que as linhas são os processos
e as colunas são os tipos de recurso.

Work: Vetor temporário com um elemento pra cada tipo de recurso

Terminado: Vetor de booleano, um elemento 
para cada processo. Simula o término do processo.


