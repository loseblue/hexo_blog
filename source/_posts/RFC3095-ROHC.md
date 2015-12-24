title: RFC3095-ROHC
tags: [LTE, ROHC]
date: 2015-12-23 14:50:13
---

# 书名
RObust Header Compression (ROHC): Framework and four profiles: RTP, UDP, ESP, and uncompressed
 
# 第一阶段
## 分类
>确定书的种类和分类
 
RFC协议
 
<!--more-->

## 中心思想
>整本书在谈什么
 
Relevant information from past packets is maintained in a context.  The context information is used to compress (decompress) subsequent packets.
 
 
## 提纲
>按照顺序与关系，列出全书的重要部分。将全书的纲要拟出来之后，再将各部分的纲要也一一列出。
 
### 3. Background
#### 3.1. Header compression fundamentals
The main reason why header compression can be done at all is the fact that there is significant redundancy between header fields
Relevant information from past packets is maintained in a context.
The context information is used to compress (decompress) subsequent packets.
The compressor and decompressor update their contexts upon certain events.
 
#### 3.2. Existing header compression schemes
CTCP compresses the 40 octet IP+TCP header to 4 octets.
CRTP [CRTP, IPHC] is a header compression scheme that compresses 40 octets of IPv4/UDP/RTP headers to a minimum of 2 octets when the UDP Checksum is not enabled.  If the UDP Checksum is enabled, the minimum CRTP header is 4 octets.
CRTP uses explicit signaling messages from decompressor to compressor, called CONTEXT_STATE messages, to indicate that the context is out of sync.
 
#### 3.3. Requirements on a new header compression scheme
The major problem with CRTP is that it is not sufficiently robust against packets being damaged between compressor and decompressor.
 
#### 3.4. Classification of header fields
 
 
### 4. Header compression framework
 
#### 4.1. Operating assumption
**Channels**
    ROHC header-compressed packets flow on channels. Some cellular radio links can have several channels connecting the same pair of nodes.  Each channel can have different characteristics in terms of error rate, bandwidth, etc.
 
**Context identifiers**
    On some channels, the ability to transport multiple packet streams is required.
 
#### 4.2. Dynamicity
The ROHC protocol achieves its compression gain by establishing state information at both ends of the link
Some state information is established at the time a channel is established.
An example of negotiated channel state is the highest context ID number to be used by the compressor (MAX_CID).
 
#### 4.3. Compression and decompression states
one compressor machine and one decompressor machine, each instantiated once per context.
The compressor and the decompressor have three states each
Both machines start in the lowest compression state and transit gradually to higher states.
 
Transitions need **not** be synchronized between the two machines.  In normal operation it is only the compressor that temporarily transits back to lower states.  The decompressor will transit back only when context damage is detected.
 
##### 4.3.1. Compressor states
For ROHC compression, the three compressor states are the Initialization and Refresh (IR), First Order (FO), and Second Order (SO) states.
The compressor starts in the lowest compression state (IR) and transits gradually to higher compression states.
 
       +----------+                +----------+                +----------+
       | IR State |   <-------->   | FO State |   <-------->   | SO State |
       +----------+                +----------+                +----------+
 
Decisions about transitions between the various compression states are taken by the compressor on the basis of:
* variations in packet headers
* positive feedback from decompressor (Acknowledgments -- ACKs)
* negative feedback from decompressor (Negative ACKs -- NACKs)
* periodic timeouts (when operating in unidirectional mode, i.e., over simplex channels or when feedback is not enabled)
 
###### 4.3.1.1. Initialization and Refresh (IR) State
The purpose of the IR state is to initialize the static parts of the context at the decompressor or to recover after failure.
 
In this state, the compressor sends complete header information.  This includes all static and nonstatic fields in uncompressed form plus some additional information.
 
The compressor stays in the IR state until it is fairly confident that the decompressor has received the static information correctly.
 
###### 4.3.1.2. First Order (FO) State
Some or all packets sent in the FO state carry context updating information.
 
###### 4.3.1.3. Second Order (SO) State
The compressor enters the SO state when the header to be compressed is completely predictable given the SN (RTP Sequence Number) and the compressor is sufficiently confident that the decompressor has acquired all parameters of the functions from SN to other fields.
 
##### 4.3.2. Decompressor states
The decompressor starts in its lowest compression state, "No Context" and gradually transits to higher states.  The decompressor state machine normally never leaves the "Full Context" state once it has entered this state.
 
       +--------------+         +----------------+         +--------------+
       |  No Context  |  <--->  | Static Context |  <--->  | Full Context |
       +--------------+         +----------------+         +--------------+
 
 
1. Initially, while working in the "No Context" state, the decompressor has not yet successfully decompressed a packet.
2. Once a packet has been decompressed correctly, the decompressor can transit all the way to the "Full Context" state, and only upon repeated failures will it transit back to lower states.
3. There, reception of any packet sent in the FO state is normally sufficient to enable transition to the "Full Context" state again.
4. Only when decompression of several packets sent in the FO state fails in the "Static Context" state will the decompressor go all the way back to the "No Context" state.
 
 
#### 4.4. Modes of operation
The ROHC scheme has three modes of operation, called Unidirectional, Bidirectional Optimistic, and Bidirectional Reliable mode.
 
                         +----------------------+
                         |  Unidirectional Mode |
                         |   +--+  +--+  +--+   |
                         |   |IR|  |FO|  |SO|   |
                         |   +--+  +--+  +--+   |
                         +----------------------+
                           ^                  ^
                          /                    \
                         /                      \
                        v                        v
    +----------------------+                  +----------------------+
    |   Optimistic Mode    |                  |    Reliable Mode     |
    |   +--+  +--+  +--+   |                  |   +--+  +--+  +--+   |
    |   |IR|  |FO|  |SO|   | <--------------> |   |IR|  |FO|  |SO|   |
    |   +--+  +--+  +--+   |                  |   +--+  +--+  +--+   |
    +----------------------+                  +----------------------+
 
 
##### 4.4.1. Unidirectional mode -- U-mode
packets are sent in one direction only: from compressor to decompressor.
This mode therefore makes ROHC usable over links where a return path from decompressor to compressor is unavailable or undesirable.
Compression with ROHC **MUST** start in the Unidirectional mode.
Transition to any of the Bidirectional modes can be performed as soon as a packet has reached the decompressor and it has replied with a feedback packet indicating that a mode transition is desired
 
##### 4.4.2. Bidirectional Optimistic mode -- O-mode
The Bidirectional Optimistic mode is similar to the Unidirectional mode.  The difference is that a feedback channel is used to send error recovery requests and (optionally) acknowledgments of significant context updates from decompressor to compressor.
 
##### 4.4.3. Bidirectional Reliable mode -- R-mode
The most important differences are a more intensive usage of the feedback channel and a stricter logic at both the compressor and the decompressor.
 
 
#### 4.5. Encoding methods
abbr.
 
### 5. The protocol
#### 5.1. Data structures
##### 5.1.1. Per-channel parameters
* **MAX_CID**
Nonnegative integer; highest context ID number to be used by the compressor
* **LARGE_CIDS**
Boolean; if false, the short CID representation (0 bytes or 1 prefix byte, covering CID 0 to 15) is used; if true, the embedded CID representation (1 or 2 embedded CID bytes covering CID 0 to 16383) is used.
* **PROFILES**
Set of nonnegative integers, each integer indicating a profile supported by the decompressor.  The compressor **MUST NOT** compress using a profile not in PROFILES.
* **FEEDBACK_FOR**
Optional reference to a channel in the reverse direction.  If provided, this parameter indicates which channel any feedback sent on this channel refers to (see 5.7.6.1).
* **MRRU**
Maximum reconstructed reception unit.  This is the size of the largest reconstructed unit in octets that the decompressor is expected to reassemble from segments (see 5.2.5).  Note that this size includes the CRC.  If MRRU is negotiated to be 0, no segment headers are allowed on the channel.
 
##### 5.1.2. Per-context parameters, profiles
Per-context parameters are established with IR headers.
 
* **Profile 0x0000** is for sending uncompressed IP packets.
* **Profile 0x0001** is for RTP/UDP/IP compression.
* **Profile 0x0002** is for UDP/IP compression.
* **Profile 0x0003** is for ESP/IP compression. ESP:IPsec包
 
profile 0x0000 for CID 15).  Such state information can also be marked read-only in the negotiation, which would cause the decompressor to discard any IR packet attempting to modify it.
 
#### 5.1.3. Contexts and context identifiers
Associated with each compressed flow is a context, which is the state compressor and decompressor maintain in order to correctly compress or decompress the headers of the packet stream.
Contexts are identified by a context identifier, CID, which is sent along with compressed headers and feedback information.
The CID space is distinct for each channel.
The CID space can be negotiated to be either small 0 through 15, or large 0 and 2^14 - 1 = 16383. Whether the CID space is large or small is negotiated no later than when a channel is established.
 
### 5.2. ROHC packets and packet types
A ROHC packet has the following general format (in the diagram, colons ":" indicate that the part is optional):
 
        --- --- --- --- --- --- --- ---
       :           Padding             :  variable length
        --- --- --- --- --- --- --- ---
       :           Feedback            :  0 or more feedback elements
        --- --- --- --- --- --- --- ---
       :            Header             :  variable, with CID information
        --- --- --- --- --- --- --- ---
       :           Payload             :
        --- --- --- --- --- --- --- ---
 
Header is either a profile-specific header or an IR or IR-DYN header.  Header either
 
1. does not carry any CID information (indicating CID zero), or
2. includes one Add-CID Octet (see below), or
3. contains embedded CID information of length one or two octets.
 
Alternatives 1) and 2) apply only to compressed headers in channels where the CID space is small.  Alternative 3) applies only to compressed headers in channels where the CID space is large.
 
**Padding Octet**
 
         0   1   2   3   4   5   6   7
       +---+---+---+---+---+---+---+---+
       | 1   1   1   0   0   0   0   0 |
       +---+---+---+---+---+---+---+---+
 
**Add-CID Octet**
 
         0   1   2   3   4   5   6   7
       +---+---+---+---+---+---+---+---+
       | 1   1   1   0 |      CID      |
       +---+---+---+---+---+---+---+---+
 
CID:   0x1 through 0xF indicates CIDs 1 through 15.
 
**Note**: The Padding Octet looks like an Add-CID octet for CID 0.
 
All Header packet types have the following general format (in the diagram, slashes "/" indicate variable length):
 
         0              x-1  x       7
        --- --- --- --- --- --- --- ---
       :         Add-CID octet         :  if (CID 1-15) and (small CIDs)
       +---+--- --- --- ---+--- --- ---+
       | type indication   |   body    |  1 octet (8-x bits of body)
       +---+--- ---+---+---+--- --- ---+
       :                               :
       /    0, 1, or 2 octets of CID   /  1 or 2 octets if (large CIDs)
       :                               :
       +---+---+---+---+---+---+---+---+
       /             body              /  variable length
       +---+---+---+---+---+---+---+---+
 
#### 5.2.1. ROHC feedback
 
Feedback carries information from decompressor to compressor.
 
* **ACK**
* **NACK**
* **STATIC-NACK**
Indicates that the static context of the decompressor is not valid or has not been established.
 
#### 5.2.2. ROHC feedback format
 
Feedback sent on a ROHC channel consists of one or more concatenated feedback elements, where each feedback element has the following format:
 
 
         0   1   2   3   4   5   6   7
       +---+---+---+---+---+---+---+---+
       | 1   1   1   1   0 |   Code    |  feedback type octet
       +---+---+---+---+---+---+---+---+
       :             Size              :  if Code = 0
       +---+---+---+---+---+---+---+---+
       /         feedback data         /  variable length
       +---+---+---+---+---+---+---+---+
 
* Code: 0 indicates that a Size octet is present.  1-7 indicates the size of the feedback data field in octets.
* Size: Optional octet indicating the size of the feedback data field in octets.
* feedback data: Profile-specific feedback information.  Includes CID information.
 
The feedback data received by the compressor has the following structure (feedback sent on a dedicated feedback channel MAY also use this format):
 
         0   1   2   3   4   5   6   7
       +---+---+---+---+---+---+---+---+
       :         Add-CID octet         : if for small CIDs and (CID != 0)
       +---+---+---+---+---+---+---+---+
       :                               :
       /  large CID (4.5.6 encoding)   / 1-2 octets if for large CIDs
       :                               :
       +---+---+---+---+---+---+---+---+
       /           feedback            /
       +---+---+---+---+---+---+---+---+
 
 
 
It is REQUIRED that the feedback field have either of the following two formats:
 
1. FEEDBACK-1
 
         0   1   2   3   4   5   6   7
       +---+---+---+---+---+---+---+---+
       | profile specific information  |  1 octet
       +---+---+---+---+---+---+---+---+
 
2. FEEDBACK-2
 
         0   1   2   3   4   5   6   7
       +---+---+---+---+---+---+---+---+
       |Acktype|                       |
       +---+---+   profile specific    /  at least 2 octets
       /             information       |
       +---+---+---+---+---+---+---+---+
 
Acktype:
* 0 = ACK
* 1 = NACK
* 2 = STATIC-NACK
* 3 is reserved (MUST NOT be used.  Otherwise unparseable.)
 
 
#### 5.2.3. ROHC IR packet type
The IR header associates a CID with a profile, and typically also initializes the context.  It can typically also refresh (parts of) the context.  It has the following general format.
 
         0   1   2   3   4   5   6   7
        --- --- --- --- --- --- --- ---
       :         Add-CID octet         : if for small CIDs and (CID != 0)
       +---+---+---+---+---+---+---+---+
       | 1   1   1   1   1   1   0 | x | IR type octet
       +---+---+---+---+---+---+---+---+
       :                               :
       /      0-2 octets of CID        / 1-2 octets if for large CIDs
       :                               :
       +---+---+---+---+---+---+---+---+
       |            Profile            | 1 octet
       +---+---+---+---+---+---+---+---+
       |              CRC              | 1 octet
       +---+---+---+---+---+---+---+---+
       |                               |
       / profile specific information  / variable length
       |                               |
       +---+---+---+---+---+---+---+---+
 
x:  Profile specific information.  Interpreted according to theprofile indicated in the Profile field.
 
* **Profile**
The profile to be associated with the CID.  In the IR packet, the profile identifier is abbreviated to the 8 least significant bits.  It selects the highest-number profile in the channel state parameter PROFILES that matches the 8 LSBs given.
* **CRC**
8-bit CRC computed using the polynomial of section 5.9.1.  Its coverage is profile-dependent, but it **MUST** cover at least the initial part of the packet ending with the Profile field.  Any information which initializes the context of the decompressor should be protected by the CRC.
* **Profile specific information**
The contents of this part of the IR packet are defined by the individual profiles.  Interpreted according to the profile indicated in the Profile field.
 
#### 5.2.4. ROHC IR-DYN packet type
In contrast to the IR header, the IR-DYN header can never initialize an uninitialized context.  However, it can redefine what profile is associated with a context.
 
         0   1   2   3   4   5   6   7
        --- --- --- --- --- --- --- ---
       :         Add-CID octet         : if for small CIDs and (CID != 0)
       +---+---+---+---+---+---+---+---+
       | 1   1   1   1   1   0   0   0 | IR-DYN type octet
       +---+---+---+---+---+---+---+---+
       :                               :
       /      0-2 octets of CID        / 1-2 octets if for large CIDs
       :                               :
       +---+---+---+---+---+---+---+---+
       |            Profile            | 1 octet
       +---+---+---+---+---+---+---+---+
       |              CRC              | 1 octet
       +---+---+---+---+---+---+---+---+
       |                               |
       / profile specific information  / variable length
       |                               |
       +---+---+---+---+---+---+---+---+
 
* **Profile**
The profile to be associated with the CID.  This is abbreviated in the same way as with IR packets.
* **CRC**
8-bit CRC computed using the polynomial of section 5.9.1.  Its coverage is profile-dependent, but it **MUST** cover at least the initial part of the packet ending with the Profile field.  Any information which initializes the context of the decompressor should be protected by the CRC.
* **Profile specific information**
This part of the IR packet is defined by individual profiles.  It is interpreted according to the profile indicated in the Profile field.
 
#### 5.2.5. ROHC segmentation
##### 5.2.5.1. Segmentation usage considerations
ROHC segmentation should only be used for occasional packets with sizes larger than what is efficient to accommodate
 
##### 5.2.5.2. Segmentation protocol
Segment Packet
 
         0   1   2   3   4   5   6   7
       +---+---+---+---+---+---+---+---+
       | 1   1   1   1   1   1   1 | F |
       +---+---+---+---+---+---+---+---+
       /           Segment             /  variable length
       +---+---+---+---+---+---+---+---+
 
* **F**
Final bit.  If set, it indicates that this is the last segment of a reconstructed unit.
 
The segment header may be preceded by padding octets and/or feedback.  It never carries a CID.
 
All segment header packets for one reconstructed unit have to be sent consecutively on a channel, When a final segment header is received, the decompressor reassembles the segment carried in this packet and any nonfinal segments that immediately preceded it into a single reconstructed unit, in the order they were received.  The reconstructed unit has the format:
 
Reconstructed Unit
 
         0   1   2   3   4   5   6   7
       +---+---+---+---+---+---+---+---+
       |                               |
       /   Reconstructed ROHC packet   /  variable length
       |                               |
       +---+---+---+---+---+---+---+---+
       /              CRC              /  4 octets
       +---+---+---+---+---+---+---+---+
 
(abbr.)
 
#### 5.2.6. ROHC initial decompressor processing
The following packet types are reserved at the framework level in the ROHC scheme:
* 1110:     Padding or Add-CID octet
* 11110:    Feedback
* 11111000: IR-DYN packet
* 1111110:  IR packet
* 1111111:  Segment
 
#### 5.2.7. ROHC RTP packet formats from compressor to decompressor
ROHC RTP uses three packet types to identify compressed headers, and two for initialization/refresh.  The format of a compressed packet can depend on the mode.  Therefore a naming scheme of the form
 
`<modes format is used in>-<packet type number>-<some property>`
 
 
### 5.3. Operation in Unidirectional mode
#### 5.3.1. Compressor states and logic (U-mode)
**state machine**
 
                             Optimistic approach
          +------>------>------>------>------>------>------>------>------+
          |                                                              |
          |        Optimistic approach         Optimistic approach       |
          |      +------>------>------+      +------>------>------+      |
          |      |                    |      |                    |      |
          |      |                    v      |                    v      v
        +----------+                +----------+                +----------+
        | IR State |                | FO State |                | SO State |
        +----------+                +----------+                +----------+
          ^      ^                    |      ^                    |      |
          |      |      Timeout       |      |  Timeout / Update  |      |
          |      +------<------<------+      +------<------<------+      |
          |                                                              |
          |                           Timeout                            |
          +------<------<------<------<------<------<------<------<------+
 
 
##### 5.3.1.1. State transition logic (U-mode)
The transition logic for compression states in Unidirectional mode is based on three principles:
1. **Optimistic approach, upwards transition**
the optimistic approach principle
2. **Timeouts, downward transition**
3. **Need for updates, downward transition**
 
 
##### 5.3.1.2. Compression logic and packets used (U-mode)
##### 5.3.1.3. Feedback in Unidirectional mode
 
#### 5.3.2. Decompressor states and logic (U-mode)
                                     Success
                    +-->------>------>------>------>------>--+
                    |                                        |
        No Static   |            No Dynamic        Success   |    Success
         +-->--+    |             +-->--+      +--->----->---+    +-->--+
         |     |    |             |     |      |             |    |     |
         |     v    |             |     v      |             v    |     v
       +--------------+         +----------------+         +--------------+
       |  No Context  |         | Static Context |         | Full Context |
       +--------------+         +----------------+         +--------------+
          ^                         |        ^                         |
          | k_2 out of n_2 failures |        | k_1 out of n_1 failures |
          +-----<------<------<-----+        +-----<------<------<-----+
 
##### 5.3.2.1. State transition logic (U-mode)
##### 5.3.2.2. Decompression logic (U-mode)        
(abbr.)
##### 5.3.2.3. Feedback in Unidirectional mode
(abbr.)
 
### 5.4. Operation in Bidirectional Optimistic mode
#### 5.4.1. Compressor states and logic (O-mode)
 
 
                                Optimistic approach / ACK
         +------>------>------>------>------>------>------>------>------+
         |                                                              |
         |      Optimistic appr. / ACK      Optimistic appr. /ACK   ACK |
         |      +------>------>------+      +------>--- -->-----+  +->--+
         |      |                    |      |                   |  |    |
         |      |                    v      |                   v  |    v
       +----------+                +----------+                +----------+
       | IR State |                | FO State |                | SO State |
       +----------+                +----------+                +----------+
         ^      ^                    |      ^                    |      |
         |      |    STATIC-NACK     |      |    NACK / Update   |      |
         |      +------<------<------+      +------<------<------+      |
         |                                                              |
         |                         STATIC-NACK                          |
         +------<------<------<------<------<------<------<------<------+
 
##### 5.4.2. Decompressor states and logic (O-mode)
(abbr.)
 
### 5.5. Operation in Bidirectional Reliable mode
#### 5.5.1. Compressor states and logic (R-mode)
 
                                           ACK
          +------>------>------>------>------>------>------>------+
          |                                                       |
          |               ACK                         ACK         |   ACK
          |      +------>------>------+      +------>------>------+  +->-+
          |      |                    |      |                    |  |   |
          |      |                    v      |                    v  |   v
        +----------+                +----------+                +----------+
        | IR State |                | FO State |                | SO State |
        +----------+                +----------+                +----------+
          ^      ^                    |      ^                    |      |
          |      |    STATIC-NACK     |      |    NACK / Update   |      |
          |      +------<------<------+      +------<------<------+      |
          |                                                              |
          |                         STATIC-NACK                          |
          +------<------<------<------<------<------<------<------<------+
 
 
#### 5.5.2. Decompressor states and logic (R-mode)
(abbr.)
 
### 5.6. Mode transitions
 
                          +-------------------------+
                          | Unidirectional (U) mode |
                          +-------------------------+
                            / ^                 \ ^
                           / / Feedback(U)       \ \ Feedback(U)
                          / /                     \ \
                         / /                       \ \
            Feedback(O) / /             Feedback(R) \ \
                       v /                           v \
       +---------------------+    Feedback(R)    +-------------------+
       | Optimistic (O) mode | ----------------> | Reliable (R) mode |
       |                     | <---------------- |                   |
       +---------------------+    Feedback(O)    +-------------------+
 
 
#### 5.6.1. Compression and decompression during mode transitions
the compressor and decompressor maintain the current compression mode for that context.
all feedback sent during a mode transition **MUST** be protected by a CRC.  A mode transition **MUST NOT** be initiated by feedback which is not protected by a CRC.
 
#### 5.6.2. Transition from Unidirectional to Optimistic mode 
When there is a feedback channel available, the decompressor may at any moment decide to initiate transition from Unidirectional to Bidirectional Optimistic mode.
Any feedback packet carrying a CRC can be used with the mode parameter set to O. The decompressor can then directly start working in Optimistic mode. 
The compressor transits from Unidirectional to Optimistic mode as soon as it receives any feedback packet that has the mode parameter set to O and that passes the CRC check.

{% plantuml %}
hide footbox

title transition procedure
participant Compressor
    rnote over Decompressor
    D_MODE = O
    endrnote
Decompressor -> Compressor: ACK(O)/NACK(O)
    rnote over Compressor
    C_MODE = O
    endrnote
{% endplantuml %}
 
If the feedback packet is lost, the compressor will continue to work in Unidirectional mode, but as soon as any feedback packet reaches the compressor it will transit to Optimistic mode.

#### 5.6.3. From Optimistic to Reliable mode
Transition from Optimistic to Reliable mode is permitted only after at least one packet has been correctly decompressed, which means that at least the static part of the context is established.
An ACK(R) or a NACK(R) feedback packet carrying a CRC is sent to initiate the mode transition.  The compressor **MUST NOT** use packet types 0 or 1 during transition. 
 
{% plantuml %}
hide footbox

title transition procedure
participant Compressor
    rnote over Decompressor
    D_TRANS = I
    endrnote
Decompressor -> Compressor: ACK(R)/NACK(R)
    rnote over Compressor
    C_TRANS = P
    C_MODE = R
    endrnote
Compressor -> Decompressor: IR/IR-DYN/UOR-2(SN,R)
    rnote over Decompressor
    D_TRANS = P
    D_MODE = R
    endrnote
Decompressor -> Compressor: ACK(SN,R)
    rnote over Compressor
    C_TRANS = D
    endrnote
Compressor -> Decompressor: R-0*, R-1*
    rnote over Decompressor
    D_TRANS = D
    endrnote
{% endplantuml %} 
 
As long as the decompressor has not received an UOR-2, IR-DYN, or IR packet with the mode transition parameter set to R, it must stay in Optimistic mode.  The compressor **must not** send packet types 1 or 0 while C_TRANS is P, i.e., not until it has received an ACK for a UOR-2, IR-DYN, or IR packet sent with the mode transition parameter set to R.  When the decompressor receives packet types 0 or 1, after having ACKed an UOR-2, IR-DYN, or IR packet, it sets D_TRANS to D.
 
#### 5.6.4. From Unidirectional to Reliable mode
#### 5.6.5. From Reliable to Optimistic mode
#### 5.6.6. Transition to Unidirectional mode

### 5.7. Packet formats
(abbr.)

### 5.8. List compression
(abbr.)

### 5.9. Header compression CRCs, coverage and polynomials
(abbr.)

## 6. Implementation issues
(abbr.)
## 7. Security Considerations
(abbr.)
## 8. IANA Considerations
(abbr.)
## 10. Intellectual Property Right Claim Considerations
(abbr.)
## 11. References
(abbr.)
## 12. Authors' Addresses
(abbr.)
## 9. Acknowledgments


 
## 问题
>作者在问的问题，或者作者想要解决的问题。
 
 
# 第二阶段
## 关键字
>诠释作者使用的关键字，与作者达成共识
 
**forward and reverse channels** in 5.1.3
 
**Unidirectional mode the optimistic approach principle**
 
 
 
## 重要句子
>从重要的句子中抓出主旨
 
一个channel可以有多个stream, 既多个Context。通过context id来区分，不同channel间相同cid无关。
`Context identifiers: On some channels, the ability to transport multiple packet streams is required.`
 
 
## 重新架构
>找出作者的论述，重新架构这些论述的前因后果，以明白作者的主张。
 
 
## 确定问题
>确定作者已经解决了哪些问题，还有哪些问题是未解决的。在未解决的问题中，哪些是作者认为自己无法解决的。
 
 

