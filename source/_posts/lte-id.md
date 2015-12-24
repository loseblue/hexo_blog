title: lte id
tags: LTE
date: 2015-12-24 11:01:07
---

LTE ID的统计和分析。

<!--more-->

 
|Number|Identifier| Title| Purpose| Structure| Perm/Temp| 3GPP Reference|
|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
| 1 |IMSI |International Mobile Subscriber Identity |Unique ID of LTE subscriber |PLMN+MSIN <= 15 digits| P |TS 23.003 2.1-2.3|
| 2 |PLMN ID | Public Land Mobile Network Identifier | Unique ID of PLMN | MCC+MNC <= 6 digits | P | TS 23.003 2.2-2.3 |
| 3 |MCC | Mobile Country Code | Assigned by ITU | 3 digits | P | TS 23.003 2.2 |
| 4 |MNC | Mobile Network Code | Assigned by National Authority | 2-3 digits | P | TS 23.003 2.2 |
| 5 |MSIN | Mobile Subscriber Indentification Number | Assigned by Operator | 9-10 digits | P | TS 23.003 2.2 |
| 6 |MSISDN | Mobile Subscriber Int'l ISDN Number | Country Code, Nat'l Dest. Code, Subsc. Number (SCCP routing) | ITU-T E.164 CC, NDC, SN | P | TS 23.003 3.1-3.3 |
| 7 |GUMMEI | Globally Unique MME Identifier | Unique ID of MME (globally) | PLMN+MMEI | P | TS 23.003 2.8.1 |
| 8 |MMEI | MME Identifier | Unique ID of MME (in PLMN) Commissioned at eNB | MMEGI+ MMEC | P | TS 23.003 2.8.1 |
| 9 |MMEGI | MME Group Identifier | Unique group ID within a PLMN | 16 bits | P | TS 23.003 2.8.1 |
|10 |MMEC | MME Code | Unique ID within MME Group | 8 bits | P | TS 23.003 2.8.1 |
|11 |GUTI | Globally Unique Temporary UE Identity | Alias for IMSI between UE and MME for security reasons | GUMMEI+ M-TMSI | T | TS 23.003 2.8.1 |
|12 |TIN | Temporary Identity Used in Next Update | Indicates which temporary ID will be used in next update | =GUTI | T | TS 23.401 4.3.5.6 |
|13 |S-TMSI | SAE Temporary Mobile Subscriber Identity | Temp short UE ID unique in MME Pool | MMEC+ M-TMSI | T | TS 23.003 2.8.1 |
|14 |M-TMSI | MME Mobile Subscriber Identity | Unique UE ID within MME | 32 bits | T | TS 23.003 2.8.1 |
|15 |C-RNTI | Cell-Radio Network Temporary Identifier | Unique UE ID for UE with a radio resource connection within a cell | 0x0001- 0xFFF3 | T | TS 25.331 |
|16 |eNB S1AP UE ID | eNB S1 Application Protocol UE Identifier | Unique UE ID on S1-MME interface in eNB | 32-bit integer | T | TS 23.401 5.2.4 |
|17 |MME S1AP UE ID | MME S1 Application Protocol UE Identifier | Unique UE ID on S1-MME interface in MME | 32-bit integer | T | TS 23.401 5.2.5 |
|18 |IMEI | International Mobile Equipment Identity | Unique mobile equipment ID | TAC+SNR+CD 15 digits | P | TS 23.003 6.2.1, 6.3 |
|19 |IMEI/SV | IMEI / Software Version | IMEI software version | TAC+SNR+SVN 16 digits | P | TS 23.003 6.2.2, 6.3 |
|20 |ECGI | E-UTRAN Cell Global Identifier | Unique cell ID (globally) | PLMN+ECI <= 52 bits | P | TS 36.413 9.2.1.38 |
|21 |ECI | E-UTRAN Cell Identifier | Unique cell ID (in PLMN) | ECI(28bits)= eNB ID+Cell ID | P | TS 36.413 9.2.1.38 |
|22 |Global eNB ID | Global eNodeB Identifier | Unique eNodeB ID (globally) | PLMN+eNB ID | P | TS 36.413 9.2.1.37 |
|23 |eNB ID | eNodeB Identifier | Unique eNB (in PLMN) | 20 bits | P | TS 36.413 9.2.1.37 |
|24 |PGW ID | PDN Gateway Identity | Identifies PDN gateway HSS assigns PGW for UE context | IP Addr(4bytes) or, FQDN | P | TS 23.003, 19.1 19.2, 19.4, Anx E |
|25 |TAI | Tracking Area Identity | Unique tracking area (globally) | PLMN + TAC | P | TS 23.003 19.4.2.3 |
|26 |TAC | Tracking Area Code | Unique tracking area (in PLMN) Per cell in eNB | 16 bits | P | TS 23.003 19.4.2.3 |
|27 |TAI List | Tracking Area Identity List | UE can move to cells included in TAI List w/o location update | variable length | P | TS 24.301 9.9.3.33 |
|28 |PDN ID | Packet Data Network Identity | Access Point Name; determines PGW and PDN point of connection | APN.NI+APN.OI variable length | P | TS 23.003 9.1 |
|29 |EPS Bearer ID | Evolved Packet System Bearer Identity | Identifies EPS bearer per UE (default or dedicated) | 4 bits | T | TS 23.401 5.2.1 |
|30 |E-RAB ID | E-UTRAN Radio Access Bearer Identity | Identifies E-RAB per UE | 4 bits | T | TS 23.401 5.2.1 |
|31 |DRB ID | Data Radio Bearer Identifier | Identifies  D-RAB pwer UE | 4 bits | T | TS 36.331 4.4, 6.3.2 |
|32 |LBI | Linked EPS Bearer Identifier | IDs default bearer associated with dedicated EPS bearer | 4 bits | T | TS 23.401 5.4.1 5.4.5, 5.5.1.2.2 |
|33 |TEID | Tunnel End Point Identifier | Identifies end point of GTP tunnel when it is established | 32 bits | T | TS 29.060 3.1 |
 
 
## IMSI
国际移动用户识别码（IMSI：International Mobile Subscriber Identification Number）是区别移动用户的标志，储存在SIM卡中，可用于区别移动用户的有效信息。其总长度不超过15位，同样使用0～9的数字。其中MCC是移动 用户所属国家代号，占3位数字，中国的MCC规定为460；MNC是移动网号码，由两位或者三位数字组成，中国移动的移动网络编码（MNC）为00；用于识别移动用户所归属的移动通信网；MSIN是移动用户识别码，用以识别某一移动通信网中的移动用户。
国际上为唯一识别一个移动用户所分配的号码。
IMSI共有15位，其结构如下：
MCC+MNC+MSIN
 
 
## PLMN ID
Public Land Mobile Network ID，公共陆地移动网络ID, 由政府或它所批准的经营者，为公众提供陆地移动通信业务目的而建立和经营的网络标识。
PLMN = MCC + MNC，例如中国移动的PLMN为46000，中国联通的PLMN为46001。
 
## MCC
Mobile Country Code 移动设备国家代码
三个数字，如中国为460。
 
## MNC
移动设备网络代码（Mobile Network Code，MNC）是与移动设备国家代码（Mobile Country Code，MCC）（也称为“MCC / MNC”）相结合，以用来表示唯一一个的移动设备的网络运营商。由所在国家分配，通常2~3数字组成。
 
## MSIN
Mobile Subscriber Identification Number 移动用户识别号码
10-11位，其结构如下：
CC+M0M1M2M3+ABCD
CC由不同运营商分配，其中的M0M1M2M3和MDN号码中的H0H1H2H3可存在对应关系，ABCD四位为自由分配。
 
## GUTI
Globally Unique Temporary UE Identity，全球唯一临时UE标识。
在网络中唯一标识UE，可以减少IMSI、IMEI等用户私有参数暴露在网络传输中。GUTI由核心网分配，在attach accept, TAU accept, RAU accept等消息中带给UE。只有第一次attach时UE携带IMSI，而之后MME会将IMSI和GUTI进行一个对应，以后就一直用GUTI，通过 attachaccept带给UE.
在一个MME下，GUTI等同于M-TMSI。
GUTI由兩個部分組成，
一個是GUMMEI (Globally Unique Temporary UE Identity) ，一個是 M-TMSI(MME Temporary Mobile Subscriber Identity)
GUMMEI:GUMMEI是用作辨別 MME，代表用戶是在哪一個MME下面
M-TMSI:而M-TMSI是用戶在單一個MME底下的識別身分
因為這邊是Globally，所以可以想見，此ID必須要全球通用，可以想見，這個ID會有MCC(mobile Country Code)，MNC(Mobile Network Code)，以表示這個ID在什麼國家及營運商內。而MME Group ID則是將營運商的MME分不同的群組。而MME Code就是MME群組裡面的特定MME。加上M-TMSI則可以表示特定的用戶，雖然是暫時性的ID，但這是全球唯一的辨識!也就是說我是某一個MME，我能決定發給用戶的就是M-TMSI，GUMMEI則是事先都定義好的。這概念很類似子網域
 
## TIN
Temporary Identity used in Next update。在LTE网络中，TIN为UE保存的一个参数，标识了自己在下一次发送Attach Request/RAU/TAU Request时使用的临时ID（temporary ID）。临时ID可以是GUTI、P-TMSI或者RAT-related TMSI。
 
## S-TMSI
SAE-Temporary Mobile Subscriber Identit
S-TMSI = MMEC + M-TMSI
S-TMSI是临时UE识别号，MME分配S-TMSI,而在小区级识别RRC连接时，C-RNTI提供唯一的UE识别号。
如果多个 UE 的随机接入过程冲突，每个 UE 用自己的 s-TMSI 作为自己的竞争决议标识。如果 UE 没有得到 MME 分配的 s-TMSI，那么会指定一个 48 比 特的随机数作为 s-TMSI，并且作为随机接入过程中自己的竞争决议标识。
若是有Paging上的需要, 則會使用S-TMSI，S-TMSI則是由MME Code加上 M-TMSI所構成，也就是當要做Paging的時候。會paging到所有Cell(Tracking Area 內)，所以會由NAS paging S-TMSI到特定的MME之中。
 
## M-TMSI
MME-Temporary Mobile Subscriber Identity，MME临时用户标识， 唯一识别MME中的UE
 
## GUMMEI
Globally Unique MME Identity全球唯一的MME标识
GUMMEI (not more than 48 bits) = PLMN ID + MMEI
GUTI包含了GUMMEI
 
## MMEI
MME Identifier MME标识
由MME组标识(MME Group ID，简称MMEGI)和MME编号(MME Code，简称MMEC)组成。
MMEI (24 bits) = MMEGI + MMEC
 
## MMEGI
MME Group Identifier MME组标识
在一个PLMN内是唯一的。
16位组成。
 
## MMEC
MME Code  MME编号
一个MME Group中唯一MME编号
S-TMSI包含MMEC
8位组成
 
## C-RNTI
Cell- Radio Network Temporary Identifier 小区无线网络临时标识
在一个小区内用来唯一标识一个UE.
0x0001 ~ 0xFFF3 (16 bits)
 
## eNB S1AP UE ID
eNB S1 Application Protocol UE ID  UE在eNB侧S1接口上的唯一标识，由eNB分配。
 
## MME S1AP UE ID
MME S1 Application Protocol UE ID UE在MME侧S1接口上的唯一标识。
 
 
## IMEI
International Mobile Equipment Identity 移动设备国际身份码
是由15位数字组成的"电子串号"，它与每台手机一一对应，而且该码是全世界唯一的。每一部手机在组装完成后都将被赋予一个全球唯一的一组号码，这个号码从生产到交付使用都将被制造生产的厂商所记录。手机用户可以在手机中查到自己手机的IMEI码。
IMEI码由GSM（全球移动通信协会）统一分配，授权BABT（英国通信认证管理委员会）审受。
IMEI (15 digits) = TAC + SNR + CD
 
## IMEI/SV
IMEI/Software Version设备的软件版本号，同样改码是全球唯一的。
IMEI/SV (16 digits) = TAC + SNR + SVN
 
## ECGI
E-UTRAN Cell Global Identifier E-UTRAN小区全局标识符
ECGI由PLMN+Cell Identity组成，用于在PLMN中全局标识一个小区。
Cell Identity（小区标识）由EnodeB ID+Cell ID组成。其中包含28bit信息，前20bit表示EnodeB ID，后8bit表示Cell ID。
ECGI (not more than 52 bits) = PLMN ID+ ECI
 
## ECI
E-UTRAN Cell Identifier 演进通用陆地无线接入网络小区标识
在一个PLMN中是唯一的。
ECI (28 Bits) = eNB ID + Cell ID
 
##  Global eNB ID
Global eNodeB Identifier 全球eNodeB标识符
Global eNB ID (not more than 44 bits) = PLMN ID + eNB ID
 
## eNB ID
eNodeB Identifier 标识一个PLMN中的eNB
20 bits
 
## P-GW ID
PDN GW Identity
To identify a specific PDN GW (P-GW)
HSS assigns P-GW for PDN (IP network) connection of each UE
IP address (4 bytes) or FQDN (variable length)
 
## TAI
Tracking Area Identity  跟踪区标识
To identify Tracking Area 全球唯一
TAI (not more than 32 bits) = PLMN ID + TAC
 
 
## TAC
Tracking Area Code 跟踪区域码
定义小区所属的跟踪区域码，一个跟踪区域可以涵盖一个或多个小区
16 bits
 
跟踪区（Tracking Area）是LTE系统为UE的位置管理新设立的概念。当UE处于空闲状态时，核心网络能够知道UE所在的跟踪区，同时当处于空闲状态的UE需要被寻呼时，必须在UE所注册的跟踪区的所有小区进行寻呼。
TA是小区级的配置，多个小区可以配置相同的TA，且一个小区只能属于一个TA。
TAI是LTE的跟踪区标识（Tracking Area Identity），是由PLMN和TAC组成。
TAI = PLMN + TAC（Tracking Area Code）
 
多个TA组成一个TA列表，同时分配给一个UE，UE在该TA列表（TA List）内移动时不需要执行TA更新，以减少与网络的频繁交互；
当UE进入不在其所注册的TA列表中的新TA区域时，需要执行TA更新，MME给UE重新分配一组TA，新分配的TA也可包含原有TA列表中的一些TA；
每个小区只属于一个TA。
 
## TAI List
Tracking Area Identity List TA列表
多个TA组成一个TA列表，同时分配给一个UE，UE在该TA列表（TA List）内移动时不需要执行TA更新，以减少与网络的频繁交互。
{TAI} (variable length) TAI List
 
## PDN ID
Packet Data Network Identity 分组数据网标识
PDN Identity (APN) is used to determine the P-GW and point of interconnection with a PDN
APN(接入点名称)的值作为PDN网络的标识, PDN GW 位于 EPC 和 PDN 的边界.
 
## EPS Bearer ID
Evolved Packet System Bearer Identifier
UE到核心网的承载成为EPS bearer，，往下映射由基站与核心网间的S1 bearer和基站和终端间的Radio bearer构成。MME为每个EPS Bearer分配相应的EBI （EPS Bearer ID），构造相应的 Activate Dedicated EPS Bear消息，将其作为NAS PDU，包含在发送到eNodeB的Bear Setup Request 消息中。
4 bits
 
## E-RAB ID
E-UTRAN Radio Access Bearer Identifier 无线接入承载标识
 
## DRB ID
Data Radio Bearer Identifier 数据无线承载标识
 
## LBI
Linked EPS Bearer ID
被关联的默认承载的EPS Bearer ID，在激活专有承载时用到.
 
## TEID
Tunnel End Point identifier 隧道端点标识
To identify the end point of a GTP tunnel when the tunnel is established
当该GTP隧道建立后，发送节点向接收节点发送GTP消息时，该GTP消息头中将携带接收节点所分配的TEID值

