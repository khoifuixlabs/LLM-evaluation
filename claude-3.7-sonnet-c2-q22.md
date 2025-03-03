In a NAK-only ARQ protocol, the receiver sends negative acknowledgments only when errors are detected in received packets. Let me analyze the timeouts needed and why ACK-based protocols are generally preferred.

## Timeouts in a NAK-only Protocol

A NAK-only protocol would require the following timeouts:

1. **Sender-side final packet timeout**: After sending the final packet, the sender must wait for a period to ensure no NAKs are coming back before considering the transmission complete.

2. **Receiver-side packet arrival timeout**: The receiver needs a timeout mechanism to detect missing packets (since it can only send NAKs for packets it knows are missing or corrupt).

3. **NAK response timeout**: After sending a NAK, the receiver would need to wait for retransmission, and timeout if not received.

4. **Sender inactivity timeout**: The receiver needs to detect if the sender has crashed or terminated transmission.

## Why ACK-based Protocols Are Preferred

ACK-based protocols are generally preferred for several reasons:

1. **Positive confirmation**: ACKs provide positive confirmation that packets were received correctly, giving higher confidence in transmission success.

2. **Simpler detection of packet loss**: With ACKs, the sender can easily detect lost packets when expected ACKs don't arrive within a timeout period.

3. **Better handling of lost feedback**: If an ACK is lost, the sender will timeout and retransmit. If a NAK is lost, the error remains undetected since the sender doesn't know to retransmit.

4. **Clearer connection state**: ACKs help maintain a clearer picture of connection state, since silence in a NAK protocol is ambiguous (could mean successful delivery or complete failure).

5. **Better handling of final packets**: It's difficult in a NAK-only system to know when transmission is truly complete, as silence is interpreted as success.

6. **Flow control integration**: ACKs can more easily incorporate flow control information.

A NAK-only system could work in channels with very low error rates, but most real-world networks experience enough errors that the reliability advantages of ACK-based protocols outweigh their slightly higher overhead.
