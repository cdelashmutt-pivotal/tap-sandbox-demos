# Quickstart
Edit values-overlay.yaml to remove any parts you don't need.

kubectl config use-context tap-sandbox

./generate-tap-values.sh # To review the values that will be applied
./generate-tap-values.sh | kubectl -f- # To apply the values

## Minio for in-cluster techdocs
Edit to choose your root user and password:

```helm install --namespace minio --set 'rootUser=?,rootPassword=?' --set 'persistence.size=10Gi' --set resources.requests.memory=512Mi --set replicas=1 --set mode=standalone minio minio/minio```

Forward the minio port to localhost
```kubectl port-forward minio -n minio 9000```

Use the `mc` cli to create a bucket
```
mc alias set local http://localhost:9000 <root-user> <password>
mc mb local/techdocs-storage
```

Generate techdocs
```
npx @techdocs/cli generate --source-dir ./catalog --output-dir ./site
```

Publish the techdocs
```
export AWS_ACCESS_KEY_ID='<root-user>' AWS_SECRET_ACCESS_KEY='<password>' AWS_REGION=us-east-1
npx @techdocs/cli@1.4.7 publish --publisher-type awsS3 --storage-name techdocs-storage --entity default/<catalog-component>/<component-name> --awsEndpoint http://localhost:9000 --directory ./site --awsS3ForcePathStyle true
```

Enjoy techdocs!