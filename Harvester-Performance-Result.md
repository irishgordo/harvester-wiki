# Harvester Performance Result

##  v1.4.0 Performance Result

### Test Environment:
| Components | Configuration |
|---|---|
| Cluster Size | 3 nodes |
| CPU | AMD EPYC 7513 |
| Memory | 128 GB |
| Network | 40 Gb |
| Disk | Samsung 980PRO 2TB |
| Harvester | v1.4.0 |
| Longhorn | v1.7.2 |
| Kubevirt | v1.2.2 |

### Network Performance:
Pod2Pod network performance: **30Gb**

Tested with `iperf`, as below:

Server command: `iperf -s`

Client command: `iperf -c 10.52.1.26 -P 8`

### Performance - Raw Device
FIO key parameters for each test:

Seq Write: `IOEngine=libaio, BS=128k, iodepth=32, numjobs=1, direct=1, runtime=300s, ramp_time=10s`

Seq Read: `IOEngine=libaio, BS=128k, iodepth=32, numjobs=1, direct=1, runtime=300s, ramp_time=10s`

Rand Write: `IOEngine=libaio, BS=4k, iodepth=32, numjobs=4, direct=1, runtime=300s, ramp_time=10s`

Rand Read: `IOEngine=libaio, BS=4k, iodepth=32, numjobs=4, direct=1, runtime=300s, ramp_time=10s`
| | Seq Write | Seq Read | Random Write | Random Read |
|----------|--------|--------|------------|-----------|
| Raw Device | 2673 MiB/s | 3363 MiB/s | 299K IOPs | 810K IOPs |

### Performance - Linux VM
VM Configuration: 8 CPU, 16GB Memory

Tuning parameters:
| Components | Tuning Value |
| --- | --- |
| nomerge | 2 |
| CPU model | host-passthrough |
| blockMultiQueue | True (enable) |
| ioThreadsPolicy | Auto |
| dedicatedIOThread | true (for target disk volume) |

**NOTE:** _Please configure `nomerge` on the Host side instead of the VM side. The Longhorn volume will create the corresponding device on the host, the target for the `nomerge` configuration._

Disk Configurations:
| Components | Value |
| --- | --- |
| Interface | VirtIO/SCSI |
| Size | 100 GB |

FIO key parameters for each test:

Seq Write (1 Jobs): `IOEngine=libaio, BS=128k, iodepth=32, numjobs=1, direct=1, runtime=300s, ramp_time=10s`

Seq Write (4 Jobs): `IOEngine=libaio, BS=128k, iodepth=32, numjobs=4, direct=1, runtime=300s, ramp_time=10s`

Seq Read: `IOEngine=libaio, BS=128k, iodepth=32, numjobs=1, direct=1, runtime=300s, ramp_time=10s`

Rand Write: `IOEngine=libaio, BS=4k, iodepth=32, numjobs=4, direct=1, runtime=300s, ramp_time=10s`

Rand Read: `IOEngine=libaio, BS=4k, iodepth=32, numjobs=4, direct=1, runtime=300s, ramp_time=10s`

| | Seq Write (1 Job) | Seq Write (4 Jobs) | Seq Read | Random Write | Random Read |
|---|---|---|---|---|---|
| v1 volume, virtio | 347 MiBs | 345 MiBs | 1761 MiBs | 37.7k IOPs | 36.9k IOPs |
| v1 volume, scsi | 343 MiBs | 348 MiBs | 1963 MiBs | 37.6k IOPs | 36.1k IOPs |
| v2 volume, 1 cores, virtio | 249 MiBs | 246 MiBs | 1360 MiBs | 46.7k IOPs | 138k IOPs |
| v2 volume, 1 cores, scsi | 244 MiBs | 239 MiBs | 1135 MiBs | 35.1k IOPs | 64.7k IOPs |
| v2 volume, 2 cores, virtio | 261 MiBs | 262 MiBs | 1450 MiBs | 47.5k IOPs | 137k IOPs |
| v2 volume, 2 cores, scsi | 466 MiBs | 469 MiBs | 1999 MiBs | 64.5k IOPs | 106k IOPs |
| v2 volume, 4 cores, virtio | 261 MiBs | 276 MiBs | 1463 MiBs | 52k IOPs | 142k IOPs |
| v2 volume, 4 cores, scsi | 790 MiBs | 759 MiBs | 3365 MiBs | 101k IOPs | 148k IOPs | 

**NOTE:** _`cores` means the v2 data engine with multiple cores (REF: https://github.com/longhorn/longhorn/issues/8835)_

_This feature comes with Longhorn v1.8.0 and Harvester v1.5.0. Here, we use the custom build to use this feature._

### Performance - Windows VM
VM Configuration: 8 CPU, 16GB Memory

Tuning parameters:
| Components | Tuning Value |
| --- | --- |
| nomerge | 2 |
| CPU model | host-passthrough |
| blockMultiQueue | True (enable) |
| ioThreadsPolicy | Auto |
| dedicatedIOThread | true (for target disk volume) |

**NOTE:** _Please configure `nomerge` on the Host side instead of the VM side. The Longhorn volume will create the corresponding device on the host, the target for the `nomerge` configuration._

Disk Configurations:
| Components | Value |
| --- | --- |
| Interface | VirtIO/SCSI |
| Size | 100 GB |
| Filesystem | NTFS |

We use [Diskspd](https://github.com/microsoft/diskspd), which is developed by the Microsoft team to benchmark the performance of the Windows VM.

The following `diskspd` commands for benchmarking:

Seq Write: `diskspd.exe -c90G -b128k -si128k -w100 -F8 -O128 -W10 -d300 -Sh testfile.dat`

Seq Read: `diskspd.exe -c90G -b128k -si128k -F8 -O128 -W10 -d300 -Sh testfile.dat`

Rand Write: `diskspd.exe -c90G -b4k -w100 -F8 -r -O128 -W30 -d300 -Sh testfile.dat`

Rand Read: `diskspd.exe -c90G -b4k -F8 -r -O128 -W30 -d300 -Sh testfile.dat`

| | Seq Write | Seq Read | Random Write | Random Read |
|---|---|---|---|---|
| v2 volume, 1 cores, virtio | 241 MiBs | 1323 MiBs | 24.6k IOPs | 53.4k IOPs |
| v2 volume, 1 cores, scsi | 245 MiBs | 1053 MiBs | 32.6k IOPs | 64.5k IOPs |
| v2 volume, 2 cores, virtio | 263 MiBs  | 1423 MiBs | 28.4k IOPs | 60.4k IOPs |
| v2 volume, 2 cores, scsi | 482 MiBs | 2079 MiBs | 54k IOPs | 105.4k IOPs |
| v2 volume, 4 cores, virtio | 259 MiBs | 1428 MiBs | 34.5k IOPs | 65.3k IOPs |
| v2 volume, 4 cores, scsi | 933 MiBs | 3821 MiBs | 67.4k IOPs | 128.8k IOPs |

**NOTE:** _`cores` means the v2 data engine with multiple cores (REF: https://github.com/longhorn/longhorn/issues/8835)_

_This feature comes with Longhorn v1.8.0 and Harvester v1.5.0. Here, we use the custom build to use this feature._
 