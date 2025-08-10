### **1. The Exists API**

#### **Mô tả**
- **Exists API** được sử dụng để kiểm tra xem một **document** có tồn tại trong Elasticsearch hay không.
- API này không trả về nội dung của document, mà chỉ trả về trạng thái HTTP:
  - `200 OK` nếu document tồn tại.
  - `404 Not Found` nếu document không tồn tại.

#### **Cách sử dụng**
- **Endpoint**: `HEAD /{index}/_doc/{id}`
- Sử dụng phương thức `HEAD` thay vì `GET`.

##### **Ví dụ:**
Kiểm tra xem document có ID là `1` trong index `example_index` có tồn tại hay không:
```bash
HEAD /example_index/_doc/1
```

- **Kết quả**:
  - Nếu document tồn tại: **Trả về HTTP 200 OK**.
  - Nếu document không tồn tại: **Trả về HTTP 404 Not Found**.

---

#### **Lưu ý khi sử dụng Exists API**
1. **Chỉ kiểm tra sự tồn tại**:
   - API này không trả về nội dung document ngay cả khi document tồn tại.
   - Nếu bạn cần nội dung của document, hãy sử dụng **GET API** thay thế.

2. **Hiệu suất tốt hơn**:
   - Vì không trả về nội dung document, Exists API nhanh hơn so với GET API.

3. **Thích hợp cho kiểm tra trước khi thao tác**:
   - Ví dụ: Kiểm tra sự tồn tại trước khi cập nhật hoặc xóa document.

---

#### **Tài liệu tham khảo**
- [Exists API Documentation](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-get.html#docs-get-head)

---

### **2. The Update API**

#### **Mô tả**
- **Update API** được sử dụng để cập nhật các document đã có trong Elasticsearch.
- Cập nhật được thực hiện theo cách **partial update** (chỉ thay đổi các trường được chỉ định trong yêu cầu).
- Nếu document không tồn tại, bạn có thể tùy chọn chèn (upsert) document mới.

---

#### **Cách sử dụng**
- **Endpoint**: `POST /{index}/_update/{id}`
- Cần cung cấp:
  - **ID của document**.
  - Các trường cần cập nhật hoặc hành động cập nhật.

##### **Ví dụ 1: Cập nhật một document**
Cập nhật trường `status` của document có ID `1` trong index `example_index`:
```json
POST /example_index/_update/1
{
  "doc": {
    "status": "active"
  }
}
```

- **Kết quả**:
  Elasticsearch chỉ thay đổi trường `status` mà không sửa đổi các trường khác trong document.

##### **Ví dụ 2: Sử dụng Upsert**
Nếu document không tồn tại, bạn có thể chèn document mới bằng cách sử dụng tùy chọn `upsert`:
```json
POST /example_index/_update/1
{
  "doc": {
    "status": "active"
  },
  "upsert": {
    "status": "new",
    "created_at": "2025-08-05"
  }
}
```

- **Kết quả**:
  - Nếu document tồn tại, trường `status` sẽ được cập nhật thành `"active"`.
  - Nếu document không tồn tại, Elasticsearch sẽ chèn một document mới với nội dung:
    ```json
    {
      "status": "new",
      "created_at": "2025-08-05"
    }
    ```

---

#### **Lưu ý khi sử dụng Update API**
1. **Partial Updates**:
   - Update API chỉ thay đổi các trường được chỉ định trong `doc`.
   - Các trường không được đề cập trong yêu cầu sẽ không bị ảnh hưởng.

2. **Không hỗ trợ cập nhật toàn bộ**:
   - Nếu bạn muốn thay thế toàn bộ nội dung của document, hãy sử dụng **INDEX API** thay thế.

3. **Concurrency Control (Kiểm soát đồng thời)**:
   - Elasticsearch sử dụng **versioning** để đảm bảo tính đồng nhất khi có nhiều tác vụ cập nhật diễn ra đồng thời.
   - Bạn có thể sử dụng tham số `retry_on_conflict` để thử lại khi xảy ra xung đột.

4. **Upsert**:
   - Upsert rất hữu ích khi bạn không chắc chắn document có tồn tại hay không.
   - Nếu không cần upsert, hãy đảm bảo document đã tồn tại trước khi cập nhật (có thể dùng Exists API).

5. **Script Updates**:
   - Bạn có thể sử dụng **scripts** để thực hiện các cập nhật phức tạp hơn.

**Ví dụ: Tăng giá trị của trường `views` lên 1 bằng script:**
```json
POST /example_index/_update/1
{
  "script": {
    "source": "ctx._source.views += params.count",
    "params": {
      "count": 1
    }
  }
}
```

---

#### **Tài liệu tham khảo**
- [Update API Documentation](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-update.html)

---

### **So sánh Exists API và Update API**

| **Tính năng**           | **Exists API**                                    | **Update API**                                  |
|--------------------------|--------------------------------------------------|-----------------------------------------------|
| **Mục đích**            | Kiểm tra sự tồn tại của document.                | Cập nhật nội dung của document.               |
| **Đầu vào**             | ID của document.                                 | ID của document và nội dung cần cập nhật.      |
| **Kết quả trả về**       | HTTP Status Code (200 hoặc 404).                 | Nội dung và thông tin về phiên bản document.  |
| **Hiệu suất**           | Nhanh, không trả về nội dung document.           | Tùy thuộc vào kích thước và hành động cập nhật.|

---

Nếu bạn cần thêm ví dụ hoặc giải thích chi tiết hơn, hãy cho tôi biết nhé!