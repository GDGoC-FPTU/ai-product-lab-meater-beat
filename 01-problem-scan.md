# 01 - Problem Scan

## Phase 1 - SCAN: Bang quet co hoi

| # | Subsidiary | Lens | Mo ta ngan bai toan |
|---|---|---|---|
| 1 | Xanh SM | Ton thoi gian | Dieu phoi vien xu ly thu cong cac truong hop xe dien con pin thap, tai xe bao sap het pin va can quyet dinh tram sac gan nhat hay xe sac pin di dong. |
| 2 | Xanh SM | Stakeholder Pain | Tai xe phan nan goi tong dai khi sap het pin phai cho dieu phoi vien tra cuu nhieu he thong, dan den cham ho tro va co nguy co xe nam duong. |
| 3 | VinFast | Lap lai | Doi soat hoa don sac dien tu tram sac doi tac voi log sac thuc te theo tung xe, tung thoi diem va tung ma giao dich. |
| 4 | Vinhomes | AI-upgrade | Phan loai phan anh cu dan tren app Vinhomes Resident va draft phan hoi ban dau theo dung ban quan ly, muc do khan cap va SLA. |
| 5 | Vinmec | Ton thoi gian | Bac si mat 20-30 phut de tong hop benh an, ket qua xet nghiem va loi dan thanh ban tom tat xuat vien de benh nhan de hieu. |
| 6 | Vinpearl | Pain tu nguoi khac | Quan ly khach san phai doc review tren nhieu kenh de loc phan nan khan cap ve phong, thai do nhan vien va dich vu. |

## Phase 2 - QUICK-ASSESS: 3 Quick Problem Cards

### Card 1 - Xanh SM: Xu ly xe pin thap / su co sac pin

| Truong | Noi dung |
|---|---|
| Bai toan | Tai xe Xanh SM bao xe con pin thap, dieu phoi vien can quyet dinh nhanh: di toi tram sac gan nhat hay dieu xe sac pin di dong. |
| Cong ty thanh vien | Xanh SM |
| Actor / Operator | Dieu phoi vien trung tam van hanh, tai xe dang cho ho tro. |
| Workflow thu cong hien tai | 1. Tai xe goi tong dai bao pin va vi tri. -> 2. Dieu phoi vien mo ban do noi bo de xac dinh toa do. -> 3. Mo dashboard tram sac de xem khoang cach va tinh trang tru sac. -> 4. So sanh voi muc pin. -> 5. Soan tin nhan huong dan hoac goi doi xe sac pin di dong. |
| Buoc ton thoi gian / loi nhat | Buoc 3-4, mat khoang 10-12 phut/luot vi phai tra cuu cheo giua ban do, dashboard tram sac va thong tin xe. |
| AI ho tro o buoc nao | AI doc thong tin pin, vi tri, danh sach tram sac; draft de xuat an toan va tin nhan cho dispatcher duyet. |
| Metric thanh cong | Giam thoi gian xu ly tu 15 phut xuong duoi 3 phut/luot; 100% case pin < 5% khong de xuat tram xa hon 5 km. |
| Quick Architecture | LLM Feature + Rule guardrail. |

### Card 2 - Vinhomes: Phan loai phan anh cu dan

| Truong | Noi dung |
|---|---|
| Bai toan | Cu dan gui phan anh qua app, nhan vien CSKH phai doc thu cong va chuyen dung bo phan xu ly. |
| Cong ty thanh vien | Vinhomes |
| Actor / Operator | Nhan vien CSKH, ban quan ly toa nha, cu dan. |
| Workflow thu cong hien tai | 1. Cu dan gui ticket. -> 2. CSKH doc noi dung. -> 3. Xac dinh loai su co. -> 4. Gan phong ban va muc uu tien. -> 5. Draft phan hoi cho cu dan. |
| Buoc ton thoi gian / loi nhat | Buoc 2-4, mat 6-10 phut/ticket; loi hay gap la route nham bo phan ky thuat, ve sinh, bao ve. |
| AI ho tro o buoc nao | LLM phan loai ticket, trich xuat toa nha/can ho/van de, draft phan hoi dau tien. |
| Metric thanh cong | 85% ticket duoc phan loai duoi 20 giay; giam route nham tu 12% xuong duoi 4%. |
| Quick Architecture | LLM Feature co human review. |

### Card 3 - Vinmec: Draft tom tat xuat vien

| Truong | Noi dung |
|---|---|
| Bai toan | Bac si phai tong hop nhieu nguon du lieu lam ban tom tat xuat vien ro rang cho benh nhan. |
| Cong ty thanh vien | Vinmec |
| Actor / Operator | Bac si dieu tri, dieu duong hanh chinh, benh nhan. |
| Workflow thu cong hien tai | 1. Bac si mo EMR. -> 2. Doc chan doan, xet nghiem, thuoc. -> 3. Viet tom tat. -> 4. Kiem tra loi y khoa. -> 5. In/gui cho benh nhan. |
| Buoc ton thoi gian / loi nhat | Buoc 2-3, mat 20-30 phut/benh nhan; de thieu loi dan dung thuoc hoac lich tai kham. |
| AI ho tro o buoc nao | LLM tao ban nhap tom tat de bac si kiem tra va ky duyet. |
| Metric thanh cong | Giam thoi gian draft tu 25 phut xuong duoi 8 phut; 100% ban cuoi co bac si duyet. |
| Quick Architecture | LLM Feature, bat buoc HITL. |

## Lua chon top 1 de deep-dive

Nhom chon Card 1 - Xanh SM xu ly xe pin thap / su co sac pin, vi bai toan co tan suat cao, tac dong truc tiep den an toan van hanh, metric ro rang va phu hop voi prompt prototype da xay dung.
