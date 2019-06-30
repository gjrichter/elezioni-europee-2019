Visualizzazioni cartografiche sulla base dei dati pubblicato dal Ministero dell'Interno sul sito 'Eligendo' nella parte 'Reportistica'.<br>
Source: [Eligendo - Ministero dell'Interno](https://elezioni.interno.gov.it/report)

Partito vincente e percentuale dei voti
---

- ### preparazione dei dati per la visualizzazione attraverso una procedura esterna.

  ```javascript
	ixmaps.totale_incidenti_georef.process = function(dbTable){
	  dbTable.addColumn({'source':'DATA','destination':'GiornoDellaSettimana'},
	    function(value){
		var dateA = value.split('/');
		var date = new Date(Number(dateA[2]),Number(dateA[1])-1,Number(dateA[0]));
		return String(date.getDay());
	  });
	}; 
  ```
  la procedura crea una nuova colonna 'GiornoDellaSettimana' basato sulla colonna 'DATA'. Crea un oggetto JavaScript Date() per ogni  valore delle colonna 'DATA' per poi chiedere il giorno della settimana.
  
  la funzione deve essere definito come metodo `.process` del oggetto dati definito nel tema con la proprietà `"dbtable":"totale_incidenti_georef"` .
  
  la sorgente JavaScript che contiene la descritta procedura deve essere comunicato attravarso la proprietà `"dbtableExt":"http://..."` del tema  

- ### aggregazione per una griglia dinamica definito in pixel dello schermo

  La griglia di aggregazione viene definito con `"gridwidthpx": "100"` in unita di pixel dello schermo. l'aggregazione viene ricalcolata ad ogni cambio dello zoom. 
  
  La grandezza dei grafici della visializzazione corrisponde alla griglia, definito con il aggiunto `|GRIDSIZE` nella definizione del tema.

- ### aggregazione di **valori discreti** creando nuove colonne nei dati

  Per aggregare i valori in una visualizzazione si calcola normalmente la somma o la media dei valori delle righe della tabella le quale posizioni geografiche ricadono in un definito rettangolo (o esagono) della griglia di aggregazione.

  Ma se la colonna dati contiene valori discreti, come per esempio il giorno della settimana della data dell'incidente, la somma non può rappresentare un risultato utile.

  In questi casi iXMaps crea per ogni valore (giorno) trovato una **nuova colonna** nella tabella dove **conta le presenze** di questo valore (giorno) invece di sommare il valore stesso.

  Con il aggiunto `EXACT` nella definizione del tipo del tema `"type":"..."` viene programmato questo conteggio di valori discreti.

  Alla fine dell'aggregazione, la tabella consiste in un numero di righe che rappresentano i quadrati della griglia contenedo le somme delle presenze per ogni valore, in questo caso per ogni giorno della settimana ('lunedì','martedì',...).

- ### visualizzazione delle curve con gli incidenti x giorno della settimana

  La rappresentazione dei dati aggregati viene scelto con il typo `CHART|SYMBOL|SEQUENCE|PLOT|LINES|BOX|GRID|AREA|FIXSIZE|GRIDSIZE`
  
  Che definisce i grafici come una sequenza di simboli `CHART|SYMBOL|SEQUENCE|PLOT`, invisibili via `"symbols":["none"]`, collegati con linee e riempito sotto `LINES|AREA`.
  
  I grafici avranno un contenitore con griglia `BOX|GRIGLIA` e la grandezza della griglia di aggregazione `GRIDSIZE`.
  
  Altri elementi della definizione sono `values` `label` e `xaxis`. Di questi `values` è molto importante perche definisce la sequenza dei giorni da 'lunedì' a 'domenica' che altrimenti sarebbe rimasto al caso o meglio alla sequenza di trovarli casualmente durante l'aggregazione. Invece di avere una sequenza utile per le curve, una sequenza casuale non ceerebbe una grafica interpretabile. 
