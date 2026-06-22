# BÁO CÁO THỰC HÀNH KIỂM THỬ TỰ ĐỘNG VỚI SELENIUM

> **Học phần:** Kiểm thử phần mềm  
> **Công cụ:** Selenium WebDriver, Python, Pytest  
> **Website kiểm thử:** Hệ thống quản lý chương trình đào tạo UniMIS  
> **Môi trường chạy:** Windows + Docker + Google Chrome  
> **Sinh viên thực hiện:** [Điền họ tên]  
> **Repository:** [Dán link GitHub repository tại đây]

---

## 1. Mục tiêu bài thực hành

Bài thực hành nhằm tìm hiểu và áp dụng Selenium WebDriver để tự động hóa thao tác trên trình duyệt. Các kịch bản kiểm thử được xây dựng cho website UniMIS, tập trung vào những chức năng thường dùng trên trang quản trị như đăng nhập, quản lý môn học, quản lý chương trình đào tạo, tìm kiếm dữ liệu và kiểm tra quyền truy cập.

Selenium WebDriver được sử dụng để điều khiển trình duyệt như người dùng thực hiện: mở trang, nhập dữ liệu, nhấn nút, chờ phần tử xuất hiện và kiểm tra kết quả. Pytest được dùng để tổ chức, chạy và thống kê kết quả các test case.

---

## 2. Công cụ và môi trường

| Thành phần | Sử dụng |
|---|---|
| Ngôn ngữ lập trình | Python |
| Công cụ tự động hóa | Selenium WebDriver |
| Framework chạy test | Pytest |
| Trình duyệt | Google Chrome |
| Ứng dụng kiểm thử | UniMIS chạy bằng Django và PostgreSQL |
| Môi trường triển khai | Docker Compose |
| Hệ điều hành | Windows |

Cài đặt các thư viện cần thiết:

```bash
pip install selenium pytest pytest-html
```

Chạy hệ thống UniMIS bằng Docker:

```bash
docker compose up -d
```

Chạy toàn bộ kiểm thử Selenium:

```bash
pytest s_tests -v
```

Có thể xuất báo cáo HTML bằng lệnh:

```bash
pytest s_tests -v --html=selenium_report.html --self-contained-html
```

---

## 3. Cấu trúc thư mục kiểm thử

```text
selenium_tests/
│
├── s_tests/
│   ├── test_login.py
│   ├── test_course.py
│   ├── test_curriculum.py
│   ├── test_add_course_to_curriculum.py
│   ├── test_major.py
│   ├── test_school.py
│   ├── test_permission.py
│   └── test_search_filter.py
│
├── conftest.py
├── requirements.txt
└── README.md
```

Các test được chia theo từng nhóm chức năng để dễ bảo trì và mở rộng.

---

## 4. Các test case tiêu biểu

Bài tập yêu cầu tối thiểu 03 test case. Trong bài thực hành này đã xây dựng nhiều test case hơn yêu cầu. Dưới đây là các test case đại diện.

| Mã TC | Chức năng | Các bước chính | Kết quả mong đợi | Trạng thái |
|---|---|---|---|---|
| TC01 | Đăng nhập thành công | Mở trang đăng nhập, nhập đúng tài khoản quản trị và mật khẩu, nhấn đăng nhập | Hệ thống chuyển đến trang quản trị Django | PASS |
| TC02 | Đăng nhập sai mật khẩu | Nhập đúng tên đăng nhập nhưng sai mật khẩu | Hệ thống hiển thị thông báo đăng nhập không thành công | PASS |
| TC03 | Tạo môn học thành công | Truy cập chức năng môn học, nhập mã môn học, tên môn học, số tín chỉ và lưu | Môn học mới xuất hiện trong danh sách | PASS |
| TC04 | Kiểm tra bắt buộc khi tạo môn học | Bỏ trống các trường bắt buộc rồi lưu | Hệ thống yêu cầu nhập các trường bắt buộc | PASS |
| TC05 | Tìm kiếm môn học tồn tại | Nhập mã/tên môn học đã có vào ô tìm kiếm | Danh sách hiển thị đúng môn học cần tìm | PASS |
| TC06 | Tạo chương trình đào tạo | Nhập ngành, năm áp dụng và các thông tin hợp lệ | Chương trình đào tạo được tạo thành công | PASS |
| TC07 | Không cho phép tạo trùng chương trình đào tạo | Tạo lại chương trình có cùng ngành và năm áp dụng | Hệ thống không tạo bản ghi trùng | PASS |
| TC08 | Thêm môn học vào chương trình đào tạo | Chọn chương trình đào tạo, chọn môn học và lưu | Môn học được gắn vào chương trình đào tạo | PASS |
| TC09 | Xuất PDF chương trình đào tạo | Mở chương trình đào tạo và chọn xuất PDF | Hệ thống tạo hoặc tải tệp PDF | PASS |
| TC10 | Kiểm tra quyền truy cập | Truy cập trang tạo môn học khi chưa đăng nhập | Người dùng bị chuyển về trang đăng nhập | PASS |

---

## 5. Ví dụ mã nguồn kiểm thử

Ví dụ kiểm thử đăng nhập thành công:

```python
def test_login_success(driver):
    driver.get("http://localhost:8000/admin/login/")

    driver.find_element(By.NAME, "username").send_keys("admin")
    driver.find_element(By.NAME, "password").send_keys("mat_khau")

    driver.find_element(By.CSS_SELECTOR, "input[type='submit']").click()

    assert "Trang quản trị cho Django" in driver.page_source
```

Trong các test thực tế, nhóm sử dụng `WebDriverWait` để chờ phần tử sẵn sàng trước khi thao tác. Cách này giúp hạn chế lỗi do trang tải chậm hoặc phần tử chưa hiển thị.

---

## 6. Kết quả chạy kiểm thử

Kết quả từ ảnh chạy Pytest:

| Chỉ số | Kết quả |
|---|---:|
| Tổng số test case | 41 |
| Test PASS | 40 |
| Test FAILED | 1 |
| Tỷ lệ PASS | 97,56% |

Kết quả cho thấy hầu hết các chức năng được kiểm thử hoạt động đúng theo kỳ vọng. Các nhóm chức năng đã chạy thành công gồm:

- Đăng nhập với dữ liệu đúng, sai hoặc để trống.
- Quản lý trường, ngành, môn học và chương trình đào tạo.
- Tìm kiếm môn học, ngành và chương trình đào tạo.
- Thêm môn học vào chương trình đào tạo.
- Xuất PDF chương trình đào tạo.
- Kiểm tra phân quyền và chuyển hướng về trang đăng nhập.

### Lỗi còn lại

| Test bị lỗi | Mô tả |
|---|---|
| `test_create_faculty_empty_required_fields` | Test kiểm tra việc bỏ trống trường bắt buộc khi tạo khoa đang FAILED. Cần kiểm tra lại locator, thông báo validation hiển thị trên giao diện hoặc điều kiện assert trong mã test. |

Lỗi này không làm ảnh hưởng đến các test case còn lại, tuy nhiên cần được xử lý trước khi kết luận bộ kiểm thử hoàn toàn ổn định.

---

## 7. Hình ảnh minh họa

Đặt ảnh kết quả chạy test vào thư mục `evidence/` của repository, ví dụ:

```text
evidence/
└── selenium-test-result.png
```

Sau đó chèn ảnh vào README:

```markdown
![Kết quả chạy Selenium](evidence/selenium-test-result.png)
```

> Ảnh minh họa cho thấy Pytest đã chạy các kịch bản Selenium; đa số test có trạng thái `PASSED`, còn một test kiểm tra tạo khoa với trường bắt buộc đang `FAILED`.

---

## 8. Nhận xét

Việc áp dụng Selenium giúp tự động hóa các thao tác lặp lại trên giao diện web, đặc biệt phù hợp với các chức năng CRUD, kiểm tra dữ liệu đầu vào, tìm kiếm và phân quyền. So với kiểm thử thủ công, bộ test có thể chạy lại nhiều lần sau khi sửa mã nguồn, từ đó hỗ trợ kiểm thử hồi quy.

Trong quá trình thực hiện, một số vấn đề thường gặp là phần tử chưa tải xong, locator thay đổi, hoặc test kiểm tra nội dung hiển thị không khớp hoàn toàn với giao diện tiếng Việt. Các vấn đề này được giảm thiểu bằng cách sử dụng chờ tường minh (`WebDriverWait`), locator ổn định và kiểm tra chính xác thông báo kỳ vọng.

---

## 9. Tài liệu đã tham khảo

1. Selenium Documentation – WebDriver và Selenium IDE.  
2. Pytest Documentation – cách tổ chức, chạy và báo cáo kiểm thử.  
3. Tài liệu hướng dẫn thực hành Selenium của học phần Kiểm thử phần mềm.

---

## 10. Hướng dẫn nộp bài

1. Tạo GitHub Repository mới, ví dụ: `selenium-unimis-testing`.
2. Đưa mã nguồn kiểm thử, tệp `requirements.txt`, thư mục ảnh minh họa và `README.md` vào repository.
3. Commit và push lên GitHub.
4. Dán link repository vào nơi nộp bài và Google Sheet cùng với bài Postman.

Ví dụ lệnh Git:

```bash
git init
git add .
git commit -m "Add Selenium automated test report"
git branch -M main
git remote add origin <LINK_REPOSITORY_CUA_BAN>
git push -u origin main
```
