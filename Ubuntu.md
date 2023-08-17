
목차

----

1. Linux 기본 명령어

$> lsb_release -a

[Ubuntu Server / GUI 설치](#Ubuntu-Server-/-GUI-설치)

1. [페키지 관리](#페키지-관리)

   1. dpkg 명령어

   1. [apt upgrade](#apt-upgrade)

      1. [ubuntu not upgraded(kept back)](#ubuntu-not-upgraded(kept-back))

1. 네트워크
   1. [네트워크 유틸리티 설치](#네트워크-유틸리티-설치)
   1. [tshark 간단 사용법](#tshark-간단-사용법) 
   1. [브릿지](#브릿지)
      1. [brctl 명령어](#brctl-명령어)
      1. [ip 명령어](#ip-명령어) 
      1. [ethtool 명령어](#ethtool-명령어)
   1. [네트워크 Namespace](#네트워크-namespace)
      1. [호스트와 컨테이너를 연결하는 가상 인터페이스 만들기](#호스트와-컨테이너를-연결하는-가상-인터페이스-만들기)

1. [리눅스 가상화](#리눅스-가상화)
   1. [Ubuntu에서 KVM 설치하기](#Ubuntu에서-KVM-설치하기)
   1. [가상 머신 과 네트워킹](#가상-머신-과-네트워킹)
      1. [Use bridged networking for virtual machines](#use-bridged-networking-for-virtual-machines) 
         1. [virsh net-xxx 명령어](#virsh-net-xxx-명령어)
      1. [snapshot](#snapshot) 

----

### Ubuntu Server / GUI 설치

  GUI 환경을 제공하는 GUI 패키지 목록

  - kubuntu-desktop
  - lubuntu-desktop
  - ubuntu-desktop
  - ubuntu-desktop-minimal
  - xubuntu-desktop

# 페키지 관리

## dpkg 명령어

  > 데비안 패키지 관리 시스템의 기초가 되는 소프트웨어로서, deb 패키지의 설치, 삭제, 정보 제공을 위해 사용되는 명령어  
    dpkg 자체는 apt 등과 같은 고급 도구에 비해 낮은 레벨의 도구이며 복잡한 패키지를 원격에서 받아오는 등의 일을 합니다.

  * 사용법

    ````
    # 설치된 패키지 목록 확인
    $> dpkg -l
   
    # 해당 패키지로부터 설치된 모든 파일 목록 확인
    $>
    ````

## apt upgrade

### ubuntu not upgraded(kept back)

  우분투는 단계적 업데이트를 하고 있는데, 특정 문제가 해결된 빠른 업데이트는 아래와 같은 목록으로 나타납니다. 하지만 아직 안정화가 끝나지 않은 상태이기 때문에 업그레이드가 되지 않은 것입니다.

# 네트워크

## 네트워크 유틸리티 설치

```shell
$> sudo apt install -y iputils-ping
```

## tshark 간단 사용법

```shell
# 설치
$> sudo apt install -y tshark

# 설치된 NIC 를 보여줌.
$> shark -D

$> tshark -i 1 -Y 'tcp.port == 80 && ip.addr == 192.168.1.10' -t a --color
```

## 브릿지

  > 브릿지(Bridge)는 두 개의 이더넷 세그먼트를 연결하기 위해서 사용한다.  
    브릿지에서 패킷은 IP주소가 아닌, 이더넷 주소를 기반으로 전송된다.  
    패킷은 L2영역에서 이루어지기 때문에, 프로토콜에 상관 없이 투명하게 다룰 수 있다.

  > 니눅스 브릿지 코드는 ANSI/IEEE 802.1d 표준의 서브셋으로 구현한다.
 
### 브릿지와 방화벽

리눅스 브릿지는 트래픽을 제어할 수 있기 때문에, 하드웨어 브릿지 보다 많은 기능을 수행할 수 있다. 리눅스 브릿지는 방화벽 프로그램인 ebtables/iptables 과 함께 사용한다.

### brctl 명령어

우분투 리눅스에서 브릿지는 기본 모듈로 올라온다. 특별한 설정이 없더라도 brctl 명령을 사용할 수 있다.  
brctl 명령을 찾을 수 없다면 ```bridge-utils```패키지를 인스톨 하자.

```shell
$> sudo apt install bridge-utils

$> brctl 
Usage: brctl [commands]
commands:
        addbr         <bridge>        add bridge
        delbr         <bridge>        delete bridge
        addif         <bridge> <device>    add interface to bridge
        delif         <bridge> <device>    delete interface from bridge
        hairpin       <bridge> <port> {on|off}    turn hairpin on/off
        setageing     <bridge> <time>        set ageing time
        setbridgeprio    <bridge> <prio>        set bridge priority
        setfd         <bridge> <time>        set bridge forward delay
        sethello      <bridge> <time>        set hello time
        setmaxage     <bridge> <time>        set max message age
        setpathcost    <bridge> <port> <cost>    set path cost
        setportprio    <bridge> <port> <prio>    set port priority
        show          [ <bridge> ]        show a list of bridges
        showmacs      <bridge>        show a list of mac addrs
        showstp       <bridge>        show bridge stp info
        stp           <bridge> {on|off}    turn stp on/off
```

  * brctl 명령어 사용 예) 

    ```shell
    # 브릿지 디바이스 추가하기
    $> brctl addif bridgename device
    
    # 브릿지 디바이스 제거하기
    $> brctl delif bridgename device
    
    # 브릿지에 연결 및 확인
    $> brctl addbr br549
    $> brctl addif br549 eht0
    $> brctl addif br549 eth1
    $> brctl show
    bridge name  bridge id           STP enabled    interfaces
    br549        8000.5a2f6ffa5b2c   no             tap1
                                                    tap2
    ```

    브릿지는 패킷을 받으면, 패킷의 MAC을 일정시간 저장한다. 그리고 MAC 테이블이 가득 차는 걸 방지하기 위해서 일정 시간마다 MAC주소를 삭제하는데, 이를 ageing timer이라고 한다. setageing 명령을 이용해서 ageing time 시간을 변경할 수 있다. time의 단위는 초(second)다.
    
    ```shell
    $> brctl setageing bridgename time
    ```

#### Spanning Tree protocol

  여러 개 혹은 중복(redundant) 브릿지를 설치하는 경우, 루프를 방지하기 위해서 STP 를 활성화 해야 한다.

  ```shell
  brctl stp br549 on
  ```

### ip-명령어

  * link
    * show - display device attributes
      > dev NAME (default)  
        NAME specifies the network device to show.
      
      > group GROUP  
        GROUP specifies what group of devices to show.

      > vrf NAME  
        NAME specifies the VRF which enslaves devices to show.
       
      > type TYPE
        TYPE specifies the type of devices to show.  
      >  
      > Note that the type name is not checked against the list of supported types - instead it is sent as - is to the kernel.  
      Later it is used to filter the returned interface list by comparing it with the relevant attribute in case the kernel didn't filter already. Therefore any string is accepted, but may lead to empty output.

    ```shell
    $> ip -c -a
    ```

### ethtool 명령어

리눅스에서 NIC Card의 여러가지 정보를 확인할 수 있습니다.

  ```shell
  # 드라이버 정보 확인하기
  $> ethtool -i enp3s0
  
  # 다양한 정보 확인하기
  $> ethtool enp3s0
  ```

## 네트워크 Namespace

### 호스트와 컨테이너를 연결하는 가상 인터페이스 만들기

리눅스에서는 이 가상 인터페이스를 veth(Virtual Ethernet Device)라고 부르며 ip 명령어로 생성하는 것이 가능합니다. veth는 항상 쌍으로 만들어집니다.

  ```shell
  $> sudo ip link add veth0 type veth peer name veth1
  ```
  ![img.png](.res/ubuntu.네트워크.네임스페이스.01.png)

  ```shell
  $> sudo ip link add veth0 type veth peer name veth1
  ```

  ```shell
  $> sudo ip netns add netns.01
  $> sudo ip netns list
  
  $> sudo ip netns exec netns.01 ip -br link
  ```

  > ip netns를 사용하면 ```/var/run/netns``` 아래에 네트워크 네임스페이스가 영속화됩니다.  
    네트워크 네임스페이스 별 설정은 ```/etc/netns``` 아랭에 저장합니다.

  ![img.png](.res/ubuntu.네트워크.네임스페이스.02.png)

  ```shell
  $> sudo ip netns add netns.01
  $> sudo ip link add veth0 type veth peer name veth1

  $> sudo link set veth1 netns netns.01 
  ```

  ![img.png](.res/ubuntu.네트워크.네임스페이스.03.png)

   ```shell
   $> sudo ip a add 10.200.0.2/24 dev veth0
   $> suo ip netns exec netns.01 ip a add 10.200.0.3/24 dev veth1
   
   $> sudo ip link set dev veth0 up
   $> sudo ip netns exec netns.01 sudo ip link set dev veth1 up
   ```

  ![img.png](.res/ubuntu.네트워크.네임스페이스.04.png)

   ```shell
   $> sudo ip link add br0 type bridge
   $> sudo ip link set br0 up
   $> sudo ip netns add netns.01
   $> sudo ip link add brid1 type veth peer name veth1
   
   $> sudo ip link set brid1 master br0
   $> sudo ip link set dev brid1 up
   
   $> sudo ip link set veth1 netns netns.01
   $> sudo ip netns exec netns.01 ip link set dev veth1 up
   ```

   정상적으로 의도한 대로 동작하지 않는 경우

   ```shell
   $> iptables --policy FORWARD ACCEPT
   $> iptables -L | grep FORWARD
   
   # NAT 규칙을 추가
   $> iptables -t nat -A POSTROUTING -s 10.201.0.0/24 -j MASQUERADE
   
   # Namespace 별도 DNS 설정
   $> sudo echo 'nameserver 8.8.8.8' > /etc/netns/netns.01/resolv.con
   ```



# 리눅스 가상화

## Ubuntu에서 KVM 설치하기

```shell
# 가사화 지원 여부 확인 
$> sudo apt install -y cpu-checker
$> kvm-ok

  >INFO: /dev/kvm exists
   KVM acceleration can be used 
```

```shell
# 요구 패키지 설치 
$> sudo apt install -y qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils virtinst virt-manager

# KVM 설치 학인
$> kvm --version
```
 - qemu-kvm - KVM 하이퍼바이저에 대한 하드웨어 에뮬레이션을 제공
 - libvirt-daemon-system - libvirt 데몬을 시스템 서비스로 실행하는 구성 파일
 - libvirt-clients - 가상화 플랫폼을 관리하기위한 소프트웨어
 - bridge-utils - 이더넷 브리지를 구성하기위한 명령 줄 도구 세트
 - virtinst - 가상 머신을 만들기위한 명령 줄 도구 집합
 - virt-manager - 사용하기 쉬운 GUI 인터페이스와 libvirt를 통해 가상 머신을 관리하기위한 명령 줄 유틸리티

  libvirtd 가 정상적으로 실행되었는지 확인

```shell 
$> sugo systemctl is-active libvirtd

# Output
$> active
```

가상 머신을 생성하고 관리하려면 사용자 계정을 "libvirt" 및 "kvm" 그룹에 추가

```shell 
$> sudo usermod -aG libvirt $USER
$> sudo usermod -aG kvm     $USER
```

VM 생성 및 실행 방법

```shell
# 방법 1
$> sudo virt-manager

# 방법 2
$> sudo virt-install --name ubuntu-guest --os-variant ubuntu20.04 --vcpus 2 --ram 2048 
   --location http://ftp.ubuntu.com/ubuntu/dists/focal/main/installer-amd64/ 
   --network bridge=virbr0,model=virtio 
   --graphics none --extra-args='console=ttyS0,115200n8 serial'
```

### 가상 머신 과 네트워킹

  KVM에서는 기본값으로 호스트 OS위에 'virbr0'이라는 가상브릿지와 TAP디바이스 'vnet~'이 작성되어 'libvirtd' 데몬에 의해 관리된다.

  -net 옵션을 이용해서 네트워크 카드를 설정할 수 있다. 네트워크 설정을 위해서는 두개의 -net 옵션을 이용해야 한다.  
  기본 값은 -net nic -net user 이다.

  * 가상 머신상에서 네트워크 카드 정보 확인, lshw 는 모든 하드웨어 정보를 보여주는 리눅스 명령어

    ```shell
    $> lshw
    ......
           *-network
               description: Ethernet interface
               product: RTL-8139/8139C/8139C+
    ```

#### 지원하는 네트워크 카드

NE2000 PCI, NE2000 ISA, RTL 8139, PCNET을 사용할 수 있다. -net nic,model을 이용해서 게스트 운영체제에 제공할 네트워크 카드를 설정할 수 있다.

  ```shell
  -net nic,model=ne2k_pci -net user
  -net nic,model=ntl8139 -net user
  -net nic,model=pcnet -net user
  ```
  ISA 카드를 사용할 경우 -M 옵션을 이용해야 한다.

#### Use bridged networking for virtual machines

  * Creating a new virtual network
  
    아래와 같이 가상머신을 위한 네트워크 정의 파일 "bridged.xml" 파일을 만든다.  
    
    ```xml
    <network>
      <name>bridged</name>
      <forward mode="bridge" />
      <bridge name="br0" />
    </network>
    ```

    ```shell
    # 네트워크 정의 파일을 이용하여 새로운 네트워크를 생성한다.
    $> sudo virsh net-define bridged.xml
    
    # 
    $> sudo virsh net-start bridged
    $> sudo virsh net-autostart bridged
    ```

#### virsh net-xxx 명령어


#### snapshot

  * create snapshot

    ```shell
    $> virsh snapshot-create-as --domain {VM-NAME} --name "{SNAPSHOT-NAME}"
    
    $> virsh shapshot-list --domain {VM-NAME}
    
    $> virsh snapshot-revert --domain {VM-NAME} --snapshotname "{SNAPSHOT-NAME}" --running
    
    $> virsh snapshot-delete --domain {VM-NAME} --snapshotname "{SNAPSHOT-NAME}"
    ```
    


