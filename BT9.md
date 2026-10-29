# Phần 1: Phân tích bài toán (I/O)

## **1.**
*   **Đầu vào (Inputs):**
    *   Các vai trò người dùng: Ban Kỹ thuật (3), Ban Thiết kế (2), Ban Kinh doanh (2), Nhà đầu tư (1).
    *   Cấp độ tài liệu: Dữ liệu nhạy cảm (API Keys, Mã nguồn), Dữ liệu nội bộ nhóm (UI, Business Plan gốc), Dữ liệu cộng tác chéo (Business Plan xem chéo, Slide Pitch).
*   **Đầu ra (Outputs):**
    *   Cấu trúc thư mục Google Drive 2 cấp phân tách rõ ràng.
    *   Bảng ma trận phân quyền dựa trên nguyên tắc "Đặc quyền tối thiểu" (Least Privilege).

## **2. Đề xuất giải pháp**
### Cấu trúc Google Drive sẽ được tổ chức theo mô hình: **Thư mục dự án gốc ➔ Các thư mục ban/nhóm chuyên trách**. 
*   📁 **CampusPay_Project_Master** (Thư mục gốc)
    *   📁 **01_Tech_Internal**: Chứa mã nguồn, API Keys, tài liệu hệ thống (Chỉ Ban KT).
    *   📁 **02_Design_Internal**: Chứa file thiết kế UI, assets đồ họa (Ban TK quản lý).
    *   📁 **03_Business_Internal**: Chứa tài chính, kế hoạch kinh doanh nội bộ (Ban KD quản lý).
    *   📁 **04_Cross_Collaboration**: Tài liệu chung (Slide Pitch, Guideline) (Các ban cùng truy cập).
    *   📁 **05_Investor_Share**: Bản sao chép đã đóng gói (PDF) dành riêng cho đối tác ngoài.

## **3. Thiết kế các bước cấu hình tuần tự**
*   **Bước 1 - Tạo & Đặt tên:** Trưởng nhóm tạo cấu trúc thư mục 2 cấp chuẩn hóa, đặt tên có đánh số (01, 02...) để dễ kiểm soát.
*   **Bước 2 - Phân quyền thư mục gốc (Root):** Không share toàn bộ thư mục gốc cho tất cả mọi người. Set mặc định là `Restricted`.
*   **Bước 3 - Cấp quyền chi tiết (Access Permissions):** Thêm trực tiếp địa chỉ Email của từng thành viên vào đúng thư mục ban của họ với quyền `Editor`. Các thư mục cần xem chéo cấp quyền `Viewer`.
*   **Bước 4 - Cấu hình bảo mật:** Tắt tính năng tải về (Viewers can download) đối với các tài liệu nội bộ nhạy cảm và thiết lập `Expiration Date` cho người ngoài.
*   **Bước 5 - Kiểm tra & Thu hồi định kỳ (Access Audit):** Trưởng nhóm test link ẩn danh để đảm bảo không rò rỉ. Sẵn sàng thu hồi quyền hoặc chuyển nhượng (Transfer Ownership) khi có biến động nhân sự.

---

# Phần 2: Triển khai & Chống lỗi

## **4. Bảng phân quyền hoàn chỉn**

| Loại tài liệu | Ban KT (3) | Ban TK (2) | Ban KD (2) | Nhà đầu tư (1) | Ghi chú bảo mật (Security Notes) |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **1. Mã nguồn & API Keys** | **Editor** | Không (None) | Không (None) | Không (None) | `Restricted` toàn bộ. Tuyệt đối không share ra ngoài Ban KT. |
| **2. Kế hoạch Kinh doanh (Business Plan)** | **Viewer** | Không (None) | **Editor** | **Viewer** | `Restricted`. Đặt giới hạn thời gian tự động hủy (Expiration Date) 14 ngày cho Nhà đầu tư. |
| **3. Slide Pitch (Gọi vốn)** | Viewer | **Editor** | **Editor** | **Viewer** | Ban TK & KD là `Editor` để cùng làm nội dung/hình ảnh. NĐT hết hạn sau 14 ngày. |
| **4. Tài sản đồ họa UI/UX** | **Viewer** | **Editor** | Viewer | Không (None) | Ban KT xem UI để code, Ban KD xem để lấy tư liệu làm slide. |
| **5. Tài liệu vận hành chung (Rules, Guideline)** | Viewer | Viewer | Viewer | Không (None) | Trưởng nhóm là `Editor`, toàn bộ thành viên là `Viewer` để tuân thủ luật chung. |

## **5. Giải quyết các "Bẫy dữ liệu" (Ràng buộc hệ thống)**

*   **Bẫy 1 (Kinh doanh share Business Plan cho Kỹ thuật lấy context):**
    *   **Quyền sẽ cấp:** `Viewer` (Người xem).
    *   **Lý do:** Đảm bảo nguyên tắc đặc quyền tối thiểu. Ban Kỹ thuật chỉ cần đọc (nhìn qua cửa kính) để lấy context lập trình, tuyệt đối không được phép chỉnh sửa nội dung tài liệu mật của Ban Kinh doanh. Chế độ chia sẻ bắt buộc là `Restricted` (Thêm chính xác email của 3 bạn kỹ thuật), không dùng "Anyone with the link".

*   **Bẫy 2 (Nhà đầu tư muốn tải PDF Slide Pitch offline nhưng không được sửa):**
    *   **Quyền sẽ cấp:** `Viewer` (Người xem). 
    *   **Tính năng tải về:** **KHÔNG TẮT** tính năng *"Viewers and commenters can see the option to download"*.
    *   **Lý do:** Quyền `Viewer` đã đáp ứng yêu cầu "không cho bản chỉnh sửa được". Việc giữ lại nút Tải về (Download) là hợp lý vì phục vụ đúng nhu cầu chính đáng (thuyết trình offline) của nhà đầu tư. Để an toàn, cần đặt thêm thời hạn `Expiration Date` tự động thu hồi quyền sau 14 ngày.

*   **Bẫy 3 (Thành viên nghỉ việc - Chặn truy cập tức thì):**
    *   **Thao tác 1 (Chuyển quyền):** Nếu thành viên đó tự tạo các file quan trọng trong thư mục chung, trưởng nhóm phải yêu cầu hoặc chủ động sử dụng tính năng **Transfer Ownership** (Chuyển nhượng quyền chủ sở hữu) tài liệu đó sang cho trưởng nhóm.
    *   **Thao tác 2 (Xóa quyền):** Vào mục Share, tìm email của nhân viên đó và chọn **Remove Access** (Xóa quyền) khỏi thư mục gốc và các thư mục liên quan ngay lập tức. Thao tác này có hiệu lực realtime (tức thì), người đó sẽ bị văng ra khỏi file nếu đang mở.