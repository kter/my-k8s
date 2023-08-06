# SetUp

## Ubuntuのダウンロード

1. 下記サイトからUbuntu Serverのisoをダウンロード
https://jp.ubuntu.com/download

## UbuntuをUSBメモリに書き込む

1. USBメモリのドライブのパスを確認
    ```bash
    % diskutil list
    /dev/disk0 (internal, physical):
       #:                       TYPE NAME                    SIZE       IDENTIFIER
       0:      GUID_partition_scheme                        *500.3 GB   disk0
       1:             Apple_APFS_ISC Container disk1         524.3 MB   disk0s1
       2:                 Apple_APFS Container disk3         494.4 GB   disk0s2
       3:        Apple_APFS_Recovery Container disk2         5.4 GB     disk0s3

    /dev/disk3 (synthesized):
       #:                       TYPE NAME                    SIZE       IDENTIFIER
       0:      APFS Container Scheme -                      +494.4 GB   disk3
                                     Physical Store disk0s2
       1:                APFS Volume Macintosh HD            9.1 GB     disk3s1
       2:              APFS Snapshot com.apple.os.update-... 9.1 GB     disk3s1s1
       3:                APFS Volume Preboot                 9.2 GB     disk3s2
       4:                APFS Volume Recovery                799.1 MB   disk3s3
       5:                APFS Volume Data                    398.9 GB   disk3s5
       6:                APFS Volume VM                      20.5 KB    disk3s6

    /dev/disk4 (external, physical):
       #:                       TYPE NAME                    SIZE       IDENTIFIER
       0:     FDisk_partition_scheme                        *15.8 GB    disk4
       1:                  Apple_HFS Untitled                15.8 GB    disk4s1
    ```
2. フォーマット
    ```bash
    % diskutil eraseDisk ms-dos UBUNTU /dev/disk4
    Started erase on disk4
    Unmounting disk
    Creating the partition map
    Waiting for partitions to activate
    Formatting disk4s2 as MS-DOS (FAT) with name UBUNTU
    512 bytes per physical sector
    /dev/rdisk4s2: 30407648 sectors in 1900478 FAT32 clusters (8192 bytes/cluster)
    bps=512 spc=16 res=32 nft=2 mid=0xf8 spt=32 hds=255 hid=411648 drv=0x80 bsec=30437376 bspf=14848 rdcl=2 infs=1 bkbs=6
    Mounting disk
    Finished erase on disk4
    ```
3. アンマウント
    ```bash
    % diskutil unmountDisk /dev/disk4
    Unmount of all volumes on disk4 was successful
    ```
4. ISOをUSBメモリに書き込み
    ```bash
    % sudo dd if=ubuntu-22.04.2-live-server-amd64.iso of=/dev/disk4 bs=4028
    Password:
    490559+1 records in
    490559+1 records out
    1975971840 bytes transferred in 439.278078 secs (4498225 bytes/sec)
    ```
5. USBメモリを取り外し
    ```bash
    % diskutil eject /dev/disk4
    Disk /dev/disk4 ejected
    ```

## USBメモリからUbuntuをインストール

画面の指示に従いインストールを行う

## MicroK8sのインストール

```bash
sudo snap install microk8s --classic
sudo snap install kubectl --classic
sudo cp -r /root/.kube ~/
sudo chown -R $USER ~/.kube
sudo usermod -a -G microk8s $USER
newgrp microk8s
microk8s status --wait-ready

microk8s config > $HOME/.kube/config
kubectl get all --all-namespaces
sudo vim /etc/hosts
# 各サーバーのIPとホスト名を記載
microk8s add-node
# add-nodeで表示されたコマンドを他サーバでも実行

microk8s enable metallb
# MetalLBで使用するIP範囲を指定。自分は192.168.128.200-192.168.128.230
microk8s enable ingress
```
