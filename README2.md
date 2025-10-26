# 🚀 Gitops na prática - Programa de Bolsas - DevSecOps 


## 🎯 Objetivos do Projeto

  Implantar a aplicação Online Boutique em um ambiente Kubernetes local (Rancher Desktop) utilizando o modelo GitOps com ArgoCD, garantindo o controle de versionamento e sincronização automática entre o repositório Git e o cluster.
  O projeto tem como metas:
  - Criar um repositório Git público contendo a estrutura de manifests YAML da aplicação.
  - Realizar o deploy completo do ArgoCD.
  - Configurar e criar o aplicativo no ArgoCD, apontando para o repositório Git.
  - Sincronizar a aplicação e verificar que todos os pods estão em execução corretamente.
  - Acessar o frontend da aplicação através do comando kubectl port-forward, validando o funcionamento da interface web
    
---

## 🛠 Tecnologias Utilizadas

  - Kubernetes

    
    - Server Version: v1.33.5+k3s1

    
  - Rancher Desktop

    
    - Version: 1.20.0

    
  - ArgoCD

    
    - Version: v3.1.9+8665140

    
  - Git e GitHub
  - Kubectl

    
    - Client Version: v1.34.1

    
  - Containerd
  - YAML

      
---


## 🖥️ Ambiente de desenvolvimento utilizado

- Sistema operacional:  Windows 11 
- Versão: Microsoft Windows 11 Pro 10.0.26100
- Terminal: PowerShell

  
---

## 🧩 Pré-requisitos

Vão ser necessários para o desenvolvimento:

- ✅ [Rancher Desktop](https://rancherdesktop.io/) (mais sobre ao decorrer do documento)
- ✅ kubectl
- ✅ [Git](https://git-scm.com/)
- ✅ Conta no [GitHub](https://github.com/)

---

## 📝 Como Utilizar

  - Seguir a ordem do documento para alcançar o objetivo passo a passo 
  - Realizar as configurações conforme descrito nas etapas 

---


## 📚 Navegação
   - Rancher Desktop

   - GitHub
   
   - Instalar o ArgoCD
   
   - Acessar o ArgoCD
   
   - Acessar o Frontend
   
   - Conectar Repositório Privado ao ArgoCD (opcional)
   
   - Custumizar manifest (opcional)
   
---


## 1️⃣ Rancher Desktop

📥 Baixe o Rancher Desktop:  
👉 https://rancherdesktop.io/

### ⚙️ Configurações necessárias


- Vá em **Preferences → Kubernetes** e verifique se está **enabled**


<img width="644" height="486" alt="img1" src="https://github.com/user-attachments/assets/0068d4c4-0450-4307-b19f-d6930197c836" />


- Vá em **Preferences → Container Engine** e selecione **containerd**


<img width="645" height="487" alt="img2" src="https://github.com/user-attachments/assets/62f546c7-16b6-469f-bd89-0adc189838a6" />


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


👉 https://github.com/GoogleCloudPlatform/microservices-demo


<img width="1232" height="407" alt="img3" src="https://github.com/user-attachments/assets/c0ebaccd-7cac-4cb0-8be3-44e1e8c04537" />


Após criar o fork, ele aparecerá no seu perfil do GitHub. Essa será a sua base, de onde buscará o arquivo YAML.


### 🧱 Criação do seu Repositório GitOps


Crie um **novo repositório público** na sua conta GitHub. Esse repositório armazenará o arquivo utilizado pelo ArgoCD.


<img width="935" height="248" alt="img4" src="https://github.com/user-attachments/assets/1db9cd2d-b104-41de-83d9-76513191c209" />

---

### 🖥 Configurar Repositório Local

No terminal, configure o Git se necessário:


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


Cole o conteúdo do YAML do fork (ou baixe e adicione na pasta k8s):


<img width="1075" height="657" alt="Image" src="https://github.com/user-attachments/assets/45a3c74c-75b6-495e-a7df-694263ae02f3" />


Faça commit e envie:


```bash
git add .
git commit -m "Adiciona arquivo do Online Boutique"
git push origin main
```


Sua estrutura do repositório deve ser:


<img width="342" height="249" alt="img3" src="https://github.com/user-attachments/assets/6b76caf5-d88d-415d-94ae-f88cb06c945f" />

---


## 3️⃣ Instalar o ArgoCD

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

## 4️⃣ Acessar o ArgoCD


1. Deixe este terminal aberto para encaminhar a porta:


```bash
kubectl -n argocd port-forward svc/argocd-server 8080:443
```

Saída esperada:

<img width="706" height="143" alt="img5" src="https://github.com/user-attachments/assets/80c73e68-6342-4699-b142-e99353e0e4a3" />


2. Abra outro terminal e adquira a senha de acesso:


```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}"
```


3. Decodifique a senha usando a saída do último comando (necessário para PowerShell):


```bash
[System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String("saída_do_último_comando_aqui"))
```

Saída esperada:


<img width="718" height="135" alt="substituicao" src="https://github.com/user-attachments/assets/de582fe1-d253-420f-a904-e2669d87ff62" />


4. Agora, acesse: https://localhost:8080  

   Login: `admin`


   Senha: a que foi adquirida acima


Tela de acesso:


   <img width="956" height="622" alt="telalogin" src="https://github.com/user-attachments/assets/2352ce49-8911-467c-aea4-9a3ee69a8f48" />


---

### 🛠 Criando o Aplicativo no ArgoCD

Na interface do ArgoCD, clique em **"New App"** e preencha conforme a tabela:



| Campo | Valor |
|-------|--------|
| Application Name | onlineboutique |
| Project | default |
| Sync Policy | Automatic |
| Opções | Enable Auto-Sync, Prune Resources, Self Heal, Set Deletion Finalizer, Auto-Create Namespace |
| Repository URL | Sua URL do repositório GitHub |
| Revision | main |
| Path | k8s |
| Cluster URL | https://kubernetes.default.svc |
| Namespace | default |

Interface com as informações:


<img width="843" height="632" alt="img5" src="https://github.com/user-attachments/assets/1fcc141a-fcfa-40cc-80c4-c068b1e32eaa" />


<img width="845" height="630" alt="img6" src="https://github.com/user-attachments/assets/64a976a6-9149-4742-bd9b-1a0f0ecc4de8" />
ㅤ

<img width="919" height="306" alt="namespace" src="https://github.com/user-attachments/assets/ffb328ff-0e3d-4da7-b356-f3e0e68d76b4" />
ㅤ


#### Ajustando o Estado Health

1. Edite o serviço `frontend` no YAML, mudando de **LoadBalancer** para **NodePort** pois o projeto é somente local e não utiliza LoadBalancer.
   

<img width="741" height="306" alt="img9 1" src="https://github.com/user-attachments/assets/b96bc1da-9b35-4b4f-bde5-488b460407f2" />


2. Faça o commit da alteração e aguarde a sincronização automática.


O status deve indicar healthy como na imagem a seguir (é possível confirmar navegando no painel do ArgoCd, em Applications):

ㅤ
<img width="558" height="414" alt="img9-substitta" src="https://github.com/user-attachments/assets/d9676815-c9ea-49f7-800d-a45099f04939" />

---

## 5️⃣ Acessar o Frontend

1. No Powershell, configure o port-forward para acesso ao frontend:


```bash
kubectl port-forward svc/frontend 8081:80
```

Saída esperada:

<img width="541" height="139" alt="img10" src="https://github.com/user-attachments/assets/7ea97389-3bbc-4aa1-995b-0c8dc01308e8" />


2. Acesse: http://localhost:8081 no seu navegador


Saída esperada:


<img width="1364" height="683" alt="img11" src="https://github.com/user-attachments/assets/8f3133fd-7fed-48c2-b9a6-329aee726d27" />
ㅤ

Se tudo ocorreu bem, no navegador deve aparecer a tela desejada funcionando. 

---

## 6️⃣ Conectar Repositório Privado ao ArgoCD (opcional)

Também é possível fazer esse processo com um repositório privado, será necessário apenas realizar alguns passos a mais. Nesse exemplo, o ponto de partida vai ser o repositório público já existente. 


### 1. Tornar o repositório utilizado nos últimos passos, que era público, privado.


Para isso, vá em Settings --> Danger Zone ---> Change visibility


Observe:

<img width="1333" height="205" alt="img1" src="https://github.com/user-attachments/assets/19a923b7-01b8-4c7e-a21b-54455ab1cfd0" />


<img width="873" height="183" alt="img2" src="https://github.com/user-attachments/assets/413a1619-f5f6-456f-b45d-1d0e220412c8" />

ㅤ

### 2. Criar um Token


Para isso, vá em Settings → Developer settings → Personal access tokens → Generate new token (classic)


Será necessário anotar o token adquirido.


Observe o texto no bloco de cor verde na imagem:


<img width="1335" height="637" alt="token" src="https://github.com/user-attachments/assets/4401da79-977c-4316-a872-107a0eab49bf" />



### 3. Conectar Repositório Privado ao ArgoCD


Vá ao painel do ArgoCD → **Settings → Repositories** → **+ CONNECT REPO**.


Observe:


<img width="1342" height="324" alt="img3" src="https://github.com/user-attachments/assets/bcc60635-8fcd-4a1c-8e24-ea09ed434f81" />

ㅤ

<img width="1341" height="250" alt="img4" src="https://github.com/user-attachments/assets/04642d17-6a32-4747-b5b1-4cdcdfba3a45" />
ㅤ


Preencha os campos conforme abaixo:


| Campo | Valor |
|-------|-------|
| Repository URL | a url do seu repositório |
| Username | seu usuário GitHub |
| Password | token adquirido |


Observe:


<img width="1029" height="597" alt="img5" src="https://github.com/user-attachments/assets/a91b9dbd-ef80-498d-b286-f707e1ba6181" />
ㅤ


<img width="963" height="321" alt="img6" src="https://github.com/user-attachments/assets/12b5dea0-9002-4f99-af4f-58a5b16f7b4d" />
ㅤ


Confirme o Connection Status:


<img width="1029" height="193" alt="img7" src="https://github.com/user-attachments/assets/61929422-41ff-424e-a640-642868dd8e3e" />


Após realizar esses procedimentos: espere a sincronização, volte em Applications no painél e também na interface da aplicação para verificar se a situação do projeto continua favorável.


---

### 7️⃣ Custumizar manifest (opcional)

Para o teste, vamos mudar o número de réplicas do deployment do frontend. Escalar o frontend pode ser importante para lidar com um maior volume de tráfego.

Adicione a seguinte linha no seu arquivo YAMl para ajustar o número de réplicas para 3:


<img width="572" height="248" alt="custumizacao" src="https://github.com/user-attachments/assets/bd856e4f-630d-4252-94f6-d36e7d60625c" />


Com o comando a seguir, no PowerShell, é possível ver o antes e depois da mudança:


```bash
 kubectl get deployments
```


Saída antes:


<img width="612" height="273" alt="custantes" src="https://github.com/user-attachments/assets/470c9ec7-ec53-413b-8c67-bb3326e9636f" />


Saída depois:


<img width="610" height="266" alt="custdepois" src="https://github.com/user-attachments/assets/83ed634f-44eb-41c6-b5f4-243b69603618" />


É possível ajustar o número de réplicas de acordo com o desejado, não necessariamente precisa ser 3.

Aguarde um momento para que haja a sincronização automática e verifique o estado do projeto no Argo CD. Se estiver healthy e for possível verificar a modificação com o comando anterior, o teste foi um sucesso!




