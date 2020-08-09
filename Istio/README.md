# Deployment Istio 

### Install Istio
>   * Download Istio
>       $ curl -L https://git.io/getLastIstio | ISTIO_VERSION=1.4.2 sh-
>   * Install istio using istioctl ( istio-1.4.2/bin/istioctl )
>       $ istioctl manifest apply --set profile=[profile-type]
>   > Profile Type
>   >   1. default: enable component according to the default setting of the IstioOperator API
>   >   2. demo: configuration designed to showcase istio functionality with modest resource requirements
>   >   3. minimal: the minimal set of components necessary to use istio's traffic management features
>   >   4. remote: used for configuring remote clusters of a multicluster mesh with a shared control plane configuration
>   >   5. empty: deploys nothing This can be useful as a base profile for custom configuration
>   >   6. preview: the preview profile contains features that are experimental

>   >   The components marked as x are installed within each profile:

>   >   > ||default|demo|minimal|remote|
>   >   > |:----|---|---|---|---|
>   >   > |**Core components**|||||
>   >   > |     istio-egressgateway||x|||
>   >   > |     istio-ingressgateway|x|x|||
>   >   > |     istiod|x|x|x||
>   >   > |**addons**|||||
>   >   > |grafana||x|||
>   >   > |istio-tracing||x|||
>   >   > |kiali||x|||
>   >   > |prometheus|x|x||x|

### Start Kiali(visualizing your mesh)
>   >   *Create Secret
>   >   ```bash
>   >       $ USERNAME=$(read -p 'Kiali Username: ' uval && echo -n &uval | base64)
>   >
>   >       $ PASSWD=$(read -p 'Kiali Passpharse: ' pval && echo -n &pval | base64)
>   >
>   >       $ cat <<EOF | kubectl apply -f -
>   >       apiVersion: v1
>   >       kind: secret
>   >       metadata:
>   >           name: kiali
>   >           namespace: istio-system
>   >           labels:
>   >               app: kiali
>   >       type: Opaque
>   >       data:
>   >           username: $USERNAME
>   >           passphrase: $PASSWD
>   >       EOF
>   >   ```
