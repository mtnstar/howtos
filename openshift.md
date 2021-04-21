# Pull Image from Openshift to local Docker

1. Login at openshift
2. docker login -u openshift -p $(oc whoami -t) registry.ocp.example.com
3. docker pull registry.ocp.example.com/ose-project-int/rails-sti-base
