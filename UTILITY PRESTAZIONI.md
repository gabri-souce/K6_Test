### MODIFICHE DA FARE NEL FILE STRESS
 Trova questa sezione nel file:


yaml
export const options = {
  stages: [
    { duration: '1m', target: 100 },   #  MODIFICA QUESTO
    { duration: '5m', target: 100 },   #  MODIFICA QUESTO
    { duration: '30s', target: 0 },
  ],

### OPZIONE 1: PICCO REALISTICO ITALIA (CONSIGLIATO)


yaml
export const options = {
  stages: [
    { duration: '3m', target: 700 },   #  Ramp-up a 700 VUs
    { duration: '15m', target: 700 },  #  Mantieni 700 VUs
    { duration: '2m', target: 0 },
  ],
Rappresenta: Picco mattutino nazionale (9:00-10:00)

### OPZIONE 2: CARICO NORMALE GIORNALIERO


yaml
export const options = {
  stages: [
    { duration: '2m', target: 300 },   #  Ramp-up a 300 VUs
    { duration: '10m', target: 300 },  #  Mantieni 300 VUs
    { duration: '1m', target: 0 },
  ],
Rappresenta: Carico medio giornaliero

### OPZIONE 3: STRESS ESTREMO


yaml
export const options = {
  stages: [
    { duration: '5m', target: 1500 },  #  Ramp-up a 1500 VUs
    { duration: '20m', target: 1500 }, # Mantieni 1500 VUs
    { duration: '3m', target: 0 },
  ],
Rappresenta: Evento eccezionale o disaster recovery

### OPZIONE 4: CAPACITÀ MASSIMA


yaml
export const options = {
  stages: [
    { duration: '10m', target: 2500 }, #  Ramp-up a 2500 VUs
    { duration: '30m', target: 2500 }, #  Mantieni 2500 VUs
    { duration: '5m', target: 0 },
  ],
Rappresenta: Tutto il traffico nazionale su un cluster


### total.yaml e stress.yaml ###
### MODIFICA OPZIONALE: Sleep più aggressivo
Trova questa riga:


javascript
sleep(0.1);  // Sleep attuale (0.1 secondi)
Per simulare utenti più realistici, cambia in:


javascript
sleep(Math.random() * 2 + 1);  //  Sleep casuale 1-3 secondi
Motivo: Gli utenti reali non fanno 10 richieste/secondo, ma aspettano tra un'azione e l'altra.


       
### PARAMETRI LOAD TEST - MODIFICA QUI PER CAMBIARE STRESS

duration: Quanto dura ogni fase (es: 30s, 1m, 5m, 10m)
target: Quanti Virtual Users (VUs) simultanei
        
  ESEMPI:
         - Test leggero:  { duration: '2m', target: 5 }
         - Test medio:    { duration: '5m', target: 20 }
         - Test pesante:  { duration: '10m', target: 50 }
         - Stress test:   { duration: '15m', target: 100 }
         - Spike test:    { duration: '1m', target: 200 }
        
        
    
      
### SOGLIE DI PERFORMANCE - MODIFICA PER TEST PIU RIGOROSI
    
      http_req_duration: Tempo risposta richieste HTTP
      - p(95)<2000 = 95% richieste sotto 2 secondi
      - Valori piu stringenti: p(95)<1000, p(95)<500
      
      http_req_failed: Percentuale errori
      - rate<0.10 = massimo 10% di errori tollerati
      - Valori piu stringenti: rate<0.05 (5%), rate<0.01 (1%)
    
### VERIFICA DASHBOARD GRAFANA
	1	Menu → Dashboards → Import
	2	ID: 19665
	3	Load → Prometheus: Prometheus → Import
