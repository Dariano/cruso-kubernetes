Alguns comandos utilizados


kubectl describe node kube-01 | grep -i taint
kubectl run nginx --image nginx -- port 80
kubectl scale deployment nginx --replicas=5
kubectl taint node kube-01 key=value:NoSchedule
kubectl describe node kube-01 | grep -i taint
kubectl scale deployment nginx --replicas=8
kubectl taint node kube-01 key=value:NoExecute
kubectl taint node kube-01 key-
kubectl scale deployment nginx --replicas=3
kubectl scale deployment nginx --replicas=6
kubectl scale deployment nginx --replicas=1
kubectl drain instance-3

journalctl -u kubelet | less # Viulizar os logs

kubectl logs -f "nome do pod" -n "nome do namespaces"


https://github.com/kubernetes/heapster
https://kubernetes.io/docs/tasks/debug-application-cluster/logging-elasticsearch-kibana/ 
https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/
https://kubernetes.io/docs/tasks/debug-application-cluster/debug-cluster/
https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/
https://kubernetes.io/docs/tasks/debug-application-cluster/debug-cluster/ 
https://kubernetes.io/docs/tasks/debug-application-cluster/debug-pod-replication-controller/ 
https://kubernetes.io/docs/tasks/debug-application-cluster/debug-service/ 
https://github.com/kubernetes/kubernetes/issues 
https://kubernetes.slack.com/

https://github.com/kubernetes/heapster/blob/master/docs/influxdb.md


kubectl run db --image=redis:4 --port 6379 --expose --labels app=bookstore,role=db 

kubectl run opa --labels app=inventory,role=web --rm -t -i --image=alpine -- sh 

nc -v -w  2 db 6379



kubectl run web --image=nginx app=web --expose --port 80
kubectl run --rm -it --image=apline strigus -- sh
wget -qO- http://web


kubectl run --rm -it --image=alpine strigus -- sh
wget -qO- http://web


kubectl run apiserver --image=nginx --labels app=bookstore,role=api --expose --port 80
kubectl run --rm -i -t --image=alpine strigu --labels app=bookstore,role=frontend -- sh
wget -qO- --timeout 2 http://apiserver 


https://github.com/ahmetb/kubernetes-network-policy-recipes

