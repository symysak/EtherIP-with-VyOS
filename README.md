# VyOS1.4でEtherIPを使う
EtherIP(IPv6)をVyOSで使う方法を紹介します。
IPv4は検証していないのでよくわかりません。
稼働実績としては、4か月ほど安定して稼働しています。
## 環境
VyOS 1.4
## EtherIPとは
EtherIP(RFC3378)
## 使用するもの
https://github.com/symysak/eoip
※fork元
https://github.com/Nat-Lab/eoip
## 設定
VyOSのconfigで設定できないことを無理やりやっているので、結構お行儀が悪いです。自己責任で行ってください。

流れとしては、isoをビルドする環境でeoipをmakeしてから、それをVyOS側に移し、make installする。
### isoをビルドできる環境で作業する
```
git clone https://github.com/symysak/eoip
cd eoip/
make
cd ..
zip -r eoip-files eoip/
```
### VyOSで作業する
```
unzip eoip-files.zip
cd eoip
make install
```
これで完了です。
実際にEtherIPv6を使ってみるところまでやってみます。
```
vi /config/scripts/vyos-postconfig-bootup.script

sudo ip tuntap add dev tap1 mode tap
sudo ip link set tap1 up
sudo ip addr add <IPアドレス> dev tap1
sudo ip link set tap1 mtu 1464
sudo eoip -6 tap1 remote <remote ip addr> local <local ip addr> &
:wq
```
configure内ではtap1は設定に使用できないので、先ほどの/config/scripts/vyos-postconfig-bootup.scriptに、tap1をbr0のメンバーに入れる設定等を書いて、configureではbr0として使用するといい感じになると思います。
