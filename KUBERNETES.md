### O k8s tem os seguintes componentes principais:

* Master node
* Worker node
* Services
* Controllers
* Pods
* Namespaces e quatas
* Network e policies
* Storege

**Kube-apiserver** é o central de operações do cluster k8s. Todas as chamadas, internas ou externas são tratadas por ele. Ele é o único que conecta no ETCD.

**kube-scheduler** usa um algoritmo para verificar em qual determinado pod devera ser hospedado. Ele verifica os recursos disponíveis  do node para verificar qual o melhor node para receber aquele pod.

No **ETCD** são armezenados o estado do cluster, rede e outras informações persistentes.

**kube-controller-manager** é o controle principal que interage com o kube-apiserver para determinar o seu estado. Se o estado não bate, o manager ira contactar o controller necessário para checar seu estado desejado. Tem diversos controller em uso como os endpoints, namespace e replication.

**kubelet** interage com o Docker instalado no node e garante que os containers que precisavam estar em execução realmente estejam.

O **kube-proxy** é o responsável por gerenciar as redes, para os containers. É o responsável por expor portas dos containers.

**Supervisord** é o responsável por monitorar e restabelecer, se necessário, o kubelet e o docker. Por esse motivo, quando existe algum problema em relação ao kubelet, como por exemplo o uso do cgroup driver diferente do que está rodando no Docker, você perceberá que ele ficará tentando subir o kubelet frequentemente.

**Pod** é a menor unidade que você irá tratar no k8s. Você poderá ter mais de um container por Pod, porém vale lembrar que eles dividirão os mesmos recursos, como por exemplo IP. Uma das boas razões para se ter mais de um container em um Pod e o fato de você ter os logs consolidados.

**Services** é uma forma de você expor a comunicação através de um NodePort ou LoadBalancer e para distribuir as requisições entre diversos Pods daquele Deployment. Funciona como um balanceador de carga.

Para prover a rede para os containers, o k8s utiliza a especificação do **CNI**, Container Network Interface.

CNI é uma especificação que reuni algumas bibliotecas para o desenvolvimento de plugins para configuração e gerenciamento de redes para os conteiners. Ele provê uma comum interface entre as diversas soluções de rede para k8s. Você encontra diversos plugins para AWS, GCP, Cloud Foundry entre outros. https://github.com/containernetworking/cni

Enquanto o CNI define a rede dos pods, ele não te ajuda na comunicação entre os pods de diferentes nodes.

As características básicas da rede do k8s são:

* Todos os pods conseguem se comunicar entre eles em diferentes nodes

* Todos os nodes pode se comunicar com todos os pods

* No utilizar NAT

Todos os IP dos pods e nodes são roteados sem a utilização de NAT. Isso é solucionado com a utilização de algum software que te ajudará na criação de uma rede Overlay.

Segue alguns:

* Weave
* Flannel
* Canal
* Calico
* Romana
* Nuage
* Contiv

https://kubernetes.io/docs/concepts/cluster-administration/addons/


### Deployments

Quando você utiliza o `kubectl run`, você está realizando o deploy de um de um objeto chamado **Deployment**. Como outros objetos, o Deployment também pode ser cirado através de um arquivo YAML ou de um JSON, os spec files.

Se você deseja  alterar alguma configuração de seus objetos, como o pod, você pode utilizar o `kubectl apply`, através de um spec file, ou ainda através do `kubectl edit`.

As versões anteriores dos **ReplicaSet** são mantidas, possibilitando o **rollback** em caso de falhas.

As **label** são importantes para o gerenciamentoo do cluster, pois com ela é possível buscar ou selecionar recursos em seu cluster, fazendo com que você consiga organizar em pequenas categorias, facilitando assim a sua busca e organizando seus pods e seus recursos do cluster. As **labels** não são recursos do API server, eles são armazenados no metadata em formato de chave-valor.

Antes tinhamos somente o RC, **Replication Controller**, que era um controle sobre o número de réplicas que determinado **pod** estava executando, o problema que todo esse gerenciamento era feito do lado do client. Para solucionar esse problema, foi adicionado o objeto **Deployment**, que permite a atualização pelo lado do server. **Deployments** geram ReplicaSets, que oferecerem melhores opções do que o ReplicationControoler, e por esse motivo está sendo substituido.


> kubectl run nginx --image=nginx --port 80

> kubectl edit deployment ngnix

Modifique a versão da imagem

> kubectl rollout history deployment nginx

> kubectl rollout history deployment nginx --revision 1

> kubectl rollout undo deployment nginx --to-revision 1

> kubectl describe pod nginx-3421 | grep -i image