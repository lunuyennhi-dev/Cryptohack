## 1. Mô tả đề bài
> [Paste lại description của bài ở đây]

<details>
<summary><b>Click để xem chi tiết mã nguồn <code>chall.py</code></b></summary>

```python
# Paste mã nguồn đề bài vào đây
```

</details>

---

##  2. Prerequisites & Mathematical Properties (Kiến thức nền tảng)

*Tóm tắt các lý thuyết toán học / thuật toán mã hóa liên quan đến bài.*

### 2.1. [Tên lý thuyết 1: VD: Thặng dư bậc hai / RSA / LLL...]
- Định nghĩa / Công thức:
  $$\left(\frac{x}{p}\right) \equiv x^{\frac{p-1}{2}} \equiv \begin{cases} 1 & \text{nếu } x \in \text{QR}_p \\\\ -1 & \text{nếu } x \in \text{QNR}_p \end{cases} \pmod p$$

### 2.2. [Tên lý thuyết 2 / Tính chất toán học]
- Mô tả tính chất đặc biệt giúp khai thác bài toán:
  $$\text{Công thức toán ở đây}$$

---

##  3. Vulnerability Analysis (Phân tích Lỗ hổng)

1. **Phân tích tham số:**
   - Quan sát các giá trị cho sẵn hoặc cách sinh khóa...

2. **Điểm yếu chính (Vulnerability):**
   ```diff
   # Phân tích đoạn code bị lỗi trong chall.py
   - Code bị lỗi hoặc sơ hở ở đây
   + Code đúng đáng lẽ phải như thế này
   ```

3. **Hướng giải quyết (Exploit Idea):**
   - Bước 1: ...
   - Bước 2: ...

---

##  4. Exploit Script (Code khai thác)

Tạo file `solve.py` và chạy để lấy Flag:

```python
#!/usr/bin/env python3
from Crypto.Util.number import long_to_bytes

# 1. Khai báo dữ liệu đầu vào
# ...

# 2. Xử lý toán học / Giải mã
# ...

# 3. In ra Flag
# print(f"[+] Flag: {flag}")
```

> [!TIP]
> [Ghi chú mẹo nhỏ hoặc công cụ/thư viện hữu ích khi chạy script, ví dụ: dùng SageMath hoặc PyCryptodome]

---
