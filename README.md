# Exercícios - CompassUOL

![compassUOL](https://github.com/user-attachments/assets/fd730623-8fe6-42af-976c-5375f14e0b3a)


Esta lista de exercício foi realizada em um terminal linux - Ubuntu, onde supriu todas as necessidades exigidas.


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


## Secret

>Crie um Secret chamado "app-secret" contendo informações sensíveis. Injete o Secret como uma variável de ambiente em um pod e teste se está acessível.

- O primeiro passo para começar é realizar a criação de um arquivo, para isso utilize o comando:
```
nano app-secret.yaml
```
- Adicione o seguinete script no editor nano:
```
  apiVersion: v1
kind: Secret
metadata:
  name: app-secret
type: Opaque
data:
  DB_USERNAME: YWRtaW4=     # Base64 de "admin"
  DB_PASSWORD: cGFzc3dvcmQ= # Base64 de "password"
```
- Para apçlicação cluster utilize:
```
  kubectl apply -f app-secret.yaml
```
- Verifique a criação do secret:
```
  kubectl get secrets
```
- Criação de um pod para agregar o secret, use o comando:
```
  nano pod-with-secret.yaml
```
- Adicione o script:
```
  apiVersion: v1
kind: Pod
metadata:
  name: secret-pod
spec:
  containers:
  - name: app-container
    image: busybox
    command: ["/bin/sh", "-c", "env && sleep 3600"]
    env:
    - name: DB_USERNAME
      valueFrom:
        secretKeyRef:
          name: app-secret
          key: DB_USERNAME
    - name: DB_PASSWORD
      valueFrom:
        secretKeyRef:
          name: app-secret
          key: DB_PASSWORD
```
- Aplicação do pod ao cluster com o comando:
```
  kubectl apply -f pod-with-secret.yaml
```
- Verificação da criação do pod:
```
  kubectl get pods
```
- Testagem da acessibilidade so secret:
```
  kubectl exec -it secret-pod -- sh
```
- Dentro do pod utilize o camndo:
```
  env | grep DB_
```

![image](https://github.com/user-attachments/assets/eb0f1bc9-b94a-4574-acd3-e0f953be3d6f)


## Persistent Volume

>Configure um PersistentVolume de 1Gi de armazenamento local e vincule-o a um PersistentVolumeClaim. Monte o volume em um pod e salve arquivos para verificar a persistência.

- Para iniciar é preciso criar um aquivo persistentvolume:
```
  nano pv.yaml
```
- Adicione o seguinte script:
```
  apiVersion: v1
kind: PersistentVolume
metadata:
  name: local-pv
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/mnt/data"
```
- Aplicação no cluster:
```
  kubectl apply -f pv.yaml
```
- Verificação da criação:
```
  kubectl get pv
```
- Criação de um arquivo persistentvolumeclaim:
```
  nano pvc.yaml
```
- Adicione o script:
```
  apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: local-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```
- Aplicação no cluster:
```
  kubectl apply -f pvc.yaml
```
- Verificação da implementação do PVC ao PV:
```
  kubectl get pvc
```
Em STATUS deve aparecer  `Bound` como na imagem abaixo:

![image](https://github.com/user-attachments/assets/a2b44608-65db-4402-a0c5-bba86f4036e8)

- Criação de um arquivo para utilização de volume:
```
  nano pod-with-pvc.yaml
```
- Adicione o script:
```
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-volume
spec:
  containers:
  - name: app-container
    image: busybox
    command: ["/bin/sh", "-c", "while true; do sleep 3600; done"]
    volumeMounts:
    - name: local-storage
      mountPath: "/data"
  volumes:
  - name: local-storage
    persistentVolumeClaim:
      claimName: local-pvc
  ```
- Aplicação cluster:
 ```
 kubectl apply -f pod-with-pvc.yaml
 ```
 - Verificação da criação:
 ```
 kubectl get pods
 ```
- Para testagem da persistência acesse o pod:
```
kubectl exec -it pod-with-volume -- sh
```
- Criação do arquivo no volume montado:
```
echo "Hello, PersistentVolume!" > /data/test.txt
```
- Verifique a saída usando o comando:
```
cat /data/test.txt
```

![image](https://github.com/user-attachments/assets/8e16a95e-c806-4193-b9f9-be63f078a360)


## Cluster IP

>Crie um serviço do tipo ClusterIP para um Deployment chamado "backend" e teste a conectividade interna entre pods usando o nome do serviço.

- Para iniciar é necessário criar um deplyment:
```
  nano backend-deployment.yaml
```
- Adicione o script:
```
  apiVersion: apps/v1
kind: Deployment
metadata:
  name: backend
spec:
  replicas: 3
  selector:
    matchLabels:
      app: backend
  template:
    metadata:
      labels:
        app: backend
    spec:
      containers:
      - name: backend-container
        image: nginx
        ports:
        - containerPort: 80
```
- Aplicação no cluster:
```
  kubectl apply -f backend-deployment.yaml
```
- Verificação da criação:
```
  kubectl get pods
```
- Criação de um arquivo cluster IP:
```
  nano backend-service.yaml
```
- Adicione o script:
```
  apiVersion: v1
kind: Service
metadata:
  name: backend-service
spec:
  selector:
    app: backend
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
  type: ClusterIP
```
- Aplicação do serviço no cluster:
```
  kubectl apply -f backend-service.yaml
```
- Verifique a criação do serviço:
```
  kubectl get svc
```

![image](https://github.com/user-attachments/assets/3797ab57-1ca9-4971-86be-ceb670224375)


- Para testar a conectividade crie primeiro um pod:
```
kubectl run test-pod --image=busybox --restart=Never --command -- sleep 3600
```
- Verifique a criação:
```
  kubectl get pods
```
- Acesse o pod:
```
  kubectl exec -it test-pod -- sh
```
- Dentro do pod teste a conectividade com o comando:
```
  wget -qO- backend-service
```

![image](https://github.com/user-attachments/assets/f9b8cff9-f683-4059-bd2b-f616043a9ff7)


## Job

>Implante um Job chamado "batch-job" que execute um comando simples e termine. Verifique os logs do Job para confirmar sua execução.

- Inicialmente é realizado a criação de um arquivo:
```
  nano batch-job.yaml
```
- Adicione o script:
```
  apiVersion: batch/v1
kind: Job
metadata:
  name: batch-job
spec:
  template:
    metadata:
      name: batch-job
    spec:
      containers:
      - name: batch-job-container
        image: busybox
        command: ["sh", "-c", "echo 'Hello from Kubernetes Job!' && sleep 5"]
      restartPolicy: Never
  backoffLimit: 4
```
- Aplicação cluster:
```
  kubectl apply -f batch-job.yaml
```
- Verifique o status:
```
  kubectl get jobs
```

![image](https://github.com/user-attachments/assets/c747463d-f942-48a3-941d-a8739fd12ed5)

- Verificação da lista de pods gerados pelo Job:
```
  kubectl get pods
```

![image](https://github.com/user-attachments/assets/398cdbaa-bba1-4c2e-9c00-49a526e75f14)


- Verificação dos logs:
```
kubectl logs <NOME_DO_POD>
```

![image](https://github.com/user-attachments/assets/493a55a5-bd40-490e-95f7-5cc4de86f927)


## Pod Autoescaler

>Crie um Horizontal Pod Autoscaler para um Deployment chamado "hpa-deployment" e configure-o para escalar com base no uso de CPU. Aumente a carga e observe o escalonamento.

- Para iniciar criaremos um arquivo deplyment:
```
nano hpa-deployment.yaml
```
- Adicione o script:
```
  apiVersion: apps/v1
kind: Deployment
metadata:
  name: hpa-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: hpa-app
  template:
    metadata:
      labels:
        app: hpa-app
    spec:
      containers:
      - name: hpa-container
        image: nginx
        resources:
          requests:
            cpu: 200m
          limits:
            cpu: 500m
  ```
- Aplicação cluster:
 ```
  kubectl apply -f hpa-deployment.yaml
 ```
- Criação da implementação:
 ```
  kubectl autoscale deployment hpa-deployment --cpu-percent=50 --min=1 --max=10
 ```
- Verificação da criação:
 ```
  kubectl get hpa
 ```

![image](https://github.com/user-attachments/assets/e422ac8d-6319-4401-b49a-01f3b5548e6f)

- Criação do pod para aumentar a carga:
```
  nano stress-test.yaml
```
- Adicione o script:
```
  apiVersion: v1
kind: Pod
metadata:
  name: stress-test
spec:
  containers:
  - name: stress
    image: busybox
    command: ["sh", "-c", "while true; do yes > /dev/null; done"]
```
- Aplicação cluster:
```
  kubectl apply -f stress-test.yaml
```
- Agora monitore o escalonamento com o seguinte comando:
```
  kubectl get hpa -w
```

![image](https://github.com/user-attachments/assets/9744d87e-33c2-4d48-b69a-ef69fe807deb)


## NodePort

>Crie um serviço do tipo NodePort para expor externamente um Deployment chamado "webapp". Acesse o serviço usando o endereço IP do Minikube e a porta atribuída.

- Para iniciarmos a instalação do minikube acesse a documentação oficial:
  
  https://minikube.sigs.k8s.io/docs/start/?arch=%2Flinux%2Fx86-64%2Fstable%2Fbinary+download

- Crie um arquivo no editor nano:
 ```
    nano webapp-service.yaml
  ```
- Adicione o script:
 ```
  apiVersion: v1
kind: Service
metadata:
  name: webapp-service
spec:
  type: NodePort
  selector:
    app: webapp
  ports:
    - protocol: TCP
      port: 80        # Porta interna do serviço
      targetPort: 8080 # Porta do contêiner no Deployment
      nodePort: 30008  # Porta externa no nó (opcional, pode ser gerada automaticamente)
 ```
- Aplicação cluster:
```
  kubectl apply -f webapp-service.yaml
```
- Verificação da criação:
```
  kubectl get service webapp-service
```
- Acesso ao serviço utilizando o endereço IP e a porta 3008 atribuída:
```
  curl http://192.168.49.2:30008
```

![Captura de tela 2025-01-12 230640](https://github.com/user-attachments/assets/3257f203-a428-4ecf-977b-1c3de46ddec4)


## Restar-pod

>Crie um pod chamado "restart-pod" com a política de reinício configurada como "OnFailure". Provoque uma falha no pod e observe seu comportamento.

- Inicialmente crie um arquivo nano:
```
  nano restart-pod.yaml
```
- Adicione o script:
```
  apiVersion: v1
kind: Pod
metadata:
  name: restart-pod
spec:
  restartPolicy: OnFailure
  containers:
  - name: fail-container
    image: alpine
    command: ["sh", "-c", "exit 1"]
```
- Aplicação cluster:
```
  kubectl apply -f restart-pod.yaml
```
- Verifique o estado:
```
  kubectl get pods
```

![image](https://github.com/user-attachments/assets/1ab343da-b2fc-4070-94ca-60e232f5bee7)

- Observe o comportamento de reinicio:
```
  kubectl describe pod restart-pod
```

![Captura de tela 2025-01-13 121949](https://github.com/user-attachments/assets/592806c3-4de1-4abc-a4c4-e3096e112a89)


Observe que em `Reason` consta `Error`, atestando um comportamento de falha.

- Agora observe o numero de reinicialização do pod:
```
  kubectl get pod restart-pod -o wide
```

![Captura de tela 2025-01-13 122655](https://github.com/user-attachments/assets/8c389b5d-f2a8-4d02-b220-dcccaff33799)

