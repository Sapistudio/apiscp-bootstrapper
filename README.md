Bootstrap provides an automated installation process to setup apnscp on a compatible CentOS/RHEL machine.

# Usage
After registering on [my.apnscp.com](https://my.apnscp.com), use the token to automatically download the key.
```shell
./bootstrap.sh <api token>
```

Alternatively, if you have the x509 key ("license.pem") available,
```shell
./bootstrap.sh -k <key file>
```

Or run from the command-line with a license,
```shell
curl https://raw.githubusercontent.com/apisnetworks/apnscp-bootstrapper/master/bootstrap.sh | bash -s - -k <keyfile>
```

Or in trial mode:
```shell
curl https://raw.githubusercontent.com/apisnetworks/apnscp-bootstrapper/master/bootstrap.sh | bash
```

# Benchmarking providers

This utility can also be used to benchmark a provider since it runs unassisted from start to finish.

```shell
curl https://raw.githubusercontent.com/apisnetworks/apnscp-bootstrapper/master/bootstrap.sh | bash
```

Check back in ~2 hours then run the following command:

```bash
IFS=$'\n' ; DATES=($((tail -n 1 /root/apnscp-bootstrapper.log | grep failed=0 ; grep -m 1 'u=root' /root/apnscp-bootstrapper.log ) | awk '{print $1, $2}')) ; [[ ${#DATES[@]} -eq 2 ]] && python -c 'from datetime import datetime; import sys; format="%Y-%m-%d %H:%M:%S,%f";print datetime.strptime(sys.argv[1], format)-datetime.strptime(sys.argv[2], format)' "${DATES[0]}" "${DATES[1]}" || (echo -e "\n>>> Unable to verify Bootstrapper completed - is Ansible still running or did it fail? Last 10 lines follow" && tail -n 10 /root/apnscp-bootstrapper.log)
```

A duration will appear or the last 10 lines of /root/apnscp-bootstrapper.log if it failed.



## Provider stats

Bootstrapping should complete within 90 minutes on a single core VPS. Under 60 minutes is impressive. These are stats taken from [Bootstrapper](https://github.com/apisnetworks/apnscp-playbooks) initial runs as bundled with part of [apnscp](https://apisnetworks.com). Note that as with shared hosting, or any shared resource, performance is adversely affected by oversubscription and noisy neighbors. Newer hypervisors show better benchmark numbers whereas older hypervisors show lower performance figures.

* Linode 2048, 1x CPU
  * 1:17:23.97 (E5-2697, 4599 bogomips)
  * 1:21:49.02 (E5-2697, 4599 bogomips)
* DigitalOcean 2 GB, 1x CPU
  * 1:39:49.07 (Gold 6140, 4589 bogomips)
* DigitalOcean 2 GB, 1x High Throughput CPU
  * 1:07:14.14 (Platinum 8168, 5387 bogomips)
* DigitalOcean 2 GB, 2x CPU
  * 1:47:06.25 (Gold 6140, 4589x2 bogomips)
* Vultr 2GB, 1x CPU (NJ data center)
  * 0:51:51.36 (Virtual CPU 82d93d4018dd, 5187 bogomips)
    *yum.apnscp.com is presently on Vultr and whereas this affects a small segment of the test, performance was still impressive thus necessitating a retest in another location*
* Vultr 2GB, 1x CPU (ATL data center retest)
  * 1:26:46.265  (Virtual CPU 82d93d4018dd, 5187 bogomips)
