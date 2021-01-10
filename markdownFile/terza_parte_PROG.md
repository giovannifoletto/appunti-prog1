## Programmazione Ricorsiva

da recuperare appunti dal tablet



## Allocazione dinamica

L'allocazione normalmente avviene in maniera statica, ovvero conosco la dimensione in partenza e con questa informazione creo uno spazio di memoria che possa contenere queste informazioni. Questo limite nella quantità però può essere un limite, ad esempio in una variabile che deve gestire un input: 

* se ho una variabile troppo piccola c'è il rischio di *overflow*, ovvero di riempirla troppo e in caso causare un crash del programma o un comportamento anomalo
* se ho una variabile troppo grande rischio di occupare un sacco di spazio per niente, togliendolo invece a altri processi a cui potrebbe servire.

Questo quindi riporta il problema alle capacità del programmatore, che dovevano essere molto bravi a stimare la quantità di memoria corretta. Ma in realtà per risolvere questo problema in un modo molto più elegante si può usare la programmazione con tipi di dato a memoria dinamica, ovvero creare oggetti che non possono essere descritti in modo statico dal programma. Questo può servire anche nel caso la variabile sia vuota, e quindi ci sia la necessità di liberare completamente lo spazio.

Nel C queste funzioni sono presenti nella `<stdlib.h>`, una libreria che contiene la strategia per un allocazione e de-allocazione della memoria dinamica. Si interfaccia con le risorse del sistema operativo e si occupa della richiesta e del rilascio della memoria.

Ricordo che di solito le variabili vengono allocate all'inizio del programma e viene de-allocata alla fine dell'esecuzione del programma. Tutte queste operazioni, insieme ai record di attivazione sono presenti nello stack, ovvero quella struttura che si occupa della gestione delle funzioni.

Nel C questa libreria ha una funzione nativa che si occupa dell'allocazione dinamica:

```c
void * malloc(size_t size); // Header della funzione malloc
```

In cui il parametro formale è di tipo `size_t` che praticamente è un `unsigned int`. La funzione torna un puntatore alla sezione di memoria assegnata, ma questo puntatore è sprovvisto di tipo, che va quindi assegnato al momento del ritorno della funzione attraverso un casting.

Il suo funzionamento è il seguente:

```c
#include <stdlib.h>

int main(){
    
	int *P; // Definisco un puntatore a intero
	
    P = malloc(sizeof(int));
}
```

L'operazione che si sta eseguendo è di *portabilità*, infatti così facendo si può definire la quantità di byte per il tipo *int* su ogni macchina che il codice viene eseguito, rendendo il codice stesso molto flessibile. 

Nell'esempio bisogna notare che: 

1. viene lanciata la funzione `malloc` che si occupa di creare uno spazio di memoria della dimensione passata, in questo caso la dimensione è `sizeof(int)`. 
2. La funzione `sizeof()` restituisce il valore intero di byte che un certo tipo di dato necessita. Può sembrare inutile nel caso di un valore *int*, di cui è conosciuta la dimensione, ma risulta molto comoda quando abbiamo un tipo di dato user-defined. 
3. La funzione `malloc` torna un indirizzo alla posizione di memoria in cui è stato creato lo spazio per la variabile. È molto importante che il puntatore sia definito dello stesso tipo di dato di cui si sta creando lo spazio di memoria, in modo che il compilatore riesca a "tradurre" quello che è contenuto in quell'indirizzo.

Potrebbe succedere che questa assegnazione di memoria non vada a buon fine, e quindi devo verificare che il puntatore non torni *NULL*. Se si trova il valore nullo del puntatore però si sa che non si è assegnata la memoria, ma non se ne conosce il motivo.

Dopo queste operazioni, però bisogna ricordare di rilasciare la memoria quando si smette di usarla. Se non si rilascia una zona di memoria non avvengono errori di sintassi, però potrebbe succedere che il sistema operativo si rifiuti di fornirci altra memoria poiché ne abbiamo troppa allocata e inutilizzata. Per eseguire questa operazione si esegue:

```c
TipoPuntatore* P;
free(P);
```

In cui il puntatore è l'indirizzo alla zona di memoria che è stata precedentemente allocata. Per questo è importante non perdere mai l'indirizzo originale della zona di memoria creata, in modo che si possa sempre de-allocare lo spazio.

### Gestione della memoria con l'allocazione dinamica

Sappiamo che generalmente le funzioni lavorano sulla struttura dello **stack**, che contiene le variabili delle funzioni create staticamente all'interno delle funzioni e i record di attivazione. Questo spazio viene poi anche liberato automaticamente al momento della fine della funzione, oppure alla fine del programma.

Quando invece si dichiarano tipi di dato in modo dinamico, il compilatore ci assegna un tipo di memoria diversa chiamato **heap**, cioè mucchio. Nello *stack* quindi compariranno dei puntatori, che non saranno altro che gli indirizzi della memoria allocata dinamicamente. Quest'ultima invece è posizionata nell'*heap*. La mappa della memoria usata nel nostro programma quindi ora prende questa forma. 

(L'esempio rappresenta lo *stack* con i vari indirizzi che puntano alla memoria dinamica posizionata nell'*heap*, che a sua volta può contenere un puntatore a altre zone allocate dinamicamente)

![image-20210103130905695](C:\Users\giova\Documents\1_UNI\programmazione1\appunti\appunti-prog1\image\image-20210103130905695.png)

Come si vede anche dalle foto, mentre lo *stack* è molto ordinato, l'*heap* non è così uniforme perchè contiene dei blocchi di memoria di grandezza diversa e generalmente i blocchi a disposizione gestiti dall'allocazione dinamica *non* sono in generale contigui. 

La gestione della memoria nell'heap è molto complicata, perché bisogna tener conto in un registro che zone di memoria sono occupate e quali sono libere, oltre che segnalare il contenuto in quelle che sono piene. Questo è importante perché il compilatore ha bisogno di sapere quali zone sono libere o occupate in un certo momento per potere gestire il rilascio della memoria.

Siccome la gestione dello spazio disponibile e quello occupato nell'heap è molto complicato, si utilizza una struttura di dati chiamata *lista*. All'interno di questa lista sono presenti tutte le informazioni che sono:  le dimensioni dello spazio disponibile, le informazioni necessarie ad aggiornare il registro delle zone libere e di quelle occupate e un puntatore alla prossima zona libera. 

I blocchi, cioè gli elementi della lista, sono tenuti in ordine crescente di indirizzo e l'ultimo blocco punta al primo, crea quindi una *lista circolare*. 

Per capire il funzionamento e il motivo di questa implementazione per la gestione della memoria dell'heap, si deve spiegare il funzionamento della `malloc`. Quando questa funzione viene lanciata, il compilatore inizia a cercare una zona libera di memoria a partire dal puntatore che rappresenta l'inizio degli indirizzi a disposizione dell'heap (elemento 1). Da questo si scandaglia la lista finché non si trova uno spazio disponibile, e quando lo si trova si aggiorna il registro. Se nel corso di questa ricerca il puntatore torna uguale a quello inziale, cosa possibile siccome la lista è circolare, vuol dire che non sono presenti zone di memoria libere nel range, e quindi si tornerà un errore.

![image-20210103234028124](C:\Users\giova\Documents\1_UNI\programmazione1\appunti\appunti-prog1\image\image-20210103234028124.png)

In generale quindi la memoria necessaria al funzionamento del programma è concettualmente semplificabile in questo modo: 

![image-20210103234832110](C:\Users\giova\Documents\1_UNI\programmazione1\appunti\appunti-prog1\image\image-20210103234832110.png)

Dove il *codice C* contiene le istruzioni in parte alle *variabili Globali*, che sono visibili a tutto il programma. Poi nello *stack*, si posiziona al gradino più basso sempre la funzione *main*, e seguono poi tutti le altre contenenti anche tutti i record di attivazione necessari a quella determinata funzione. Infine la zona dell'heap. Questo stack può aumentare l'altezza nel programma, analogamente l'heap, che può anche lui cambiare dimensione (questo è il motivo per cui c'è la freccia gialla).

Si ricorda poi che è possibile dichiarare la sezione di memoria dinamica allocata come variabile *static* e quindi evitare che questa determinata zona di memoria venga creata tutte le volte che si entra nella funzione che la richiede, ma al contrario, sia creata alla partenza del programma e poi utilizzata per tutto il suo svolgimento. Questo può essere utile se si vuole evitare di dichiarare molte volte zone di memoria molto grandi.



### Rischi nella gestione dinamica della memoria

Tra i maggiori rischi dell'uso di programmazione con uso della memoria dinamica è la produzione di *garbage*. La parola garbage è esemplificativa del fatto che si produce della memoria che non viene utilizzata, ma che è rimasta allocata. Questo avviene spesso quando si perde il puntatore alla zona di memoria dinamicamente allocata. Il *garbage* quindi viene creato semplicemente per opera del programmatore, che compie degli sbagli al momento dell'assegnazione e del rilascio di memoria dinamica.  

Un altro errore piuttosto comune è il *dangling reference*, cioè un *assegnazione fluttuante*. Succede ad esempio quando un luogo di memoria è indicato da due puntatori e si esegue una `free` per liberare la zona con uno dei due. Il problema che eseguendo la funzione *solo* un puntatore verrà eliminato, mentre il suo gemello si mantiene e adesso indica una zona di memoria vuota, perché è stata de-allocata. 

Questo può avere lati negativi sulla correttezza del risultato aspettato, e alcuni linguaggi hanno tolto questa funzionalità. 



#### Garbage Collection

Il problema dei *dangling reference* è facilmente risolvibile attraverso una tecnica chiamata **garbage collection**, che consiste nella raccolta di zone di memoria non referenziate. Per il C/C++ esistono librerie ad-hoc. 

Altri linguaggi di alto livello come LISP o JAVA hanno un sistema di garbage collection nativamente.

Ci sono anche altre tecniche per risolvere questo problema, come l'*Automatic Reference Counting* o *ARC*. Questo modo di fare è utilizzato ad esempio nell'Objective-C e nel Swift (linguaggi ecosistema Apple). L'ARC funziona in modo che tiene conto degli oggetti di memoria referenziati, e quando navigando la lista ne trovano uno non referenziato cambiano l'indicazione da occupato a libero.



### Allocazione dinamica nel C++

L'operatore che crea un blocco di memoria dinamica nel C++ è la funzione `new`, che general dinamicamente una variabile di un certo tipo assegnandole un blocco di memoria della dimensione opportuna e restituisce il puntatore che contiene l'indirizzo al blocco appena creato.

La sintassi completa è:

```c++
tipoPuntatore *ptr = new tipoPuntatore;
tipoPuntatore *ptr = new tipoPuntatore[3];

// ad esempio per creare lo spazio dinamico di 100 caratteri:

char* p = new char[100];

// Per liberare la memoria nel C++ si utilizza (singolo pointer o array di elementi)
delete ptr;
delete [] ptr; 
```



## Tipi di Dato Astratto

Come abbiamo visto i tipi di dato sono l'insieme di valori di una variabile. 

L'*Abstract Data Type* è un modello matematico che include le operazioni definite nel modello assieme al tipo di dato. Il concetto di *ADT* esula dal linguaggio di programmazione perché sono cose che possono essere implementate in ogni linguaggio. Gli *ADT* invece hanno la necessità di usare strutture proprie di un linguaggio di programmazione per essere creati, di conseguenza la loro implementazione è differente.



### Liste concatenate

Se bisognava gestire una sequenza di valori in ingresso fin ad ora l'unico modo per rappresentarli era l'utilizzo di un array. Nel caso però che ci si chieda un insieme dinamico di elementi, però, il concetto di array non funziona perché non riesce a gestire un insieme dinamico di elementi.

Ma adesso possiamo anche usare le **liste concatenate** come soluzione alternativa per creare un insieme di elementi *quando l'accesso casuale non è un requisito necessario*.

Nella lista concatenata i blocchi che la compongono possono anche essere in zone di memoria distanti, perché sono allocati nell'heap. E già qui ci si distanzia dal concetto di base dell'array, che consiste in una serie di spazi tutti consecutivi. 

Per implementare una coda è necessario che ogni elemento contenga almeno le informazioni per accedere all'elemento successivo, ma nel caso di una lista doppiamente concatenata, l'oggetto contiene anche il link all'elemento precedente.



#### Vantaggi e svantaggi rispetto all'array

|                           VANTAGGI                           |                          SVANTAGGI                           |
| :----------------------------------------------------------: | :----------------------------------------------------------: |
|                   Flessibilità di modifica                   | Onerosità dell'accesso agli elementi, cioè ho un unico modo per accedere a un determinato elemento e questo modo è di scorrere tutta la lista |
|        Riduzione e aumento dinamico delle dimensioni         |   Non ho un accesso casuale ai dati (come invece l'array)    |
| Memorizzo solo quello che serve, non ho bisogno di sovradimensionare e non rischio di sottodimensionare |                                                              |



#### Modello matematico dietro le *liste concatenate*

La lista è una sequenza di zero o più elementi di un determinato tipo di dato, anche strutturato se necessario.

Il numero di elementi (*n*) di tale lista è detta *lunghezza*. Se `n >= 1`, *a1* è il primo elemento dell'insieme. Se `n=0` la lista è vuota. La lista procede in ordine lineare, quindi *a1* viene prima di *a2* e così via. Inoltre l'accesso è sequenziale quindi da un elemento posso passare solo al suo successivo o precedente, non posso "saltare". Essendo un insieme dinamico posso partire con zero elementi al suo interno e poi farla crescere o decrescere. 

E' utile definire l'esistenza di un blocco *FINE*. Creandolo, la *distanza* è il numero di elementi tra il primo blocco e il blocco *FINE*, e varierà nel tempo.



#### Implementazione

L'implementazione in C necessita dell'utilizzo di una variabile strutturata:

```c
typedef struct Telemento{
	Tipoelemento info;
	struct EL *Prossimo;
}Telemento;
```

All'interno di questa struttura dobbiamo notare la struttura *autoreferenziata* `struct EL * Prossimo` che ci permette di raggiungere il prossimo elemento della lista. Questa definizione è una forma di ricorsione. Quando si alloca l'oggetto il calcolo dello spazio necessario però non cade in un loop infinito perché gli elementi sono concatenati attraverso un puntatore a un elemento, non l'intero elemento e quindi possiede un valore di memoria fisso. 

Il campo `struct EL *Prossimo` è chiamato link e viene usato per legare una struttura di `EL` a una struttura dello stesso tipo. Inoltre questo puntatore è necessario a contenere le informazioni necessarie per passare al prossimo elemento. 

La rappresentazione di una lista quindi può essere una cosa del tipo: 

![image-20201130102437198](C:\Users\giova\Documents\1_UNI\programmazione1\appunti\appunti-prog1\image\image-20201130102437198.png)

Nella rappresentazione il simbolo della messa a terra segnala l'ultimo elemento della lista, mentre il primo è chiamato *testa*. L'ultimo puntatore viene inizializzato a `NULL` e serve a indicare che siamo arrivati alla fine della lista. La *testa* della lista, che può essere anche `NULL` se la lista è vuota, di solito contiene il puntatore al primo elemento della lista. Per trovare l'ultimo elemento quindi bisognerà scorrere la lista finché non si raggiunge un elemento con `prox == NULL`.



Dopo aver definito il tipo di dato necessario a creare un blocco della lista, attraverso i `typedef` creiamo degli alias che potranno tornarci utili nella scrittura del programma. (Uno che indica il singolo blocco della lista e l'altro che indica l'elemento *testa* della lista)

```c
typedef struct EL ELemLista;
typedef ElemLista *ListaDiElem; 
```

Per dichiarare la lista poi si può fare:

```c
ListadiElem Lista1, Lista2, Lista3; // Dichiarazione standard

ElemLista *Lista1; 					// Dichiarazione abbreviata

struct EL *Lista1; 					// Dichiarazione con NomeTipo completo
```

La dichiarazione standard mette in evidenza che si sta dichiarando una lista, la dichiarazione abbreviata mette in evidenza il tipo di blocchi che conterrà la lista, mentre invece la dichiarazione con il nome del tipo di dato completo permette di indicare esplicitamente il tipo dei suoi elementi, senza ricorrere a un `typedef` (che però è molto comodo nell'uso).



Per inizializzare una lista si deve creare il primo elemento, ovvero la *testa della lista* e assegnarci il valore `NULL`.

```c
ListaDiElem Lista;
Lista = NULL; 
```

Facendo questo si viene a creare un oggetto rappresentabile in questo modo:

![image-20210104114122777](C:\Users\giova\Documents\1_UNI\programmazione1\appunti\appunti-prog1\image\image-20210104114122777.png)

Un altro modo di inizializzare la lista è dichiarare una testa in modo globale e creare una funzione apposita per crearla. *L'OPERAZIONE DI ACCESSO ATTRAVERSO UNA VARIABILE GLOBALE è SCONSIGLIATO*:

```c
#include <stdlib.h>
ListaDiElem Lista; // dichiaro globalmente
void Inizializza(void){
    Lista = NULL;
}
```

Un metodo migliore per eseguire questa operazione, è attraverso un *doppio puntatore*, ovvero un puntatore all'indirizzo della testa della lista, che devo inizializzare a puntare a *NULL*. 

```c
void initializza(ListaDiElem *ListaPtr){
    *ListaPtr = NULL;    
}
```

Il concetto di come lavora questa funzione è rappresentabile con:

![](C:\Users\giova\Documents\1_UNI\programmazione1\appunti\appunti-prog1\image\image-20210104114814838.png)

Dopodichè, ho bisogno di una serie di funzioni per la corretta implementazione di questo *ADT*. Queste funzioni sono:

* la funzione per il controllo se la lista è vuota

  ```c
  bool IsListaVuota(ListaDiElem Lista){
      
      if (Lista == NULL) return true;
      else return false;
  }
  
  // o anche
  
  bool IsListaVuota(ListaDiElem Lista){
      return (Lista == NULL);
  }
  ```

  * La funzione passa l'elemento per copia, siccome è necessario controllare solo il primo elemento della lista, che è un valore finito
  * Il secondo metodo implica il fatto che la funzione abbia bisogno di far tornare un valore di tipo `bool` e quindi esegue un casting dopo aver eseguito il controllo.

* Funzione di ricerca di un elemento

  ```c
  bool Ricerca(ListaDiElem Lista, TipoElemento ElemCercato){
  
      ElemLista *Cursore;
      if (Lista != NULL){
      	cursore = Lista;
     		
          while (cursore != NULL){
          	
              if (cursore->info == ELemCercato) return True;
              cursore = cursore->prox;
              
      	}
      }
      return false; 
  }
  ```

  * dichiaro un elemento `Cursore` che è un oggetto dello stesso tipo del blocco della lista e servirà per spazzolare tutti gli elementi di una lista
  * con il controllo `Lista != NULL` controllo di non essere arrivato all'ultimo elemento
  * nel ciclo `while` controllo se il mio elemento è quello cercato, se non lo è passo all'elemento successivo. Se trovo l'elemento cercato ritorno `true`. (quindi questa funzione permette di trovare solo il primo elemento, non calcola la possibilità di avere una ripetizione)
  * se `return false`  vuol dire che ho passato tutta la lista fino all'elemento che punta a `NULL` e non ho trovato nulla
  * *Questo algoritmo è molto influenzabile dalla quantità di elementi che sono presenti nella lista*

* Funzione di ricerca attraverso la ricorsione:

  ```c
  bool RicercaRecursiva(ListaDiElem Lista, TipoElemento ElementoCercato){
      
      if (Lista == Null) return false;
      else
          if (Lista->info == ELemCercato)
              return true;
      	else
              return RicercaRicorsiva(Lista->Prox, ElemCercato);
  }
  ```

  * `Lista == NULL` è per controllare di non essere arrivati all'ultimo elemento della lista, e questo passaggio è anche il *passo base* della funzione ricorsiva
  * Cerco l'elemento che voglio trovare con `Lista->info == ElemCercato ` , se si trova torna `true`, mentre se non si trova si rilancia la funzione in maniera ricorsiva sul prossimo elemento
  * Il numero di confronti con la funzione di ricerca non ricorsiva è lo stesso, ma il record di attivazione è molto maggiore, e aumenta di molto quando l'elemento cercato è molto distante dalla testa.
  * si può fare anche un'implementazione con una funzione che torni l'elemento cercato quando viene trovato

* Due funzioni minori sono quella che ritorna il valore del primo elemento, e quella che torna il valore dell'ultimo elemento:

  ```c
  TipoElemento TestaLista(ListaDiElem Lista){
      if IsListaVuota(Lista) return TipoElemento();
      return Lista->prossimo->info;
  }
  
  ListaDiElem CodaLista(ListaDIElem Lista){
      if IsListaVuota(Lista) return NULL;
      return Lista->prossimo->prossimo;
  }
  ```

  * In `TestaLista` controllo se la lista non è vuota, in caso torno un elemento vuoto (usando un costruttore), invece se non è vuota, torno il campo info del primo valore
  * `CodaLista` ritorna il valore `NULL`se la lista è vuota, mentre torna il valore `prossimo` del primo elemento della lista, cioè il secondo elemento della lista.

* La funzione inserimento in testa è particolare delle lista, perché impiega sempre lo stesso tempo a essere eseguita:

  ```c
  void InsiementoInTesta(ListaDiElem *Lista, TipoElemento Elem){
  
      ElemLista *Punt;
      Punt = malloc(sizeof(ElemLista));
      Punt->info = Elem;
      
      Punt->prox = Elem;
      *Lista = Punt;
  	
  }
  ```

  * il funzionamento di base è che si crea un elemento nuovo, si impone il campo `prossimo` come il primo elemento della lista e si impone il puntatore a testa al primo elemento della lista.
  * L'operazione ha un *costo unitario e costante* per cui è un azione da prediligere

* Funzione inserimento in coda:

  ```c
  void InserisciInCoda(ListaDiElem *Lista, TipoElemento Elem){
     
      ElemLista *Ptr;
      
      if (ListaVuota (*Lista)){
          Ptr = malloc(sizeof(ElemLista));
          Ptr->Prox = NULL;
          Ptr->Info = Elem;
          *Lista = Ptr;
      }
      else InserisciInCoda(&((*Lista)->Prox), Elem);
  }
  ```

  * la funzione lavora in modo ricorsivo finché non arrivo alla funzione di base, cioè finché non arrivo all'ultimo elemento
  * a questo punto recupero l'elemento precedente, e impongo il suo campo `prossimo` come indirizzo del nuovo elemento, e il campo `prossimo` del nuovo elemento come puntatore a `NULL`
  * L'inserimento in coda ha un costo di risorse e di tempo variabile in base al numero di elementi della lista, che si contrappone al costo unitario dell'inserimento in testa

* Funzione inserisci in lista ordinata (dove sto inserendo i valori in modo che rispettino una relazione d'ordine tra di loro):

  ```c
  void InserisciInOrdine(ListaDiElem * Lista, TipoElemento Elem){
      
      ElemLista *Punt, *PuntCorrente, *PuntPrecedente;
      PuntPrecedente = NULL;
      PuntCorrente=*Lista;
      
      while ((PuntoCorrente != NULL) && (Elem > PuntoCorrente->Info)){
          PuntoPrecedente = PuntoCorrente;
          PuntoCorrente = PuntoCorrente->Prox;
      }
      
      Punt = malloc(sizeof(ElemLista));
      Punt->Prox = PuntCorrente;
      Punt->Info = Elem;
      
      if (PuntoPcedente != NULL) PuntoPrecedente->Prox = Punt;
      	else *Lista=Punt;
  }
  ```

  * attraverso due variabili di servizio tengo conto dell'elemento corrente e del successivo. Controllo che il mio valore non sia compreso tra questi due rispetto alla mia relazione d'ordine: se questo è vero allora si inserisce  l'elemento, se invece risulta falso allora si scorre ancora la lista
  * la cosa più difficile di questa funzione è mantenere l'ordine, senza perdere link tra gli elementi e senza scombinare un ordine che preesisteva

* Altre funzioni per l'implementazione delle lista che possono tornare utili sono ad esempio:

  * `StampaDiTuttaLaLista`, che si occupa di stampare tutti i valori `info` degli elementi che compongono la lista
  * `EliminaElemLista`, che si occupa di eliminare un elemento all'interno della lista, che può essere all'inizio, in coda o in mezzo. Per implementare questa funzione:
    * si trova l'elemento da eliminare
    * si stacca il puntatore precedente e lo si ricollega al blocco successivo a quello trovato
    * si esegue un operazione di `free(Elemento)` 
  * funzione per il calcolo della lunghezza della lista
  * funzione per rovesciare l'ordine della lista
  * funzione per concatenare due liste



#### Estensione al concetto di lista: *Liste doppiamente concatenate*

Le *liste doppiamente concatenate* hanno due puntatori, uno che punta al `prossimo` elemento e uno che punta al `precedente`.

```c
struct EL{
	tipoelemento Info;
	struct EL *Prox;
	struct EL *Prec;
}; 

typedef struct EL ElemLista;
typedef ElemLista *Lista;
```

Questa implementazione ha il costo di essere più grande, perché occupa più spazio in memoria, però ha il lato positivo di facilitare l'inserimento e la cancellazione degli elementi. 

Nelle liste doppiamente concatenate si possono eseguire tutte le funzioni delle liste semplicemente concatenate, però bisogna ricordare sempre dell'aggiornamento e la gestione de secondo puntatore.



#### Estensione al concetto di lista: *Liste circolari*

Questa struttura è molto importante, tanto che viene usata per gestire il *registro dell'heap*. Questa struttura ha come testa il primo elemento, e nessun elemento punta a `NULL`, perché l'ultimo elemento punta di nuovo alla testa.

```c
struct EL{
    Tipoelemento Info;
    struct EL *prox;
}

typedef struct EL ElemLista;
```



#### Sommario: *LE LISTE*

Le liste sono un importante esempio di struttura dinamica che nel C viene implementata attraverso puntatori (quindi un implementazione di basso livello). Possono essere singolarmente o doppiamente concatenate, ordinate o meno, circolari. Hanno una serie di aspetti positivi e negativi:

|                           VANTAGGI                           |                          SVANTAGGI                           |
| :----------------------------------------------------------: | :----------------------------------------------------------: |
| efficienza della struttura dinamica della lista rispetto all'array: si evita lo spreco di memoria e anche l'*overflow*, ovvero l'eccessivo riempimento | Nella struttura c'è un *overhead* ovvero un ingombro maggiore dell'elemento per via dei puntatori che lo compongono |
| Tempo necessario all'esecuzione delle funzioni per l'inserimento in coda sempre costante | La funzione di ricerca e di inserimento in coda è molto penalizzata siccome si deve scorrere tutta la lista |



### Stack

L'*ADT* chiamata **stack** può essere anche chiamata *lista LIFO*, cioè *Last In First Out*, o *pushdown list*.

L'*ADT stack* si usa ad esempio per i record di attivazione delle funzioni.



#### Modello matematico dietro lo *stack*

Il modello matematico che permette di caratterizzare questo tipo di dato è una lista *L* dove l'inserimento e l'estrazione degli elementi avviene esclusivamente da un solo lato, ovvero l'alto (*top/cima*).

Le operazioni consentite sono quindi quelle di `push()` e di `pull()`, che servono rispettivamente a aggiungere in cime alla pila e a togliere l'elemento in cima alla pila. 

Questo è molto importante perché consente di avere l'unicità sull'elemento da modificare al momento dell'estrazione dallo stack e anche sulla posizione di inserimento di un prossimo elemento.



#### Implementazione

Le operazioni standard consentite nella gestione dello stack, come predetto nel modello matematico sono:

```c
Push(PILA, elemento);
Pop(PILA); 
```

Dove:

* `Push()` si occupa di inserire nella pila
* `Pop()` si occupa dell'operazione di *pull*, ovvero di estrarre dalla pila. Si nota che questa funzione ha bisogno solo del parametro che indica la pila, perché l'elemento da estrarre è univoco.

Per la natura con cui è creato lo stack si riempie sempre dal basso e poi lo si svuota estraendo gli elementi dall'alto.

Altre operazioni semplificano la gestione dello stack, come ad esempio:

- `top(PILA)` : funzione che restituisce la testa della pila
- `StackIsEmpty(PILA)` : funzione che controlla se lo stack è vuoto
- `StackIsFull(PILA)` : funzione che controlla se lo stack è pieno (*questa funzione è necessaria solo se si è implementato lo stack in maniera statica).



Un problema si può creare nel caso ci sia la necessità di recuperare un elemento che non è posto il cima. Si crea quindi un array o uno stack temporaneo che servirà a contenere tutti i valori estratti in ordine finché non trovo il valore cercato. Quando trovo l'elemento cercato, riporto tutti gli elementi all'interno dello stack attraverso operazioni di `push()` e posso eliminare la struttura temporanea. 

Ricorda che le funzioni di `push()` e di `pop()` hanno un costo temporale e di calcolo unico, cioè costano sempre la stessa quantità di risorse e di tempo, quindi si possono usare ad oltranza senza il rischio che il tempo di esecuzione cresca troppo. 

Principalmente si possono fare due tipi di implementazione: con l'array e con le liste. Entrambi hanno lati positivi e lati negativi.



##### Implementazione con *Array*

L'implementazione attraverso un array dello stack ha la principale caratteristica di aver uno spazio di memoria fissa. Questo per lo più è uno svantaggio, perché lo stack è una struttura che tende a crescere e decrescere nel corso del programma, quindi avere la dimensione fissata ha un certo peso sul programma. Oltre a questo va gestito il caso in cui lo stack sia pieno.

Le funzioni di base per un implementazione di una struttura stack con array sono:

```c
#define StackMAx 20 // dichiarazione della massima dimensione dello stack

struct Tipostack {
    int N; 			 // numero di elementi presenti nello stack al momento
    int s[StackMAx]; // elementi presenti nello stack
}Tipostack;

typedef struct TipoStack STack; 	// Alias per le variabili strutturate
typedef struct Tipostack *StackPtr; // Puntatore a tipoStack

bool StackIsEmpty(StackPtr p){
    return (p->N == 0);
}

bool StackIsFull(StackPtr p){
    return (p->N == MAXP);
}

void Push(StackPtr, Tipoelemento Elem){
    if (StackIsFull(p)){
        exit();
    }
    p->s[p->N] = Elem;
    p->N=p->N+1;
}

int Pop(StackPtr p){
    if (StackIsEmpty(p)){
        exit();
    }
    p->N = p->N-1;
    return p->s[p->N];
}
```

All'interno di questa implementazione dobbiamo notare:

* la creazione di un `TipoStack` che serve per avere il controllo sul numero di elementi presenti all'interno dello stack e logicamente sul valore di quegli argomenti

* le funzioni `StackIsFull()` e `StackIsEmpty()` che sono necessarie al controllo sulla quantità di elementi all'interno dello stack, siccome questo è di dimensione fissata

* la funzione `Push()` che inserisce un elemento nello stack e aumenta il contatore di spazi, logicamente controllando che lo stack non sia pieno

* la funzione `Pop()` che estrae un elemento dalla cima dello stack, cioè dal valore del contatore, ne diminuisce il valore e torna il valore estratto

  

##### Implementazione con le *Liste*

Implementare uno stack con le liste permette alla struttura di crescere e decrescere in modo dinamico e indefinitamente grande (logicamente si ricordano le limitazioni fisiche). Il problema di questa implementazione è che la gestione può diventare molto complicata dal punto di vista logico.

Un implementazione tipo della struttura stack con le liste ha queste particolari funzioni:

```c
struct stackNode{			// Dichiaro l'elemento base della lista
    TipoElemento info;
    struct stackNode *prox;
};

typedef struct stackNode StackNode;	// Dichiaro gli alias 
typedef stackNode *StackNodePtr;

bool StackIsEmpty(StackNodePtr Node){
    if (Node == NULL) return true;
    else return false;
}
void PrintStack(StackNodePtr CurrentPtr){
    
    if (StackIsEmpty){
        cout << "Stack vuoto!" << endl;
        exit();
    } else {
        while (CurrentPrt != NULL){
            cout << CurrentPtr->info;
            CurrentPtr = CurrentPtr->prox;
        }
    }
   
}

void Push(StackNodePtr *TopPrt, TipoElemento Elem){
    
    StackNode *Punt;
    Punt = new StackNode;
    
    Punt->Info=Elem;
    Punt->prox=*TopPtr;
    *TopPtr = Punt;
      
}

TipoElemento Pop(StackNodePtr *TopPtr){
    
    StackNodePtr tmpPtr=*TopPtr;
    TipoElemento topvalue = (*TopPtr)->info;
    
    *TopPtr = (*TopPtr)->Prox;
    free(tmpPtr);
    
    return topvalue;
}
```

In questa implementazione dobbiamo notare: (L'implementazione è fatta con dei doppi puntatori perché Riccardi ha fatto così. Si può fare anche molto più semplicemente con un puntatore singolo sfruttando la struttura della lista e le sue funzioni)

* si definisce una variabile struttura che contiene la struttura di base di un blocco della lista e si dichiarano 
* le funzioni `StackIsEmpty()` e `PrintStack()` non sono necessarie ma facilitano l'implementazione
* la funzione `Push()` prende il nuovo elemento, crea un blocco di dimensione dinamica nello stack e gli inserisce l'elemento. Poi esegue le operazioni di inserimento in testa della lista
* la funzione `Pop()` si occupa di eliminare l'elemento di testa (esattamente le stesse operazioni che si farebbero per una lista concatenata) e tornare il valore dell'elemento eliminato



#### Un utilizzo particolare dello stack: *la notazione postfissa*

Nel nostro uso quotidiano usiamo la notazione *infissa*, cioè quella che usa le parentesi, ma che ha di negativo il fatto che ci può essere un ambiguità di precedenza nell'esecuzione delle operazioni che viene risolta parzialmente con le parentesi, ovvero con la creazione di una tabella di priorità.

Per evitare l'utilizzo di questa tabella si può usare la *notazione postfissa*, chiamata anche *notazione polacca*. I principali lati positivi di questa notazione per la scrittura è che:

- è utile per il calcolatore infatti è un tipo di scrittura che la macchina la preferisce e con cui lavora. Usandola si evita un *layer* di traduzione eseguito dal linguaggio di programmazione.
- è apprezzata perché non ha ambiguità, quindi non ha bisogno di usare tabelle di precedenza e associatività, non lascia quindi spazio alla soggettività
- ha come lato negativo che utilizza un implementazione attraverso *stack*, che può risultare complicata



La traduzione da notazione infissa a postfissa è semplificabile in questo algoritmo:

* leggo una stringa in notazione infissa, che può essere `( 9 + ( 8 * 4 ) )`
* eseguo un continuo `push()` di tutti gli elementi della stringa:
  * se trovo un operatore lo inserisco nello *stack*
  * se trovo un numero lo stampo
  * se trovo una parentesi aperta o uno spazio ignoro
  * se trovo una parentesi chiusa eseguo  un `pull()` dallo stack e lo stampo

Otterrò quindi un comportamento uguale a questo:

<img src="C:\Users\giova\Documents\1_UNI\programmazione1\appunti\appunti-prog1\Giovanni\image\image-20201124115813132.png" alt="image-20201124115813132" style="zoom:80%;" />



Questa funzione può essere scritta in codice:

```c++
void infissa2postfissa(const char* e){
    // Alloca lo stack
    for (int i=0; e[i] != '\0'; ++i){ // se la stringa non è finita continua
        if ((e[i] == "(") || (e[i] == ' ')) continue;
        // se la stringa contiene una parentesi, ignora e vai avanti
        if (e[i] == ')')
            cout << pop(S);
        // se incontri la parentesi tonda estrai e esegui, ritorna il valore finale
        if ((e[i] == '-') || (e[i] == '*') || (e[i] == '+')
           					|| (e[i] == '/'))
            Push(S, e[i]);
        // se contiene un operando inserisci in cima allo stack
        if ((e[i] >= '0') && (e[i] <= '9'))
            cout << e[i];
        // se incontriamo un numero lo stampiamo in output
    }
	// Free S
}
```



### Coda o Queue

L'*ADT* coda o queue è chiamata anche lista FIFO, cioè First In First Out. Il concetto di coda nella programmazione non è accomunabile a quello della vita reale, infatti ci sono un sacco di modi per fare delle code. Nella programmazione invece ne esiste uno solo.

La struttura della coda ha per definizione due accessi con funzioni diverse: la *coda* e la *testa* , che hanno il compito rispettivamente di uscita e di ingresso. E è proprio questa caratteristica che differenzia lo stack dalla coda. Di conseguenza questa struttura funziona in modo molto lineare:

- l'inserimento avviene solamente dal lato della coda
- l'estrazione avviene solamente da lato della testa



#### Implementazione

La struttura della coda ha principalmente due operazioni formali che permettono l'inserimento e la rimozione. 

```c
void Enqueue(TipoCoda Coda,TipoDato Dato); 

TipoDiDato Dequeue(TipoCoda Coda);

void CheckIfEmpty(TipoCoda Coda);
void PrintQueue(TipoCoda Coda);
```

* la funzione `Enqueue()` che prenda la funzione (e in alcune implementazioni è proprio chiamata) `Put()` e si occupa dell'inserimento dal lato della coda
* la funzione `Dequeue()`che prende la funzione ( e può anche prendere il nome di) `Get()` e si occupa dell'estrazione dell'elemento in testa alla coda e di ritornarlo
* altre due funzioni molto meno importanti ma che semplificano l'implementazione sono `CheckIfEmpty()` e `PrintQueue()`, che con il loro nome spiegano bene la loro funzione

Come lo stack si può implementare questa struttura con due modi differenti: attraverso l'uso delle liste e con l'uso degli array.



##### Implementazione con *LISTE*

```c++
typedef struct Tnodo{
    TipoElemento info;
    Tnodo* next;
}Tnodo;

struct queue{
    EL *Head;
    EL *Tail;
}


typedef struct EL ElemLista;
typedef EL* ElemPtr;
typedef struct queue Queue;
typedef Queue *QueuePtr;

void Enqueue(QueuePtr qP, TipoElemento El){
    
    ElemPtr = new Elemlista;
    ElemPtr->info = El;
    
    qP->Tail = ElemPtr;
    ElemPtr->next = NULL;
    
}

TipoElemento Dequeue(QueuePtr qP){
    
    ElemLista el = qP->Head->info;
    QueuePtr tmp = qP->Head;
    qP->Head = qP->Head->Prox;
    delete [] tmp;
    
    return el;    
    
}
bool checkIfEmpty(QueuePtr qP);

/* primo inserimento, devo modificarli entrambi
 * Head-Tail -> Tnodo inserito
 * aggiungo un altro
 * head -> Tnodo -> Tnodo2 -> NUll, con Tail -> Tnodo2
 * 
 */
```

In questa implementazione bisogna notare:

* la variabile strutturata rappresenta il blocco di base della lista, che contiene al suo interno il valore dell'elemento e il puntatore al prossimo blocco, come da definizione per le liste
* la struttura `queue` fornisce la testa e la coda della lista, inizializzati nel *main* o in una funzione a parte
* definisco degli alias per rendere il codice più leggibile
* la funzione `Enqueue()` è il corrispettivo per inserimento in coda della lista, con l'unica differenza che in questo caso la struttura `queue` fornisce già il puntatore alla coda, quindi evita di fare un ciclo su tutta la struttura
* la funzione `Dequeue()` si occupa di eliminare l'elemento in testa, impostare il nuovo elemento di testa nella struttura `queue` e poi liberare lo spazio del blocco. Questa funzione torna l'elemento eliminato



##### Implementazione con *Array*

L'implementazione del concetto di coda con la struttura array avviene con l'importante concetto per cui la struttura è allocata staticamente. 

A lato di questo tipo di implementazione invece esiste il concetto di *buffer o array circolare*, che è semplicemente un array con una diversa gestione degli indici. In quest'ultima struttura si deve fare attenzione a come viene calcolato l'elemento successivo che è leggermente diverso da un normale array:

```c++
i = (i+1); 			  // Incremento normale in una coda di array
i = (i+1) % CodaSize; // Incremeneto in un arrai circolare

// i = Codasize-1 sarà l'ultimo elemento dell'array
// i = Codasize +1 Sarà il primo elemento dell'array
```

L'array circolare quindi impone che:

* attraverso la relazione `(i+1) % CodaSize` si verifica se incrementare o ritornare a zero. Infatti la relazione con l'operatore di modulo può anche essere chiamata verifica della condizione di pienezza, infatti se `i == CodaSize` vuol dire che il resto è uguale a zero e che quindi il buffer torna al valore zero
* date queste condizioni si può dedurre che `i = Codasize - 1` è l'ultimo elemento dell'array e `i = Codasize + 1` è il primo elemento dell'array
* il `front` descrive un indice di valore uguale a zero
* `rear` indica il prossimo elemento. 
* la coda è vuota quando `fronte == rear`
* la coda è piena quando `(rear+1) % CodaSize == front`

 Un implementazione possibile del buffer circolare è con un valore usato come *sentinella* ovvero come indice per indicare che la coda è piena. Questa implementazione ha bisogno quindi di una colonna con il ruolo di sentinella, motivo per cui serve inizializzare un array con dimensione `CodaSize+1` per usare questa tecnica.

Un tipo di implementazione possibile per la coda con gli array può essere:

```c++
TipoElemento info;
struct queue{
    int CodaSize; // contiene al max CodaSize-1
    int Front;
    int Rear;
    TipoElemento *s;
};
typedef struct queue Queue;
typedef Queue *QueuePtr;
QueuPtr Q;

// Togliere dalla coda, incrementa di uno l'indice Front.

// Quando aggiungo in coda sta crescendo l'indice Rear.

bool QueueIsEmpty(Q);
bool QueueIsFull(Q);

void Enqueue(QueuePtr pt, TipoElemento El);
TipoElemento Dequeue(QueuePtr pt);

void QueuePrint(QueuePtr Q); // ricordarsi che è circolare, quindi devo usare il modulo

```

* le funzioni sono praticamente uguali a quelle che faresti per gestire una lista, ma più facili dal punto di vista logico. Se si vuole usare il buffer circolare bisogna ricordare il metodo per incrementarlo correttamente e implementarlo nel modo giusto. 	