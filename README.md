Deploying the Sock Shop Microservice on a Kubernetes Cluster
OBJECTIVES
Set up a deployment pipeline.
Implement metrics with Alertmanager.
Establish monitoring with Grafana.
Enable logging with Prometheus.
Utilize Prometheus for monitoring purposes.
Apply Ansible or Terraform for configuration management.
Choose an IaaS provider that suits your preferences.
Ensure the application operates on Kubernetes.
Secure the application with HTTPS using a Let’s Encrypt certificate.
Prerequisites:
An active Azure subscription.
Visual Studio Code (VSCode) installed.
Install Azure CLI, kubectl, Helm (Kubernetes package manager), and Terraform.
Steps to Achieve the Objectives
Set up the Kubernetes Cluster:
Begin by initializing Terraform in your project's root directory. Run terraform plan followed by terraform apply to provision your Kubernetes cluster on your chosen cloud provider.


Update kubeconfig:
Once the cluster is up and running, update your kubeconfig file to ensure communication with the Kubernetes cluster is possible.


Deploy the Application:
Use the provided deployment YAML file to deploy the Sock Shop application. After a successful deployment, you should see output similar to the image below.


Verify Deployment:
Confirm that the application is running by using the command kubectl get pods,svc -n sock-shop. This command will list all pods and services in the sock-shop namespace.


Install Ingress Controller:
To route traffic to the application, install the NGINX Ingress Controller using Helm. First, add the NGINX repository with helm repo add nginx https://helm.nginx.com/stable, update it with helm repo update, search for the desired chart with helm search repo nginx, and install it using helm install ingress nginx/nginx-ingress.


Set Up DNS Records:
In your Namecheap dashboard (or another DNS provider), create an "A record" to link your domain name to the Ingress external IP. Additionally, set up "CNAME" records for the specific services you want to access.


Apply Ingress Configuration:
Apply your Ingress rules using either kubectl apply -f main-ingress.yaml or helm install my-app/templates/main-ingress.yaml to allow access to your application via the domain name.


Access the Application:
After applying the Ingress rules, open the application in your browser. At this stage, it may still show as "Not Secure."


Install Cert-Manager for HTTPS:
To enable HTTPS, install Cert-Manager using Helm. Add the Jetstack repository with helm repo add jetstack https://charts.jetstack.io --force-update and install Cert-Manager using kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.15.2/cert-manager.yaml.


Configure and Apply Certificates:
Set up an Issuer or ClusterIssuer and a certificate YAML file based on the Cert-Manager documentation. Make sure your Ingress is properly annotated for TLS, then apply the Ingress, Issuer, and Certificate configurations. Verify the certificate status using kubectl get certificate -n sock-shop.



Test HTTPS Configuration:
Finally, access your application in the browser again to confirm that it is now secured with HTTPS.


Deploy Monitoring and Logging Tools:
Add the Prometheus community Helm chart repository with helm repo add prometheus-community https://prometheus-community.github.io/helm-charts. Install the Prometheus stack in the sock-shop namespace using helm install prome prometheus-community/kube-prometheus-stack -n sock-shop.



Access Grafana and Prometheus Dashboards:
Update your main-ingress.yaml to include the services and ports for Grafana and Prometheus. To retrieve the default Grafana login credentials, find the Grafana pod name with kubectl get pods -n sock-shop and then describe the pod to locate the secret name. Decode the secret to obtain the admin username and password.



Prometheus does not require login credentials, so you can access it directly via the browser.


Configure Alertmanager for Notifications:
Set up alerts in Grafana, such as Slack notifications. To do this, create a Slack workspace (or be an admin of one), add a webhook, and use the webhook URL in your Grafana alert configuration. Trigger an alert by deleting a pod, for example, and receive notifications in your designated Slack channel.