Below are clear, step‑by‑step algorithms for every functional page we have implemented so far.  
You can copy these into your record and adapt the wording if needed.

I’ll group them by cipher:

- Common/index page
- Shift cipher (2 pages)
- Hill cipher (7 pages)
- Playfair cipher (2 pages)

---

## 0. Index Page (Navigation Only)

**Algorithm: Index Page (Navigation)**

1. Start.
2. Display three separate containers:
   1. **Shift Cipher** container:
      - Show links:
        - “Encrypt using Shift Cipher” → `shift_encrypt.html`
        - “Decrypt using Shift Cipher” → `shift_decrypt.html`
   2. **Hill Cipher** container:
      - Show links to all 7 Hill pages:
        - Matrix inverse / determinant / cofactor / det inverse
        - Determinant
        - Multiplicative inverse (mod 26)
        - Hill encryption
        - Hill decryption
        - Cofactor matrix
        - Transpose
   3. **Playfair Cipher** container:
      - Show links:
        - “Encrypt using Playfair Cipher” → `playfair_encrypt.html`
        - “Decrypt using Playfair Cipher” → `playfair_decrypt.html`
3. Wait for the user to click a link.
4. When a link is clicked, load the corresponding page.
5. Stop.

---

# SHIFT CIPHER

## 1. Shift Cipher – Encryption Page

**Algorithm: Shift Cipher Encryption**

1. Start.
2. Read plaintext string `P` from the user.
3. Read integer key `K` from the user.
4. Normalize `K` to the range 0–25:  
   `K = ((K mod 26) + 26) mod 26`.
5. Initialize empty string `C` for ciphertext.
6. For each character `ch` in `P`:
   1. If `ch` is an alphabetic letter A–Z or a–z:
      1. Convert `ch` to lowercase.
      2. Convert this letter to a number `x` in 0–25 (`a → 0, …, z → 25`).
      3. Compute shifted value: `y = (x + K) mod 26`.
      4. Convert `y` back to a letter in A–Z (UPPERCASE).
      5. Append this letter to `C`.
   2. Else (non‑letter character):
      1. Append `ch` unchanged to `C`.
7. Display `C` as the encrypted ciphertext (UPPERCASE).
8. Stop.

---

## 2. Shift Cipher – Decryption Page

**Algorithm: Shift Cipher Decryption**

1. Start.
2. Read ciphertext string `C` from the user.
3. Read integer key `K` from the user.
4. Normalize `K` to the range 0–25:  
   `K = ((K mod 26) + 26) mod 26`.
5. Initialize empty string `P` for plaintext.
6. For each character `ch` in `C`:
   1. If `ch` is an alphabetic letter A–Z or a–z:
      1. Convert `ch` to lowercase.
      2. Convert this letter to a number `y` in 0–25.
      3. Compute original value: `x = (y − K) mod 26`  
         (implement as `x = (y - K + 26) mod 26` to avoid negatives).
      4. Convert `x` back to a letter in a–z (lowercase).
      5. Append this letter to `P`.
   2. Else:
      1. Append `ch` unchanged to `P`.
7. Display `P` as the decrypted plaintext (lowercase).
8. Stop.

---

# HILL CIPHER – SUPPORTING MATH

Before the Hill pages, list the core math sub‑algorithms you are using.

### Sub‑Algorithm A: Determinant of an n×n Matrix (Recursive Laplace Expansion)

**Algorithm: Determinant(matrix `A` of size n×n)**

1. If `n = 1`, return `A[0][0]`.
2. If `n = 2`, return  
   `det = A[0][0] * A[1][1] − A[0][1] * A[1][0]`.
3. Otherwise (`n > 2`):
   1. Set `det = 0`.
   2. For each column index `j` from 0 to `n−1` in the first row:
      1. Compute the minor matrix `M` by deleting row 0 and column `j` from `A`.
      2. Compute cofactor sign: `sign = (−1)^(0 + j)`.  
         (even sum: +1, odd sum: −1)
      3. Recursively compute `subDet = determinant(M)`.
      4. Update `det = det + sign * A[0][j] * subDet`.
   3. Return `det`.

---

### Sub‑Algorithm B: Cofactor Matrix

**Algorithm: CofactorMatrix(matrix `A` of size n×n)**

1. For each `i` from 0 to `n−1`:
   1. For each `j` from 0 to `n−1`:
      1. Form minor matrix `M` by deleting row `i` and column `j` from `A`.
      2. Compute `minorDet = determinant(M)`.
      3. Compute cofactor sign: `sign = (−1)^(i + j)`.
      4. Set `C[i][j] = sign * minorDet`.
2. Return the matrix `C` as the cofactor matrix.

---

### Sub‑Algorithm C: Transpose of a Matrix

**Algorithm: Transpose(matrix `A` of size n×n)**

1. For each `i` from 0 to `n−1`:
   1. For each `j` from 0 to `n−1`:
      1. Set `T[j][i] = A[i][j]`.
2. Return `T` as the transpose of `A`.

---

### Sub‑Algorithm D: Euclidean Algorithm (gcd)

**Algorithm: gcd(a, b)**

1. Set `a = |a|`, `b = |b|`.
2. While `b ≠ 0`:
   1. Set `r = a mod b`.
   2. Set `a = b`, `b = r`.
3. When loop ends, `a` is `gcd(a, b)`.
4. Return `a`.

---

### Sub‑Algorithm E: Extended Euclidean Algorithm

Finds `x, y` such that `a·x + b·y = gcd(a, b)`.

**Algorithm: ExtendedEuclid(a, b)**

1. Initialize:
   - `old_r = a`, `r = b`
   - `old_s = 1`, `s = 0`
   - `old_t = 0`, `t = 1`
2. While `r ≠ 0`:
   1. Compute quotient `q = floor(old_r / r)`.
   2. Update remainder:
      - `(old_r, r) = (r, old_r − q·r)`
   3. Update coefficients:
      - `(old_s, s) = (s, old_s − q·s)`
      - `(old_t, t) = (t, old_t − q·t)`
3. At the end, `old_r = gcd(a, b)`, `old_s`, `old_t` satisfy  
   `a·old_s + b·old_t = old_r`.
4. Return `(gcd = old_r, x = old_s, y = old_t)`.

---

### Sub‑Algorithm F: Multiplicative Inverse mod 26

**Algorithm: MultiplicativeInverseMod(a, 26)**

1. Compute `a = a mod 26` (make it in 0–25).
2. Call ExtendedEuclid(a, 26) to get `(gcd, x, y)`.
3. If `gcd ≠ 1`:
   1. No multiplicative inverse exists; report failure.
4. Else:
   1. The inverse is `inv = x mod 26` (ensure in 0–25).
5. Return `inv`.

---

### Sub‑Algorithm G: Row Vector × Matrix (1×n · n×n) mod 26

**Algorithm: MultiplyRowVectorMatrixMod(P[1×n], K[n×n])**

1. For each column index `j` from 0 to `n−1`:
   1. Set `sum = 0`.
   2. For each index `i` from 0 to `n−1`:
      1. `sum = sum + P[i] * K[i][j]`.
   3. Set result `C[j] = sum mod 26`.
2. Return result vector `C[1×n]`.

(Used as `C = P · K (mod 26)` and `P = C · K⁻¹ (mod 26)`.)

---

# HILL CIPHER PAGES

## 3. Hill – Matrix Inverse / Determinant / Cofactor Page

**Algorithm: Matrix Inverse, Determinant, Cofactor (mod 26)**

1. Start.
2. Read matrix dimension `n` from user.
3. Read `n×n` matrix elements from user (row‑wise).
4. Construct matrix `A` of size `n×n`.
5. Compute determinant `det = determinant(A)` (Sub‑Alg A).
6. Compute `detMod = det mod 26`.
7. Display `det` and `detMod`.
8. Compute cofactor matrix `C = CofactorMatrix(A)` (Sub‑Alg B).
9. Display `C`.
10. Try to compute inverse of determinant:
    1. Call `invDet = MultiplicativeInverseMod(detMod, 26)` (Sub‑Alg F).
    2. If no inverse exists, display message “det has no inverse mod 26”.
    3. Else display `invDet`.
11. Try to compute inverse matrix `A⁻¹ (mod 26)`:
    1. Transpose `C` to get adjugate matrix `Adj = Transpose(C)` (Sub‑Alg C).
    2. For each entry in `Adj`, compute `Adj[i][j] = (Adj[i][j] * invDet) mod 26`.
    3. Display this as the inverse matrix (mod 26).  
       (If `invDet` doesn’t exist, skip or show appropriate error.)
12. Stop.

---

## 4. Hill – Determinant of a Matrix Page

**Algorithm: Determinant of a Matrix**

1. Start.
2. Read matrix dimension `n` from user.
3. Read `n×n` matrix elements from user.
4. Construct matrix `A`.
5. Compute `det = determinant(A)` (Sub‑Alg A).
6. Compute `detMod = det mod 26`.
7. Display `det` and `detMod`.
8. Stop.

---

## 5. Hill – Multiplicative Inverse (mod 26) Page

**Algorithm: Multiplicative Inverse of a (mod 26)**

1. Start.
2. Read integer `a` from user.
3. Compute `g = gcd(a, 26)` (Sub‑Alg D).
4. Display `g` as `gcd(a, 26)`.
5. If `g ≠ 1`:
   1. Display: “No multiplicative inverse exists modulo 26.”
   2. Stop.
6. Else:
   1. Use ExtendedEuclid(a, 26) to get `(gcd, x, y)` (Sub‑Alg E).
   2. Compute inverse `inv = x mod 26` (0–25).
   3. Display `inv`.
7. Stop.

---

## 6. Hill Cipher – Encryption Page

**Algorithm: Hill Cipher Encryption (Row Vector × Matrix)**

1. Start.
2. Read matrix dimension `n` from user.
3. Read `n×n` key matrix elements from user and form key matrix `K`.
4. Compute determinant `det` of `K` and `detMod = det mod 26`.
5. Check invertibility:
   1. Compute `g = gcd(detMod, 26)`.
   2. If `g ≠ 1`, display “Key matrix is not invertible modulo 26” and stop.
6. Read plaintext string `P` from user.
7. Convert plaintext to numeric vector sequence:
   1. Initialize empty list `indices`.
   2. For each character `ch` in `P`:
      1. If `ch` is a letter A–Z / a–z:
         - Convert to lowercase.
         - Map to number `x` in 0–25 (`a→0,…,z→25`).
         - Append `x` to `indices`.
   3. If `indices` is empty, report error and stop.
   4. While length of `indices` is not a multiple of `n`, append value for `'x'` (23) as padding.
8. Encryption in blocks (1×n row vectors):
   1. Initialize empty ciphertext string `C`.
   2. For `i` from 0 to length(`indices`)−1, step by `n`:
      1. Take block `Pblock` = `indices[i..i+n−1]` (1×n row vector).
      2. Compute `Cblock = Pblock · K (mod 26)` using Sub‑Alg G.
      3. For each value `y` in `Cblock`:
         - Convert `y` back to a letter in A–Z (UPPERCASE) and append to `C`.
9. Display `C` as the Hill ciphertext.
10. Stop.

---

## 7. Hill Cipher – Decryption Page

**Algorithm: Hill Cipher Decryption (Row Vector × Inverse Matrix)**

1. Start.
2. Read matrix dimension `n` from user.
3. Read `n×n` key matrix elements from user and form key matrix `K`.
4. Check key invertibility:
   1. Compute determinant `det` and `detMod = det mod 26`.
   2. Compute `g = gcd(detMod, 26)`.
   3. If `g ≠ 1`, display “Key matrix is not invertible modulo 26” and stop.
5. Compute inverse key matrix `K⁻¹ (mod 26)`:
   1. Compute cofactor matrix `CofK`.
   2. Compute adjugate `Adj = Transpose(CofK)`.
   3. Compute `invDet = MultiplicativeInverseMod(detMod, 26)`.
   4. Compute each entry of `K⁻¹` as `Kinv[i][j] = (Adj[i][j] * invDet) mod 26`.
6. Read ciphertext string `Ctext` from user.
7. Convert ciphertext to numeric sequence:
   1. Initialize empty list `indices`.
   2. For each character `ch` in `Ctext`:
      1. If `ch` is a letter:
         - Convert to lowercase.
         - Map to number `y` in 0–25.
         - Append `y` to `indices`.
   3. If `indices` is empty, report error and stop.
   4. Check that `length(indices)` is a multiple of `n`.  
      If not, report error and stop.
8. Decryption in blocks:
   1. Initialize empty plaintext string `P`.
   2. For `i` from 0 to length(`indices`)−1, step by `n`:
      1. Take block `Cblock = indices[i..i+n−1]` (1×n row vector).
      2. Compute `Pblock = Cblock · K⁻¹ (mod 26)` using Sub‑Alg G (with `Kinv`).
      3. For each value `x` in `Pblock`:
         - Convert `x` back to a letter in a–z (lowercase) and append to `P`.
9. Display `P` as the decrypted plaintext (lowercase).
10. Stop.

---

## 8. Hill – Cofactor Matrix Page

**Algorithm: Cofactor Matrix of a Given Matrix**

1. Start.
2. Read matrix dimension `n` from user.
3. Read `n×n` matrix elements and form matrix `A`.
4. Compute cofactor matrix `C = CofactorMatrix(A)` (Sub‑Alg B).
5. Display `C` as the cofactor matrix.
6. Stop.

---

## 9. Hill – Transpose Page

**Algorithm: Transpose of a Matrix**

1. Start.
2. Read matrix dimension `n` from user.
3. Read `n×n` matrix elements and form matrix `A`.
4. Compute `T = Transpose(A)` (Sub‑Alg C).
5. Display `T` as the transpose matrix.
6. Stop.

---

# PLAYFAIR CIPHER

## 10. Playfair Cipher – Encryption Page

**Algorithm: Playfair Encryption**

1. Start.
2. Read key string `K` from user.
3. Build the 5×5 key square:
   1. Convert `K` to lowercase.
   2. Remove non‑letters.
   3. Replace every `j` with `i`.
   4. Scan from left to right, keep each letter only the first time it appears, forming processed key `K'`.
   5. Initialize key square string `S` with `K'`.
   6. For each letter `a` to `z`:
      1. Skip `j` (since `i`/`j` are merged).
      2. If letter is not already in `S`, append it.
   7. Place letters of `S` row-wise into a 5×5 matrix.
   8. Record the (row, column) position of each letter in a lookup table.
4. Read plaintext string `P` from user.
5. Normalize plaintext `P`:
   1. Convert to lowercase.
   2. Remove non‑letters.
   3. Replace every `j` with `i`.
6. Form digraphs (pairs of letters) from the normalized text:
   1. Initialize an empty list of pairs.
   2. Process characters from left to right:
      1. Let first letter be `a`.
      2. If there is a second letter `b`:
         - If `a ≠ b`, form pair `(a, b)` and move ahead by 2 characters.
         - If `a = b`, form pair `(a, filler)` where `filler = 'x'` (or `'z'` if `a` is already `'x'`), and move ahead by 1 character.
      3. If only a single letter remains at the end:
         - Form pair `(a, 'x')`.
7. For each digraph `(a, b)`:
   1. Find positions `(row_a, col_a)` and `(row_b, col_b)` in the key square using the lookup table.
   2. Apply Playfair rules:
      1. **Same row** (`row_a == row_b`):
         - `a'` at `(row_a, (col_a + 1) mod 5)`
         - `b'` at `(row_b, (col_b + 1) mod 5)`
      2. **Same column** (`col_a == col_b`):
         - `a'` at `((row_a + 1) mod 5, col_a)`
         - `b'` at `((row_b + 1) mod 5, col_b)`
      3. **Rectangle** (different row and column):
         - `a'` at `(row_a, col_b)`
         - `b'` at `(row_b, col_a)`
   3. Append `a'` and `b'` (converted to UPPERCASE) to ciphertext string `C`.
8. Display `C` as the Playfair ciphertext.
9. Stop.

---

## 11. Playfair Cipher – Decryption Page

**Algorithm: Playfair Decryption**

1. Start.
2. Read key string `K` from user.
3. Build the same 5×5 key square as in encryption (same procedure as in Step 3 above).
4. Read ciphertext string `Ctext` from user.
5. Normalize ciphertext:
   1. Convert to lowercase.
   2. Remove non‑letters.
   3. Replace `j` with `i`.
6. Ensure normalized ciphertext length is even; if not, report error.
7. Form digraphs `(A, B)` from ciphertext (two letters at a time).
8. For each digraph `(A, B)`:
   1. Find positions `(row_A, col_A)` and `(row_B, col_B)` in the key square.
   2. Apply inverse Playfair rules:
      1. **Same row** (`row_A == row_B`):
         - `a` at `(row_A, (col_A + 4) mod 5)` (one step left)
         - `b` at `(row_B, (col_B + 4) mod 5)`
      2. **Same column** (`col_A == col_B`):
         - `a` at `((row_A + 4) mod 5, col_A)` (one step up)
         - `b` at `((row_B + 4) mod 5, col_B)`
      3. **Rectangle** (different row and column):
         - `a` at `(row_A, col_B)`
         - `b` at `(row_B, col_A)`
   3. Append `a` and `b` (lowercase) to plaintext string `P`.
9. (Optionally) Note: filler letters (`x` or `z`) may appear in `P` where they were inserted to break double letters or pad odd length; these are usually left as is in simple implementations.
10. Display `P` as the decrypted Playfair plaintext (lowercase).
11. Stop.

---

If your staff want each sub‑algorithm (determinant, extended Euclid, etc.) written separately, you can copy the Sub‑Algorithms A–G and then, for each page, simply write “Call Determinant(A)” or “Call ExtendedEuclid(a, 26)”.