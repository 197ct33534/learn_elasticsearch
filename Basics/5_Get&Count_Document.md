### **Tìm hiểu về Get Documents trong Elasticsearch**

#### **1. Get Documents**
Lệnh **GET** được sử dụng để truy xuất một tài liệu (document) cụ thể từ một index trong Elasticsearch dựa vào **ID của document**.

##### **Ví dụ:**
- **Truy xuất một document bằng ID:**
```json
GET /example_index/_doc/1
```

- **Kết quả:**
  Elasticsearch trả về tài liệu có ID là `1`:
  ```json
  {
    "_index": "example_index",
    "_type": "_doc",
    "_id": "1",
    "_version": 1,
    "found": true,
    "_source": {
      "title": "Elasticsearch Guide",
      "content": "This is an introductory guide to Elasticsearch."
    }
  }
  ```

#### **2. Truy xuất nhiều documents cùng lúc**
Để lấy nhiều tài liệu dựa trên ID, bạn có thể sử dụng **Multi Get (MGET)** API.

- **Ví dụ:**
```json
GET /_mget
{
  "docs": [
    { "_index": "example_index", "_id": "1" },
    { "_index": "example_index", "_id": "2" }
  ]
}
```

- **Kết quả:**
  Elasticsearch trả về các tài liệu tương ứng:
  ```json
  {
    "docs": [
      {
        "_index": "example_index",
        "_id": "1",
        "_source": {
          "title": "Elasticsearch Guide",
          "content": "This is an introductory guide to Elasticsearch."
        }
      },
      {
        "_index": "example_index",
        "_id": "2",
        "_source": {
          "title": "Advanced Elasticsearch",
          "content": "Learn advanced techniques with Elasticsearch."
        }
      }
    ]
  }
  ```

#### **3. Lưu ý khi sử dụng**
- Nếu document không tồn tại, Elasticsearch sẽ trả về `found: false`.
- Bạn có thể sử dụng tham số `stored_fields` để chỉ lấy các trường cụ thể trong document.

---

### **Tìm hiểu về Count Documents trong Elasticsearch**

#### **1. Count Documents**
Lệnh **Count API** được sử dụng để **đếm số lượng documents** thỏa mãn một điều kiện cụ thể trong một index.

##### **Ví dụ 1: Đếm tổng số documents trong index**
- **Truy vấn:**
```json
GET /example_index/_count
```
- **Kết quả:**
```json
{
  "count": 10,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  }
}
```

#### **2. Đếm documents với điều kiện**
Bạn có thể sử dụng **query** trong lệnh `count` để chỉ đếm những documents thỏa mãn điều kiện.

- **Ví dụ: Đếm những documents có `status` là `"active"`**
```json
GET /example_index/_count
{
  "query": {
    "term": {
      "status": "active"
    }
  }
}
```

- **Kết quả:**
```json
{
  "count": 5,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  }
}
```

#### **3. Lưu ý khi sử dụng**
- Count API không trả về nội dung của documents, chỉ trả về số lượng.
- Sử dụng Count API nhanh hơn so với Search API nếu bạn chỉ cần biết tổng số tài liệu thỏa mãn điều kiện.

---

### **So sánh giữa Get và Count Documents**

| **Tính năng**           | **Get Documents**                              | **Count Documents**                              |
|--------------------------|-----------------------------------------------|------------------------------------------------|
| **Mục đích**            | Truy xuất tài liệu cụ thể.                     | Đếm số lượng tài liệu.                         |
| **Đầu vào**             | ID của document (hoặc danh sách ID).           | Query (điều kiện).                             |
| **Kết quả trả về**       | Nội dung của document (hoặc nhiều documents).  | Số lượng tài liệu thỏa mãn điều kiện.          |
| **Tốc độ**              | Nhanh (tìm kiếm theo ID).                      | Nhanh hơn nếu chỉ cần đếm số lượng.            |

---

### **Tài liệu tham khảo**
1. **Get Document API**:  
   [Elasticsearch Get API](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-get.html)

2. **Count API**:  
   [Elasticsearch Count API](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-count.html)

Nếu bạn cần thêm ví dụ hoặc giải thích chi tiết hơn, hãy cho tôi biết nhé!