## Controlling Access to the Kubernetes API
* Note: __K8S__ ==> __Kubernetes__ 
* Users access the API using kubectl, client libraries, or by making REST requests. Both human users and Kubernetes service accounts can be authorized for API access. When a request reaches the API, it goes through several stages, illustrated in the following diagram:
* ![preview](./images/stages.png)

    [refer here for Kubernetes Documentation](https://kubernetes.io/docs/reference/access-authn-authz/controlling-access/)


#### Transport Security

* In a __K8S__ cluster, 
    * API serves on port 443
    * All the requests are of REST or Client Libraries. i.e __HTTPS__ based requests and it needs a __SSL__ Certificate.
    * That Certificate will be created whenever a K8S cluster is created at *$USER/.kube/config* and self-signed. (Note: If it is a __SINGLE__ user only)
    * For multiple users, then creator needs to share the Certificate with others.

#### Authentication

* Once TLS is established,
    * The HTTP request moves to Authentication step.
    * __This is shown as step 1 in the diagram__.
    * cluster admin configures the API server to run one or more Authenticator Modules. Authenticators are described in more detail [here](https://kubernetes.io/docs/reference/access-authn-authz/authentication/).
    * Authentication modules include Client Certificates, Password, and Plain Tokens, Bootstrap Tokens, and JWT Tokens (used for service accounts).
    * Multiple authentication modules can be specified, in which case each one is tried in sequence, until one of them succeeds.
    * If the request cannot be authenticated, it is rejected with HTTP status code 401.
        * Otherwise, the user is authenticated as a specific username, and the user name is available to subsequent steps to use in their decisions. Some authenticators also provide the group memberships of the user, while other authenticators do not.

#### Authorization

* After authentication the request must be Authorized
    * __This is shown as step 2 in the diagram__. 
    * A request must include the
        * username of the requester.
        * the requested action. and 
        * the object affected by the action.
    * The request is authorized if an existing policy declares that the user has permissions to complete the requested action.
####
    For example, if Bob has the policy below, then he can read pods only in the namespace projectCaribou:

    {
        "apiVersion": "abac.authorization.kubernetes.io/v1beta1",
        "kind": "Policy",
        "spec": {
            "user": "bob",
            "namespace": "projectCaribou",
            "resource": "pods",
            "readonly": true
        }
    }

* If bob makes only a get/read request for the mentioned __namespace__ only gets Authorized otherwise __denied__.

* Kubernetes supports multiple authorization modules, 
    * such as
        * ABAC mode
        * RBAC Mode
        * Webhook mode.
* To learn more about Kubernetes authorization, including details about creating policies using the supported authorization modules, see [Authorization Overview](https://kubernetes.io/docs/reference/access-authn-authz/authorization/)

#### Admission Control

* Admission Control Modules are software modules that can modify or reject requests.
    * __This is shown as step 3 in the diagram__.
    * In addition to the attributes available to Authorization Modules, Admission Control Modules can access the contents of the object that is being created or updated. 
    * They act on objects being created, deleted, updated or connected (proxy), but not reads.
    * Unlike Authentication and Authorization Modules, if any admission controller module rejects, then the request is __immediately rejected__.
    * The available Admission Control Modules are described [here](https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/).
    * Once a request passes all admission controllers, it is validated using the validation routines for the corresponding API object, and then written to the object store (shown as __step 4__).

#### API Server Ports and IPs
* The previous discussion applies to requests sent to the secure port of the API server (the typical case). The     API server can actually serve on 2 ports:

* By default the Kubernetes API server serves HTTP on 2 ports:

    1. Localhost Port:

        is intended for testing and bootstrap, and for other components of the master node (scheduler, controller-manager) to talk to the API
        no TLS
        default is port 8080, change with --insecure-port flag.
        default IP is localhost, change with --insecure-bind-address flag.
        request bypasses authentication and authorization modules.
        request handled by admission control module(s).
        protected by need to have host access
    2. Secure Port:

        use whenever possible
        uses TLS. Set cert with --tls-cert-file and key with --tls-private-key-file flag.
        default is port 6443, change with --secure-port flag.
        default IP is first non-localhost network interface, change with --bind-address flag.
        request handled by authentication and authorization modules.
        request handled by admission control module(s).
        authentication and authorization modules run.