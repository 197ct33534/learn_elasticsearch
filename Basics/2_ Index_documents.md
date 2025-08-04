### 1. **Có bao nhiêu cách để index document?**

Trong Elasticsearch, có 3 cách phổ biến để index documents:

---

#### **Cách 1: Sử dụng API `PUT`**
- Đây là cách index một document với **ID cố định** (bạn tự chỉ định ID).
- Nếu document có ID đã tồn tại, Elasticsearch sẽ **ghi đè** document cũ.

**Ví dụ:**
```json
PUT /my_index/_doc/1
{
  "title": "Document 1",
  "content": "This is the first document."
}
```

---

#### **Cách 2: Sử dụng API `POST`**
- Đây là cách index một document mà **ID được tự động sinh ra bởi Elasticsearch**.
- Elasticsearch sẽ tạo một ID ngẫu nhiên và trả về ID này trong kết quả.

**Ví dụ:**
```json
POST /my_index/_doc/
{
  "title": "Document 2",
  "content": "This is the second document."
}
```

---

#### **Cách 3: Sử dụng Bulk API**
- Cho phép index **nhiều documents cùng lúc** trong một lần gọi API.
- Đây là cách tối ưu khi bạn cần index số lượng lớn documents.

**Ví dụ:**
```json
POST /_bulk
{ "index": { "_index": "my_index", "_id": "3" } }
{ "title": "Document 3", "content": "This is the third document." }
{ "index": { "_index": "my_index" } }
{ "title": "Document 4", "content": "This is the fourth document." }
```

**Giải thích:**
- Document 3 có ID cố định là `"3"`.
- Document 4 không có ID chỉ định => Elasticsearch sẽ tự tạo ID ngẫu nhiên.

---

### 2. **Không tạo index trước thì có tạo được documents không?**

Câu trả lời là **CÓ**. Elasticsearch **tự động tạo index** khi bạn index documents mà index đó chưa tồn tại, trừ khi bạn đã tắt tính năng tự động này.

#### **Ví dụ:**
Bạn index một document vào index `my_new_index` mà chưa tạo index trước đó:

```json
PUT /my_new_index/_doc/1
{
  "title": "Auto-created Index",
  "content": "Elasticsearch can create indices automatically."
}
```

- Elasticsearch sẽ tự động tạo index `my_new_index` với các thiết lập mặc định (5 shards, 1 replica).
- Tuy nhiên, các **mappings (kiểu dữ liệu của fields)** sẽ được Elasticsearch gán tự động dựa trên dữ liệu bạn cung cấp.

---

#### **Hạn chế của việc không tạo index trước:**
- **Mappings không được kiểm soát:** Elasticsearch tự động đoán kiểu dữ liệu dựa trên giá trị ban đầu, có thể không chính xác.
  - Ví dụ: Nếu bạn gửi một số nguyên (`42`), Elasticsearch sẽ gán kiểu **integer**, nhưng sau đó bạn muốn lưu chuỗi (`"42"`), sẽ gây lỗi vì không khớp kiểu dữ liệu.
- **Thiếu tối ưu:** Các thiết lập như số lượng **shards**, **replicas**, hoặc **analyzer** (bộ phân tích văn bản) sẽ áp dụng mặc định.

---

### 3. **Lời khuyên**
- **Nếu bạn biết cấu trúc dữ liệu trước:** Nên tạo index thủ công với mappings và settings phù hợp trước khi index documents.
- **Nếu bạn cần nhanh chóng lưu dữ liệu:** Có thể để Elasticsearch tự động tạo index, nhưng cần kiểm tra và điều chỉnh mappings nếu cần.

Nếu bạn cần chi tiết hơn hoặc ví dụ cụ thể, hãy cho tôi biết!