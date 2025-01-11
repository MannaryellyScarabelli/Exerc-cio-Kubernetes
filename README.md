# Exercícios Kubernetes - CompassUOL

Esta lista de exercício foi realizada em um terminal linux - Ubuntu, onde supriu todas as necessidades.


## My-pod

>Crie um pod chamado "my-pod" usando uma imagem simples como "nginx" e verifique seu estado com os comandos de monitoramento do Kubernetes.

Para iniciar é necessário ter docker e kubernetes (kind) instalados terminal linux, o que foi utilizado aqui foi o ubuntu.

- Abra o terminal e adicione o seguinte comando:
```
nano my-pod.yaml
```
- Adicione o seguinte script:
```
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
  - name: nginx
    image: nginx
    ports:
    - containerPort: 80
```
- Para aplicar o arquivo no cluster utilize o camando:
```
  kubectl apply -f my-deployment.yaml
```
- Agora realize a verificação com um comando do kubernetes:
```
kubectl get deployments
```

A saída será como na imagem abaixo

  ![image](https://github.com/user-attachments/assets/df9933e5-5a41-4545-b3c6-a807add2b564)


## My-deplyment

>Implante um Deployment chamado "my-deployment" com três réplicas de uma aplicação baseada na imagem "httpd". Atualize a imagem do Deployment para uma versão mais recente.

- Para iniciar é preciso criar um arquivo com o seguinte comando:
```
  nano my-deployment.yaml
```
- Adicione o seguinte script:
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: httpd
        image: httpd:2.4
        ports:
        - containerPort: 80
```
- Para criar o cluster do deplyment utilize o comando:
```
  kubectl apply -f my-deployment.yaml
```

![image](https://github.com/user-attachments/assets/ab5e4a45-2d88-44c4-b543-a15cb27fc117)


- Para verificar a implementação deplyment com cluster:
```
  kubectl get deployments
```
- Para atualizar a imagem do deployment use o comando:
```
  kubectl set image deployment/my-deployment httpd=httpd:2.5
```
- Para verificar o status de sua atualização use:
```
kubectl rollout status deployment/my-deployment
```

  ![image](https://github.com/user-attachments/assets/6aae2f76-666b-4ccf-b15d-d40cca57c7af)


## ConfigMap

>Crie um ConfigMap chamado "app-config" com uma variável de configuração personalizada. Monte o ConfigMap em um pod e verifique se o valor foi aplicado corretamente.

- Inicia com a criação de um arquivo no editor nano:
```
  nano app-config.yaml
```
- Adicione o script:
```
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  APP_MESSAGE: "Hello from ConfigMap"
```
- Implementação do cluster use:
```
kubectl apply -f app-config.yaml
```
- Verifique a criação do ConfigMap:
```
  kubectl get configmap
```
- Crie um pod para o ConfigMap:
```
nano pod-with-configmap.yaml
```
- Use o script:
```
apiVersion: v1
kind: Pod
metadata:
  name: configmap-pod
spec:
  containers:
  - name: app-container
    image: busybox
    command: ["/bin/sh", "-c", "env && sleep 3600"]
    env:
    - name: APP_MESSAGE
      valueFrom:
        configMapKeyRef:
          name: app-config
          key: APP_MESSAGE
```
- Criação do cluster:
```
  kubectl apply -f pod-with-configmap.yaml
```
- Verfique a criação:
```
kubectl get pods
```
- Agora verifique o valor do ConfigMap:
```
kubectl exec -it configmap-pod -- sh
```
- Use o comando abaixo dentro do pod acessado:
```
  env | grep APP_MESSAGE
```

![Captura de tela 2025-01-10 221404](https://github.com/user-attachments/assets/70d50341-7d10-46da-b02f-3cb5886646b3)
