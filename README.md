  🚀 **Rocky Linux 8 Kickstart Server Setup**

  📌 **Project Overview**

This project demonstrates how to set up an **Automated Rocky Linux 8 Deployment Server** using:

* PXE Boot
* DHCP Server
* TFTP Server
* HTTP Server
* Kickstart Configuration

The goal of this project is to automate OS installation across multiple systems without manual intervention.

------------------------------------------------------------------------------------------------------------------------------------

  🛠️ **Technologies Used**

* Rocky Linux 8
* PXE Boot
* DHCP (Dynamic Host Configuration Protocol)
* TFTP (Trivial File Transfer Protocol)
* HTTP Server (Apache)
* Kickstart

------------------------------------------------------------------------------------------------------------------------------------

  🧱 **Lab Setup**

| Component      | Details           |
| -------------- | ----------------- |
| Server OS      | Rocky Linux 8     |
| Client Machine | PXE Boot Enabled  |
| Network Type   | LAN               |
| ISO Used       | Rocky Linux 8 ISO |

---

  ⚙️ **Step-by-Step Setup**

  🔹 1. Install Required Packages

```bash
dnf install -y dhcp-server tftp-server httpd syslinux
```

------------------------------------------------------------------------------------------------------------------------------------

  🔹 **2. Configure DHCP Server**

**Edit DHCP config file:**

```bash
vi /etc/dhcp/dhcpd.conf
```

**Add:**

```bash
subnet 192.168.0.0 netmask 255.255.255.0 {
  range 192.168.0.100 192.168.0.200;      **Your IP Range**
  option routers 192.168.0.1;
  filename "pxelinux.0";
  next-server 192.168.0.170;             ** System IP Address**
}
```

**Start DHCP:**

```bash
systemctl enable --now dhcpd
```

------------------------------------------------------------------------------------------------------------------------------------

  🔹**3. Configure TFTP Server**

```bash
systemctl enable --now tftp.socket
```

Copy PXE boot files:

```bash
cp /usr/share/syslinux/pxelinux.0 /var/lib/tftpboot/
cp /usr/share/syslinux/menu.c32 /var/lib/tftpboot/
```

**Create PXE config directory:**

```bash
mkdir -p /var/lib/tftpboot/pxelinux.cfg
```

------------------------------------------------------------------------------------------------------------------------------------

  🔹 **4.Mount Rocky Linux ISO**

```bash
mkdir /var/www/html/rocky8
mount -o loop Rocky-8.iso /var/www/html/rocky8
```

------------------------------------------------------------------------------------------------------------------------------------

  🔹 **5. Start HTTP Server**

```bash
systemctl enable --now httpd
```

------------------------------------------------------------------------------------------------------------------------------------

 🔹 **6. Configure PXE Boot Menu**

```bash
vi /var/lib/tftpboot/pxelinux.cfg/default
```

Add:

```bash
default menu.c32
prompt 0
timeout 50

label rocky8
  menu label Install Rocky Linux 8
  kernel vmlinuz
  append initrd=initrd.img inst.repo=http://192.168.0.170/rocky8 inst.ks=http://192.168.0.170/ks.cfg        
```
"  Here : system IP Address (192.168.0.170) "
------------------------------------------------------------------------------------------------------------------------------------

  🔹 **7. Copy Kernel Files**

```bash
cp /var/www/html/rocky8/images/pxeboot/vmlinuz /var/lib/tftpboot/
cp /var/www/html/rocky8/images/pxeboot/initrd.img /var/lib/tftpboot/
```

------------------------------------------------------------------------------------------------------------------------------------

  🔹 **8. Create Kickstart File**

```bash
vi /var/www/html/ks.cfg
```

**Example:**

```bash
 version=RHEL8
install
url --url="http://192.168.0.170/rocky8"   Here : system IP Address (192.168.0.170)
lang en_US.UTF-8
keyboard us
network --bootproto=dhcp
rootpw redhat
firewall --disabled
selinux --disabled
timezone Asia/Kolkata
bootloader --location=mbr
clearpart --all --initlabel
autopart
reboot

%packages
@^minimal-environment
%end
```

------------------------------------------------------------------------------------------------------------------------------------

  🔹 **9. Restart Services**

```bash
systemctl restart dhcpd
systemctl restart httpd
systemctl restart tftp
```

------------------------------------------------------------------------------------------------------------------------------------

  **🚧 Problems Faced**

* DHCP not assigning IP
* TFTP permission issues
* HTTP server not accessible
* PXE boot failure
* Wrong Kickstart path

-------------------------------------------------------------------------------------------------------------------------------------

  **💡 Key Learnings**

* Automated OS deployment using Kickstart
* Network booting (PXE)
* Server configuration (DHCP, TFTP, HTTP)
* Troubleshooting real-time errors

--------------------------------------------------------------------------------------------------------------------------------------

  **📚 Conclusion**

This project helped in understanding how enterprise-level OS deployment works using automation tools like Kickstart and PXE boot.

---------------------------------------------------------------------------------------------------------------------------------------


**Omkar Patil**
GitHub: https://github.com/Omkar-patil-006

----------------------------------------------------------------------------------------------------------------------------------------

