### **Deep Pagination trong Elasticsearch**

#### **1. Khái niệm**
- **Deep pagination** là việc lấy dữ liệu ở các trang rất sâu (giá trị `from` lớn, ví dụ: từ document thứ 10000 trở lên) khi thực hiện truy vấn với Search API của Elasticsearch.
- Thường xuất hiện khi người dùng hoặc hệ thống muốn duyệt qua toàn bộ dữ liệu, ví dụ: xuất dữ liệu lớn, hiển thị dữ liệu phân trang dạng "Load more".

---

#### **2. Vấn đề với deep pagination**
- **Hiệu suất thấp**: Elasticsearch phải tải và bỏ qua tất cả các documents trước đó (`from`), gây tốn CPU, RAM và I/O.
- **Mất ổn định**: Nếu dữ liệu thay đổi (insert/update/delete), các trang sâu có thể bị lặp hoặc thiếu dữ liệu.
- **Giới hạn**: Thông thường, Elasticsearch mặc định cho phép truy vấn tới 10.000 documents đầu tiên (`index.max_result_window`), muốn vượt phải cấu hình lại (rất không khuyến khích).

---

#### **3. Giải pháp tốt hơn cho deep pagination**

##### **a. Scroll API**
- Dùng để lấy từng batch dữ liệu lớn, duyệt tuần tự qua toàn bộ dữ liệu mà không tốn tài nguyên cho mỗi truy vấn.
- Thích hợp cho xuất dữ liệu, ETL, backup.

**Ví dụ:**
```json
POST /my_index/_search?scroll=1m
{
  "size": 1000,
  "query": {
    "match_all": {}
  }
}
```
- Kết quả trả về `scroll_id` dùng để lấy tiếp batch tiếp theo.

```json
POST /_search/scroll
{
  "scroll": "1m",
  "scroll_id": "DnF1ZXJ5..."
}
```

---

##### **b. Search After**
- Thay vì dùng `from`, dùng `search_after` để truy vấn trang tiếp theo dựa trên giá trị của trường sắp xếp (thường là `_id`, hoặc trường timestamp, số thứ tự).
- Thích hợp cho dữ liệu phân trang lớn mà vẫn muốn hiệu suất cao.

**Ví dụ:**
```json
GET /my_index/_search
{
  "size": 100,
  "query": { "match_all": {} },
  "sort": [{ "created_at": "asc" }, { "_id": "asc" }],
  "search_after": [1628500000, "abc123"] // Giá trị cuối cùng của document trước đó
}
```

---

#### **4. Lưu ý khi dùng deep pagination**

- **Không khuyến khích dùng `from` lớn**: Chỉ dùng cho các trang đầu, phân trang sâu => dùng `scroll` hoặc `search_after`.
- **Dữ liệu thay đổi**: Khi dùng scroll, dữ liệu nên "immutable" hoặc ít thay đổi để tránh lặp/thiếu.
- **Nhớ giải phóng scroll**: Scroll giữ tài nguyên trên cluster, cần xóa khi không dùng nữa (`DELETE /_search/scroll`).
- **Với search_after**: Luôn sắp xếp bằng trường duy nhất và ổn định (timestamp + _id).

---

### **Tài liệu tham khảo**

- [Deep Pagination with search_after](https://www.elastic.co/guide/en/elasticsearch/reference/current/paginate-search-results.html#search-after)
- [Scroll API](https://www.elastic.co/guide/en/elasticsearch/reference/current/paginate-search-results.html#scroll-search-results)
- [Elasticsearch pagination best practices](https://www.elastic.co/guide/en/elasticsearch/reference/current/paginate-search-results.html)

---

Nếu bạn muốn ví dụ thực tế hoặc hướng dẫn chi tiết cách dùng scroll/search_after, hãy hỏi thêm nhé!