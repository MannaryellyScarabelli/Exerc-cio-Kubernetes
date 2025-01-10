# Exercícios Kubernetes - CompassUOL

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
