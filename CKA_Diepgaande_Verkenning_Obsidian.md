# 🧠 CKA Diepgaande Verkenning (Dag 6–15)
_Afgestemd op Mumshad's Udemy-cursus_

## ✅ Checklist Overzicht
- [x] Dag 6 – Scheduling
- [x] Dag 7 – Logging & Monitoring
- [x] Dag 8 – Cluster Maintenance
- [ ] Dag 9 – Security I (RBAC)
- [ ] Dag 10 – Security II (SA, Secrets)
- [ ] Dag 11 – Storage I
- [ ] Dag 12 – Storage II
- [ ] Dag 13 – Troubleshooting
- [ ] Dag 14 – Core Concepts
- [ ] Dag 15 – Herhaling & Consolidatie

---

## 📅 Dag 6 – Scheduling
- Sectie: `Section 4 – Scheduling`
- 📌 Topics:
  - nodeSelector
  - Taints & Tolerations
  - Affinity/Anti-affinity
  - Static Pods
- 🔧 Labs:
  - Maak pods met nodeSelector
  - Plaats pod op tainted node met toleration
  - Deploy static pod naar master node

---

## 📅 Dag 7 – Logging & Monitoring
- Sectie: `Section 5 – Logging & Monitoring`
- 📌 Topics:
  - `kubectl logs`
  - `kubectl describe`, `events`, `top`
- 🔧 Labs:
  - Analyseer falende pods met logs & describe
  - Installeer metrics-server en test met `kubectl top`

---

## 📅 Dag 8 – Cluster Maintenance
- Sectie: `Section 6 – Cluster Maintenance`
- 📌 Topics:
  - Cluster upgrades via `kubeadm`
  - `etcd` backup & restore
  - Certificaten beheren
- 🔧 Labs:
  - Voer een `etcdctl snapshot save` uit
  - Valideer certs met `kubeadm certs check-expiration`

---

## 📅 Dag 9 – Security I (RBAC)
- Sectie: `Section 7 – Security`
- 📌 Topics:
  - Roles, RoleBindings
  - ClusterRoles
  - `kubectl auth can-i`
- 🔧 Labs:
  - Maak Role voor alleen lezen op pods
  - Beperk gebruiker tot bepaalde namespace

---

## 📅 Dag 10 – Security II (SA, Secrets)
- Sectie: `Section 7 – Security`
- 📌 Topics:
  - Service Accounts
  - Secrets & ConfigMaps
- 🔧 Labs:
  - Maak pod met specifieke SA
  - Mount secrets via volume en env vars

---

## 📅 Dag 11 – Storage I
- Sectie: `Section 8 – Storage`
- 📌 Topics:
  - Volumes: emptyDir, hostPath
  - PV & PVC
- 🔧 Labs:
  - Handmatig PV en PVC aanmaken
  - Pod koppelen aan PVC

---

## 📅 Dag 12 – Storage II
- Sectie: `Section 8 – Storage`
- 📌 Topics:
  - StorageClass
  - Dynamic Provisioning
- 🔧 Labs:
  - Test dynamische PVC met StorageClass
  - Debug wanneer PVC niet bound

---

## 📅 Dag 13 – Troubleshooting
- Sectie: `Section 9 – Troubleshooting`
- 📌 Topics:
  - Foutmeldingen oplossen
  - Resource limits, CrashLoopBackOff
- 🔧 Labs:
  - Simuleer een foute image of onbestaande command

---

## 📅 Dag 14 – Core Concepts
- Sectie: `Section 10 – Core Concepts`
- 📌 Topics:
  - Namespaces, Deployments
  - Services, Pods
- 🔧 Labs:
  - Basis YAML maken zonder `kubectl create`
  - Resource beheren met `kubectl apply -f`

---

## 📅 Dag 15 – Herhaling & Consolidatie
- 🔁 Herhaal moeilijke onderwerpen
- 🧪 Doe een mock examen of killer.sh
- 📚 Bookmark de juiste docs-pagina’s
