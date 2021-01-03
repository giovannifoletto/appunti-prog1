### Conversioni di Tipo

Come si diceva precedentemente il tipo di dato consente di fare un controllo sulla variabile, ma può anche avvenire di aver la necessità di una conversione fra variabili, per cui si è citata l'operazione di *casting*. Questa operazione segue un meccanismo automatico, in modo che io non debba preoccuparmi di quello che succede a basso livello per la conversione, e permette una corretta conversione tra variabili non uguali.

Il C fornisce un metodo per il *casting* che però necessità di attenzione al momento dell'utilizzo, per fare in modo che le variabili non perdano valore. Generalmente il C lavora con le regole della precedenza al momento dell'assegnazione, quindi viene convertito il valore a sinistra dell'operatore di assegnamento(`=`).

*NOTA BENE:* le conversioni automatiche possono essere molto particolari, quindi prestare molta attenzione al tipo di dato che tornano.il problema è sempre la codifica di oggetti, come ad esempio int o float (che si presuppone una perdita di cifre dopo la virgola), che sono anche piuttosto simili, ma quando si inizia a dover comparare un int con un char inizia a essere più complicato. 

Per fare questo si seguono delle regole che il C segue e a cui noi quindi ci atteniamo. 

![image-20201020113449566](C:\Users\giova\Documents\1_UNI\programmazione1\appunti\appunti-prog1\image\image-20201020113449566.png)

Facendo una conversione implicita, il compilatore si occupa di convertire le variabili in modo che siano dello stesso tipo. 

![image-20201020113717182](C:\Users\giova\Documents\1_UNI\programmazione1\appunti\appunti-prog1\image\image-20201020113717182.png)

Si eseguono tutte le azioni in ordine in modo che le variabili di tipo inferiori diventino superiori, di conseguenza se devo fare:

```c
int a == float b; 
	int a => float a;
	float a == float b;
```

Per rendere meglio comprensibile/visibile o per rendere il codice più ordinato/leggibile, si usa la keyword *(cast)*.

```c
float media;
int num1=5, num2=3;
everage = (num1+num2)/2; // in questo caso ci affidiamo alla converione implicita, il valore mancherà di cifre decimali

// se invece 
everage = (float) (num1+num2)/2; // risultato corretto, ma anche più leggibile.
```

L'operatore di casting è uno di quelli che ha la precedenza su molto, nell'ordine in cui si eseguono le operazioni (tabella di precedenza). 

*Ad Esempio:*

Per calcolare il valore di 
$$
\pi
$$
con precisione a piacere usando l'approssimazione di Leibniz:

![image-20201020115026359](C:\Users\giova\Documents\1_UNI\programmazione1\appunti\appunti-prog1\image\image-20201020115026359.png)

Questo metodo (ma soprattutto dal grafico di questo metodo) si comprende che è possibile calcolare il pigreco con un certo grado di precisione. Per avere 4 cifre piuttosto convergenti bisogna, come si vede appunto dal grafico, usare almeno un centino di cifre.

*Implementazione del metodo di Liebnitz: (nel main)*

```c
int i;
int precisione;
int segno = -1;
float pigreco = 1;

scanf("%d", &precisione);

for (i=1; i<=precisione; i++){
	pigreco += segno * 1/(2*i+1);
    
    printf("%f\n", pigreco*4);
    segno = (-1) * segno
}
```

In questo programma, in cui si usa la conversione implicita, che porta a un errore di approssimazione, perché la conversione implicita nel caso di divisone tra due variabili **int** causa un numero intero che logicamente manca del resto dalla divisione stessa.

Con il casting in modo esplicito, il programma funziona meglio, e soprattutto non lascia posto alla possibilità che il compilatore possa ogni tanto fare correttamente e in altri casi sbagliando la conversione implicita.

### TIPI USER-DEFINED IN C

Per usare variabili più complesse che siano definite puramente dal programmatore e contengano al loro interno più variabili e di più tipi, sia *user-defined* sia predefinite si utilizza uno *struct* ,  ovvero variabili strutturate.

Tutto ciò permette di creare variabili molto complesse, con una serie di caratteristiche diverse fra di loro.

```c
struct Impiegato{
	char Nome[20];
    char Cognome[20];
    float Stipendio;
    char CodiceFiscale[16];
}
```

Alla fine di questa definizione di struttura, che descrive come sarà questa variabile, non viene alloccata nessuna memoria, ma è solo un passo formale del C.

Però com'è che si fa ad accedere a queste variabili? Ad esempio negli array sapevo come erano i campi e l'unica cosa a cui dovevo stare attento era di non sorpassare il numero di spazi assegnati nel richiamare i caratteri. Se non più seguire una dichiarazione questa funzione *struct* non ha valore (vuol dire che la dichiaro e poi posso iniziare a usarla).

```c
struct Impiegato{
	char Nome[20];				// DEFINIZIONE
    char Cognome[20];			// DEFINIZIONE
    float Stipendio;			// DEFINIZIONE
    char CodiceFiscale[16];		// DEFINIZIONE
} amministratore, segretario; 	//DICHIARAZIONE

struct Impiegato staff1,staff2; //DICHIARAZIONE 
//Da qui in poi posso iniziare a usarla, siccome la ho dichiarata
```

All'interno dello *struct* non importa l'ordine (a meno che una variabile dipenda da un altra, in questo caso la variabile indipendente va dichiarata prima). infatti al compilatore non cambia se metti prima il nome o il cognome, perché poi la variabile viene richiamata in ugual modo. (Nessun cambio di ordine della definizione porta a miglioramento delle prestazioni, il programma ci mette ugual tempo a definire prima un campo di un altro).

Per indicizzare i campi all'interno dello *struct* viene usata la *dot notation*:

```c
impiegato.Stipendio = 0.0; 		// assegno al campo stipendio dello struct impiegato uguale a zero
// dopo aver inizializzato queste cose, poi posso stilare l'albero sintattico per usare lo struct come variabile normale:

if (impiegato.Stipendio < 2000){
    impiegato.Stipendio = 1.5 * impiegato.Stipendio;
}
```

Il *dot operator* è in cima alla tabella delle precedenze di traduzione, in linea con l'ordine di precedenza delle parentesi.

Il construtto *struct* occupa in memoria esattamente la somma della memoria occupata dagli elementi che lo compongono.

### STRUTTURE COMPLESSE

Le strutture complesse sono variabili strutturate che usano a loro volta variabili strutturate al loro interno.  Ad esempio:

```c
struct lato{
	int lunghezza;
	int x;
	int y;
}
struct rettangolo{
	lato lato-lungo;
	lato lato-corto;
}
```

Se all'interno dello *struct* è dichiarato un campo che a sua volta è uno *struct*, allora il dot operator sarà riutilizzato fino ad ottenere il campo ricercato.

```c
struct Data{				// definizione Data
	int giorno;
	int mese;
	int anno;
};

struct Data mia-data;		// dichiarazione Data

struct Agenda{				// definizione Agenda
    char[10] NomeEvento;
    mia-data DataEvento;
}

struct Agenda mia-agenda;	// dichiarazione Agenda

mia-agenda.mia-data.mese = 12;
    						// accedo al campo di Agenda, che è sua volta una variabile strutturata.
```



### Utilizzo del *typedef*:

L'idea è quello di creare un meccanismo sintattico formale in modo che io possa usare degli altri nomi per definire e chiamare le variabili, che possono essere variabili semplici o strutturate. 

Di solito il *typedef* è posto tra la definizione di costanti e il main().

Il *typedef*  funziona creando una tabella di sinonimi che rappresentano in realtà tipi di dati predefiniti o user-defined, ma che hanno il lato positivo di comparire al programmatore con un nuovo nome, appunto un sinonimo. Il nuovo tipo eredita le operazioni del tipo di partenza e ne è uguale, eccetto per il nome a cui ci si riferisce per richiamarlo. Ad esempio:

```c
typedef int intero;  //definisco alias per 'int' chiamato intero
intero numero; 		 // difinisco un tipo 'intero' esattamente come fosse un qualsiasi altro tipo
```

Il *typedef* non si basa sulla ereditarietà, cioè non funziona che gli elementi prendono il valore di quelli di partenza perché lo "ereditano", ma proprio perché il compilatore crea una tabella di sinonimi che viene usata al momento della compilazione per sostituire direttamente il tipo di dato originale al posto del suo alias presente nel codice.

Dove si usa:

- nel caso di variabili che ricorrono spesso e necessitano di essere più comprensibili/utilizzabili.
- evitare di definire più volte lo stesso tipo di variabile

```c
typedef char[10] NomePersona;
typedef NomePersona CognomePersona;
// adesso il tipo NomePersona è uguale al tipo CognomePersona che a sua volta è uguale a dire char[10]. Ma allo stesso modo posso anche dichiarare:
NomePersona Persona1, Persona2, Persona3;
```

A questo punto il programmare diventa sempre più a pensare a che cosa scegliere, in modo che il programma sia il più comprensibile e utilizzabile possibile.

```c
typedef int Vettore[20];
typedef Vettore Vettore20per20;
typedef vettore Nome;
// osso dichiarare una serie di cose usando sempre il tipo vettore

// a questo punto posso io a priori dichiare delle costanti a un tipo di dato in modo che mi venga più comodo il suo uso.
```

### Utilizzo del *enum*:

Un sottotipo di variabile *struct* è l'*enum* per indicare una variabile strutturata che può prendere solo un valore finito e predeterminato di valori. 

Questo tipo di variabile è molto utile ad esempio se si vuole usare una lista di cose di cui si conosce tutto, e che deve essere presa come variabile comprensibile al compilatore all'interno del programma. Un altra caratteristica è che all'interno della variabile *enum* il compilatore assegna a ogni valore, un'alias di valore *int*. 

Ad esempio un *enum* potrebbero essere i giorni della settimana a cui ad ogni giorni si associa un valore *int*, in modo da poter fare facilmente  cicli con i numeri.

```c
enum Settimana {
	lun, mar, merc, giove, sab, dom
        // lun = 0; mar = 1; merc = 2, giove = 3; ...
}
```



## I PUNTATORI

I puntatori sono un elemento necessario per la programmazione in C per:

* costruzione di funzioni

- allocazione dinamica
- efficienza dei programmi in memoria

I puntatori hanno il lato positivo nel fatto che permettono un grandissimo uso della memoria e quindi aiutano a rendere il programma più efficiente, al contrario però il loro utilizzo potrebbe causare errori di alloccamento di memoria o di sovrascrittura di un luogo di memoria. Di conseguenza i puntatori sono un punto di forza del C, ma anche punto di debolezza.

il concetto di puntatore si deve vedere come una variabile che contiene essa stessa l'indirizzo di memoria. Adesso la variabile puntatore contiene invece solo l'indirizzo, lasciando quindi libero lo spazio necessario all'allocazione di un valore.

Questo concetto è molto diverso da quello di una normale variabile, nella quale era contenuto sia il tipo di dato sia il suo indirizzo di memoria. 

ATTENZIONE CHE: il puntatore non da garanzie che all'interno di quello spazio di memoria ci sia qualcosa o se quello che è presente sia comprensibile.

La rappresentazione più efficace del puntatore è come quella che segue: il quadrato rappresenta il puntatore, la freccia invece indica a cosa punta il valore. Sopra il quadrato è presente l'identificatore del puntatore. La freccia arriva in una zona con un solo tipo di dato e poi identificata da un nome.

![image-20201022154400716](C:\Users\giova\Documents\1_UNI\programmazione1\appunti\appunti-prog1\image\image-20201022154400716.png)

```c
// sintassi per la dichiarazione di un puntatore:
TipoDato *Puntatore;
```

Il tipo di dato che viene assegnato al puntatore è necessario affinché il compilatore del C riesca a tradurre quello che viene indicato dalla variabile stessa. Di fatto, il puntatore non ha un tipo predefinito.

Un puntatore è definibile anche attraverso un *typedef*:

```c
typedef TipoDiDato *TipoDiDatoPuntato;
```



### Operatore `*` e `&` (dereferenziazione e indirizzo di)

Questi operatori servono per operare con i puntatori e con gli indirizzi di memoria.

L'operatore di **dereferenziazione** ha principalmente due funzioni:

* nella dichiarazione del puntatore, indica che la variabile dichiarata *è* un puntatore
* in qualsiasi altro uso, per recuperare il valore della variabile, ovvero il contenuto dello spazio a cui sta puntando.

Utilizzo generale:

```c
// nella dichiarazione, indica che si sta indicando un puntatore
TipoDiDato *PuntatoreTipoDiDato;

// Presa la variabile 'IndirizzoAlTipoDiDato' come un indirizzo
PuntatoreTipoDiDato = IndirizzoAlTipoDiDato; 

// viene assegnato un TipoDiDato, al valore del punatore
*PuntatoreTipoDiDato = ValoreTipoDiDato; 

// Per accedere al valore del puntatore si deve usare ancora deferenziare:
printf(*PuntatoreTipoDiDato); // Stampa: ValoreTipoDiDato
```

Da come si vede nell'esempio è importante specificare il tipo di dato del puntatore, perché l'operatore di dereferenziazione non fornisce queste informazioni. Se non si specifica il tipo di dato non si stanno fornendo al compilatore informazioni necessarie per *tradurre* il contenuto del suo valore.

L'operatore **indirizzo di** al contrario permette di calcolare un indirizzo di memoria di una data variabile.

```c
Puntatore *P;
P = &x;
// L'operatore '&' fa in modo da restituire l'indirizzo della variabile x, che poi viene assegnato al puntatore P
```

Questo metodo è molto conveniente per evitare di dover inserire manualmente il valore dell'indirizzo di memoria di una certa variabile. Questo modo è molto comodo anche per cambiare il right-value al puntatore.

Esempio: 

```c
// Dichiaro Puntatori P, Q e inizializzo a NULL, e un TipoDato y
Tipopuntatore P, Q;
TipoDato y = 10;
P = NULL;
Q = NULL;

printf("%d ", y);

// Inizializzo il puntatore sull'indirizzo di y
P = &y; 

// Cambio il valore della variabile puntata
*P = 14;

printf("%d", y); //14
printf("%d", *P);// 14

y++;

printf("%d", y); //15

Q = P; 
// Sto dicendo che all'indirizzo di memoria che era indicato in P adesso punta anche Q. Adesso ci sono 3 modi per accedere/modificare quella variabile.

printf("%d", *Q); // 15
```

Entrambi gli operatori  `*` e `&` sono inseriti al secondo posto nella tabella delle precedenze, superati delle parentesi e dagli operatori `.` e `->`.

### Problematiche e Rischi con i puntatori: 

L'utilizzo dei puntatori è facile ma anche potenzialmente pericoloso, principalmente stare attenti che: 

- esiste il problema dell'accesso multiplo (più puntatori allo stesso luogo di memoria), questo problema è detto **aliasing**
- il codice perde leggibilità e immediatezza nella comprensione quasi immediatamente, quindi diventa più difficoltoso ottenere un codice pulito e ordinato.
- non vengano persi valori, eliminando un puntatore, come si vede sotto. Il valore di y infatti non è più recuperabile dopo questa operazione, e occuperà sempre un valore di memoria inutilizzabile dal resto del programma.

![image-20201022170000134](C:\Users\giova\Documents\1_UNI\programmazione1\appunti\appunti-prog1\image\image-20201022170000134.png)

### Puntatori, Dot-notation e Variabili Strutturate:

Esempio di Struct con l'uso di puntatori:

```c
// dichiaro uno struct
typedef struct {
	int PrimoCampo;
	char SecondoCampo;
}TipoDato;

// nel MAIN, dichiaro x e *P come TipoDato, e assegno al puntatore l'indirizzo di x
TipoDato x, *P;
P = &x;

// Per accedere alle classi della variabile strutturata, rispettando le precedenze degli operatori:

(*P).PrimoCampo = 12; // Anche se le parentesi tonde non sono necessarie

*P.PrimoCampo = 12;   // Perchè la Dot-notation ha massima priorità

// Esiste anche una sintassi abbreviata per il puntatore a una struttura:
P -> PrimoCampo = 12; // questo operatore si trova in cima priorità
```

Da ricordare poi l'operatore unario **sizeof()** che restiuisce il numero di byte di una variabile.



### I tipi di dato con i puntatori:

il puntatore come detto è una variabile che non ha un tipo predefinito, ma gliene viene assegnato uno al momento della dichiarazione, azione necessaria affinché il compilatore capisca quello che contiene il valore del puntatore stesso. 

Per questo motivo, i puntatori possono essere utilizzati anche con le altre keyword che definiscono le variabili, in particolare si fa notare **const**. 

La keyword *const* può diventare necessaria nel caso in cui le variabili debbano essere accessibili in sola lettura, quindi evitare che vengano modificate.

La keyword *const* non va scambiato con *define*:

* *define* è un operazione che avviene a livello del linker, operando semplicemente una sostituzione attraverso tabella di sinonimi.
* *const* crea una tabella dei sinonimi indicando che la data variabile non si può modificare



Esistono due modi per protegger il puntatore, nel primo proteggiamo il puntatore, nonostante la variabile puntata possa ancora cambiare:

```c
int * const ptr = &x; 
```

Se invece voglio un puntatore che contenga una variabile non modificabile devo usare un doppio *const*:

```c
const int * const ptr = &x;
```



### Aritmetica dei puntatori:

L'aritmetica dei puntatori è necessaria per sommare indirizzi, o modificare il valore puntato dell'indirizzo.

Operatori di autoincremento con i puntatori o di modifica della variabile puntata: 

```c
// Dichiaro e assegno il puntatore
int x=1;
int *ip;
ip = &x;

// vado ad incrementare di 10 il valore che trovo dentro il banco di memoria
*ip = *ip+10;
// Valore x: 11

// incrementa la variabile puntata da *ip di uno
*ip += 1; 
// Valore x: 12

++*ip; 
(*ip)++;
// In questo caso i due operatori hanno lo stesso risultato, ma:
// NB: gli operatori * e di autoincremento(++x, x++) hanno stessa precedenza, di conseguenza il compilatore avrebbe associato da destra e il risultato potrebbe risultare diverso.
```

Aritmetica degli indirizzi:

```c
int y[] = {1, 2, 3, 4, 5};
int *p = &y; // puntatore inizializzato a y[0]

*(p+1) == 2; // Al puntatore viene aggiunto l'indizzo dello spazio di un intero, siccome il puntatore è inizializzato ad intero. Questo significa praticamente che si prende il valore intero +1 rispetto al puntatore.
*(1+p) == *(p+1); 
```

Per questo motivo si può dedurre che:

```c
// Dichiaro un array di numeri (un vettore), e poi assegno al puntatore a intero l'indirizzo del primo elemento del vettore. 
int a[10];
int *p;

a[i] == *(a+i); 
// *(a+i) è l'indirizzo di 'a' più 'i', quindi ne risulta l'elemento appartenente all'insieme 'a' in posizione 'i'
// 'i' in realtà non incrementa propriamente di i, ma di i*sizeof(TipoPuntatore)

// Questo vuol dire che: inizializzare p al primo elemento del vettore in questi due modi è equivalente
(p = &a) == (p = &a[0]);
(p = &(a+1)) == (p = &a[1]);

// Al contrario NON sono ammessi: (perchè sto sbagliando l'assegnamento a destra)
a = p;
a = p+1;

// Sottrazione con puntatori:
int *q;

p-q; // Restituisce il numero di elementi di differenza tra i due puntatori, ovvero il numero di spazi di memoria tra i due. ATTENZIONE: NON RESTITUISCE LA DIFFERENZA FRA I VALORI DEI DUE PUNTATORI
```

Si possono anche costruire array di puntatori:

```c
const char *semi[4] = {
    "Cuori",
    "Picche",
    "Fiori",
}
```

Questo è un array fatto con valori di stringhe costanti. Si può anche fare con valori di stringhe variabili, attraverso un array di puntatori non costanti.



### Relazione tra Array e Puntatori in C

Se si provasse a scrivere un programma che usasse una variabile a puntatore come array di caratteri cosa succederebbe?

```c
int main (){
	
    char stringa[] = "Ciao Mondo";
	char *stringaP = "Ciao Terra";
	
	printf("Stringa = %s \n", stringa);
	printf("StringaP = %s \n", stringaP);
	
}
```

Questo funziona perché l'operatore `[]` nel C viene usato come il puntatore al primo elemento dell'array, quindi se ne deduce anche che, per l'aritmetica dei puntatori:

```c
char *stringa == char stringa[];

stringa[5] == *(stringa+5);

stringa[5] == *(5+stringa);

stringa[5] == 5[stringa];
```

Attenzione alla modifica del puntatore a stringa, perché modificandolo si può perdere l'inizio della stringa, facendo l'errore dell'aliasing (sopra).

Quindi per scorrere i valori di una stringa implementata con un puntatore abbiamo bisogno di un puntatore di servizio:

```c
char *stringa = "Il sale della vita";
char *p;
int len = 0;
// variabile ptr p, con il valore di stringa, presa a variabile intermedia.
p = stringa;

while (*p++ != '\0')
	++len;
printf("Lunghezza stringa %s: %d\n", stringa, len)
```



## LE FUNZIONI IN C

Le funzioni nel C hanno diverse motivazioni:

* evitare di riscrivere intere operazioni **ripetute**, con il rischio di introdurre errori.
* scrivere blocchi **riutilizzabili**, da programmi/programmatori differenti.
* **incapsulamento** di algoritmi e possibilità di creare un **interfaccia** tra i programmi (ovvero differenza tra compito da eseguire e come questo venga eseguito).
* si possono scrivere **librerie**, ovvero insiemi di funzioni per eseguire determinate azioni definite



Le funzioni nel C:

- hanno bisogno di risorse per essere eseguiti
- devono avere tutti gli elementi di sintassi formale necessari affinché la funzione effettivamente funzioni e restituisca un valore corretto.
- si scorpora il codice in un **programma chiamante** e un **programma chiamato**.



### Funzionamento della macchina astratta del C e l'asservimento delle funzioni

I **sottoprogrammi** sono asserviti a sottoprogrammi chiamanti. Affinché questo sottoprogramma funzioni, c'è bisogno che all'interno del programma chiamato siano presenti tutti i parametri necessari a funzionare, sia che questi debbano essere passati da un altro sottoprogramma, sia che debbano solo essere accessibili.

Esistono due tipi di sottoprogrammi:

* *funzioni*, che restituiscono un valore al chiamante.

- *procedure*, che svolgono un compito per il chiamante, ma non restituiscono nessun valore (definiti con *void*, che è la keyword che indica nessun valore o tipo e serve per rispettare la sintassi formale della dichiarazione delle funzioni).



### Struttura di un programma C

In un programma solitamente, è presente:

* il sottoprogramma *main*, ovvero il principale, tutti quello che è preceduto da `#` è un indicazione al linker.

  ```c
  #include <libreria>
  #include "programma.h"
  
  #define MACRO valoreMAcro
  
  int main() {
  	// codice
  }
  ```

  * `#include <libreria>` serve per aggiungere delle librerie standard
  * `#include "programma.h"` serve per aggiungere librerie presenti nella stessa cartella del file che contiene il *main*.
  * `#define MACRO valoreMacro`  crea un alias che sostituisce il valore della macro al nome della macro.

* Una serie di sottoprogrammi che si possono chiamare

* ```c
  int funzione(int valore1, int valore2){ // questa linea è chiamata testata
  	// codice
  }
  ```

  * `int` all'inizio è il *parametro di ritorno della funzione*. Può essere un tipo di variabile predefinito o uno user-defined
  * `funzione` è il *nome della funzione*
  * `(int valore1, int valore2)` sono i *parametri o valori formali*. Indicano i parametri che vanno forniti alla funzione e il tipo di questo parametro. C'è la possibilità di aver tipo di parametri sia predefiniti che strutturati. 
  * `{ //codice }` è il luogo dove il programma si svolge quando la funzione viene chiamata, ovvero il *corpo della funzione*. Questa può contenere una *parte dichiarativa locale*, dove vengono definite le variabili necessarie all'esecuzione, e una *parte programmatica* che contiene l'algoritmo del programma.

Nel C i sottoprogrammi hanno:

* la *definizione* del programma, che comprende sia la testata che il corpo della funzione
* la *dichiarazione* della funzione, chiamata anche *prototipo*, che comprende solo la testata.

```c
int funzione(int valore1, int valore2); // questa è una dichiarazione o prototipo

int funzione(int valore1, int valore2){ // questa una definizione del programma
	// codice
}
```



#### Collegamento tra funzione e chiamante

Al momento della restituzione del valore al programma chiamante possono succedere due cose. O il programma ha un ritorno di tipo *void* quindi non ritorna nulla, oppure ha un tipo di variabile che deve restituire. In questo caso, allora:

* la funzione non restituisce nessun valore, quindi la funzione termina con l'ultima istruzione prima delle parentesi graffe
* la funzione termina con `return;`

* la funzione termina con `return espressione;`, allora il valore dell'*espressione* viene passato al programma chiamante. L'*espressione* può essere:
  * tipo di dato predefinito o user-defined, che coincide con il *parametro di ritorno*
  * **non** può restituire un array
  * **può** restituire puntatori, così rende possibile passare strutture molto grandi molto velocemente
  * può essere restituito solo un valore

Nel sottoprogramma si possono avere delle variabili locali, che vengono dichiarate normalmente e che servono per l'effettivo svolgimento del programma. Il loro valore può essere restituito anche con il `return valore;`.



#### Parametri formali e effettivi

Ci sono due tipi di parametri:

- parametri *formali* sono quelli elencati nella testata 
- parametri *effettivi* sono quelli con la quale la funzione viene invocata

I parametri formali vengono inizializzati con valori dei parametri effettivi. Di fatto il programma chiamante si occupa di creare i parametri effettivi per chiamare la funzione attraverso i parametri formali della funzione. Al momento in cui questi parametri vengono passati, l'ordine conta. Questo vuol dire che i parametri effettivi devono essere i corrispettivi dei parametri formali dichiarati nel sottoprogramma. Questo vuol dire anche che prima di usare una funzione, è necessario che questa sia *definita*, ovvero che siano stati dichiarati la testata e il codice della funzione.



## Modello di esecuzione e il concetto di ambiente

Prendiamo un esempio di programma che utilizza funzioni chiamanti e sottoprogrammi:

```c
int x = 0;
int f1(int p);
void f2();

main(){
    f2();
}
int f1(int p){
    return p+x;
}
void f2(){
    printf("%d", f1(x));
}
```

Ogni sottoprogramma crea in memoria uno spazio, chiamato *ambiente*, che contiene le variabili locali, i parametri passati e il risultato. Questo spazio di memoria viene rilasciato alla fine dell'esecuzione del programma. 

Posso pensare come se avessi una macchina dedicata che viene creata a ogni esecuzione di quella funzione. Questa macchina viene alloccata e messa a disposizione quando la funzione viene istanziata, e poi viene rilasciato dealloccando tutta la memoria occupata quando ho finito il suo utilizzo. 

In realtà una sola macchina virtuale può eseguire questo comportamento. L'immagine sotto è un ottima rappresentazione di quello che accade.



![image-20201029162606113](C:\Users\giova\Documents\1_UNI\programmazione1\appunti\appunti-prog1\image\image-20201029162606113.png)

Quando allocco memoria per un programma io di fatto sto costruisco una pila, in cui i pezzi sono in contatto tra di loro e contengono i vari ambienti delle funzioni, che a loro volta contengono le variabili necessarie al funzionamento della funzione stessa. Questa struttura può essere rappresentata dallo **stack**, chiamato anche pila, che fa riferimento alla struttura LIFO (Last In First Out), una delle strutture complesse viste più avanti. 

Quando ho finito l'esecuzione di un ambiente posso rilasciarlo. Siccome gli ambienti si sono accumulati dal basso verso l'alto, logicamente rilasciando un ambiente alla volta, si smonterà lo stack dall'alto verso il basso.

Quando una funzione viene invocata si crea una copia delle variabili che  vengono passate dalla funzione chiamante, mentre si deve ritornare il risultato dell'ambiente prima che venga dealloccato e quindi perdere tutte le variabili non sono state passate. La copia delle variabili permette di avere tutte le informazioni necessarie al funzionamento della funzione corrente. Questo è utile e necessario, perché come abbiamo visto, al momento della creazione di una funzione, si crea l'ambiente necessario a quella funzione, che per forza di cose è diverso dall'ambiente del programma chiamante. Passando il valore per copia, quindi, ci si assicura che tutte le variabili necessarie al corretto funzionamento siano presenti nell'ambiente corrente.

Lo svantaggio di questo metodo è la lentezza nello scrivere grosse strutture di dati, un ingombro di memoria esagerato, soprattutto se la variabile da copiare è molto grande, o è ad esempio un intero database, che non si può replicare tante volte se no la memoria finirebbe.

Per ovviare a questo problema si usano i puntatori. Il puntatore infatti è un oggetto molto piccolo che include al suo interno un indirizzo di memoria, rendendolo così accessibile alla funzione. In questo modo di fatto si sta modificando la variabile presente in un altro ambiente, senza bisogno di avere una copia all'interno dell'ambiente corrente. Questo però ha anche dei problemi, infatti in questo caso i cambiamenti sulle variabili sono permanenti, e non si sta passando nessun valore alla funzione chiamante per eseguire questi cambiamenti.

Il rischio nell'utilizzo dei puntatori è che nel caso di un errore nella gestione del puntatore si possono creare gravi problemi di accesso a sezioni di memoria non normalmente accessibili e c'è il rischio di corrompere sezioni di memoria. Per cercare di risolvere in parte questo problema si deve cercare di indicare i valori costanti nei puntatori.

Un esempio:



![image-20201029170916817](C:\Users\giova\Documents\1_UNI\programmazione1\appunti\appunti-prog1\image\image-20201029170916817.png)



In questa implementazione:

* Non ho copiato tutto il vettore V, ma solo il suo indirizzo e quindi siamo riusciti a diminuire lo spazio in memoria necessario all'esecuzione del programma. 

* Con questo uso siamo riusciti a modificare un valore appartenente al *main*. 
* `&v` al momento del passaggio del vettore serve perché è richiesto un puntatore nella definizione della funzione, e questo operatore restituisce l'indirizzo della variabile.

Il vantaggio di usare i puntatori in C consente nel fatto che si può passare qualsiasi tipo di dato. Infatti usando uno *struct* posso gestire tutti i parametri formali e passo un'intera struttura con un solo puntatore.



### Passaggio di parametri pro e contro

Il passaggio per *valore* in una funzione:

* esegue una copia inefficiente se il parametro è ingombrante 
* parametro effettivo e formale occupano zone distinte di memoria
* fornisce valori di ingresso

Il passaggio per *indirizzo* in una funzione:

* permette la copia di un indirizzo di una variabile semplice o strutturata
* la modifica avviene direttamente sul parametro effettivo
* sono possibili effetti collaterali



### Ambiti di visibilità

Per comprendere il concetto di visibilità delle variabili, si deve prima comprendere quello di *blocco*. Il blocco di codice nel C si dichiara con `{ }` e può comparire in ogni sintassi che consente un istruzione (nel corpo di una funzione, o in un qualsiasi ciclo).

Due blocchi fra loro possono essere:

* *annidati*, cioè l'uno dentro l'altro
* *paralleli*, cioè entrambi all'interno di un terzo blocco.

Per essere utilizzate le variabili devo essere visibili, ovvero il programma deve sapere a cosa corrisponde l'identificatore della variabile. 

Se la dichiarazione avviene nella **parte dichiarativa locale** allora le variabili sono visibili ovunque all'interno del suo blocco e i blocchi che sono contenuti al suo interno. 

Se la dichiarazione avviene nella **parte dichiarativa globale**, quindi esterno a qualsiasi blocco, la variabile è visibile da tutte le funzioni, incluso *main* e procedure e in tutti i blocchi del programma.

Può avere *side effect o effetti collaterali*, ovvero l'esecuzione di una funzione non è più confinato all'interno del suo ambiente e interessa variabili di altre funzioni, oppure tutto è accessibile, anche quando non lo dovrebbe essere. Per questo motivo l'uso di variabili globali è solitamente sconsigliato (da non confondere con le macro per il linker, che invece possono essere molto utili).

Da ricordare il *mascheramento*: se due variabili una globale e una locale hanno lo stesso nome, il linker assegna il valore di quella locale, di fatto mascherando eventuali omonimi.



#### Ciclo di vita delle variabili

Il normale ciclo di vita di una variabile va dal momento della creazione/alloccamento della memoria, fino alla distruzione/dealloccamento della memoria. 

Esistono due classi di variabili:

- **statiche**: 
  - alloccate una volta e poi distrutte al termine dell'esecuzione del programma
  - lo sono le variabili *globali*
  - persistenti all'interno e all'esterno di invocazioni di funzioni
  - si può dichiarare una variabile o un blocco *static* al momento dell'alloccamento, che diventano persistenti all'interno ed all'esterno di invocazioni di funzioni.
- **dinamiche/automatiche**:
  - possono essere create e distrutte *automaticamente*/implicitamente o esplicitamente dal programmatore con appositi construtti.
  - sono dichiarate quando il flusso di esecuzione entra nel loro ambito di visibilità e distrutte all'uscita di tale ambito.
  - sono dichiarate nelle funzioni e nei blocchi, anche come parametri
  - *NB:* le variabili automatiche appartenenti allo stesso blocco che viene più volte ripetuto occupano indirizzi sempre differenti e non contengono il valore precedente



## Gestione dei Files in C

Il C attraverso una libreria fornisce un interfaccia consistente per gestire i files. Questo viene eseguito ad altro livello, interfacciandosi direttamente con il sistema operativo, infatti le risorse richieste "vivono" fuori dal programma stesso. 

Per scrivere e leggere files, è necessario interfacciarsi a delle periferiche. In generale si può pensare anche che esistano due tipi di periferiche: 

* quelle *fisiche*: ovvero l'effettivo elemento con cui il sistema operativo si interfaccia, che può essere uno schermo o una stampa o qualsiasi altra cosa, e che crea con il sistema operativo un flusso di lettura e scrittura di dati
* quelle *logiche*: cioè l'elemento che permette di interfacciarsi nello stesso modo univoco con tutte le periferiche fisiche. Questo avviene attraverso il sistema operativo, che fornisce l'astrazione necessaria tra le periferiche e il programma in esecuzione.

Nel C le periferiche sono di tipo logico, quindi forniscono un astrazione attraverso il sistema operativo, creando dei *flussi* o *streams*, che sono i responsabili di creare queste interfacce consistenti, ovvero quelle interfacce necessarie al C per creare un livello di astrazione che prescinde dalla periferica vera e propria.

I flussi si comportano nello stesso modo e si possono eseguire le stesse funzioni su di loro. I flussi possono permettere un accesso casuale o lineare in lettura o scrittura, cioè lettura e scrittura di byte, che possono partire dall'inizio e arrivare alla fine nel caso di un accesso lineare, o invece possono partire da un determinato punto con l'accesso casuale.

Tutto questo è molto utile perché adesso il nostro modo di comunicazione con una periferica necessiterà solo di inizializzare un flusso, e poi interfacciarsi con questo. Si dice che un flusso è aperto se si è associata una certa periferica e si riesce a leggere e scrivere da e su questa.

I flussi possono essere di due tipi:

- *BINARIO*: lettura e scrittura di byte
  - Le sequenze di byte possono essere pacchettizzate in vari formati
  - scriviamo o leggiamo byte per byte, quindi abbiamo una corrispondenza 1:1 tra quello che è rappresentato e quello che viene letto/scritto
  - nessuna traduzione dell'informazione che vuol dire nessuna perdita di informazioni dovuti all'aggiunta o la diversa codifica delle informazioni
- di tipo *TESTO*: sequenza solo di caratteri in lettura/scrittura
  - Sequenze di righe con zero o più caratteri delimitata da `\n` (carattere a capo).
  - Alcuni caratteri potrebbero non aver giusta corrispondenza nella lettura o scrittura perché provengono da macchine diverse, e che quindi usano codifica in byte diverse. (problemi nella codifica dell'informazione)
  - Bisogna quindi tenere in considerazione che la corrispondenza di caratteri scritti o letti e quelli memorizzati *non è garantita*.



### Concetto di Files:

I file sono contenitore di informazioni accessibili e manipolabili con operazioni di *read & write*. La loro gestione è sottoposta al sistema operativo, che si occupa di controllare lo stato della periferica e di esporre una parte di questa periferica al linguaggio di alto livello. Di conseguenza il sistema operativo è necessario nella gestione dei files.

L'operazione di associazione di un file e uno stream avviene con una operazione di *open* che crea un flusso tra periferica e il programma e che permette lo scambio di informazioni (keyword *open* non standard, ma usata nel C). Il C permette questa associazione in modo consistente tramite la libreria **`stdio.h`**. Nel momento in cui si include questa libreria nel programma, allora si creeranno i flussi necessari al suo funzionamento. In questa libreria sono presenti i **tre flussi standard**. Questi vengono aperti al momento dell'esecuzione del programma e sono: 

* sulla periferica video: **`stdout`** e **`stderr`**
* sulla periferica da tastiera: **`stdin`**

Questo è anche il motivo per cui non c'è bisogno di aprire manualmente lo stream per gestire l'operazione di `printf()`, infatti la libreria si incarica di aprire, gestire i flussi e poi chiuderli. 

Quasi tutto è astratto per la gestione della periferica attraverso questa libreria come ad esempio le azioni di apertura, di lettura, di scrittura, e di chiusura. Alcune cose dell'aspetto operativo però va conosciuto, infatti mentre gli stream operano sempre nella stessa maniera, le periferiche non sono assolutamente simili: possono avere diverse caratteristiche di accesso ai files. Infatti

- se una periferica consente l'accesso sequenziale, allora bisognerà sempre leggere e scrivere in ordine.
- se una periferica consente l'accesso random, allora si potrà iniziare a leggere e scrivere in modo casuale, posizionandoci all'interno del file a piacere

Per la gestione del flusso è necessario anche saper "staccare" il file dallo *stream*, ovvero chiudere il flusso stesso. E' importante perché il sistema operativo utilizza delle risorse anche per gestire la periferica, lasciando quindi una periferica libera si possono usare per altre azioni. 

La scorretta chiusura di uno stream può causare problemi. Infatti generalmente gli stream funzionano attraverso la bufferizzazione dello stream, cioè quando non gestiscono singolarmente tutti byte dello stream, ma si raggruppano vengono mandati in blocchi. Quando non viene chiuso lo stream con un buffer non vuoto le informazioni che questo contiene non vengono passate al programma causando una perdita di dati. Per risolvere questo problema si può eseguire un operazione di *flushing* , cioè la capacità di caricare tutto quello che contiene il buffer del flusso in modo programmatico. Generalmente è un operazione che esegue prima della chiusura dello stream stesso perché si occupa di assicurarsi che il buffer sia vuoto, e in caso contrario passare tutti i dati al programma, in modo che tutto venga letto/scritto e poi svolto, senza rischiare la perdita di dati. Nel C il comando per eseguire questa azione è `fflush(<stream>)`. 

Il C possiede la capacità di poter di accedere a delle funzioni e dei flussi di medio livello del sistema operativo che altri linguaggi non possiedono, fornendo inoltre la stessa interfaccia consistente.



#### Variabile di tipo *FILE*

La variabile di tipo *FILE* può essere pensata come una qualsiasi altro tipo di variabile, a cui si assegna un puntatore. Questo puntatore serve a indicare la sezione di memoria che è incaricata di leggere e accogliere il buffer del file.

```c
FILE *file_da_aprire;
```

Il puntatore di tipo *FILE* conterrà un campo di byte per la lettura e scrittura e per lo stato della periferica. Questo è un oggetto profondamente legato al sistema operativo, che si occupa di istanziare tutti i campi necessari al suo funzionamento, come ad esempio lo stato della periferica. Un esemplificazione di cosa avviene in un programma quando vengono aperti più files.

![](C:\Users\giova\Documents\1_UNI\programmazione1\appunti\appunti-prog1\image\image-20201110122807433.png)



### Operazione di gestione dei file:

```c
FILE *fopen (nomefile, modalità);
// Header della funzione fopen apri lo strem con un file <nomefile> e con modalità (vedi dopo)

int fclose (FILE *fp);
// Header della funzione che termina l'associazione tra il flusso e file di una periferica

// Per usare questa funzione in un programma devo
FILE *fp;
fp = fopen("MioFile", "r"); 
//se il file è nella cartella in cui sto lavorando, mentre invece se ho bisogno di un file in un altra posizione devo selezionare tutto il percorso del file
```

Per esempio:

```c
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char *argv[]){
    FILE *fp;
    char c;
    
    fp = fopen("/tmp/MioFile.txt", "r");
    
    if (fp == NULL){
        printf("Il file non può essere aperto\n");
        exit(1);
    }
    
    fclose(fp);
}
```

In questo esempio si deve notare che:

1. `if (fp == NULL)` è necessario a controllare che l'azione di apertura del file sia andata a buon fine, infatti in caso di errore il puntatore assume valore *NULL*

2. Nel caso avvenga un errore è necessario che il programmi termini con `exit()` , prima di cercare di lavorare su una cosa che non esiste

3. ricordare `fclose(<puntatore_a_file>)` per chiudere la periferica. Attenzione alle operazioni di `fflush()` prima di chiudere. Questa funzione deve **SEMPRE ESSERE PRESENTE** alla fine, perché la sua mancanza può provocare perdita di dati, perdita di files e altri errori.

   

#### Modalità di accesso ai files

Come visto sopra la funzione `fopen()` accetta come secondo parametro la modalità con cui deve accedere al file. Le modalità possibili sono:

- `r`  : lettura modalità testo
- `w`  : scrittura modalità testo a inizio file
- `a`  : scrittura modalità testo alla fine del file (*append*)
- `rb` : lettura in modo binario
- `ab` : scrittura in modalità binaria a fine file (*append*)
- `wb` : scrittura in modalità binaria a inizio file
- `r+` : apertura per modalità testo per lettura e scrittura
- `w+` : crea file in modalità testo per lettura e scrittura. Si il file già esisteva viene sovrascritto
- `a+`  : append o crea un file in modalità testo per lettura e scrittura a fine file
- `r+b` : apertura in modalità binaria per lettura e scrittura
- `w+b` : crea un file in modalità binario per scrittura e lettura
- `a+b` : append o crea file in modalità binario per lettura e scrittura.

Ricordare che se un file è aperto in una modalità non si può usarne un altra senza chiuderlo e riaprirlo.

#### Altre funzioni utili alla gestione dei file

Esistono funzioni per la lettura e scrittura di caratteri: (tutti restituiscono *EOF - end of file* in caso di errore)

```c
int getchar (void); // legge da stdin

int putchar(int c); // scrive su stdout

int fget (FILE *fp); // legge carattere da FILE
  
int fputc (int c, FILE *fp); // scriver carattere su FILE
 
```

*Per la lettura e scrittura di stringhe su file:*

```c
char *fgets (char *s, int length, FILE *fp);

int *fputs (char *s, FILE *fp);

int puts(char *s);
 
```

* `fgets()` e `fputs()` scrive stringhe su *stream* specificato, ad eccezione del carattere *NULL*. Restituiscono *EOF* come errore.
* `fgets()` legge i caratteri finché non trova il *EOF*, il valore nullo o al più `length-1`, il carattere `\n`  viene letto e incluso nella stringa
* `puts()` scrive stringhe su *stdout* aggiungendo *NEWLINE* e restituisce *EOF* come errore.

*Operazioni sulla gestione dei files*

```c
int remove (nomeFile);

int rename (vecchionome, nuovonome);
```

* le azioni `remove()` e `rename()` servono a rimuovere e rinominare i file. 
* entrambe queste funzioni dipendono dal sistema operativo: se non si dispongono i permessi necessari a accedere alle risorse e a modificare lo stato dei file, o se il file risulta aperto da un altro *stream* allora l'azione non è consentita e si restituisce un errore.
* il controllo dei permessi e dello stato di utilizzo del file è sottoposto all'implementazione delle funzioni, che devono prevedere questi due casi.

*Operazione di lettura e scrittura di testo da/su file:*

```c
int printf(stringa, elementi);  		  			// Scrittura in stdout

int scanf(stringa, indirizzo_elementi);   			// Lettura in stdin

int fprintf(FILE *fp, stringa, elementi); 			// Scrittura su stream di tipo FILE

int fscanf(FILE *fp, stringa, indirizzo_elementi);	// Lettura su stream tipo FILE
```



*Operazione di lettura e scrittura in binario da/su file:*

```c
int fread(void *ptr, dimElemento, numElementi, FILE *fp);

int fwrite(void *ptr, dimElemento, numElementi, FILE *fp);
```

* `fread()` legge un blocco di dimensione maggiore di un byte e li memorizza all'indirizzo di `ptr`. Restituisce il numero di elementi effettivamente letti, che potrebbe essere diverso da `dimElemento*numElementi`, quindi bisogna controllare con `ferror()` se sono ritornati errori.
* `fwrite()` scrive blocchi di byte prelevandoli dall'indirizzo di `ptr`. Restituisce il numero di elementi effettivamente scritti, quindi sorge la possibilità di controllo anche in questo caso.



*Accesso Random a FILE:*

```c
int fseek(FILE *fp, long offset, int refpoint);

void rewind(FILE *fp);
```

* `fseek()` permette di accedere a posizioni nel file `fp` per operazioni di lettura e scrittura:
  * `fp` è il puntatore a tipo *FILE*
  * `refpoint` è il parametro che indica da dove bisogna calcolare l'`offset`. Questo parametro può variare tra:
    * `SEEK_SET`: l'inizio del file
    * `SEEK_CUR`: posizione corrente del cursore, cioè l'elemento incaricato di scorrere lo stream
    * `SEEK_END`: fine del file
  * `offset` è il numero di bytes per il calcolo della nuova posizione corrente a partire da `refpoint`
* da notare che la funzione `rewind()` non è altro che la funzione macro per eseguire `fseek(fp, 0, SEEK_SET)`, cioè iniziale a leggere lo stream dall'inizio con *offset* uguale a zero.



#### Pro e Contro di File Binari e di Testo

Entrambi i tipi di file hanno lati positivi e negativi: 

*FILE BINARI:*

* *PRO*: occupano meno spazio, hanno la possibilità di un accesso random
* *CONTRO*: non c'è portabilità tra calcolatore, non si può leggere con un normale editor di testo

*FILE di TESTO:*

* *PRO*: completa portabilità fra calcolatori
* *CONTRO*: più complicata la modifica e l'accesso random, perché bisogna conoscere la struttura del file, dimensione maggiore a parità di contenuto, possibilità di corruzione e incompatibilità dei caratteri



### Gestione degli errori

Durante l'utilizzo dei files, si possono causare degli errori. Questi devono essere correttamente gestiti per evitare un comportamento non corretto del programma. 

Quando la funzione deve restituire una variabile di tipo *FILE*, c'è la possibilità di un errore generico nell'inizializzazione dello stream, ovvero quando si restituisce il puntatore con un valore *NULL*, e la possibilità di raggiungere l'*EOF*, quindi terminare la lettura del file.

Per controllare e in caso prevenire questi errori, sono presenti tre funzioni:

```c
int ferror(FILE *fp); 	  // return TRUE se errore in un azione di r/w

int feof(FILE *fp); 	 // return TRUE se viene raggiunta la fine del file

void clearerr(FILE *fp); // esegue la cancellazione dei segnali di errore
```

