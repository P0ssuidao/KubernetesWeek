# Ex 1:
Crie um POD chamando com as seguintes características:
  *  Nome do POD: nginx-html
  *  Crie um volume do tipo emptydir com o nome: html-index
 
   ## container: 1
     *   nome: nginx-html
     *   image: Nginx
     *   utilize o volme: html-index
     *   Monte o volume no path: /usr/share/nginx/html
     
   ## container 2
     *   nome: busybox
     *   image: busybox
     *   command: ["/bin/sh", "-c", "while true; do date >> /html/index.html; sleep 1; done"]
     *   utilize o volme: html-index
     *   Monte o volume no path: /html
        
Resposta:
```
apiVersion: v1
kind: Pod
metadata:
  name: nginx-html
spec:
  containers:
    - name: busybox
      image: busybox
      volumeMounts:
      - mountPath: "/html"
        name: html-index
      command: ["/bin/sh", "-c", "while true; do date >> /html/index.html; sleep 1; done"]
    - name: nginx
      image: nginx
      volumeMounts:
      - mountPath: "/usr/share/nginx/html"
        name: html-index
  volumes:
    - name: html-index
      emptyDir: {}
 ```
# Ex 2:
Crie uma Secret com as seguintes características utilizando o kubectl:
   * nome: admin-secret
   *    user=admin
   *    pass=senhasupersegura
Agora Crie um POD com as seguintes características:
   * nome: pod-secret-admin
   * image: nginx
   * port: 80
   
Monte a secret admin-secret como volume  em: /secret

Resposta:
```
kubectl create secret generic admin-secret --from-literal=user=admin --from-literal=pass=senhasupersegura

kubectl run pod-secret-admin --image=nginx --port=80 --dry-run=client -o yaml > pod.yaml

apiVersion: v1
kind: Pod
metadata:
  name: pod-secret-admin
spec:
  containers:
    - name: pod-secret-admin
      image: nginx
      volumeMounts:
      - name: secret-volume
        mountPath: /secret
  volumes:
    - name: secret-volume
      secret:
        secretName: admin-secret
  restartPolicy: Never

kubectl apply -f pod.yaml
```
