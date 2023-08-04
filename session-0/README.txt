###For Curl to work you need to have the token
###For Kubectl to work you need to have kubeconfig
###For client to work you need to have either token or kubeconfig


kubectl config view --minify --output 'jsonpath={.clusters[0].cluster.server}'

###If your kubeconfig is having token based access

curl -X GET https://kubernetes.docker.internal:6443/api/v1/namespaces -H "Authorization: Bearer $(kubectl config view --minify --output 'jsonpath={.users[0].user.token}')"
###Else you need to create a service account to get the token
kubectl apply -f <folder_name> // this will create sa, role and role bindings
// get the token from secret
kubectl get secret $(kubectl get serviceaccount my-service-account -o jsonpath='{.secrets[0].name}') -o jsonpath='{.data.token}' | base64 --decode
// Run from a location that has access to clusterIP
curl -k -X GET "https://kubernetes.default.svc.cluster.local/api/v1/namespaces/default/pods" -H "Authorization: Bearer eyJhbGciOiJSUzI1NiIsImtpZCI6IlJaZXdRYW1TWGFiTkRYZWhpcUhxeVlORXl3a2ZUVU0yd2k4ZmlYcFN6eXcifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJkZWZhdWx0Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZWNyZXQubmFtZSI6Im15LXNlcnZpY2UtYWNjb3VudC10b2tlbi1yam0ybCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50Lm5hbWUiOiJteS1zZXJ2aWNlLWFjY291bnQiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC51aWQiOiIyNjllNTI4My1mZTIxLTRjOTctYmVjYS0xZWNlOTEzZGQ4M2IiLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6ZGVmYXVsdDpteS1zZXJ2aWNlLWFjY291bnQifQ.GRNG3KEbqzBcwaKWyRgCyJ01ZO6BKoM2cbWh244KqzWQsHC1kWbBR-EmmsBNBkijtIr1XhUnEr2gc3b6DLg6IOH6H_ikvfdfoXcNC-aE0NvHifNZUA2RI6iDhAwA-kaLvydjKBTJequ-7Ut2CQJhFRF73FVL7UBsrBURn1AAL8N7lV7H3abPF3loyuK4DBF0t9k9XtFO11P7b651SULhtXtd6UAkzmKS5YxUQ4-a-2HVHHvhN6fs_MEep_zvHSZeG9tnAymQ01EFzIZ7Godv7aKcP2JgFtu0uBLsMCHwxsit0P_7RZIKKk4r3y8molDmep5awjwAaOkSzEbbQh4WGQ" | more

###Make use of kubectl proxy
kubectl proxy
curl -s http://localhost:8001/api/v1/namespaces | jq -r '.items[].metadata.name'
curl -X POST -H "Content-Type: application/json" -d '{"apiVersion":"v1","kind":"Namespace","metadata":{"name":"new-namespace"}}' http://localhost:8001/api/v1/namespaces

curl -X DELETE http://localhost:8001/api/v1/namespaces/new-namespace

curl -s http://localhost:8001/api/v1/namespaces/default/pods | jq -r '.items[].metadata.name'

cat <<EOF | curl -s http://localhost:8001/api/v1/namespaces/default/pods --data @- -H "Content-Type: application/json"
{
  "apiVersion": "v1",
  "kind": "Pod",
  "metadata": {
    "name": "busybox-pod"
  },
  "spec": {
    "containers": [
      {
        "name": "busybox-container",
        "image": "busybox",
        "command": ["sleep", "3600"]
      }
    ]
  }
}
EOF

curl -X DELETE http://localhost:8001/api/v1/namespaces/default/pods/busybox-pod

###For python_code we need to have required bundles installed in the server
pip3 install kubernetes
python3 list_pods.py

###For go_code you can run the code or create an executable and then run the executable from anywhere
go mod tidy
go run ./main.go
go build ./main.go -o get_all_pods