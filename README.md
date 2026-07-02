# basic_kustomize_example_nginx_acm_policy  

This repo contains a basic example of Kustomize usage.

It renders a base - which:

- Creates an nginx deployment
- Uses default replica count of 1 (if not specified)
- Doesn't specify a namespace

It renders an overlay for dev/prod which alters the base to:

- Declare a different replia count for dev (2) and prod (5)
- It sets a namespace to deploy to in dev (jerome-dev) and a namespace to deploy to in prod (jerome-prod)

It then takes this base + overlay and combines them in to a single hydrated-app.yaml (which now reflects our unique namespace and replica count per environment generated) (found in overlays/$ENV/hydrated-app.yaml):

- From this new base, we build an ACM policy
- So that it can be used in ACM to dictate managed cluster state

To do this we make use of a policy-generator (policies/$ENV/policy-generator.yaml):

- Which injects the ACM specific policy specifications (e.g. inform/enforce/environment labels this policy will apply to)
- It then spits out policy, which can be applied to ACM, which can then be used to differentiate our dev/prod clusters in terms of this nginx application deployment.

---

Caveats:

It does NOT create the namespace for the deployment (only tells the deployment which namespace to use). This has been explicitly left as a manual task, in order to show that policy can and will error - showing you exactly what issue has arisen.

Commands:

$ kustomize build overlays/dev > policies/dev/hydrated-app.yaml 
$ kustomize build policies/dev --enable-alpha-plugins 
 
$ kustomize build overlays/prod > policies/prod/hydrated-app.yaml 
$ kustomize build policies/prod --enable-alpha-plugins 
