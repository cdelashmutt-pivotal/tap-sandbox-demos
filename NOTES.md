helm install --namespace minio --set 'rootUser=?,rootPassword=?' --set 'persistence.size=10Gi' --set resources.requests.memory=512Mi --set replicas=1 --set mode=standalone minio minio/minio