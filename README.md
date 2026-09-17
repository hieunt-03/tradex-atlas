# TradeX Atlas — System overview & Business flows

## Mở và trình chiếu

Mở `index.html` bằng Chrome, Edge hoặc Safari. Không cần Internet, không cần khởi chạy backend, không cần npm để sử dụng. Three.js đã nằm trong `atlas.bundle.js`; không tải từ CDN.

Khi sao chép sang máy khác, giữ các file `index.html`, `lessons.html`, `style.css`, `atlas.bundle.js`, `lessons.bundle.js`, `THIRD_PARTY_LICENSES.txt` trong cùng thư mục. Có thể mang theo README này. Các file nguồn và thư mục node_modules chỉ cần cho người chỉnh sửa, không cần cho người trình chiếu.

## Hai trang riêng

- `index.html`: **Quần đảo TradeX**. Một vùng biển 3D chứa 9 hòn đảo, mỗi hòn đảo là một luồng nghiệp vụ. Bấm vào đảo để hạ cánh: mỗi thành phần là một công trình riêng (nhà, tháp, hải đăng, cối xay, cầu gỗ, nhà ga, thùng chứa, đảo mây…), không dùng chung một khối hộp.
- Mỗi luồng có địa chỉ riêng bằng hash, ví dụ `index.html#trade`, `index.html#market`. Hash là tên thành phần (`index.html#redis`) sẽ mở luồng đầu tiên chứa thành phần đó và chọn sẵn công trình tương ứng.
- `lessons.html`: bản đọc chi tiết cho cùng 9 luồng (`lessons.html#trade`). Riêng `lessons.html#market` có bản đồ tương tác và bảng tra cứu 13 thành phần dữ liệu của bảng giá. Dùng khi cần đọc nhanh hoặc trên máy không có WebGL.

Bố cục trang quần đảo: sidebar danh sách luồng theo 3 chương, khung 3D ở giữa, sổ tay ghi chú bên phải (Hướng dẫn · Khái niệm · Thành phần · Câu hỏi BA) và thanh điều chỉnh khung cảnh bên dưới.

## Có gì trong mô hình?

- 15 service nội bộ với hồ sơ riêng: trách nhiệm, đầu vào, đầu ra, dữ liệu liên quan, ranh giới, ví dụ, lỗi cần lưu ý, câu hỏi BA và nguồn đối chiếu.
- 39 khối gồm 15 service, 13 market data component, 2 tác nhân, 4 nhóm đối tác và 5 thành phần hỗ trợ. Nhóm hỗ trợ có Kafka, Redis, MongoDB, dữ liệu quan hệ và thư viện dùng chung.
- 9 hành trình, 40 bước: tổng quan, đăng nhập, mở tài khoản, bảng giá, lệnh thường, lệnh điều kiện, tài khoản/tiền, Copy Trading, thông báo.
- Giao diện tối màu rừng, dựng hình low-poly. Giữ tên chuẩn Redis, Kafka, MongoDB và tên service; phần giải thích nghiệp vụ bằng tiếng Việt.

## Tương tác

- Kéo nền 3D để xoay; cuộn hoặc dùng +/− để phóng to; kéo chuột phải để di chuyển. Trên màn hình cảm ứng: một ngón xoay, hai ngón thu/phóng và di chuyển.
- Ở **quần đảo**: bấm vào một hòn đảo hoặc nhãn số để mở luồng. `↩ Về quần đảo` hoặc phím Esc để quay lại.
- Trong một **đảo luồng**: bấm công trình, nhãn số hoặc thẻ dưới bản đồ để mở tab **Thành phần** (trách nhiệm, nhận/trả, ranh giới, kết nối trong luồng, nguồn đối chiếu). `◎ Bay tới` đưa camera tới công trình đó.
- **Hướng dẫn:** từng bước của luồng, kèm câu hỏi “Lượt của bạn” và điểm cốt lõi. ← → để đổi bước; camera tự khung lại các công trình tham gia bước hiện tại.
- **Khái niệm:** từ điển của luồng — 63 khái niệm, tổng cộng. Mỗi mục có tiêu đề tiếng Việt (“Cấu hình cách đăng nhập”, “Bốn con số tài sản dễ nhầm”…), nhãn tên kỹ thuật để tra cứu (`login_method`, `buyingPower`, `StopOrderStatusEnum`…), và phần giải thích viết bằng lời cho BA: ai làm gì, khách hàng thấy gì, hai thứ nào dễ nhầm. Không có đường dẫn file hay tên hàm trong phần giải thích; những giá trị BA cần nhớ (danh sách trạng thái, các loại bản tin, mã trường hiển thị trên màn hình) được viết thành câu. Cuối mỗi mục là một dòng cảnh báo “code không chứng minh được gì” và liên kết tới đúng `file:line` cho ai muốn mở mã nguồn.
- **Chạy hành trình:** tự chuyển bước mỗi 9 giây. Đổi luồng, chọn công trình hoặc ẩn tab sẽ dừng.
- Đường bay sáng + mũi tên + gói hàng chuyển động chỉ hướng dữ liệu của bước hiện tại; nhãn trên đường bay là ý nghĩa của quan hệ đó. Các công trình ngoài bước hiện tại chuyển sang màu xỉn và nhãn thu lại thành chấm số.
- **Điều chỉnh khung cảnh:** bật/tắt nhãn, bật/tắt gói tin, đổi tốc độ gói tin và góc nhìn.
- Nếu WebGL không khả dụng hoặc mất context, trang tự chuyển sang danh sách 2D với cùng nội dung.
- Các tab ghi chú hỗ trợ ← →. Tôn trọng cài đặt giảm chuyển động: nước, mây, gói tin và chuyển cảnh camera đứng yên.
- Toàn màn hình dùng tính năng của trình duyệt; nếu không hỗ trợ, dùng lệnh toàn màn hình của ứng dụng trình duyệt.

## Kịch bản 20–30 phút

1. **Toàn cảnh (3 phút):** phân biệt người sử dụng, TradeX, đối tác và nền tảng.
2. **Gateway → AAA → configuration (3 phút):** ai xác thực, ai cấp quyền, ai định tuyến?
3. **eKYC (3 phút):** phân biệt hồ sơ, tài khoản, hợp đồng và tiểu khoản.
4. **Bảng giá (4 phút):** 13 thành phần dữ liệu — Quote, Bid / Offer, SymbolInfo, product types, các nhánh riêng và Minute bar.
5. **Lệnh thường → lệnh điều kiện (5 phút):** tiếp nhận không đồng nghĩa khớp; điều kiện có vòng đời riêng.
6. **Copy Trading (3 phút):** danh mục mẫu, tài sản subscriber, tính khối lượng, gửi lệnh và lưu kết quả.
7. **Vận hành → thông báo (3 phút):** chiến dịch, kênh gửi, trạng thái gửi/đã đọc.
8. Chọn một công trình và dùng tab **Thành phần** để thảo luận tiêu chí nghiệm thu, tình huống lỗi và đối soát.

Mỗi mục tương ứng một hòn đảo: mở đảo, bấm **Chạy hành trình** để trình chiếu tự động, dừng lại ở bước cần thảo luận.

## Phạm vi và lưu ý về bằng chứng

Mô hình dựa trên mã và tài liệu trong workspace, không xác minh runtime production. Nguồn cụ thể nằm trong catalog.js, glossary.js và các tab **Khái niệm** / **Thành phần**; liên kết chỉ hoạt động khi giữ thư mục system-tour trong workspace gốc.

Các đường là quan hệ logic, có thể gom gateway/Kafka/kho dữ liệu. Nhóm kho quan hệ không có nghĩa tất cả service dùng chung một database. Thư viện không phải service chạy độc lập. Mũi tên/chấm chuyển động biểu diễn hướng, không phải thời gian hoặc tốc độ thực tế.

Điểm được hiệu chỉnh so với tài liệu tổng quan cũ:

- ws-v2 nhận dữ liệu Kafka trực tiếp, không mặc định chờ realtime-v2.
- Lệnh thường và lệnh điều kiện tách nhánh; stop order thực thi theo cấu hình Lotte/Tuxedo.
- Copy Trading có tính riêng cho subscriber, điều chỉnh theo tiền khả dụng và gửi qua đích cấu hình; không xác nhận luôn qua lotte-bridge.
- nhsv-admin hiện được đối chiếu ở phần Copy Trading và quản trị Push. Tài liệu cũ ghi luồng margin nhưng các lớp tương ứng chưa được tìm thấy trong checkout hiện tại, nên không thể hiện là chức năng đã xác minh.
- Lịch sử/đã đọc thông báo không tập trung toàn bộ ở notification.
- Khả năng đọc lịch sử không bảo đảm kho lưu đầy đủ mọi tick/phút.
- Các ví dụ/trình tự giải thích/câu hỏi BA không thay thế chính sách sản phẩm hoặc tên trạng thái trong database.

Không có credential, địa chỉ server nội bộ, dữ liệu cá nhân hay API giao dịch thật. Chính sách của trang chặn các kết nối mạng. Thư viện Three.js dùng giấy phép MIT trong THIRD_PARTY_LICENSES.txt.

## Dành cho người chỉnh sửa

- app.js: dữ liệu hành trình nền.
- catalog.js: hồ sơ từng thành phần, quan hệ và hành trình bổ sung.
- notes.js: 3 chương, mục tiêu học và điểm cần phân biệt của từng luồng; dùng chung cho cả hai trang.
- glossary.js: từ điển khái niệm theo luồng (term, giải thích cho BA, cấu trúc dữ liệu, nguồn `file:line`, cảnh báo phạm vi). Sửa trực tiếp file này rồi build lại.
- island3d.js: bộ dựng hình low-poly — đảo, cây cối, bến thuyền, biển, mây, đường mòn, gói hàng và một công trình riêng cho từng thành phần.
- partner-logos.js: logo Lotte và FPT.eContract do người dùng cung cấp, nhúng trực tiếp để hai mô hình đối tác vẫn hiển thị khi chạy offline.
- world.js: cảnh Three.js — camera, điều khiển, quần đảo, đảo luồng, đường bay có mũi tên, nhãn nổi và khung hình theo từng bước.
- atlas.js: điều khiển trang quần đảo — sidebar, sổ tay ghi chú, chú giải, hash routing và 2D fallback.
- lessons.html / lessons.js: bản đọc chi tiết, sơ đồ SVG theo từng bài, giải thích, checkpoint, nguồn và bài trước/sau.
- market-flow.js: 13 thành phần dữ liệu, 6 bước giải thích và hai nhánh service pipeline của bài Xem bảng giá.
- style.css: giao diện tối màu rừng, responsive.
- atlas.bundle.js / lessons.bundle.js: các bản đóng gói dùng trực tiếp khi mở HTML.

Sau khi đổi mã JS: chạy `npm ci` nếu chưa có dependencies, rồi `npm run build`.
Kiểm tra nội dung/quan hệ/nguồn: `npm run check`.
Không cần đưa nội dung nội bộ lên hosting.
