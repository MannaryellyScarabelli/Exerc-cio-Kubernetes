# Exercícios Kubernetes - CompassUOL

Esta lista de exercício foi realizada em um terminal linux - Ubuntu, onde supriu todas as necessidades.

>Crie um pod chamado "my-pod" usando uma imagem simples como "nginx" e verifique seu estado com os comandos de monitoramento do Kubernetes.

## My-pod
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
- Agora verifique se a implementação foi criada com o comando:
```
kubectl get deployments
```
A saída será como na imagem abaixo
  ![image](https://github.com/user-attachments/assets/df9933e5-5a41-4545-b3c6-a807add2b564)
