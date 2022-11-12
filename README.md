# Sectiunea 8 - Jobs & Docker Containers

## GitHub Actions & Containers

- In GitHub Actions un job poate rula pe o masina preconfigurata (Just the Runner) sau intr-un container pe care il configureaza utilizatorul
- Lista masinilor preconfigurate si software-ul preinstalat pe fiecare din ele: [Runners](https://docs.github.com/en/actions/using-github-hosted-runners/about-github-hosted-runners)
- Containerele definite de utilizator ruleaza si ele pe un runner, dar pasii job-ului vor fi rulati in interiorul containerului.
- Se pot create servicii suplimentare (Services), care sunt containere suplimentare utilizate in pasi (ex. Testing Database)
- 

## Configurare acces la MongoDB cloud

- A se vedea [documentatia](https://github.com/liviu-moraru/github-actions-06-env)
- A fost creat in repo, environmentul **testing** (posibil doar pt. repo publice)
- Au fost definite var. de mediu pt. **testing**: MONGODB_USERNAME si MONGODB_PASSWORD

## Put a job to run into container

```yaml
jobs:
  test:
    environment: testing
    runs-on: ubuntu-latest
    container: node:16
```
- node:16 este o imagine existenta in Docker Hub

- Alta varianta este:

```yaml
jobs:
  test:
    environment: testing
    runs-on: ubuntu-latest
    container: 
      image: node:16
      env:
```
**env** declara variabilele de mediu pentru container (folosite la lansarea containerului)

## Service Containers

- Un job (fie daca ruleaza intr-un runner sau intr-un container), poate declara un "service container", adica un container care se lanseaza odata cu job-ul.
- De utilizat mai ales pt. un job de test care foloseste de exemplu o baza de date. In loc sa lucreze cu baza de date de productie, se testeaza o baza de date care ruleaza intr-un container.

### Exemplu pt. un job care ruleaza intr-un container

- In job se adauga cuvantul cheie **services** cu specificatiile imaginii pt. service container si variabilele de mediu necesare containerului (daca e cazul).
- Exemplu: imaginea mongo, care necesita variabile de mediu

```yaml
jobs:
  test:
    environment: testing
    runs-on: ubuntu-latest
    container: node:16
      # image: node:16
      # env:
    env:
      # a se vedea mai jos
      PORT: 8080
    services:
      mongodb:
        image: mongo
        env:
          MONGO_INITDB_ROOT_USERNAME: root
          MONGO_INITDB_ROOT_PASSWORD: example
```
- Setarea variabilelor de mediu pt. jobul de test pt. a accesa service containerul (mongo)
- Daca jobul ruleaza si el intr-un container, in host (runner), containerul de job poate accesa containerul serviciului prin numele serviciului definit in **services**
- Adica, probabil, runner-ul creaza un cluster de containere si un DNS server.
- De asemenea portul implicit sub care ruleaza daemonul mongo in service container (27017) este externalizat pentru containerul care ruleaza jobul
- Connection string-ul de acces la daemonul mongodb este `mongodb://root:example@mongo:27017/` (pt. user-ul si passw specificate)
- Astfel ca variabilele de mediu pt. job trebuie setate astfel:

```yaml
env:
  MONGODB_CONNECTION_PROTOCOL: mongodb # Partea mongodb:// din connection string
  MONGODB_CLUSTER_ADDRESS: mongodb # Adresa DNS a serverului (partea @mongo din connection string). mongodb este numele serviciului (vezi mai sus)
  MONGODB_USERNAME: root
  MONGODB_PASSWORD: example
```

- Configurare finala

```yaml
jobs:
  test:
    environment: testing
    runs-on: ubuntu-latest
    container: node:16
      
    env:
      MONGODB_CONNECTION_PROTOCOL: mongodb
      MONGODB_CLUSTER_ADDRESS: mongodb
      MONGODB_USERNAME: root
      MONGODB_PASSWORD: example
      PORT: 8080
    services:
      mongodb:
        image: mongo
        env:
          MONGO_INITDB_ROOT_USERNAME: root
          MONGO_INITDB_ROOT_PASSWORD: example
```
