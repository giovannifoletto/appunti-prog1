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



