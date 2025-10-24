# 🚀 Guia de Configuração GitOps com ArgoCD + Rancher Desktop

## 🧩 Pré-requisitos

Antes de começar, instale e configure:

- ✅ [Rancher Desktop](https://rancherdesktop.io/)
- ✅ [kubectl](https://kubernetes.io/docs/tasks/tools/)
- ✅ [Git](https://git-scm.com/)
- ✅ Conta no [GitHub](https://github.com/)

---

## 1️⃣ Rancher Desktop

📥 Baixe o Rancher Desktop:  
👉 https://rancherdesktop.io/

### ⚙️ Configurações necessárias

- Vá em **Preferences → Kubernetes** e verifique se está **enabled**

<img width="1365" height="716" alt="Image" src="https://github.com/user-attachments/assets/38777acd-87e1-47ab-b235-172bc9a6a04c" />

- Vá em **Preferences → Container Engine** e selecione **containerd**

<img width="1365" height="717" alt="Image" src="https://github.com/user-attachments/assets/cafbd489-6021-4ecc-8433-a0664540b65a" />

No terminal, execute:

```bash
kubectl config get-contexts
```

Saída esperada:

<img width="737" height="83" alt="Image" src="https://github.com/user-attachments/assets/e06a0517-9395-4466-b019-a5633c9a6800" />

---

## 2️⃣ GitHub

### 🍴 Fork do Repositório Base

👉 https://github.com/GoogleCloudPlatform/microservices-demo

<img width="1326" height="574" alt="Image" src="https://github.com/user-attachments/assets/f5f69c8f-2272-42c0-9898-21eb43b4bad3" />

### 🧱 Criação do seu Repositório

Crie um repositório público na sua conta GitHub:

<img width="946" height="527" alt="Image" src="https://github.com/user-attachments/assets/98fa7cd8-16db-42e7-8699-00450bed4727" />

---

### 🖥 Configurar Repositório Local

Configure o Git se necessário:

```bash
git config --global user.name "seu-nome-aqui"
git config --global user.email "seu-email-aqui@gmail.com"
```

Clone seu repositório e adicione o manifesto:

```bash
cd ~/Documents
git clone https://github.com/VitoriaAmelia/appgitops-projeto2
cd appgitops-projeto2
mkdir k8s
cd k8s
code online-boutique.yaml
```

Cole o conteúdo do YAML do fork ou baixe:

<img width="1075" height="657" alt="Image" src="https://github.com/user-attachments/assets/45a3c74c-75b6-495e-a7df-694263ae02f3" />

Comite e envie:

```bash
git add .
git commit -m "Adiciona manifesto do Online Boutique"
git push origin main
```

<img width="342" height="249" alt="img3" src="https://github.com/user-attachments/assets/6b76caf5-d88d-415d-94ae-f88cb06c945f" />

---

## 3️⃣ Instalar o ArgoCD no Cluster Local

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

Verificar pods:

```bash
kubectl get pods -n argocd
```

<img width="850" height="172" alt="img4(pods-running)" src="https://github.com/user-attachments/assets/37675f4c-cf6d-4155-8a9c-a5c3158e1f16" />

---

## 4️⃣ Acesso ao ArgoCD

Mantenha este terminal:

```bash
kubectl -n argocd port-forward svc/argocd-server 8080:443
```

<img width="706" height="143" alt="img5" src="https://github.com/user-attachments/assets/80c73e68-6342-4699-b142-e99353e0e4a3" />

Outro terminal:

Obtenha senha admin:

```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}"
```

Decodifique:

```bash
[System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String("Resposta_recebida_do_comando_anterior"))
```

<img width="718" height="135" alt="img6" src="https://github.com/user-attachments/assets/3c54e6d1-819a-4144-9acd-31d1d1897064" />

Acesse: https://localhost:8080  
Login: admin / senha obtida

---

### 🛠 Criando App no ArgoCD

Configurar conforme tabela:

| Campo | Valor |
|-------|--------|
| Application Name | Projeto-Gitops |
| Project | default |
| Sync Policy | Automatic |
| Opções | Prune, Self Heal, Auto-Finalizer, Auto-Create Namespace |
| Repository URL | Seu repositório GitHub |
| Revision | main |
| Path | k8s |
| Cluster URL | https://kubernetes.default.svc |
| Namespace | default |

<img width="1056" height="633" alt="img7-substituta" src="https://github.com/user-attachments/assets/7f6e40ec-d8ea-4083-85cc-e4702fdd8e7d" />
<img width="1064" height="630" alt="img8" src="https://github.com/user-attachments/assets/18188a25-9154-4e79-9a84-408f460de67b" />

#### Ajustando Health

1. Edite o serviço LoadBalancer → NodePort

<img width="741" height="306" alt="img9 1" src="https://github.com/user-attachments/assets/b96bc1da-9b35-4b4f-bde5-488b460407f2" />

2. Commit e sincronize

<img width="558" height="414" alt="img9-substitta" src="https://github.com/user-attachments/assets/d9676815-c9ea-49f7-800d-a45099f04939" />

---

## 5️⃣ Acessar o Frontend

```bash
kubectl port-forward svc/frontend 8081:80
```

<img width="541" height="139" alt="img10" src="https://github.com/user-attachments/assets/7ea97389-3bbc-4aa1-995b-0c8dc01308e8" />

Acesse: http://localhost:8081

<img width="1364" height="683" alt="img11" src="https://github.com/user-attachments/assets/8f3133fd-7fed-48c2-b9a6-329aee726d27" />

---

## 🔐 Conectando Repositório Privado ao ArgoCD

### 1️⃣ Tornar privado

<img width="1333" height="205" alt="img1" src="https://github.com/user-attachments/assets/19a923b7-01b8-4c7e-a21b-54455ab1cfd0" />
<img width="873" height="183" alt="img2" src="https://github.com/user-attachments/assets/413a1619-f5f6-456f-b45d-1d0e220412c8" />

---

### 2️⃣ Criar Personal Access Token

<img width="1335" height="637" alt="token" src="https://github.com/user-attachments/assets/df154687-ea48-4e19-9926-674ab3ca4ce7" />

---

### 3️⃣ Conectar no ArgoCD

<img width="1342" height="324" alt="img3" src="https://github.com/user-attachments/assets/bcc60635-8fcd-4a1c-8e24-ea09ed434f81" />
<img width="1341" height="250" alt="img4" src="https://github.com/user-attachments/assets/04642d17-6a32-4747-b5b1-4cdcdfba3a45" />

Configurar:

| Campo | Valor |
|-------|-------|
| Repository URL | https://github.com/seu-usuario/gitops-projeto.git |
| Username | usuário GitHub |
| Password | token (PAT) |

<img width="1029" height="597" alt="img5" src="https://github.com/user-attachments/assets/a91b9dbd-ef80-498d-b286-f707e1ba6181" />
<img width="963" height="321" alt="img6" src="https://github.com/user-attachments/assets/12b5dea0-9002-4f99-af4f-58a5b16f7b4d" />
<img width="1029" height="193" alt="img7" src="https://github.com/user-attachments/assets/61929422-41ff-424e-a640-642868dd8e3e" />
