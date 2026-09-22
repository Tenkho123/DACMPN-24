# DACMPN-24

## Tài liệu dự án
- [Xem Requirement Outline (RO)](https://drive.google.com/drive/u/0/folders/1JpALwpWWb9QYQpeqq1AwARhUL7bqrZ2U)
- [Xem Sơ đồ Quy trình & Trạng thái (Mermaid)](./docs/diagrams.md)

## Sơ đồ quy trình Upload và Bóc tách File 3D
```mermaid
graph TD
    A[Provider: Tải lên file 3D <br> GLB/GLTF] --> B{Hệ thống Validate <br> Kích thước, Định dạng, Lỗi}
    
    B -- Không hợp lệ --> C[Báo lỗi & Yêu cầu tải lại]
    
    B -- Hợp lệ --> D[Hệ thống tự động Parse file <br> GLTF/GLB]
    D --> E[Trích xuất danh sách Nodes/Meshes <br> thành linh kiện độc lập]
    
    E --> F[Provider: Tạo các Bước lắp ráp]
    F --> G[Provider: Kéo thả/Gán linh kiện <br> vào từng bước tương ứng]
    G --> H[Provider: Thêm Text/Audio mô tả]
    
    H --> I[Lưu thành bản Nháp - Draft]
```

## Sơ đồ Vòng đời Trạng thái Bài Hướng Dẫn
```mermaid
stateDiagram-v2
    [*] --> Draft : Tạo mới bài viết
    Draft --> Submitted : Gửi yêu cầu duyệt (Submit)
    
    state "Quy trình Kiểm duyệt (Admin)" as Review {
        Submitted --> Approved : Chấp thuận
        Submitted --> Rejected : Từ chối (Kèm lý do)
    }
    
    Rejected --> Draft : Provider chỉnh sửa lại
    
    Approved --> Published : Provider Xuất bản
    
    Published --> Hidden : Tạm ẩn (Ngừng publish)
    Hidden --> Published : Mở lại
    
    note right of Published
        - Hệ thống sinh mã QR tại bước này.
        - Mã QR link theo ID bài viết.
    end note
    
    Published --> Draft_V2 : Chỉnh sửa bài đã Publish (Sinh version mới)
    Draft_V2 --> Submitted : Gửi duyệt lại version mới
    
    note right of Draft_V2
        Sau khi version mới được Approved & Published,
        QR code cũ vẫn giữ nguyên, trỏ về version mới.
    end note
```

## Sơ đồ Xử lý Fallback trải nghiệm Guest trên Mobile
```mermaid
graph TD
    A[Guest/Customer Quét mã QR Code] --> B[Truy cập WebApp trên Mobile]
    
    B --> C{Kiểm tra thiết bị & Trình duyệt <br> Có hỗ trợ WebGL / GPU đủ mạnh?}
    
    C -- Hỗ trợ tốt --> D[Tải Viewer 3D Three.js]
    D --> E[Hiển thị mô hình 3D tương tác <br> Xoay, Zoom, Highlight]
    
    C -- WebGL Yếu / Bị tắt --> F[Kích hoạt chế độ Fallback 2D]
    F --> G[Hiển thị danh sách các bước dạng Ảnh tĩnh / Text / Video ngắn]
    
    E --> H{Thời gian tải 3D > 5s?}
    H -- Đúng --> I[Hiển thị nút: 'Chuyển sang chế độ xem nhẹ 2D']
    H -- Sai --> J[Trải nghiệm mượt mà]
```

## Sơ đồ Xử lý Quota & Tài khoản SaaS
```mermaid
graph TD
    A[Provider đăng ký / Sử dụng dịch vụ] --> B{Kiểm tra Quota <br> Dung lượng & Số bài}
    
    B -- Dưới hạn mức --> C[Cho phép tạo & Xuất bản bài viết 3D]
    
    B -- Đạt/Vượt hạn mức --> D[Cảnh báo Vượt Quota <br> trên Dashboard & Email]
    D --> E[Khóa tính năng Xuất bản bài mới]
    E --> F{Provider xử lý?}
    
    F -- Nâng cấp gói cước --> G[Mở khóa ngay lập tức & Mở rộng Quota]
    F -- Xóa bớt bài/dữ liệu cũ --> C
    
    A --> H{Kiểm tra Chu kỳ Thanh toán}
    H -- Thanh toán thành công --> C
    H -- Thanh toán thất bại/Hết hạn --> I[Chuyển sang Trạng thái Ân hạn - 7 ngày]
    I --> J{Sau 7 ngày chưa thanh toán?}
    
    J -- Đã thanh toán --> C
    J -- Chưa thanh toán --> K[Khóa tài khoản Provider & <br> Chuyển View 3D sang dạng Fallback 2D]
```
