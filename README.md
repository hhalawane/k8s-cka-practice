# 🧠 CKA Practice Environment

Welkom in mijn Kubernetes-experimenteerzone – een pragmatisch oefenplatform ter voorbereiding op de **Certified Kubernetes Administrator (CKA)** certificering.

Hier test ik scenario's, breek ik bewust configuraties, en herstel ik ze met doelgerichte precisie. Alles draait om het opbouwen van échte ervaring met Kubernetes – direct in de terminal.

---

## ⚙️ Doel van deze repository

Dit is géén productieklare setup.

Het is een hands-on labomgeving waarin:

- Alle YAML's met zorg worden opgebouwd in `vim`  
- Elke `kubectl`-opdracht met de hand wordt ingevoerd (inclusief typfouten 😉)  
- Alles draait in lichte, tijdelijke **kind**-clusters (Kubernetes in Docker)  
- De focus ligt op begrijpen, debuggen, en beheersen – niet op shortcuts  

Beschouw dit als mijn persoonlijke Kubernetes dojo: iedere fout is een les, iedere fix een stap vooruit.

---

## 🔁 Typische Workflow

```bash
kind create cluster
vim deployment.yaml
kubectl apply -f deployment.yaml
# Debuggen als het misgaat (want dat gebeurt gegarandeerd)
```

Herhalen tot het *klikt* – en dan nog een keer.

---

## 📦 Wat je hier vindt

- 🧾 Voorbeelden van manifests (Deployments, Services, Ingress)
- 🔧 Realistische troubleshooting cases
- 🔐 RBAC configuraties
- 💾 Etcd back-ups & herstelprocedures
- ⬆️ Cluster upgrades & node management  
- 🎯 Alles georkestreerd vanuit de command line – zonder GUI’s of IDE’s

---

## 📚 Waarom deze aanpak?

Omdat de CKA niet vraagt naar je favoriete code editor –  
maar of je effectief kunt navigeren, analyseren en herstellen binnen een *pure CLI-omgeving*.

Dit is hoe ik mijn Kubernetes-vaardigheden op scherp zet, één `kubectl get pods` per keer.
