1) I contratti per gli eventi e il topic su cui vengono inviati devono essere creati anche nell'api del servizio che sta in Listen?

2) Bisogna creare due topic differenti per l'evento di creazione e eliminazione di un dato album? (Secondo noi no, si può fare sullo stesso topic)

3) Per gli eventi che interessano recensioni-seguite, è necessario creare un adapter inbound differente per ogni topic a cui ci si è iscritti? O si può fare tutto nello stesso file?

4) Una volta implementato kafka, possiamo eliminare i vari folder albumclient, connessioniclient ecc... necessari per l'invocazione remota?

5) Testo della recensione va messo in RecensioniCreatedEvent?