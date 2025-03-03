To prove that the Internet checksum computation is independent of byte order (host order or network order), we need to show that the sum of 16-bit words can be computed in either byte order, and the result will be the same except for the final byte order of the checksum.

### Background:

The Internet checksum is computed using **one's complement arithmetic**. The checksum is calculated by summing 16-bit words of the data and taking the one's complement of the result. The key property of one's complement arithmetic is that it is **commutative** and **associative**, meaning the order in which the additions are performed does not affect the final result.

### Proof:

1. **Representation of 16-bit words:**

   - Let \([A, B]\) represent a 16-bit word where \(A\) is the high-order byte and \(B\) is the low-order byte.
   - The value of \([A, B]\) in decimal is \(256 \cdot A + B\) (since \(A\) is the high-order byte).
   - Similarly, \([B, A]\) represents the same bytes swapped, and its value is \(256 \cdot B + A\).

2. **Sum of words in original order:**

   - The sum of the words in the original order is:
     \[
     [A, B] +' [C, D] +' \dots +' [Y, Z]
     \]
   - In decimal, this is:
     \[
     (256 \cdot A + B) + (256 \cdot C + D) + \dots + (256 \cdot Y + Z)
     \]

3. **Sum of words in swapped order:**

   - The sum of the words in the swapped order is:
     \[
     [B, A] +' [D, C] +' \dots +' [Z, Y]
     \]
   - In decimal, this is:
     \[
     (256 \cdot B + A) + (256 \cdot D + C) + \dots + (256 \cdot Z + Y)
     \]

4. **Equivalence of the sums:**

   - Notice that the sum of the swapped words is:
     \[
     (256 \cdot B + A) + (256 \cdot D + C) + \dots + (256 \cdot Z + Y)
     \]
   - This can be rewritten as:
     \[
     (256 \cdot A + B) + (256 \cdot C + D) + \dots + (256 \cdot Y + Z)
     \]
     because addition is commutative and associative. The order of the terms does not affect the result.

5. **Final checksum:**
   - The final checksum is the one's complement of the sum. Since the sum is the same regardless of byte order, the checksum will also be the same.
   - However, the **byte order of the final checksum** must be swapped to match the required network byte order (big-endian). This is because the computation itself is independent of byte order, but the final representation of the checksum must conform to the network standard.

### Conclusion:

The Internet checksum computation is independent of byte order during the summation process. The sum of 16-bit words can be computed in either byte order, and the result will be the same. The only difference is that the final checksum must be byte-swapped to match the required network byte order. This proves the claim.
