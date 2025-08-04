### **Field Data Types trong Elasticsearch là gì?**
**Field data types** trong Elasticsearch là các kiểu dữ liệu được sử dụng để định nghĩa các trường (fields) trong một document. Elasticsearch cần biết kiểu dữ liệu của mỗi trường để lưu trữ và xử lý thông tin một cách hiệu quả, đặc biệt là để tìm kiếm, lập chỉ mục và phân tích dữ liệu.

---

### **Danh sách các Field Data Types**
Dưới đây là danh sách các kiểu dữ liệu trong Elasticsearch, được chia thành các nhóm chính:

#### **1. Core Data Types (Nhóm dữ liệu cơ bản)**
- **`text`** (Thường dùng): Dùng để lưu trữ văn bản dài, có thể được phân tích (analyzed) để tìm kiếm toàn văn.
  - Ví dụ: `{"title": "Elasticsearch is amazing"}`
- **`keyword`** (Thường dùng): Lưu trữ văn bản ngắn hoặc các giá trị chính xác, không phân tích. Phù hợp cho các trường như mã, tên, ID, hoặc từ khóa.
  - Ví dụ: `{"status": "ACTIVE"}`
- **`long`**: Dùng để lưu trữ số nguyên dài.
  - Ví dụ: `{"views": 1000000}`
- **`integer`** (Thường dùng): Lưu trữ số nguyên.
  - Ví dụ: `{"age": 25}`
- **`float`**: Lưu trữ số thực.
  - Ví dụ: `{"price": 19.99}`
- **`double`**: Lưu trữ số thực với độ chính xác cao hơn `float`.
  - Ví dụ: `{"balance": 12345.6789}`
- **`boolean`** (Thường dùng): Lưu trữ giá trị đúng/sai.
  - Ví dụ: `{"is_available": true}`
- **`date`** (Thường dùng): Lưu trữ ngày giờ, hỗ trợ nhiều định dạng (ISO 8601, Unix timestamp,...).
  - Ví dụ: `{"created_at": "2025-08-04T15:00:00Z"}`

---

#### **2. Complex Data Types (Nhóm dữ liệu phức hợp)**
- **`object`** (Thường dùng): Lưu trữ dữ liệu dạng đối tượng JSON lồng nhau.
  - Ví dụ:
    ```json
    {
      "address": {
        "city": "New York",
        "zip": "10001"
      }
    }
    ```
- **`nested`**: Một dạng đặc biệt của `object`, dùng cho các mảng đối tượng (arrays of objects) mà cần tìm kiếm độc lập từng đối tượng con.
  - Ví dụ:
    ```json
    {
      "comments": [
        { "user": "Alice", "text": "Great post!" },
        { "user": "Bob", "text": "I agree!" }
      ]
    }
    ```

---

#### **3. Specialized Data Types (Nhóm dữ liệu đặc biệt)**
- **`ip`**: Lưu trữ địa chỉ IP (IPv4 hoặc IPv6).
  - Ví dụ: `{"ip_address": "192.168.1.1"}`
- **`geo_point`**: Lưu trữ tọa độ địa lý (latitude và longitude) để tìm kiếm không gian (spatial search).
  - Ví dụ: `{"location": { "lat": 40.7128, "lon": -74.0060 }}`
- **`geo_shape`**: Lưu trữ hình dạng địa lý (đường, đa giác,...).
- **`range`**: Lưu trữ một khoảng giá trị (range) cho các kiểu `integer`, `float`, `date`,...
  - Ví dụ: `{"price_range": { "gte": 10, "lte": 50 }}`

---

#### **4. Other Data Types (Các kiểu dữ liệu khác)**
- **`binary`**: Lưu trữ dữ liệu nhị phân được mã hóa Base64.
- **`alias`**: Dùng để trỏ đến một trường khác trong cùng một index.

---

### **Nhấn mạnh các Field Data Types thường dùng**
1. **`text`**: Dùng cho các trường cần tìm kiếm toàn văn (full-text search).
2. **`keyword`**: Dùng cho các trường không phân tích, như ID, mã, hoặc giá trị phân loại.
3. **`integer` và `float`**: Dùng cho các trường lưu trữ số liệu.
4. **`boolean`**: Dùng cho các trường lưu trạng thái (đúng/sai).
5. **`date`**: Dùng cho các trường lưu trữ thời gian.
6. **`object`**: Dùng khi cần lưu trữ dữ liệu lồng nhau.

---

### **Link tài liệu chi tiết về Field Data Types**
Bạn có thể tham khảo tài liệu chính thức của Elasticsearch để tìm hiểu chi tiết về các kiểu dữ liệu:
- [Elasticsearch Field Data Types Documentation](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-types.html)

---

Nếu bạn cần thêm ví dụ cụ thể hoặc giải thích chi tiết hơn, hãy cho tôi biết nhé!