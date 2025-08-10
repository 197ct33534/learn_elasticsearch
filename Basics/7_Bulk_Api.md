### **The Bulk API trong Elasticsearch**

#### **1. Mô tả**
- **Bulk API** trong Elasticsearch cho phép thực hiện **nhiều thao tác** (insert, update, delete) trên nhiều documents chỉ với **một yêu cầu duy nhất**.
- Đây là API được tối ưu hóa để xử lý dữ liệu với khối lượng lớn hiệu quả hơn so với việc thực hiện nhiều yêu cầu riêng lẻ.

---

### **2. Cú pháp cơ bản**
- Request đến Bulk API bao gồm các **lệnh hành động** (action) và dữ liệu liên quan, được gửi dưới dạng một chuỗi JSON.
- Mỗi lệnh hành động nằm trên một dòng riêng biệt, gồm:
  - `index`: Chèn hoặc ghi đè document.
  - `create`: Chỉ chèn document nếu chưa tồn tại.
  - `update`: Cập nhật document.
  - `delete`: Xóa document.

---

#### **Ví dụ 1: Thực hiện nhiều thao tác (index, update, delete)**

**Request:**
```json
POST /_bulk
{ "index": { "_index": "example_index", "_id": "1" } }
{ "title": "Document 1", "content": "This is the first document" }
{ "update": { "_index": "example_index", "_id": "2" } }
{ "doc": { "content": "Updated content for document 2" } }
{ "delete": { "_index": "example_index", "_id": "3" } }
```

**Phân tích:**
- **Dòng 1 & 2**: Chèn hoặc ghi đè document có ID `1` trong index `example_index`.
- **Dòng 3 & 4**: Cập nhật nội dung của document có ID `2`.
- **Dòng 5**: Xóa document có ID `3`.

---

#### **Ví dụ 2: Chỉ tạo mới document**

**Request:**
```json
POST /_bulk
{ "create": { "_index": "example_index", "_id": "4" } }
{ "title": "Document 4", "content": "This is the fourth document" }
{ "create": { "_index": "example_index", "_id": "5" } }
{ "title": "Document 5", "content": "This is the fifth document" }
```

**Phân tích:**
- **Dòng 1 & 2**: Tạo mới document có ID `4`.
- **Dòng 3 & 4**: Tạo mới document có ID `5`.
- **Lưu ý**: Nếu document đã tồn tại, lệnh `create` sẽ trả về lỗi.

---

#### **Ví dụ 3: Bulk từ file**
Bạn có thể gửi dữ liệu từ file trực tiếp đến Bulk API.

**Command:**
```bash
curl -X POST "localhost:9200/_bulk" -H "Content-Type: application/json" --data-binary @bulk_data.json
```

**File `bulk_data.json`:**
```json
{ "index": { "_index": "example_index", "_id": "1" } }
{ "title": "Document 1", "content": "This is the first document" }
{ "delete": { "_index": "example_index", "_id": "2" } }
{ "update": { "_index": "example_index", "_id": "3" } }
{ "doc": { "content": "Updated content for document 3" } }
```

---

### **3. Ứng dụng thực tế**
- **1. Nhập dữ liệu hàng loạt**:
  - Bulk API thường được sử dụng để tải dữ liệu lớn vào Elasticsearch một cách nhanh chóng, ví dụ như nhập dữ liệu logs, sản phẩm, hoặc sự kiện.
- **2. Đồng bộ hóa dữ liệu**:
  - Sử dụng Bulk API để thực hiện đồng bộ hóa dữ liệu từ một hệ thống nguồn (như cơ sở dữ liệu MySQL) vào Elasticsearch.
- **3. Cập nhật dữ liệu hàng loạt**:
  - Dùng để cập nhật các trường cụ thể trong một tập hợp documents lớn.
- **4. Xóa dữ liệu hàng loạt**:
  - Xóa nhiều documents không cần thiết để giải phóng không gian hoặc làm sạch dữ liệu.

---

### **4. Lưu ý khi sử dụng Bulk API**

#### **1. Kích thước batch**
- Bulk API hoạt động hiệu quả nhất khi bạn chia dữ liệu thành các **batch nhỏ** (ví dụ: 5,000–10,000 tài liệu mỗi lần).
- **Tránh gửi quá nhiều documents trong một yêu cầu**, vì nó có thể gây tắc nghẽn tài nguyên hoặc lỗi `OutOfMemory`.

#### **2. Ghi nhớ thứ tự dòng**
- Trong payload, các lệnh (`index`, `update`, `delete`) phải nằm trên một dòng riêng biệt, tiếp theo là dữ liệu trên dòng tiếp theo.

#### **3. Định dạng dữ liệu**
- Dữ liệu phải được định dạng đúng chuẩn JSON.
- Không có dấu phẩy cuối cùng giữa các lệnh hoặc trong payload.

#### **4. Phân tích lỗi**
- Bulk API không dừng lại nếu một hành động bị lỗi. Nó tiếp tục xử lý các hành động khác.
- **Response** trả về trạng thái của từng hành động, vì vậy bạn cần kiểm tra các lỗi cụ thể.

**Ví dụ Response với lỗi:**
```json
{
  "took": 30,
  "errors": true,
  "items": [
    { "index": { "_index": "example_index", "_id": "1", "status": 201 } },
    { "create": { "_index": "example_index", "_id": "2", "status": 409, "error": "document already exists" } }
  ]
}
```

#### **5. Gắn cờ `refresh`**
- Để cải thiện hiệu suất, bạn có thể sử dụng tham số `refresh=wait_for` hoặc `refresh=false` để kiểm soát quá trình làm mới index sau khi thực hiện Bulk.

---

### **5. Tài liệu tham khảo**
- **Bulk API Documentation**:  
  [https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-bulk.html](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-bulk.html)

- **Best Practices for Bulk API**:  
  [https://www.elastic.co/guide/en/elasticsearch/reference/current/tune-for-indexing-speed.html](https://www.elastic.co/guide/en/elasticsearch/reference/current/tune-for-indexing-speed.html)

---

Nếu bạn cần thêm ví dụ hoặc giải thích sâu hơn về cách sử dụng Bulk API, hãy cho tôi biết!