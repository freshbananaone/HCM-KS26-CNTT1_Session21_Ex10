# BÀI 10: ĐÁNH GIÁ ĐA GIẢI PHÁP QUY TRÌNH KHẮC PHỤC SỰ CỐ KỸ THUẬT

## Phần 1: So sánh hai phương án xử lý sự cố

### Câu 1. Bảng so sánh Trade-off

| Tiêu chí | Phương án 1: Hỗ trợ cảm tính qua nhóm chat | Phương án 2: Troubleshooting 4 chặng |
|---|---|---|
| **1. Thời gian xử lý sự cố (Downtime)** | Có thể kéo dài vì kỹ thuật viên phải chờ Senior Dev đọc tin nhắn hoặc nghe máy. | Kỹ thuật viên tự kiểm tra và tra cứu ngay. Các lỗi đơn giản có thể xử lý trong 5–10 phút; nếu không được thì chuyển cấp sau 15 phút. Mục tiêu rút ngắn 80% cần được đo bằng số liệu thực tế. |
| **2. Áp lực và chi phí cho Senior Devs** | Rất cao vì Senior thường xuyên phải trả lời các lỗi cơ bản, dễ bị gián đoạn khi đang phát triển phần mềm. | Giảm bớt việc lặp lại. Senior chủ yếu nhận những lỗi khó đã có thông tin, nhật ký và các bước thử xử lý rõ ràng. |
| **3. Năng lực và tính tự chủ của kỹ thuật viên** | Dễ trở nên thụ động, chỉ biết chụp ảnh gửi nhóm và chờ người khác giúp. | Rèn được kỹ năng đọc lỗi, tìm tài liệu, phân tích nguyên nhân và tự xử lý. Khi mất mạng vẫn có thể dùng tài liệu hướng dẫn được lưu sẵn. |
| **4. Khả năng lưu trữ kiến thức (Knowledge Base)** | Tin nhắn dễ bị trôi, khó tìm lại, người sau có thể tiếp tục gặp đúng lỗi cũ. | Các lỗi và cách xử lý được ghi vào Wiki hoặc kho tài liệu nội bộ để mọi người cùng tra cứu, cập nhật. |

**Đánh đổi:** Phương án 1 đơn giản, nhanh để nhờ người hỗ trợ lúc đầu nhưng càng nhiều trạm thì càng dễ quá tải. Phương án 2 cần thời gian đào tạo và xây dựng tài liệu, đổi lại có thể giảm phụ thuộc vào Senior và làm việc ổn định hơn về lâu dài.

### Câu 2. Nên chọn phương án nào khi mở rộng lên 1.000 trạm sạc?

Theo em, VoltCharge nên chọn **phương án 2 – Troubleshooting 4 chặng**. Khi công ty có đến 1.000 trạm, nếu lỗi nào cũng gọi cho Senior thì bộ phận kỹ thuật sẽ không thể hỗ trợ kịp, còn khách hàng phải chờ lâu.

Mỗi giờ một trụ sạc ngừng hoạt động gây thiệt hại **500.000 đồng**. Vì vậy, tự giải quyết sớm những lỗi đơn giản sẽ giúp giảm thời gian gián đoạn và chi phí. Ngoài ra, Senior có thêm thời gian phát triển tính năng mới, còn kỹ thuật viên ở các trạm ngày càng chủ động hơn.

Em cũng nghĩ nên kết hợp quy trình này với **Wiki nội bộ và hướng dẫn xử lý được lưu ngoại tuyến**, để khi mất mạng vẫn biết các bước kiểm tra cơ bản. Với lỗi nguy hiểm liên quan đến điện, quá nhiệt hoặc an toàn của người dùng thì phải dừng vận hành và báo ngay cho bộ phận phụ trách, không cố thử đủ 15 phút.

## Phần 2: Sơ đồ luồng ra quyết định gỡ lỗi

### Câu 3. Sơ đồ Decision Flowchart

```text
[START: Kiosk xuất hiện thông báo lỗi]
                |
                v
[Đảm bảo an toàn, ghi lại lỗi và tình trạng máy]
                |
                v
{Có dấu hiệu nguy hiểm về điện, nhiệt hoặc an toàn?}
       | CÓ                         | KHÔNG
       v                             v
[Dừng vận hành, báo ngay]      [CHẶNG 1: GOOGLE]
       |                        - Chọn từ khóa chính
       v                        - Dùng site:, "...", -
     [END]                           |
                                     v
                           {Tìm được kết quả phù hợp?}
                              | CÓ            | KHÔNG
                              v                |
                    [CHẶNG 2: ĐỌC NGUỒN]       |
                    - Đọc lướt trong 30 giây   |
                    - Xem tích xanh, Upvote    |
                    - Kiểm tra độ mới, phiên bản|
                    - Chỉ thử giải pháp an toàn |
                              |                |
                              v                |
                      {Đã sửa được?}            |
                       | CÓ   | KHÔNG           |
                       v       |                |
                 [Ghi vào Wiki] |                |
                       |       +----------------+
                       v                        |
                     [END]                      v
                                    [CHẶNG 3: HỎI AI]
                                    - Context: thiết bị, hệ điều hành,
                                      thao tác đang làm
                                    - Raw Error: lỗi nguyên văn,
                                      đã che thông tin bí mật
                                    - Instruction: giải thích tiếng Việt
                                      và đề xuất cách xử lý an toàn
                                               |
                                               v
                                    [Kiểm tra và thử tối đa 2
                                     giải pháp phù hợp, được phép]
                                               |
                                               v
                                         {Đã sửa được?}
                                      | CÓ        | KHÔNG
                                      v            v
                                [Ghi vào Wiki] {Đã đến mốc 15 phút
                                      |          từ lúc bắt đầu?}
                                      v           | CÓ     | CHƯA
                                    [END]         v         v
                                          [CHẶNG 4: BUG REPORT] [Tiếp tục kiểm tra
                                          - Bối cảnh môi trường    có kiểm soát
                                          - Cách tái hiện lỗi      trong thời gian
                                          - Kết quả mong đợi       còn lại]
                                            và thực tế                 |
                                          - Log đã che dữ liệu         +--> [Đánh giá lại]
                                            nhạy cảm, việc đã thử          (quay về kiểm tra)
                                                 |
                                                 v
                                          [Gửi IT Helpdesk cấp 2]
                                                 |
                                                 v
                                                [END]
```

**Giải thích 4 chặng:**

**Chặng 1 – Tìm Google:** Khi gặp lỗi, em đọc thông báo và lấy vài từ quan trọng để tìm kiếm. Ví dụ:

```text
"UnauthorizedAccessException" "VoltCharge"
site:stackoverflow.com "Access to the path" "is denied"
site:github.com "ControllerDaemon" -tutorial
```

- `site:` giới hạn tìm kiếm trong một website.
- `"..."` tìm đúng cụm từ được đặt trong dấu ngoặc kép.
- `-` loại bỏ kết quả có từ không muốn tìm.

**Chặng 2 – Đọc StackOverflow/GitHub Issues:** Em đọc lướt khoảng 30 giây để xem bài có đúng lỗi đang gặp không. Tích xanh `✓` (câu trả lời được chấp nhận) và nhiều Upvote là dấu hiệu tham khảo tốt, nhưng vẫn phải đối chiếu phiên bản phần mềm, ngày đăng và bình luận. Không chạy lệnh lạ chỉ vì có nhiều lượt thích.

**Chặng 3 – Nhờ AI giải thích:** Nếu chưa hiểu lỗi tiếng Anh, em dùng Prompt 3 thành phần gồm **Context – Raw Error – Instruction**. AI có thể giúp giải thích và gợi ý hướng xử lý, nhưng em phải tự kiểm tra tính đúng đắn, chỉ thử tối đa hai cách phù hợp và không đưa mật khẩu, API Key hoặc mã OTP vào chat.

**Chặng 4 – Gửi Bug Report:** Nếu lỗi vẫn chưa được giải quyết khi đã đến mốc **15 phút kể từ khi bắt đầu xử lý**, em gửi báo cáo cho IT Helpdesk thay vì tự đoán tiếp. Phiếu báo lỗi gồm 4 phần:

1. **Context:** Trụ sạc số mấy, hệ điều hành, phiên bản ứng dụng và thời điểm xảy ra lỗi.
2. **Steps to Reproduce:** Các thao tác dẫn đến lỗi để người khác có thể kiểm tra lại.
3. **Expected vs. Actual:** Kết quả mong đợi là gì, thực tế đã xảy ra chuyện gì.
4. **Evidence & Attempts:** Ảnh chụp/log đã che dữ liệu nhạy cảm và các cách đã thử nhưng chưa thành công.

