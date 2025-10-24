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

<img width="1014" height="656" alt="img1" src="https://github.com/user-attachments/assets/0068d4c4-0450-4307-b19f-d6930197c836" />

- Vá em **Preferences → Container Engine** e selecione **containerd**

<img width="1015" height="657" alt="img2" src="https://github.com/user-attachments/assets/62f546c7-16b6-469f-bd89-0adc189838a6" />

Essas configurações garantem que o Kubernetes esteja ativo e que o Rancher Desktop use o container runtime compatível com o ArgoCD.

Verifique o contexto ativo do Kubernetes:

```bash
kubectl config get-contexts
```

Saída esperada:

<img width="737" height="83" alt="Image" src="https://github.com/user-attachments/assets/e06a0517-9395-4466-b019-a5633c9a6800" />

---

## 2️⃣ GitHub

### 🍴 Fork do Repositório Base

O fork é uma cópia independente de outro repositório, criada na sua conta GitHub.  
Você o utilizará para ter acesso total aos arquivos e poder modificá-los sem alterar o original.

👉 https://github.com/GoogleCloudPlatform/microservices-demo

<img width="1232" height="307" alt="img3" src="https://github.com/user-attachments/assets/c0ebaccd-7cac-4cb0-8be3-44e1e8c04537" />

Após criar o fork, ele aparecerá no seu perfil do GitHub. Essa será a sua base de onde buscará o manifesto.

### 🧱 Criação do seu Repositório GitOps

Crie um **novo repositório público** na sua conta GitHub. Esse repositório armazenará apenas os manifestos que o ArgoCD sincronizará com o cluster local.

<img width="935" height="148" alt="img4" src="https://github.com/user-attachments/assets/1db9cd2d-b104-41de-83d9-76513191c209" />

---

### 🖥 Configurar Repositório Local

Configure o Git (apenas na primeira vez):

```bash
git config --global user.name "seu-nome-aqui"
git config --global user.email "seu-email-aqui@gmail.com"
```

Clone o repositório que você criou:

```bash
cd ~/Documents
git clone https://github.com/VitoriaAmelia/appgitops-projeto2
cd appgitops-projeto2
```

Crie a estrutura de pastas e arquivos:

```bash
cd ~/Documents
git clone https://github.com/VitoriaAmelia/appgitops-projeto2
cd appgitops-projeto2
mkdir k8s
cd k8s
code online-boutique.yaml
```

Cole o conteúdo do YAML do fork no arquivo que será aberto no editor de código (ou baixe e adicione na pasta k8s):

<img width="1075" height="657" alt="Image" src="https://github.com/user-attachments/assets/45a3c74c-75b6-495e-a7df-694263ae02f3" />

Depois, volte ao terminal e faça commit e push:

```bash
git add .
git commit -m "Adiciona manifesto do Online Boutique"
git push origin main
```

Sua estrutura deve ser:

<img width="342" height="249" alt="img3" src="https://github.com/user-attachments/assets/6b76caf5-d88d-415d-94ae-f88cb06c945f" />

---

## 3️⃣ Instalar o ArgoCD no Cluster Local

Crie o namespace e siga os passos para instalação:

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

Verifique se os pods estão rodando corretamente:

```bash
kubectl get pods -n argocd
```

Saída esperada:

<img width="850" height="172" alt="img4(pods-running)" src="https://github.com/user-attachments/assets/37675f4c-cf6d-4155-8a9c-a5c3158e1f16" />

---

## 4️⃣ Acesso ao ArgoCD

Deixe este terminal aberto para encaminhar a porta:

```bash
kubectl -n argocd port-forward svc/argocd-server 8080:443
```

<img width="706" height="143" alt="img5" src="https://github.com/user-attachments/assets/80c73e68-6342-4699-b142-e99353e0e4a3" />

Abra outro terminal e adquira a senha de acesso:

```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}"
```

Decodifique a senha usando a saída do último comando:

```bash
[System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String("saída_do_último_comando_aqui"))
```

<img width="718" height="135" alt="img6" src="https://github.com/user-attachments/assets/3c54e6d1-819a-4144-9acd-31d1d1897064" />

Agora, acesse: https://localhost:8080  
Login: `admin`  
Senha: a que foi adquirida acima

---

### 🛠 Criando o Aplicativo no ArgoCD

Na interface do ArgoCD, clique em **"New App"** e preencha conforme a tabela:

| Campo | Valor |
|-------|--------|
| Application Name | Projeto-Gitops |
| Project | default |
| Sync Policy | Automatic |
| Opções | Prune, Self Heal, Auto-Finalizer, Auto-Create Namespace |
| Repository URL | https://github.com/VitoriaAmelia/appgitops-projeto2 |
| Revision | main |
| Path | k8s |
| Cluster URL | https://kubernetes.default.svc |
| Namespace | default |

<img width="1056" height="633" alt="img7-substituta" src="https://github.com/user-attachments/assets/7f6e40ec-d8ea-4083-85cc-e4702fdd8e7d" />
<img width="1064" height="630" alt="img8" src="https://github.com/user-attachments/assets/18188a25-9154-4e79-9a84-408f460de67b" />

#### Ajustando o Estado Health

1. Edite o serviço `frontend` no YAML, mudando de **LoadBalancer** para **NodePort**.

<img width="741" height="306" alt="img9 1" src="https://github.com/user-attachments/assets/b96bc1da-9b35-4b4f-bde5-488b460407f2" />

2. Faça o commit da alteração e aguarde a sincronização automática.

<img width="558" height="414" alt="img9-substitta" src="https://github.com/user-attachments/assets/d9676815-c9ea-49f7-800d-a45099f04939" />

---

## 5️⃣ Acessar o Frontend

Encaminhe a porta do serviço `frontend` para acesso local:

```bash
kubectl port-forward svc/frontend 8081:80
```

<img width="541" height="139" alt="img10" src="https://github.com/user-attachments/assets/7ea97389-3bbc-4aa1-995b-0c8dc01308e8" />

Acesse: http://localhost:8081

<img width="1364" height="683" alt="img11" src="https://github.com/user-attachments/assets/8f3133fd-7fed-48c2-b9a6-329aee726d27" />

---

## 🔐 Conectando Repositório Privado ao ArgoCD

### 1️⃣ Tornar o Repositório Privado

<img width="1333" height="205" alt="img1" src="https://github.com/user-attachments/assets/19a923b7-01b8-4c7e-a21b-54455ab1cfd0" />
<img width="873" height="183" alt="img2" src="https://github.com/user-attachments/assets/413a1619-f5f6-456f-b45d-1d0e220412c8" />

---

### 2️⃣ Criar um Personal Access Token (PAT)

<img width="1335" height="637" alt="token" src="https://github.com/user-attachments/assets/df154687-ea48-4e19-9926-674ab3ca4ce7" />

---

### 3️⃣ Conectar Repositório Privado ao ArgoCD

Abra o painel do ArgoCD → **Settings → Repositories** → **+ CONNECT REPO**.

<img width="1342" height="324" alt="img3" src="https://github.com/user-attachments/assets/bcc60635-8fcd-4a1c-8e24-ea09ed434f81" />
<img width="1341" height="250" alt="img4" src="https://github.com/user-attachments/assets/04642d17-6a32-4747-b5b1-4cdcdfba3a45" />

Preencha conforme abaixo:

| Campo | Valor |
|-------|-------|
| Repository URL | https://github.com/VitoriaAmelia/appgitops-projeto2.git |
| Username | seu usuário GitHub |
| Password | token (PAT) |

<img width="1029" height="597" alt="img5" src="https://github.com/user-attachments/assets/a91b9dbd-ef80-498d-b286-f707e1ba6181" />
<img width="963" height="321" alt="img6" src="https://github.com/user-attachments/assets/12b5dea0-9002-4f99-af4f-58a5b16f7b4d" />
<img width="1029" height="193" alt="img7" src="https://github.com/user-attachments/assets/61929422-41ff-424e-a640-642868dd8e3e" />
