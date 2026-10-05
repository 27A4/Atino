# Atino – Tài liệu nghiệp vụ ERP (Odoo)
**Chuỗi: Thiết kế → Mua → Sản xuất / Gia công → Phân phối → Bán lẻ & Online → Kế toán**

Phiên bản 1.0, bản nháp để duyệt (Bước 1/4). Các bước sau: file BPMN (đã kèm), hướng dẫn setup Odoo 20, kịch bản demo.

---

## 1. Mục tiêu và phạm vi

Atino là chuỗi thời trang có cửa hàng (offline) và bán online. Tài liệu này mô tả **quy trình xuyên suốt giữa các phòng ban**, mỗi hành động (nhập hàng, xuất hàng, duyệt nhập nguyên liệu…) là một bước có người chịu trách nhiệm, chứng từ và màn hình Odoo tương ứng.

Phạm vi: cả chuỗi (10 quy trình P00–P09). Sản phẩm **không cố định một loại**: áo, quần, váy, phụ kiện đều dùng chung khung quy trình; khác nhau ở biến thể (size, màu), định mức NVL (BoM) và nguồn cung.

Ba nguồn cung của một mã hàng:

| Nguồn cung | Ý nghĩa | Chạy qua |
|---|---|---|
| Tự sản xuất | Xưởng Atino cắt–may–hoàn thiện | P02, P03, P04 |
| Gia công ngoài | Xưởng thuê làm (khi thiếu năng lực hoặc không kịp hạn) | P02, P03 (NVL cấp), P05 |
| Mua sẵn | Phụ kiện, hàng thương mại mua về bán | P02, P03 |

## 2. Nguyên tắc vận hành ERP xuyên suốt

1. **Một dữ liệu, nhập một lần.** Sản phẩm, BoM, giá, NCC khai báo ở P01, các phòng ban sau chỉ dùng lại, không nhập lại.
2. **Không có chứng từ thì không có nghiệp vụ.** Mua hàng có PO, nhập kho có phiếu nhập, xuất kho có phiếu xuất, bán hàng có đơn/hóa đơn, thu chi có bút toán.
3. **Mỗi bước có đúng một người chịu trách nhiệm** (phòng ban ở cột «Phòng ban» của từng quy trình).
4. **Người tạo không phải người duyệt.** PO lớn, gia công ngoài, đổi trả ngoại lệ, giảm giá vượt mức, khóa kỳ đều cần cấp trên duyệt trên hệ thống.
5. **Kho là nguồn sự thật về tồn.** Tồn chỉ thay đổi bằng phiếu nhập, xuất, điều chuyển, điều chỉnh, không sửa số tay.
6. **QC là cổng chặn.** NVL và thành phẩm chưa QC đạt thì chưa vào kho khả dụng.
7. **Khớp ba bên trước khi trả tiền:** PO – phiếu nhập – hóa đơn.
8. **Cuối kỳ khóa sổ.** Sau khóa kỳ chỉ điều chỉnh bằng bút toán kỳ sau.

## 3. Phòng ban và vai trò

| Phòng ban | Trách nhiệm chính | Quyền duyệt | App Odoo chính |
|---|---|---|---|
| Ban Giám đốc | Duyệt giá, kế hoạch ra mã, PO lớn, gia công ngoài, báo cáo kỳ | Có | Tất cả (xem), Approvals |
| Merchandising – Kế hoạch | Kế hoạch bộ sưu tập, phân bổ hàng, dự báo nhu cầu | Không | Inventory, Sales |
| Thiết kế – R&D | Mẫu, tech pack, định mức NVL, BoM | Duyệt mẫu | Manufacturing, PLM tùy chọn |
| Mua hàng | RFQ, báo giá, PO, làm việc với NCC và xưởng gia công | PO trong hạn mức | Purchase |
| Kho (NVL & thành phẩm) | Nhập, xuất, điều chuyển, kiểm kê | Xác nhận nhập/xuất | Inventory, Barcode |
| Xưởng sản xuất | Cắt, may, hoàn thiện theo lệnh SX | Không | Manufacturing, Shop Floor |
| QC | Kiểm tra NVL đầu vào, thành phẩm, QC tại xưởng gia công | Quyết định Pass/Fail | Quality |
| Kế toán | Hóa đơn, công nợ, giá thành, đối soát, đóng kỳ | Duyệt chi | Accounting |
| Cửa hàng (nhân viên, quản lý) | Bán lẻ, đổi trả, đóng ca | Quản lý duyệt đổi trả, giảm giá lớn | Point of Sale |
| CSKH / Bán hàng online | Xác nhận đơn online, xử lý hủy/đổi | Không | Sales, eCommerce |
| Vận chuyển | Giao hàng đến cửa hàng và khách | Không | Delivery connector |
| Xưởng gia công ngoài, NCC | Bên ngoài, làm việc qua PO và email/portal | – | Portal |

## 4. Bản đồ quy trình và file BPMN

| Mã | Quy trình | File BPMN |
|---|---|---|
| P00 | Tổng thể chuỗi giá trị | `P00_Tong_the_chuoi_gia_tri.bpmn` |
| P01 | Thiết lập sản phẩm, định mức, giá | `P01_Thiet_lap_san_pham_va_dinh_muc.bpmn` |
| P02 | Mua NVL: đề xuất → báo giá → duyệt → đặt hàng | `P02_Mua_NVL_de_xuat_duyet_dat_hang.bpmn` |
| P03 | Nhập kho NVL, QC, thanh toán NCC | `P03_Nhap_kho_NVL_QC_thanh_toan_NCC.bpmn` |
| P04 | Kế hoạch & sản xuất nội bộ, quyết định gia công | `P04_San_xuat_noi_bo_va_quyet_dinh_gia_cong.bpmn` |
| P05 | Gia công ngoài | `P05_Gia_cong_ngoai.bpmn` |
| P06 | Phân bổ & điều chuyển kho → cửa hàng | `P06_Phan_phoi_kho_den_cua_hang.bpmn` |
| P07 | Bán lẻ tại cửa hàng (POS) | `P07_Ban_le_tai_cua_hang_POS.bpmn` |
| P08 | Bán online | `P08_Ban_hang_online.bpmn` |
| P09 | Kế toán: giá thành, đối soát, đóng kỳ | `P09_Ke_toan_gia_thanh_doi_soat_dong_ky.bpmn` |

Luồng nối giữa các quy trình:

```
P01 ──► P02 ──► P03 ──► P04 ─────────────┐
         ▲        ▲      │ (thiếu NVL)   │
         └────────┴──────┤               ▼
                         └──► P05 (gia công) ──► P06 ──► P07 (cửa hàng)
                                                    └──► P08 (online)
                                    P03/P05/P07/P08 ──► P09 (đóng kỳ)
```

## 5. Chứng từ và trạng thái chính

| Chứng từ | Trạng thái | Người tạo → người duyệt/xác nhận |
|---|---|---|
| RFQ / PO mua | Draft → Sent → To Approve → Purchase Order → Locked/Cancelled | Mua hàng → Trưởng phòng / BGĐ (theo hạn mức) |
| Phiếu nhập (Receipt) | Draft → Ready → Done (hoặc Backorder) | Hệ thống → Kho |
| Quality Check | To do → Pass / Fail | Hệ thống → QC |
| Lệnh sản xuất (MO) | Draft → Confirmed → In Progress → Done / Cancelled | Kế hoạch SX → Xưởng |
| PO gia công | như PO mua; kèm MO gia công tự tạo | Mua hàng → BGĐ |
| Phiếu điều chuyển / giao hàng | Draft → Waiting → Ready → Done | Kho |
| Đơn POS | Draft → Paid → Posted / Refunded | Nhân viên bán hàng → Quản lý (đổi trả) |
| Đơn website (Sales Order) | Quotation → Sales Order → Locked | Khách → CSKH |
| Hóa đơn mua / bán | Draft → Posted → Paid | Kế toán |
| Phiên POS | Opening → In Progress → Closing → Closed | Quản lý |
| Kỳ kế toán | Mở → Khóa (Lock Date) | Kế toán → BGĐ |

---

## 6. Chi tiết từng quy trình

Cách đọc bảng: **Bước** đánh số theo thứ tự cột trên sơ đồ; cột **Chuyển tới** cho biết bước kế tiếp và điều kiện rẽ nhánh. Cột **Odoo** là màn hình sẽ cấu hình ở Bước 3 (tên menu có thể chỉnh theo Odoo 20).


### P00 – Tổng thể chuỗi giá trị Atino (Thiết kế → Mua → Sản xuất → Bán → Kế toán)

- **Mục tiêu:** Cho thấy toàn bộ chuỗi và các phòng ban bàn giao cho nhau như thế nào; mỗi bước lớn là một quy trình con (P01–P09).
- **Khởi đầu:** Bắt đầu mùa hàng / kỳ kế hoạch mới.
- **Phòng ban tham gia:** Khách hàng, Kinh doanh (Cửa hàng & Online), Merchandising – Kế hoạch, Mua hàng, Kho, Sản xuất (nội bộ & gia công), QC, Kế toán, Ban Giám đốc
- **File sơ đồ:** `P00_Tong_the_chuoi_gia_tri.bpmn`

| Bước | Phòng ban | Hành động | Chứng từ / kết quả | Odoo | Chuyển tới |
|---|---|---|---|---|---|
| B1 | Merchandising – Kế hoạch | **Bắt đầu:** Bắt đầu mùa hàng / kỳ kế hoạch | – | – | B2 |
| B2 | Merchandising – Kế hoạch | Thiết lập sản phẩm, BoM, giá (P01) | Mã sản phẩm, BoM, bảng giá | Inventory, Manufacturing, Sales | B3 |
| B3 | Ban Giám đốc | Phê duyệt kế hoạch ra mã & giá | Quyết định duyệt | Activity trên sản phẩm | B4 |
| B4 | Merchandising – Kế hoạch | Dự báo nhu cầu & lập kế hoạch cung ứng | Kế hoạch cung ứng | Inventory › Replenishment; Manufacturing › Planning | B5 |
| B5 | Mua hàng | Mua NVL / hàng mua sẵn (P02) | PO đã xác nhận | Purchase | B6 |
| B6 | Kho | Nhập kho & kiểm tra NVL (P03) | Phiếu nhập, kết quả QC đầu vào | Inventory, Quality | B7 |
| B7 | Sản xuất (nội bộ & gia công) | Sản xuất nội bộ hoặc gia công ngoài (P04, P05) | Lệnh SX / PO gia công | Manufacturing, Subcontracting | B8 |
| B8 | QC | QC thành phẩm | Kết quả kiểm tra AQL | Quality | B9 |
| B9 | Kho | Nhập kho thành phẩm & phân phối (P06) | Phiếu nhập TP, phiếu điều chuyển | Inventory | B10 |
| B10 | Khách hàng | Mua hàng tại cửa hàng / website | Đơn hàng | POS, eCommerce | B11 |
| B11 | Kinh doanh (Cửa hàng & Online) | Bán hàng, giao hàng, thu tiền (P07, P08) | Hóa đơn bán, phiếu giao | Point of Sale, Sales, eCommerce | B12 |
| B12 | Kế toán | Ghi nhận doanh thu, thanh toán NCC | Bút toán, thanh toán | Accounting | B13 |
| B13 | Kế toán | Giá thành, đối soát, đóng kỳ (P09) | Giá thành, báo cáo | Accounting | B14 |
| B14 | Ban Giám đốc | Xem xét báo cáo kết quả kinh doanh | Quyết định điều hành | Accounting › Reporting | B15 |
| B15 | Merchandising – Kế hoạch | **Quyết định:** Cần bổ sung hàng? | Dựa trên tồn kho và doanh số | – | Có: B4; Không: B16 |
| B16 | Merchandising – Kế hoạch | **Kết thúc:** Kết thúc kỳ | – | – | – |

**Kiểm soát và ngoại lệ:**

- Mỗi ô là một quy trình con, xem file P01–P09 tương ứng.
- Vòng lặp «Cần bổ sung hàng?» đưa dữ liệu bán hàng quay lại kế hoạch cung ứng, đây là điểm khác biệt chính của ERP so với làm tay.

### P01 – Thiết lập sản phẩm, định mức (BoM) và giá

- **Mục tiêu:** Mỗi mã hàng chỉ được bán/sản xuất/mua khi đã có đủ: sản phẩm + biến thể, nguồn cung, BoM (nếu tự làm/gia công), giá mua, giá bán, giá vốn.
- **Khởi đầu:** Có bộ sưu tập hoặc mã hàng mới.
- **Phòng ban tham gia:** Merchandising – Kế hoạch, Thiết kế – R&D, Mua hàng, Kế toán, Ban Giám đốc
- **File sơ đồ:** `P01_Thiet_lap_san_pham_va_dinh_muc.bpmn`

| Bước | Phòng ban | Hành động | Chứng từ / kết quả | Odoo | Chuyển tới |
|---|---|---|---|---|---|
| B1 | Merchandising – Kế hoạch | **Bắt đầu:** Có mã hàng / bộ sưu tập mới | – | – | B2 |
| B2 | Merchandising – Kế hoạch | Lập đề xuất mã hàng & kế hoạch bộ sưu tập | Đề xuất mã hàng (SL, size, màu, kênh bán) | Inventory › Products (nháp) | B3 |
| B3 | Thiết kế – R&D | Thiết kế mẫu & lập tech pack | Tech pack, định mức vải-phụ liệu sơ bộ | Đính kèm tech pack vào sản phẩm / BoM nháp | B4 |
| B4 | Thiết kế – R&D | May mẫu, thử mẫu & chốt định mức NVL | Biên bản duyệt mẫu, định mức chốt | Manufacturing › Manufacturing Orders (lệnh mẫu) | B5 |
| B5 | Thiết kế – R&D | **Quyết định:** Mẫu đạt? | Đạt form, chất liệu, định mức | – | Không đạt: B3; Đạt: B6 |
| B6 | Merchandising – Kế hoạch | Chọn nguồn cung: tự SX / gia công / mua sẵn | Phân loại nguồn cung từng mã | Product › Inventory › Routes (Manufacture / Buy) | B7 |
| B7 | Mua hàng | Xin báo giá vải, phụ liệu, công gia công, hàng mua sẵn | Báo giá NCC, MOQ, lead time | Purchase › RFQ, Vendor Pricelist | B8 |
| B8 | Kế toán | Tính giá thành dự kiến & đề xuất giá bán | Giá thành dự kiến, giá bán đề xuất | Manufacturing › BoM Overview; Sales › Pricelists | B9 |
| B9 | Ban Giám đốc | Xem xét & phê duyệt giá, kế hoạch ra mã | Quyết định duyệt | Activity trên sản phẩm | B10 |
| B10 | Ban Giám đốc | **Quyết định:** Duyệt? | Giá, biên lợi nhuận, kế hoạch | – | Không duyệt: B6; Duyệt: B11 |
| B11 | Merchandising – Kế hoạch | **Song song:** Khởi tạo song song | – | – | B12; B13; B14; B15 |
| B12 | Merchandising – Kế hoạch | Tạo sản phẩm, biến thể size/màu, danh mục | Product template + variants, mã vạch | Inventory › Products; Attributes & Variants; Product Categories | B16 |
| B13 | Thiết kế – R&D | Tạo BoM & công đoạn (routing) | BoM loại Manufacture hoặc Subcontracting | Manufacturing › Bills of Materials; Operations; Work Centers | B16 |
| B14 | Mua hàng | Khai báo NCC & bảng giá mua | Giá mua, MOQ, lead time | Product › Purchase tab (Vendors) | B16 |
| B15 | Kế toán | Thiết lập nhóm kế toán & phương pháp tính giá vốn | Tài khoản kế toán, giá vốn | Product Category › Costing Method, Inventory Valuation, Accounts | B16 |
| B16 | Merchandising – Kế hoạch | **Song song:** Hoàn tất khởi tạo | – | – | B17 |
| B17 | Merchandising – Kế hoạch | Kích hoạt sản phẩm (bán tại POS / Website) | Sản phẩm sẵn sàng bán | Product › Sales: Available in POS, Website Published | B18 |
| B18 | Merchandising – Kế hoạch | **Kết thúc:** Sản phẩm sẵn sàng kinh doanh | – | – | – |

**Kiểm soát và ngoại lệ:**

- Cổng song song: 4 phòng ban khai báo cùng lúc, chỉ khi cả 4 xong mới được kích hoạt bán.
- Sản phẩm không có BoM (loại tự SX/gia công) hoặc không có giá mua (loại mua sẵn) thì không được kích hoạt.
- Mã hàng nhóm áo, quần, váy, phụ kiện dùng chung một khung: biến thể size/màu, BoM mẫu theo nhóm hàng.

### P02 – Mua nguyên phụ liệu: đề xuất, báo giá, duyệt, đặt hàng

- **Mục tiêu:** Mọi khoản mua đều đi từ nhu cầu có căn cứ → báo giá so sánh → duyệt theo hạn mức → PO có số tham chiếu.
- **Khởi đầu:** Tồn NVL dưới mức tối thiểu, hoặc lệnh sản xuất thiếu NVL.
- **Phòng ban tham gia:** Kế hoạch SX / Kho, Mua hàng, Ban Giám đốc, Nhà cung cấp
- **File sơ đồ:** `P02_Mua_NVL_de_xuat_duyet_dat_hang.bpmn`

| Bước | Phòng ban | Hành động | Chứng từ / kết quả | Odoo | Chuyển tới |
|---|---|---|---|---|---|
| B1 | Kế hoạch SX / Kho | **Bắt đầu:** Tồn NVL dưới mức tối thiểu hoặc lệnh SX thiếu NVL | – | – | B2 |
| B2 | Kế hoạch SX / Kho | Kiểm tra tồn kho & nhu cầu NVL | Danh sách NVL cần mua | Inventory › Replenishment; Reporting › Stock | B3 |
| B3 | Kế hoạch SX / Kho | Lập yêu cầu mua hàng (RFQ nháp) | RFQ nháp đóng vai trò yêu cầu mua | Inventory › Replenishment › Order (tạo RFQ nháp) | B4 |
| B4 | Mua hàng | Rà soát yêu cầu, gộp nhu cầu, chọn NCC | RFQ đã gộp | Purchase › Orders › Requests for Quotation | B5 |
| B5 | Mua hàng | Gửi RFQ cho 2–3 NCC | RFQ đã gửi | Purchase › Send by Email; Alternatives | B6 |
| B6 | Nhà cung cấp | Gửi báo giá (giá, MOQ, lead time) | Báo giá NCC | Email / Vendor portal | B7 |
| B7 | Mua hàng | So sánh báo giá, chốt NCC & tạo PO | PO nháp | Purchase › Alternatives (Compare) | B8 |
| B8 | Mua hàng | **Quyết định:** Giá trị PO vượt hạn mức duyệt? | PO lớn hơn hạn mức (giả định 50 triệu VND) | – | Vượt hạn mức: B9; Trong hạn mức: B11 |
| B9 | Ban Giám đốc | Xem xét & phê duyệt PO | PO chuyển trạng thái To Approve → Purchase Order | Purchase › Settings › Purchase Order Approval | B10 |
| B10 | Ban Giám đốc | **Quyết định:** Duyệt? | – | – | Không duyệt: B7; Duyệt: B11 |
| B11 | Mua hàng | Xác nhận PO & gửi NCC | PO đã xác nhận, phiếu nhập dự kiến tự tạo | Purchase › Confirm Order; Send PO | B12 |
| B12 | Nhà cung cấp | Xác nhận đơn & ngày giao | Ngày giao xác nhận | Email / Vendor portal | B13 |
| B13 | Kế hoạch SX / Kho | Theo dõi ngày giao, cập nhật kế hoạch SX | Lịch hàng về | Purchase › Receipt Date; Manufacturing › Planning | B14 |
| B14 | Kế hoạch SX / Kho | **Kết thúc:** Chuyển sang nhập kho NVL (P03) | – | – | – |

**Kiểm soát và ngoại lệ:**

- Odoo không có chứng từ PR riêng: dùng RFQ nháp (tự tạo từ Replenishment) làm yêu cầu mua.
- Cấm xác nhận PO nếu chưa đi qua bước so sánh báo giá (trừ NCC độc quyền, ghi chú lý do).
- NCC giao trễ hoặc đổi giá: Mua hàng cập nhật PO và báo Kế hoạch SX để xem lại lịch (có thể kích hoạt gia công ở P04).

### P03 – Nhập kho NVL, QC đầu vào, đối chiếu hóa đơn & thanh toán NCC

- **Mục tiêu:** Hàng chỉ vào kho khả dụng khi đủ chứng từ và đạt QC; chỉ thanh toán khi khớp ba bên PO – phiếu nhập – hóa đơn.
- **Khởi đầu:** NCC giao hàng đến kho.
- **Phòng ban tham gia:** Nhà cung cấp, Kho, QC, Mua hàng, Kế toán
- **File sơ đồ:** `P03_Nhap_kho_NVL_QC_thanh_toan_NCC.bpmn`

| Bước | Phòng ban | Hành động | Chứng từ / kết quả | Odoo | Chuyển tới |
|---|---|---|---|---|---|
| B1 | Nhà cung cấp | **Bắt đầu:** NCC giao hàng đến kho | – | – | B2 |
| B2 | Kho | Đối chiếu phiếu giao với PO, kiểm đếm số lượng | Số lượng thực nhận | Inventory › Receipts | B3 |
| B3 | Kho | **Quyết định:** Đủ số lượng, đúng mã hàng? | Sai lệch ≤ dung sai (giả định 2%) thì coi là đủ | – | Đủ: B5; Thiếu / sai: B4 |
| B4 | Mua hàng | Thương lượng NCC: nhận một phần hoặc giao bù | Quyết định nhận một phần / giao bù | Purchase › PO (chatter, ghi chú) | B5 |
| B5 | Kho | Tạo phiếu nhập kho, ghi số lượng thực nhận | Phiếu nhập (Ready), backorder nếu thiếu | Inventory › Receipts › Quantity; Backorder | B6 |
| B6 | QC | Kiểm tra chất lượng đầu vào (màu, khổ, co rút, lỗi) | Kết quả Pass / Fail | Quality › Quality Checks (Control Point trên Receipt) | B7 |
| B7 | QC | **Quyết định:** Đạt? | Theo tiêu chuẩn AQL của NVL | – | Đạt: B8; Không đạt: B9 |
| B8 | Kho | Xác nhận nhập kho (Validate Receipt) | Tồn kho tăng, bút toán tồn kho | Inventory › Validate | B10 |
| B9 | Mua hàng | Lập yêu cầu trả hàng, liên hệ NCC | Phiếu trả hàng | Inventory › Return (trên phiếu nhập) | B11 |
| B10 | Kho | Xếp hàng vào vị trí kho, dán mã vạch | Hàng có vị trí lưu | Inventory › Locations, Putaway Rules, Barcode | B12 |
| B11 | Mua hàng | **Kết thúc:** Chờ NCC giao bù | – | – | – |
| B12 | Kế toán | Nhận hóa đơn NCC, lập hóa đơn mua (Vendor Bill) | Vendor Bill (Draft) | Accounting › Vendors › Bills | B13 |
| B13 | Kế toán | **Quyết định:** Khớp PO – Phiếu nhập – Hóa đơn? | Số lượng và đơn giá khớp | – | Khớp: B15; Lệch: B14 |
| B14 | Mua hàng | Yêu cầu NCC điều chỉnh hoặc phát hành credit note | Credit note / hóa đơn mới | Accounting › Credit Note | Hóa đơn mới: B12 |
| B15 | Kế toán | Xác nhận hóa đơn, lên lịch thanh toán | Hóa đơn Posted, công nợ phải trả | Accounting › Bills › Confirm | B16 |
| B16 | Kế toán | Duyệt chi & thanh toán NCC | Phiếu chi, hóa đơn Paid | Accounting › Register Payment | B17 |
| B17 | Kế toán | **Kết thúc:** Đóng công nợ NCC | – | – | – |

**Kiểm soát và ngoại lệ:**

- Hàng chưa QC ở trạng thái chờ, không được dùng để sản xuất.
- Hàng không đạt chuyển về NCC bằng phiếu trả (Return), không xóa chứng từ.
- Bật kiểm soát hóa đơn theo SL thực nhận (Bill Control: Received quantities) để Odoo tự cảnh báo lệch.

### P04 – Lập kế hoạch & sản xuất nội bộ (kèm quyết định chuyển gia công)

- **Mục tiêu:** Xưởng Atino sản xuất theo lệnh; nếu thiếu NVL, thiếu công suất hoặc không kịp hạn giao thì chuyển một phần hoặc toàn bộ sang xưởng gia công ngoài (P05).
- **Khởi đầu:** Có nhu cầu thành phẩm: đơn sỉ, đơn online, bổ sung cửa hàng, hàng mùa vụ.
- **Phòng ban tham gia:** Kinh doanh / Merchandising, Kế hoạch SX, Kho, QC, Xưởng sản xuất, Ban Giám đốc
- **File sơ đồ:** `P04_San_xuat_noi_bo_va_quyet_dinh_gia_cong.bpmn`

| Bước | Phòng ban | Hành động | Chứng từ / kết quả | Odoo | Chuyển tới |
|---|---|---|---|---|---|
| B1 | Kinh doanh / Merchandising | **Bắt đầu:** Có nhu cầu thành phẩm (đơn sỉ, online, bổ sung CH, mùa vụ) | – | – | B2 |
| B2 | Kế hoạch SX | Lập kế hoạch SX & đánh giá năng lực (NVL, công suất, hạn giao) | Kế hoạch SX, ngày hoàn thành dự kiến | Manufacturing › Planning; Replenishment | B3 |
| B3 | Kế hoạch SX | **Quyết định:** Xưởng nội bộ đủ NVL, công suất & kịp hạn? | Đủ cả 3 điều kiện (xem mục quy tắc quyết định) | – | Đủ: B7; Thiếu: B4 |
| B4 | Ban Giám đốc | Xem xét phương án: gia công ngoài, tăng ca hoặc dời hạn | Phương án đề xuất | Activity trên lệnh / đơn hàng | B5 |
| B5 | Ban Giám đốc | **Quyết định:** Duyệt gia công ngoài? | Chi phí, chất lượng, hạn giao | – | Không duyệt: B2; Duyệt: B6 |
| B6 | Ban Giám đốc | **Song song:** Chia lệnh | – | – | Phần dư: B8; Phần nội bộ: B7 |
| B7 | Kế hoạch SX | Tạo & xác nhận lệnh sản xuất (MO) theo BoM | MO trạng thái Confirmed | Manufacturing › Manufacturing Orders › Confirm | B9 |
| B8 | Ban Giám đốc | **Kết thúc:** Phần dư chuyển gia công ngoài (P05) | – | – | – |
| B9 | Kho | Kiểm tra & giữ chỗ NVL | NVL được giữ chỗ cho MO | MO › Check Availability | B10 |
| B10 | Kho | **Quyết định:** Đủ NVL? | Theo giữ chỗ trên MO | – | Đủ: B12; Thiếu: B11 |
| B11 | Kế hoạch SX | **Kết thúc:** Thiếu NVL: tạo yêu cầu mua (P02), MO chờ | – | – | – |
| B12 | Kho | Xuất NVL cho xưởng (Pick & Transfer) | NVL chuyển vào khu sản xuất | Inventory › Transfers (Pick Components) | B13 |
| B13 | Xưởng sản xuất | Cắt vải theo sơ đồ cắt | Bán thành phẩm cắt, hao hụt vải ghi nhận | Manufacturing › Shop Floor › Work Order: Cắt | B14 |
| B14 | Xưởng sản xuất | May / ráp sản phẩm | Số lượng may, giờ công | Shop Floor › Work Order: May | B15 |
| B15 | Xưởng sản xuất | Là, hoàn thiện, gắn nhãn, đóng gói | Thành phẩm chờ QC | Shop Floor › Work Order: Hoàn thiện | B16 |
| B16 | QC | QC thành phẩm (AQL) | Kết quả Pass / Fail | Quality › Quality Checks (Control Point) | B17 |
| B17 | QC | **Quyết định:** Đạt? | Theo mức AQL (giả định AQL 2.5) | – | Đạt: B18; Không đạt: B19 |
| B18 | Kho | Hoàn thành MO, nhập kho thành phẩm | Tồn thành phẩm tăng, giá thành MO | MO › Mark as Done | B20 |
| B19 | Xưởng sản xuất | Sửa lỗi, tái chế hoặc loại bỏ (Scrap) | Phiếu hủy / hàng sửa lại | MO › Scrap | Kiểm lại: B16 |
| B20 | Kho | **Kết thúc:** Thành phẩm sẵn sàng phân phối (P06) | – | – | – |

**Kiểm soát và ngoại lệ:**

- Ba điều kiện tự sản xuất: (1) đủ NVL hoặc có lịch hàng về kịp; (2) tải công suất xưởng trong ngưỡng; (3) ngày hoàn thành dự kiến còn dư thời gian QC + vận chuyển.
- Thiếu điều kiện (1): chuyển P02/P03. Thiếu (2) hoặc (3): chia lệnh nội bộ + gia công. Sản phẩm xưởng không có thiết bị (thêu, wash…): gia công toàn bộ.
- Phần gia công chuyển sang P05; phần nội bộ vẫn chạy MO tại P04. Hai phần có chung mã đơn hàng gốc để truy vết.

### P05 – Gia công ngoài (xưởng thuê)

- **Mục tiêu:** Khi xưởng nội bộ không đủ năng lực hoặc không kịp hạn: giao việc cho xưởng gia công bằng PO gia công, cấp NVL, kiểm soát chất lượng và thanh toán tiền công.
- **Khởi đầu:** Có phần sản xuất chuyển gia công từ P04.
- **Phòng ban tham gia:** Kế hoạch SX, Mua hàng, Ban Giám đốc, Xưởng gia công ngoài, QC, Kho, Kế toán
- **File sơ đồ:** `P05_Gia_cong_ngoai.bpmn`

| Bước | Phòng ban | Hành động | Chứng từ / kết quả | Odoo | Chuyển tới |
|---|---|---|---|---|---|
| B1 | Kế hoạch SX | **Bắt đầu:** Có phần SX chuyển gia công (từ P04) | – | – | B2 |
| B2 | Kế hoạch SX | Chọn xưởng gia công, lập yêu cầu (SL, hạn, mẫu chuẩn) | Yêu cầu gia công | Manufacturing › BoM loại Subcontracting | B3 |
| B3 | Mua hàng | Xin báo giá, thương lượng đơn giá công, tạo PO gia công | PO gia công nháp | Purchase › RFQ (NCC là xưởng gia công) | B4 |
| B4 | Mua hàng | **Quyết định:** Vượt hạn mức duyệt? | PO gia công lớn hơn hạn mức (giả định 30 triệu VND) | – | Vượt: B5; Trong hạn mức: B7 |
| B5 | Ban Giám đốc | Xem xét & duyệt PO gia công | PO được duyệt | Purchase › Purchase Order Approval | B6 |
| B6 | Ban Giám đốc | **Quyết định:** Duyệt? | – | – | Không duyệt: B3; Duyệt: B7 |
| B7 | Mua hàng | Xác nhận PO, gửi xưởng, ký biên bản gia công | PO gia công đã xác nhận, phiếu nhập gia công tự tạo | Purchase › Confirm Order | B8 |
| B8 | Kho | Cấp NVL & mẫu chuẩn cho xưởng gia công | Phiếu xuất NVL sang kho xưởng gia công | Inventory › Resupply Subcontractor | B9 |
| B9 | Xưởng gia công ngoài | Nhận NVL, xác nhận, cắt–may theo mẫu | Biên bản nhận NVL | (bên ngoài hệ thống) | B10 |
| B10 | QC | QC kiểm tra đầu chuyền / giữa chuyền tại xưởng | Biên bản QC tại xưởng | Quality › Quality Checks | B11 |
| B11 | Xưởng gia công ngoài | Hoàn thành & giao thành phẩm về kho Atino | Phiếu giao hàng | (bên ngoài hệ thống) | B12 |
| B12 | Kho | Nhận hàng gia công, đối chiếu PO, kiểm đếm | Phiếu nhập, MO gia công tự đóng | Inventory › Receipts (Subcontracting) | B13 |
| B13 | QC | QC thành phẩm nhập (AQL) | Kết quả Pass / Fail | Quality › Quality Checks | B14 |
| B14 | QC | **Quyết định:** Đạt? | Theo AQL | – | Đạt: B16; Không đạt: B15 |
| B15 | Mua hàng | Yêu cầu xưởng sửa lỗi, giảm trừ công hoặc bồi hoàn | Biên bản xử lý lỗi | Purchase › PO (chatter); Accounting › Credit Note | Sửa lại: B11 |
| B16 | Kho | Xác nhận nhập kho thành phẩm (Validate Receipt) | Tồn thành phẩm tăng | Inventory › Validate | B17 |
| B17 | Kế toán | Đối chiếu hóa đơn–PO–phiếu nhập & thanh toán tiền công | Hóa đơn Paid, trừ phạt nếu có | Accounting › Vendor Bills; Payments | B18 |
| B18 | Kế toán | **Kết thúc:** Hoàn tất gia công, thành phẩm sẵn sàng phân phối (P06) | – | – | – |

**Kiểm soát và ngoại lệ:**

- Odoo: BoM loại Subcontracting gắn với xưởng gia công; khi xác nhận PO gia công, hệ thống tạo MO gia công và đối chiếu theo phiếu nhập.
- NVL xuất cho xưởng vẫn thuộc sở hữu Atino và nằm ở kho «Subcontracting»; cần đối soát hao hụt khi thanh toán.
- Trường hợp xưởng gia công tự mua vải (mua trọn gói): đổi sang quy trình mua hàng thường P02–P03 với sản phẩm là thành phẩm.

### P06 – Phân bổ & điều chuyển hàng từ kho tổng đến cửa hàng

- **Mục tiêu:** Cửa hàng yêu cầu hoặc Merchandising chủ động phân bổ; kho xuất hàng có chứng từ, vận chuyển, cửa hàng nhận và đối chiếu.
- **Khởi đầu:** Tồn cửa hàng thấp hoặc có hàng mới cần phân bổ.
- **Phòng ban tham gia:** Merchandising – Kế hoạch, Kho tổng, Vận chuyển, Cửa hàng
- **File sơ đồ:** `P06_Phan_phoi_kho_den_cua_hang.bpmn`

| Bước | Phòng ban | Hành động | Chứng từ / kết quả | Odoo | Chuyển tới |
|---|---|---|---|---|---|
| B1 | Cửa hàng | **Bắt đầu:** Tồn cửa hàng thấp / có hàng mới cần phân bổ | – | – | B2 |
| B2 | Cửa hàng | Lập yêu cầu bổ sung hàng theo size / màu | Yêu cầu bổ sung | Inventory › Replenishment (min/max tại kho cửa hàng) | B3 |
| B3 | Merchandising – Kế hoạch | Xem xét & phân bổ theo doanh số, tồn, kênh | Kế hoạch phân bổ | Inventory › Reporting; Replenishment | B4 |
| B4 | Merchandising – Kế hoạch | **Quyết định:** Tồn kho tổng đủ? | Tồn khả dụng ≥ nhu cầu phân bổ | – | Đủ: B6; Thiếu: B5 |
| B5 | Merchandising – Kế hoạch | **Kết thúc:** Thiếu hàng: tạo nhu cầu SX bổ sung (P04) | – | – | – |
| B6 | Kho tổng | Tạo phiếu điều chuyển / xuất kho đến cửa hàng | Phiếu điều chuyển (Ready) | Inventory › Transfers (Internal / Delivery) | B7 |
| B7 | Kho tổng | Soạn hàng, đóng thùng, quét mã vạch | Kiện hàng có mã | Barcode › Pick; Put in Pack | B8 |
| B8 | Kho tổng | Xác nhận xuất kho (Validate) | Tồn kho tổng giảm, hàng đang vận chuyển | Inventory › Validate | B9 |
| B9 | Vận chuyển | Vận chuyển hàng đến cửa hàng | Bàn giao hàng | Phiếu điều chuyển, biên bản bàn giao | B10 |
| B10 | Cửa hàng | Nhận hàng, đối chiếu phiếu, kiểm đếm | Số lượng thực nhận | Inventory › Receipts (kho cửa hàng) | B11 |
| B11 | Cửa hàng | **Quyết định:** Khớp số lượng, hàng không lỗi? | – | – | Khớp: B13; Lệch: B12 |
| B12 | Kho tổng | Điều tra chênh lệch, điều chỉnh tồn / khiếu nại vận chuyển | Biên bản chênh lệch | Inventory › Inventory Adjustment | B14 |
| B13 | Cửa hàng | Xác nhận nhận hàng, xếp lên kệ | Tồn cửa hàng tăng | Inventory › Validate | B15 |
| B14 | Kho tổng | **Kết thúc:** Ghi nhận chênh lệch, xử lý xong | – | – | – |
| B15 | Cửa hàng | **Kết thúc:** Hàng sẵn sàng bán (POS) | – | – | – |

**Kiểm soát và ngoại lệ:**

- Mỗi cửa hàng là một kho/vị trí riêng trong Odoo, tồn cửa hàng chỉ tăng khi cửa hàng xác nhận nhận.
- Hàng đang vận chuyển được theo dõi ở vị trí trung chuyển (Transit) để không mất dấu.

### P07 – Bán lẻ tại cửa hàng (POS), đổi trả, đóng ca & đối soát

- **Mục tiêu:** Mỗi giao dịch tại quầy đều được ghi trong POS; đổi trả có duyệt; cuối ca đối soát tiền mặt, thẻ, QR với doanh thu ghi sổ.
- **Khởi đầu:** Bắt đầu ca bán hàng.
- **Phòng ban tham gia:** Khách hàng, Nhân viên bán hàng, Quản lý cửa hàng, Kế toán
- **File sơ đồ:** `P07_Ban_le_tai_cua_hang_POS.bpmn`

| Bước | Phòng ban | Hành động | Chứng từ / kết quả | Odoo | Chuyển tới |
|---|---|---|---|---|---|
| B1 | Nhân viên bán hàng | **Bắt đầu:** Bắt đầu ca bán hàng | – | – | B2 |
| B2 | Quản lý cửa hàng | Kiểm tra quỹ đầu ca & mở ca POS | Phiên POS mở, tiền đầu ca | Point of Sale › Open Register (Opening Cash) | B3 |
| B3 | Nhân viên bán hàng | **Quyết định:** Khách mua hàng hay đổi/trả? | – | – | Mua hàng: B4; Đổi / trả: B5 |
| B4 | Nhân viên bán hàng | Tư vấn, quét mã vạch, áp khuyến mãi / thẻ thành viên | Đơn POS (Draft) | POS › Order; Loyalty & Promotions | B6 |
| B5 | Quản lý cửa hàng | Kiểm tra hóa đơn, điều kiện đổi/trả & duyệt | Quyết định đổi/trả (giả định: trong 7 ngày, còn tag) | POS › Orders (tra cứu hóa đơn) | B7 |
| B6 | Khách hàng | Khách thanh toán (tiền mặt, thẻ, QR) | Thanh toán ghi nhận | POS › Payment | B8 |
| B7 | Quản lý cửa hàng | Hoàn trả (Refund) trên POS, nhập lại kho / hàng lỗi | Đơn hoàn, tồn cập nhật | POS › Refund; Inventory › Return | B9 |
| B8 | Nhân viên bán hàng | Xác nhận thanh toán, in hóa đơn, đóng gói | Đơn POS Paid, tồn cửa hàng giảm | POS › Validate | B9 |
| B9 | Nhân viên bán hàng | **Quyết định:** Còn giao dịch? | Còn khách hay hết ca | – | Còn giao dịch: B3; Hết ca: B10 |
| B10 | Quản lý cửa hàng | Đếm tiền, đóng ca POS (Closing) | Biên bản đóng ca, tiền đếm thực tế | POS › Close Register | B11 |
| B11 | Kế toán | Đối soát doanh thu POS với tiền mặt, thẻ, QR & ghi sổ | Bút toán doanh thu, đối soát | Accounting › POS Sessions, Journal Entries | B12 |
| B12 | Kế toán | **Quyết định:** Có chênh lệch? | – | – | Có: B13; Không: B14 |
| B13 | Quản lý cửa hàng | Giải trình chênh lệch, xử lý | Biên bản giải trình | POS › Session (ghi chú) | Đối soát lại: B11 |
| B14 | Kế toán | **Kết thúc:** Hoàn tất đối soát ca | – | – | – |

**Kiểm soát và ngoại lệ:**

- Nhân viên giảm giá tối đa mức quy định (giả định 10%); vượt mức cần Quản lý nhập mã duyệt.
- Chỉ Quản lý có quyền hoàn trả/Refund và đóng ca.
- Chênh lệch quỹ phải giải trình bằng văn bản; không sửa số liệu sau khi ca đã đóng.

### P08 – Bán hàng online: đặt hàng, soạn, giao, hoàn, đối soát

- **Mục tiêu:** Đơn website/app đi một mạch từ xác nhận → kiểm tồn → soạn đóng gói → giao → thu tiền → hóa đơn, kể cả giao thất bại và hoàn hàng.
- **Khởi đầu:** Khách đặt hàng trên website hoặc app.
- **Phòng ban tham gia:** Khách hàng, CSKH / Bán hàng online, Đơn vị vận chuyển, Kho, Kế toán
- **File sơ đồ:** `P08_Ban_hang_online.bpmn`

| Bước | Phòng ban | Hành động | Chứng từ / kết quả | Odoo | Chuyển tới |
|---|---|---|---|---|---|
| B1 | Khách hàng | **Bắt đầu:** Khách truy cập website / app | – | – | B2 |
| B2 | Khách hàng | Chọn sản phẩm (size, màu), đặt hàng, thanh toán online hoặc COD | Đơn website (Quotation) | eCommerce › Cart, Checkout | B3 |
| B3 | CSKH / Bán hàng online | Xác nhận đơn, kiểm tra thông tin & rủi ro | Sales Order xác nhận | Sales › Orders | B4 |
| B4 | CSKH / Bán hàng online | **Quyết định:** Đủ tồn kho? | Tồn khả dụng kho online | – | Đủ: B6; Hết hàng: B5 |
| B5 | CSKH / Bán hàng online | Báo khách hết hàng: đổi mã, chờ hàng hoặc hủy & hoàn tiền | Phương án đã thống nhất | Sales › Order (chatter); Credit Note | B7 |
| B6 | Kho | Soạn hàng (Pick) | Danh sách soạn | Inventory › Delivery Orders; Barcode | B8 |
| B7 | CSKH / Bán hàng online | **Kết thúc:** Kết thúc: đổi / hủy đơn | – | – | – |
| B8 | Kho | Đóng gói, in vận đơn | Kiện hàng, vận đơn | Inventory › Put in Pack; Shipping connector | B9 |
| B9 | Kho | Xác nhận xuất kho & bàn giao ĐVVC | Tồn giảm, phiếu giao đã đóng | Inventory › Validate Delivery | B10 |
| B10 | Đơn vị vận chuyển | Vận chuyển & giao hàng cho khách | Trạng thái giao | Tracking (vận đơn) | B11 |
| B11 | Đơn vị vận chuyển | **Quyết định:** Giao thành công? | – | – | Thành công: B12; Thất bại: B13 |
| B12 | Khách hàng | Khách nhận hàng & xác nhận | Xác nhận nhận hàng | Portal / email xác nhận | B14 |
| B13 | Kho | Nhận hàng hoàn, kiểm tra, nhập lại kho | Tồn nhập lại, phiếu hoàn | Inventory › Return | B14 |
| B14 | Kế toán | Đối soát ĐVVC / cổng thanh toán, lập hóa đơn hoặc credit note | Hóa đơn, thanh toán, đối soát COD | Accounting › Invoices, Credit Notes, Payments | B15 |
| B15 | Kế toán | **Kết thúc:** Hoàn tất đơn hàng online | – | – | – |

**Kiểm soát và ngoại lệ:**

- Tồn website là tồn khả dụng thực của kho online; không bán vượt tồn.
- Đối soát COD với ĐVVC theo kỳ (tuần), chênh lệch xử lý ở P09.
- Đơn hoàn: kiểm hàng trước khi nhập lại kho khả dụng; hàng lỗi vào vị trí riêng.

### P09 – Kế toán: kiểm kê, giá thành, đối soát & đóng kỳ

- **Mục tiêu:** Cuối kỳ các phòng ban chốt số liệu song song; Kế toán tính giá thành, đối soát, lập báo cáo; Ban Giám đốc duyệt rồi khóa kỳ.
- **Khởi đầu:** Cuối kỳ (cuối tháng).
- **Phòng ban tham gia:** Kế toán, Kho, Xưởng sản xuất, Cửa hàng / Online, Ban Giám đốc
- **File sơ đồ:** `P09_Ke_toan_gia_thanh_doi_soat_dong_ky.bpmn`

| Bước | Phòng ban | Hành động | Chứng từ / kết quả | Odoo | Chuyển tới |
|---|---|---|---|---|---|
| B1 | Kế toán | **Bắt đầu:** Cuối kỳ (cuối tháng) | – | – | B2 |
| B2 | Kế toán | **Song song:** Chốt số liệu song song | – | – | B3; B4; B5 |
| B3 | Kho | Kiểm kê kho, đối chiếu tồn hệ thống và thực tế | Bảng kiểm kê | Inventory › Physical Inventory | B6 |
| B4 | Xưởng sản xuất | Chốt lệnh SX, nhập giờ công & hao hụt thực tế | MO Done, chi phí thực tế | Manufacturing › MO; Work Orders | B7 |
| B5 | Cửa hàng / Online | Chốt doanh thu, nộp tiền & báo cáo bán hàng | Báo cáo doanh thu theo kênh | POS › Sessions; eCommerce › Orders | B7 |
| B6 | Kho | Điều chỉnh chênh lệch tồn, ghi nhận hao hụt | Bút toán điều chỉnh tồn | Inventory › Apply (Inventory Adjustment) | B7 |
| B7 | Kế toán | **Song song:** Đủ số liệu | – | – | B8 |
| B8 | Kế toán | Tính giá thành thực tế (NVL + công + gia công + chi phí chung) | Giá thành thực tế từng mã | Manufacturing › Cost Analysis; Landed Costs | B9 |
| B9 | Kế toán | Đối soát doanh thu, COD, công nợ NCC & xưởng gia công | Biên bản đối soát | Accounting › Reconciliation; Aged Payable/Receivable | B10 |
| B10 | Kế toán | Ghi nhận chi phí, khấu hao, định giá tồn kho | Bút toán cuối kỳ | Accounting › Journal Entries; Inventory Valuation | B11 |
| B11 | Kế toán | Lập báo cáo kết quả kinh doanh theo kênh / nhóm hàng | Báo cáo P&L | Accounting › Reporting › Profit & Loss | B12 |
| B12 | Ban Giám đốc | Xem xét báo cáo, phê duyệt | Quyết định duyệt | Accounting › Reporting | B13 |
| B13 | Ban Giám đốc | **Quyết định:** Duyệt? | – | – | Cần điều chỉnh: B10; Duyệt: B14 |
| B14 | Kế toán | Khóa kỳ kế toán | Kỳ đã khóa | Accounting › Settings › Lock Dates | B15 |
| B15 | Kế toán | **Kết thúc:** Hoàn tất đóng kỳ | – | – | – |

**Kiểm soát và ngoại lệ:**

- Cổng song song: ba bộ phận chốt cùng lúc, thiếu một bộ phận thì Kế toán chưa được tính giá thành.
- Sau khóa kỳ, mọi điều chỉnh phải bằng bút toán kỳ sau, không sửa chứng từ cũ.

---

## 7. Quy tắc quyết định: tự sản xuất hay gia công ngoài

Đây là quyết định chính của P04. Kế hoạch SX đánh giá ba điều kiện cho từng lệnh:

| # | Điều kiện | Cách đo | Nếu không đạt |
|---|---|---|---|
| 1 | Đủ NVL | Tồn khả dụng + hàng về trước ngày cắt | Chạy P02/P03 mua NVL; hoặc cấp NVL cho xưởng gia công |
| 2 | Đủ công suất xưởng | Tải work center cắt/may/hoàn thiện trong kỳ ≤ ngưỡng (giả định 85%) | Chia lệnh: phần trong năng lực làm nội bộ, phần dư gia công |
| 3 | Kịp hạn giao | Ngày hoàn thành dự kiến + thời gian QC + vận chuyển ≤ hạn giao | Chia lệnh hoặc gia công toàn bộ; nếu vẫn trễ thì dời hạn với khách |
| 4 | Xưởng có thiết bị / tay nghề | Ví dụ thêu, wash, in đặc biệt | Gia công toàn bộ hoặc theo công đoạn |

Đủ cả 3 điều kiện đầu → tự sản xuất. Thiếu → Kế hoạch SX đề xuất, **Ban Giám đốc duyệt** phương án gia công (chi phí, chất lượng, hạn giao). Hai phần của một lệnh gốc (nội bộ và gia công) giữ chung mã đơn hàng nguồn để truy vết.

Cách thể hiện trên Odoo (chi tiết ở Bước 3): mỗi mã hàng có hai BoM, một loại *Manufacture* và một loại *Subcontracting* (gắn xưởng gia công). Phần nội bộ chạy MO, phần dư chạy PO gia công.

## 8. Ma trận bàn giao giữa các phòng ban

| Từ | Đến | Bàn giao | Chứng từ | Thời hạn gợi ý (SLA) |
|---|---|---|---|---|
| Merchandising | Thiết kế – R&D | Đề xuất mã hàng | Product nháp | 1 ngày |
| Thiết kế – R&D | Mua hàng | Định mức, tech pack | BoM | 1 ngày |
| Mua hàng | Ban Giám đốc | PO vượt hạn mức | PO To Approve | 1 ngày làm việc |
| Kế hoạch SX | Mua hàng | NVL thiếu | RFQ nháp | Trong ngày |
| NCC | Kho | Hàng giao | Phiếu giao | Theo PO |
| Kho | QC | Hàng chờ kiểm | Quality Check | 4 giờ |
| QC | Kho | Kết quả đạt | Check Pass | 4 giờ |
| Kho | Xưởng | NVL xuất sản xuất | Pick / MO | Trước giờ bắt đầu ca |
| Xưởng | QC | Thành phẩm chờ kiểm | Work Order Done | Cuối công đoạn |
| QC | Kho | Thành phẩm đạt | MO Done | 4 giờ |
| Kế hoạch SX | Mua hàng | Phần dư chuyển gia công | RFQ / PO gia công | 1 ngày |
| Kho | Xưởng gia công | Cấp NVL | Phiếu xuất Subcontracting | Theo kế hoạch |
| Merchandising | Kho tổng | Kế hoạch phân bổ | Phiếu điều chuyển | 1 ngày |
| Kho tổng | Cửa hàng | Hàng đến | Phiếu điều chuyển | 1–2 ngày |
| Cửa hàng / Online | Kế toán | Doanh thu, tiền | Phiên POS đóng, đơn online | Hàng ngày |
| Kế toán | Ban Giám đốc | Báo cáo kỳ | P&L | 3 ngày sau cuối kỳ |

## 9. Hạn mức duyệt và dung sai (GIẢ ĐỊNH, cần Atino xác nhận)

| Hạng mục | Giá trị giả định |
|---|---|
| PO mua NVL cần Ban Giám đốc duyệt | > 50.000.000 VND |
| PO gia công cần Ban Giám đốc duyệt | > 30.000.000 VND |
| Dung sai số lượng khi nhận NVL | ± 2% |
| Mức AQL kiểm thành phẩm | AQL 2.5 |
| Ngưỡng tải công suất xưởng | 85% |
| Giảm giá tại POS (nhân viên) | ≤ 10%, trên mức này cần Quản lý |
| Đổi trả tại cửa hàng | Trong 7 ngày, còn tag, có hóa đơn |
| Chênh lệch quỹ cuối ca | Mọi chênh lệch phải giải trình |

## 10. Báo cáo và chỉ số theo dõi

| Phòng ban | Chỉ số | Nguồn Odoo |
|---|---|---|
| Mua hàng | Tỷ lệ giao đúng hạn của NCC, chênh lệch giá PO so với báo giá | Purchase › Reporting |
| Kho | Độ chính xác tồn kho, thời gian nhập/xuất | Inventory › Reporting |
| Sản xuất | Tỷ lệ hao hụt vải, năng suất theo work center, tải công suất | Manufacturing › Reporting |
| QC | Tỷ lệ đạt đầu vào và thành phẩm, tỷ lệ lỗi theo xưởng | Quality › Reporting |
| Gia công | Tỷ lệ giao đúng hạn, tỷ lệ lỗi, chi phí công trên sản phẩm | Purchase + Quality |
| Bán hàng | Doanh thu theo kênh, cửa hàng, nhóm hàng; tỷ lệ đổi trả | POS, Sales, eCommerce |
| Kế toán | Giá thành thực tế so với dự kiến, lãi gộp, công nợ | Accounting › Reporting |

## 11. Đối chiếu với tài liệu ScaleUp (phần Manufacture)

| Thẻ trong tài liệu | Nội dung | Nằm ở quy trình Atino |
|---|---|---|
| 1. Define the bill of materials | Tạo sản phẩm, nguyên liệu, BoM | P01 (bước tạo BoM) |
| 2. Purchase raw materials | RFQ → PO nguyên liệu | P02 |
| 3. Receive products | Nhận hàng, backorder khi thiếu | P03 |
| 4. Set up operations & work centers | Công đoạn, work center, chi phí giờ máy, hướng dẫn | P01 (routing) và P04 |
| 5. Plan a manufacturing order | MO, Shop Floor | P04 |
| 6. Add a quality check | Control Point tại công đoạn | P03, P04, P05 |
| 7. Check your quality test | Nhập số đo khi sản xuất | P04 (QC thành phẩm) |
| 8. Control cost | BoM Overview: giá thành vật tư + công | P01 (giá thành dự kiến) và P09 (giá thành thực tế) |

Phần Atino mở rộng so với tài liệu: gia công ngoài (P05), quyết định tự SX hay gia công (P04), phân phối đến cửa hàng (P06), bán lẻ và online (P07, P08), đóng kỳ (P09).

## 12. Điểm cần Atino xác nhận trước Bước 3

1. Các hạn mức, dung sai ở mục 9 có đúng thực tế không.
2. Số cửa hàng, có kho online riêng không, hay online lấy hàng từ kho tổng.
3. Xưởng gia công ngoài thanh toán theo công (cấp NVL) hay mua trọn gói.
4. Có dùng bảng giá theo kênh (cửa hàng, online, sỉ) hay giá chung.
5. Chính sách đổi trả và khách hàng thân thiết thực tế.
6. Đội ngũ có dùng gói Enterprise của Odoo không (một số tính năng như Quality, Shop Floor, Planning yêu cầu Enterprise).

## 13. Cách mở file BPMN trong Bizagi Modeler

1. Mở Bizagi Modeler → **File → Import → BPMN** (hoặc kéo thả file `.bpmn`).
2. Mỗi file là một sơ đồ có pool và các lane theo phòng ban. Mô tả Odoo, chứng từ, phòng ban của từng bước nằm ở phần mô tả (Documentation) của ô.
3. Nên **Save As** thành `.bpm` ngay sau khi import để dùng file gốc của Bizagi.
4. Nếu Bizagi xếp lại bố cục khác, dùng **Auto-layout** hoặc kéo tay, logic luồng không đổi.
# Atino
