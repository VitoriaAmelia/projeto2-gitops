# 🚀 Projeto GitOps - Online Boutique

## 🧩 Pré-requisitos

Antes de começar, instale e configure:

- [Rancher Desktop](https://rancherdesktop.io/)
- [kubectl](https://kubernetes.io/docs/tasks/tools/)
- [Git](https://git-scm.com/)
- [GitHub](https://github.com/)

---

## 1️⃣ Rancher Desktop

Baixe o Rancher Desktop:  
👉 [https://rancherdesktop.io/](https://rancherdesktop.io/)

### Configurações necessárias

- Vá em **Preferences → Kubernetes** e verifique se está **enabled**
  
<img width="1365" height="716" alt="Image" src="https://github.com/user-attachments/assets/38777acd-87e1-47ab-b235-172bc9a6a04c" />


- Vá em **Preferences → Container Engine** e selecione **containerd**  


<img width="1365" height="717" alt="Image" src="https://github.com/user-attachments/assets/cafbd489-6021-4ecc-8433-a0664540b65a" />


No terminal, execute para verificar os contextos do Kubernetes:


```bash
kubectl config get-contexts
```

A saída deve ser:


<img width="737" height="83" alt="Image" src="https://github.com/user-attachments/assets/e06a0517-9395-4466-b019-a5633c9a6800" />

---

## 2️⃣ GitHub

### Fork do repositório base

Crie um **fork** do repositório oficial do projeto:  
👉 [https://github.com/GoogleCloudPlatform/microservices-demo](https://github.com/GoogleCloudPlatform/microservices-demo)


<img width="1326" height="574" alt="Image" src="https://github.com/user-attachments/assets/f5f69c8f-2272-42c0-9898-21eb43b4bad3" />

### Criação do repositório próprio

Crie um **novo repositório** na sua conta GitHub:  

<img width="946" height="527" alt="Image" src="https://github.com/user-attachments/assets/98fa7cd8-16db-42e7-8699-00450bed4727" />


Dê um nome (ex: `gitops-projeto`) e mantenha **público**  


<img width="847" height="580" alt="Image" src="https://github.com/user-attachments/assets/02798c40-dae3-4cd3-a64c-8d89e37b06e1" />


---

### Configurando o repositório local no Power Shell

Se ainda não estiver autenticado no GitHub localmente:

```bash
git config --global user.name "seu-nome-aqui"
git config --global user.email "seu-email-aqui@gmail.com"
```

Em seguida:

```bash
cd ~/Documents
git clone https://github.com/VitoriaAmelia/appgitops-projeto2
cd appgitops-projeto2
mkdir k8s
cd k8s
code online-boutique.yaml
```

Depois que o VsCode abrir, cole o conteúdo econtrado no link e salve:  
👉 [https://github.com/GoogleCloudPlatform/microservices-demo/blob/main/release/kubernetes-manifests.yaml](https://github.com/GoogleCloudPlatform/microservices-demo/blob/main/release/kubernetes-manifests.yaml)

Depois, volte ao terminal e execute:

```bash
git add .
git commit -m "Adiciona manifesto do Online Boutique"
git push origin main
```

---

## 3️⃣ Instalar o ArgoCD no Cluster Local

No **PowerShell**, execute:

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

Verifique se os pods estão rodando:

```bash
kubectl get pods -n argocd
```

---

## 4️⃣ Acessar o ArgoCD Localmente

Deixe esta janela aberta (pode ser em segundo plano):

```bash
kubectl -n argocd port-forward svc/argocd-server 8080:443
```

Abra **outro PowerShell** para continuar.

Pegue as credenciais do ArgoCD:

```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}"
```

Decodifique a senha retornada:

```bash
[System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String("Resposta_recebida_do_comando_anterior"))
```

A senha será a saída do último comando.

Abra no navegador:  
🔗 [https://localhost:8080](https://localhost:8080)

Login:
- Usuário: **admin**
- Senha: *(obtida acima)*

---

### Criando o aplicativo no ArgoCD

Na interface, clique em **"New App"** e preencha os seguintes campos:

| Campo | Valor |
|-------|--------|
| **Application Name** | Projeto-Gitops |
| **Project** | default |
| **Sync Policy** | Automatic |
| **Opções** | Prune Resources, Self Heal, Set Deletion Finalizer, Auto-Create Namespace |
| **Repository URL** | URL do seu repositório GitHub |
| **Revision** | main |
| **Path** | k8s |
| **Cluster URL** | https://kubernetes.default.svc |
| **Namespace** | default |

mostrar telas da configuração

mostrar tela de sinc

Para que seu estado fique healthy:

1 - localize LoadBalancer no código Yaml da Online Boutique

img do localizador

2 - mude para NodePort e salve

img de mudado

3 - Faça comit e verifique a mudança:

img healthy

---

## 5️⃣ Acessar o Frontend

Para expor o serviço frontend:

```bash
kubectl port-forward svc/frontend 8081:80
```

Agora, acesse no navegador:  
👉 [http://localhost:8081](http://localhost:8081)

Seu tudo ocorreu bem, aqui será possível visualizar o app


---
## 🔐 Conectando repositório privado ao Argo CD

### Deixe o repositório privado
Acesse:
`GitHub → Settings → General → Danger Zone → Change repository visibility → Private`

---

### 2️⃣ Gere um Personal Access Token (PAT)

Caminho:
`GitHub → Settings → Developer settings → Personal access tokens → Tokens (classic) → Generate new token (classic)`

Dê um nome para o token (exemplo: `argo-access`) e marque as permissões:

✅ `repo` — acesso total a repositórios privados  
✅ `read:packages` — leitura de pacotes (opcional)

Depois clique em **Generate token**  
Copie o token gerado (exemplo: `ghp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`) — guarde com segurança.

---

### 3️⃣ Configure o repositório no Argo CD

1. Acesse o painel do **Argo CD**  
2. Vá no menu lateral → **Settings ⚙️ → Repositories**
3. Clique em **“+ CONNECT REPO”**
4. Escolha **HTTPS**
5. Preencha:

| Campo | Valor |
|-------|-------|
| **Repository URL** | `https://github.com/seu-usuario/gitops-projeto.git` |
| **Username** | seu nome de usuário do GitHub |
| **Password** | o token gerado (PAT) |

6. Clique em **CONNECT**

7. Espere sincronizar 
