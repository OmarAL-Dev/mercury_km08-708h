![OpenWrt logo](include/logo.png)

OpenWrt Project is a Linux operating system targeting embedded devices. Instead
of trying to create a single, static firmware, OpenWrt provides a fully
writable filesystem with package management. This frees you from the
application selection and configuration provided by the vendor and allows you
to customize the device through the use of packages to suit any application.
For developers, OpenWrt is the framework to build an application without having
to build a complete firmware around it; for users this means the ability for
full customization, to use the device in ways never envisioned.

Sunshine!

# OpenWrt for Mercury KM08-708H / KT 708

This repository is a customized fork of OpenWrt maintained to add support for the **Mercury KM08-708H (KT 708)** router. Binaries are automatically built using **GitHub Actions**.

---

### ⚠️ Critical Warning: Partition Layout Mismatch

> **CAUTION:** There appear to be multiple hardware revisions/variants of this router in the market with differing flash partition layouts. 

This build utilizes a modified DTS configuration for the NAND layout:

* **Bootloader:** `0x0` – `0x80000` (512 KiB)
* **Config:** `0x80000` – `0x80000` (512 KiB)
* **Factory:** `0x100000` – `0x40000` (256 KiB)

**Verify your router's current flash partition table before flashing.** Flashing this image on a device with a conflicting layout may brick your router.

```dts
&nand {
	status = "okay";

	partitions {
		compatible = "fixed-partitions";
		#address-cells = <1>;
		#size-cells = <1>;

		partition@0 {
			label = "Bootloader";
			reg = <0x0 0x80000>;
			read-only;
		};

		partition@80000 {
			label = "Config";
			reg = <0x80000 0x80000>;
			read-only;
		};

		factory: partition@100000 {
			label = "Factory";
			reg = <0x100000 0x40000>;
			read-only;

			nvmem-layout {
				compatible = "fixed-layout";
				#address-cells = <1>;
				#size-cells = <1>;

				macaddr_factory_4: macaddr@4 {
					compatible = "mac-base";
					reg = <0x4 0x6>;
					#nvmem-cell-cells = <1>;
				};

				eeprom_factory_40000: eeprom@0 {
					reg = <0x0 0x8C04>;
				};
			};
		};
	};
};

OpenWrt is licensed under GPL-2.0
