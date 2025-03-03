I'll construct an example where the sliding window algorithm with SWS = RWS = 3 and MaxSeqNum = 5 becomes confused, accepting DATA[0] when expecting DATA[5].

Let's track both the actual packet number and its sequence number (which is N mod 5):

1. The sender transmits DATA[0] (seq=0), DATA[1] (seq=1), DATA[2] (seq=2)
2. The receiver receives and acknowledges all three packets with ACK[2]
3. The sender slides its window and transmits DATA[3] (seq=3), DATA[4] (seq=4), DATA[5] (seq=0)
4. The receiver receives DATA[3] and DATA[4], but all ACKs are lost
5. The sender times out and retransmits DATA[3] (seq=3), DATA[4] (seq=4), DATA[5] (seq=0)
6. The receiver drops the duplicate DATA[3] and DATA[4], but accepts DATA[5] (seq=0)
7. However, at this point, a new DATA[0] (seq=0) arrives

The problem occurs here: The receiver can't distinguish between DATA[5] (seq=0) and DATA[0] (seq=0) because they have the same sequence number (0).

This confusion happens because MaxSeqNum = 5 is too small. With SWS = 3, we need at least MaxSeqNum = 6 to ensure uniqueness. This is because we need enough sequence numbers to cover both the sender's window and the packets that might still be in transit or acknowledged.

When MaxSeqNum ≥ 2×SWS (or 6 in this case), we can avoid this confusion because the sequence numbers within any valid window would be unique.
