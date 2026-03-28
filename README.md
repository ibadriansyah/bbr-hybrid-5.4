BBR-Hybrid
==========

Enhanced TCP based on Google's BBR & BBRplus — tuned for online gaming and real-time traffic on mobile SoCs. Targets a stable flat-line ping by aggressively managing bufferbloat and RTT jitter.

Key Gaming Enhancements
----------------------

### Jitter
**RTT Spike Detection**  
Detects sudden latency jumps. Temporarily suppresses bandwidth probing to prevent compounding lag.

### Probe RTT
**Reduced Probing Intervals**  
Faster recovery and minimal ping bumps during RTT probing.  
Interval: 5s → 2.5s  
Duration: 200ms → 100ms

### Pacing
**Tighter Pacing Cycle**  
Conservative gain cycle reduces packet bursts that cause jitter on Wi-Fi and LTE.

### Startup
**Conservative Startup**  
Exits STARTUP phase earlier to prevent buffer stuffing at game session start.  
Threshold: 1.25x → 1.15x

### Big.LITTLE
**Big.LITTLE Optimization**  
Adaptive drain logic designed for the scheduling characteristics of modern mobile SoC processors like Xiaomi’s Redwood.

Technical Modifications
----------------------

- **inet_connection_sock.h**  
  Increased icsk_ca_priv size from 104 → 160 bytes to accommodate the larger BBRHybrid state structure.

- **tcp.h**  
  Added tso_segs_goal function pointer to congestion control ops for better TSO (TCP Segmentation Offload) control.

- **Kconfig**  
  BBRHybrid set as default TCP congestion control. CUBIC moved to a module.

Installation
------------

**Step 1 — Apply the Patch**
------
cd /path/to/kernel-root git apply path/to/bbr-hybrid.patch
**# OR **
patch -p1 < bbrhybrid.patch
------
**Step 2 — Defconfig Requirements**
CONFIG_TCP_CONG_BBRHYBRID = y CONFIG_DEFAULT_BBRHYBRID = y CONFIG_NET_SCH_FQ = y


> Like standard BBR, BBRHybrid requires the fq (Fair Queue) pacing packet scheduler to function correctly.

Performance Targets
-------------------

- **Lower Jitter** — Reduced RTT variance under high network load  
- **Faster Recovery** — Quicker adaptation to changing mobile signal  
- **Anti-Bufferbloat** — Keeps pipe full without overfilling buffers

Ping (ms) → target behavior:

Standard: fluctuating  
BBR-Hybrid: stable flat-line

Credits
-------

Original BBR — Google (Neal Cardwell, Yuchung Cheng et al.)  
BBRplus — dgq250 & cx9208" />
