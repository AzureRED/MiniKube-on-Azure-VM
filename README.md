# MiniKube-on-Azure-VM

### A process to use an Azure VM with MiniKube 

This is a process to allow you to install and use MiniKube on an Azure VM with using the built in container tool.
To learn more about Kubenetes I wanted to get to the bare "metal" of the VM.
I used MiniKube as the based to try new things and learn more about running Kubenetes.

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
