# Unikernel

## KISS principles
Keep it simple, stupid (KISS) is a design principle which states that designs and/or systems should be as simple as possible. Wherever possible, complexity should be avoided in a system—as simplicity guarantees the greatest levels of user acceptance and interaction.
KISS, an acronym for "Keep it short and simple!", is a design principle noted by the U.S. Navy in 1960.

### Filesystem
The filesystem currently used by Nanos is TFS.

### Nanos supports the following storage drivers:
* virtio_blk
* virtio_scsi
* pvscsi
* nvme
* ata_pci
* storvsc
* xenblk


### Manifest
The nanos manifest is an extremely powerful tool as it comes with many different flags and is the synthesis of a filesystem merged with various settings. Most users will never craft their own manifests by hand, opting to use OPS to craft it automatically.

→ futex_trace
→ debugsyscalls
→ fault
→ exec_protect


###Data Structures
> Nanos uses a variety of internal data structures. This is only a partial list.
* Bitmap
* ID Heap
* FreeList
* Backed Heap
* Linear Backed Heap
* Paged Back Heap
* Priority Queue
* RangeMap
* Red/Black Tree
* Scatter/Gather List
* Table
* Tuple


## Getting Started
> curl https://ops.city/get.sh -sSfL | sh


Note: We've added the following to your /root/.bashrc
If this isn't the profile of your current shell then please add the following to your correct profile:

### OPS config
export OPS_DIR="$HOME/.ops"
export PATH="$HOME/.ops/bin:$PATH"
source "$HOME/.ops/scripts/bash_completion.sh"


> ls /root/.ops/

## following changed happen
```
.bashrc
.motd_shown
.ops
```


$FindDate=Get-Date -Year 2022 -Month 12 -Day 22
Get-ChildItem -Path C:\ -Include .ops -File -Recurse -ErrorAction SilentlyContinue | Where-Object { $_.LastWriteTime -ge $FindDate 
Get-Childitem -Path C:\Users\ACER\AppData\Local\Packages -Include .ops? -Recurse

```
Ops version: 0.1.34
Nanos version: 0.0
```
 
> ops image list

> ops instance list

> ops commands

* build
* completion
* deploy - Build an image from ELF and deploy an instance
* env - Cross-build environment commands
* help
* image - manage nanos images
* instance - manage nanos instances
* pkg - Package related commands
* profile -  Profile
* run - Run ELF binary as unikernel
* update -  check for updates
* version - manage nanos volumes
* volume - 

> ops help volume

> ops volume list

> ops completion bash

> ops pkg list

> ops profile
```
Ops version: 0.1.34
Nanos version: 0.0
Qemu version: 6.2.0
Arch: linux
Virtualized: true
```

> ops update

 100% |████████████████████████████████████████|  [4s:0s]
 100% |████████████████████████████████████████|  [0s:0s]
Update nanos to 0.1.43 version.

> ops profile
```
Ops version: 0.1.34
Nanos version: 0.1.43
Qemu version: 6.2.0
Arch: linux
Virtualized: true
```

> $Env:GOOS="linux"; go build

> $Env:GOOS="linux"; go build main.go

> ops run main

> ops image list

> ops image ls <image_name> <path> [flags]

> ops image ls main

> ops image tree main

> ops image cp <image_name> <src>... <dest> [flags]

> ops image cp main lib /mnt/e/PRACTICE/nanos -r

> ops image cp main . /mnt/e/PRACTICE/nanos -r

> ops run -p 8080 g

> ls -lh ~/.oph/images

> file ~/.ops/images/main

> ops image list -t gcp -g prod-1033 -z us-west2-a

> ops image tree g

## Learning Resource
* https://www.youtube.com/watch?v=OBlJ3QuEt9k
* https://www.youtube.com/watch?v=DYpaX4BnNlg
* https://www.youtube.com/watch?v=UrHOw35uBrE&t=10s
* https://raw.githubusercontent.com/nanovms/ops/master/install.sh
* https://www.youtube.com/playlist?list=PLZij6bgEHkTXRakAtponkmP2CmlTTKlxl
* https://www.youtube.com/playlist?list=PLZij6bgEHkTXRakAtponkmP2CmlTTKlxl
