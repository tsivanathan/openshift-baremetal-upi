https://github.com/ryanhay/ocp4-metal-install#configure-storage-for-the-image-registry

Configure storage for the Image Registry
A Bare Metal cluster does not by default provide storage so the Image Registry Operator bootstraps itself as 'Removed' so the installer can complete. As the installation has now completed storage can be added for the Registry and the operator updated to a 'Managed' state.

Create the 'image-registry-storage' PVC by updating the Image Registry operator config by updating the management state to 'Managed' and adding 'pvc' and 'claim' keys in the storage key:

oc edit configs.imageregistry.operator.openshift.io
managementState: Managed
storage:
  pvc:
    claim: # leave the claim blank
Confirm the 'image-registry-storage' pvc has been created and is currently in a 'Pending' state

oc get pvc -n openshift-image-registry
Create the persistent volume for the 'image-registry-storage' pvc to bind to

oc create -f ~/ocp4-metal-install/manifest/registry-pv.yaml
After a short wait the 'image-registry-storage' pvc should now be bound

oc get pvc -n openshift-image-registry



=================
PASSWORD:

[root@ocp-svc manifest]# oc apply -f oauth-htpasswd.yaml
        Warning: resource oauths/cluster is missing the kubectl.kubernetes.io/last-applied-configuration annotation which is required by oc apply. oc apply should only be used on resources created declaratively by either oc create --save-config or oc apply. The missing annotation will be patched automatically.
oauth.config.openshift.io/cluster configured
Error from server (BadRequest): error when creating "oauth-htpasswd.yaml": Secret in version "v1" cannot be handled as a Secret: illegal base64 data at input byte 0
[root@ocp-svc manifest]#




https://stackoverflow.com/questions/53394973/cant-create-secret-in-kubernetes-illegal-base64-data-at-input

[root@ocp-svc manifest]# htpasswd -n -B -b admin password
admin:$2y$05$s7trflNSRImDR.fAOF5LfOy9UP0GITYVJ28K9KD/R4pPhxjkyCdxq

[root@ocp-svc manifest]# echo "$2y$05$s7trflNSRImDR.fAOF5LfOy9UP0GITYVJ28K9KD/R4pPhxjkyCdxq" |base64
eS1iYXNoNS5mQU9GNUxmT3k5VVAwR0lUWVZKMjhLOUtEL1I0cFBoeGpreUNkeHEK

[root@ocp-svc manifest]# vi oauth-htpasswd.yaml

[root@ocp-svc manifest]# oc apply -f ~/ocp-install/manifest/oauth-htpasswd.yaml
secret/htpasswd-secret created
oauth.config.openshift.io/cluster unchanged
[root@ocp-svc manifest]#

$ echo "mega_secret_key" | base64
bWVnYV9zZWNyZXRfa2V5Cg==
