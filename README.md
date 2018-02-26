# Curso de kubernetes

## Volumes

Vamos instalar `nfs-kernel-server` para que possamos exportar um pedaço do disco.

```sh
$ sudo apt-get install -y nfs-kernel-server
```

Agora vamos criar um diretório

```sh 
$ mkdir /opt/curso-kubernetes
```

Em seguida abra o arquivo **/etc/exports** e coloca o que você quer compartilhar.

```sh 
$ vim /etc/exports
```

> /opt/curso-kubernetes *(rw,sync,no_root_squash,subtree_ckeck)

Para exportar todas as configurações que forma adicionadas.

```sh
$ exportfs -ar
```

Nas outras máquinas instalar, isso é para que tenhamos todas as ferramentas necessárias para montarmos os discos, file system.

```
$ sudo isntall apt-get nfs-common 
```

Para ver quais os diretórios estão sendo exportados digite o comando `showmount -e 172.31.10.5`.

```
$ showmount -e 172.31.3.51
```

Para compartilhar o diretório remoto

```
$ mount 172.31.3.51:/opt/curso-kubernetes /mnt
```

Voltamos para o `master` e criamos um arquivo chamado **pv.yaml**(**pv, PercistenteVolume**) e adicionamos o seguinte conteúdo.

```sh
$ vim pv.yaml
```

```yaml
kind: PersistentVolume
apiVersion: v1
metadata:
  name: meu-volume
  labels:
    type: local
spec:
  capacity:
    storage: 100Mi
  accessModes:
  - ReadWriteMany
  persistemVolumeReclaimPolicy: Retain
  nfs:
    path: /opt/curso-kubernetes
    server: 172.31.3.51 # IP da MASTER
    readOnly: false
```

```sh
$ kubectl create -f pv.yaml
```

Somente isso não adianta nada, ainda precisamos ter o **pvc, PercistenteVolumeClaim**, precisamos dele para podermos atachar no pod.

 ```sh
 $ vim pvc.yaml
 ```


```yaml
kind: PersistenteVolumeClaim
apiVersion: v1
metadata:
  name: meu-pvc
spec:
  accessModes:
  - ReadWriteMany
  resources:
    requests:
      storege: 10Mi
```

```sh
$ kubectl create -f pvc.yaml
```

Vamos pegar o arquivo de **Deployment** e adicionar o volume

> $ kubectl get deployment nginx -o yaml > deploy_nginx.yaml


```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  annotations:
    deployment.kubernetes.io/revision: "1"
  creationTimestamp: 2018-02-23T00:31:35Z
  generation: 1
  labels:
    run: nginx
  name: nginxvolume
  namespace: default
  resourceVersion: "38090"
  selfLink: /apis/extensions/v1beta1/namespaces/default/deployments/nginxvolume
  uid: e72bce6f-1830-11e8-b264-0800275735f2
spec:
  replicas: 1
  selector:
    matchLabels:
      run: nginx
  strategy:
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 1
    type: RollingUpdate
  template:
    metadata:
      creationTimestamp: null
      labels:
        run: nginx
    spec:
      containers:
      - image: nginx
        imagePullPolicy: Always
        name: nginx
        volumeMounts:
        - name: nfs-vol
          moutPath: /opt
        ports:
        - containerPort: 80
          protocol: TCP
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
      volumes:
      - name: nfs-vol
        persistentVolumeClaim:
          claimName: meu-pvc
status:
  availableReplicas: 1
  conditions:
  - lastTransitionTime: 2018-02-23T00:31:35Z
    lastUpdateTime: 2018-02-23T00:31:35Z
    message: Deployment has minimum availability.
    reason: MinimumReplicasAvailable
    status: "True"
    type: Available
  observedGeneration: 1
  readyReplicas: 1
  replicas: 1
  updatedReplicas: 1

```

```sh
$ kubectl create -f deploy_nginx.yaml
```