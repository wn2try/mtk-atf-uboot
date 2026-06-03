# Build MTK `bl2`&`u-boot` for Ruijie X30E Pro and <br>Ruijie X60 New
ATF source code: https://github.com/mtk-openwrt/arm-trusted-firmware  
U-Boot source code: https://github.com/Yuzhii0718/bl-mt798x-dhcpd  

<br>

## X60 New - UBI Layout  
### Build `bl2`  
Install dependencies for Ubuntu 24.04  
```bash
sudo apt update
sudo apt install -y gcc-aarch64-linux-gnu device-tree-compiler
```
Clone the source code and build `bl2`  
```bash
git clone -b mtksoc --depth 1 https://github.com/mtk-openwrt/arm-trusted-firmware.git atf

cd atf
make -f Makefile PLAT=mt7986 BOOT_DEVICE=spim-nand NAND_TYPE=spim:2k+64 UBI=1 \
OVERRIDE_UBI_START_ADDR=0x680000 DRAM_USE_DDR4=0 DUAL_FIP=0 LOG_LEVEL=40 bl2
```
The generated `bl2` image will be located at `build/mt7986/release/bl2.img`.  
> bl2.img = MTK bootrom header + bl2.bin + sha256 hash

<br>

Parameter explanation:  

| Parameter                 | Value        | Default      | Description                                                       |
| :------------------------ | :----------- | :----------- | :---------------------------------------------------------------- |
| `PLAT`                    | `mt7986`     |              | Target platform: `mt7981`/`mt7986`                                |
| `BOOT_DEVICE`             | `spim-nand`  |              | SPI-NAND                                                          |
| `NAND_TYPE`               | `spim:2k+64` | `spim:2k+64` | W25N01GV (128MB): `spim:2k+64`<br>W25N02KV (256MB): `spim:2k+128` |
| `UBI`                     | `1`          | `0`          | Enable this option if FIP is located <br>in a UBI volume (`=1`).  |
| `OVERRIDE_UBI_START_ADDR` | `0x680000`   | `0x200000`   | Override the default UBI partition <br>start address.             |
| `DRAM_USE_DDR4`           | `0`          | `0`          | DRAM is DDR4 (`=1`) or DDR3 (`=0`)                                |
| `DUAL_FIP`                | `0`          | `1`          | Enable Dual-FIP (`=1`)                                            |
| `LOG_LEVEL`               | `40`         | `30`         | Warning (`=30`), Info (`=40`)                                     |

> [[Tutorial] Build, customize and use MediaTek open-source U-Boot and ATF](https://forum.openwrt.org/t/tutorial-build-customize-and-use-mediatek-open-source-u-boot-and-atf/134897)  

<br>

### Build `u-boot`
Install dependencies for Ubuntu 24.04
```bash
sudo apt update
sudo apt install -y gcc-aarch64-linux-gnu build-essential flex bison \
libssl-dev device-tree-compiler qemu-user-static nodejs 
```
clone the source code
```bash
git clone --depth 1 https://github.com/Yuzhii0718/bl-mt798x-dhcpd.git fip
cd fip
```
Apply the new config
```bash
cp ../uboot-mtk/configs-fit/mt7986_ruijie_rg-x60-new_defconfig \
uboot-mtk-20250711/configs-fit/
```
Build `u-boot`
```bash
make BOARD=ruijie_rg-x60-new VERSION=SP2 VARIANT=UBOOTMOD \
MULTI_LAYOUT=0 FIXED_MTDPARTS=0 UBIMNG=1 
```
The generated `fip` image will be located at `output/`
- `fip-mt7986_ruijie_rg-x60-new_SP2-Yuzhii-dhcpd-fit_md5-*.bin`

<br>

Parameter explanation:

| Parameter        | Value      | Default   | Description                                                                                                                                                                                                                                     |
| :--------------- | :--------- | :-------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `VERSION`        | `SP2`      | `2025`    | `2025`: ATF-20250711, UBOOT-20250711 <br>`SP2` : ATF-20260123, UBOOT-20250711                                                                                                                                                                   |
| `VARIANT`        | `UBOOTMOD` | `default` | `default`: MTK-NMBM enabled <br>`nonmbm`: MTK-NMBM disabled <br>`ubootmod`:  <br>    - u-boot env in UBI volume `ubootenv`&`ubootenv2`<br>    - reserved UBI volume `factory`(`=2M` by default)<br>    - `fip` in UBI volume for `bl2`(`UBI=1`) |
| `MULTI_LAYOUT`   | `0`        | `1`       | Multiple mtd layout for selection in Web failsafe (`=1`)                                                                                                                                                                                        |
| `FIXED_MTDPARTS` | `0`        | `1`       | Make `mtdparts` editable (`=0`)<br>Default to `1` for `MULTI_LAYOUT=1`                                                                                                                                                                          |
| `UBIMNG`         | `1`        | `0`       | Enable UBI volume management in the Web failsafe (`=1`).                                                                                                                                                                                        |

> https://github.com/Yuzhii0718/bl-mt798x-dhcpd/blob/master/README.md

<br>

## X30E Pro - Build NONMBM Variant
Install dependencies for Ubuntu 24.04
```bash
sudo apt update
sudo apt install -y gcc-aarch64-linux-gnu build-essential flex bison \
libssl-dev device-tree-compiler qemu-user-static nodejs npm
```
clone the source code
```bash
git clone --depth 1 https://github.com/Yuzhii0718/bl-mt798x-dhcpd.git uboot
cd uboot
```
Apply the new config
```bash
cp ../uboot-mtk/configs-nonmbm/mt7981_ruijie_rg-x30e-pro_defconfig \
uboot-mtk-20250711/configs-nonmbm/
```
Build `u-boot`
```bash
make BOARD=ruijie_rg-x30e-pro VERSION=SP2 VARIANT=NONMBM \
MULTI_LAYOUT=0 FIXED_MTDPARTS=0 UBIMNG=1 
```
The generated `bl2` and `fip` image will be located at `output/`
- `bl2-mt7981_ruijie_rg-x30e-pro_SP2-nonmbm_md5-*.img`
- `fip-mt7981_ruijie_rg-x30e-pro_SP2-Yuzhii-dhcpd-nonmbm_md5-*.bin`

<br><br>
