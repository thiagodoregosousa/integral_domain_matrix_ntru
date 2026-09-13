# integral_domain_matrix_ntru

A SageMath implementation of matrix NTRU over an integral domain: an
NTRU-style lattice cryptosystem where keys and messages are matrices with
entries in the ring of integers K = Q(√-3), instead of the usual polynomial
ring. This repo implements key generation, encryption, and decryption for
the scheme described in Wijayanti, Isnaini & Sari (2023), and includes code
used to probe decryption-failure boundaries and counterexamples related to
the paper's Theorem 1.

## Usage

Requires SageMath. Load the file into a Sage session and call the
functions directly:

```
$ sage
sage: load("integral_domain_functions.sage")
sage: N, p, q, d = 3, 3, 31, 5
sage: F, Fp, Fq, G, H = key_gen_F_with_integer_determinant(N, p, q, d)
sage: M = random_matrix_fixed_d_fast(N, d)      # a "message" matrix
sage: E = encrypt(M, H, N, p, q)
sage: decrypt(E, F, Fp, N, p, q) == M
True
```

`keygen_new()` is an alternative key-generation routine that does not
require F's determinant to be an integer (it uses the extended Euclidean
algorithm over K instead).

## Functions

| Function | Description |
|---|---|
| `key_gen_F_with_integer_determinant(N,p,q,d)` | Key generation: returns private key `F`, its inverses `Fp`/`Fq`, and public key `H`, requiring `F` to have integer determinant |
| `keygen_new(N,p,q,d,maxit=100)` | Alternative key generation using the extended Euclidean algorithm over K, without the integer-determinant restriction |
| `encrypt(M,H,N,p,q)` | Encrypt message matrix `M` under public key `H` |
| `decrypt(E,F,F_inv_p,N,p,q)` | Decrypt ciphertext `E` using private key `F` and its inverse mod p |
| `random_matrix_with_integer_determinant_coprime_p_q(N,p,q,d)` | Search for a candidate private key with nonzero integer determinant coprime to both `p` and `q` |
| `random_matrix(N,d)` | Generate a random N×N matrix over K with the NTRU coefficient distribution |
| `random_matrix_fixed_d(N,d)` | Like `random_matrix`, but rejection-samples until exactly `d` coefficients equal +1 and `d` equal -1 |
| `random_matrix_fixed_d_fast(N,d)` | Faster version of `random_matrix_fixed_d` using direct index sampling instead of rejection |
| `generate_random_element(N,d)` | Draw a single coefficient in {-1, 0, 1} with the probabilities used by the matrix NTRU distribution |
| `count_ones_and_minus_ones(A)` | Count the +1 and -1 coefficients of a matrix |
| `z_mod_p(z,p)` | Reduce one element of K modulo p, coefficient-wise |
| `matrixMODp(A,p)` | Reduce every entry of a matrix over K modulo p |
| `center_lift_coef(a,q)` | Center-lift the coefficients of an element of K into (-q/2, q/2] |
| `aMODb(a,b,returnQuotientAndRest=0)` | Modular reduction of one element of K by another, with center-lifted remainder |
| `extendedEuclidean(f,g)` | Extended Euclidean algorithm over K = Q(√-3); returns `s, t, r` with `s*f + t*g == r` |
| `q_min_value(N,p,prime_list=...)` | Find the smallest prime `q` above the theoretical bound that guarantees zero decryption failure |
| `random_matrix_with_fixed_line()` / `random_matrix_with_fixed_column()` | Hand-crafted N=3 matrices used to probe decryption-failure edge cases |
| `random_matrix_F_with_N_3_d_5_fixed_line()` | Search for an N=3, d=5 private key built from `random_matrix_with_fixed_line`, used to test counterexamples to the original theorem |

## Repository layout

- `integral_domain_functions.sage` — all functions: modular arithmetic over K = Q(√-3), NTRU-distributed random matrix generators, the two key-generation variants, encryption/decryption, the extended Euclidean algorithm over K, the decryption-failure parameter search (`q_min_value`), and the hardcoded N=3 matrices used to look for theorem counterexamples.

## Reference

[1] 2023, IEEE, ICoCICs, Indah E. Wijayanti, Uha Isnaini, Anny Kartika Sari. Matrix NTRU Cryptosystem over Integral Domain from Published at IEEE - ICoCICs in 2023.

[2] von zur Gathen, J., & Gerhard, J. (2013). Modern Computer Algebra (3rd ed.). Cambridge University Press.
