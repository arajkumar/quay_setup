## Deployment of Quay, Clairv4 with CRDA on Openshift 4.x

### 1. Create Openshift cluster > 4.5
#### 1.1. Cleanup DNS Recordsets on AWS Ephimeral cluster (pre-install)
Below script would help you to reuse the same cluster subdomain for each new cluster deployment.
Note: Make sure that you pass correct values to `HOSTED_ZONE_ID` and `NAME`.

```bash
bash pre-install.sh <HOSTED_ZONE_ID> <NAME>
```
#### 1.2. Create OKD/OCP Cluster
```bash
...
$INSTALLER_DIR/openshift-install --dir . create cluster
```

### 2. Use Let's Encrypt certificates on Ingress controller
#### 2.1. Get certificate pair (One time process)
Follow https://github.com/redhat-cop/openshift-4-alpha-enablement/blob/2d128d28496378e609c00da5312a3a3500fa1702/Lets_Encrypt_Certificates_for_OCP4.adoc
#### 2.2. Use certificate pair on Ingress (post-install)
Note: It is assumed that cert files are present in $PWD/certificates.

```sh
bash post-install.sh
```

### 3. Deploy Quay, CSO
```sh
bash quay_setup.sh
```

### 4. Login to quay and create an organisation and push image.
Use `oc get routes` and use the route to access quay.The default username is `quay` and password is `password` .
Create an organisation named `clairv4` .
Create a repo. 
Tag an image according to the route,org name,repo and tag.
For example
`docker pull quay.io/rafiu007/clair:pyvuln` 
`docker tag quay.io/rafiu007/clair:pyvuln quay-xyz-abc.io/clairv4/flask:image1` .
Push the image. 
`docker push quay-xyz-abc.io/clairv4/flask:image1` .

Note:
Here route is `quay-xyz-abc.io`
org name is `clairv4`
repo is `flask`
tag is `image1`

Use `quay-xyz-abc.io/clairv4/flask:image1` as image in your workload.

### All in one
Note: It is assumed that `install-config` has been created and stored in $PWD using `$INSTALLER_DIR/openshift-install --dir=. create install-config`.

```bash
bash pre-install.sh && $INSTALLER_DIR/openshift-install --dir . create cluster && bash post-install.sh && bash quay_setup.sh
```
