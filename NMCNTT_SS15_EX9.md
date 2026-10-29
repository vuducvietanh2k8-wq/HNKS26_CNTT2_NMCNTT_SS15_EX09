# THIẾT KẾ HỆ THỐNG LƯU TRỮ GOOGLE DRIVE CHO DỰ ÁN CAMPUSPAY

## PHẦN 1: PHÂN TÍCH BÀI TOÁN (I/O)

### 1. Xác định đầu vào và đầu ra

#### Đầu vào (Input)

* **Vai trò người dùng:**

  * Ban Kỹ thuật: 3 người.
  * Ban Thiết kế: 2 người.
  * Ban Kinh doanh: 2 người.
  * Nhà đầu tư tiềm năng: 1 người bên ngoài.
* **Các cấp độ tài liệu:** Tài liệu riêng của từng ban, tài liệu chia sẻ liên ban và tài liệu mật.
* **Yêu cầu bảo mật:** Tối thiểu quyền truy cập, hạn chế rò rỉ dữ liệu, kiểm soát quyền chỉnh sửa, lưu lịch sử phiên bản và tự động hết hạn quyền nhà đầu tư sau 14 ngày.

#### Đầu ra (Output)

* Cấu trúc thư mục Google Drive hai cấp rõ ràng, dễ quản lý.
* Bảng phân quyền cho 4 vai trò và 5 loại tài liệu chính.
* Quy trình cấp quyền, kiểm tra, cập nhật và thu hồi quyền truy cập.

### 2. Đề xuất cấu trúc thư mục Google Drive

```text
CampusPay/
├── 01_Ban-Ky-Thuat/
├── 02_Ban-Thiet-Ke/
├── 03_Ban-Kinh-Doanh/
├── 04_Tai-Lieu-Chia-Se/
└── 05_Tai-Lieu-Mat/
```

| Thư mục                | Nội dung                                                       |
| ---------------------- | -------------------------------------------------------------- |
| `01_Ban-Ky-Thuat/`     | Mã nguồn, tài liệu API, kết quả kiểm thử và cấu hình kỹ thuật. |
| `02_Ban-Thiet-Ke/`     | UI/UX, bản thiết kế, hình ảnh và tài sản đồ họa.               |
| `03_Ban-Kinh-Doanh/`   | Business Plan, kế hoạch kinh doanh và thông tin đối tác.       |
| `04_Tai-Lieu-Chia-Se/` | Business Summary, Slide Pitch và tài liệu cộng tác liên ban.   |
| `05_Tai-Lieu-Mat/`     | API Keys, thông tin khách hàng và dữ liệu nhạy cảm.            |

**Nguyên tắc bảo mật:**

* Thư mục riêng chỉ cấp quyền cho thành viên của ban tương ứng.
* Thư mục chia sẻ cấp quyền theo nhiệm vụ.
* Thư mục chứa dữ liệu nhạy cảm phải đặt chế độ `Restricted`.
* Tuyệt đối không sử dụng `Anyone with the link` đối với tài liệu mật.

### 3. Các bước thiết lập hệ thống

1. **Tạo thư mục:** Tạo thư mục gốc `CampusPay` và 5 thư mục con theo cấu trúc đã đề xuất.
2. **Đặt tên:** Sử dụng tiền tố số và tên thư mục ngắn gọn, thống nhất.
3. **Tổ chức tài liệu:** Chuyển tài liệu vào đúng thư mục; không lưu API Keys hoặc thông tin khách hàng trong thư mục chia sẻ.
4. **Cấp quyền:** Thêm đúng tài khoản vào từng thư mục; cấp `Editor` cho người cần chỉnh sửa và `Viewer` cho người chỉ cần xem.
5. **Cấu hình bảo mật:** Đặt tài liệu mật ở chế độ `Restricted`; giới hạn quyền tải xuống, in hoặc sao chép khi cần.
6. **Thiết lập phiên bản:** Lưu tài liệu quan trọng trên Google Drive và sử dụng `Version History` để theo dõi, khôi phục phiên bản.
7. **Cấp quyền nhà đầu tư:** Chỉ chia sẻ Business Summary và Slide Pitch; đặt ngày hết hạn quyền truy cập sau 14 ngày.
8. **Kiểm tra:** Kiểm tra bằng từng tài khoản để xác nhận không ai truy cập vượt phạm vi công việc.
9. **Thu hồi và cập nhật:** Khi thành viên nghỉ việc hoặc kết thúc nhiệm vụ, thu hồi quyền truy cập và rà soát các liên kết chia sẻ liên quan.

## PHẦN 2: TRIỂN KHAI VÀ CHỐNG LỖI

### 4. Bảng phân quyền hoàn chỉnh

**Quy ước:**

* `Editor`: Có quyền xem và chỉnh sửa.
* `Viewer`: Chỉ xem nội dung.
* `Commenter`: Có quyền xem và bình luận.
* `Restricted`: Chỉ tài khoản được cấp quyền mới có thể truy cập.
* `—`: Không cấp quyền.

| Loại tài liệu                  | Ban KT  | Ban TK | Ban KD | Nhà đầu tư | Ghi chú bảo mật                                                                                      |
| ------------------------------ | ------- | ------ | ------ | ---------- | ---------------------------------------------------------------------------------------------------- |
| Mã nguồn, tài liệu API         | Editor  | —      | —      | —          | Chỉ Ban Kỹ thuật được chỉnh sửa; không lưu API Keys trực tiếp trong tài liệu chia sẻ.                |
| Thiết kế UI/UX, tài sản đồ họa | Viewer* | Editor | —      | —          | Ban Thiết kế quản lý; Ban Kỹ thuật chỉ xem các tệp cần thiết để tích hợp giao diện.                  |
| Business Plan                  | Viewer  | —      | Editor | —          | Tài liệu mật; Ban Kỹ thuật chỉ được xem để hiểu bối cảnh, không được sửa.                            |
| Business Summary, Slide Pitch  | Viewer  | Viewer | Editor | Viewer     | Cho phép cộng tác có kiểm soát; nhà đầu tư chỉ được xem tài liệu được chọn.                          |
| API Keys, thông tin khách hàng | —       | —      | —      | —          | Chỉ người được ủy quyền riêng mới được truy cập; đặt `Restricted`, không chia sẻ liên kết công khai. |

* Ban Kỹ thuật chỉ được cấp `Viewer` đối với các tệp thiết kế cần thiết cho công việc, không cấp quyền toàn bộ thư mục nếu không cần.

**Lưu ý:**

* Bảng trên thể hiện quyền theo vai trò.
* Với tài liệu nhạy cảm, cần cấp quyền ở đúng phạm vi tệp hoặc thư mục và kiểm tra quyền kế thừa từ thư mục cha.
* Nếu nhà đầu tư chỉ cần tải bản PDF, nên chia sẻ bản PDF xuất riêng thay vì tài liệu trình chiếu gốc có quyền chỉnh sửa.

### 5. Giải quyết ba bẫy dữ liệu

#### Bẫy 1: Chia sẻ Business Plan với Ban Kỹ thuật nhưng không cho sửa

**Quyền cần cấp:** `Viewer`.

**Cách thực hiện:**

1. Chia sẻ trực tiếp tệp Business Plan với tài khoản của các thành viên Ban Kỹ thuật cần đọc.
2. Không cấp quyền `Editor` cho Ban Kỹ thuật đối với tài liệu này.
3. Giữ chế độ `Restricted` nếu tài liệu chứa thông tin nhạy cảm.

**Lý do:** Quyền `Viewer` cho phép đọc nội dung nhưng không cho phép chỉnh sửa trực tiếp, đáp ứng nguyên tắc tối thiểu quyền truy cập.

#### Bẫy 2: Nhà đầu tư cần tải PDF Slide Pitch để thuyết trình offline

**Quyền cần cấp:** `Viewer` đối với bản PDF được xuất riêng.

**Có cần tắt tính năng `Viewers can download, print, and copy` không?**

* **Không**, nếu nhà đầu tư cần tải PDF về máy để sử dụng offline.
* Cần cho phép tải xuống để đáp ứng nhu cầu sử dụng.
* Quyền `Viewer` không cho phép chỉnh sửa trực tiếp tài liệu gốc.
* Tuy nhiên, bản PDF đã tải xuống vẫn có thể được chỉnh sửa bằng phần mềm khác; không thể đảm bảo tuyệt đối rằng người nhận không sửa được bản sao.

**Cách làm an toàn:**

1. Xuất Slide Pitch thành PDF.
2. Chỉ chia sẻ bản PDF cho nhà đầu tư.
3. Không chia sẻ tệp trình chiếu gốc có quyền chỉnh sửa.
4. Không đưa API Keys hoặc thông tin khách hàng vào bản PDF.

#### Bẫy 3: Một thành viên nghỉ việc, cần chặn truy cập ngay lập tức

**Các thao tác cần thực hiện:**

1. Thu hồi quyền chia sẻ trực tiếp của thành viên trên các thư mục và tệp CampusPay.
2. Xóa thành viên khỏi các Google Groups được dùng để cấp quyền dự án, nếu có.
3. Kiểm tra các tệp được chia sẻ riêng, liên kết và quyền sở hữu mà thành viên đó có liên quan.
4. Nếu tài khoản thuộc tổ chức do quản trị viên quản lý, yêu cầu quản trị viên tạm ngưng tài khoản hoặc thu hồi quyền truy cập tổ chức theo quy trình nội bộ.
5. Kiểm tra lại quyền truy cập sau khi thu hồi.
6. Chuyển quyền sở hữu tài liệu công việc cho người tiếp nhận khi cần.

