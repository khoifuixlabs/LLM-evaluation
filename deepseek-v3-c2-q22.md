### Timeouts in a NAK-based ARQ Protocol

In a NAK-based ARQ (Automatic Repeat reQuest) protocol, the receiver only sends negative acknowledgments (NAKs) when it detects a missing or corrupted packet. The sender does not receive positive acknowledgments (ACKs) for successfully received packets. Therefore, the sender must rely on timeouts to detect lost packets. Here's how timeouts would be scheduled:

1. **Per-Packet Timeout**: The sender sets a timeout for each packet it transmits. If the sender does not receive a NAK for a packet within the timeout period, it assumes the packet was successfully received and does not retransmit it.

2. **Retransmission Timeout**: If the sender receives a NAK for a specific packet, it retransmits that packet and sets a new timeout for the retransmission. This ensures that the sender can handle cases where the retransmitted packet is also lost.

3. **Global Timeout**: In addition to per-packet timeouts, the sender may use a global timeout to handle cases where no feedback (NAKs) is received for an extended period. This could indicate a complete breakdown in communication, prompting the sender to reset the connection or take other recovery actions.

### Why ACK-based Protocols Are Usually Preferred

1. **Efficiency**: In an ACK-based protocol, the sender receives explicit confirmation that a packet has been successfully received. This allows the sender to immediately move on to the next packet, reducing latency and improving throughput. In contrast, a NAK-based protocol requires the sender to wait for a timeout to confirm successful delivery, which can lead to inefficiencies.

2. **Lower Overhead**: ACK-based protocols typically generate less feedback traffic compared to NAK-based protocols. In a NAK-based protocol, the sender must wait for timeouts to detect lost packets, which can result in unnecessary delays. In an ACK-based protocol, the sender can continuously transmit packets as long as it receives ACKs, reducing the need for timeouts.

3. **Simpler Design**: ACK-based protocols are generally simpler to implement and manage. The sender can use cumulative ACKs (e.g., TCP's cumulative ACK mechanism) to acknowledge multiple packets at once, further reducing overhead. In contrast, NAK-based protocols require more complex timeout management and retransmission logic.

4. **Better Congestion Control**: ACK-based protocols provide more timely feedback about the state of the network, which is crucial for effective congestion control. For example, TCP uses ACKs to adjust its congestion window and transmission rate. NAK-based protocols, with their reliance on timeouts, may react more slowly to network congestion, leading to suboptimal performance.

5. **Robustness**: ACK-based protocols are more robust in handling packet loss and network variability. Since the sender receives continuous feedback, it can quickly adapt to changing network conditions. In a NAK-based protocol, the sender must wait for timeouts or NAKs, which can result in slower adaptation and reduced performance.

In summary, while NAK-based protocols can be useful in specific scenarios (e.g., multicast or broadcast scenarios where ACKs would generate too much feedback traffic), ACK-based protocols are generally preferred due to their efficiency, lower overhead, simpler design, better congestion control, and robustness.
