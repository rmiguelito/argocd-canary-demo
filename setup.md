Setup:
minikube start -p argocd-canary --memory=7837mb --cpus=4 --addons=istio-provisioner,istio,ingress,ingress-dnsminikube addons enable ingress
 --ports=80:80,443:443 --listen-address=0.0.0.0

helm upgrade --install argocd argo/argo-cd -f values.yaml --namespace argocd --create-namespace
helm install argo-rollouts argo/argo-rollouts --set dashboard.enabled=true --namespace argocd --create-namespace
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d

kubectl port-forward service/argo-rollouts-dashboard 3100:3100 &
kubectl port-forward service/argocd-server 8080:443 &

helm repo add datadog https://helm.datadoghq.com
helm install datadog-operator datadog/datadog-operator
kubectl create secret generic datadog-secret --from-literal api-key=<PLACEHOLDER>

kubectl run -it --image=jrecord/nettools nettools --restart=Never


echo "import http from 'k6/http'; export default function(){ http.get('http://rollout-demo'); }" | k6 run --vus 1 --iterations 1000 -

echo "import http from 'k6/http'; export default function(){ http.get('http://rollout-demo'); }" | k6 run --vus 10 --duration 90s -