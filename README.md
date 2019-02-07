# Kubernetes
    Kubernetes is the open source containter orchetsration tool developed by google which is similar and more robust than Docker Swarm.
    Kubernetes offers a lighter version "minikube" which can be installed and configured to the workstation.
  
## Minikube	 
      Minikube is a single node kubernetes cluster inside a single VM on your workstation to try-out kubernetes. It offers many feautures like Persistent Volumes (HostOnlySystem), Config Maps , Secrets , Ingress etc.
    
## Intsalling minikube on AWS EC2 (Ubuntu)
    We will see the steps to install and verify the Minikube on the linux system. We will be using the ubuntu instance from the AWS EC2.
    
   **Step 1: Provision and Login to the ubuntu server**
   
   **Step 2: Download and install kubectl**
             Kubectl is the command line interface for kubernetes which interacts with kubernets clusters through API
             
             curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
             
             chmod +x ./kubectl
             sudo mv ./kubectl /usr/local/bin/kubectl
             
   **Step 3: Install Docker**
              Kubernets requires docker to be installed on the host system as a container runtime.
              
              sudo apt-get update &&     sudo apt-get install docker.io -y
              
              Verify the docker daemon
              sudo -i
              docker ps 
              root@ip-172-31-44-209:~# docker ps
              CONTAINER ID        IMAGE                  COMMAND                  CREATED             STATUS              PORTS               NAMES

   **Step 4: Install the MiniKube**
   
      curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 && chmod +x minikube && sudo mv minikube /usr/local/bin/
      
      verify the version
      
      ubuntu@ip-172-31-44-209:~$ minikube version
      minikube version: v0.33.1
      ubuntu@ip-172-31-44-209:~$
             
   **Step 5: Start the Minikube**
   
              minikube start --vm-driver=none
              
              Minikube supports the following drivers:
              
                virtualbox
                vmwarefusion
                kvm2 (driver installation)
                kvm (driver installation)
                hyperkit (driver installation)
                xhyve (driver installation) (deprecated)
                hyperv (driver installation) Note that the IP below is dynamic and can change. It can be retrieved with minikube ip.
                **none (Runs the Kubernetes components on the host and not in a VM. Using this driver requires Docker (docker install) and a Linux environment)**
                
                root@ip-172-31-44-209:~# minikube start
                  Starting local Kubernetes v1.13.2 cluster...
                  Starting VM...
                  Skipping virtualbox driver, existing host has none driver.
                  Getting VM IP address...
                  Moving files into cluster...
                  Setting up certs...
                  Connecting to cluster...
                  Setting up kubeconfig...
                  Stopping extra container runtimes...
                  E0207 12:54:59.688517    5743 start.go:353] Error stopping crio: driver does not support ssh commands
                  Machine exists, restarting cluster components...
                  Verifying kubelet health ...
                  Verifying apiserver health ....
                  Kubectl is now configured to use the cluster.
                  Loading cached images from config file.


                  Everything looks great. Please enjoy minikube!
                  root@ip-172-31-44-209:~#
                  
   **Step 6: verify the status**
             
             cluster info
             root@ip-172-31-44-209:~# kubectl cluster-info
             Kubernetes master is running at https://172.31.44.209:8443
             KubeDNS is running at https://172.31.44.209:8443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

             To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
             root@ip-172-31-44-209:~#
             
             root@ip-172-31-44-209:~# minikube status
             host: Running
             kubelet: Running
             apiserver: Running
             kubectl: Correctly Configured: pointing to minikube-vm at 172.31.44.209

             root@ip-172-31-44-209:~#
             
   **Step 7: Deploy Sample application and expose it**
   
    root@ip-172-31-44-209:~# kubectl run hello-minikube --image=gcr.io/google_containers/echoserver:1.4 --port=8080
    kubectl run --generator=deployment/apps.v1 is DEPRECATED and will be removed in a future version. Use kubectl run --generator=run-    pod/v1 or kubectl create instead.
    deployment.apps/hello-minikube created
    root@ip-172-31-44-209:~#
    
    Above command creates a deployment called "hello-minikube" for the image "echo server " from google container repository (GCR) and exposing its port 8080
    
    root@ip-172-31-44-209:~# kubectl get deployments
    NAME             READY   UP-TO-DATE   AVAILABLE   AGE
    hello-minikube   1/1     1            1           2m50s
    
    root@ip-172-31-44-209:~# kubectl get pods --output=wide
    NAME                              READY   STATUS    RESTARTS   AGE     IP           NODE       NOMINATED NODE   READINESS GATES
    hello-minikube-5857d96c67-t6x87   1/1     Running   0          3m29s   172.17.0.2   minikube   <none>           <none>
    
    Now expose the deployment as NodePort for the external consumers
    
    root@ip-172-31-44-209:~# kubectl expose deployment hello-minikube --type=NodePort
    service/hello-minikube exposed
    root@ip-172-31-44-209:~#

    root@ip-172-31-44-209:~# kubectl get services --output=wide
    NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE     SELECTOR
    hello-minikube   NodePort    10.105.60.116   <none>        8080:30380/TCP   4m50s   run=hello-minikube
    kubernetes       ClusterIP   10.96.0.1       <none>        443/TCP          22h     <none>
    root@ip-172-31-44-209:~#
    
    root@ip-172-31-44-209:~# curl $(minikube service hello-minikube --url)
    Hostname: hello-minikube-6fd785d459-fnpnh

        Pod Information:
        -no pod information available-

        Server values:
        server_version=nginx: 1.13.3 - lua: 10008

        Request Information:
             client_address=172.17.0.1
             method=GET
             real path=/
             query=
             request_version=1.1
             request_scheme=http
             request_uri=http://172.31.44.209:8080/

        Request Headers:
            accept=*/*
            host=172.31.44.209:30166
            user-agent=curl/7.58.0

        Request Body:
            -no body in request-

  **Step 8: Stop the kubernetes clusters**
  
            root@ip-172-31-44-209:~# minikube stop
            Stopping local Kubernetes cluster...
            Machine stopped.
            root@ip-172-31-44-209:~#


    
   
