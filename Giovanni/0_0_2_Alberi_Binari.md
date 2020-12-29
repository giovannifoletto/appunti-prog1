# Alberi binari:

sono utili perchè hanno caratteristiche importanti per la complessità di alcuni argomenti, ex. ricerca: O(log_2(n)).

Viene inoltre normale trattare gli alberi in modo ricorsivo per accedere agli elementi.

Ci sono algoritmi molto interessanti se nella fase di inserimento si è mantenuto un ordine, poiché è immediato, per come sono costruiti gli alberi è immediato inserire correttamente gli elementi.

viene chiaro trattare degli alberi binari di ricerca, in cui è garantita una relazione specifica tra padre e figlio

**Visita**: vedere ogni cosa di un albero, logicamente in maniera ricorsiva

- post-ordine (stampa del nodo dopo le chiamate ricorsive)
- pre-ordine (stampa del nodo corrente prima delle chiamate ricorsive)

* in ordine (stampa in mezzo alle chiamate ricorsive)





Eliminazione:

brutta, perché se dobbiamo eliminare un nodo, dobbiamo assicurarci che la regola della costruzione dell'albero venga rispettata. Quindi quando si elemina un elemento si deve ricostruire tutto l'albero sotto l'elemento trattato.

Se non fosse un albero binario di ricerca, la delete sarebbe molto facile perchè non serve rispettare le condizioni dell'albero.

Caso1: elimino elemento senza figli -> no problemi

Caso2:  