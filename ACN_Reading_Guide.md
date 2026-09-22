# CS G525 Advanced Computer Networks: Reading Guide and Exam Weights

**Source:** the six slide decks in `ACN/slides` + the topic lists he posted on Classroom for each Kurose chapter + the midsem post-mortem (three years of papers). Instructor: Vinayak Naik.

**Scope rule used here:** for Kurose Ch 4, 6 and 7 only the topics he listed on Classroom are in scope. The three standalone decks (Mobile IP, Wireless PHY: channel and capacity, Wireless PHY: modulation) had no topic list, so they are treated as **fully in scope**.

**Page numbers** are PDF page numbers (what your viewer shows), not the "6-41"-style footer numbers on the slides.

**Decks:** K4 = `Chapter_4_v9.0.pdf` · K6 = `Chapter_6_v9.0.pdf` · K7 = `Chapter_7_v9.0.pdf` · MIP = `mobile ip.pdf` · PHY2 = `wireless physical layer channel and capacity.pdf` · PHY3 = `lecture03-phy-modulation.pdf` (PHY2/PHY3 are Mythili Vutukuru's IIT Bombay CS 653 lectures).

---

## 0. Read this first

**Exams**
- **Midsem: Mon 05 Oct, 90 min, CLOSED book, 25%.** This is your *first* midsem: G526 is on 07 Oct and G623 on 08 Oct. ACN has to be finished by the weekend of 3–4 Oct.
- **Final: Tue 01 Dec, CLOSED book, 30%.** Adds MANET, SDN and security on top of this.

**How he examines (from the post-mortem, not guesswork)**
- **No definitions, ever.** Every question is a first-person admin scenario: "In my department…", "Why can't I…", "How do I stop…". You get marks for explaining *why a mechanism fails* and *what fixes it*.
- **~5 questions, ~30 marks, sub-parts of 2–3 marks.** About 3 minutes per mark, half a page per question.
- **Marking is additive, one mark block per named mechanism.** He writes the missing term in the margin ("hop count") and docks for it. Name the mechanism; don't just describe it.
- **"Mention the conditions under which your solution works."** He's asked for this outright twice and penalised it twice. Make it a reflex: every answer ends with a line of assumptions.
- **Draw.** Tuples, addresses and small topology sketches get credit (a switch-tree sketch has earned +4).

**What that means for reading.** You don't need to memorise slide text. For each mechanism, you need to be able to answer three questions: *what state does it keep, what breaks it, and what's the fix + its cost.* Every "Exam angle" below is written around that.

**His lecture order is fixed:** link layer → router internals → NAT → Mobile IP → physical layer → VLAN → MANET → SDN → security. The midsem cut has fallen after VLAN (24-25) or after MANET (25-26). **MANET is the swing topic** and you have no deck for it yet. If he covers OLSR/AODV before 05 Oct, get the slides immediately. In 25-26 it took 12 of 30 marks.

**Weights below are my guesses** from the three past papers plus how much time he spent on each topic. Use them for triage.

---

## 1. Midsem weight map (guesstimate)

| Block | Decks | Est. share of midsem | Why |
|---|---|---|---|
| A. Link layer: subnets, ARP, routing across subnets, switches | K6 | **~20%** | Link layer appeared in 3/3 papers. |
| B. Router internals + generalized forwarding | K4 | ~8% | 1/3 papers. OpenFlow is also compre material (SDN). |
| C. NAT | K4 | **~20%** | 3/3 midsems + both compres. His signature topic. |
| D. Wireless link layer: CSMA/CA, hidden terminal, 802.11 addressing | K7 | ~8% | Short topic list, but it's scenario-friendly ("why no CD in WiFi?"). |
| E. Mobile IP | MIP | ~12% | 2/3 papers. |
| F. Wireless PHY: channel, SNR, capacity | PHY2 | **~15%** | PHY in 2/3 midsems + both compres. The "why can't data rate keep rising" chain. |
| G. Modulation, OFDM, coherence | PHY3 | ~10% | OFDM/ISI half of the PHY question. Pure modulation (ASK/PSK/QAM) 0/3 so far. |
| H. VLAN + VXLAN | K6 | ~8% | Counted inside "link layer 3/3". VXLAN is new this year. |
| MANET (OLSR, AODV) | none yet | 0 or ~35% | Only if lectured before 05 Oct. |

**Priority order:** NAT = Link layer > PHY chain (F+G) > Mobile IP > VLAN/VXLAN > 802.11 > router internals > pure modulation.

---

## 2. Block by block

Each block: **Slides in scope** · **Topics** · **Read** · **Exam angle** (what questions will look like, in his style) · **Skip**.

### Block A: Link layer: subnets, ARP, routing across subnets, switches · midsem ~20%

**Slides in scope:** K6 p39–51 (MAC addresses, ARP, routing to another subnet), K6 p60–71 (switches, self-learning, interconnecting switches, UMass campus network, switches vs routers). Supporting: K4 p47–50 (subnets, CIDR) and K4 p59–61 (hierarchical addressing, route aggregation). These cover his listed topics "Subnet", "Network of Subnets" and "IP Addressing in a Structure".

**Topics (his list):** Subnet · Network of Subnets · ARP · Network Topology · Routing on a Tree/Hierarchical Structure · IP Addressing in a Structure · Routing across Subnets · Self-learning switch

- **Two addresses, two jobs.** IP (32-bit, hierarchical, *not* portable: depends on the subnet you're in) vs MAC (48-bit, flat, portable, only meaningful on one link). Know why each exists and what breaks if you only had one.
- **Subnet** = interfaces that can reach each other without a router. Same prefix = same subnet = ARP directly. Different prefix = send to gateway.
- **ARP:** broadcast query to FF-FF-FF-FF-FF-FF → unicast reply → cache with TTL (~20 min). Plug-and-play; no admin config.
- **Routing across subnets (K6 p46–51):** the core walk-through. The IP src/dst (A→B) stays **unchanged end to end**. The MAC src/dst changes **at every hop** (A→R's left interface, then R's right interface→B). A needs R's IP (from DHCP) and R's MAC (from ARP), *not* B's MAC.
- **Self-learning switch (K6 p63–66):** table of (MAC, interface, TTL). On a frame: learn the source, look up the destination → forward, filter (dest on the same segment → drop), or flood (unknown dest).
- **Network of switches (K6 p67–68):** self-learning works unchanged across a *tree* of switches. Be able to fill in all four switch tables for "C sends to I, I replies to C".
- **Hierarchy (K6 p69–70 + K4 p59–61):** campus = border → core → aggregation → building-closet L2 switches. Addresses assigned along the tree so that one prefix summarises a subtree (route aggregation). That's why routing tables stay small.
- **Switches vs routers (K6 p71):** both store-and-forward. Router: L3, table computed by routing algorithms. Switch: L2, table *learned* by flooding.

**Read**
- The K6 slides above, fully. Kurose Ch 6 sections "Link-Layer Addressing and ARP" and "Link-Layer Switches".
- Kurose Ch 4 "IPv4 Addressing" for subnets and CIDR only (skip DHCP detail unless you need a refresher on how a host learns its gateway).

**Exam angle**
- "Two hosts in my lab are on the same switch but different subnets. Why can't they ping each other directly? What must happen?" → gateway, ARP for the router's MAC, not the host's.
- "Trace the MAC and IP headers of a datagram from A to B via R" → table of (MAC src, MAC dst, IP src, IP dst) per hop. **Always draw this table.**
- "Why can't I build the whole campus as one giant switched LAN?" → broadcast (ARP/DHCP/flooding) crosses everything, switch tables grow with every host, **loops** in a non-tree topology make flooding circulate forever (switches need a tree, i.e. spanning tree), no aggregation. Conditions: hosts count, broadcast rate.
- "Why do switches not need configuration but routers do?" → self-learning vs routing protocol + address plan.
- "The switch table entry for a host expires / the host moves port. What happens?" → flood until relearned; TTL is why moves heal themselves.
- ARP failure modes: stale cache after a NIC change, gratuitous ARP, ARP spoofing (no authentication). Hint at spoofing if a question says "someone in my department is intercepting traffic".

**Skip (not on his list):** K6 p1–37 (intro, error detection/CRC, multiple access: ALOHA, CSMA/CD, taking-turns, cable). K6 p53–58 (Ethernet frame/standards). K6 p82–109 (MPLS, datacenter networks, "a day in the life", pure ALOHA). *Optional:* "a day in the life" p98–102 is a good ARP/DHCP revision if you have a spare 15 minutes.

**Interview hook:** the self-learning switch is a cache with TTL-based invalidation plus flood-on-miss: the same pattern as a service-discovery cache.

---

### Block B: Router internals + generalized forwarding · midsem ~8%

**Slides in scope:** K4 p2–8 (overview, data plane vs control plane, per-router vs SDN control), K4 p14–33 (router architecture, input ports, longest prefix match, switching fabrics, input/output queuing, buffering, buffer management), K4 p78–90 (match+action, flow tables, OpenFlow, middleboxes).

**Topics (his list):** network layer overview, data plane and control plane · inside a router: input/output ports, switching, buffer management · generalized forwarding: match+action, OpenFlow, middleboxes

- **Data plane vs control plane:** forwarding = per-router, nanoseconds, hardware. Routing = network-wide, seconds, software. Traditional: every router runs its own control plane. SDN: remote controller installs tables.
- **Input port:** line termination → link-layer processing → lookup ("match plus action") → queue. Goal: finish at **line speed**. **Longest prefix match** (p18–22): be able to do one by hand; know TCAM gives it in one clock cycle.
- **Switching fabrics (p23–28):** memory (bounded by memory bandwidth, 2 bus crossings per packet), bus (one packet at a time, bus speed limits it), interconnection network (crossbar/Clos, parallel, can fragment into fixed cells). Know the bottleneck of each.
- **Input queuing + HOL blocking (p29):** fabric slower than N×R → input queues. A packet stuck behind a head packet whose output is busy waits even though its own output is free.
- **Output queuing (p30–31):** fabric delivers faster than line rate R → output buffer → drop and scheduling decisions. His slide says "this is a really important slide".
- **How much buffer (p32):** RTT·C, or RTT·C/√N with N flows. Too much buffer → bufferbloat (long delays, sluggish TCP).
- **Buffer management (p33):** drop policy (tail drop, priority drop) and marking (ECN, RED).
- **Generalized forwarding (p78–87):** match on any L2/L3/L4 header fields → action (forward, drop, modify, send to controller) + counters. One abstraction gives you a router (match dst IP), a switch (match dst MAC), a firewall (match port 22 → drop) and NAT (match IP+port → rewrite). Be able to write the OpenFlow table rows for the examples on p82–86.
- **Middleboxes (p88–90):** anything on the path doing more than IP forwarding: NAT, firewall, IDS, load balancer, cache. Trend: proprietary boxes → whitebox + match+action, SDN, NFV.

**Read:** the slides. Kurose Ch 4 "What's Inside a Router?" and "Generalized Forwarding and SDN".

**Exam angle**
- "My router drops packets even though the outgoing link is idle. Why?" → HOL blocking at the input. Condition: the fabric is slower than N×R.
- "Should I just add more buffer to my home router?" → bufferbloat, RTT·C/√N. Condition: number of flows, delay-sensitive traffic.
- "Write the flow table entries to make this switch behave as a firewall / load balancer / NAT." → rows with wildcards + actions.
- "Which switching fabric would you pick for N ports at rate R, and why?" → bottleneck of each.

**Skip:** K4 p9–12 (service models; skim at most), p34–37 (packet scheduling: FCFS, priority, RR, WFQ: not on his list, but it's 4 slides, skim once), p38–40 (net neutrality), p42–62 (datagram format, addressing, DHCP: except the subnet slides used in Block A), p69–76 (IPv6, tunneling), p91–102 (IP hourglass, end-to-end argument, fragmentation, DHCP Wireshark).

---

### Block C: NAT · midsem ~20%

**Slides in scope:** K4 p64–68. Also his dedicated NAT Q&A session: your class notes are the densest source here. Use them.

**Topics:** network address translation

- **Mechanism:** every outgoing datagram's (private src IP, src port) is rewritten to (public NAT IP, new port). The pair goes into the NAT translation table. Incoming datagrams to (NAT IP, port) are looked up and rewritten back. Private ranges: 10/8, 172.16/12, 192.168/16.
- **Why it exists:** one ISP address for many hosts; renumber internally without telling anyone; change ISP without renumbering; hosts not directly addressable from outside.
- **Why it's controversial (p68):** a layer-3 box rewriting layer-4 ports (violates layering and the end-to-end argument); IPv6 was supposed to fix address shortage; **NAT traversal:** an outside client can't initiate a connection to a server behind NAT.
- **The table's state is the whole story.** An entry is created only by an *outbound* packet. Everything that fails with NAT fails because no entry exists yet, or because the entry doesn't match (port changed, timed out).
- **Fixes for traversal:** static port forwarding (manual entry), UPnP/IGD (host asks NAT to create an entry), relay (TURN-style: both sides go outbound to a public relay), hole punching (STUN-style: both sides send outbound at once so both NATs create entries). Know which work with which NAT type and what they cost.
- ~65k ports per public IP → the limit on concurrent flows. Carrier-grade NAT in 4G/5G = two layers of it.

**Read:** the slides + your class notes on the NAT Q&A. Kurose Ch 4 "Network Address Translation". For hole punching: Ford, Srisuresh & Kegel, "Peer-to-Peer Communication Across Network Address Translators" (USENIX ATC 2005), sections 1–3. It's readable in 30 min and is exactly the "why does P2P break" story.

**Exam angle** (this has appeared every single year)
- "Why can't my friend outside the hostel connect to the game server running on my laptop?" → no inbound entry. Fixes + conditions (static forwarding needs admin access to the NAT; hole punching needs a rendezvous server and a NAT that keeps the same external port).
- "Why does P2P / VoIP / FTP active mode break behind NAT?" → the peer's IP/port are inside the payload, which NAT doesn't rewrite, or the inbound connection has no entry.
- "Two hosts behind the same NAT both use source port 3345. What does the NAT do?" → assigns different external ports; the table disambiguates.
- "How many simultaneous connections can my department's NAT support? What breaks first?" → port space, table memory, timeouts.
- "Can two NATed hosts talk to each other directly?" → hole punching, and when it fails (symmetric NAT) → relay.
- **Always** give the table rows (WAN side ↔ LAN side) in your answer. That's the "concrete tuple" he rewards.

**Interview hook:** NAT = stateful connection-tracking proxy. Same reasoning as a load balancer's connection table or K8s kube-proxy's conntrack.

---

### Block D: Wireless link layer: elements, hidden terminal, CSMA/CA, 802.11 addressing · midsem ~8%

**Slides in scope:** K7 p6–12 (elements of a wireless network, link characteristics, edge vs core), K7 p23–24 (hidden terminal), K7 p59–64 (CSMA/CA, RTS/CTS), K7 p69–73 (802.11 link layer, frame, addressing).

**Topics (his list):** elements of wireless networks · CSMA/CA · hidden terminal · 802.11 frame addressing

- **Elements (p6–10):** wireless hosts (wireless ≠ mobile), base station/AP (relay between wired and wireless), wireless link (multiple access protocol), infrastructure mode vs **ad hoc** (no base station: this is the lead-in to MANET).
- **Hidden terminal (p23–24):** A and C both hear B but not each other, because of path loss (distance) or an obstacle. They collide *at B* without either knowing.
- **Why no collision *detection* in WiFi:** a radio can't hear while it transmits (own signal swamps the receiver), and even if it could, the collision happens at the receiver, not the sender (hidden terminal). So: avoid collisions, and use **ACKs** to find out.
- **CSMA/CA (p61–62):** sense idle for **DIFS** → send the whole frame. If busy → random backoff, count down only while idle. Receiver ACKs after **SIFS** (SIFS < DIFS, so the ACK always wins). No ACK → double the backoff range (binary exponential backoff).
- **RTS/CTS (p63–64):** short RTS reserves the channel. The AP's CTS is heard by *everyone in the AP's range*, including hidden terminals, and carries the duration, so they defer (NAV). RTS can still collide, but it's short, so a collision costs little.
- **802.11 addressing (p71–72):** 4 address fields. Addr1 = receiver of this frame (host or AP). Addr2 = transmitter (host or AP). Addr3 = the router interface the AP is attached to. Addr4 = ad hoc/WDS only. The AP is a **link-layer device**: it converts 802.11 ↔ 802.3 frames, and the router never learns the AP's MAC. Be able to draw the H1 → AP → R1 conversion: 802.11 (addr1 = AP, addr2 = H1, addr3 = R1) becomes 802.3 (dst = R1, src = H1). (If your copy of p72 shows "H2" as the 802.3 source, read it as H1.)
- **Frame control (p73):** type (management/control/data), to-AP/from-AP bits (which decide how addr1–3 are interpreted), retry, power management.

**Read:** the slides. Kurose Ch 7 sections on "WiFi: 802.11 wireless LANs" (MAC protocol and frame).

**Exam angle**
- "Why can't WiFi use CSMA/CD like my Ethernet does?" → can't listen while sending + collision is at the receiver (hidden terminal).
- "Two laptops at opposite ends of my lab both see an idle channel yet their throughput collapses. Why, and how do I fix it?" → hidden terminal, RTS/CTS. Conditions: the AP hears both; the fix costs overhead, so it's only worth it for large frames (RTS threshold).
- "Why does 802.11 need an ACK when Ethernet doesn't?"
- "Why does an 802.11 frame need three addresses when Ethernet has two?" → the AP is an intermediate L2 hop that isn't the final L2 destination.
- "Why is SIFS shorter than DIFS?"

**Skip:** K7 p1–5, p13–22 (radio basics, SNR, capacity: Block F covers these properly), p25–36 (multipath: *skim p25–26 for Block F*; MIMO, spectrum), p37–48 (coding/modulation: *skim p42–48 as a second view for Block G*), p49–58 (FDM/OFDM/OFDMA: *p52 is a useful OFDM picture*), p65–67 (backward compatibility, multi-user RTS), p74–154 (5G RAN, association/beaconing, scheduling, energy, 5G core, 5G mobility/handover, Bluetooth, satellite, IoT).

---

### Block E: Mobile IP · midsem ~12%

**Slides in scope:** MIP deck, **all of it** (p1–14).

**Topics**
- **Spectrum of mobility (p2):** same AP → reconnecting with DHCP (new IP, connections break) → moving across networks while *keeping connections alive*. Mobile IP is for the last one.
- **Vocabulary (p3–4):** home network, **permanent address**, **home agent (HA)**, visited network, **care-of address (COA)**, **foreign agent (FA)**, correspondent.
- **Approaches (p6–7):** (1) let routing handle it: routers advertise each mobile's permanent address → **not scalable to millions of mobiles**. (2) let end systems handle it: indirect or direct routing.
- **Registration (p8):** mobile → FA → HA ("this mobile lives in my network now"). End result: FA knows the mobile, HA knows its location.
- **Indirect routing (p9–11):** correspondent → permanent address → HA intercepts → tunnels to COA → FA → mobile. Mobile replies **directly** to the correspondent. **Triangle routing** is inefficient (worst when correspondent and mobile sit in the same visited network). Moving networks: register with the new FA, HA updates the COA → **ongoing connections survive, and the change is transparent to the correspondent.**
- **Direct routing (p12–13):** correspondent asks HA for the COA, sends straight to the FA. Fixes triangle routing, but it's **not transparent**: the correspondent must learn the COA, and what if the mobile moves again?
- **Anchor FA (p14):** keep sending to the first FA; each new FA chains forwarding from the old one.

**Read:** the deck. For depth, Kurose Ch 7 "Mobility management: principles" and "Mobile IP" (or the older 6th/7th-edition Ch 6 sections the deck came from). RFC 5944 intro if you want the real message names (agent advertisement, registration request/reply).

**Exam angle**
- "I'm on a video call while walking from the library WiFi to my hostel's network. Why does the call drop, and what would keep it alive?" → IP changes via DHCP, TCP connection is bound to the IP; Mobile IP keeps the permanent address.
- "Why not just let routers track every mobile?" → routing table size/updates don't scale.
- "My correspondent is in the same foreign network as the mobile but traffic goes through the home network. Why? Fix it, and what does the fix cost?" → triangle routing → direct routing → loss of transparency + the handoff problem → anchor FA.
- "What happens to packets in flight when the mobile moves from FA1 to FA2?" (indirect: HA retunnels to the new COA; direct: anchor FA chains).
- Conditions to state: HA reachable, the mobile has registered, ingress filtering might drop the mobile's replies (source = home address from a foreign network).

**Interview hook:** the HA is a stable indirection point in front of a moving endpoint, the same idea as a DNS name, a VIP or a Raft client redirecting to the current leader.

---

### Block F: Wireless PHY: propagation, SNR, bandwidth, capacity · midsem ~15%

**Slides in scope:** PHY2, **all of it** (p1–20). Cross-reference K7 p19–22 and p25–26 (SNR, capacity, path loss, multipath) as a second view.

**Topics**
- **Spectrum choice (p2–3):** antenna size ∝ wavelength (low frequency → big antenna); regulation/sharing; propagation (low frequencies travel far; high frequencies need line of sight). 2.4 GHz ISM is the compromise.
- **Antennas (p4):** isotropic, dipole, directional, sectorised, smart/arrays. Know the words, not the EE.
- **Analog vs digital modulation (p5–7):** digital survives distortion because you only need to tell 0 from 1.
- **Propagation (p8–10):** path loss (inverse square), reflection/diffraction/scattering, **multipath** (many delayed copies), **Doppler** (movement shifts frequency), thermal noise. Received y = h * x + n (convolution with the channel impulse response h). The receiver must estimate h and undo it: **channel equalization**. Long timescale: path loss. Short timescale: multipath fading.
- **SNR (p12):** SNR(dB) = 10·log₁₀(Ps/Pn). dB subtract (tx 50 dB − loss 30 dB = 20 dB received). dBm is relative to 1 mW (1000 mW = 30 dBm). There's a minimum SNR below which you can't decode.
- **How fast can you send (p13–17):** shorter symbols → higher rate, but **bandwidth ∝ 1/symbol duration**. Limits: your allocated band (go faster and you spill into the neighbour's channel) and hardware sampling (Nyquist: sample at ≥ 2f).
- **Shannon (p18–19):** C = B·log₂(1 + S/N), with S/N as a ratio, not dB. Example: 20 MHz, 30 dB (ratio 1000) → C ≈ 20M × 10 ≈ **200 Mbps**. (The slide says "SNR = 20 dB, so Ps/Pn = 1000": that's a typo; 30 dB = 1000.) C is an upper bound: R > C guarantees errors, but R < C doesn't guarantee success.

**Read:** the deck, twice. It's short. Vutukuru's CS 653 notes are the source if you want more. Kurose K7 p19–26 as a second pass.

**Exam angle** (his favourite PHY chain, with Block G)
- "Why can't I just keep increasing the data rate on my WiFi link?" → **bigger bandwidth → shorter symbol → symbol shorter than the multipath delay spread → ISI → errors/loss.** Plus Shannon bounds it by B and SNR, and B is limited by regulation/neighbouring channels. Resolution: multicarrier/OFDM (Block G). Name every link of the chain; each is a mark.
- "I doubled my transmit power. Did my capacity double?" → no, log₂(1 + SNR): +3 dB adds only ~1 bit/s/Hz at high SNR. Doubling **bandwidth** roughly doubles C (if SNR holds, but noise power grows with B too, so SNR drops a bit).
- Small dB/Shannon numericals are possible even though he hasn't set a pure numerical before: be fluent with 10/20/30 dB ↔ 10/100/1000 and log₂(1001) ≈ 10.
- "Why is the signal fine at 2 m but drops out as I walk around at 20 m?" → path loss (long-scale) vs multipath fading (short-scale).

---

### Block G: Modulation, multicarrier, OFDM, coherence · midsem ~10%

**Slides in scope:** PHY3, **all of it** (p1–15). Cross-reference K7 p42–48 (modulation, constellations, adaptive modulation) and K7 p52 (OFDM figure).

**Topics**
- **Carrier s = A·cos(2πft + φ):** modulate amplitude (ASK), frequency (FSK) or phase (PSK). **Constellation diagrams** as the compact notation (p4).
- **ASK (p3):** 2-ASK (0/1 or −1/+1), 4-ASK (2 bits/symbol). **FSK (p5):** rarely used, wastes bandwidth. **PSK (p6–7):** BPSK (= 2-ASK with ±1), QPSK (4 phases, 2 bits), **Gray coding** (neighbours differ by 1 bit → a symbol error costs 1 bit), 8-PSK inefficient, PSK needs **phase lock** → **DQPSK** encodes the phase *difference*, no phase lock needed.
- **QAM (p8):** amplitude + phase; QAM16/64. **Denser constellation → needs higher SNR.** That's why **adaptive modulation** (K7 p48) drops to a sparser constellation as SNR falls.
- **Single-carrier problem (p9):** if symbol duration > delay spread, all copies land in the same symbol → easy equalization. If symbol duration < delay spread → **ISI** → needs complex multi-tap equalization. Single-carrier forces a trade-off between rate and receiver complexity: bad for small mobile devices.
- **Multicarrier / OFDM (p10–12):** split the stream across N orthogonal subcarriers (each peaks where the others are zero), each sent *slowly* (symbol ≫ delay spread) → no ISI → **single-tap equalization** per subcarrier. WiFi uses 64 subcarriers. Transmitter is cheap thanks to **IFFT**; receiver uses FFT.
- **Frequency-domain view (p13):** large delay spread → channel frequency response H varies across the band (**frequency-selective**). Each OFDM subcarrier is narrow enough that H is ~flat over it → estimate one value per subcarrier.
- **Coherence bandwidth (p14):** ~1/delay spread. If it's smaller than the channel width (WiFi 20 MHz can be), the channel is frequency-selective → OFDM.
- **Coherence time (p15):** how long h stays constant; shrinks with Doppler (speed). Coherence time > packet → **slow fading** (indoor). < packet → **fast fading** (vehicular), channel changes mid-packet.

**Read:** the deck. K7 p42–48 for the constellations/error picture.

**Exam angle**
- The rest of the PHY chain from Block F: "…so how does WiFi get high rates without ISI?" → OFDM, slow per-subcarrier symbols, single-tap equalization, FFT makes it cheap. Condition: the subcarrier bandwidth must be below the coherence bandwidth.
- "My throughput drops as I move away from the AP even though I'm still connected. Why?" → SNR falls → adaptive modulation drops QAM64 → QAM16 → QPSK.
- "Why do my phone's packets fail in a moving car but not in the lecture hall?" → Doppler → short coherence time → fast fading → channel estimate at the packet start is stale by the end. Condition: packet duration vs coherence time.
- "Why DQPSK instead of QPSK?" → no phase lock needed.
- "Why is FSK rarely used?" → bandwidth.

---

### Block H: VLAN + VXLAN · midsem ~8%

**Slides in scope:** K6 p73–80.

**Topics (his list):** VLANs, VXLANs

- **Motivation (p73–74):** one big LAN = one **broadcast domain**: all ARP/DHCP/unknown-unicast flooding crosses everything (scaling, efficiency, security, privacy). Admin problem: a CS person moves to an office in EE but should stay logically in CS.
- **Port-based VLAN (p75–76):** switch management software groups ports; one physical switch acts as several virtual switches. Traffic isolation; membership can be by MAC instead of port; dynamic reassignment. **Forwarding between VLANs needs routing** (in practice a combined switch-router / L3 switch).
- **VLANs spanning switches (p77):** **trunk port** carries frames of all VLANs between switches. Frames on the trunk must carry the VLAN ID → **802.1Q**.
- **802.1Q frame (p78):** 4 bytes inserted after the source address: 2-byte TPID (0x8100) + TCI (12-bit **VLAN ID** → 4094 usable VLANs, 3-bit priority). CRC recomputed.
- **VXLAN / EVPN (p79–80):** stretch a layer-2 network over a layer-3 underlay (e.g. Sunnyvale ↔ Bangalore data centers). The **VTEP** encapsulates the Ethernet frame in UDP in IP (RFC 7348). 24-bit VNI → ~16M segments (vs 4094 VLANs). Hosts A and B think they're on the same LAN.

**Read:** the slides. Kurose Ch 6 "Virtual Local Area Networks". RFC 7348 §1–4 for VXLAN motivation and the header (short).

**Exam angle**
- "The CS and EE departments share one switch. How do I stop EE from seeing CS's broadcast traffic without buying another switch?" → port-based VLAN. Then: "Now how does a CS host reach an EE host?" → router / L3 switch between VLANs. Conditions: switch supports 802.1Q, the router has an interface (or sub-interface) per VLAN.
- "A professor moves offices across buildings but must stay on the CS network." → VLAN membership + trunk between the buildings' switches.
- "Why must frames on a trunk carry a tag, but frames to a host needn't?"
- "We have two data centers connected over the Internet and I want VMs to migrate between them without changing IP. What do I use, and what's the cost?" → VXLAN; cost = encapsulation overhead (~50 bytes → MTU issues), broadcast/unknown handling across sites, VTEP state. Why not VLAN? → 4094 limit, and VLANs don't cross an L3 network.

**Interview hook:** VXLAN is the default overlay under Kubernetes CNIs (Flannel VXLAN mode, Calico VXLAN) and cloud VPCs. A good line to have ready for backend/infra interviews.

---

## 3. What to skip (consolidated)

| Deck | Out of syllabus (PDF pages) |
|---|---|
| K4 | 9–12 (skim), 34–40, 42–62 (except subnet slides 47–50, 59–61), 69–76, 91–102 |
| K6 | 1–37, 52–58, 81–109 |
| K7 | 1–5, 13–22 (covered better by PHY2), 25–58 except the cross-references noted in Blocks F/G, 65–67, 74–154 |
| MIP, PHY2, PHY3 | nothing, all in scope |

Also from the post-mortem: Kurose Ch 3 and Ch 5 aren't taught. **No congestion control, no Dijkstra/DV traces, no throughput numericals** have appeared in any of his papers.

---

## 4. Answer template (closed book, so memorise the shape)

For every question part, aim for:
1. **Name the mechanism** in one line (the word he'd write in the margin).
2. **Why it fails / why it's needed:** the causal chain, one arrow per step.
3. **Fix + its cost.**
4. **A tuple, table or 4-node sketch** (NAT table rows, per-hop MAC/IP table, switch tables, 802.11 addr1–3).
5. **"Assumptions / conditions:"** one line. Every time.

---

## 5. Plan until 05 Oct (with G526 on 07 Oct and G623 on 08 Oct)

ACN comes first, so it gets the 3–4 Oct weekend. **This moves the G526 timed-practice block** from 2–4 Oct (as planned in the AAC guide) to 2 Oct + 5–6 Oct.

| Days | G525 (≈1–1.5 h/day on weekdays) | Notes |
|---|---|---|
| 22–24 Sep | Block C (NAT) + Block A (link layer) | Highest frequency. Build the per-hop MAC/IP table and the NAT table from memory. |
| 25–27 Sep | Blocks F + G (PHY chain) | Write the "why can't data rate keep rising" chain end to end, closed book. |
| 28–30 Sep | Block E (Mobile IP) + Block H (VLAN/VXLAN) | Plus MANET slides if he's started it. |
| 1 Oct | Blocks D + B (802.11, router internals) | Lighter topics. |
| 2 Oct | G526 timed practice (holiday) | ACN off except 30 min of flash recall. |
| 3–4 Oct | ACN: past papers 23-24, 24-25, 25-26 under time (90 min each), then compare against his red-pen marking | Answer every part with the §4 template. |
| 5 Oct | **Exam.** Evening: G526 | |

**Continuous components, don't forget:** weekly assignments (10%, no makeup), paper critique (15%, individual, two weeks) and group presentation (20%). Steer the critique and the presentation toward SDN, VXLAN/data-center overlays or congestion control: that's track-relevant reading that doubles as interview material.
