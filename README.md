# MiniKube-on-Azure-VM

### A process to use an Azure VM with MiniKube 

This is a process to allow you to install and use MiniKube on an Azure VM with using the built in container tool.
To learn more about Kubenetes I wanted to get to the bare "metal" of the VM.
I used MiniKube as the based to try new things and learn more about running Kubernetes.

MiniKube was a good place to start my journey to scale Docker.

## First Steps and Needs for Azure

To run MiniKube on Azure you need a newer VM.
The VM needs to have a VM that supports Viruriazation.
On Azure the : **Dv3** and **Ev3** series are good VM to use for MiniKube.

I used **Ubuntu 16.04** as my OS of choice and is the bases of the exaples.

lastly you need to verify that you can run the virtualiztion by looking to see if the CPU can support it.

There are a few ways to chcek the CPU of the VM to see it can support the virtualization.

```
grep "flags" /proc/cpuinfo
flags		: fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush mmx fxsr sse sse2 ss ht syscall nx pdpe1gb rdtscp lm constant_tsc rep_good nopl xtopology cpuid pni pclmulqdq **vmx** ssse3 fma cx16 sse4_1 sse4_2 movbe popcnt aes xsave avx f16c rdrand hypervisor lahf_lm abm pti retpoline tpr_shadow vnmi ept vpid fsgsbase bmi1 avx2 smep bmi2 erms xsaveopt
``` 
You are looking for virtualization flags **vmx** (intel) or **svm** (AMD).

Another way is with **lscpu** command.
```
lscpu | grep -i virtural
```

```
@MiniKube:~$ lscpu | grep -i virtual
Virtualization:        VT-x
Virtualization type:   full
```
Once you have varified that you have a virtaulizable VM is time to install.

## Installing the utilities and applications. 

Minikube needs a few things to work.
- **Docker** Engine (For this setup we will use community edition)
- **Kubectl** (To give you direct control of the Kubernetes
- **Virtual** box (To allow to virtualize the VM)
- **MiniKube** Itself
- **Lynx** (optional web browser for basic web testing in termnial)

Many of these tools can be swapped out for conpatible tools if you wish.
Also I would suggest to log to **root** as many of the install are easier.
- All of these commands are using root.

### Update 

Update the VM 
```
apt-get update
```
If the VM has not been updated in a while, go get a coffee, it could take a while.

### Docker

We need to get the repo to Docker-ce, to do that we need the apt key for the repo.
```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
```
This gets the apt key for the repo adding it to root.

You can verify the docker repo.
```
apt-key fingerprint 0EBFCD88
Key fingerprint = 9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
```
Time to add the repository
```
add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```
When that is done, you can install docker-ce.
```
apt-get install docker-ce
apt-cache policy docker-ce
```
Ok now to do a quick test to make sure that Docker is world.
And yes, is the "hello world" process.
```
docker run hello-world

latest: Pulling from library/hello-world
ca4f61b1923c: Pull complete 
Digest: sha256:083de497cff944f969d8499ab94f07134c50bcf5e6b9559b27182d3fa80ce3f7
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.
...
```
You can find the version of Docker-ce.
```
docker version
Client:
 Version:	17.12.1-ce
```

### Kubectl

Off to pull more data to get Kubectl.
Google has the version we can use.
```
curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
```
Once installed we need to get executable access to the directory and move it to usr/local/bin/kubectl wher it can intigrate with the other components.
```
chmod +x ./kubectl
mv ./kubectl /usr/local/bin/kubectl
```

### Virtualbox

We need to virtualize this VM.  So we need a hypervisor to run the Kubernetes cluster.
This is where Virtualbox comes it, or more specificly it's driver.

Time to grab apt key
```
wget -q https://www.virtualbox.org/download/oracle_vbox_2016.asc -O- | apt-key add -
```
When this is complete we can install.  (note) You want the latest version.
At the time of this writing VBox 5.2 was the latest stable version.  Your's could be different.
```
apt-get install virtualbox-5.2
```
With it installed you need to get group access to it to use it properly.
So add your useranem and root to it's group.  
You can check VBox's status too.
```
usermod -aG vboxusers <username>
systemctl status vboxdrv
```

### MiniKube

Now to add the last key piece to this virtualization framework.
Let's get MiniKube.
* *Minikube is a alpha/beta project.  It is going through a lot of changes so double check it's documentation.* *
```
curl -Lo minikube https://storage.googleapis.com/minikube/releases/v0.25.2/minikube-linux-amd64

chmod +x minikube
mv minikube /usr/local/bin/
```
Done the pieces are in place. You can take over the world, or startup MiniKube.

### First MiniKube run

To run you need to make sure you use the virtualization tool as your driver.
In my case with virtualbox:
```
minikube start --vm-driver=virtualbox
```

## Using Minikube 


