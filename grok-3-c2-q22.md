### Answer

#### Timeouts in a NAK-Only ARQ Protocol

In an Automatic Repeat reQuest (ARQ) protocol that uses only negative acknowledgments (NAKs) and no positive acknowledgments (ACKs), the sender transmits packets and relies on feedback from the receiver to determine whether retransmission is necessary. Unlike traditional ARQ protocols that use ACKs to confirm successful packet reception, this NAK-only protocol has the receiver send a NAK only when it detects an issue, such as a corrupted packet or a missing packet (e.g., detected via sequence number gaps). If the packet is received correctly, the receiver remains silent, sending no feedback. This design necessitates specific timeout mechanisms to ensure reliable data transmission. Here’s what timeouts would have to be scheduled:

1. **Sender’s Timeout per Packet:**

   - **Description:** After sending each packet, the sender starts a timer associated with that packet. This timer represents the maximum time the sender is willing to wait for a potential NAK from the receiver.
   - **Purpose:** If the sender receives a NAK before the timer expires, it retransmits the corresponding packet, indicating that the receiver detected an error or loss. If the timer expires without receiving a NAK, the sender assumes the packet was received correctly and can discard it from its buffer, proceeding to send the next packet (in a stop-and-wait scenario) or managing its sending window (in a sliding window scenario).
   - **Necessity:** Since there are no ACKs to explicitly confirm successful delivery, the absence of a NAK within the timeout period serves as an implicit indication of success. The timeout duration must account for the round-trip time (RTT)—the time for the packet to reach the receiver and a potential NAK to return—plus additional time for processing and network variability.
   - **Challenges:** Setting an appropriate timeout value is critical. If it’s too short, a delayed NAK might arrive after the sender has assumed success, leading to confusion. If it’s too long, the sender may unnecessarily delay sending subsequent packets, reducing efficiency.

2. **Receiver’s Role (Potential Timeout Consideration):**
   - **Sequence Number-Based Detection:** Typically, in ARQ protocols, packets are assigned sequence numbers. The receiver can detect missing packets by identifying gaps in the sequence (e.g., receiving packet 1, then packet 3, indicates packet 2 is missing) and immediately send a NAK for the missing packet. This reactive approach does not require a timeout on the receiver side.
   - **Optional Receiver Timeout:** In scenarios where packets are expected at regular intervals (synchronous transmission), the receiver might implement a timeout to detect a missing packet if no packet arrives within an expected timeframe. For example, if packets are sent every T seconds, the receiver could set a timer for approximately 2T seconds and send a NAK if a packet is overdue. However, this is not standard in most ARQ designs, which rely on sequence numbers rather than timing assumptions, so this timeout is generally unnecessary unless specified by the protocol.

In summary, the primary timeout required is on the sender’s side for each packet transmitted. The sender waits for a NAK, and if none arrives before the timeout, it assumes successful delivery. The receiver typically does not need timeouts if sequence numbers are used, as it can detect issues based on received packets alone.

#### Why an ACK-Based Protocol Is Usually Preferred

While a NAK-only ARQ protocol might seem efficient by reducing feedback traffic in cases where errors are rare (since NAKs are sent only when problems occur), ACK-based protocols—where the receiver sends an ACK for each correctly received packet—are generally preferred in practice. Here’s why:

1. **Reliability Through Explicit Confirmation:**

   - **ACK-Based:** The sender receives explicit confirmation (an ACK) for each packet successfully delivered. This positive feedback ensures the sender knows definitively which packets have been received, reducing ambiguity.
   - **NAK-Based:** The sender relies on the absence of a NAK to infer success, which is less reliable. If a NAK is lost or delayed beyond the timeout, the sender might incorrectly assume a packet was received when it was not, leading to data loss.

2. **Handling Lost Packets:**

   - **ACK-Based:** If a packet is lost, the sender does not receive an ACK within the timeout period and retransmits the packet. This ensures lost packets are eventually retransmitted.
   - **NAK-Based:** If a packet is lost and the receiver doesn’t detect it (e.g., the last packet in a transmission with no subsequent packets to reveal a sequence gap), no NAK is sent. The sender’s timeout expires without feedback, and it assumes success, resulting in undetected data loss.

3. **Robustness Against Lost Feedback:**

   - **ACK-Based:** If an ACK is lost, the sender might retransmit a packet unnecessarily, but the receiver can detect duplicates (using sequence numbers) and discard them, maintaining data integrity.
   - **NAK-Based:** If a NAK is lost (e.g., sent due to a corrupted packet), the sender does not retransmit, assuming the packet was received correctly after the timeout. This can lead to missing or corrupted data persisting undetected.

4. **End-of-Transmission Clarity:**

   - **ACK-Based:** The sender can confirm that all packets, including the last one, have been received once it collects ACKs for all transmitted packets. This provides a clear endpoint to the transmission.
   - **NAK-Based:** Confirming the successful delivery of the final packet is problematic. If the last packet is lost or corrupted and no further packets are sent, the receiver may not detect the issue (no sequence gap occurs), and no NAK is triggered. The sender assumes success after the timeout, leaving the transmission incomplete.

5. **Simplicity and Predictability:**

   - **ACK-Based:** The explicit feedback loop simplifies protocol design and timeout management. The sender retransmits only when an ACK is missing, and ACKs can often be piggybacked on return data packets, minimizing overhead.
   - **NAK-Based:** The reliance on timeouts and the absence of feedback introduces complexity. The sender must carefully tune timeouts to avoid premature assumptions of success or excessive delays, and handling edge cases (like lost NAKs or the last packet) requires additional mechanisms, reducing robustness.

6. **Practical Efficiency Trade-Off:**
   - While a NAK-based protocol might reduce feedback traffic when errors are infrequent, modern networks often handle ACK overhead efficiently (e.g., through cumulative ACKs in protocols like TCP). The reliability benefits of ACKs outweigh the marginal bandwidth savings of NAKs in most real-world scenarios.

#### Conclusion

In a NAK-only ARQ protocol, the sender schedules a timeout for each packet to determine when to assume successful delivery in the absence of a NAK, while the receiver typically relies on sequence numbers to detect issues without needing its own timeouts. However, ACK-based protocols are preferred due to their superior reliability, explicit confirmation of packet delivery, ability to handle lost packets and feedback, and clearer management of transmission completion. These advantages make ACK-based designs, such as those used in TCP or Selective Repeat ARQ, the standard choice for ensuring robust data communication in practice.
