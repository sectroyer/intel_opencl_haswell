# intel_opencl_haswell
## How to install OpenCL on older Intel graphic cards (Haswell/Ivy Bridge)
Since I had some issues with enabling OpenCL support on Haswell and whole process wasn't correctly described anywhere I have decided to put this quick tutorial for future references.
### Prerequisites:
In order to even you have to have drivers installed and confirm that devices is visible by running:
```
lspci | grep -i vga
00:02.0 VGA compatible controller: Intel Corporation Xeon E3-1200 v3/4th Gen Core Processor Integrated Graphics Controller (rev 06)
```
If you don't get something simillar and drivers are correctly installed. Please ensure that integrated gpu is enabled in BIOS and if you are not connecting this grahpic card as your main display you have to connect to it any monitor or hdmi emulator (which can be bought for 2-3 euro). 

I will be describing the process for Gentoo Linux however most of it can be adopted to any other distro. 
### Installation:
If you check [Gentoo Intel Wiki](https://wiki.gentoo.org/wiki/Intel) you can see that OpenCL is available from Ivy Bridge. However checking [Gentoo OpenCL Wiki](https://wiki.gentoo.org/wiki/OpenCL#Intel_-_GPU) we will learn that in order to enable OpenCL we have to install package dev-libs/intel-neo however:
`Beginning with the Broadwell processor series, Intel Graphics Compute Runtime for OpenCL is provided by dev-libs/intel-neo`. This statement isn't completely correct. It should be read as "begining with Broadwell it is ENOUGH to install dev-libs/intel-neo" on Haswell we still need it. First of all please download `sys_analyzer_linux.py` from this repo. I made small fix that was required on my system and might also help others (script didn't handle well other OpenCL vendors...). Of course there is no need if original one works fine.
#### Step 1 (libav):
First you have to install libav with correct flags:

```
x11-libs/libva-2.7.1:0/2::gentoo  USE="X drm utils -opengl -vdpau -wayland" ABI_X86="32 (64) (-x32)" VIDEO_CARDS="i965 intel nvidia -nouveau" 
```

It is important to enable intel video cards and (for `sys_analyzer_linux.py` script) utils use flag.

#### Step 2 (kernel):
Unpack MediaServerStudioEssentials2017.tar.gz and from it you need SDK2017Production16.5.tar.gz. If you can't find MediaServerStudioEssentials2017.tar.gz you can usee SDK2017Production16.5.tar.xz from this repo which has all the necessary files. When you unpack this one you will find intel-opencl-16.5-installation.pdf. You can follow this procedure with one important distinction. Do NOT patch your kernel with `/opt/intel/opencl/kernel-4.4-xcode.patch`. This one is for Android, use `/opt/intel/opencl/kernel-4.4.patch` instead. Of course you can try porting those patches to newer kernel however easiest way is to simply switch to 4.4
#### Step 2 (intel-neo):

If you simply install intel-neo `sys_analyzer_linux.py` should output:

```
[ OK ] OpenCL check:platform:Intel(R) OpenCL GPU OK CPU OK
```
However clinfo might still not show Intel GPU. This can be easilly fixed by emerging intel-neo with vaapi use flag:
```
[ebuild   R    ] dev-libs/intel-neo-20.16.16582::gentoo  USE="vaapi -l0" 0 KiB
```
After that OpenCL should work on Intel GPU without any issues.

## References:
[Gentoo Intel Wiki](https://wiki.gentoo.org/wiki/Intel)

[Gentoo OpenCL Wiki](https://wiki.gentoo.org/wiki/OpenCL#Intel_-_GPU)

[Intel MediaSDK mini-walkthrough]https://blogs.gentoo.org/lu_zero/2016/10/31/intel-mediasdk-mini-walkthrough/

[install-intel-opencl-drivers-for-linux.sh](https://gist.github.com/cmey/a2b2223e7947b62bd94d405e290c9293)
