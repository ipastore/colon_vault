---
title: "Bug: killed_bug"
date: 2025-03-01
tags:
  - BUG
---
## Summary
I ran long processing of mask filtering and matching. I 3occasions I got a "killed" in terminal. The last 2 times I think it has something to do with tinyRoma in Vs-Code (The first try was with Vs-Code insiders) so i thought it was because of the IDE. I copy and paste the log down for the last occasion but I dont have it for the before.

## Steps to Reproduce

DEBUG = True
levels = ['hard']
submaps_hard = [('118', '094'), ('118', '095')]
models = ['superpoint-lg', 'sift-lg','tiny-roma', 'sift-nn', 'gim-lg', 'roma']
resize = None
masking = True

## Observed Behavior
Killed without any TraceBack

## Expected Behavior
To run all experiments without any problem

## Environment / System Details
- **Operating System:** 
	- No LSB modules are available.
	- Distributor ID: Ubuntu
	- Description: Ubuntu 22.04.5 LTS
	- Release: 22.04
	- Codename: jammy
- **Python Version:** Python 3.10.16
- **Hardware / GPU:** 

Architecture:             x86_64
  CPU op-mode(s):         32-bit, 64-bit
  Address sizes:          39 bits physical, 48 bits virtual
  Byte Order:             Little Endian
CPU(s):                   8
  On-line CPU(s) list:    0-7
Vendor ID:                GenuineIntel
  Model name:             Intel(R) Core(TM) i7-9700K CPU @ 3.60GHz
    CPU family:           6
    Model:                158
    Thread(s) per core:   1
    Core(s) per socket:   8
    Socket(s):            1
    Stepping:             13
    CPU max MHz:          4900,0000
    CPU min MHz:          800,0000
    BogoMIPS:             7200.00
    Flags:                fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush dts acpi mmx fxsr sse sse2 ss ht tm pbe syscall 
                          nx pdpe1gb rdtscp lm constant_tsc art arch_perfmon pebs bts rep_good nopl xtopology nonstop_tsc cpuid aperfmperf pni pclmulqdq d
                          tes64 monitor ds_cpl smx est tm2 ssse3 sdbg fma cx16 xtpr pdcm pcid sse4_1 sse4_2 x2apic movbe popcnt tsc_deadline_timer aes xsa
                          ve avx f16c rdrand lahf_lm abm 3dnowprefetch cpuid_fault epb ssbd ibrs ibpb stibp ibrs_enhanced fsgsbase tsc_adjust bmi1 avx2 sm
                          ep bmi2 erms invpcid mpx rdseed adx smap clflushopt intel_pt xsaveopt xsavec xgetbv1 xsaves dtherm ida arat pln pts hwp hwp_noti
                          fy hwp_act_window hwp_epp md_clear flush_l1d arch_capabilities
Caches (sum of all):      
  L1d:                    256 KiB (8 instances)
  L1i:                    256 KiB (8 instances)
  L2:                     2 MiB (8 instances)
  L3:                     12 MiB (1 instance)
NUMA:                     
  NUMA node(s):           1
  NUMA node0 CPU(s):      0-7
Vulnerabilities:          
  Gather data sampling:   Mitigation; Microcode
  Itlb multihit:          KVM: Mitigation: VMX unsupported
  L1tf:                   Not affected
  Mds:                    Not affected
  Meltdown:               Not affected
  Mmio stale data:        Mitigation; Clear CPU buffers; SMT disabled
  Reg file data sampling: Not affected
  Retbleed:               Mitigation; Enhanced IBRS
  Spec rstack overflow:   Not affected
  Spec store bypass:      Mitigation; Speculative Store Bypass disabled via prctl
  Spectre v1:             Mitigation; usercopy/swapgs barriers and __user pointer sanitization
  Spectre v2:             Mitigation; Enhanced / Automatic IBRS; IBPB conditional; RSB filling; PBRSB-eIBRS SW sequence; BHI SW loop, KVM SW loop
  Srbds:                  Mitigation; Microcode
  Tsx async abort:        Mitigation; TSX disabled

  *-display                 
       description: VGA compatible controller
       product: TU102 [GeForce RTX 2080 Ti Rev. A]
       vendor: NVIDIA Corporation
       physical id: 0
       bus info: pci@0000:01:00.0
       logical name: /dev/fb0
       version: a1
       width: 64 bits
       clock: 33MHz
       capabilities: pm msi pciexpress vga_controller bus_master cap_list rom fb
       configuration: depth=32 driver=nvidia latency=0 resolution=1920,1080
       resources: irq:148 memory:a3000000-a3ffffff memory:90000000-9fffffff memory:a0000000-a1ffffff ioport:3000(size=128) memory:c0000-dffff
       
- **Dependencies:** 
absl-py==2.1.0
aiohappyeyeballs==2.4.6
aiohttp==3.11.12
aiosignal==1.3.2
antlr4-python3-runtime==4.9.3
appnope @ file:///Users/ktietz/ci_310/appnope_1643965056645/work
asttokens @ file:///opt/conda/conda-bld/asttokens_1646925590279/work
astunparse==1.6.3
async-timeout==5.0.1
attrs==25.1.0
beautifulsoup4==4.13.3
blosc2==2.7.1
Bottleneck @ file:///private/var/folders/nz/j6p8yfhx1mv_0grj5xl4650h0000gp/T/abs_55txi4fy1u/croot/bottleneck_1731058642212/work
certifi==2025.1.31
charset-normalizer==3.4.1
comm @ file:///private/var/folders/k1/30mswbxs7r1g6zwn8y4fyt500000gp/T/abs_3doui0bmzb/croot/comm_1709322861485/work
contourpy==1.3.1
cycler==0.12.1
debugpy @ file:///private/var/folders/nz/j6p8yfhx1mv_0grj5xl4650h0000gp/T/abs_6a37he2v_t/croot/debugpy_1736267437603/work
decorator @ file:///opt/conda/conda-bld/decorator_1643638310831/work
e2cnn==0.2.3
einops==0.8.1
exceptiongroup @ file:///private/var/folders/nz/j6p8yfhx1mv_0grj5xl4650h0000gp/T/abs_b2258scr33/croot/exceptiongroup_1706031391815/work
executing @ file:///opt/conda/conda-bld/executing_1646925071911/work
filelock==3.17.0
flatbuffers==25.1.24
fonttools==4.56.0
freetype-py==2.5.1
frozenlist==1.5.0
fsspec==2025.2.0
future==1.0.0
gast==0.6.0
gdown==5.2.0
google-pasta==0.2.0
grpcio==1.70.0
h5py==3.12.1
hsluv==5.0.4
huggingface-hub==0.28.1
idna==3.10
image-matching-models @ file:///Users/ignaciopastorebenaim/Documents/MGRCV/COLON/colon_matching/utils/image-matching-models
imageio==2.37.0
ipykernel @ file:///private/var/folders/nz/j6p8yfhx1mv_0grj5xl4650h0000gp/T/abs_908a9n7o3f/croot/ipykernel_1737660707910/work
ipython @ file:///private/var/folders/nz/j6p8yfhx1mv_0grj5xl4650h0000gp/T/abs_2evnd74hdv/croot/ipython_1734548057210/work
jedi @ file:///private/var/folders/k1/30mswbxs7r1g6zwn8y4fyt500000gp/T/abs_38ctoinnl0/croot/jedi_1733987402850/work
Jinja2==3.1.5
joblib==1.4.2
jupyter_client @ file:///private/var/folders/k1/30mswbxs7r1g6zwn8y4fyt500000gp/T/abs_9b15geux5o/croot/jupyter_client_1737570974957/work
jupyter_core @ file:///private/var/folders/nz/j6p8yfhx1mv_0grj5xl4650h0000gp/T/abs_73nomeum4p/croot/jupyter_core_1718818302815/work
keras==3.8.0
kiwisolver==1.4.8
kornia==0.8.0
kornia-moons==0.2.9
kornia_rs==0.1.8
lazy_loader==0.4
libclang==18.1.1
lightning-utilities==0.12.0
loguru==0.7.3
Markdown==3.7
markdown-it-py==3.0.0
MarkupSafe==3.0.2
matplotlib==3.10.0
matplotlib-inline @ file:///private/var/folders/nz/j6p8yfhx1mv_0grj5xl4650h0000gp/T/abs_f6fdc0hldi/croots/recipe/matplotlib-inline_1662014472341/work
mdurl==0.1.2
ml-dtypes==0.4.1
mpmath==1.3.0
msgpack==1.1.0
multidict==6.1.0
namex==0.0.8
ndindex==1.9.2
nest-asyncio @ file:///private/var/folders/k1/30mswbxs7r1g6zwn8y4fyt500000gp/T/abs_310vb5e2a0/croot/nest-asyncio_1708532678212/work
networkx==3.4.2
numexpr @ file:///private/var/folders/nz/j6p8yfhx1mv_0grj5xl4650h0000gp/T/abs_b3kvvt6tc6/croot/numexpr_1730215947700/work
numpy @ file:///private/var/folders/k1/30mswbxs7r1g6zwn8y4fyt500000gp/T/abs_f2amvjkczw/croot/numpy_and_numpy_base_1725470317860/work/dist/numpy-2.0.1-cp310-cp310-macosx_11_0_arm64.whl#sha256=6ec8707b1639af7f6893526745ed5ceed4a86932c569fe7ad97c6b4ba65e9662
omegaconf==2.3.0
opencv-python==4.11.0.86
opt_einsum==3.4.0
optree==0.14.0
packaging @ file:///private/var/folders/nz/j6p8yfhx1mv_0grj5xl4650h0000gp/T/abs_a6_qk3qyg7/croot/packaging_1734472142254/work
pandas @ file:///private/var/folders/nz/j6p8yfhx1mv_0grj5xl4650h0000gp/T/abs_4aifrweohv/croot/pandas_1732735109535/work/dist/pandas-2.2.3-cp310-cp310-macosx_11_0_arm64.whl#sha256=6c5bbde94d3ac89a919939bdecfabca704888cdaf6251a6b101db7024fed27ab
parso @ file:///private/var/folders/nz/j6p8yfhx1mv_0grj5xl4650h0000gp/T/abs_8824a1w4md/croot/parso_1733963320105/work
pexpect @ file:///tmp/build/80754af9/pexpect_1605563209008/work
pillow==11.1.0
platformdirs @ file:///private/var/folders/k1/30mswbxs7r1g6zwn8y4fyt500000gp/T/abs_a8u4fy8k9o/croot/platformdirs_1692205661656/work
prompt-toolkit @ file:///private/var/folders/k1/30mswbxs7r1g6zwn8y4fyt500000gp/T/abs_c63v4kqjzr/croot/prompt-toolkit_1704404354115/work
propcache==0.2.1
protobuf==5.29.3
psutil @ file:///private/var/folders/k1/30mswbxs7r1g6zwn8y4fyt500000gp/T/abs_10oa1k8l11/croot/psutil_1736367646006/work
ptyprocess @ file:///tmp/build/80754af9/ptyprocess_1609355006118/work/dist/ptyprocess-0.7.0-py2.py3-none-any.whl
pure-eval @ file:///opt/conda/conda-bld/pure_eval_1646925070566/work
py-cpuinfo==9.0.0
py3-wget==1.0.5
pyDeprecate==0.3.1
pyglet==1.5.28
Pygments @ file:///private/var/folders/nz/j6p8yfhx1mv_0grj5xl4650h0000gp/T/abs_29bs9f_dh9/croot/pygments_1684279974747/work
pyparsing==3.2.1
PySocks==1.7.1
python-dateutil @ file:///private/var/folders/k1/30mswbxs7r1g6zwn8y4fyt500000gp/T/abs_66ud1l42_h/croot/python-dateutil_1716495741162/work
pytorch-lightning==1.5.2
pytz @ file:///private/var/folders/k1/30mswbxs7r1g6zwn8y4fyt500000gp/T/abs_a4b76c83ik/croot/pytz_1713974318928/work
PyYAML==6.0.2
pyzmq @ file:///private/var/folders/nz/j6p8yfhx1mv_0grj5xl4650h0000gp/T/abs_95lsut8ymz/croot/pyzmq_1734709560733/work
requests==2.32.3
rich==13.9.4
roma==1.5.1
safetensors==0.5.2
scikit-image==0.25.1
scikit-learn==1.6.1
scipy==1.15.1
six @ file:///tmp/build/80754af9/six_1644875935023/work
soupsieve==2.6
stack-data @ file:///opt/conda/conda-bld/stack_data_1646927590127/work
sympy==1.13.1
tables==3.10.1
tensorboard==2.18.0
tensorboard-data-server==0.7.2
tensorflow==2.18.0
tensorflow-io-gcs-filesystem==0.37.1
termcolor==2.5.0
threadpoolctl==3.5.0
tifffile==2025.1.10
timm==1.0.14
tk==0.1.0
torch==2.6.0
torch-geometric==2.6.1
torch_cluster==1.6.3
torchmetrics==1.6.1
torchvision==0.21.0
tornado @ file:///private/var/folders/nz/j6p8yfhx1mv_0grj5xl4650h0000gp/T/abs_0axef5a0m0/croot/tornado_1733960501260/work
tqdm==4.67.1
traitlets @ file:///private/var/folders/k1/30mswbxs7r1g6zwn8y4fyt500000gp/T/abs_500m2_1wyk/croot/traitlets_1718227071952/work
transforms3d==0.4.2
trimesh==4.6.1
typing_extensions @ file:///private/var/folders/k1/30mswbxs7r1g6zwn8y4fyt500000gp/T/abs_0b3jpv_f79/croot/typing_extensions_1734714864260/work
tzdata @ file:///croot/python-tzdata_1690578112552/work
urllib3==2.3.0
vispy==0.14.3
wcwidth @ file:///Users/ktietz/demo/mc3/conda-bld/wcwidth_1629357192024/work
Werkzeug==3.1.3
wrapt==1.17.2
yacs==0.1.8
yarl==1.18.3

## Logs & Error Messages
debug.log 

2025-03-01 11:48:46,517 - process_image_pairs.py:10 - Processing pair: f_0082456 and f_0064840 using tiny-roma
2025-03-01 11:48:47,418 - process_image_pairs.py:13 - img0 shape: torch.Size([3, 1012, 1350])
2025-03-01 11:48:47,421 - process_image_pairs.py:14 - img1 shape: torch.Size([3, 1012, 1350])
2025-03-01 11:48:47,592 - process_image_pairs.py:28 - mask0 shape: (1012, 1350)
2025-03-01 11:48:47,594 - process_image_pairs.py:29 - mask1 shape: (1012, 1350)
2025-03-01 11:48:47,612 - process_image_pairs.py:37 - Mask generation took 0.092 seconds
2025-03-01 11:48:47,694 - base_matcher.py:211 - BaseMatcher Forward pass with TinyRomaMatcher
2025-03-01 11:48:47,702 - base_matcher.py:212 - img0 type: <class 'torch.Tensor'>; shape: torch.Size([3, 1012, 1350]); dtype: torch.float32
2025-03-01 11:48:47,702 - base_matcher.py:213 - img1 type: <class 'torch.Tensor'>; shape: torch.Size([3, 1012, 1350]); dtype: torch.float32
2025-03-01 11:48:47,702 - base_matcher.py:214 - mask0 type: <class 'torch.Tensor'>; shape: torch.Size([1, 1012, 1350]); dtype: torch.uint8
2025-03-01 11:48:47,702 - base_matcher.py:215 - mask1 type: <class 'torch.Tensor'>; shape: torch.Size([1, 1012, 1350]); dtype: torch.uint8
2025-03-01 11:48:51,643 - tiny.py:333 - Filtered x0_f: 2016
2025-03-01 11:48:51,862 - tiny.py:334 - Filtered x1_f: 286
2025-03-01 11:48:52,174 - tiny.py:335 - Filtered x0_c: 501
2025-03-01 11:48:52,380 - tiny.py:336 - Filtered x1_c: 68
2025-03-01 11:48:54,744 - base_matcher.py:223 - BaseMatcher Forward pass took 7.041 seconds
2025-03-01 11:48:54,746 - base_matcher.py:224 - matched_kpts0 shape: torch.Size([2048, 2])
2025-03-01 11:48:54,746 - base_matcher.py:225 - matched_kpts1 shape: torch.Size([2048, 2])
2025-03-01 11:48:54,801 - process_image_pairs.py:43 - Matching took 7.187 seconds
2025-03-01 11:49:01,193 - process_image_pairs.py:55 - Plotting took 6.387 seconds
2025-03-01 11:49:01,199 - process_image_pairs.py:56 - Saved plot to output/hard/118_095/tiny-roma/f_0082456_f_0064840_tiny-roma.png
2025-03-01 11:49:01,210 - process_image_pairs.py:10 - Processing pair: f_0081990 and f_0064994 using tiny-roma
2025-03-01 11:49:02,366 - process_image_pairs.py:13 - img0 shape: torch.Size([3, 1012, 1350])
2025-03-01 11:49:02,384 - process_image_pairs.py:14 - img1 shape: torch.Size([3, 1012, 1350])
2025-03-01 11:49:03,092 - process_image_pairs.py:28 - mask0 shape: (1012, 1350)
2025-03-01 11:49:03,111 - process_image_pairs.py:29 - mask1 shape: (1012, 1350)
2025-03-01 11:49:03,214 - process_image_pairs.py:37 - Mask generation took 0.640 seconds
2025-03-01 11:49:03,758 - base_matcher.py:211 - BaseMatcher Forward pass with TinyRomaMatcher
2025-03-01 11:49:03,914 - base_matcher.py:212 - img0 type: <class 'torch.Tensor'>; shape: torch.Size([3, 1012, 1350]); dtype: torch.float32
2025-03-01 11:49:04,099 - base_matcher.py:213 - img1 type: <class 'torch.Tensor'>; shape: torch.Size([3, 1012, 1350]); dtype: torch.float32
2025-03-01 11:49:04,292 - base_matcher.py:214 - mask0 type: <class 'torch.Tensor'>; shape: torch.Size([1, 1012, 1350]); dtype: torch.uint8
2025-03-01 11:49:04,409 - base_matcher.py:215 - mask1 type: <class 'torch.Tensor'>; shape: torch.Size([1, 1012, 1350]); dtype: torch.uint8
2025-03-01 11:49:07,561 - tiny.py:333 - Filtered x0_f: 6998
2025-03-01 11:49:07,838 - tiny.py:334 - Filtered x1_f: 2441
2025-03-01 11:49:08,104 - tiny.py:335 - Filtered x0_c: 1749
2025-03-01 11:49:08,156 - tiny.py:336 - Filtered x1_c: 616
2025-03-01 11:49:11,767 - base_matcher.py:223 - BaseMatcher Forward pass took 7.254 seconds
2025-03-01 11:49:11,775 - base_matcher.py:224 - matched_kpts0 shape: torch.Size([2048, 2])
2025-03-01 11:49:11,775 - base_matcher.py:225 - matched_kpts1 shape: torch.Size([2048, 2])
2025-03-01 11:49:11,831 - process_image_pairs.py:43 - Matching took 8.579 seconds
2025-03-01 11:49:22,673 - process_image_pairs.py:55 - Plotting took 10.837 seconds
2025-03-01 11:49:22,677 - process_image_pairs.py:56 - Saved plot to output/hard/118_095/tiny-roma/f_0081990_f_0064994_tiny-roma.png
2025-03-01 11:49:22,683 - process_image_pairs.py:10 - Processing pair: f_0081990 and f_0064986 using tiny-roma
2025-03-01 11:49:26,919 - process_image_pairs.py:13 - img0 shape: torch.Size([3, 1012, 1350])
2025-03-01 11:49:27,292 - process_image_pairs.py:14 - img1 shape: torch.Size([3, 1012, 1350])
2025-03-01 11:49:30,210 - process_image_pairs.py:28 - mask0 shape: (1012, 1350)
2025-03-01 11:49:30,411 - process_image_pairs.py:29 - mask1 shape: (1012, 1350)
2025-03-01 11:49:31,021 - process_image_pairs.py:37 - Mask generation took 2.478 seconds
2025-03-01 11:49:33,960 - base_matcher.py:211 - BaseMatcher Forward pass with TinyRomaMatcher
2025-03-01 11:49:34,435 - base_matcher.py:212 - img0 type: <class 'torch.Tensor'>; shape: torch.Size([3, 1012, 1350]); dtype: torch.float32
2025-03-01 11:49:35,293 - base_matcher.py:213 - img1 type: <class 'torch.Tensor'>; shape: torch.Size([3, 1012, 1350]); dtype: torch.float32
2025-03-01 11:49:36,412 - base_matcher.py:214 - mask0 type: <class 'torch.Tensor'>; shape: torch.Size([1, 1012, 1350]); dtype: torch.uint8
2025-03-01 11:49:37,756 - base_matcher.py:215 - mask1 type: <class 'torch.Tensor'>; shape: torch.Size([1, 1012, 1350]); dtype: torch.uint8
## Analysis / Hypothesis
“Killed” (without a traceback) almost always means the operating system forcibly terminated the process, typically because it ran out of memory. On Linux, this is usually the OOM (Out-Of-Memory) killer. Essentially, your script used too much RAM (whether system memory or GPU memory) and the OS stepped in and killed it. That is why you see just “Killed” instead of a Python exception – it did not crash inside Python, but was forcibly shut down at the OS level.

## Workarounds or Fixes
Run it again with less submaps

## Next Steps
- [ ] Test with that same image
- [ ] Run it again with less submaps
- [ ] Run it exactly as this bug.
