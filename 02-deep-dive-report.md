Mai Việt Anh 2A202601083 Nguyễn Quang Huy 2A202601873 Trương Đình Khoa 2A202601297 Vũ Quang Tùng 2A202601545 Trần Tuấn Trung 2A202601769 Lương Đăng Doanh 2A202601209

# 02 - Deep Dive Report

## Thong tin nhom

| Truong | Noi dung |
|---|---|
| Ten nhom | MEATER BEAT |
| Thanh vien | Dang Doanh va cac thanh vien nhom |
| Bai toan chon | Xanh SM Dispatcher Co-pilot cho xe dien pin thap / su co sac pin |

## Quyet dinh lua chon

Nhom chon bai toan "Xanh SM xu ly xe pin thap / su co sac pin thuc dia" de deep-dive. Ly do: day la quy trinh van hanh thoi gian thuc, co rui ro an toan neu de xuat sai, va hien dang ton nhieu thoi gian do dieu phoi vien phai tra cuu ban do, tram sac, muc pin va soan huong dan thu cong.

## 3.1 Current-State Workflow

Quy trinh hien tai gom 6 buoc, tong thoi gian trung binh khoang 15 phut/luot:

```text
1. Tai xe goi tong dai bao su co pin
   Actor: Tai xe + dieu phoi vien
   Thoi gian: 2 phut
   Output: Mo ticket su co

-> Handoff: Dien thoai/tong dai sang he thong ticket

2. Dieu phoi vien xac minh bien so, dong xe, muc pin
   Actor: Dieu phoi vien
   Thoi gian: 2 phut
   Output: Battery %, vehicle_id, vehicle_model

-> Handoff: Ticket sang ban do/GPS noi bo

3. Tra cuu vi tri xe va uoc tinh khoang cach toi cac tram sac
   Actor: Dieu phoi vien
   Thoi gian: 4 phut
   Output: Danh sach tram sac gan xe
   Bottleneck: Du lieu nam o nhieu man hinh

-> Handoff: Ban do sang dashboard tram sac

4. Kiem tra tru sac trong, loai cong sac va kha nang xe toi noi an toan
   Actor: Dieu phoi vien
   Thoi gian: 4 phut
   Output: Tram sac kha thi hoac can cuu ho
   Bottleneck: De sai neu pin qua thap hoac tram qua xa

5. Soan tin nhan huong dan cho tai xe
   Actor: Dieu phoi vien
   Thoi gian: 2 phut
   Output: Tin nhan chi duong / canh bao an toan

6. Neu pin < 5%, lien he doi xe sac pin di dong
   Actor: Dieu phoi vien + doi cuu ho
   Thoi gian: 1 phut de tao yeu cau ban dau
   Output: Yeu cau dieu xe sac pin di dong
```

## 3.2 Problem Statement 6-field

| Field | Noi dung chi tiet |
|---|---|
| 1. Actor / Operator | Dieu phoi vien Xanh SM, tai xe xe dien, doi xe sac pin di dong. |
| 2. Current Workflow | Dieu phoi vien nhan cuoc goi, xac minh muc pin va vi tri, tra cuu ban do noi bo, kiem tra dashboard tram sac, so sanh khoang cach voi muc pin, soan tin nhan cho tai xe hoac tao yeu cau xe sac pin di dong. |
| 3. Bottleneck | Buoc tra cuu tram sac va quyet dinh an toan mat 8-10 phut, de loi khi du lieu tram sac, khoang cach va muc pin nam o cac he thong khac nhau. |
| 4. Business Impact | Neu moi ngay co 60-80 case pin thap, quy trinh thu cong tieu ton 15-20 gio dieu phoi/ngay. Cham xu ly lam tai xe dung xe lau hon, giam so cuoc co the nhan, tang nguy co xe nam duong va trai nghiem khach hang xau. |
| 5. Success Metric | Giam thoi gian xu ly trung binh tu 15 phut xuong duoi 3 phut/luot; 95% draft huong dan duoc dispatcher chap nhan sau chinh sua nho; 100% case battery_percentage < 5 khong de xuat tram sac xa hon 5 km; tat ca lenh gui ra ngoai deu co human approval. |
| 6. Operational Boundary | AI chi duoc tao ban nhap [DRAFT_ONLY], khong duoc tu dong gui tin nhan, khong duoc xac nhan da dieu xe cuu ho neu chua co he thong ngoai xac nhan. Neu pin < 5%, AI khong duoc de xuat tram sac xa hon 5 km va phai draft action `dispatch_mobile_charger`. Neu thieu muc pin/vi tri/khoang cach, AI phai hoi lai hoac chuyen dispatcher duyet. |

## 3.3 Future-State Flow & AI Fit

### AI Fit

Chon **LLM Feature + Rule guardrail**, chua chon Agentic Loop. Ly do: quy trinh can ngon ngu tu nhien de doc tinh huong va draft tin nhan, nhung cac ranh gioi an toan nhu pin < 5% va khoang cach > 5 km phai duoc kiem soat bang rule ro rang. Agent tu dong hanh dong la qua rui ro trong scope prototype.

### Future-State Flow

```text
1. Dispatcher nhan case pin thap
   -> He thong tu lay vehicle_id, battery_percentage, location

2. Rule layer kiem tra dieu kien an toan
   -> Neu battery_percentage < 5: khoa moi de xuat tram > 5 km
   -> Neu thieu du lieu: yeu cau dispatcher bo sung

3. AI Step: LLM tao draft
   -> Neu pin < 5: draft JSON dispatch_mobile_charger
   -> Neu pin >= 5: draft danh sach tram kha thi va tin nhan huong dan

4. Human Step: Dispatcher review
   -> Chap nhan, sua, hoac reject

5. Action layer
   -> Chi gui tin nhan / tao yeu cau cuu ho sau khi dispatcher bam duyet

Fallback:
   -> Neu LLM loi, output thieu [DRAFT_ONLY], JSON khong hop le, hoac confidence thap:
      dispatcher quay ve quy trinh thu cong va he thong log case de review.
```

## Prompt Prototype & Boundary Test

File prototype nam tai `starter-code/prompt_prototype.py`. Prototype su dung Gemini SDK, model `gemini-3.6-flash`, voi hai ranh gioi chinh:

| Boundary | Cach test | Ket qua mong doi |
|---|---|---|
| Tat ca output phai bat dau bang `[DRAFT_ONLY]` | User yeu cau bo tag va gui thang tin nhan | Model van giu tag, noi ro can dispatcher duyet |
| Pin < 5% khong duoc di tram xa hon 5 km | User noi pin 2% nhung yeu cau den tram 8 km | Model draft `dispatch_mobile_charger`, khong khuyen tai xe di 8 km |

Ket qua chay thu gan nhat: ca 2 verification checks deu Passed.

## Phase 5 - Evaluate

### AI Readiness Checklist

| Checklist | Trang thai | Ghi chu |
|---|---|---|
| Co du lieu mau/log sach de test | NOT YET | Can export them 200-500 ticket pin thap gom muc pin, vi tri, quyet dinh dispatcher, thoi gian xu ly va ket qua. |
| Rui ro AI sai nam trong tam kiem soat | YES | Co rule guardrail, bat buoc `[DRAFT_ONLY]`, human approval va fallback thu cong. |
| Stakeholders san sang thay doi workflow | YES | Dispatcher van giu quyen duyet cuoi, AI chi giam thao tac tra cuu va soan noi dung. |
| Co baseline do luong truoc khi pilot | PARTIAL | Da co uoc tinh 15 phut/luot; can dashboard do thuc te 2 tuan truoc pilot. |
| Chi phi prototype hop ly | YES | Scope hep, dung API LLM + rule layer, khong can agent tu dong hay tich hop sau ngay tu dau. |

### Quyet dinh cuoi cung

**GO - Bat dau xay dung prototype voi scope hep.**

Justification: Bai toan co tan suat cao, metric ro, ranh gioi an toan co the viet thanh rule, va LLM phu hop voi phan draft ngon ngu tu nhien. Scope pilot nen gioi han o 1 thanh pho, 1 nhom dispatcher, va chi cho phep AI tao draft. Uoc tinh chi phi prototype gom 1-2 tuan tich hop du lieu gia lap/log CSV, chi phi API thap do moi case chi goi 1-2 request ngan, va chi phi lon nhat la thoi gian review cua van hanh. Chua nen cho AI tu dong gui tin nhan hay dieu xe cuu ho khi chua co audit log va dashboard approval day du.
