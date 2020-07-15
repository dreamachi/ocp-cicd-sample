# OpenShift CI/CD 



### prepare images for ci/cd (bastion or registry node using root)

------

##### a. pull images

```shell
podman login registry.redhat.io
podman pull registry.redhat.io/openshift4/ose-jenkins:v4.4
podman pull registry.redhat.io/openshift4/ose-jenkins-agent-maven:v4.2
podman pull registry.redhat.io/rhscl/postgresql-96-rhel7:latest
podman pull docker.io/openshiftdemos/gogs:0.11.34
podman pull docker.io/siamaksade/sonarqube:latest
podman pull registry.redhat.io/jboss-eap-7/eap72-openshift:1.2
podman pull sonatype/nexus3
```

##### b. tag images

```shell
podman tag registry.redhat.io/openshift4/ose-jenkins:v4.4 bastion.demo.ocp4.com:5000/openshift4/ose-jenkins:v4.4
podman tag registry.redhat.io/openshift4/ose-jenkins-agent-maven:v4.2 bastion.demo.ocp4.com:5000/openshift4/ose-jenkins-agent-maven:v4.2
podman tag registry.redhat.io/rhscl/postgresql-96-rhel7:latest bastion.demo.ocp4.com:5000/rhscl/postgresql-96-rhel7:latest 
podman tag docker.io/openshiftdemos/gogs:0.11.34 bastion.demo.ocp4.com:5000/openshiftdemos/gogs:0.11.34
podman tag registry.redhat.io/jboss-eap-7/eap72-openshift:1.2 bastion.demo.ocp4.com:5000/jboss-eap-7/eap72-openshift:1.2
podman tag docker.io/siamaksade/sonarqube:latest bastion.demo.ocp4.com:5000/siamaksade/sonarqube:latest
```

##### c. push images to mirror registry
```shell
podman push bastion.demo.ocp4.com:5000/openshift4/ose-jenkins:v4.4
podman push bastion.demo.ocp4.com:5000/openshift4/ose-jenkins-agent-maven:v4.2
podman push bastion.demo.ocp4.com:5000/rhscl/postgresql-96-rhel7:latest 
podman push bastion.demo.ocp4.com:5000/openshiftdemos/gogs:0.11.34
podman push bastion.demo.ocp4.com:5000/jboss-eap-7/eap72-openshift:1.2
podman push bastion.demo.ocp4.com:5000/siamaksade/sonarqube:latest
```



### register certificate for mirror regitstry(cluster admin)

------

##### a. create a ConfigMap in the openshift-config namespace
```shell
oc login -u ocpadmin -p ocpadmin
vi mirror-registry-ca.yaml
```

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: mirror-registry-ca
data:
  bastion.demo.ocp4.com..5000: |
    -----BEGIN CERTIFICATE-----
    MIIGNTCCBB2gAwIBAgIJAPsLndCZRU5MMA0GCSqGSIb3DQEBCwUAMIGvMQswCQYD
    VQQGEwJLUjEOMAwGA1UECAwFU2VvdWwxDjAMBgNVBAcMBVNlb3VsMRAwDgYDVQQK
    DAdnb2RiYWJ5MRAwDgYDVQQLDAdra2FtYWdpMRgwFgYDVQQDDA8qLmdvZGJhYnku
    Y28ua3IxHDAaBgNVBAMMEyouZ3djLmdvZGJhYnkuY28ua3IxJDAiBgkqhkiG9w0B
    CQEWFWtrYW1hZ2lAZ29kYmFieS5jby5rcjAgFw0yMDA3MDgwNzMyNTVaGA8yMTIw
    MDYxNDA3MzI1NVowga8xCzAJBgNVBAYTAktSMQ4wDAYDVQQIDAVTZW91bDEOMAwG
    A1UEBwwFU2VvdWwxEDAOBgNVBAoMB2dvZGJhYnkxEDAOBgNVBAsMB2trYW1hZ2kx
    GDAWBgNVBAMMDyouZ29kYmFieS5jby5rcjEcMBoGA1UEAwwTKi5nd2MuZ29kYmFi
    eS5jby5rcjEkMCIGCSqGSIb3DQEJARYVa2thbWFnaUBnb2RiYWJ5LmNvLmtyMIIC
    IjANBgkqhkiG9w0BAQEFAAOCAg8AMIICCgKCAgEA061zlyk3oGVKZBTH//sOrPpi
    jm5sbny2RQwHb2mEgDWpLgv54ngYranqDMF3onB3S5U4lnTiwbnsr8vGjY44Ekol
    Cd/0iYMpwUeb70ZLyofXBVGEzjkwzd9WiKzsEIb6/EMuXrIA8i52Q058dRkyRuCA
    7obGIrcVAfCbxbHmKexDyHWeA/XTMLDgQrQXOH5D/gN6SShpcBtSELG9OW3Tduni
    W6/hwt5H1lEX/Q0bUEFSC0iVW3OjOl3BNF25e7H8L79SzOPNxO9QjYOX67/aWxjm
    QXLBEkn3DotVFtwKcqmN8BLLq1DaXRyJ/sZh/TVbhveYuZzHnDPWJXBFtr+jK0Dk
    PIZxaiH1PU1p4sAafeGod0yM7fMbeLWA0hL+8s2a9V05SvGsLqjoDv/lBkTX9V1R
    osXrQOJJlU0n6vQblNJY+gZumxDsx4orZ5wLLeG4/tZE3Mya8PXZKBxLjtkS76Gp
    C9ZcJo9uWRiDraImU7G+gfoFo1r756NcwN4egg+m5bq5406SSv8Rioh0s8a7+dCV
    bjRJYYvcl5TzWccNIv79jvrsa0oPiTXo5Bta2z7RwJW+dQrEeg2aO7FrWQpgM23o
    4f6L32XgH4zZlSo19b1XK0vtSb6k4V4cOFFzzOxcJGI0IH/Y4sB8abGzxF3XWSM4
    VVuMDKwRTtLK5IB3QE0CAwEAAaNQME4wHQYDVR0OBBYEFDY9AXDxjHL6x1HElYhV
    ZYjqQqIBMB8GA1UdIwQYMBaAFDY9AXDxjHL6x1HElYhVZYjqQqIBMAwGA1UdEwQF
    MAMBAf8wDQYJKoZIhvcNAQELBQADggIBAK0/WFOQIfDXRj7H7j6FqrDb+glRuzza
    3pdoU3gHQWRP0KqxIvTIvhQT6dtnfm0kbIepttUZ0HveLj9hT/xOkAk3CchGWPub
    gdz9xBKRjNO5zaj4jZt0OYHjNU411Bet4h2wVNuG2voCVOntzR7+9w6HpGYFxQ0Q
    TZWF9DmtwplWMmX48bAZlg/jiOQERvMX0zK+vAIUrZ0QGOID/mF81oLyHSgVy66i
    wAUbd+uJhqqhFYUhmNabuSasp+bqp01kbH9mhuj5v9F/ljhYYbxhqzYsDNJRe6Hr
    cmXz+MVYPrycJPI1iQzMcjnBpZN6Rx+B6WHX4vWtUWJsa9SjJyqC2Z4cYFG4kEox
    kYhHludnsN0/pmWbZR7X9YAq/wp8iOh1izlK4wGaU2B+r8b1Jbcr+mP5Nr0ja1z6
    9qNOVATnratuxZahB8139hXyHAzAfZR7h5hgAjDSpnQk9l5dR+dVzaGMBzKK0xj6
    ROp0X3RdJHCyRfCuu1ZO5BSJXoIBVw8G31Pr72NXtHwfjrgQyS5O7pNf0ZK4t6Pf
    u0zKa84GVmnHIfVuDCCyXS88UZf4s1GTYyJLoPqM3+JlsdoEB/rsCggYb1/jFkze
    uxcAmBmdCfw7IYkYKfc8CACxFVG8br3xTAejVp5p9Q7IVj0MNDOuGbbz6XDz51Pu
    /DdNAb8KHGYH
    -----END CERTIFICATE-----
```

```
oc create -f mirror-registry-ca.yaml -n openshift-config
```



##### b. configure an additional CA

```shell
oc edit image.config.openshift.io cluster
```

```yaml
(...)
spec:
  additionalTrustedCA:
    name: mirror-registry-ca
(...)
```



##### c. import jenkins image(cluster admin)

```sh
oc import-image jenkins:2 --from=bastion.demo.ocp4.com:5000/openshift4/ose-jenkins:v4.4 --confirm -n openshift
oc import-image postgresql:9.6 --from=bastion.demo.ocp4.com:5000/rhscl/postgresql-96-rhel7:latest --confirm -n openshift
oc import-image eap72-openshift:1.2 --from=bastion.demo.ocp4.com:5000/jboss-eap-7/eap72-openshift:1.2 --confirm -n openshift
```



### prepare ci/cd tools

------

##### a. create jenkins template(cluster admin)

```shell
oc create -f ocp-jenkins-template.json -n openshift 
```

- ocp-jenkins-template.json

```json
{
    "apiVersion": "v1",
    "kind": "Template",
    "labels": {
        "app": "jenkins-ephemeral",
        "template": "jenkins-ephemeral-template"
    },
    "message": "A Jenkins service has been created in your project.  Log into Jenkins with your OpenShift account.  The tutorial at https://github.com/openshift/origin/blob/master/examples/jenkins/README.md contains more information about using this template.",
    "metadata": {
        "annotations": {
            "description": "Jenkins service, without persistent storage.\n\nWARNING: Any data stored will be lost upon pod destruction. Only use this template for testing.",
            "iconClass": "icon-jenkins",
            "openshift.io/display-name": "Jenkins (Ephemeral)",
            "openshift.io/documentation-url": "https://docs.okd.io/latest/using_images/other_images/jenkins.html",
            "openshift.io/long-description": "This template deploys a Jenkins server capable of managing OpenShift Pipeline builds and supporting OpenShift-based oauth login.  The Jenkins configuration is stored in non-persistent storage, so this configuration should be used for experimental purposes only.",
            "openshift.io/provider-display-name": "Red Hat, Inc.",
            "openshift.io/support-url": "https://access.redhat.com",
            "tags": "instant-app,jenkins"
        },
        "name": "jenkins-ephemeral"
    },
    "objects": [
        {
            "apiVersion": "v1",
            "kind": "Route",
            "metadata": {
                "annotations": {
                    "haproxy.router.openshift.io/timeout": "4m",
                    "template.openshift.io/expose-uri": "http://{.spec.host}{.spec.path}"
                },
                "name": "${JENKINS_SERVICE_NAME}"
            },
            "spec": {
                "tls": {
                    "insecureEdgeTerminationPolicy": "Redirect",
                    "termination": "edge"
                },
                "to": {
                    "kind": "Service",
                    "name": "${JENKINS_SERVICE_NAME}"
                }
            }
        },
        {
            "apiVersion": "v1",
            "kind": "DeploymentConfig",
            "metadata": {
                "annotations": {
                    "template.alpha.openshift.io/wait-for-ready": "true"
                },
                "name": "${JENKINS_SERVICE_NAME}"
            },
            "spec": {
                "replicas": 1,
                "selector": {
                    "name": "${JENKINS_SERVICE_NAME}"
                },
                "strategy": {
                    "type": "Recreate"
                },
                "template": {
                    "metadata": {
                        "labels": {
                            "name": "${JENKINS_SERVICE_NAME}"
                        }
                    },
                    "spec": {
                        "containers": [
                            {
                                "env": [
                                    {
                                        "name": "OPENSHIFT_ENABLE_OAUTH",
                                        "value": "${ENABLE_OAUTH}"
                                    },
                                    {
                                        "name": "OPENSHIFT_ENABLE_REDIRECT_PROMPT",
                                        "value": "true"
                                    },
                                    {
                                        "name": "DISABLE_ADMINISTRATIVE_MONITORS",
                                        "value": "${DISABLE_ADMINISTRATIVE_MONITORS}"
                                    },
                                    {
                                        "name": "KUBERNETES_MASTER",
                                        "value": "https://kubernetes.default:443"
                                    },
                                    {
                                        "name": "KUBERNETES_TRUST_CERTIFICATES",
                                        "value": "true"
                                    },
                                    {
                                        "name": "JENKINS_SERVICE_NAME",
                                        "value": "${JENKINS_SERVICE_NAME}"
                                    },
                                    {
                                        "name": "JNLP_SERVICE_NAME",
                                        "value": "${JNLP_SERVICE_NAME}"
                                    }
                                ],
                                "image": " ",
                                "imagePullPolicy": "IfNotPresent",
                                "livenessProbe": {
                                    "failureThreshold": 2,
                                    "httpGet": {
                                        "path": "/login",
                                        "port": 8080
                                    },
                                    "initialDelaySeconds": 420,
                                    "periodSeconds": 360,
                                    "timeoutSeconds": 240
                                },
                                "name": "jenkins",
                                "readinessProbe": {
                                    "httpGet": {
                                        "path": "/login",
                                        "port": 8080
                                    },
                                    "initialDelaySeconds": 3,
                                    "timeoutSeconds": 240
                                },
                                "resources": {
                                    "limits": {
                                        "memory": "${MEMORY_LIMIT}"
                                    }
                                },
                                "securityContext": {
                                    "capabilities": {},
                                    "privileged": false
                                },
                                "terminationMessagePath": "/dev/termination-log",
                                "volumeMounts": [
                                    {
                                        "mountPath": "/var/lib/jenkins",
                                        "name": "${JENKINS_SERVICE_NAME}-data"
                                    }
                                ]
                            }
                        ],
                        "dnsPolicy": "ClusterFirst",
                        "restartPolicy": "Always",
                        "serviceAccountName": "${JENKINS_SERVICE_NAME}",
                        "volumes": [
                            {
                                "emptyDir": {
                                    "medium": ""
                                },
                                "name": "${JENKINS_SERVICE_NAME}-data"
                            }
                        ]
                    }
                },
                "triggers": [
                    {
                        "imageChangeParams": {
                            "automatic": true,
                            "containerNames": [
                                "jenkins"
                            ],
                            "from": {
                                "kind": "ImageStreamTag",
                                "name": "${JENKINS_IMAGE_STREAM_TAG}",
                                "namespace": "${NAMESPACE}"
                            },
                            "lastTriggeredImage": ""
                        },
                        "type": "ImageChange"
                    },
                    {
                        "type": "ConfigChange"
                    }
                ]
            }
        },
        {
            "apiVersion": "v1",
            "kind": "ServiceAccount",
            "metadata": {
                "annotations": {
                    "serviceaccounts.openshift.io/oauth-redirectreference.jenkins": "{\"kind\":\"OAuthRedirectReference\",\"apiVersion\":\"v1\",\"reference\":{\"kind\":\"Route\",\"name\":\"${JENKINS_SERVICE_NAME}\"}}"
                },
                "name": "${JENKINS_SERVICE_NAME}"
            }
        },
        {
            "apiVersion": "v1",
            "groupNames": null,
            "kind": "RoleBinding",
            "metadata": {
                "name": "${JENKINS_SERVICE_NAME}_edit"
            },
            "roleRef": {
                "name": "edit"
            },
            "subjects": [
                {
                    "kind": "ServiceAccount",
                    "name": "${JENKINS_SERVICE_NAME}"
                }
            ]
        },
        {
            "apiVersion": "v1",
            "kind": "Service",
            "metadata": {
                "name": "${JNLP_SERVICE_NAME}"
            },
            "spec": {
                "ports": [
                    {
                        "name": "agent",
                        "nodePort": 0,
                        "port": 50000,
                        "protocol": "TCP",
                        "targetPort": 50000
                    }
                ],
                "selector": {
                    "name": "${JENKINS_SERVICE_NAME}"
                },
                "sessionAffinity": "None",
                "type": "ClusterIP"
            }
        },
        {
            "apiVersion": "v1",
            "kind": "Service",
            "metadata": {
                "annotations": {
                    "service.alpha.openshift.io/dependencies": "[{\"name\": \"${JNLP_SERVICE_NAME}\", \"namespace\": \"\", \"kind\": \"Service\"}]",
                    "service.openshift.io/infrastructure": "true"
                },
                "name": "${JENKINS_SERVICE_NAME}"
            },
            "spec": {
                "ports": [
                    {
                        "name": "web",
                        "nodePort": 0,
                        "port": 80,
                        "protocol": "TCP",
                        "targetPort": 8080
                    }
                ],
                "selector": {
                    "name": "${JENKINS_SERVICE_NAME}"
                },
                "sessionAffinity": "None",
                "type": "ClusterIP"
            }
        }
    ],
    "parameters": [
        {
            "description": "The name of the OpenShift Service exposed for the Jenkins container.",
            "displayName": "Jenkins Service Name",
            "name": "JENKINS_SERVICE_NAME",
            "value": "jenkins"
        },
        {
            "description": "The name of the service used for master/slave communication.",
            "displayName": "Jenkins JNLP Service Name",
            "name": "JNLP_SERVICE_NAME",
            "value": "jenkins-jnlp"
        },
        {
            "description": "Whether to enable OAuth OpenShift integration. If false, the static account 'admin' will be initialized with the password 'password'.",
            "displayName": "Enable OAuth in Jenkins",
            "name": "ENABLE_OAUTH",
            "value": "true"
        },
        {
            "description": "Maximum amount of memory the container can use.",
            "displayName": "Memory Limit",
            "name": "MEMORY_LIMIT",
            "value": "512Mi"
        },
        {
            "description": "The OpenShift Namespace where the Jenkins ImageStream resides.",
            "displayName": "Jenkins ImageStream Namespace",
            "name": "NAMESPACE",
            "value": "openshift"
        },
        {
            "description": "Whether to perform memory intensive, possibly slow, synchronization with the Jenkins Update Center on start.  If true, the Jenkins core update monitor and site warnings monitor are disabled.",
            "displayName": "Disable memory intensive administrative monitors",
            "name": "DISABLE_ADMINISTRATIVE_MONITORS",
            "value": "false"
        },
        {
            "description": "Name of the ImageStreamTag to be used for the Jenkins image.",
            "displayName": "Jenkins ImageStreamTag",
            "name": "JENKINS_IMAGE_STREAM_TAG",
            "value": "jenkins:2"
        }
    ]
}
```



##### b. create projecs for ci/cd(project admin)

```shell
oc login -u devadm -p devadm

oc new-project dev-test   --display-name="test - Dev"
oc new-project stage-test --display-name="test - Stage"
oc new-project cicd-test  --display-name="CI/CD"

oc policy add-role-to-group edit system:serviceaccounts:cicd-test -n dev-test
oc policy add-role-to-group edit system:serviceaccounts:cicd-test -n stage-test
```




##### c. config builder secret link(optional, only using mirror regitstry)

```shell
oc -n cicd-test create secret docker-registry mirror-reg-pull \
    --docker-server=bastion.demo.ocp4.com:5000 \
    --docker-username=admin \
    --docker-password=admin  \
    --docker-email=''

oc -n dev-test create secret docker-registry mirror-reg-pull \
    --docker-server=bastion.demo.ocp4.com:5000 \
    --docker-username=admin \
    --docker-password=admin  \
    --docker-email=''
	
oc -n stage-test create secret docker-registry mirror-reg-pull \
    --docker-server=bastion.demo.ocp4.com:5000 \
    --docker-username=admin \
    --docker-password=admin  \
    --docker-email=''
	
oc -n cicd-test secret link builder mirror-reg-pull
oc -n dev-test secret link builder mirror-reg-pull
oc -n stage-test secret link builder mirror-reg-pull

oc import-image sonarqube:latest --from=bastion.demo.ocp4.com:5000/siamaksade/sonarqube:latest --confirm -n cicd-test
oc import-image gogs:0.11.34 --from=bastion.demo.ocp4.com:5000/openshiftdemos/gogs:0.11.34 --confirm -n cicd-test
```



##### d. install jenkins
```shell
oc new-app jenkins-ephemeral -n cicd-test
```



##### e. adjust jenkins(optional)
```
oc set resources dc/jenkins --limits=cpu=2,memory=2Gi --requests=cpu=100m,memory=512Mi
```



##### f. instal gogs

```
HOSTNAME=$(oc get route jenkins -o template --template='{{.spec.host}}' | sed "s/jenkins-//g")
GOGS_HOSTNAME="gogs-$HOSTNAME"

if [ "${EPHEMERAL}" == "true" ] ; then
  sed -i 's/docker.io\/openshiftdemos\/gogs/bastion.demo.ocp4.com\/openshiftdemos\/gogs/g' gogs-template-ephemeral.yaml
  oc new-app -f gogs-template-ephemeral.yaml \
      --param=GOGS_VERSION=0.11.34 \
      --param=DATABASE_VERSION=9.6 \
      --param=HOSTNAME=$GOGS_HOSTNAME \
      --param=SKIP_TLS_VERIFY=true
else
  sed -i 's/docker.io\/openshiftdemos\/gogs/bastion.demo.ocp4.com\/openshiftdemos\/gogs/g' gogs-template.yaml
  oc new-app -f gogs-template.yaml \
      --param=GOGS_VERSION=0.11.34 \
      --param=DATABASE_VERSION=9.6 \
      --param=HOSTNAME=$GOGS_HOSTNAME \
      --param=SKIP_TLS_VERIFY=true
fi
```

- gogs-template-ephemeral.yaml

```yaml
kind: Template
apiVersion: v1
metadata:
  annotations:
    description: The Gogs git server (https://gogs.io/)
    tags: instant-app,gogs,go,golang
  name: gogs
objects:
- kind: ServiceAccount
  apiVersion: v1
  metadata:
    labels:
      app: ${APPLICATION_NAME}
    name: ${APPLICATION_NAME}
- kind: Service
  apiVersion: v1
  metadata:
    annotations:
      description: Exposes the database server
    name: ${APPLICATION_NAME}-postgresql
  spec:
    ports:
    - name: postgresql
      port: 5432
      targetPort: 5432
        selector:
      name: ${APPLICATION_NAME}-postgresql
- kind: DeploymentConfig
  apiVersion: v1
  metadata:
    annotations:
      description: Defines how to deploy the database
    name: ${APPLICATION_NAME}-postgresql
    labels:
      app: ${APPLICATION_NAME}
  spec:
    replicas: 1
    selector:
      name: ${APPLICATION_NAME}-postgresql
    strategy:
      type: Recreate
    template:
      metadata:
        labels:
          name: ${APPLICATION_NAME}-postgresql
        name: ${APPLICATION_NAME}-postgresql
      spec:
        serviceAccountName: ${APPLICATION_NAME}
        containers:
        - env:
          - name: POSTGRESQL_USER
            value: ${DATABASE_USER}
          - name: POSTGRESQL_PASSWORD
            value: ${DATABASE_PASSWORD}
          - name: POSTGRESQL_DATABASE
            value: ${DATABASE_NAME}
          - name: POSTGRESQL_MAX_CONNECTIONS
            value: ${DATABASE_MAX_CONNECTIONS}
          - name: POSTGRESQL_SHARED_BUFFERS
            value: ${DATABASE_SHARED_BUFFERS}
          - name: POSTGRESQL_ADMIN_PASSWORD
            value: ${DATABASE_ADMIN_PASSWORD}
          image: ' '
          livenessProbe:
            initialDelaySeconds: 30
            tcpSocket:
              port: 5432
            timeoutSeconds: 1
          name: postgresql
          ports:
          - containerPort: 5432
          readinessProbe:
            exec:
              command:
              - /bin/sh
              - -i
              - -c
              - psql -h 127.0.0.1 -U ${POSTGRESQL_USER} -q -d ${POSTGRESQL_DATABASE} -c 'SELECT 1'
            initialDelaySeconds: 5
            timeoutSeconds: 1
          resources:
            limits:
              memory: 512Mi
          volumeMounts:
          - mountPath: /var/lib/pgsql/data
            name: gogs-postgres-data
        volumes:
        - name: gogs-postgres-data
          emptyDir: {}
    triggers:
    - imageChangeParams:
      automatic: true
      containerNames:
      - postgresql
        from:
        kind: ImageStreamTag
        name: postgresql:${DATABASE_VERSION}
        namespace: openshift
        type: ImageChange
    - type: ConfigChange
- kind: Service
  apiVersion: v1
  metadata:
    annotations:
      description: The Gogs server's http port
      service.alpha.openshift.io/dependencies: '[{"name":"${APPLICATION_NAME}-postgresql","namespace":"","kind":"Service"}]'
    labels:
      app: ${APPLICATION_NAME}
    name: ${APPLICATION_NAME}
  spec:
    ports:
    - name: 3000-tcp
      port: 3000
      protocol: TCP
      targetPort: 3000
    - name: 10022-tcp
      port: 10022
      protocol: TCP
      targetPort: 10022
        selector:
      app: ${APPLICATION_NAME}
      deploymentconfig: ${APPLICATION_NAME}
        sessionAffinity: None
        type: ClusterIP
- kind: Route
  apiVersion: v1
  id: ${APPLICATION_NAME}-http
  metadata:
    annotations:
      description: Route for application's http service.
    labels:
      app: ${APPLICATION_NAME}
    name: ${APPLICATION_NAME}
  spec:
    host: ${HOSTNAME}
    port:
      targetPort: 3000-tcp
    to:
      name: ${APPLICATION_NAME}
- kind: Route
  apiVersion: v1
  id: ${APPLICATION_NAME}-ssh
  metadata:
    annotations:
      description: Route for application's ssh service.
    labels:
      app: ${APPLICATION_NAME}
    name: ${APPLICATION_NAME}-ssh
  spec:
    host: secure${HOSTNAME}
    port:
      targetPort: 10022-tcp
    to:
      name: ${APPLICATION_NAME}
- kind: DeploymentConfig
  apiVersion: v1
  metadata:
    labels:
      app: ${APPLICATION_NAME}
    name: ${APPLICATION_NAME}
  spec:
    replicas: 1
    selector:
      app: ${APPLICATION_NAME}
      deploymentconfig: ${APPLICATION_NAME}
    strategy:
      resources: {}
      rollingParams:
        intervalSeconds: 1
        maxSurge: 25%
        maxUnavailable: 25%
        timeoutSeconds: 600
        updatePeriodSeconds: 1
      type: Rolling
    template:
      metadata:
        creationTimestamp: null
        labels:
          app: ${APPLICATION_NAME}
          deploymentconfig: ${APPLICATION_NAME}
      spec:
        serviceAccountName: ${APPLICATION_NAME}
        containers:
        - image: " "
          imagePullPolicy: Always
          name: ${APPLICATION_NAME}
          ports:
          - containerPort: 3000
            protocol: TCP
          - containerPort: 10022
            protocol: TCP
          resources: {}
          terminationMessagePath: /dev/termination-log
          volumeMounts:
          - name: gogs-data
            mountPath: /opt/gogs/data
          - name: gogs-config
            mountPath: /etc/gogs/conf
          readinessProbe:
              httpGet:
                path: /
                port: 3000
                scheme: HTTP
              initialDelaySeconds: 3
              timeoutSeconds: 1
              periodSeconds: 20
              successThreshold: 1
              failureThreshold: 3
          livenessProbe:
              httpGet:
                path: /
                port: 3000
                scheme: HTTP
              initialDelaySeconds: 3
              timeoutSeconds: 1
              periodSeconds: 10
              successThreshold: 1
              failureThreshold: 3
        dnsPolicy: ClusterFirst
        restartPolicy: Always
        securityContext: {}
        terminationGracePeriodSeconds: 30
        volumes:
        - name: gogs-data
          emptyDir: {}
        - name: gogs-config
          configMap:
            name: gogs-config
            items:
              - key: app.ini
                path: app.ini
    test: false
    triggers:
    - type: ConfigChange
    - imageChangeParams:
      automatic: true
      containerNames:
      - ${APPLICATION_NAME}
        from:
        kind: ImageStreamTag
        name: ${APPLICATION_NAME}:${GOGS_VERSION}
        type: ImageChange
- kind: ImageStream
  apiVersion: v1
  metadata:
    labels:
      app: ${APPLICATION_NAME}
    name: ${APPLICATION_NAME}
  spec:
    tags:
    - name: "${GOGS_VERSION}"
      from:
        kind: DockerImage
        name: bastion.demo.ocp4.com/openshiftdemos/gogs:${GOGS_VERSION}
      importPolicy: {}
      annotations:
        description: The Gogs git server docker image
        tags: gogs,go,golang
        version: "${GOGS_VERSION}"
- kind: ConfigMap
  apiVersion: v1
  metadata:
    name: gogs-config
    labels:
      app: ${APPLICATION_NAME}
  data:
    app.ini: |
      RUN_MODE = prod
      RUN_USER = gogs

      [database]
      DB_TYPE  = postgres
      HOST     = ${APPLICATION_NAME}-postgresql:5432
      NAME     = ${DATABASE_NAME}
      USER     = ${DATABASE_USER}
      PASSWD   = ${DATABASE_PASSWORD}
      
      [repository]
      ROOT = /opt/gogs/data/repositories
      
      [server]
      ROOT_URL=http://${HOSTNAME}
      SSH_DOMAIN=secure${HOSTNAME}
      START_SSH_SERVER=true
      SSH_LISTEN_PORT=10022
      
      [security]
      INSTALL_LOCK = ${INSTALL_LOCK}
      
      [service]
      ENABLE_CAPTCHA = false
      
      [webhook]
      SKIP_TLS_VERIFY = ${SKIP_TLS_VERIFY}
parameters:
- description: The name for the application.
  name: APPLICATION_NAME
  required: true
  value: gogs
- description: 'Custom hostname for http service route.  Leave blank for default hostname, e.g.: <application-name>-<project>.<default-domain-suffix>'
  name: HOSTNAME
  required: true
- displayName: Database Username
  from: gogs
  value: gogs
  name: DATABASE_USER
- displayName: Database Password
  from: '[a-zA-Z0-9]{8}'
  value: gogs
  name: DATABASE_PASSWORD
- displayName: Database Name
  name: DATABASE_NAME
  value: gogs
- displayName: Database Admin Password
  from: '[a-zA-Z0-9]{8}'
  generate: expression
  name: DATABASE_ADMIN_PASSWORD
- displayName: Maximum Database Connections
  name: DATABASE_MAX_CONNECTIONS
  value: "100"
- displayName: Shared Buffer Amount
  name: DATABASE_SHARED_BUFFERS
  value: 12MB
- displayName: Database version (PostgreSQL)
  name: DATABASE_VERSION
  value: "9.5"
- name: GOGS_VERSION
  displayName: Gogs Version
  description: 'Version of the Gogs container image to be used (check the available version https://hub.docker.com/r/openshiftdemos/gogs/tags)'
  value: "0.11.34"
  required: true
- name: INSTALL_LOCK
  displayName: Installation lock
  description: 'If set to true, installation (/install) page will be disabled. Set to false if you want to run the installation wizard via web'
  value: "true"
- name: SKIP_TLS_VERIFY
  displayName: Skip TLS verification on webhooks
  description: Skip TLS verification on webhooks. Enable with caution!
  value: "false"
```



---------------------------------------------------------------------------------------------------

##### g. install sonarqube
```shell
sed -i 's/docker.io\/siamaksade\/sonarqube/bastion.demo.ocp4.com\/siamaksade\/sonarqube/g' sonarqube-template.yml
oc new-app -f sonarqube-template.yml --param=SONARQUBE_MEMORY_LIMIT=1Gi
oc set resources dc/sonardb --limits=cpu=500m,memory=1Gi --requests=cpu=50m,memory=128Mi
oc set resources dc/sonarqube --limits=cpu=1,memory=2Gi --requests=cpu=50m,memory=128Mi 
```

- sonarqube-template.yml 

```yaml
apiVersion: v1
kind: Template
metadata:
  name: "sonarqube"
objects:

- apiVersion: v1
  kind: ImageStream
  metadata:
    labels:
      app: sonarqube
    name: sonarqube
  spec:
    tags:
    - annotations:
      description: The SonarQube Docker image
      tags: sonarqube
      from:
        kind: DockerImage
        name: docker.io/siamaksade/sonarqube:latest
      importPolicy: {}
      name: latest
- apiVersion: v1
  kind: Secret
  stringData:
    database-name: ${POSTGRES_DATABASE_NAME}
    database-password: ${POSTGRES_PASSWORD}
    database-user: ${POSTGRES_USERNAME}
  metadata:
    labels:
      app: sonarqube
      template: postgresql-template
    name: sonardb
  type: Opaque
- apiVersion: v1
  stringData:
    password: ${SONAR_LDAP_BIND_PASSWORD}
    username: ${SONAR_LDAP_BIND_DN}
  kind: Secret
  metadata:
    name: sonar-ldap-bind-dn
  type: kubernetes.io/basic-auth
- apiVersion: v1
  kind: DeploymentConfig
  metadata:
    generation: 1
    labels:
      app: sonarqube
      template: postgresql-template
    name: sonardb
  spec:
    replicas: 1
    selector:
      name: sonardb
    strategy:
      activeDeadlineSeconds: 21600
      recreateParams:
        timeoutSeconds: 600
      resources: {}
      type: Recreate
    template:
      metadata:
        labels:
          name: sonardb
      spec:
        containers:
        - env:
          - name: POSTGRESQL_USER
            valueFrom:
              secretKeyRef:
                key: database-user
                name: sonardb
          - name: POSTGRESQL_PASSWORD
            valueFrom:
              secretKeyRef:
                key: database-password
                name: sonardb
          - name: POSTGRESQL_DATABASE
            valueFrom:
              secretKeyRef:
                key: database-name
                name: sonardb
          imagePullPolicy: IfNotPresent
          livenessProbe:
            failureThreshold: 3
            initialDelaySeconds: 30
            periodSeconds: 10
            successThreshold: 1
            tcpSocket:
              port: 5432
            timeoutSeconds: 1
          name: postgresql
          ports:
          - containerPort: 5432
            protocol: TCP
          readinessProbe:
            exec:
              command:
              - /bin/sh
              - -i
              - -c
              - psql -h 127.0.0.1 -U $POSTGRESQL_USER -q -d $POSTGRESQL_DATABASE -c
                'SELECT 1'
            failureThreshold: 3
            initialDelaySeconds: 5
            periodSeconds: 10
            successThreshold: 1
            timeoutSeconds: 1
          resources:
            limits:
              memory: ${POSTGRES_CONTAINER_MEMORY_SIZE_LIMIT}
              cpu: ${POSTGRES_CONTAINER_CPU_LIMIT}
            requests:
              memory: 1Gi
          securityContext:
            capabilities: {}
            privileged: false
          terminationMessagePath: /dev/termination-log
          terminationMessagePolicy: File
          volumeMounts:
          - mountPath: /var/lib/pgsql/data
            name: sonardb-data
        dnsPolicy: ClusterFirst
        restartPolicy: Always
        schedulerName: default-scheduler
        securityContext: {}
        terminationGracePeriodSeconds: 30
        volumes:
        - name: sonardb-data
          emptyDir: {}
    test: false
    triggers:
    - imageChangeParams:
      automatic: true
      containerNames:
        - postgresql
          from:
            kind: ImageStreamTag
            name: 'postgresql:9.6'
            namespace: openshift
          type: ImageChange
    - type: ConfigChange
- apiVersion: v1
  kind: DeploymentConfig
  metadata:
    generation: 1
    labels:
      app: sonarqube
    name: sonarqube
  spec:
    replicas: 1
    selector:
      app: sonarqube
      deploymentconfig: sonarqube
    strategy:
      activeDeadlineSeconds: 21600
      recreateParams:
        timeoutSeconds: 600
      type: Recreate
    template:
      metadata:
        labels:
          app: sonarqube
          deploymentconfig: sonarqube
      spec:
        containers:
        - env:
          - name: JDBC_URL
            value: jdbc:postgresql://sonardb:5432/sonar
          - name: JDBC_USERNAME
            valueFrom:
              secretKeyRef:
                key: database-user
                name: sonardb
          - name: JDBC_PASSWORD
            valueFrom:
              secretKeyRef:
                key: database-password
                name: sonardb
          - name: FORCE_AUTHENTICATION
            value: ${FORCE_AUTHENTICATION}
          - name: PROXY_HOST
            value: ${PROXY_HOST}
          - name: PROXY_PORT
            value: ${PROXY_PORT}
          - name: PROXY_USER
            value: ${PROXY_USER}
          - name: PROXY_PASSWORD
            value: ${PROXY_PASSWORD}
          - name: LDAP_URL
            value: ${SONAR_LDAP_URL}
          - name: LDAP_REALM
            value: ${SONAR_AUTH_REALM}
          - name: LDAP_AUTHENTICATION
            value: ${SONAR_LDAP_BIND_METHOD}
          - name: LDAP_USER_BASEDN
            value: ${SONAR_BASE_DN}
          - name: LDAP_USER_REAL_NAME_ATTR
            value: ${SONAR_LDAP_USER_REAL_NAME_ATTR}
          - name: LDAP_USER_EMAIL_ATTR
            value: ${SONAR_LDAP_USER_EMAIL_ATTR}
          - name: LDAP_USER_REQUEST
            value: ${SONAR_LDAP_USER_REQUEST}
          - name: LDAP_GROUP_BASEDN
            value: ${SONAR_LDAP_GROUP_BASEDN}
          - name: LDAP_GROUP_REQUEST
            value: ${SONAR_LDAP_GROUP_REQUEST}
          - name: LDAP_GROUP_ID_ATTR
            value: ${SONAR_LDAP_GROUP_ID_ATTR}
          - name: LDAP_CONTEXTFACTORY
            value: ${SONAR_LDAP_CONTEXTFACTORY}
          - name: SONAR_AUTOCREATE_USERS
            value: ${SONAR_AUTOCREATE_USERS}
          - name: LDAP_BINDDN
            valueFrom:
              secretKeyRef:
                key: username
                name: sonar-ldap-bind-dn
          - name: LDAP_BINDPASSWD
            valueFrom:
              secretKeyRef:
                key: password
                name: sonar-ldap-bind-dn
          imagePullPolicy: Always
          livenessProbe:
            failureThreshold: 3
            httpGet:
              path: /
              port: 9000
              scheme: HTTP
            initialDelaySeconds: 45
            periodSeconds: 10
            successThreshold: 1
            timeoutSeconds: 1
          name: sonarqube
          ports:
          - containerPort: 9000
            protocol: TCP
          readinessProbe:
            failureThreshold: 3
            httpGet:
              path: /
              port: 9000
              scheme: HTTP
            initialDelaySeconds: 10
            periodSeconds: 10
            successThreshold: 1
            timeoutSeconds: 1
          resources:
            requests:
              cpu: 200m
              memory: 1Gi
            limits:
              cpu: ${SONARQUBE_CPU_LIMIT}
              memory: ${SONARQUBE_MEMORY_LIMIT}
          terminationMessagePath: /dev/termination-log
          terminationMessagePolicy: File
          volumeMounts:
          - mountPath: /opt/sonarqube/data
            name: sonar-data
        dnsPolicy: ClusterFirst
        restartPolicy: Always
        schedulerName: default-scheduler
        securityContext: {}
        terminationGracePeriodSeconds: 30
        volumes:
        - name: sonar-data
          emptyDir: {}
    test: false
    triggers:
    - imageChangeParams:
      automatic: true
      containerNames:
      - sonarqube
        from:
        kind: ImageStreamTag
        name: sonarqube:latest
        type: ImageChange
    - type: ConfigChange
- apiVersion: v1
  kind: Route
  metadata:
    labels:
      app: sonarqube
    name: sonarqube
  spec:
    port:
      targetPort: 9000-tcp
    tls:
      termination: edge
    to:
      kind: Service
      name: sonarqube
      weight: 100
    wildcardPolicy: None
- apiVersion: v1
  kind: Service
  metadata:
    annotations:
      template.openshift.io/expose-uri: postgres://{.spec.clusterIP}:{.spec.ports[?(.name=="postgresql")].port}
    labels:
      app: sonarqube
      template: postgresql-template
    name: sonardb
  spec:
    ports:
    - name: postgresql
      port: 5432
      protocol: TCP
      targetPort: 5432
        selector:
      name: sonardb
        sessionAffinity: None
        type: ClusterIP
      status:
        loadBalancer: {}
- apiVersion: v1
  kind: Service
  metadata:
    labels:
      app: sonarqube
    name: sonarqube
  spec:
    ports:
    - name: 9000-tcp
      port: 9000
      protocol: TCP
      targetPort: 9000
        selector:
      deploymentconfig: sonarqube
        sessionAffinity: None
        type: ClusterIP
      status:
        loadBalancer: {}
      parameters:
  - description: Password for the Posgres Database to be used by Sonarqube
    displayName: Postgres password
    name: POSTGRES_PASSWORD
    generate: expression
    from: '[a-zA-Z0-9]{16}'
    required: true
  - description: Username for the Posgres Database to be used by Sonarqube
    displayName: Postgres username
    name: POSTGRES_USERNAME
    generate: expression
    from: 'user[a-z0-9]{8}'
    required: true
  - description: Database name for the Posgres Database to be used by Sonarqube
    displayName: Postgres database name
    name: POSTGRES_DATABASE_NAME
    value: sonar
    required: true
  - description: Postgres Container Memory size limit
    displayName: Postgres Container Memory size limit
    name: POSTGRES_CONTAINER_MEMORY_SIZE_LIMIT
    value: 1Gi
  - description: Postgres Container CPU limit
    displayName: Postgres Container CPU limit
    name: POSTGRES_CONTAINER_CPU_LIMIT
    value: "1"
  - name: SONARQUBE_MEMORY_LIMIT
    description: SonarQube memory
    displayName: SonarQube memory
    value: 2Gi
  - name: SONARQUBE_CPU_LIMIT
    description: SonarQube Container CPU limit
    displayName: SonarQube Container CPU limit
    value: "2"
  - name: FORCE_AUTHENTICATION
    displayName: Force authentication
    value: "false"
  - name: SONAR_AUTH_REALM
    value: ''
    description: The type of authentication that SonarQube should be using (None or LDAP) (Ref - https://docs.sonarqube.org/display/PLUG/LDAP+Plugin)
    displayName: SonarQube Authentication Realm
  - name: SONAR_AUTOCREATE_USERS
    value: 'false'
    description: When using an external authentication system, should SonarQube automatically create accounts for users?
    displayName: Enable auto-creation of users from external authentication systems?
    required: true
  - name: PROXY_HOST
    description: Hostname of proxy server the SonarQube application should use to access the Internet
    displayName: Proxy server hostname/IP
  - name: PROXY_PORT
    description: TCP port of proxy server the SonarQube application should use to access the Internet
    displayName: Proxy server port
  - name: PROXY_USER
    description: Username credential when the Proxy Server requires authentication
    displayName: Proxy server username
  - name: PROXY_PASSWORD
    description: Password credential when the Proxy Server requires authentication
    displayName: Proxy server password
  - name: SONAR_LDAP_BIND_DN
    description: When using LDAP authentication, this is the Distinguished Name used for binding to the LDAP server
    displayName: LDAP Bind DN
  - name: SONAR_LDAP_BIND_PASSWORD
    description: When using LDAP for authentication, this is the password with which to bind to the LDAP server
    displayName: LDAP Bind Password
  - name: SONAR_LDAP_URL
    description: When using LDAP for authentication, this is the URL of the LDAP server in the form of ldap(s)://<hostname>:<port>
    displayName: LDAP Server URL
  - name: SONAR_LDAP_REALM
    description: When using LDAP, this allows for specifying a Realm within the directory server (Usually not used)
    displayName: LDAP Realm
  - name: SONAR_LDAP_AUTHENTICATION
    description: When using LDAP, this is the bind method (simple, GSSAPI, kerberos, CRAM-MD5, DIGEST-MD5)
    displayName: LDAP Bind Mode
  - name: SONAR_LDAP_USER_BASEDN
    description: The Base DN under which SonarQube should search for user accounts in the LDAP directory
    displayName: LDAP User Base DN
  - name: SONAR_LDAP_USER_REAL_NAME_ATTR
    description: The LDAP attribute which should be referenced to get a user's full name
    displayName: LDAP Real Name Attribute
  - name: SONAR_LDAP_USER_EMAIL_ATTR
    description: The LDAP attribute which should be referenced to get a user's e-mail address
    displayName: LDAP User E-Mail Attribute
  - name: SONAR_LDAP_USER_REQUEST
    description: An LDAP filter to be used to search for user objects in the LDAP directory
    displayName: LDAP User Request Filter
  - name: SONAR_LDAP_GROUP_BASEDN
    description: The Base DN under which SonarQube should search for groups in the LDAP directory
    displayName: LDAP Group Base DN
  - name: SONAR_LDAP_GROUP_REQUEST
    description: An LDAP filter to be used to search for group objects in the LDAP directory
    displayName: LDAP Group Request Filter
  - name: SONAR_LDAP_GROUP_ID_ATTR
    description: The LDAP attribute which should be referenced to get a group's ID
    displayName: LDAP Group Name Attribute
  - name: SONAR_LDAP_CONTEXTFACTORY
    description: The ContextFactory implementation to be used when communicating with the LDAP server
    displayName: LDAP Context Factory
    value: com.sun.jndi.ldap.LdapCtxFactory
```



### Application Initial Deploy ###

------

##### a. dev

```
oc new-build --name=test --image-stream=eap72-openshift:1.2 --binary=true -n dev-test
oc new-app test:latest --allow-missing-images -n dev-test
oc set triggers dc -l app=test --containers=test --from-image=test:latest --manual -n dev-test
```

##### b. stage
```
oc new-app test:stage --allow-missing-images -n stage-test
oc set triggers dc -l app=test --containers=test --from-image=test:stage --manual -n stage-test
```

##### c. quay enable(optional)

```shell
if [ "${ENABLE_QUAY}" == "true" ] ; then

  oc create secret generic quay-cicd-secret --from-literal="username=${QUAY_USERNAME}" --from-literal="password=${QUAY_PASSWORD}" -n cicd-test
  oc label secret quay-cicd-secret credential.sync.jenkins.openshift.io=true -n cicd-test

  oc create secret docker-registry quay-cicd-secret --docker-server=quay.io --docker-username="${QUAY_USERNAME}" --docker-password="${QUAY_PASSWORD}" --docker-email=cicd@redhat.com -n dev-test
  oc new-build --name=test --image-stream=eap72-openshift:1.2 --binary=true --push-secret=quay-cicd-secret --to-docker --to='quay.io/${QUAY_USERNAME}/test-app:latest' -n dev-test
  oc new-app --name=test --docker-image=quay.io/${QUAY_USERNAME}/${QUAY_REPOSITORY}:latest --allow-missing-images -n dev-test
  oc set triggers dc test --remove-all -n dev-test
  oc patch dc test -p '{"spec": {"template": {"spec": {"containers": [{"name": "test", "imagePullPolicy": "Always"}]}}}}' -n dev-test
  oc delete is test -n dev-test
  oc secrets link default quay-cicd-secret --for=pull -n dev-test

  oc create secret docker-registry quay-cicd-secret --docker-server=quay.io --docker-username="${QUAY_USERNAME}" --docker-password="${QUAY_PASSWORD}" --docker-email=cicd@redhat.com -n stage-test
  oc new-app --name=test --docker-image=quay.io/${QUAY_USERNAME}/test-app:stage --allow-missing-images -n stage-test
  oc set triggers dc test --remove-all -n stage-test
  oc patch dc test -p '{"spec": {"template": {"spec": {"containers": [{"name": "test", "imagePullPolicy": "Always"}]}}}}' -n stage-test
  oc delete is test -n stage-test
  oc secrets link default quay-cicd-secret --for=pull -n stage-test
else

  oc new-build --name=test --image-stream=eap72-openshift:1.2 --binary=true -n dev-test
  oc new-app test:latest --allow-missing-images -n dev-test
  oc set triggers dc -l app=test --containers=test --from-image=test:latest --manual -n dev-test

  oc new-app test:stage --allow-missing-images -n stage-test
  oc set triggers dc -l app=test --containers=test --from-image=test:stage --manual -n stage-test
fi
```

##### d. dev(additional config)
```
oc expose dc/test --port=8080 -n dev-test
oc expose svc/test -n dev-test
oc set probe -n dev-test dc/test --readiness -- /bin/bash -c /opt/eap/bin/readinessProbe.sh
oc set probe -n dev-test dc/test --liveness --initial-delay-seconds=60 -- /bin/bash -c /opt/eap/bin/livenessProbe.sh 
oc rollout cancel dc/test -n dev-test
```

##### e. stage(additional config)
```
oc expose dc/test --port=8080 -n stage-test
oc expose svc/test -n stage-test
oc set probe dc/test -n stage-test --readiness -- /bin/bash -c /opt/eap/bin/readinessProbe.sh 
oc set probe dc/test -n stage-test --liveness --initial-delay-seconds=60 -- /bin/bash -c /opt/eap/bin/livenessProbe.sh 
oc rollout cancel dc/test -n stage-test
```



### prepare application(requirement : gogs user is already created through gogs web ui) ###

------

```shell
git clone https://github.com/OpenShiftDemos/openshift-tasks.git
cd openshift-tasks
git remote set-url origin http://gogs-cicd-test.apps.demo.ocp4.com/gogs/openshift-tasks.git
git push -u origin master
```





### create pipeline build

------

```shell
oc new-app -f cicd-template.yaml -p DEV_PROJECT=dev-test -p STAGE_PROJECT=stage-test -p ENABLE_QUAY=false -n cicd-test
```



cicd-template.yaml

```yaml
apiVersion: v1
kind: Template
labels:
  template: cicd
  group: cicd
metadata:
  annotations:
    iconClass: icon-jenkins
    tags: instant-app,jenkins,gogs,nexus,cicd
  name: cicd
message: "Use the following credentials for login:\nJenkins: use your OpenShift credentials\nNexus: admin/admin123\nSonarQube: admin/admin\nGogs Git Server: gogs/gogs"
parameters:

- displayName: DEV project name
  value: dev
  name: DEV_PROJECT
  required: true
- displayName: STAGE project name
  value: stage
  name: STAGE_PROJECT
  required: true
- displayName: Ephemeral
  description: Use no persistent storage for Gogs and Nexus
  value: "true"
  name: EPHEMERAL
  required: true
- description: Webhook secret
  from: '[a-zA-Z0-9]{8}'
  generate: expression
  name: WEBHOOK_SECRET
  required: true
- displayName: Integrate Quay.io
  description: Integrate image build and deployment with Quay.io 
  value: "false"
  name: ENABLE_QUAY
  required: true
- displayName: Quay.io Username
  description: Quay.io username to push the images to tasks-sample-app repository on your Quay.io account
  name: QUAY_USERNAME
- displayName: Quay.io Password
  description: Quay.io password to push the images to tasks-sample-app repository on your Quay.io account
  name: QUAY_PASSWORD
- displayName: Quay.io Image Repository
  description: Quay.io repository for pushing Tasks container images
  name: QUAY_REPOSITORY
  required: true
  value: tasks-app
  objects:
- apiVersion: v1
  kind: BuildConfig
  metadata:
    annotations:
      pipeline.alpha.openshift.io/uses: '[{"name": "jenkins", "namespace": "", "kind": "DeploymentConfig"}]'
    labels:
      app: cicd-pipeline
      name: cicd-pipeline
    name: tasks-pipeline
  spec:
    triggers:
     - type: GitHub
       github:
         secret: ${WEBHOOK_SECRET}
          - type: Generic
            generic:
              secret: ${WEBHOOK_SECRET}
              runPolicy: Serial
              source:
                type: None
              strategy:
                jenkinsPipelineStrategy:
            env:
       - name: DEV_PROJECT
         value: ${DEV_PROJECT}
       - name: STAGE_PROJECT
         value: ${STAGE_PROJECT}
       - name: ENABLE_QUAY
         value: ${ENABLE_QUAY}
         jenkinsfile: |-
           def mvnCmd = "mvn -s configuration/cicd-settings-nexus3.xml"
           pipeline {
             agent {
               label 'maven'
             }
             stages {
               stage('Build App') {
                 steps {
                   git branch: 'master', url: 'http://gogs:3000/gogs/openshift-tasks.git'
                   sh "${mvnCmd} install -DskipTests=true"
                 }
               }
               stage('Test') {
                 steps {
                   sh "${mvnCmd} test"
                   step([$class: 'JUnitResultArchiver', testResults: '**/target/surefire-reports/TEST-*.xml'])
                 }
               }
               stage('Code Analysis') {
                 steps {
                   script {
                     sh "${mvnCmd} sonar:sonar -Dsonar.host.url=http://sonarqube:9000 -DskipTests=true"
                   }
                 }
               }
               stage('Archive App') {
                 steps {
                   sh "${mvnCmd} deploy -DskipTests=true -P nexus"
                 }
               }
               stage('Build Image') {
                 steps {
                   sh "cp target/openshift-tasks.war target/ROOT.war"
                   script {
                     openshift.withCluster() {
                       openshift.withProject(env.DEV_PROJECT) {
                         openshift.selector("bc", "test").startBuild("--from-file=target/ROOT.war", "--wait=true")
                       }
                     }
                   }
                 }
               }
               stage('Deploy DEV') {
                 steps {
                   script {
                     openshift.withCluster() {
                       openshift.withProject(env.DEV_PROJECT) {
                         openshift.selector("dc", "test").rollout().latest();
                       }
                     }
                   }
                 }
               }
               stage('Promote to STAGE?') {
                 agent {
                   label 'maven'
                 }
                 steps {
                   timeout(time:15, unit:'MINUTES') {
                       input message: "Promote to STAGE?", ok: "Promote"
                   }
       
                   script {
                     openshift.withCluster() {
                       if (env.ENABLE_QUAY.toBoolean()) {
                         withCredentials([usernamePassword(credentialsId: "${openshift.project()}-quay-cicd-secret", usernameVariable: "QUAY_USER", passwordVariable: "QUAY_PWD")]) {
                           sh "skopeo copy docker://quay.io/${QUAY_USERNAME}/${QUAY_REPOSITORY}:latest docker://quay.io/${QUAY_USERNAME}/${QUAY_REPOSITORY}:stage --src-creds \"$QUAY_USER:$QUAY_PWD\" --dest-creds \"$QUAY_USER:$QUAY_PWD\" --src-tls-verify=false --dest-tls-verify=false"
                         }
                       } else {
                         openshift.tag("${env.DEV_PROJECT}/test:latest", "${env.STAGE_PROJECT}/test:stage")
                       }
                     }
                   }
                 }
               }
               stage('Deploy STAGE') {
                 steps {
                   script {
                     openshift.withCluster() {
                       openshift.withProject(env.STAGE_PROJECT) {
                         openshift.selector("dc", "test").rollout().latest();
                       }
                     }
                   }
                 }
               }
             }
           }
       type: JenkinsPipeline
```