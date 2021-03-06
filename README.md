## Oxfs

<p>
<img alt="GitHub" src="https://img.shields.io/github/license/RainMark/oxfs">
<img alt="PyPI" src="https://img.shields.io/pypi/v/oxfs">
<img alt="PyPI - Python Version" src="https://img.shields.io/pypi/pyversions/oxfs">
<img alt="PyPI - Wheel" src="https://img.shields.io/pypi/wheel/oxfs">
<img alt="GitHub last commit" src="https://img.shields.io/github/last-commit/RainMark/oxfs">
</p>

Oxfs is a user-space network file system similar to SSHFS, and the underlying data transfer is based on the SFTP protocol. Oxfs introduces an asynchronous refresh policy to solve the jamming problem caused by the mismatch between network speed and user operation file speed. When Oxfs writes a file, it first writes to the local cache file and submits an asynchronous update task to update the content to the remote host. Similarly, when reading a file, it is preferred to read from a local cache file. Oxfs's data cache eventually falls to disk, and even if it is remounted, the history cache can still be used.

### Get Started

#### Ubuntu

```bash
$ # Setup
$ sudo apt-get install python3.7
$ python3.7 -m pip install oxfs --user

$ # Run Oxfs
$ mkdir remote
$ sudo oxfs -s user@xxx.xxx.xxx.xxx -m remote -r /home/oxfs -c /tmp/oxfs
```

#### Darwin

```bash
$ # Setup
$ brew install python3
$ mkdir ~/.venv
$ python3 -m venv ~/.venv/oxfs
$ source ~/.venv/oxfs/bin/activate
$ pip install oxfs

$ # Run Oxfs
$ mkdir remote
$ sudo oxfs -s user@xxx.xxx.xxx.xxx -m remote -r /home/oxfs -c /tmp/oxfs
```

#### Parameters

```bash
$ oxfs --help
usage: oxfs [-h] [-s HOST] [-p PORT] [-m MOUNT_POINT] [-r REMOTE_PATH]
            [-c CACHE_PATH] [-l LOGGING] [-d] [-v]

optional arguments:
  -h, --help            show this help message and exit
  -s HOST, --host HOST  ssh host (for example: root@127.0.0.1)
  -p PORT, --port PORT  oxfs apiserver port, default: 10010)
  -m MOUNT_POINT, --mount_point MOUNT_POINT mount point
  -r REMOTE_PATH, --remote_path REMOTE_PATH remote path, default: /
  -c CACHE_PATH, --cache_path CACHE_PATH oxfs files cache path
  -l LOGGING, --logging LOGGING set log file, default: /tmp/oxfs.log
  -d, --daemon          run in background
  -v, --verbose         print verbose info
```

### Performance

#### Environment

- Ping Latency: 190 ms
- VPS: BandwagonHost (SPECIAL 10G KVM PROMO V3 - LOS ANGELES - CN2)
- VPS Operating System: Centos 7 x86_64 bbr
- Host: Intel® Core™ i5-4210U CPU @ 1.70GHz × 4 , SSD 125.5 GB
- Host Operating System: Ubuntu 18.04.2 LTS x86_64 reno

####  Method

```bash
$ iozone -R -b output.xls -p -c -r 1k -s 20m -l 1 -u 1
```

- Oxfs vs Sshfs

![](benchmark/oxfs-vs-sshfs.png)

- Sshfs performance (default parameters).
  - Sshfs enable the kernel cache by default, that's why re-read is deadly fast.

![](benchmark/sshfs.png)

- Oxfs performance (default parameters).
  - Oxfs just enable the fuse auto_cache flag.

![](benchmark/oxfs.png)

### Changelog

- release/0.2.0
  - [New] Add restful API to refresh the cache, default: http://127.0.0.1:10010 .

- release/0.1.2
  - [Removed] Remove auto_unmount fuse parameter, some osxfuse do not support it.
  - [Deprecated] Disable the daemon parameter, turn on it in the future.

- release/0.1.1
  - [Added] enable the auto_cache by default.

- release/0.1.0
  - [Fixed] Fix the multi-thread bugs for rename operation.
