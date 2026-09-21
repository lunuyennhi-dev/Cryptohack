# [Crypto] Quadratic Residue Leak — CTF Write-up

![Category](https://img.shields.io/badge/Category-Crypto-blue)
![Topic](https://img.shields.io/badge/Topic-Number_Theory-purple)
![Difficulty](https://img.shields.io/badge/Difficulty-Easy--Medium-green)

---

## 1. Prerequisites & Mathematical Properties (Kiến thức nền tảng)

Để giải bài này, ta cần nắm vững các khái niệm cơ bản trong **Lý thuyết số (Number Theory)**:

### 1.1. Thặng dư bậc hai (Quadratic Residue - QR)
Cho số nguyên lẻ $p$ và số nguyên $x$ thỏa mãn $\gcd(x, p) = 1$. 
- $x$ được gọi là **thặng dư bậc hai modulo $p$** ($x \in \text{QR}_p$) nếu tồn tại số nguyên $y$ sao cho:
  $$y^2 \equiv x \pmod p$$
- Nếu không tồn tại $y$ như vậy, $x$ được gọi là **phi thặng dư bậc hai** ($x \in \text{QNR}_p$).

### 1.2. Ký hiệu Legendre (Legendre Symbol) & Tiêu chuẩn Euler
Ký hiệu Legendre $\left(\frac{x}{p}\right)$ giúp xác định nhanh $x$ có phải là QR mod $p$ hay không:

$$\left(\frac{x}{p}\right) \equiv x^{\frac{p-1}{2}} \equiv \begin{cases} 1 & \text{nếu } x \in \text{QR}_p \\\\ -1 \ (\equiv p-1) & \text{nếu } x \in \text{QNR}_p \end{cases} \pmod p$$

### 1.3. Tính chất nhân của Ký hiệu Legendre (Multiplicative Property)
Ký hiệu Legendre có tính chất nhân:
$$\left(\frac{a \cdot b}{p}\right) = \left(\frac{a}{p}\right) \cdot \left(\frac{b}{p}\right)$$

Đặc biệt, với $p \equiv 3 \pmod 4$, $-1$ luôn là một phi thặng dư bậc hai modulo $p$:
$$\left(\frac{-1}{p}\right) = (-1)^{\frac{p-1}{2}} = -1$$

---

## 2. Challenge Overview

Mã nguồn `chall.py` mã hóa từng bit của `FLAG` theo quy trình:
1. Chuyển `FLAG` thành chuỗi bit nhị phân $b \in \{'0', '1'\}$.
2. Với mỗi bit $b$, sinh số ngẫu nhiên $e \in [1, p]$ và tính $n = a^e \pmod p$.
3. Nếu $b == '1' \implies c = n$.
4. Nếu $b == '0' \implies c = -n \pmod p$.

<details>
<summary><b>Click để xem mã nguồn <code>chall.py</code></b></summary>

```python
from random import randint

a = 288260533169915
p = 1007621497415251
FLAG = b'crypto{????????????????????}'

def encrypt_flag(flag):
    ciphertext = []
    plaintext = ''.join([bin(i)[2:].zfill(8) for i in flag])
    for b in plaintext:
        e = randint(1, p)
        n = pow(a, e, p)
        if b == '1':
            ciphertext.append(n)
        else:
            n = -n % p
            ciphertext.append(n)
    return ciphertext

print(encrypt_flag(FLAG))
```

</details>

---

##  3. Vulnerability Analysis & Proof (Phân tích Lỗ hổng)

Ta tiến hành kiểm tra hai tham số $p$ và $a$ cho sẵn:

1. **Kiểm tra $p$:** 
   $$p = 1007621497415251 \implies p \equiv 3 \pmod 4 \implies \left(\frac{-1}{p}\right) = -1$$

2. **Kiểm tra $a$:** 
   $$\left(\frac{a}{p}\right) = a^{\frac{p-1}{2}} \pmod p = 1 \implies a \in \text{QR}_p$$

Vì $a$ là thặng dư bậc hai, nên mọi lũy thừa $n = a^e \pmod p$ cũng **luôn luôn là thặng dư bậc hai** với mọi $e$:
$$\left(\frac{n}{p}\right) = \left(\frac{a^e}{p}\right) = \left(\frac{a}{p}\right)^e = 1^e = 1$$

### Phân loại Bit dựa trên Legendre Symbol:
- **Trường hợp bit $b = '1'$:**
  $$c = n \implies \left(\frac{c}{p}\right) = \left(\frac{n}{p}\right) = 1$$

- **Trường hợp bit $b = '0'$:**
  $$c = -n \pmod p \implies \left(\frac{c}{p}\right) = \left(\frac{-1 \cdot n}{p}\right) = \left(\frac{-1}{p}\right) \cdot \left(\frac{n}{p}\right) = (-1) \cdot 1 = -1$$

> **Chốt lại:** Việc đổi dấu $-n \pmod p$ làm thay đổi tính chất thặng dư bậc hai của số đó, tạo ra một kênh phụ (side-channel) cho phép ta nhận biết bit `'0'` và `'1'` mà không cần giải bài toán Logarithm rời rạc (DLP).

---

##  4. Exploit Script

```python
#!/usr/bin/env python3
from Crypto.Util.number import long_to_bytes

a = 288260533169915
p = 1007621497415251

# Dữ liệu ciphertext thu được từ đề bài
ciphertext = [...] 

bits = ""
for c in ciphertext:
    # Sử dụng tiêu chuẩn Euler để tính Ký hiệu Legendre
    legendre_symbol = pow(c, (p - 1) // 2, p)
    
    if legendre_symbol == 1:
        bits += "1"
    else:
        bits += "0"

# Khôi phục chuỗi Byte từ chuỗi Bit
flag_bytes = int(bits, 2).to_bytes(len(bits) // 8, byteorder="big")
print(f"[+] Flag: {flag_bytes.decode()}")
```

> [!TIP]
> Tiêu chuẩn Euler cho phép tính Ký hiệu Legendre trong thời gian $O(\log p)$ nhờ giải thuật bình phương và nhân (Exponentiation by squaring), giúp script giải hoàn tất gần như ngay lập tức.

---

## 🎯 5. Takeaways & Conclusion

- Không phải lúc nào bài toán dạng $a^e \pmod p$ cũng bắt buộc phải giải **Logarithm rời rạc (DLP)**.
- Luôn kiểm tra các tính chất toán học của tham số ($p \pmod 4$, Legendre Symbol, Jacobi Symbol) khi gặp các phép toán điều kiện biến đổi số phức tạp.
