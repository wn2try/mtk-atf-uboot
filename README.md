# Build MTK `bl2` & `u-boot` for <br>Ruijie X30E Pro and X60 New  

<br>

Source code: https://github.com/Yuzhii0718/bl-mt798x-dhcpd  

<br>

## X60 New - UBI Layout  
Install dependencies for Ubuntu 24.04  
```bash
sudo apt update
sudo apt install -y gcc-aarch64-linux-gnu build-essential flex bison \
libssl-dev device-tree-compiler qemu-user-static nodejs 
```
Clone the source code  
```bash
git clone --depth 1 https://github.com/Yuzhii0718/bl-mt798x-dhcpd.git uboot
cd uboot
```
Apply the new config  
```bash
cp ../uboot-mtk/configs-fit/mt7986_ruijie_rg-x60-new_defconfig \
uboot-mtk-20250711/configs-fit/
cp ../atf-mtk/configs-fit/mt7986_ruijie_rg-x60-new_defconfig \
atf-20260123/configs-fit/
```
Build `u-boot` & `bl2`  
```bash
make BOARD=ruijie_rg-x60-new VERSION=SP2 VARIANT=UBOOTMOD \
MULTI_LAYOUT=0 FIXED_MTDPARTS=0 UBIMNG=1 
```
The generated `bl2` and `fip` image will be located at `output/`  
- `bl2-mt7986_ruijie_rg-x60-new_SP2-fit_md5-*.img`
- `fip-mt7986_ruijie_rg-x60-new_SP2-Yuzhii-dhcpd-fit_md5-*.bin`
> bl2.img = MTK bootrom header + bl2.bin + sha256 hash  

<br>

Parameter explanation:  

| Parameter        | Value      | Default   | Description                                                                                                                                                                                                                                                                                               |
| :--------------- | :--------- | :-------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `VERSION`        | `SP2`      | `2025`    | `2025`: ATF-20250711, UBOOT-20250711 <br>`SP2` &nbsp;: ATF-20260123, UBOOT-20250711                                                                                                                                                                                                                       |
| `VARIANT`        | `UBOOTMOD` | `default` | `default`: MTK-NMBM enabled <br>`nonmbm`: MTK-NMBM disabled <br>`ubootmod`:  <br>&nbsp;&nbsp;&nbsp;&nbsp;- u-boot env in UBI volume `ubootenv`&`ubootenv2`<br>&nbsp;&nbsp;&nbsp;&nbsp;- reserved UBI volumes: `factory=2M` by default<br>&nbsp;&nbsp;&nbsp;&nbsp;- `fip` in UBI volume for `bl2`(`UBI=1`) |
| `MULTI_LAYOUT`   | `0`        | `1`       | Multiple mtd layout for selection in Web failsafe (`=1`)                                                                                                                                                                                                                                                  |
| `FIXED_MTDPARTS` | `0`        | `1`       | Make `mtdparts` editable (`=0`)<br>Default to `1` for `MULTI_LAYOUT=1`                                                                                                                                                                                                                                    |
| `UBIMNG`         | `1`        | `0`       | Enable UBI volume management in the Web failsafe (`=1`).                                                                                                                                                                                                                                                  |

> https://github.com/Yuzhii0718/bl-mt798x-dhcpd/blob/master/README.md

<br>

`bl2` parameters:  

| Config /  <br>Makefile option                                                                                       | Default                                              | Description                                                         |
| :------------------------------------------------------------------------------------------------------------------ | :--------------------------------------------------- | :------------------------------------------------------------------ |
| `_PLAT_MT7986=y` /  <br>`PLAT=mt7986`                                                                               |                                                      | Target platform: `mt7981`/`mt7986`                                  |
| `_BOOT_DEVICE_SPIM_NAND=y` /  <br>`BOOT_DEVICE=spim-nand`                                                           | `spim-nand` /  <br>`_BOOT_DEVICE_SPIM_NAND=y`        | SPI-NAND                                                            |
| `_NAND_TYPE_2K_64=y` /  <br>`NAND_TYPE=spim:2k+64`                                                                  | `spim:2k+64` /  <br>`_NAND_TYPE_2K_64=y`             | W25N01GV (128MB): `spim:2k+64`<br>W25N02KV (256MB): `spim:2k+128`   |
| `_NAND_UBI=y` /  <br>`UBI=1`                                                                                        | `NMBM=1` /  <br>`_NAND_NMBM=y`                       | Enable this option if FIP is located <br>in a UBI volume (`UBI=1`). |
| `_ENABLE_OVERRIDE_UBI_START_ADDR=y`<br>`OVERRIDE_UBI_START_ADDR=0x680000` /  <br>`OVERRIDE_UBI_START_ADDR=0x680000` | `0x200000`                                           | Override the default UBI partition <br>start address.               |
| `_DRAM_DDR3=y` /  <br>`DRAM_USE_DDR4=0`                                                                             | `0` /  <br>`_DRAM_DDR3=y`                            | DRAM is DDR4 (`DRAM_USE_DDR4=1`) <br>or DDR3 (`=0`)                 |
| `_DUAL_FIP=n` /  <br>`DUAL_FIP=0`                                                                                   | `1` if `UBI=1` /  <br>`_DUAL_FIP=y` if `_NAND_UBI=y` | Default to enable Dual-FIP (`DUAL_FIP=1`) <br>if `UBI=1`            |
| `_LOG_LEVEL_INFO=y` /  <br>`LOG_LEVEL=40`                                                                           | `30` /  <br>`_LOG_LEVEL_WARNING=y`                   | Warning (`=30`), Info (`=40`)                                       |

> [[Tutorial] Build, customize and use MediaTek open-source U-Boot and ATF](https://forum.openwrt.org/t/tutorial-build-customize-and-use-mediatek-open-source-u-boot-and-atf/134897)  
> [MediaTek ATF Configuration](https://github.com/mtk-openwrt/arm-trusted-firmware/blob/mtksoc/plat/mediatek/apsoc_common/Config.in)

<br>

## X30E Pro - Build NONMBM Variant  
Install dependencies for Ubuntu 24.04  
```bash
sudo apt update
sudo apt install -y gcc-aarch64-linux-gnu build-essential flex bison \
libssl-dev device-tree-compiler qemu-user-static nodejs 
```
Clone the source code  
```bash
git clone --depth 1 https://github.com/Yuzhii0718/bl-mt798x-dhcpd.git uboot
cd uboot
```
Apply the new config  
```bash
cp ../uboot-mtk/configs-nonmbm/mt7981_ruijie_rg-x30e-pro_defconfig \
uboot-mtk-20250711/configs-nonmbm/
```
Build `u-boot` & `bl2`  
```bash
make BOARD=ruijie_rg-x30e-pro VERSION=SP2 VARIANT=NONMBM \
MULTI_LAYOUT=0 FIXED_MTDPARTS=0 UBIMNG=1 
```
The generated `bl2` and `fip` image will be located at `output/`  
- `bl2-mt7981_ruijie_rg-x30e-pro_SP2-nonmbm_md5-*.img`
- `fip-mt7981_ruijie_rg-x30e-pro_SP2-Yuzhii-dhcpd-nonmbm_md5-*.bin`

<br><br>
