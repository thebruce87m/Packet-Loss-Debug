# Packet-Loss-Debug

Watch for dropped packets:

```bash
$ watch -d ifconfig

enp0s31f6: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
...
        RX errors 0  dropped 222740  overruns 0  frame 0
...
```


Find out the driver for the ethernet:

```bash
$ lspci -v

00:1f.6 Ethernet controller: Intel Corporation Ethernet Connection (14) I219-V (rev 11)
	Subsystem: ASRock Incorporation Ethernet Connection (14) I219-V
	Flags: bus master, fast devsel, latency 0, IRQ 125
	Memory at a3100000 (32-bit, non-prefetchable) [size=128K]
	Capabilities: <access denied>
	Kernel driver in use: e1000e
	Kernel modules: e1000e
```

Find out more about the driver module, including configurable parameters under `parm:`

```bash
$ modinfo e1000e

modinfo e1000e
filename:       /lib/modules/5.15.0-46-generic/kernel/drivers/net/ethernet/intel/e1000e/e1000e.ko
license:        GPL v2
description:    Intel(R) PRO/1000 Network Driver
author:         Intel Corporation, <linux.nics@intel.com>

...

parm:           debug:Debug level (0=none,...,16=all) (int)
parm:           copybreak:Maximum size of packet that is copied to a new buffer on receive (uint)
parm:           TxIntDelay:Transmit Interrupt Delay (array of int)
parm:           TxAbsIntDelay:Transmit Absolute Interrupt Delay (array of int)
parm:           RxIntDelay:Receive Interrupt Delay (array of int)
parm:           RxAbsIntDelay:Receive Absolute Interrupt Delay (array of int)
parm:           InterruptThrottleRate:Interrupt Throttling Rate (array of int)
parm:           IntMode:Interrupt Mode (array of int)
parm:           SmartPowerDownEnable:Enable PHY smart power down (array of int)
parm:           KumeranLockLoss:Enable Kumeran lock loss workaround (array of int)
parm:           WriteProtectNVM:Write-protect NVM [WARNING: disabling this can lead to corrupted NVM] (array of int)
parm:           CrcStripping:Enable CRC Stripping, disable if your BMC needs the CRC (array of int)
```
Notes:

* `TxIntDelay` - If a packet has been received but no subsequent packets have been recieved in this time then fire the interrupt. Multiple packets received without a delay of this time will defer the interrupt.
* `TxAbsIntDelay` - Maximum amount of time before an interrupt will be fired regardless of `TxIntDelay`




Show ring buffer sizes on the NIC:

```bash
$ ethtool --show-ring enp0s31f6

Ring parameters for enp0s31f6:
Pre-set maximums:
RX:		4096
RX Mini:	0
RX Jumbo:	0
TX:		4096
Current hardware settings:
RX:		256
RX Mini:	0
RX Jumbo:	0
TX:		256
```
