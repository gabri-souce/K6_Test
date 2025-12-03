# ===================================================================
# K6 LOAD TESTING MANAGEIQ
# ===================================================================

##  TARGET ATTUALE

**IP:** 10.1.98.72 (tcosprmimiq04)
**Credenziali:** admin / smartvm

---

##  DEPLOYMENT RAPIDO (3 COMANDI)

```bash

# Deploy test
kubectl apply -f k6-manageiq-total.yaml

# Monitora
kubectl logs -f k6-manageiq-cloud -n k6-tests
```
##  CLEANUP

```bash
# Cancella test singolo
kubectl delete pod k6-manageiq-cloud -n k6-tests

# Cancella namespace
kubectl delete namespace k6-tests

---

##  FILE DISPONIBILI 
(Nota: il file "total" ha la lista completa delle chiamate API mentre il file "stress" ha soltanto quelle realmente funzionanti)

| File | VUs | Durata | Uso |
|------|-----|--------|-----|
| prod | 5 | 3 min | Test baseline |
| stress | 100 | 6.5 min | Test carico pesante |
| provisioning | 10 | 4.5 min | Test provisioning VM |
| network-storage | 10 | 4 min | Test network/storage |

---

### DUE MODIFICHE NECESSARIE! (in quanto i test puntano all' indirizzo 10.1.98.72)

#### 1. Variabile Ambiente 

```yaml
env:
  - name: MANAGEIQ_URL 
    value: "https://10.1.98.72"  # ← CAMBIA QUI
```

#### 2. CONFIGMAP DATI CSV  (Lista completa degli endpoint ManageIQ - Dovra essere cambianto anche l'indirizzo di tutte le chiamate api)

    data:
      api_calls_complete.csv: |

---

##  GRAFANA DASHBOARD

# Browser (esempio)
http://localhost:3000

# Login (esempio)
User: admin
Pass: prom-operator

# Import Dashboard
ID: 19665
```

### Grafana "No Data"

```bash
# Verifica output Prometheus in logs K6
kubectl logs k6-manageiq-cloud -n k6-tests | grep "output:"
# Deve mostrare: "output: Prometheus remote write"

# Test Prometheus Remote Write
kubectl port-forward -n monitoring svc/8dcmp-mon-kube-prometheus-st-prometheus 9090:9090 &
curl -X POST http://localhost:9090/api/v1/write  # Deve rispondere 400
```

### Errori 404/400 (indica che la tolleranza di errori delle chiamate API è stata superata,tale tolleranza è modificabile)

**È normale!** Tolleranza: < 15%


---

##  INTERPRETAZIONE RISULTATI

```
Cerca nei logs finali:

✓ http_req_duration p(95): < 2000ms = OK
✓ http_req_failed: < 10% = OK
✓ checks: > 90% = OK

http_reqs: Più alto = Meglio
```

### Istanze Specifiche

```yaml
value: "https://10.1.98.69"  # istanza 01
value: "https://10.1.98.70"  # istanza 02
value: "https://10.1.98.71"  # istanza 03
value: "https://10.1.98.72"  # istanza 04 (attuale)
```

---

##  CHECKLIST PRE-DEPLOY

- [ ] Cluster Kubernetes accessibile
- [ ] Namespace monitoring esiste
- [ ] Service Prometheus: `8dcmp-mon-kube-prometheus-st-prometheus`
- [ ] IP target raggiungibile: `curl -k https://10.1.98.72/api`
- [ ] Credenziali corrette
- [ ] File YAML aggiornato (se cambio IP)

