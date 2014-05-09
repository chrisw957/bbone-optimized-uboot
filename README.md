bbone-optimized-uboot
=====================

We use a custom beaglebone for a project, and we wanted to get it booted as fast as possible. One way of speeding up the boot was to optimize u-boot. By turning off everything we weren't using, we got the time to "start linux" from 5.498 seconds to 2.248 seconds-- that is a savings of 3.250 seconds.

Here is the original boot sequence, with timestamps provided by the tstamp utility:

<pre>
0.000 0.000:
0.004 0.004: U-Boot SPL 2013.10-dirty (Nov 14 2013 - 12:59:11)
0.267 0.263: reading args
0.272 0.005: spl: error reading image args, err - -1
0.274 0.002: reading u-boot.img
0.278 0.004: reading u-boot.img
0.320 0.042:
0.320 0.000:
0.324 0.004: U-Boot 2013.04-dirty (Apr 22 2014 - 22:11:16)
0.324 0.000:
0.377 0.053: I2C: ready
0.379 0.002: DRAM: 512 MiB
0.863 0.484: WARNING: Caches not enabled
1.565 0.701: NAND: No NAND device found!!!
1.566 0.001: 0 MiB
1.568 0.003: MMC: OMAP SD/MMC: 0, OMAP SD/MMC: 1
1.574 0.005: *** Warning - readenv() failed, using default environment
1.574 0.000:
2.128 0.555: musb-hdrc: ConfigData=0xde (UTMI-8, dyn FIFOs, HB-ISO Rx, HB-ISO Tx, SoftConn)
2.131 0.003: musb-hdrc: MHDRC RTL version 2.0
2.133 0.002: musb-hdrc: setup fifo_mode 4
2.138 0.004: musb-hdrc: 28/31 max ep, 16384/16384 memory
2.144 0.006: USB Peripheral mode controller at 47401000 using PIO, IRQ 0
2.150 0.007: musb-hdrc: ConfigData=0xde (UTMI-8, dyn FIFOs, HB-ISO Rx, HB-ISO Tx, SoftConn)
2.153 0.003: musb-hdrc: MHDRC RTL version 2.0
2.157 0.004: musb-hdrc: setup fifo_mode 4
2.160 0.003: musb-hdrc: 28/31 max ep, 16384/16384 memory
2.165 0.005: USB Host mode controller at 47401800 using PIO, IRQ 0
2.170 0.005: Net: not set. Validating first E-fuse MAC
2.193 0.023: cpsw, usb_ether
2.196 0.003: Hit any key to stop autoboot: 0
3.213 1.017: mmc_send_cmd : timeout: No status update
3.264 0.051: reading uEnv.txt
3.269 0.005: 115 bytes read in 4 ms (27.3 KiB/s)
3.273 0.004: Importing environment from mmc ...
3.935 0.662: 4403952 bytes read in 651 ms (6.5 MiB/s)
3.982 0.047: 24864 bytes read in 38 ms (638.7 KiB/s)
3.985 0.003: Booting from mmc ...
3.994 0.009: ## Booting kernel from Legacy Image at 80007fc0 ...
3.996 0.002: Image Name: Linux-3.8.13
4.001 0.005: Image Type: ARM Linux Kernel Image (uncompressed)
4.005 0.004: Data Size: 4403888 Bytes = 4.2 MiB
4.007 0.002: Load Address: 80008000
4.010 0.003: Entry Point: 80008000
5.078 1.069: Verifying Checksum ... OK
5.082 0.004: ## Flattened Device Tree blob at 80f80000
5.086 0.004: Booting using the fdt blob at 0x80f80000
5.088 0.002: XIP Kernel Image ... OK
5.097 0.009: OK
5.102 0.005: Using Device Tree in place at 80f80000, end 80f8911f
5.113 0.011:
5.115 0.002: Starting kernel ...
5.115 0.000:
5.255 0.140: Uncompressing Linux... done, booting the kernel.
5.498 0.244: [ 0.000000] Booting Linux on physical CPU 0x0
</pre>

And here is the optimized u-boot log:
<pre>
0.000 0.000: 
0.005 0.005: U-Boot SPL 2013.04-dirty (May 09 2014 - 17:41:58)
0.127 0.122: OMAP SD/MMC: 0
0.183 0.056: reading u-boot.img
0.186 0.003: reading u-boot.img
0.216 0.031: 
0.217 0.001: 
0.221 0.004: U-Boot 2013.04-dirty (May 09 2014 - 17:41:58)
0.221 0.000: 
0.274 0.053: I2C:   ready
0.276 0.002: DRAM:  512 MiB
0.300 0.024: WARNING: Caches not enabled
0.815 0.515: MMC:   OMAP SD/MMC: 0, OMAP SD/MMC: 1
0.818 0.003: Using default environment
0.818 0.000: 
0.961 0.143: Net:   Net Initialization Skipped
0.963 0.002: No ethernet found.
0.966 0.003: Hit any key to stop autoboot:  0 
1.831 0.865: 4403952 bytes read in 802 ms (5.2 MiB/s)
1.887 0.056: 24864 bytes read in 46 ms (527.3 KiB/s)
1.890 0.003: Booting from mmc ...
1.900 0.010: ## Booting kernel from Legacy Image at 80007fc0 ...
1.903 0.003:    Image Name:   Linux-3.8.13
1.908 0.005:    Image Type:   ARM Linux Kernel Image (uncompressed)
1.912 0.004:    Data Size:    4403888 Bytes = 4.2 MiB
1.914 0.002:    Load Address: 80008000
1.916 0.002:    Entry Point:  80008000
1.920 0.004: ## Flattened Device Tree blob at 80f80000
1.924 0.004:    Booting using the fdt blob at 0x80f80000
1.926 0.002:    XIP Kernel Image ... OK
1.942 0.016: OK
1.947 0.005:    Using Device Tree in place at 80f80000, end 80f8911f
1.961 0.014: 
1.963 0.002: Starting kernel ...
1.963 0.000: 
2.169 0.206: Uncompressing Linux... done, booting the kernel.
2.480 0.310: [    0.000000] Booting Linux on physical CPU 0x0
</pre>

We did this by being merciless with the u-boot config file.  Our system always boots from the eMMC, so we turned off everything else.  This includes hardcoding anything that may have been in uEnv.txt.  All of this can be done by editing the /include/configs/am335x_evm.h file.

Here is the am335x_evm.h file that I ended up with:
<a href="https://gist.github.com/chrisw957/4b1be5369608662bf4d0">/include/configs/am335x_evm.h</a>

Another fix is related to the mmc timeout.  This can be fixed by adding a udelay() in omap_hsmmc.c.
<pre>
Index: git/drivers/mmc/omap_hsmmc.c
===================================================================
--- git.orig/drivers/mmc/omap_hsmmc.c	2014-05-09 18:24:10.044401801 -0500
+++ git/drivers/mmc/omap_hsmmc.c	2014-05-09 18:25:00.655765532 -0500
@@ -378,6 +378,7 @@
 	}
 
 	writel(cmd->cmdarg, &mmc_base->arg);
+	udelay(50);
 	writel((cmd->cmdidx << 24) | flags, &mmc_base->cmd);
 
 	start = get_timer(0);
</pre>
