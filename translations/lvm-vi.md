<div align="right">
  <div><b>Language:</b> VI | <a href="./lvm-en.md">EN</a></div>
  <div><i>Your language contributions are welcome!</i></div>
</div>
<p align="center">
  <br/>
  <a href="https://phuonguno98.github.io/Logical-Volume-Management/">	
      <img src="../img/lvm.webp" alt="Logical Volume Management" style="height: 80%; width: 80%">
  </a>
</p>


# Nội dung
<!--TOC-->
- [Nội dung](#nội-dung)
- [1. Giới thiệu về LVM](#1-giới-thiệu-về-lvm)
  - [1.1. Một số ứng dụng của LVM](#11-một-số-ứng-dụng-của-lvm)
  - [1.2. Cấu trúc của LVM](#12-cấu-trúc-của-lvm)
  - [1.3. Các thành phần của LVM](#13-các-thành-phần-của-lvm)
- [2. Cài đặt và cấu hình LVM trên hệ điều hành CentOS 8](#2-cài-đặt-và-cấu-hình-lvm-trên-hệ-điều-hành-centos-8)
  - [2.1. Tạo máy ảo CentOS và gắn thêm 3 ổ cứng ảo](#21-tạo-máy-ảo-centos-và-gắn-thêm-3-ổ-cứng-ảo)
  - [2.2. Tạo các Partition cho các ổ đĩa mới](#22-tạo-các-partition-cho-các-ổ-đĩa-mới)
  - [2.3. Tạo Physical Volume](#23-tạo-physical-volume)
  - [2.4. Tạo Volume Group](#24-tạo-volume-group)
  - [2.5. Tạo Logical Volume](#25-tạo-logical-volume)
  - [2.6. Tạo mount point và thực hiện mount LVM mới](#26-tạo-mount-point-và-thực-hiện-mount-lvm-mới)
- [3. Mở rộng/thay đổi kích thước Logical Volume](#3-mở-rộngthay-đổi-kích-thước-logical-volume)
  - [3.1. Mở rộng Volume Group](#31-mở-rộng-volume-group)
  - [3.2. Tăng kích thước Logical Volume](#32-tăng-kích-thước-logical-volume)
  - [3.3. Giảm dung lượng Logical Volume và Volume Group](#33-giảm-dung-lượng-logical-volume-và-volume-group)
- [4. Auto-mount Logical Volume](#4-auto-mount-logical-volume)
- [5. Snapshot và restore Logical Volume](#5-snapshot-và-restore-logical-volume)
  - [5.1. Tạo snapshot Logical Volume](#51-tạo-snapshot-logical-volume)
  - [5.2. Tăng thêm dung lượng cho snapshot](#52-tăng-thêm-dung-lượng-cho-snapshot)
  - [5.3. Restore Logical Volume](#53-restore-logical-volume)
- [6. Remove Logical Volume](#6-remove-logical-volume)
- [7. Remove Volume Group](#7-remove-volume-group)
- [8. Remove Physical Volume](#8-remove-physical-volume)
- [9. LVM Thin Provisioning](#9-lvm-thin-provisioning)
  - [9.1. Giới thiệu Thin Provisioning](#91-giới-thiệu-thin-provisioning)
  - [9.2. Cấu hình Thin Provisioning](#92-cấu-hình-thin-provisioning)
    - [9.2.1. Tạo Volume group](#921-tạo-volume-group)
    - [9.2.2. Tạo thin pool](#922-tạo-thin-pool)
    - [9.2.3. Tạo thin volume](#923-tạo-thin-volume)
    - [9.2.4 Tạo filesystem và mount các volume](#924-tạo-filesystem-và-mount-các-volume)
- [10. Tính năng Manage Multiple Logical Volume Management Disk sử dụng Striping I/O](#10-tính-năng-manage-multiple-logical-volume-management-disk-sử-dụng-striping-io)
- [11. Tính năng LVM Migration](#11-tính-năng-lvm-migration)
- [Hướng dẫn cấu hình LVM một số trường hợp thường sử dụng](#hướng-dẫn-cấu-hình-lvm-một-số-trường-hợp-thường-sử-dụng)
  - [Trường hợp 1: Tăng dung lượng cho disk chứa OS và mở rộng dung lượng cho phân vùng chứa `root`](#trường-hợp-1-tăng-dung-lượng-cho-disk-chứa-os-và-mở-rộng-dung-lượng-cho-phân-vùng-chứa-root)
  - [Trường hợp 2: Tăng dung lượng phân vùng `root` (`/`) bằng cách gắn thêm disk mới](#trường-hợp-2-tăng-dung-lượng-phân-vùng-root--bằng-cách-gắn-thêm-disk-mới)
- [License](#license)
<!--/TOC-->

# 1. Giới thiệu về LVM
**LVM (Logical Volume Management)** là một công nghệ giúp quản lý các thiết bị lưu trữ dữ liệu trên các hệ điều hành Linux. Công nghệ này cho phép người dùng gom nhóm các ổ cứng vật lý và phân tách chúng thành những phân vùng nhỏ hơn, dễ dàng mở rộng các phân vùng này khi cần thiết.

LVM cho phép ấn định không gian đĩa cứng thành những **Logical Volume** khiến cho việc thay đổi kích thước trở nên dễ dàng hơn (so với partition). Với **Logical Volume Manager** (LVM) bạn có thể thay đổi kích thước mà không cần phải sửa lại partition table của OS.

## 1.1. Một số ứng dụng của LVM
- Quản lý một lượng lớn ổ đĩa một cách dễ dàng.
- Dễ dàng dồn, tách đĩa.
- Điều chỉnh phân vùng ổ cứng một cách linh động.
- Backup hệ thống bằng cách snapshot các phân vùng ổ cứng (real-time).
- Migrate dữ liệu dễ dàng.

## 1.2. Cấu trúc của LVM

<p align="center"><img src="../img/1.png" alt="Cấu trúc của LVM"></p>

## 1.3. Các thành phần của LVM
LVM phân các lớp trên các ổ cứng vật lý bao gồm các thành phần sau:

**Hard drives – Drives:** Thiết bị lưu trữ dữ liệu, ví dụ như trong linux nó là `/dev/sda`.

**Partition:**
- Partitions là các phân vùng của Hard drives, mỗi Hard drives có 4 partition, trong đó partition bao gồm 2 loại là primary partition và extended partition.
- Primary partition: Phân vùng chính, có thể khởi động , mỗi đĩa cứng có thể có tối đa 4 phân vùng này.
- Extended partition: Phân vùng mở rộng

**Physical Volumes – PV:**
- Ổ cứng vật lý từ hệ thống (đĩa cứng, partition, iSCSI LUN, SSD…) là đơn vị cơ bản để LVM dùng để khởi tạo các Volume Group. Trên mỗi một PV sẽ chứa khoảng 1MB header ghi dữ liệu về cách phân bố của Volume Group chứa nó. Header này sẽ hỗ trợ rất nhiều trong việc phục hồi dữ liệu khi có sự cố xảy ra.
- Là những thành phần cơ bản được sử dụng bởi LVM dể xây dựng lên các tầng cao hơn . Một Physical Volume không thể mở rộng ra ngoài phạm vi một ổ đĩa. Chúng ta có thể kết hợp nhiều Physical Volume thành Volume Groups.

**Volume Group – VG:**
- Là tập hợp các ổ cứng vật lý (PV) thành một kho lưu trữ chung với tổng dung lượng của các ổ đĩa con. Mỗi khi ta thêm một PV vào VG, LVM sẽ tự động chia dung lượng trên PV thành nhiều Physical Extent với kích cỡ bằng nhau. Và từ VG, ta có thể tạo ra nhiều Logical Volume và dễ dàng chỉnh sửa dung lượng của chúng.
- Nhiều Physical Volume trên những ổ đĩa khác nhau được kết hợp lại thành một Volume Group.
- Volume Group được sử dụng để tạo ra các Logical Volume, trong đó người dùng có thể tạo, thay đổi kích thước, lưu trữ, gỡ bỏ và sử dụng.

**Logical Volume – LV:** là các phân vùng logic được tạo ra từ VG. Logical Volume tương tự như các partition trên ổ cứng bình thường nhưng linh hoạt hơn vì kích thước của LV có thể được dễ dàng thay đổi theo thời gian thực mà không lo làm gián đoạn hệ thống. Sở dĩ ta có thể dễ dàng thay đổi được kích thước của LV vì LV được chia thành nhiều Logical Extent, mỗi Logical Extent này sẽ được mapping tương ứng với 1 Physical Extent trên các ổ đĩa.

**extent:** extent là đơn vị nhỏ nhất của VG. Mỗi một volume được tạo ra từ VG chứa nhiều extent nhỏ với kích thuớc cố định bằng nhau. Các extent trên LV không nhất thiết phải nằm liên tục với nhau trên ổ cứng vật lý bên dưới mà có thể nằm rải rác trên nhiều ổ cứng khác nhau. Extent chính là nền tảng cho công nghệ LVM, các LV có thể được mở rộng hay thu nhỏ lại bằng cách add thêm các extent hoặc lấy bớt các extent từ volume này.

# 2. Cài đặt và cấu hình LVM trên hệ điều hành CentOS 8
## 2.1. Tạo máy ảo CentOS và gắn thêm 3 ổ cứng ảo
Chọn **Edit virtual machine settings**

<p align="center"><img src="../img/2.png"></p>

Thực hiện add thêm **Hard Disk** cho máy ảo

<p align="center"><img src="../img/3.png"></p>

Khởi động máy ảo và kiểm tra trạng thái ổ đĩa bằng lệnh:

<pre>
[root@localhost admin]# lsblk
</pre>

<p align="center"><img src="../img/4.png"></p>

## 2.2. Tạo các Partition cho các ổ đĩa mới
Tạo **Partition** cho `sdb`, sử dụng lệnh:

<pre>
[root@localhost admin]# fdisk /dev/sdb
</pre>

Sử dụng lần lượt các lệnh như trong hình.

<p align="center"><img src="../img/5.png"></p>

Đã tạo thành công **Partition** LVM `sdb1` có dung lượng 20G. Thực hiện tương tự đối với `sdc`. Kết quả sau khi tạo **Partition**:

<p align="center"><img src="../img/6.png"></p>

## 2.3. Tạo Physical Volume
Tạo các **Physical Volume** cho `sdb1` và `sdc1` bằng lệnh sau:

<pre>
[root@localhost admin]# pvcreate /dev/sdb1 /dev/sdc1
</pre>

<p align="center"><img src="../img/7.png"></p>

Quét đĩa để kiểm tra:

<pre>
[root@localhost admin]# lvmdiskscan
</pre>

<p align="center"><img src="../img/8.png"></p>

Để xem các **Physical Volume** vừa tạo ra ta sử dụng lệnh:

<pre>
[root@localhost admin]# pvs
</pre>

<p align="center"><img src="../img/9.png"></p>

Hiển thị thông tin chi tiết **Physical Volume** `/dev/sdb1` bằng lệnh:

<pre>
[root@localhost admin]# pvdisplay /dev/sdb1
</pre>

<p align="center"><img src="../img/10.png"></p>

## 2.4. Tạo Volume Group
Để tạo một **Volume Group** mới có tên “**vg001**” từ các PV `sdb1` và `sdc1` sử dụng lệnh sau:

<pre>
[root@localhost admin]# vgcreate vg001 /dev/sdb1 /dev/sdc1
</pre>

<p align="center"><img src="../img/11.png"></p>

Để xem các **VG** vừa tạo sử dụng lệnh:

<pre>
[root@localhost admin]# vgs
</pre>

<p align="center"><img src="../img/12.png"></p>

Kiểm tra thông tin **VG** `vg001` bằng lệnh sau:

<pre>
[root@localhost admin]# vgdisplay vg001
</pre>

<p align="center"><img src="../img/13.png"></p>

**VG** `vg001` được tạo từ hai **PV** `sdb1` (20GB) và `sdc1` (20GB) nên sẽ có dung lượng là 40GB.
Có thể thêm một hay nhiều **PV** khác vào **VG** hiện có bằng lệnh sau:

<pre>
[root@localhost admin]# vgextend vg001 /dev/sdb2
</pre>

(trong đó, `/dev/sdb2` là **PV** mới muốn thêm vào **VG** `vg001` hiện có)

## 2.5. Tạo Logical Volume
Giả sử cần tạo 02 **LV** có tên là “**projects**” với dung lượng 10GB và “**backups**” sử dụng toàn bộ dung lượng còn lại của **VG** `vg001`. Chạy các lệnh sau:

<pre>
[root@localhost admin]# lvcreate -n projects -L 10G vg001
[root@localhost admin]# lvcreate -n backups -l 100%FREE vg001
</pre>

<p align="center"><img src="../img/14.png"></p>

Sử dụng lệnh `lvs` để xem các **LV** vừa tạo:

<p align="center"><img src="../img/15.png"></p>

Hoặc có thể xem toàn bộ thông tin của một **LV** “**projects**” bằng lệnh sau:

<pre>
[root@localhost admin]# lvdisplay vg001/projects
</pre>

<p align="center"><img src="../img/16.png"></p>

## 2.6. Tạo mount point và thực hiện mount LVM mới
Kiểm tra thông tin sử dụng không gian đĩa trên hệ thống ta thấy `projects` và `backups` chưa thể sử dụng:

<p align="center"><img src="../img/17.png"></p>

Để sử dụng được **LV**, ta cần tạo filesystem. Tạo filesystem cho **projects** sử dụng `ext4` (vì `ext4` cho phép tăng hoặc giảm kích thước của mỗi **LV**, với `xfs` chỉ cho phép tăng kích thước) bằng lệnh sau:

<pre>
[root@localhost admin]# mkfs.ext4 /dev/vg001/projects
</pre>

<p align="center"><img src="../img/18.png"></p>

Tạo thư mục mới tên `projects` và mount vào `/dev/vg001/projects`

<pre>
[root@localhost admin]# mkdir /projects
[root@localhost admin]# mount /dev/vg001/projects /projects/
</pre>

Kiểm tra lại kết quả mount bằng lệnh sau:

<pre>
[root@localhost admin]# df -HT
</pre>

<p align="center"><img src="../img/19.png"></p>

Mount thành công **LV** `projects` vào `/projects`, `/projects` hiện tại đã có thể sử dụng để lưu trữ dữ liệu.

<p align="center"><img src="../img/20.png"></p>

# 3. Mở rộng/thay đổi kích thước Logical Volume
## 3.1. Mở rộng Volume Group
Kiểm tra dung lượng **VG** `vg001` hiện tại bằng lệnh

<pre>
[root@localhost projects]# vgdisplay vg001
</pre>

<p align="center"><img src="../img/21.png"></p>

Dung lượng của `vg001` hiện tại **VG** **Size** là 40GB, **Free** là 0GB. Để tăng thêm dung lượng cho `vg001` ta tạo PV mới rồi thêm **PV** này vào `vg001`.

Tạo **Partition** `sdb2` mới dung lượng 16GB:

<p align="center"><img src="../img/22.png"></p>

Tạo **PV** mới `/dev/sdb2` bằng lệnh:

<pre>
[root@localhost admin]# pvcreate /dev/sdb2
</pre>

<p align="center"><img src="../img/23.png"></p>

Mở rộng **VG** `vg001` bằng cách thêm **PV** `sdb2` vào `vg001`, sử dụng lệnh:

<pre>
[root@localhost admin]# vgextend vg001 /dev/sdb2
</pre>

<p align="center"><img src="../img/24.png"></p>

Dung lượng của `vg001` lúc này đã được tăng lên thành 56GB và còn trống 16GB. Dung lượng của `vg001` đã được tăng thêm 16GB, như vậy ta có thể tăng kích thước của **LV** `projects` lên thêm tối đa dưới 16GB.

## 3.2. Tăng kích thước Logical Volume
Kiểm tra dung lượng của **LV** `projects` hiện tại:

<pre>
[root@localhost projects]# lvdisplay /dev/vg001/projects
</pre>

<p align="center"><img src="../img/25.png"></p>

Tăng thêm dung lượng cho `projects` thêm 10GB bằng lệnh:

<pre>
[root@localhost admin]# lvextend -L +10GB /dev/vg001/projects
</pre>

<p align="center"><img src="../img/26.png"></p>

Kích thước của `projects` lúc này đã được tăng lên thành 20GB:
Sau khi tăng kích thước của LV ta cần thay đổi kích thước tệp hệ thống filesystem và xác nhận thay đổi bằng lệnh sau (đối với `ext4/ext2/ext3`):

<pre>
[root@localhost admin]# resize2fs /dev/vg001/projects
</pre>

<p align="center"><img src="../img/27.png"></p>

Dung lượng của **projects** lúc này đã được tăng lên và có thể sử dụng được:

<p align="center"><img src="../img/28.png"></p>

## 3.3. Giảm dung lượng Logical Volume và Volume Group
Khi muốn giảm dung lượng các **Logical Volume**, ta cần phải chú ý vì nó có thể bị lỗi hoặc bị mất dữ liệu trong khi chúng ta giảm dung lượng của các **Logical Volume**. Để đảm bảo an toàn khi giảm **Logical Volume** cần thực hiện các bước sau:

1. Trước khi bắt đầu, cần sao lưu dữ liệu để tránh mất mát dữ liệu khi có sự cố xảy ra.
2. Khi giảm dung lượng **Logical Volume** chúng ta phải ngắt kết nối hệ thống tệp trước khi giảm.
3. Để giảm dung lượng **Logical Volume**, cần thực hiện đầy đủ và cẩn thận các bước cần thiết dưới đây:
    - Ngắt kết nối file system.
    - Kiểm tra file system sau khi ngắt kết nối.
    - Giảm file system.
    - Giảm kích thước **Logical Volume**.
    - Kiểm tra lỗi cho file system.
    - Mount lại file system và kiểm tra kích thước của nó.

Giả sử cần giảm dung lượng của LV **projects** lại còn 10GB và giảm dung lượng VG **vg001** xuống còn 40GB, ta thực hiện các bước như sau:
Đầu tiên, thực hiện unmount filesystem bằng lệnh:

<pre>
[root@localhost ~]# umount /dev/mapper/vg001-projects
</pre>

<p align="center"><img src="../img/29.png"></p>

Kiểm tra lỗi filesystem bằng lệnh

<pre>
[root@localhost ~]# e2fsck -f /dev/vg001/projects
</pre>

<p align="center"><img src="../img/30.png"></p>

Nếu không có điều bất thường xảy ra, ta có thể giảm dung lượng của **projects** xuống theo nhu cầu. Giảm dung lượng **LV** **projects** bằng lệnh:

<pre>
[root@localhost ~]# lvreduce -L 10G /dev/vg001/projects
</pre>

Sau đó gõ `y` để xác nhận:

<pre>
Do you really want to reduce vg001/projects? [y/n]: y
</pre>

<p align="center"><img src="../img/31.png"></p>

Kiểm tra lại lần nữa để đảm bảo filesystem không bị lỗi:

<pre>
[root@localhost admin]# e2fsck -f /dev/vg001/projects
</pre>

<p align="center"><img src="../img/32.png"></p>

Sau cùng để sử dụng được thì cần mount lại filesystem và kiểm tra dung lượng sau khi giảm.

Mount lại **LV** **projects**:

<pre>
[root@localhost admin]# mount /dev/vg001/projects /projects
</pre>

Kiểm tra lại dung lượng:

<pre>
[root@localhost admin]# df -HT
</pre>

<p align="center"><img src="../img/33.png"></p>

Dung lượng **VL** **projects** giờ đã được giảm xuống còn 10GB.

Tiếp theo, để giảm dung lượng **Volume Group** ta cũng thực hiện tương tự. Đầu tiên, unmount `/dev/vg001/projects`:

<pre>
[root@localhost ~]# umount /dev/vg001/projects
</pre>

<p align="center"><img src="../img/34.png"></p>

Kiểm tra lỗi filesystem bằng lệnh:

<pre>
[root@localhost admin]# e2fsck -f /dev/vg001/projects
</pre>

<p align="center"><img src="../img/35.png"></p>

Giảm dung lượng `vg001` xuống còn 40GB bằng cách gỡ **PV** `sdb2` khỏi `vg001`, sử dụng lệnh sau:

<pre>
[root@localhost ~]# vgreduce vg001 /dev/sdb2
</pre>

<p align="center"><img src="../img/36.png"></p>

VG `vg001` lúc này chỉ còn 40GB:

<p align="center"><img src="../img/37.png"></p>

Sau cùng kiểm tra filesystem và mount lại `/dev/vg001/projects`.

<p align="center"><img src="../img/38.png"></p>

# 4. Auto-mount Logical Volume
Các cấu hình **LVM** như trên chỉ là tạm thời, khi hệ thống khởi động lại sẽ *không tự động* mount các **LV**. Để tự động mount các **LV** khi hệ thống khởi động, ta cần hình trong file `/etc/fstab`.

Cấu trúc file `/etc/fstab` như sau:

<p align="center"><img src="../img/39.png"></p>

Cấu trúc của một trường gồm các phần:
**[Device]   [Mount Point]   [File System Type]   [Options]   [Dump]   [Pass]**

**[Device]** có thể sử dụng **UUID** hoặc dạng **path**. Xác định **UUID** của volume bằng lệnh sau:

<pre>
[root@localhost ~]# blkid
</pre>

<p align="center"><img src="../img/40.png"></p>

Để tự động mount **projects** khi khởi động, ta mở file `/etc/fstab` và thêm dòng sau vào cuối file và lưu lại.

<pre>
/dev/mapper/vg001-projects   /projects        ext4    defaults        0 0
</pre>

Hoặc

<pre>
UUID=" 2d3b52df-715c-4740-be30-e7cc5bbe07de "	/projects        ext4    defaults        0 0
</pre>

<p align="center"><img src="../img/41.png"></p>

# 5. Snapshot và restore Logical Volume
## 5.1. Tạo snapshot Logical Volume
Kiểm tra **projetcs** trước khi tạo snapshot:

<p align="center"><img src="../img/42.png"></p>

Kiểm tra dung lượng trống của **VG** ``vg001` bằng lệnh:

<pre>
[root@localhost ~]# vgs
</pre>

<p align="center"><img src="../img/43.png"></p>

Để tạo được snapshot cho **LV** `projects` thuộc **VG** `vg001` thì `vg001` cần có không gian trống **VFree**. Ta cần tăng dung lượng trống cho `vg001`:

<p align="center"><img src="../img/44.png"></p>

Dung lượng trống của `vg001` là cột **VFree** với giá trị 16GB, dung lượng này có thể dùng để tạo snapshot.
Tạo snapshot **projects-snap01** có dung lượng 10GB cho `/dev/vg001/projects` bằng lệnh sau:

<pre>
[root@localhost ~]# lvcreate -s -L 10G -n projects-snap01 /dev/vg001/projects
</pre>

<p align="center"><img src="../img/45.png"></p>

***Chú ý***: Chỉ có thể tạo được snapshot có dung lượng nhỏ hơn dung lượng ``vg001` còn trống.

Lệnh xóa snapshot:

<pre>
[root@localhost admin]# lvremove /dev/vg001/VinhLong-snap01
</pre>

<p align="center"><img src="../img/46.png"></p>

## 5.2. Tăng thêm dung lượng cho snapshot
Để tăng thêm dung lượng cho snapshot **projects-snap01** ta thực hiện tương tự như tăng dung dung cho một **Logical Volume**, sử dụng lệnh sau:

<pre>
[root@localhost admin]# lvextend -L +5G /dev/vg001/projects-snap01
</pre>

## 5.3. Restore Logical Volume
Trước khi restore cần unmount tệp hệ thống. Lệnh unmount:

<pre>
[root@localhost admin]# umount /dev/mapper/vg001-projects
</pre>

<p align="center"><img src="../img/47.png"></p>


Nội dung của **projects** trước khi restore:

<p align="center"><img src="../img/48.png"></p>

Để restore lại snapshot, ta sử dụng lệnh sau:

<pre>
[root@localhost admin]# lvconvert --merge /dev/vg001/projects-snap01
</pre>

<p align="center"><img src="../img/49.png"></p>

Sau khi restore lại thì snapshot sẽ được tự động xóa. Kiểm tra lại dữ liệu tại thời điểm snapshot sẽ được trả về lại như ban đầu:

<p align="center"><img src="../img/50.png"></p>

# 6. Remove Logical Volume
Trước khi remove **LV**, ta phải remove mục nhập **fstab** đã đặt trước đó và ngắt kết nối điểm lắp (mount point).

<p align="center"><img src="../img/51.png"></p>

*Ví dụ:* Bây giờ chúng ta sẽ loại bỏ LV **backups**. Trước tiên hãy đảm bảo rằng không có mục nhập nào trong tệp **fastab** cho **backups** và sau đó ngắt kết nối điểm gắn kết **backups**.

<p align="center"><img src="../img/52.png"></p>

Unmount **backup**:

<p align="center"><img src="../img/53.png"></p>

Sử dụng lệnh sau để thực hiện remove **LV** **backups**:

<pre>
[root@localhost ~]# lvremove /dev/vg001/backups
</pre>

<p align="center"><img src="../img/54.png"></p>

# 7. Remove Volume Group
Để remove **VG** `vg001` ta sử dụng lệnh sau:

<pre>
[root@localhost ~]# vgremove vg001
</pre>

Hoặc thêm tùy chọn `-f` để xác nhận xóa tất cả các **LV** có trong **VG**:

<pre>
[root@localhost ~]# vgremove -f vg001
</pre>

<p align="center"><img src="../img/55.png"></p>

# 8. Remove Physical Volume
Để remove các **PV** `sdb1`, `sdb2`, `sdc1` ta sử dụng lệnh sau:

<pre>
[root@localhost ~]# pvremove /dev/sdb1 /dev/sdb2 /dev/sdc1
</pre>

<p align="center"><img src="../img/56.png"></p>

# 9. LVM Thin Provisioning
## 9.1. Giới thiệu Thin Provisioning
**Thin Provisioning** là tính năng cấp phát ổ cứng dựa trên sự linh hoạt của **LVM**, nó cho phép tạo ra **LV** lớn hơn các dung lượng có sẵn. **LVM** chỉ cấp phát ổ cứng khi có dữ liệu thật được ghi xuống, vì vậy, tính năng này giúp tiết kiệm được dung lượng cho hệ thống, tận dụng một cách tối ưu dung lượng lưu trữ. Tuy nhiên, tính năng này sẽ gây ra phân mãnh hệ thống và tình trạng *over provisioning*.

## 9.2. Cấu hình Thin Provisioning
### 9.2.1. Tạo Volume group
Tiến hành tạo **VG** `vg001` như sau:

<p align="center"><img src="../img/57.png"></p>

### 9.2.2. Tạo thin pool
Tạo **Thin pool** `mythinpool` với dung lượng 5GB trong **VG** `vg001` bằng lệnh:

<pre>
[root@localhost ~]# lvcreate -L 5G --thinpool mythinpool vg001
</pre>

<p align="center"><img src="../img/58.png"></p>

### 9.2.3. Tạo thin volume
Tạo các **Thin volume** `user1`, `user2`, `user3` trong **Thin pool** `mythinpool` với dung lượng mỗi volume là 3GB.

<pre>
[root@localhost ~]# lvcreate -V 3G --thin -n user1 vg001/mythinpool
[root@localhost ~]# lvcreate -V 3G --thin -n user2 vg001/mythinpool
[root@localhost ~]# lvcreate -V 3G --thin -n user3 vg001/mythinpool
</pre>

<p align="center"><img src="../img/59.png"></p>

### 9.2.4 Tạo filesystem và mount các volume
Tạo các filesystem tương ứng bằng lệnh sau:

<pre>
[root@localhost ~]# mkfs.ext4 /dev/vg001/user1
[root@localhost ~]# mkfs.ext4 /dev/vg001/user2
[root@localhost ~]# mkfs.ext4 /dev/vg001/user3
</pre>

Tạo các mount point và thực hiện mount các volume:

<pre>
[root@localhost ~]# mkdir -p /{user1,user2,user3}
[root@localhost /]# mount /dev/vg001/user1 /user1
[root@localhost /]# mount /dev/vg001/user2 /user2
[root@localhost /]# mount /dev/vg001/user3 /user3
</pre>

Kết quả tạo thành công:

<p align="center"><img src="../img/60.png"></p>

Thử thêm tập tin vào và kiểm tra dung lượng sử dụng:

<p align="center"><img src="../img/61.png"></p>

Việc mở rộng thin volume cũng có thể được thực hiện tương tự như mở rộng **Logical Volume**.

# 10. Tính năng Manage Multiple Logical Volume Management Disk sử dụng Striping I/O
**Logical Volume Management** cho phép ghi dữ liệu qua nhiều ổ vật lý bằng cách sử dụng **Striping I/O**.

Đặc điểm của Striping:

- Nó sẽ làm tăng hiệu suất của đĩa.
- Tránh được việc ghi nhiều lần vào cùng một ổ cứng.
- Việc lấp đầy ổ đĩa có thể được giảm bớt bằng cách sử dụng tính năng phân dải trên nhiều ổ đĩa.

Cấu hình striping cho `vg001` để dữ liệu sẽ được ghi trên nhiều **PV**. Chuẩn bị **VG** `vg001`:

<p align="center"><img src="../img/62.png"></p>

`vg001` hiện có 3 **PV**, giờ ta sẽ cấu hình để dữ liệu sẽ được ghi trên cả 3 **PV** này. Chúng ta tạo ra một **Logical Volume** có tên `strip01` với kích thước 1GB, và được tạo trong **Volume Group** `vg001`, xác định sử dụng 3 stripe, có nghĩa là dữ liệu ghi vào **Logical Volume** của chúng ta, cần phải được **Logical Volume** ghi trên 3 **PV**.

<pre>
[root@localhost /]# lvcreate -L 1G -n strip01 -i3 vg001
</pre>

<p align="center"><img src="../img/63.png"></p>

Kiểm tra lại bằng lệnh:

<pre>
[root@localhost /]# lvdisplay /dev/vg001/strip01 -m
</pre>

<p align="center"><img src="../img/64.png"></p>

Để tạo kích thước stripes được xác định, chúng ta cần tạo một **Logical Volume** với kích thước 1GB bằng kích thước stripes được xác định là 256KB. Chỉ stripe trên 2 **PV**, chúng ta cũng có thể xác định **PV** nào chúng ta muốn được stripe.

<pre>
[root@localhost /]# lvcreate -L 1G -i2 -I 256 -n strip02 vg001 /dev/sdb1 /dev/sdc1
</pre>

<p align="center"><img src="../img/65.png"></p>

Để biết các **LV** phụ thuộc phân vùng nào ta có thể sử dụng lệnh sau:

<pre>
[root@localhost /]# dmsetup deps /dev/vg001/strip0[1-2]
</pre>

<p align="center"><img src="../img/66.png"></p>

# 11. Tính năng LVM Migration
Tính năng này cho phép di chuyển dữ liệu từ một **Logical Volume** sang một ổ mới mà không làm mất dữ liệu hoặc downtime. Có thể áp dụng với disk SATA,SSD,SAN storage iSCSI or FC.

Hiện tại ta có một **LV** `projects` được ánh xạ xuống ổ đĩa `/dev/sdb1`:

<p align="center"><img src="../img/67.png"></p>

Nội dung trong **projects** gồm:

<p align="center"><img src="../img/68.png"></p>

Giờ ta cần chuyển toàn bộ dữ liệu trong `sdb1` sang ổ đĩa mới. Đầu tiên, ta cần chuẩn bị một **PV** mới giả sử **PV** mới này được đặt tên là `sdc2`:

<p align="center"><img src="../img/69.png"></p>

Kiểm tra tên **Logical Volume** và **Volume Group** bằng lệnh sau:

<pre>
[root@localhost ~]#  vgs -o+devices | grep  vg001
</pre>

<p align="center"><img src="../img/70.png"></p>

Ta thấy, hiện tại **VG** `vg001` được nối với `/dev/sdb1`.

Tiếp theo, thêm `/dev/sdc2` đã tạo mới vào `vg001` bằng lệnh sau:

<pre>
[root@localhost ~]# vgextend vg001 /dev/sdc2
</pre>

<p align="center"><img src="../img/71.png"></p>

Thực hiện chuyển dữ liệu từ sdb1 sang `sdc2`.  Tạo một mirror mới để chuyển dữ liệu bằng lệnh sau:

<pre>
[root@localhost admin]# lvconvert -m1 /dev/vg001/projects /dev/sdc1
</pre>

<p align="center"><img src="../img/72.png"></p>

Khi đã tạo 1 mirror mới thì ta có thể bỏ `/dev/sdb1` và kiểm tra lại với `lvs -o+devices` chúng ta sẽ thấy `vg001` chỉ còn nối với `/dev/sdc1`:

<p align="center"><img src="../img/73.png"></p>

Kiểm tra lại dữ liệu trong `/projects` không bị mất hay thay đổi.

<p align="center"><img src="../img/74.png"></p>

Như vậy, ta đã chuyển chuyển dữ liệu từ `sdb1` sang `sdc1` thành công. Ta cũng có thể gỡ bỏ sdb1 khỏi `vg001`.

<p align="center"><img src="../img/75.png"></p>

# Hướng dẫn cấu hình LVM một số trường hợp thường sử dụng
## Trường hợp 1: Tăng dung lượng cho disk chứa OS và mở rộng dung lượng cho phân vùng chứa `root`
***Bước 1: Tăng thêm dung lượng mới cho disk.***

Server hiện tại có 01 disk với dung lượng 20GB

<p align="center"><img src="../img/76.png"></p>

Partition chứa `root` (`/`) là `sda2` hiện tại có dung lượng 19GB. Logical Volume chứa `root` (`/`) là `centos_centos--server-root` có dung lượng 17GB:

<p align="center"><img src="../img/77.png"></p>

Thực hiện tăng dung lượng cho disk thêm 30G. Sau đó tăng dung lượng phân vùng chứa `root` (`/`) thêm 30GB.

Sau khi tăng dung lượng cho disk, lúc này disk có dung lượng 50GB:

<p align="center"><img src="../img/78.png"></p>

Kiểm tra bằng lệnh `lsblk`, disk `sda` đã nhận dung lượng mới là 50GB:

<p align="center"><img src="../img/79.png"></p>

***Bước 2: Tăng dung lượng cho partition.***

Cần cài đặt gói `cloud-utils-growpart` (nếu server chưa có) để mở rộng phân vùng bằng lệnh cài đặt sau:

<pre>
[root@centos-server ~]# yum install cloud-utils-growpart
</pre>

Mở rộng dung lượng cho partition `sda2` đang chứa `root` (`/`) bằng lệnh:

<pre>
[root@centos-server ~]# growpart /dev/sda 2
</pre>

Trong câu lệnh trên, `/dev/sda` là tên disk, số `2` là số thứ tự partition chứa `root` (`/`).

<p align="center"><img src="../img/80.png"></p>

Kiểm tra lại dung lượng của `sda2` lúc này đã tăng lên thêm 30GB (thành 49GB):

<pre>
[root@centos-server ~]# lsblk
</pre>

<p align="center"><img src="../img/81.png"></p>

***Bước 3: Tăng dung lượng cho Physical Volume.***

Kiểm tra dung lượng trống của Physical Volume `/dev/sda2` trước khi tăng dung lượng:

<pre>
[root@centos-server ~]# pvs
</pre>

<p align="center"><img src="../img/82.png"></p>

Tăng dung lượng cho Physical Volume `/dev/sda2` bằng lệnh sau:

<pre>
[root@centos-server ~]# pvresize /dev/sda2
</pre>

<p align="center"><img src="../img/83.png"></p>

Kiểm tra dung lượng trống của Physical Volume sau khi tăng đã thêm 30GB cột **PFree**:

<pre>
[root@centos-server ~]# pvs
</pre>

<p align="center"><img src="../img/84.png"></p>

***Bước 4: Tăng dung lượng Logical Volume.***

Kiểm tra dung lượng của Volume Group đang trống 30GB (cột **VFree**) => Khả dụng để tăng dung lượng Logical Volume chứa `root` (`/`) :

<pre>
[root@centos-server ~]# vgs
</pre>

<p align="center"><img src="../img/85.png"></p>

Trên server hiện có 02 Logical Volume, kiểm tra bằng lệnh sau:

<pre>
[root@centos-server ~]# lvs
</pre>

<p align="center"><img src="../img/86.png"></p>

Mở rộng dung lượng cho Logical Volume chứa `root` (`/`) với toàn bộ dung lượng khả dụng hiện có:

<pre>
[root@centos-server ~]# lvextend -l +100%FREE /dev/centos_centos-server/root
</pre>

Trong câu lệnh trên, `/dev` là path trỏ tới disk, `/dev/centos_centos-server` là path trỏ tới Logical Volume Group, `/dev/centos_centos-server/root` là path trỏ tới Logical Volume `root` chứa `root` (`/`).

<p align="center"><img src="../img/87.png"></p>

Kiểm tra lại Logical Volume:

<pre>
[root@centos-server ~]# lvs
</pre>

Dung lượng đã được tăng thêm 30GB thành 47GB so với ban đầu là 17GB

<p align="center"><img src="../img/88.png"></p>

***Bước 5: Cập nhật lại filesystem.***

Kiểm tra định dạng filesystem mà `root` (`/`) đang sử dụng:

<pre>
[root@centos-server ~]# df -HT
</pre>

Cập nhật filesystem để `root` (`/`) nhận dung thêm dung lượng mới. Do `root` (`/`) đang sử dụng định dạng `xfs` nên sử dụng lệnh sau:

<pre>
[root@centos-server ~]# xfs_growfs /dev/mapper/centos_centos--server-root
</pre>

<p align="center"><img src="../img/89.png"></p>

Kiểm tra lại, dung lượng của `root` (`/`) lúc này đã được tăng lên thành 47GB (ban đầu 17GB).

<pre>
[root@centos-server ~]# lsblk
</pre>

<p align="center"><img src="../img/90.png"></p>

Dung lượng thực tế sử dụng:

<p align="center"><img src="../img/91.png"></p>

## Trường hợp 2: Tăng dung lượng phân vùng `root` (`/`) bằng cách gắn thêm disk mới
***Bước 1: Gắn thêm disk mới.***

Gắn thêm 01 disk mới mới 20GB cho server:

<p align="center"><img src="../img/92.png"></p>

Kiểm tra bằng lệnh, server đã nhận thêm 01 disk mới `sdb` có dung lượng 20GB:

<p align="center"><img src="../img/93.png"></p>

***Bước 2: Tạo partition mới.***

Tạo partition mới `sdb1` cho disk `sdb`:

<p align="center"><img src="../img/94.png"></p>

***Bước 3: Tạo Physical Volume.***

Tạo mới Physical Volume cho `sdb1`:

<pre>
[root@centos-server ~]# pvcreate /dev/sdb1
</pre>

<p align="center"><img src="../img/95.png"></p>

Kiểm tra lại Physical Volume vừa tạo:

<pre>
[root@centos-server ~]# pvs
</pre>

<p align="center"><img src="../img/96.png"></p>

***Bước 4: Mở rộng dung lượng Volume Group.***

Mở rộng VG `centos_centos-server` bằng cách thêm PV `sdb1` vào:

<pre>
[root@centos-server ~]# vgextend centos_centos-server /dev/sdb1
</pre>

<p align="center"><img src="../img/97.png"></p>

Sau khi mở rộng, ta thấy VG `centos_centos-server` nằm trên 02 PV `sda2` và `sdb1`:

<pre>
[root@centos-server ~]# pvs
</pre>

<p align="center"><img src="../img/98.png"></p>

***Bước 5: Mở rộng dung lượng Logical Volume chứa `root`.***

Mở rộng LV phân vùng chứa `root` (`/`):

<pre>
[root@centos-server ~]# lvextend -l +100%FREE /dev/mapper/centos_centos--server-root
</pre>

<p align="center"><img src="../img/99.png"></p>

***Bước 6: Cập nhật lại filesystem.***

Cập nhật filesystem để nhận dung thêm dung lượng mới. Do phân vùng `root` (`/`) sử dụng định dạng `xfs` nên sử dụng lệnh sau:

<pre>
[root@centos-server ~]# xfs_growfs /dev/mapper/centos_centos--server-root
</pre>

<p align="center"><img src="../img/100.png"></p>

Kiểm tra lại, dung lượng của `root` (`/`) lúc này đã được tăng lên thành 66GB (ban đầu 47GB) và nằm trên hai LV `sda2` và `sdb1`.

<pre>
[root@centos-server ~]# lsblk
</pre>

<p align="center"><img src="../img/101.png"></p>

<pre>
[root@centos-server ~]# df -hT
</pre>

<p align="center"><img src="../img/102.png"></p>

[Trang chính](../README.md)

# License

[UNO](../LICENSE.md) &copy;
