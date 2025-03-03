I'll prove that the Internet checksum computation is independent of byte order by analyzing how the one's complement sum works with 16-bit words in different byte orders.

Let's consider the one's complement sum of n 16-bit words:
[A,B] +' [C,D] +' ... +' [Y,Z]

Where each pair [X,Y] represents a 16-bit word with bytes X and Y.

In one's complement arithmetic, the sum operation (+') involves:

1. Adding the operands as unsigned integers
2. If there's a carry bit beyond the bit width, adding it back to the result (end-around carry)

Let's analyze what happens when we swap the byte order:
[B,A] +' [D,C] +' ... +' [Z,Y]

First, let's see what each 16-bit word represents in both cases:

- [A,B] represents the value (A×256 + B) in big-endian
- [B,A] represents the value (B×256 + A) in little-endian

For the original sum, we have:
(A₁×256 + B₁) +' (A₂×256 + B₂) +' ... +' (Aₙ×256 + Bₙ)

For the swapped sum, we have:
(B₁×256 + A₁) +' (B₂×256 + A₂) +' ... +' (Bₙ×256 + Aₙ)

These sums produce different results. However, the key insight is that the one's complement of the final checksum is what's actually used, and when we swap the bytes of the final checksum, we get a value that works correctly regardless of the original byte order.

Let's see why:

1. If we compute the sum in big-endian:
   Sum = (A₁×256 + B₁) +' (A₂×256 + B₂) +' ... +' (Aₙ×256 + Bₙ) = X×256 + Y

2. If we compute the sum in little-endian:
   Sum = (B₁×256 + A₁) +' (B₂×256 + A₂) +' ... +' (Bₙ×256 + Aₙ) = Y×256 + X

3. The one's complement of the big-endian sum is:
   ~(X×256 + Y) = ~X×256 + ~Y (bitwise NOT of each byte)

4. The one's complement of the little-endian sum is:
   ~(Y×256 + X) = ~Y×256 + ~X

5. When we swap the bytes of the one's complement of the little-endian sum:
   [~X, ~Y] which equals ~(X×256 + Y)

This equals the one's complement of the big-endian sum, which is what we wanted to prove.

Therefore, the Internet checksum computation is independent of byte order, as long as we swap the bytes in the final checksum to match the required output order.
