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

<img width="737" height="83" alt="Image" src="https://github.com/user-attachments/assets/e06a0517-9395-4466-b019-a5633c9a6800" />

---

## 2️⃣ GitHub

### Fork do repositório base

Crie um **fork** do repositório oficial do projeto:  
👉 [https://github.com/GoogleCloudPlatform/microservices-demo](https://github.com/GoogleCloudPlatform/microservices-demo)

<img width="663" height="287" alt="Image" src="https://github.com/user-attachments/assets/f5f69c8f-2272-42c0-9898-21eb43b4bad3" />

### Criação do repositório próprio

Crie um **novo repositório** na sua conta GitHub:  

<img width="473" height="263" alt="Image" src="https://github.com/user-attachments/assets/98fa7cd8-16db-42e7-8699-00450bed4727" />

Dê um nome (ex: `projetinho-teste`) e mantenha **público**  

<img width="423" height="290" alt="Image" src="https://github.com/user-attachments/assets/02798c40-dae3-4cd3-a64c-8d89e37b06e1" />

Estrutura desejada do repositório:  

<img width="371" height="68" alt="Image" src="https://github.com/user-attachments/assets/672c7c13-1863-40a8-9b54-10992f930062" />
