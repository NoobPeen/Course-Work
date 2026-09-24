# CS G525 Advanced Computer Networks: Reading Guide and Exam Weights

**Source:** the six slide decks in `ACN/slides` + the topic lists he posted on Classroom for each Kurose chapter + the midsem post-mortem (three years of papers) + the senior's Notion class notes + the three handout books. Instructor: Vinayak Naik.

**Updated 24 Sep:** added Block I (MANET), mobility inside a LAN / two joined networks / spanning tree / DHCP in Block A, tunnelling and the two VLAN past-paper questions in Block H, the handout-vs-lectures map in §0, and a book page map (§6). Every book page below was checked against the PDFs in `sem1/ACN/`.

**Scope rule used here:** for Kurose Ch 4, 6 and 7 only the topics he listed on Classroom are in scope. The three standalone decks (Mobile IP, Wireless PHY: channel and capacity, Wireless PHY: modulation) had no topic list, so they are treated as **fully in scope**.

**Page numbers** are PDF page numbers (what your viewer shows), not the "6-41"-style footer numbers on the slides.

**Decks:** K4 = `Chapter_4_v9.0.pdf` · K6 = `Chapter_6_v9.0.pdf` · K7 = `Chapter_7_v9.0.pdf` · MIP = `mobile ip.pdf` · PHY2 = `wireless physical layer channel and capacity.pdf` · PHY3 = `lecture03-phy-modulation.pdf` (PHY2/PHY3 are Mythili Vutukuru's IIT Bombay CS 653 lectures).

**Books:** K8e = Kurose & Ross 8e (the handout names 6e; 8e is the copy you have) · P&D = Peterson & Davie 5e · Tan = Tanenbaum 5e · Notion = `ACN/notion-page.pdf` (senior's 2024 class notes). **Book pages are printed page numbers**, the ones in the book's own header. To jump in your viewer: PDF page = printed + 11 (K8e), + 33 (P&D), + 24 (Tan). Notion pages are PDF pages.

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

**His lecture order is fixed:** link layer → router internals → NAT → Mobile IP → physical layer → VLAN → MANET → SDN → security. The midsem cut has fallen after VLAN (24-25) or after MANET (25-26). **MANET is the swing topic.** He has no deck for it (it's board-taught), but the Notion notes cover his lectures on it (Notion p24–42), and Block I below is built from them. If he starts OLSR/AODV before 05 Oct, Block I becomes top tier. In 25-26 it took 12 of 30 marks.

**What the handout says (and doesn't).** The handout has **no midsem syllabus**, only a 9-module course plan, and his real lecture order doesn't follow it. Map of plan → what's actually happened:

| Handout module | Where it lives in his lectures | Midsem status |
|---|---|---|
| 1–2 Intro, layered architecture | Folded into the link-layer and router lectures | Only as background |
| 3 Routing: LS, DV, hierarchical, DSR, AODV, DSDV, OLSR, ZRP | Hierarchical addressing → Block A. The rest → MANET (Block I) | Swing. **LS/DV as concepts only**: no traces in any paper |
| 4 Routing among mobile devices, global Internet, multicast, MPLS | Mobile IP → Block E. Multicast, BGP, MPLS: not lectured | Mobile IP in. Rest out |
| 5 TCP congestion control, QoS | Not lectured before the midsem in any year | Out (it has shown up in a compre) |
| 6 Wireless: LAN, PAN, sensor, ad hoc (12 lectures, "class notes") | 802.11 → D, PHY → F + G, ad hoc → I. PAN/Bluetooth and sensor networks: not lectured | In, except PAN/sensor |
| 7–9 SDN, wireless security, applications | After MANET in his order | Out: final only |

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
| I. MANET: routing taxonomy, OLSR, AODV | Notion p24–42 (no deck) | 0 or ~35% | Only if lectured before 05 Oct. M25: 12 of 30 marks. |

**Priority order:** NAT = Link layer > PHY chain (F+G) > Mobile IP > VLAN/VXLAN > 802.11 > router internals > pure modulation. **MANET (I) goes to the top tier the day he starts lecturing it.**

---

## 2. Block by block

Each block: **Slides in scope** · **Topics** · **Read** · **Exam angle** (what questions will look like, in his style) · **Skip**.

### Block A: Link layer: subnets, ARP, routing across subnets, switches · midsem ~20%

**Slides in scope:** K6 p39–51 (MAC addresses, ARP, routing to another subnet), K6 p60–71 (switches, self-learning, interconnecting switches, UMass campus network, switches vs routers). Supporting: K4 p47–50 (subnets, CIDR) and K4 p59–61 (hierarchical addressing, route aggregation). These cover his listed topics "Subnet", "Network of Subnets" and "IP Addressing in a Structure". Added: K4 p51–57 (DHCP, skim) and K7 p99–101 (beaconing and association: the re-association step when a host moves between APs).

**Topics (his list):** Subnet · Network of Subnets · ARP · Network Topology · Routing on a Tree/Hierarchical Structure · IP Addressing in a Structure · Routing across Subnets · Self-learning switch

- **Two addresses, two jobs.** IP (32-bit, hierarchical, *not* portable: depends on the subnet you're in) vs MAC (48-bit, flat, portable, only meaningful on one link). Know why each exists and what breaks if you only had one.
- **Subnet** = interfaces that can reach each other without a router. Same prefix = same subnet = ARP directly. Different prefix = send to gateway.
- **ARP:** broadcast query to FF-FF-FF-FF-FF-FF → unicast reply → cache with TTL (~20 min). Plug-and-play; no admin config.
- **Routing across subnets (K6 p46–51):** the core walk-through. The IP src/dst (A→B) stays **unchanged end to end**. The MAC src/dst changes **at every hop** (A→R's left interface, then R's right interface→B). A needs R's IP (from DHCP) and R's MAC (from ARP), *not* B's MAC.
- **Self-learning switch (K6 p63–66):** table of (MAC, interface, TTL). On a frame: learn the source, look up the destination → forward, filter (dest on the same segment → drop), or flood (unknown dest).
- **Network of switches (K6 p67–68):** self-learning works unchanged across a *tree* of switches. Be able to fill in all four switch tables for "C sends to I, I replies to C".
- **Hierarchy (K6 p69–70 + K4 p59–61):** campus = border → core → aggregation → building-closet L2 switches. Addresses assigned along the tree so that one prefix summarises a subtree (route aggregation). That's why routing tables stay small.
- **Switches vs routers (K6 p71):** both store-and-forward. Router: L3, table computed by routing algorithms. Switch: L2, table *learned* by flooding.
- **Mobility inside one LAN, with no router (M25 Q3, 6 marks; K8e §7.3.4 pp.556–558).** Not on any slide, but he set it. The host keeps its IP only if old and new positions are in the **same subnet and broadcast domain**, joined only by L2 devices (switches, APs). It scans beacons, re-associates with the new AP (K7 p100–101), and the switches relearn its MAC → new port. To make that instant, the new AP broadcasts a frame with the host's source MAC, or the host sends a **gratuitous ARP** (Tan p.469). **Condition he marks:** no router means **no Internet** from that setup. The grader wrote "No Internet" in the margin.
- **Two networks joined by a router (M25 Q5, 6 marks).** Different prefixes, separate broadcast domains. The router terminates the frame, does longest-prefix match, decrements TTL, ARPs for the next hop and builds a **new frame** with its own MAC as the source. It never passes L2 broadcasts across. To stop traffic between them: an **ACL / packet filter** on the router interface (in Block B's terms, a match on src/dst prefix → drop). Conditions: the router is the only path, and source addresses aren't spoofed.
- **Loops → spanning tree (P&D §3.1.4 pp.194–199; Tan §4.8.3 pp.337–340).** Not on his slides, but it's the named mechanism behind "self-learning only works on a tree". In a mesh, a flooded frame circulates forever and switch tables flap. Bridges elect a **root** (lowest ID). Each bridge keeps only its shortest-path port toward the root and one designated bridge per LAN, and blocks every other port. Cost: blocked links carry no traffic, and the tree must re-converge when a link fails. One sentence of this earns the "loops" mark.
- **DHCP, one line (K4 p51–57; K8e pp.341–344).** Discover → Offer → Request → ACK over UDP broadcast. It hands out the IP, mask, **default gateway** and DNS server, with a lease. It's how A learns R's IP in the walk-through above, and why moving to another subnet gives you a new IP (Block E).

**Read**
- The K6 slides above, fully. K8e §6.4.1 "Link-Layer Addressing and ARP" pp.478–484 and §6.4.3 "Link-Layer Switches" pp.491–497 (why switched LANs are restricted to a spanning tree: p.496).
- K8e §4.3.2 "IPv4 Addressing" pp.333–344 for subnets and CIDR (DHCP is pp.341–344: skim).
- K8e §7.3.4 "Mobility in the Same IP Subnet" pp.556–558. **Read this one: it answers M25 Q3 almost word for word.** p.558 adds that the same trick works across a VLAN spanning several buildings, which ties it to Block H.
- Second view: P&D §3.2.5–3.2.6 pp.220–231 (subnetting, ARP); P&D §3.1.4 pp.189–202 (learning bridges, spanning tree, limits of bridges). Tan §4.8.2 pp.334–337 (learning bridges); ARP and gratuitous ARP pp.467–469.

**Exam angle**
- "Two hosts in my lab are on the same switch but different subnets. Why can't they ping each other directly? What must happen?" → gateway, ARP for the router's MAC, not the host's.
- "Trace the MAC and IP headers of a datagram from A to B via R" → table of (MAC src, MAC dst, IP src, IP dst) per hop. **Always draw this table.**
- "Why can't I build the whole campus as one giant switched LAN?" → broadcast (ARP/DHCP/flooding) crosses everything, switch tables grow with every host, **loops** in a non-tree topology make flooding circulate forever (switches need a tree, i.e. spanning tree), no aggregation. Conditions: hosts count, broadcast rate.
- "Why do switches not need configuration but routers do?" → self-learning vs routing protocol + address plan.
- "The switch table entry for a host expires / the host moves port. What happens?" → flood until relearned; TTL is why moves heal themselves.
- ARP failure modes: stale cache after a NIC change, gratuitous ARP, ARP spoofing (no authentication). Hint at spoofing if a question says "someone in my department is intercepting traffic".
- **(M25 Q3)** "How does an organization provide mobility within the same network without a device that understands routing? (a) Conditions (b) Setup and protocol." → same subnet + broadcast domain, L2-only path, **no Internet**; switch tree sketch (it earned +4), self-learning relearns the port, gratuitous ARP / AP broadcast to make it immediate.
- **(M25 Q5)** "Two networks are joined. What differs between them? What does the joining device do? How do I stop traffic from one going into the other?" → prefixes + broadcast domains; LPM + TTL + re-ARP + new frame; ACL on the router. "The router doesn't allow it" with no mechanism scored 1 of 2.
- **(C23 Q6, compre)** "Even with infinite switch buffers, why does link-layer reliability drop as nodes increase?" → more flooding/broadcast as tables churn, **output-port contention**, host receive buffers overflow, more collisions on shared or wireless segments. Buffer size only fixes drops inside the switch.

**Skip (not on his list):** K6 p1–37 (intro, error detection/CRC, multiple access: ALOHA, CSMA/CD, taking-turns, cable). K6 p53–58 (Ethernet frame/standards). K6 p82–109 (MPLS, datacenter networks, "a day in the life", pure ALOHA). *Optional:* "a day in the life" p98–102 is a good ARP/DHCP revision if you have a spare 15 minutes. Also skip: spanning-tree configuration-message details (P&D pp.198–199; read pp.194–197 for the idea), RSTP, DHCP message formats and relay agents.

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
- *Low priority, not in any of his papers:* "My WiFi throughput collapses even though the network is idle." → bit-error losses look like congestion to TCP, so TCP shrinks its window. The fix is to hide the losses at the link layer: 802.11 ACK + retransmission (ARQ), FEC. Worth one line only because it ties Block D to the congestion-control material that shows up in the compres. K8e §7.7 pp.594–596; P&D p.504 sidebar; Tan §6.3.3 pp.539–540.

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
- "My throughput drops as I move away from the AP even though I'm still connected. Why?" → SNR falls → adaptive modulation drops QAM64 → QAM16 → QPSK. Name the trigger too: 802.11 **rate adaptation** falls back one rate after 2 missed ACKs and steps up after 10 ACKed frames in a row (K8e pp.559–560).
- "Why do my phone's packets fail in a moving car but not in the lecture hall?" → Doppler → short coherence time → fast fading → channel estimate at the packet start is stale by the end. Condition: packet duration vs coherence time.
- "Why DQPSK instead of QPSK?" → no phase lock needed.
- "Why is FSK rarely used?" → bandwidth.

---

### Block H: VLAN + VXLAN · midsem ~8%

**Slides in scope:** K6 p73–80. Added: K4 p71–74 (tunnelling: the logical vs physical view on p74 is the picture to reproduce).

**Topics (his list):** VLANs, VXLANs. Added: tunnelling in general, because VXLAN, Mobile IP's HA → COA leg and his "virtual topology" question are all the same mechanism.

- **Motivation (p73–74):** one big LAN = one **broadcast domain**: all ARP/DHCP/unknown-unicast flooding crosses everything (scaling, efficiency, security, privacy). Admin problem: a CS person moves to an office in EE but should stay logically in CS.
- **Port-based VLAN (p75–76):** switch management software groups ports; one physical switch acts as several virtual switches. Traffic isolation; membership can be by MAC instead of port; dynamic reassignment. **Forwarding between VLANs needs routing** (in practice a combined switch-router / L3 switch).
- **VLANs spanning switches (p77):** **trunk port** carries frames of all VLANs between switches. Frames on the trunk must carry the VLAN ID → **802.1Q**.
- **802.1Q frame (p78):** 4 bytes inserted after the source address: 2-byte TPID (0x8100) + TCI (12-bit **VLAN ID** → 4094 usable VLANs, 3-bit priority). CRC recomputed.
- **VXLAN / EVPN (p79–80):** stretch a layer-2 network over a layer-3 underlay (e.g. Sunnyvale ↔ Bangalore data centers). The **VTEP** encapsulates the Ethernet frame in UDP in IP (RFC 7348). 24-bit VNI → ~16M segments (vs 4094 VLANs). Hosts A and B think they're on the same LAN.
- **Tunnelling, the general pattern (K4 p71–74).** Put the whole inner packet (or frame) in the payload of an outer packet addressed endpoint to endpoint. Routers in the middle forward on the **outer header only**. The far endpoint decapsulates and sees the inner packet unchanged. The slide's case is IPv6 over IPv4. His word for it is a **"wormhole"**. Instances you already know: VXLAN (Ethernet in UDP/IP), Mobile IP (HA wraps the datagram in IP to the COA, P&D p.374), GRE. Cost every time: header overhead (→ MTU), per-endpoint state, and the middle of the network can't see or filter the inner header.
- **VLAN membership doesn't care about IP.** It's an L2 decision by port or MAC, so it can split hosts that no subnet mask can separate (C25 Q6 below).

**Read:** the slides. K8e §6.4.4 "Virtual Local Area Networks" pp.497–501 (802.1Q frame pp.499–500). K8e §4.3.4 tunnelling pp.351–353 (IPv6-over-IPv4, same figure as K4 p74). P&D §3.2.9 "Virtual Networks and Tunnels" pp.235–240 (the clearest general treatment). P&D "Virtual LANs" pp.201–202. Tan §4.8.5 "Virtual LANs" pp.342–349; Tan §5.5.3 "Tunneling" pp.429–431. RFC 7348 §1–4 for VXLAN motivation and the header (short).

**Exam angle**
- "The CS and EE departments share one switch. How do I stop EE from seeing CS's broadcast traffic without buying another switch?" → port-based VLAN. Then: "Now how does a CS host reach an EE host?" → router / L3 switch between VLANs. Conditions: switch supports 802.1Q, the router has an interface (or sub-interface) per VLAN.
- "A professor moves offices across buildings but must stay on the CS network." → VLAN membership + trunk between the buildings' switches.
- "Why must frames on a trunk carry a tag, but frames to a host needn't?"
- "We have two data centers connected over the Internet and I want VMs to migrate between them without changing IP. What do I use, and what's the cost?" → VXLAN; cost = encapsulation overhead (~50 bytes → MTU issues), broadcast/unknown handling across sites, VTEP state. Why not VLAN? → 4094 limit, and VLANs don't cross an L3 network.
- **(M24 Q1, 6 marks)** "Two privileged people need their data separated from everyone else on the same network. Encryption hides the payload but not the headers. How do you keep both private, and why does it work?" → their ports in their **own VLAN**, 802.1Q trunk if they're on different switches, no route (or an ACL'd one) to other VLANs. It works because the switch never delivers their frames, headers included, to other ports, and their broadcasts (ARP, DHCP) stay inside the VLAN. Conditions: managed 802.1Q switches, admin-controlled ports, trusted trunks (a tag isn't encryption). This scored "Excellent".
- **(C25 Q6, compre)** "Divide a subnet so that alternate IPs (…1.2, …1.4, …) belong to the same network. Condition?" → no mask can do it (masks group high-order bits; alternate addresses differ in the lowest bit) → **VLAN** by port. Conditions: VLAN-capable switches, the admin controls address assignment, and the two halves don't need to talk (both think the /24 is on-link, so cross-group ARP fails). The script that missed the condition got "Condition?" in the margin.
- **(C23 Q7, compre)** "Physical topology A — B — C — D. Make A and D virtual neighbours. Explain with packet headers." → tunnel: inner header (A → D) inside an outer header between the tunnel endpoints; B and C forward on the outer header; D decapsulates. Draw both headers.

**Skip:** K4 p69–70 and p75–76 (IPv6 datagram format, IPv6 adoption), IPv6 transition politics, VXLAN header fields beyond the 24-bit VNI, EVPN/BGP internals (one line is enough: BGP carries MAC reachability so the WAN doesn't flood-and-learn).

**Interview hook:** VXLAN is the default overlay under Kubernetes CNIs (Flannel VXLAN mode, Calico VXLAN) and cloud VPCs. A good line to have ready for backend/infra interviews.

---

### Block I: MANET: routing taxonomy, OLSR, AODV · midsem 0 or ~35% (swing)

**Slides in scope:** none. He teaches it on the board, right after VLAN. The record of his lectures is Notion p24–42: MANET intro p24–25, routing taxonomy and LS/DV p25–29, why MANET is hard p29–30, OLSR p31–35, AODV p36–42. **Status check:** if he hasn't started it by ~1 Oct, it's final-only. Drop it to one skim and spend the time on past papers.

**Topics (handout module 3 + his lectures):** why ad hoc routing is hard · source vs hop-by-hop vs virtual-circuit routing · link state vs distance vector (concepts and cost, **no traces**) · OLSR: HELLO, MPR set, MS set, TC messages · AODV: RREQ/RREP, broadcast ID, sequence numbers, reverse route · handout-only: DSR, DSDV, ZRP

- **Why it's hard (Notion p29; Tan p.389):** every node is both host and router, and the topology changes as nodes move, so routes die without warning. Wireless links vary, and there's **interference**: the packet B forwards to C competes with A's next packet to B. Many redundant links mean naive flooding repeats the same information. Batteries are limited, and eavesdropping and spoofing are easy. A protocol must **discover** a path, **maintain** it, and define how routing information is **exchanged**. **Proactive** (OLSR: routes always ready) vs **reactive** (AODV: routes found on demand).
- **His routing taxonomy (Notion p25–28):** **source routing** (the source picks the whole path, so it needs the full map), **hop-by-hop** (each node picks only the next hop), **virtual circuit** (path set up end to end in advance, state in every router, "very costly"). Link state floods link information so every node builds the full map: cost grows with the number of links, O(n²) when dense. Distance vector swaps a vector of at most N−1 distances with neighbours: O(N). **Pairing: LS ↔ Dijkstra, DV ↔ Bellman–Ford.** The Notion notes have these swapped. Don't copy that.
- **Plain link state is redundant (Notion p29–30):** if A and B are neighbours, both tell C about link A–B. OLSR makes two cuts: (1) only designated relays retransmit; (2) not every link is advertised.
- **OLSR HELLO (Notion p33):** a periodic one-hop broadcast listing your neighbours. **HELLOs are never forwarded.** From them each node learns its 2-hop neighbourhood.
- **MPR set (Notion p31–33):** MPR(N) = the smallest subset of N's 1-hop neighbours such that every 2-hop neighbour of N is a neighbour of some MPR. Several valid sets can exist, and each node computes its own. His graph (edges 4–1, 4–3, 4–5, 4–6, 1–2, 3–2, 6–7): MPR(4) = {3, 6} or {1, 6}. {3} alone misses 7, {6} alone misses 2, {1, 5} fails because 5 doesn't reach 7.
- **MS set (Notion p33–34):** MS(X) = the nodes that chose X as their MPR. You compute the MPRs first, then the MS sets ("second-order information").
- **TC messages (Notion p34–35):** carry the advertised neighbours (= the MS set) plus a **sequence number** so stale topology is ignored. A node with an **empty MS set sends no TC**. A node retransmits a TC only if the node it got it from **selected it as an MPR**. (The Notion notes state this backwards.) Every node *processes* every TC.
- **AODV (Notion p36–42; Tan pp.389–392):** reactive and hop-by-hop. Nodes off active paths keep nothing. Each node keeps two counters: a **sequence number** and a **broadcast ID**. The RREQ carries ⟨src, src seq, broadcast ID, dst, dst seq, hop count⟩ and is flooded. **(src, broadcast ID)** identifies duplicates, which are dropped. Each hop records a **reverse route** to the source. The destination, or an intermediate node with a route at least as fresh, unicasts a **RREP** back along the reverse route, and each hop sets the forward route as the RREP passes. **Reverse route first, forward route second.**
- **The route-choice rule he marks:** **higher sequence number wins, and hop count only breaks ties.** The destination sequence number works like a logical clock and stops distance vector's count-to-infinity confusion between old and new routes (Tan p.391).
- **Maintenance (Tan p.391):** a missed HELLO or a failed forward marks the link dead. Routes through it are purged, active neighbours are told, and the source rediscovers. Expanding-ring search limits floods: RREQ with TTL 1, then 2, 3, …
- **Handout-only, in no paper so far:** **DSR** is reactive *source* routing (the whole path rides in the header and is cached). **DSDV** is proactive DV with destination sequence numbers (AODV's ancestor). **ZRP** is hybrid (proactive inside a zone of radius r, reactive beyond it). One line each.

**Read**
- Notion p24–42 first: it's the only record of how *he* taught it. Then `ACN_Textbook` Ch 9, which has the Notion errors fixed and the worked MPR/MS sets.
- **Tan §5.2.11 "Routing in Ad Hoc Networks" pp.389–392: the one book section to read.** It covers AODV discovery (Fig. 5-20, p.390), maintenance and destination sequence numbers (p.391), and route sharing plus DSR (p.392).
- P&D p.378, sidebar "Mobile Ad Hoc Networks": one page on proactive vs reactive, OLSR as optimised OSPF, AODV as on-demand DV.
- Background, concepts only: Tan §5.2.3 flooding pp.368–370, §5.2.4 distance vector pp.370–373 (count-to-infinity p.372), §5.2.5 link state pp.373–378. Or K8e §5.2.1–5.2.2 pp.383–395 (count-to-infinity pp.393–395). P&D §3.1.2–3.1.3 pp.174–189 for virtual circuits vs source routing, his three-way taxonomy.
- K8e pp.534–535 and K7 p9–10: where ad hoc/MANET sits in the wireless taxonomy. One glance.
- **OLSR is not in any of the three books** beyond P&D's paragraph. For field names: RFC 3626 §3 (MPR, HELLO, TC). RFC 3561 §§1–6 for AODV.

**Exam angle** (M25 gave this block 12 of 30 marks)
- **(M25 Q1)** "In OLSR: (a) when will a node not generate a TC message? (b) when will it not mention a link in its TC? (c) the MPR set of each node for A — B." → (a) empty MS set; (b) the neighbour isn't in its MS set; (c) MPR(A) = MPR(B) = ∅: no 2-hop neighbours, so no TCs at all.
- **(C24 Q3, compre)** "When is a node's MS set empty? When is its MPR set empty?" → MS empty: nobody needs it to reach their 2-hop neighbours (a leaf). MPR empty: no 2-hop neighbours: two nodes, an isolated node, or a **clique**. The script that missed the clique got 1 of 2.
- **(M25 Q2)** "Two RREQs with the same broadcast ID but different source sequence numbers: how is the route to the source decided? Same broadcast ID and same sequence number?" → higher source seq = fresher reverse route, drop the other. Equal → it's a duplicate over two paths: rebroadcast once, keep the **fewer-hops** copy. The script that left out "hop count" got 1.5 of 3.
- **(C24 Q3(c) / C25 Q4)** "A node gets an RREQ whose destination sequence number is lower than the one it holds." → its route is fresher, so it answers with an RREP itself (if the route is still active). "Why sequence numbers in the RREQ?" → reject stale routes and loops; mark freshness of the reverse route.
- Likely in his style: "After an earthquake I deploy 30 radios with no infrastructure. Traffic is occasional and teams keep moving. OLSR or AODV, and when would you switch?" → AODV (no standing overhead, fresh routes on demand). Switch to OLSR when traffic becomes frequent and many-to-many and discovery latency dominates. Conditions: symmetric links, every node in range of at least one neighbour.

**Skip:** DV/LS **traces** (Dijkstra tables, Bellman–Ford iterations: none in any paper), OSPF/BGP (K8e §5.3–5.4) and RIP/OSPF message formats (P&D §3.3.2–3.3.3), hierarchical/broadcast/multicast/anycast routing (Tan §5.2.6–5.2.9 pp.378–386), GPSR (Tan p.392), RFC packet formats.

**Interview hook:** AODV's "freshest sequence number wins, then shortest" is the same rule as Raft terms and Lamport clocks: a newer epoch beats a better-looking stale claim. OLSR's MPRs are fan-out reduction, the same idea as relay selection in gossip protocols.

---

## 3. What to skip (consolidated)

| Deck | Out of syllabus (PDF pages) |
|---|---|
| K4 | 9–12 (skim), 34–40, 42–62 (except subnet slides 47–50, DHCP 51–57 as a skim, 59–61), 69–70, 75–76, 91–102. **71–74 (tunnelling) is now in: Block H** |
| K6 | 1–37, 52–58, 81–109 |
| K7 | 1–5, 13–22 (covered better by PHY2), 25–58 except the cross-references noted in Blocks F/G, 65–67, 74–98, 102–154. **99–101 (beaconing, association) is now in: Block A** |
| MIP, PHY2, PHY3 | nothing, all in scope |
| Notion | p42–68 (SDN, security, SSL: post-midsem). Keep p1–42 |
| K8e (book) | Ch 2, Ch 3 (not taught), Ch 5 except the §5.2 concepts for Block I, §6.2–6.3 (error detection, multiple access), §6.5–6.6 (MPLS, data centres), §7.4 and §7.6.1 (4G/5G), §7.3.6 (Bluetooth), Ch 8 (security: post-midsem) |
| P&D (book) | Ch 2 except §2.7 wireless and the Shannon–Hartley bit on pp.74–75, Ch 4.1–4.3 (global Internet, multicast, MPLS: handout module 4, never lectured), Ch 5–6 (transport, congestion: not taught) |
| Tan (book) | Use only the sections §6 lists. Skip §5.2.6–5.2.9 (hierarchical, broadcast, multicast, anycast routing) and §5.3–5.4 (congestion control, QoS) |

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
| 28–30 Sep | Block E (Mobile IP) + Block H (VLAN/VXLAN + tunnelling) | Plus Block I (MANET) from the Notion notes + Tan pp.389–392 if he's started it. Do the MPR/MS sets for his 7-node graph by hand. |
| 1 Oct | Blocks D + B (802.11, router internals) | Lighter topics. |
| 2 Oct | G526 timed practice (holiday) | ACN off except 30 min of flash recall. |
| 3–4 Oct | ACN: past papers 23-24, 24-25, 25-26 under time (90 min each), then compare against his red-pen marking | Answer every part with the §4 template. |
| 5 Oct | **Exam.** Evening: G526 | |

**Continuous components, don't forget:** weekly assignments (10%, no makeup), paper critique (15%, individual, two weeks) and group presentation (20%). Steer the critique and the presentation toward SDN, VXLAN/data-center overlays or congestion control: that's track-relevant reading that doubles as interview material.

---

## 6. Book page map (all blocks)

The slides are the syllabus; the books are for when a slide is too terse to answer a "why". **Priority** = what to open first if you only have time for one book per block. Printed pages; add 11 (K8e), 33 (P&D) or 24 (Tan) to get the PDF page.

| Block | Read first | Second view | Skip in the books |
|---|---|---|---|
| A. Link layer | K8e §6.4.1 pp.478–484 (ARP); §6.4.3 pp.491–497 (switches); §7.3.4 pp.556–558 (mobility in one subnet) | K8e §4.3.2 pp.333–344 (subnets, CIDR; DHCP pp.341–344). P&D §3.1.4 pp.189–202 (spanning tree pp.194–197); §3.2.5–3.2.6 pp.220–231. Tan §4.8.2–4.8.3 pp.334–340; ARP pp.467–469 | K8e §6.4.2 Ethernet pp.484–491; P&D pp.198–199 (STP message details) |
| B. Router internals | K8e §4.2 pp.311–325 (TCAM p.316, fabrics pp.317–319, queueing pp.319–324, bufferbloat p.324); §4.4 pp.353–360; §4.5 pp.360–364 | K8e §4.1.1 pp.304–309. P&D §3.4 pp.267–280 (HOL blocking p.272, fabrics p.273) | K8e §4.2.5 scheduling pp.325–330 (skim once at most) |
| C. NAT | K8e §4.3.3 pp.344–346 | Tan "NAT" pp.451–454. P&D pp.335–336 | K8e §4.3.4 IPv6 header pp.347–351 |
| D. 802.11 | K8e §7.2 pp.536–539 (hidden terminal pp.538–539); §7.3.2 pp.548–553 (CSMA/CA, RTS/CTS); §7.3.3 pp.553–556 (addresses) | P&D §2.7 pp.128–141 (hidden node p.137, CSMA/CA p.138). Tan §4.4.3 pp.303–309 (hidden/exposed p.305) | K8e §7.3.5–7.3.6 pp.559–563, §7.4 pp.563–578 |
| E. Mobile IP | K8e §7.5 pp.578–587 (triangle routing p.585); §7.6.2 pp.592–594 | P&D §4.4 pp.369–379 (Mobile IP pp.372–379; HA's gratuitous ARP + tunnel p.374). Tan §5.2.10 pp.386–389 | K8e §7.6.1 4G/5G mobility pp.587–592 |
| F. PHY: channel, capacity | the PHY2 deck (no book matches his treatment) | K8e §7.2 pp.536–539. Tan §2.1 pp.90–95 (Nyquist and Shannon p.94); §2.3 pp.105–110. P&D pp.74–75 (Shannon–Hartley) | Tan §2.2 guided media, §2.4+ satellites and telephone system |
| G. Modulation, OFDM | the PHY3 deck | Tan §2.5.2 pp.130–132 (ASK/FSK/PSK, QPSK/QAM); §2.5.3 pp.132–133 (the OFDM part of FDM); §4.4.2 pp.301–303 (802.11 PHY). K8e pp.537–538 (BER vs SNR); p.571 (OFDM in LTE). P&D p.135 | Tan §2.5.1 baseband and line codes pp.125–130; §2.5.4–2.5.5 TDM/CDM |
| H. VLAN, VXLAN, tunnels | K8e §6.4.4 pp.497–501 (802.1Q pp.499–500); P&D §3.2.9 pp.235–240 (tunnels) | K8e pp.351–353 (IPv6-over-IPv4 tunnel). P&D pp.201–202. Tan §4.8.5 pp.342–349; §5.5.3 pp.429–431 | K8e §6.5 MPLS pp.501–505; P&D §4.3 MPLS |
| I. MANET | Tan §5.2.11 pp.389–392 | P&D p.378 (sidebar). Tan §5.2.3–5.2.5 pp.368–378 (concepts). K8e §5.2 pp.380–395 (concepts). P&D §3.1.2–3.1.3 pp.174–189 | All traces; Tan §5.2.6–5.2.9; GPSR p.392 |
