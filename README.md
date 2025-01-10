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

