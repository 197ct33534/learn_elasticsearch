### **Tìm hiểu về Delete Documents trong Elasticsearch**

Elasticsearch cung cấp nhiều cách để xóa dữ liệu (documents) khỏi index. Việc xóa documents có thể được thực hiện theo nhiều cách khác nhau, tùy thuộc vào nhu cầu cụ thể và số lượng documents cần xóa.

---

### **Có mấy cách để xóa documents?**

#### **1. Xóa một document bằng ID**
- Sử dụng **API DELETE** để xóa một tài liệu cụ thể dựa trên **ID** của nó.
- **Sử dụng khi**: Bạn biết chính xác ID của document cần xóa.

**Ví dụ:**
```json
DELETE /example_index/_doc/1
```

- **Kết quả**:
  - Document có ID `1` trong index `example_index` sẽ bị xóa.
  - Elasticsearch sẽ gắn cờ "đã xóa" cho document thay vì loại bỏ ngay lập tức khỏi bộ lưu trữ, và document sẽ được loại bỏ hoàn toàn sau một quá trình gọi là **segment merging**.

---

#### **2. Xóa nhiều documents bằng Query**
- Sử dụng **Delete by Query API** để xóa tất cả các documents thỏa mãn một truy vấn (query).
- **Sử dụng khi**: Bạn cần xóa nhiều documents dựa trên một điều kiện.

**Ví dụ:**
```json
POST /example_index/_delete_by_query
{
  "query": {
    "match": {
      "status": "inactive"
    }
  }
}
```

- **Kết quả**:
  - Tất cả các documents trong `example_index` có `status` là `"inactive"` sẽ bị xóa.
  - **Lưu ý quan trọng**: Việc xóa bằng query có thể ảnh hưởng đến hiệu suất nếu số lượng documents lớn.

---

#### **3. Xóa toàn bộ documents trong một index**
- Thay vì xóa từng document, bạn có thể **xóa toàn bộ các documents** trong một index bằng cách sử dụng **Delete by Query API** với query `match_all`.
- **Sử dụng khi**: Bạn muốn làm trống index mà không xóa index.

**Ví dụ:**
```json
POST /example_index/_delete_by_query
{
  "query": {
    "match_all": {}
  }
}
```

- **Kết quả**:
  - Tất cả documents trong `example_index` sẽ bị xóa.

---

#### **4. Xóa toàn bộ index**
- Sử dụng **Delete Index API** để xóa toàn bộ index, bao gồm cả documents và cấu hình liên quan.
- **Sử dụng khi**: Bạn muốn loại bỏ hoàn toàn index khỏi Elasticsearch.

**Ví dụ:**
```json
DELETE /example_index
```

- **Kết quả**:
  - Index `example_index` cùng tất cả các documents và cấu hình của nó sẽ bị xóa.

---

### **Những lưu ý khi sử dụng Delete Documents**

1. **Xóa không thể hoàn tác**:
   - Khi document bị xóa, bạn không thể khôi phục nó trừ khi bạn đã có bản sao lưu hoặc đã bật tính năng **snapshot**.

2. **Hiệu suất khi dùng `_delete_by_query`**:
   - **Delete by Query API** có thể gây tải lớn lên cluster nếu bạn xóa số lượng lớn documents.
   - Elasticsearch sẽ phải quét qua các segments để tìm các documents phù hợp với query.

3. **Xóa không đồng bộ**:
   - Việc xóa document không loại bỏ ngay lập tức tài liệu khỏi bộ lưu trữ. Thay vào đó, Elasticsearch gắn cờ "đã xóa", và tài liệu sẽ được loại bỏ hoàn toàn trong quá trình **segment merging**.

4. **Xóa toàn bộ index**:
   - Nếu bạn sử dụng **Delete Index API**, toàn bộ cấu hình và dữ liệu của index sẽ bị xóa. Hãy cẩn thận khi sử dụng lệnh này.

5. **Xử lý lỗi khi xóa**:
   - Nếu bạn cố gắng xóa một document không tồn tại, Elasticsearch sẽ trả về trạng thái `404 Not Found` nhưng không gây lỗi nghiêm trọng.

---

### **So sánh các phương pháp xóa**

| **Phương pháp**           | **Sử dụng khi nào**                                  | **Ưu điểm**                                | **Hạn chế**                              |
|---------------------------|---------------------------------------------------|-------------------------------------------|------------------------------------------|
| **Xóa bằng ID**           | Khi bạn biết chính xác ID của document cần xóa.    | Nhanh, đơn giản.                          | Chỉ xóa được một document tại một thời điểm. |
| **Xóa bằng Query**        | Khi cần xóa nhiều documents dựa trên điều kiện.    | Linh hoạt, áp dụng cho nhiều documents.   | Ảnh hưởng hiệu suất nếu số lượng lớn.    |
| **Xóa toàn bộ documents** | Khi cần làm trống index nhưng giữ lại cấu hình.    | Giữ nguyên index và mappings.            | Không loại bỏ được cấu hình index.       |
| **Xóa toàn bộ index**     | Khi cần xóa toàn bộ index và dữ liệu liên quan.    | Xóa triệt để dữ liệu và cấu hình.         | Không thể khôi phục index nếu không có backup. |

---

### **Tài liệu tham khảo**
- [Delete API](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-delete.html)
- [Delete by Query API](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-delete-by-query.html)
- [Delete Index API](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-delete-index.html)

### **Câu hỏi 1: Khi xóa nhầm một document, tôi có bản sao lưu thì tôi sẽ khôi phục nó như thế nào?**

Khi một document bị xóa nhầm nhưng bạn có bản sao lưu (trong snapshot hoặc một nơi khác), bạn có thể khôi phục nó bằng cách **reindex (tái lập chỉ mục)** từ bản sao lưu.

#### **Ví dụ minh họa:**

- **Bước 1: Tìm document trong bản sao lưu**
  Giả sử bạn đã lưu document sau vào một file hoặc snapshot:
  ```json
  {
    "_id": "1",
    "index": "example_index",
    "document": {
      "title": "Khôi phục tài liệu",
      "content": "Đây là một tài liệu đã bị xóa nhầm."
    }
  }
  ```

- **Bước 2: Reindex document (khôi phục lại)**
  Sử dụng API `PUT` để khôi phục document từ bản sao lưu:

  ```json
  PUT /example_index/_doc/1
  {
    "title": "Khôi phục tài liệu",
    "content": "Đây là một tài liệu đã bị xóa nhầm."
  }
  ```

- **Kết quả:**
  Document có ID `1` sẽ được khôi phục vào index `example_index`.

---

### **Câu hỏi 2: Xóa không đồng bộ - Tôi đang hiểu rằng document bị xóa mềm kiểu `is_deleted = true`, khi search thì trường sẽ loại bỏ. Tôi hiểu như vậy có đúng không? Khi nào tài liệu sẽ được loại bỏ hoàn toàn? Có cách nào để xóa document ngay lập tức không?**

#### **1. Xóa không đồng bộ là gì?**
- Khi một document bị xóa, Elasticsearch không thực sự xóa nó khỏi bộ lưu trữ ngay lập tức. 
- Thay vào đó, document sẽ được **gắn cờ là "đã xóa"**. Điều này có nghĩa là:
  - **Khi search**: Document sẽ không còn hiển thị trong kết quả tìm kiếm.
  - **Thực tế**: Dữ liệu vẫn tồn tại trong các **segment** và sẽ được loại bỏ hoàn toàn trong **quá trình hợp nhất segment (segment merging)**.

#### **2. Document sẽ bị loại bỏ hoàn toàn khi nào?**
- Elasticsearch sử dụng **segment merging** để tối ưu hóa bộ lưu trữ.
- Trong quá trình này:
  - Các đoạn dữ liệu nhỏ (segments) được hợp nhất lại.
  - Những document đã bị gắn cờ là "đã xóa" sẽ bị loại bỏ hoàn toàn khỏi index.

#### **3. Có cách nào để xóa ngay lập tức không?**
- Không có cách nào để xóa "ngay lập tức" một document khỏi các segments đang tồn tại.
- Tuy nhiên, bạn có thể **bắt buộc hợp nhất segment** để loại bỏ document nhanh hơn bằng cách sử dụng API `_forcemerge`:

  **Ví dụ:**
  ```json
  POST /example_index/_forcemerge?only_expunge_deletes=true
  ```

  - Tùy chọn `only_expunge_deletes=true` sẽ chỉ loại bỏ các document đã bị gắn cờ là "đã xóa".

---

### **Câu hỏi 3: Việc xóa toàn bộ index sử dụng Delete Index API là xóa như thế nào? Có ngay lập tức không? Có thể khôi phục không (chẳng hạn mình đã có bản sao)?**

#### **1. Việc xóa toàn bộ index bằng Delete Index API**
- Khi sử dụng API `DELETE` để xóa index, toàn bộ:
  - **Documents** (tài liệu).
  - **Mappings** (cấu trúc dữ liệu).
  - **Settings** (cấu hình index).
  
  sẽ bị xóa khỏi Elasticsearch.

**Ví dụ:**
```json
DELETE /example_index
```

#### **2. Xóa có ngay lập tức không?**
- **Có**, việc xóa toàn bộ index diễn ra ngay lập tức.
- Sau khi xóa, bạn không thể truy cập lại vào index hoặc các documents trong index đó.

#### **3. Có thể khôi phục index không?**
- **Có**, nhưng chỉ khi bạn đã tạo **snapshot (bản sao lưu)** trước khi xóa index.
- Elasticsearch hỗ trợ **Snapshot and Restore**, cho phép bạn khôi phục index từ bản sao lưu.

**Ví dụ:**
- Khôi phục index từ snapshot:
```json
POST /_snapshot/my_backup/snapshot_1/_restore
{
  "indices": "example_index"
}
```

Trong đó:
- `my_backup`: Tên của repository chứa snapshot.
- `snapshot_1`: Tên của snapshot bạn muốn khôi phục.
- `indices`: Tên của index cần khôi phục (trong trường hợp này là `example_index`).

#### **Lưu ý khi khôi phục:**
- Nếu bạn không có snapshot, bạn sẽ không thể khôi phục index.
- Hãy luôn tạo **snapshot** định kỳ để tránh mất dữ liệu quan trọng.

---

### **Tóm tắt**

| **Hành động**                           | **Có thể khôi phục không?** | **Ghi chú**                                                                                              |
|-----------------------------------------|-----------------------------|----------------------------------------------------------------------------------------------------------|
| **Xóa 1 document (Delete by ID)**       | Có, nếu có bản sao lưu.     | Khôi phục bằng cách reindex lại document từ bản sao lưu hoặc snapshot.                                   |
| **Xóa nhiều document (Delete by Query)**| Có, nếu có bản sao lưu.     | Hạn chế sử dụng nếu không có bản sao lưu trước đó.                                                       |
| **Xóa toàn bộ index (Delete Index API)**| Có, nếu có snapshot.        | Snapshot là cách duy nhất để khôi phục lại index sau khi xóa.                                           |
| **Loại bỏ hoàn toàn document**          | Không ngay lập tức.         | Document sẽ bị loại bỏ hoàn toàn trong **quá trình hợp nhất segment** hoặc sử dụng API `_forcemerge`.    |

Nếu bạn cần thêm ví dụ hoặc giải thích chi tiết hơn, hãy cho tôi biết nhé!