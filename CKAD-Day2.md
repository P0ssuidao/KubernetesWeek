# Ex1:
Crie um ConfigMap com as seguintes características utilizando o kubectl:
 *   nome: db-env
 *    ip=10.0.0.1
 *    db=develop


Agora Crie um Deployment com as seguintes características:
*    nome: deploy-cm
*    image: nginx
*    port: 80
*    Utilize o LivenessProbe com httpGet no path / na porta 80
     *    initialDelaySeconds: 3
     *    periodSeconds: 3
* Exporte todo o configMap criado anteriormente como variáveis de ambiente


# Resposta:

```
kubectl create cm db-env --from-literal ip=10.0.0.1 --from-literal db=develop


apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: deploy-cm
  name: deploy-cm
spec:
  replicas: 1
  selector:
    matchLabels:
      app: deploy-cm
  strategy: {}
  template:
    metadata:
      labels:
        app: deploy-cm
    spec:
      containers:
      - image: deploy-cm
        name: deploy-cm
        envFrom:
        - configMapRef:
            name: db-env
        livenessProbe:
          initialDelaySeconds: 3
          periodSeconds: 3
          httpGet:
            path: /
            port: 80
```

# Ex2:

Crie um Deployment com as seguintes características:
*    nome: my-canary-v1
*    image: nginx
*   port: 80


Crie um service para o deployment criado anteriormente com as seguintes características:
*    nome: canary-service
*    tipo: ClusterIP
*    port: 8080
*   target-Port: 80


Crie um novo Deployment com as seguintes características:
*    nome: my-canary-v2
*    image: httpd
*    port: 80


Agora utilize o service criado para direcionar 80% do trafego para o deployment my-canary-v2 e 20% para o my-canary-v1


# Respostas:

```
kubectl create deploy my-canary-v1 --image nginx
kubectl create deploy my-canary-v2 --image httpd
kubectl expose deploy my-canary-v1 --port 8080 --target-port 80 --name canary-service
kubectl edit deploy my-canary-v1 # ADD LABEL
kubectl edit deploy my-canary-v2  # ADD LABEL
kubectl edit svc canary-service # TROCA LABEL
kubectl scale deployment my-canary-v2 --replicas 8
kubectl scale deployment my-canary-v1 --replicas 2
```
