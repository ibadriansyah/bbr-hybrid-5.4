# BBR-Hybrid TCP for Android Kernel

BBRHybrid is an enhanced TCP congestion control algorithm based on Google's BBR and BBRplus. This version is specifically tuned for online gaming and real-time interactive traffic on mobile SoCs (like Xiaomi's redwood).

It aims to provide a stable "flat-line" ping by aggressively managing bufferbloat and detecting RTT jitter before it affects gameplay.
# 🚀 Key Gaming Enhancements

Compared to standard BBR/BBRplus, this implementation introduces:

    RTT Spike Detection: Automatically detects sudden latency jumps (jitter). When a spike is detected, it temporarily suppresses bandwidth probing to prevent making the lag worse.

    Reduced PROBE_RTT:

        Interval reduced from 5s to 2.5s for faster recovery.

        Duration reduced from 200ms to 100ms to minimize the "ping bump" during RTT probing.

    Tighter Pacing Cycle: Uses a more conservative gain cycle { 1.125, 0.875, 1, 1, 1, 1, 1, 1 } to reduce packet bursts that cause jitter on Wi-Fi and LTE.

    Conservative Startup: Exits the STARTUP phase earlier (1.15x threshold vs 1.25x) to prevent initial buffer stuffing when a game session starts.

    Big.LITTLE Optimization: Adaptive drain logic designed to handle the scheduling characteristics of modern mobile processors.

# 🛠 Technical Modifications

This patch includes critical kernel-level changes:

    inet_connection_sock.h: Increased icsk_ca_priv size from 104 to 160 bytes to accommodate the larger BBRHybrid state structure.

    tcp.h: Added tso_segs_goal function pointer to the congestion control ops for better TSO (TCP Segmentation Offload) control.

    Kconfig: BBRHybrid is set as the default TCP congestion control and CUBIC is moved to a module.

# 📦 Installation
1. Apply the Patch

Navigate to your kernel root directory and apply the changes:
Bash

git apply path/to/bbr-hybrid.patch

2. Defconfig Requirements

Ensure your defconfig includes the following for optimal performance:
Cuplikan kode

CONFIG_TCP_CONG_BBRHYBRID=y
CONFIG_DEFAULT_BBRHYBRID=y
CONFIG_NET_SCH_FQ=y

Note: Like standard BBR, BBRHybrid requires the fq (Fair Queue) pacing packet scheduler.
📈 Performance Targets

    Lower Jitter: Reduced variance in RTT during high network load.

    Faster Recovery: Quicker adaptation to changing signal strengths on mobile data.

    Anti-Bufferbloat: Keeps the "pipe" full without overfilling network buffers.

# Credits

    Original BBR: Google (Neal Cardwell, Yuchung Cheng, et al.)

    BBRplus: dog250 & cx9208
