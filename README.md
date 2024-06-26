# setup-soft-roce-action

[![Setup Soft-RoCE](https://github.com/SF-Zhou/setup-soft-roce-action/actions/workflows/main.yml/badge.svg)](https://github.com/SF-Zhou/setup-soft-roce-action/actions/workflows/main.yml)

Setup [Soft-RoCE](https://github.com/Soft-RoCE) in GitHub Actions environment.

### Usage

Copy the following lines into you own workflow file.

```yml
      - name: Setup Soft-RoCE
        run: |
          KERNEL_VERSION=$(uname -r | cut -d '-' -f 1)
          KERNEL_NAME="linux-${KERNEL_VERSION%'.0'}"
          DOWNLOAD_LINK="https://cdn.kernel.org/pub/linux/kernel/v${KERNEL_VERSION%%.*}.x/${KERNEL_NAME}.tar.xz"
          ETHERNET_CARD=$(ip link | awk -F ": " '$0 !~ "lo|vir|wl|^[^0-9]"{print $2;getline}' | head -1)
          echo "kernel version is ${KERNEL_VERSION}, download link is ${DOWNLOAD_LINK}, ethernet card is ${ETHERNET_CARD}"
          wget -q $DOWNLOAD_LINK -O /tmp/$KERNEL_NAME.tar.xz
          tar xf /tmp/$KERNEL_NAME.tar.xz --directory=/tmp
          RXE_PATH="/tmp/$KERNEL_NAME/drivers/infiniband/sw/rxe"
          sed 's/$(CONFIG_RDMA_RXE)/m/g' $RXE_PATH/Makefile > $RXE_PATH/Kbuild
          make -C /lib/modules/$(uname -r)/build M=$RXE_PATH modules -j
          sudo modprobe ib_core
          sudo modprobe rdma_ucm
          sudo insmod $RXE_PATH/rdma_rxe.ko
          sudo rdma link add rxe_0 type rxe netdev $ETHERNET_CARD
          rdma link
```
