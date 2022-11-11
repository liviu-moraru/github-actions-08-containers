# Sectiunea 8 - Jobs & Docker Containers

## GitHub Actions & Containers

- In GitHub Actions un job poate rula pe o masina preconfigurata (Just the Runner) sau intr-un container pe care il configureaza utilizatorul
- Lista masinilor preconfigurate si software-ul preinstalat pe fiecare din ele: [Runners](https://docs.github.com/en/actions/using-github-hosted-runners/about-github-hosted-runners)
- Containerele definite de utilizator ruleaza si ele pe un runner, dar pasii job-ului vor fi rulati in interiorul containerului.
- Se pot create servicii suplimentare (Services), care sunt containere suplimentare utilizate in pasi (ex. Testing Database)
- 
