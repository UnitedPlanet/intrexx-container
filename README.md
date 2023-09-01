# Intrexx standalone in Kubernetes
## Prerequisite
This deployment requires helm version>3.0 to be installed. 
The namespace in the yaml-files has to be changed to the desired namespace.
The deployment steps have to be executed in the given order.
It is assumed that a default namespace is set and the yaml files are modified accordingly.

If you want to change the namespace values of the helm charts you have to append ```--namespace yournamespace``` to every helm command listed below.

## Postgresql installation
Required files: 
1. ix-db-pvc.yaml
2. ix-db.yaml 

Commands:
> kubectl apply -f ix-db-pvc.yaml

> helm install ix-db oci://registry-1.docker.io/bitnamicharts/postgresql -f ix-db.yaml


After this command, the output should be copied.

## Solr installation
Required files:
1. ix-solr-pvc.yaml

Commands
> kubectl apply -f ix-solr-pvc.yaml

> helm install ix-solr oci://registry-1.docker.io/bitnamicharts/solr -f ix-solr.yaml

After this command, the output should be copied. The solr-password is specified in this output and will be used in the next step.

## Intrexx installation
Required files:
1. ix-portal-init.yaml
2. ix-portal.yaml
### Initialisation of the Intrexx portal
The ix-portal-init.yaml file is used for this step.

Necessary Modifications:
1. In the ix-portal-init.yaml file, the solr-password has to be copied in the SOLR_PASSWORD environment
2. The DB_HOST and SOLR_HOST are specified for the default namespace and have to be modified to work with the namespace that is used for the deployment.

Commands
> kubectl apply -f ix-pvc.yaml

> kubectl apply -f ix-portal-init.yaml

The following command can be used to verify that the initialisation of the pod was executed correctly

> kubectl logs -f ix-portal-init

### Runtime of the intrexx portal
The ix-portal.yaml and ix-portal-service.yaml file is used for this step.

Necessary Modifications:
1. In the ix-portal.yaml file, the solr-password has to be copied in the SOLR_PASSWORD environment
2. The DB_HOST and SOLR_HOST are specified for the default namespace and have to be modified to work with the namespace that is used for the deployment.

Commands
> kubectl apply -f ix-portal.yaml

> kubectl apply -f ix-portal-service.yaml

### Configuration of the Ingress service
Required files:
1. ix-ingress.yaml

Commands
> kubectl apply -f ix-ingress.yaml

## Last steps

Currently, it is necessary to modify the solr URL via the Intrexx Portal Manager. Please start the Intrexx Portal Manager, connect to the portal and navigate to tools. There, you have to select Search -> Solr Search Server. In the field URL, the suffix /solr has to be added to the existing URL. In the "Task scheduler" submenu, the task "Tagging search engine maintenance" has to be executed.