# DIGIT QUICK-SETUP

. **PREREQUISITES**

1) Fork and Clone this Repo
2) Download the following :
   
   * Kubernetes
     
   * kubectl
   
   * Minikube
   
   * k3d
   
   * Docker
   
   * golang
   
   * helm
     
   * Postman
     
   * IDE (anyone will work : VS code, IntelliJ , etc)

3) **Creating a lightweight cluster**

   Create the "Kube" directory in the desired place (ensure you use the right dir path if it is different from the example) and change permission. This is used as k3d cluster persistent storage to store metadata and container logs.

    For Linux/Mac 

        cd ~
   
        mkdir kube
   
        chmod 777 kube
   
        cd kube
   
        pwd  #copy the path you get here. Provide an absolute path to below k3d c

4) **Config file to connect to cluster**
   
      Once the cluster creation is successful, get the kubeconfig file, that enables you to connect to the cluster.
      
          k3d kubeconfig get k3s-default > myk3dconfig
      
          export KUBECONFIG=<path-to-your-kube_config>
      
          kubectl config use-context k3d-k3s-default --kubeconfig=myk3dconfig
      
      Verify the cluster creation by running the following commands from your local machine where the kubectl is installed. It provides the sample output as below if everything works fine.
      
      kubectl cluster-info
      
      OutPut
      
      Kubernetes control plane is running at https://0.0.0.0:33931
      CoreDNS is running at https://0.0.0.0:33931/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
      Metrics-server is running at https://0.0.0.0:33931/api/v1/namespaces/kube-system/services/https:metrics-server:/proxy
      
      **NOTE** IF IT IS NOT RUNNING / OR NOT RUNNING AT 0.0.0.0:XXXXX  :
      
      1> TRY STARTING MINKUBE
      
      2> Check at time of creating cluster , if there was any error due to a blocked port , 



5) **Verify cluster list and nodes**
  
          k3d cluster list

      OutPut
      
      NAME          SERVERS   AGENTS   LOADBALANCER
   
      k3s-default   1/1       2/2      true
      
          kubectl get nodes
      
      OutPut
      
      NAME                       STATUS   ROLES                  AGE     VERSION
   
      k3d-k3s-default-agent-0    Ready    <none>                 3d18h   v1.21.1+k3s1
   
      k3d-k3s-default-agent-1    Ready    <none>                 3d18h   v1.21.1+k3s1
   
      k3d-k3s-default-server-0   Ready    control-plane,master   3d18h   v1.21.1+k3s1
      
          kubectl top nodes
      
      OutPut
      
      W0625 07:56:24.588781   12810 top_node.go:119] Using json format to get metrics. Next release will switch to protocol-buffers, switch early by passing --use-protocol-buffers flag
   
      NAME                       CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%
   
      k3d-k3s-default-agent-0    547m         6%     1505Mi          9%
   
      k3d-k3s-default-agent-1    40m          0%     2175Mi          13%
   
      k3d-k3s-default-server-0   59m          0%     2286Mi          14%  

6)  # Deployment Configuration

    Clone the following DIGIT Devops GitRepo. You may have to install git and then run git clone on your machine.

        git clone -b quickstart https://github.com/egovernments/DIGIT-DevOps 

    After cloning the repo CD into the folder DIGIT-DevOps, type the "code" command. This opens the visual editor and the DIGIT-DevOps repo files.

        cd DIGIT-DevOps
        code .

    Add the following lines to the host file in your local machine. Save and close the file.

    127.0.0.1 quickstart.local.digit

    for above step you can use :

        sudo nano /etc/hosts

7) # Deployment

      Once the deployment config is ready run the following command. Provide the necessary details and the interactive installer will take care of the rest.
      
      Run the deployer go script from the following directory:
      
          export KUBECONFIG=<path-to-your-kube_config>
          
          cd DIGIT-DevOps/deploy-as-code/egov-deployer
          
          sudo go run digit_setup.go
      
      #Be prepared for the following questions
      1. Are you good to proceed?
      2. Please enter the fully qualified path of the kubeconfig file
      3. Which DIGIT Version You would like to install, Select below
      4. Select the DIGIT modules that you want to install, choose Exit to complete selection
      5. Choose the target env files that are identified from your local configs
      6. Are we good to proceed with the actual deployment?
      
      All Done.
      
      2. Check all the pods are running and ready using the below command
      
              kubectl get pods -A

      3. Test the Digit application status in the command prompt/terminal using the command below:

              curl -Is http://quickstart.local.digit/employee/login |  head -n 1
          
          OutPut:
         
          HTTP/2 200

         **Note : re-run command again in few minutes , it may take some time to get all services running ,**

8) # Post-Deployment Steps

    Post-deployment the application is now accessible from the configured domain.
    
    For initiating PGR employee login, create a sample tenant, city, user to login and assign LME employee role through the seed script.
    
    Perform the kubectl port-forwarding of the egov-user service running from Kubernetes cluster to your localhost. This provides access to the egov-user service directly and allows the service to interact with the API.
    
        kubectl port-forward svc/egov-user 8080:8080 -n egov
   
    Output:
   
    Forwarding from 127.0.0.1:8080 -> 8080
   
    Forwarding from [::1]:8080 -> 8080
    
    2. Seed the sample data
    
        Ensure you have the postman to run the following seed data API. If not, run the Install postman on your local machine.
    
        Import the following postman collection into the postman and run it. This has the seed data that enable sample test users and localisation data.
    
            DIGIT Bootstrap
    
    To test the Kubernetes operations through kubectl from the local machine, execute the below commands.
    
    #get the pods running in the cluster
          kubectl get pods -n egov
    
    Output:

    NAME                                                    READY   STATUS    RESTARTS   AGE
   
    citizen-79cf89659c-8glf4                                1/1     Running   0          14d

    egov-accesscontrol-78b78dddb9-tfbkf                     1/1     Running   0          21d

    egov-enc-service-574cd7b5b5-hmhh4                       1/1     Running   0          36d

    egov-idgen-84954b565b-xsnqj                             1/1     Running   0          45d

    egov-indexer-5f5fbb6f4b-58rtm                           1/1     Running   0          43d

    egov-localization-6cc5977bb9-gm7f9                      1/1     Running   0          27d

    egov-mdms-service-65d6d65d8c-t85d9                      1/1     Running   0          21d

    egov-user-6676968d76-8n6t6                              1/1     Running   0          29d

    egov-workflow-v2-5cdb96bcf5-dcgmf                       1/1     Running   0          36d

    employee-749464fbfb-tptlh                               1/1     Running   0          14d

    nginx-ingress-controller-b9678869c-mkslb                1/1     Running   0          49d

    pgr-services-b9f4ffdbf-5h5kd                            1/1     Running   0          38d

    zuul-788bf8cd8b-9nxfl                                   1/1     Running   0          41d
    
    
    #Delete the pods so that it gets restarted automatically
    
          kubectl delete pods zuul-788bf8cd8b-9nxfl egov-workflow-v2-5cdb96bcf5-dcgmf pgr-services-b9f4ffdbf-5h5kd -n egov
    
      Output:
      
      pod "zuul-788bf8cd8b-9nxfl" deleted'
     
      pod "egov-workflow-v2-5cdb96bcf5-dcgmf" deleted
     
      pod "pgr-services-b9f4ffdbf-5h5kd" deleted
    
    This completes the setup of DIGIT Infra, deployment and installation of the DIGIT-PGR module.
    
    Paste the below link in the browser.
    
        quickstart.local.digit/employee
    
    Use the below credentials to login into the complaint section:
    
    **Username: GRO**
    
    **Password: eGov@4321**
    
    **City: CITYA**
