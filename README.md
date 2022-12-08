

Terraform Controller Reconciliation via Gitops

[Introduction](#br1)

[The](#br1)[ ](#br1)[benefits](#br1)[ ](#br1)[of](#br1)[ ](#br1)[using](#br1)[ ](#br1)[the](#br1)[ ](#br1)[Terraform](#br1)[ ](#br1)[Controller](#br1)[ ](#br1)[:](#br1)

[How](#br1)[ ](#br1)[does](#br1)[ ](#br1)[it](#br1)[ ](#br1)[work?](#br1)

[GitOpsing](#br1)[ ](#br1)[your](#br1)[ ](#br1)[Terraform](#br1)

[Prerequisites](#br1)

[Installation](#br1)[ ](#br1)[via](#br1)[ ](#br1)[Gitops](#br1)[ ](#br1)[approach](#br1)

[1.](#br1)[ ](#br1)[Install](#br1)[ ](#br1)[Flux](#br1)[ ](#br1)[CLI](#br1)[ ](#br1)[and](#br1)[ ](#br1)[Kind](#br1)

[2.](#br2)[ ](#br2)[Make](#br2)[ ](#br2)[Personal](#br2)[ ](#br2)[Access](#br2)[ ](#br2)[Token](#br2)[ ](#br2)[for](#br2)[ ](#br2)[creating](#br2)[ ](#br2)[repositories](#br2)

[3.](#br2)[ ](#br2)[Export](#br2)[ ](#br2)[env](#br2)[ ](#br2)[vars](#br2)[ ](#br2)[locally](#br2)

[4.](#br2)[ ](#br2)[Create](#br2)[ ](#br2)[local](#br2)[ ](#br2)[demo](#br2)[ ](#br2)[cluster](#br2)[ ](#br2)[or](#br2)[ ](#br2)[connect](#br2)[ ](#br2)[to](#br2)[ ](#br2)[an](#br2)[ ](#br2)[existing](#br2)[ ](#br2)[cluster](#br2)

[5.](#br2)[ ](#br2)[Simple](#br2)[ ](#br2)[bootstrap:](#br2)

[6.](#br3)[ ](#br3)[Clone](#br3)[ ](#br3)[the](#br3)[ ](#br3)[newly](#br3)[ ](#br3)[created](#br3)[ ](#br3)[git](#br3)[ ](#br3)[repo](#br3)[ ](#br3)[to](#br3)[ ](#br3)[your](#br3)[ ](#br3)[local](#br3)[ ](#br3)[workspace](#br3)

[7.](#br4)[ ](#br4)[Lets](#br4)[ ](#br4)[now](#br4)[ ](#br4)[install](#br4)[ ](#br4)[the](#br4)[ ](#br4)[terraform](#br4)[ ](#br4)[controller](#br4)[ ](#br4)[using](#br4)[ ](#br4)[flux](#br4)

[9.](#br5)[ ](#br5)[Lets](#br5)[ ](#br5)[go](#br5)[ ](#br5)[ahead](#br5)[ ](#br5)[and](#br5)[ ](#br5)[push](#br5)[ ](#br5)[this](#br5)[ ](#br5)[to](#br5)[ ](#br5)[Git](#br5)

[10.](#br5)[ ](#br5)[The](#br5)[ ](#br5)[magic](#br5)[ ](#br5)[happens](#br5)[ ](#br5)[now](#br5)

[11.](#br5)[ ](#br5)[Change](#br5)[ ](#br5)[a](#br5)[ ](#br5)[new](#br5)[ ](#br5)[Helm](#br5)[ ](#br5)[release](#br5)[ ](#br5)[value](#br5)[ ](#br5)[through](#br5)[ ](#br5)[Git](#br5)

[Tying](#br6)[ ](#br6)[in](#br6)[ ](#br6)[your](#br6)[ ](#br6)[Terraform](#br6)[ ](#br6)[resources](#br6)

[Define](#br6)[ ](#br6)[source](#br6)

[The](#br7)[ ](#br7)[GitOps](#br7)[ ](#br7)[Automation](#br7)[ ](#br7)[mode](#br7)

[Get](#br7)[ ](#br7)[outputs](#br7)[ ](#br7)[as](#br7)[ ](#br7)[secrets](#br7)

Introduction

The [Terraform](https://weaveworks.github.io/tf-controller/)[ ](https://weaveworks.github.io/tf-controller/)[Controller](https://weaveworks.github.io/tf-controller/)[ ](https://weaveworks.github.io/tf-controller/)is a [Flux](http://fluxcd.io/)[ ](http://fluxcd.io/)controller that can manage your Terraform resources. Although Flux runs on Kubernetes, whatever you are

using Terraform for, the Flux controller can manage it.

The benefits of using the Terraform Controller :

\1. Full Gitops Automation

\2. GitOps for existing Terraform resources.

\3. There is drift detection of Terraform resources and it can be used as a glue for Terraform resources and Kubernetes workloads.

\4. The ability to do manual approvals or auto-approve Terraform plans

\5. The Terraform outputs can be set as a Kubernetes secret

\6. It is also integrated with Terraform Cloud and Terraform

How does it work?

TF-controller uses **the Controller/Runner architecture**. The Controller acts as a client, and talks to each Runner's Pod via gRPC. Please make

sure

\1. **Each Runner's Pod in each Namespace** is allowed to open, and serve at **port 30000** (the gRPC port of a Runner), and the Controller

can connect to it.

\2. **The Controller** needs to download tar.gz BLOBs from the **Source controller** via **port 80**.

\3. **The Controller** needs to post the events to the **Notification controller** via **port 80**.

GitOpsing your Terraform

Prerequisites

**Flux >= 0.32**

**Kubernetes >= 1.20.6**.

Installation via Gitops approach

Now you need to install Terraform Controller. There are many ways to do it, check out the [installation](https://weaveworks.github.io/tf-controller/getting_started/#installation)[ ](https://weaveworks.github.io/tf-controller/getting_started/#installation)[docs](https://weaveworks.github.io/tf-controller/getting_started/#installation)[ ](https://weaveworks.github.io/tf-controller/getting_started/#installation)for more information.

One very easy way to do it is to add this HelmReleaseto your bootstrap repository.

**1. Install Flux CLI and Kind**





$ brew upgrade fluxcd/tap/flux kind

$ brew reinstall fluxcd/tap/flux kind

$ flux --version && kind --version

flux version v0.37.0

kind version 0.17.0

**2. Make Personal Access Token for creating repositories**

\1. [Generate](https://github.com/settings/tokens)[ ](https://github.com/settings/tokens)[new](https://github.com/settings/tokens)[ ](https://github.com/settings/tokens)[token](https://github.com/settings/tokens)[ ](https://github.com/settings/tokens)[in](https://github.com/settings/tokens)[ ](https://github.com/settings/tokens)[dev](https://github.com/settings/tokens)[ ](https://github.com/settings/tokens)[settings](https://github.com/settings/tokens)

\2. Check all permissions under repo & save

\3. Copy PAT to buffer

**3. Export env vars locally**

export GITHUB\_TOKEN=[paste PAT]

echo -n $GITHUB\_TOKEN | wc -c

40

**4. Create local demo cluster or connect to an existing cluster**

kind create cluster

gcloud container clusters get-credentials playground-snigdha --region

europe-west1 --project playground-snigdha-s9tqz

**5. Simple bootstrap:**





flux bootstrap github --owner=snigdhasambitak --repository=flux-tf-

controller --private=false --personal=true --path=gke-cluster/playground

connecting to github.com

cloning branch "main" from Git repository "https://github.com

/snigdhasambitak/flux-tf-controller.git"

cloned repository

generating component manifests

generated component manifests

committed sync manifests to "main"

("d4a6589ea59549cd87cc35d8199ab81e475c717a")

pushing component manifests to "https://github.com/snigdhasambitak

/flux-tf-controller.git"

installing components in "flux-system" namespace

installed components

reconciled components

determining if source secret "flux-system/flux-system" exists

generating source secret

public key: ecdsa-sha2-nistp384

AAAAE2VjZHNhLXNoYTItbmlzdHAzODQAAAAIbmlzdHAzODQAAABhBIR

/6QneJ7d7RBYnhZnCEeAj80IwNGGikpf+wVKK3ekBFg3ubB9i681AvkKGU8Yk735c9Pvy112

GXCiG41ShXjgFhxAPeTWi+pt4ErNyu/Di7LldVO5twAELd2fV1tZrog==

configured deploy key "flux-system-main-flux-system-./gke-cluster

/playground" for "https://github.com/snigdhasambitak/flux-tf-controller"

applying source secret "flux-system/flux-system"

reconciled source secret

generating sync manifests

generated sync manifests

committed sync manifests to "main"

("750987ccd399ff934e84c60f4d03235e0e980ae2")

pushing sync manifests to "https://github.com/snigdhasambitak/flux-tf-

controller.git"

applying sync manifests

reconciled sync configuration

waiting for Kustomization "flux-system/flux-system" to be reconciled

Kustomization reconciled successfully

confirming components are healthy

helm-controller: deployment ready

kustomize-controller: deployment ready

notification-controller: deployment ready

source-controller: deployment ready

all components are healthy

**6. Clone the newly created git repo to your local workspace**





gcl git@github.com:snigdhasambitak/flux-tf-controller.git

cd flux-for-helm-users

tree

.

LICENSE

README.md

gke-cluster

playground

flux-system

gotk-components.yaml

gotk-sync.yaml

kustomization.yaml

Lets also create a folder named tf-controller to store our helm releases, which will be used in the next step

mkdir ./gke-cluster/playground/tf-controller

**7. Lets now install the terraform controller using flux**

We'll tell Flux about the Helm repo info with a HelmRepositoryCR representing a Flux source. Instead of helm add repoyou can use flux

create source helmto export the CRD to a local file:

flux create source helm tf-controller \

--url=https://weaveworks.github.io/tf-controller \

--namespace=flux-system \

--export > gke-cluster/playground/tf-controller/tf-controller.yaml

cat gke-cluster/playground/tf-controller/tf-controller.yaml

\---

apiVersion: source.toolkit.fluxcd.io/v1beta2

kind: HelmRepository

metadata:

name: tf-controller

namespace: flux-system

spec:

interval: 1m0s

url: https://weaveworks.github.io/tf-controller

Next we'll create a HelmReleaseCustom Resource locally





flux create helmrelease tf-controller \

--release-name=tf-controller \

--source=HelmRepository/tf-controller \

--chart=tf-controller \

--chart-version=">=0.2.5" \

--namespace=flux-system \

--export > ./gke-cluster/playground/tf-controller/tf-controller-

helmrelease.yaml

**9. Lets go ahead and push this to Git**

git add --all

git commit -m 'Configure tf-controller Helm Repo source and Helm

Release' && git push

**10. The magic happens now**

We can verify that Flux is now managing this Helm release.

*If you want to immediately trigger reconciliation on a local demo cluster you can manually call flux reconcile. We shouldn't*

*need to trigger that manually in this demo because we set the interval to 10s.*

*In real word clusters there are important use cases for setting up webhook receivers to automate this immediacy, and there are*

*equally important use cases for letting your defined sync interval run its course.*

flux reconcile helmrelease tf-controller

Flux will add labels

kubectl get deploy tf-controller -o yaml | grep flux

helm.toolkit.fluxcd.io/name: tf-controller

helm.toolkit.fluxcd.io/namespace: flux-system

**11. Change a new Helm release value through Git**

You believe me that we are now doing GitOps, but let's prove it.

Change a value in your HelmReleaseCR:

Lets add values and make the replica count to 2

values:

replicaCount: 2





git commit -am 'Configure values to increment the helm release' && git

push

We can see our Helm release incremented the revision:

helm ls

NAME

UPDATED

CHART

tf-controller

35:50.017980287 +0000 UTC

0.9.3 v0.13.1

NAMESPACE

REVISION

STATUS

APP VERSION

flux-system

2

2022-12-07 22:

tf-controller-

deployed

And that the new release revision applied our change:

helm diff revision tf-controller 1 2

Tying in your Terraform resources

And here is where all the beauty of Terraform Controller comes in - it does all the hard work for you. All you will need to do to is

\1. Define the source of your Terraform resources

\2. Enable GitOps Automation





**Define source**

So let’s go ahead, here we define a Source controller’s source (you can pick any of GitRepository, Bucket, OCIRepository). A GitRepos

itoryentry could look like this:

apiVersion: source.toolkit.fluxcd.io/v1beta1

kind: GitRepository

metadata:

name: gcs-bucket

namespace: flux-system

spec:

interval: 30s

url: https://github.com/snigdhasambitak/terraform-hello

ref:

branch: main

**The GitOps Automation mode**

The GitOps automation mode could be enabled by setting .spec.approvePlan=auto. In this mode, Terraform resources will be planned, and

automatically applied for you. Here is a simple example you can just copy and paste.

apiVersion: infra.contrib.fluxcd.io/v1alpha1

kind: Terraform

metadata:

name: gcs-bucket

namespace: flux-system

spec:

approvePlan: "auto"

interval: 1m

path: ./

sourceRef:

kind: GitRepository

name: gcs-bucket

namespace: flux-system

writeOutputsToSecret:

name: gcs-bucket-output

Once you commit this to Git, you should see Terraform Controller pick this up quickly.

If it takes time then we can do a manual reconciliation

If it takes time then we can do a manual reconciliation

flux reconcile ks flux-system --with-source

To confirm the reconsiliation process lets go a get

k get terraform

NAME

READY

STATUS

AGE

gcs-bucket True

No drift: main

/5c957c477f07b21e8e6d690d515c497911002c69 36m





**Get outputs as secrets**

We wanted the outputs as secrets which can be lates consumed by some other resources. This was defined in the above section

k get secrets gcs-bucket-output -o yaml

apiVersion: v1

data:

terraform\_hello: SGVsbG8sIGF1ZGllbmNlIQ==

kind: Secret

metadata:

creationTimestamp: "2022-12-07T23:41:04Z"

name: gcs-bucket-output

namespace: flux-system

ownerReferences:

\- apiVersion: infra.contrib.fluxcd.io/v1alpha1

controller: true

kind: Terraform

name: gcs-bucket

uid: a97308a2-a617-4670-a48f-61d358b68bb9

resourceVersion: "113337"

uid: 8944f0c1-5bb7-4a62-b98a-ba5ecd7e11cc

type: Opaque

Now lets do a base64 decode of the secret and verify if its propagating the right value

echo "SGVsbG8sIGF1ZGllbmNlIQ==" | base64 -D

Hello, audience!%

Awesome! Now finally we have automated this end to end using the gitops way

