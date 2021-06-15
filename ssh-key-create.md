## Generating an SSH private key

Official Documentation: [Generating an SSH private key and adding it to the agent](https://docs.openshift.com/container-platform/4.5/installing/installing_vsphere/installing-vsphere.html#ssh-agent-using_installing-vsphere)

Generate your ssh key

```console
ssh-keygen -t rsa -b 4096 -N '' -f ~/.ssh/id_rsa
```