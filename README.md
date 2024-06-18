# Hệ thống quản lý gia phả
# VI THÀNH VĂN K57KMT

# Giới thiệu qua bài 

Hệ thống quản lý gia phả tôi tạo để có thể quản lý thông tin của các thành viên trong gia đình. Hệ thống này cho phép thêm, sửa, xóa, và tra cứu thông tin các thành viên cũng như mô tả quan hệ giữa các thành viên trong gia phả.

## các bảng cần phải thiết kế , có ý tưởng như sau 

tôi muốn có bảng giapha để lưu trữ thông tin của các thành viên , và 1 bảng để lưu thế hệ.
gồm các trường sau :
- Lưu trữ thông tin các thành viên gia phả bao gồm: tên, tuổi, giới tính, mã cha mẹ, ngày sinh, ngày mất.
- Quản lý các mối quan hệ cha mẹ - con cái.
- thêm trigger để bắt lỗi , ví dụ như tuổi con đẻ ra không thể hơn tuổi cha mẹ được , hoặc đẻ ra giới tính không thể có giá trị khác với Nam hoặc nữ
 - sp có chức năng thêm, sửa, xóa, truy vấn đời thứ mấy , con nhà nào , gọi như nào  

## Mô tả bài toán

Hệ thống bao gồm các chức năng cơ bản sau:

- Thêm thành viên.
- Sửa thông tin thành viên.
- Xóa thành viên.
- Mô tả quan hệ giữa các thành viên.
- Tra cứu thông tin thành viên.
- Báo cáo thống kê số lượng thành viên.

## Các chức năng chi tiết

### Stored Procedures

- `sp_ThemThanhVien`: Thêm thành viên mới vào gia phả.
- `sp_SuaThanhVien`: Sửa thông tin thành viên.
- `sp_XoaThanhVien`: Xóa thành viên khỏi gia phả.
- `sp_MoTaThanhVien`: Mô tả quan hệ giữa các thành viên.
- `sp_TraCuuThanhVien`: Tra cứu thông tin thành viên.
- `sp_BaoCaoThongKe`: Báo cáo thống kê số lượng thành viên.

### Trigger

- trg_KiemTraTuoi: Kiểm tra tuổi con không lớn hơn tuổi cha mẹ.
- trg_KiemTraGioiTinh: Kiểm tra giới tính chỉ có thể là Nam hoặc Nữ.

## Hướng dẫn sử dụng

1. Tạo bảng `GiaPhaHoPham` 
2. Thêm các stored procedures và triggers 
3. Sử dụng các sp để quản lý thông tin thành viên và tra cứu dữ liệu thông qua các @ACTION
4. Sử dụng trigger để đảm bảo tính toàn vẹn dữ liệu khi thêm hoặc sửa thông tin thành viên.

## Hình ảnh cụ thể và kết quả 

### Bảng thông tin 
![bảng.png](https://tiloid.com/uploads/images/june2024/bang1.png)
ở đây có các trường 
- ID là khóa chính tức (pk) kiểu int vì ID là kiểu số nguyên mà ngắn gọn dễ hiểu cứ từ ông bà (0-1) xuống đến thằng cháu chắt nào đấy cho dễ ,nó giống như mã định danh để xác định thông tin cũng như truy xuất chính xác 
 - Ten kiểu NVARCHAR để lưu tên , nên dùng CHAR , VACHARRT , Nvarcha kiểu ký tự là viết được thêm N' ' để viết có dấu 
 - gioitinh là để xác định giới tính check luôn để bắt lỗi. dùng kiểu CHAR 
 - Machame để xác định mã bố mẹ - con cái xem quan hệ rõ ràng thế nào , kiểu INT số nguyên , là khóa phụ(FK) ,để tham chiếu với tư cách phân biệt cấp bậc quan hệ mẹ con 
 - Ngaysinh kiểu date đại diện cho ngày tháng năm sinh , thường là dùng date , 
 - Ngaymat tương tự kiểu ngày sinh  
 ### bảng thehe
![1.png](https://tiloid.com/uploads/images/june2024/1.png)
#### bảng này gồm :
-thehe là khóa chính để sau còn chiếu để quét số thành viên của các thế hệ , để kiểu dữ liệu INT 
-Slthanhvien int lưu số lượng để in ra 
## Thêm dữ liệu cho các thành viên 
![DL.png](https://tiloid.com/uploads/images/june2024/DL.png)![DL1.png](https://tiloid.com/uploads/images/june2024/DL1.png)![Dl3.png](https://tiloid.com/uploads/images/june2024/Dl3.png)
## truy vấn tổng xem có sót ai không 
![truyvantong.png](https://tiloid.com/uploads/images/june2024/truyvantong.png)
 - trong này tôi dùng CTE để đơn giản hóa truy vấn , nó là kiểu 1 bảng tạm thời common table exprestion 
 - có cấu trúc như sau 
 - ![CTESQL.png](https://tiloid.com/uploads/images/june2024/CTESQL.png)
 - thì khúc đầu CTE 
 - CTE được đặt tên là CTE và bắt đầu bằng việc chọn các thành viên gốc (không có cha mẹ).
 -Sau đó, phần UNION ALL sẽ thêm các thành viên con bằng cách tham chiếu đến CTE c đã được định nghĩa trước đó.
CapDo (cấp độ) của mỗi thành viên được tăng lên 1 cho mỗi cấp độ con.
 -Kết quả cuối cùng được hiển thị với tên thành viên được lùi vào theo số cấp độ, giúp dễ dàng nhận diện cấu trúc phân cấp.
 #### tại sao lại dùng CTE 
tôi tìm được ý như sau 
 - Cải thiện khả năng đọc và bảo trì mã SQL: CTE giúp chia nhỏ các truy vấn phức tạp thành các thành phần dễ hiểu hơn.
 -Tái sử dụng mã: CTE có thể được tham chiếu nhiều lần trong truy vấn chính.
 -Tính đệ quy: Hỗ trợ xử lý các cấu trúc dữ liệu phân cấp một cách dễ dàng và hiệu quả.
### Kết quả 
![KQ.png](https://tiloid.com/uploads/images/june2024/KQ.png)

## trigerr bắt lỗi 
 ### lỗi thứ nhất :![trigerr.png](https://tiloid.com/uploads/images/june2024/trigerr.png)
 - bắt lỗi giới tính , ban đầu tạo biến lưu kết quả DECLARE ...
 - dùng cursor để duyệt các bản dữ liệu từ bảng ghi mới hay bản cập nhật INTSERTED
 - mở cur
 - lấy bản ghi 
 - kiểm tra trạng thái nếu là 0 tức là có bản ghi 
 - bắt đầu kiểm tra 
 - giới tính chỉ có thể là nam hoặc nữ IF UPPER(@Gioitinh) check từ Bảng Giaphahopham NOT in (Nam , Nữ ) 1 trong 2
 - nếu lỗi thì in ra RAISERROR ('Giới tính không hợp lệ. Chỉ có thể là  "Nam" hoặc "Nữ".', 16, 1);
            ROLLBACK TRANSACTION; -- Quay lại trạng thái trước đó nếu có lỗi
            RETURN; -- Kết thúc trigger
### kết quả lỗi giới tính 
![kqloigioitinh.png](https://tiloid.com/uploads/images/june2024/kqloigioitinh.png)
  ###Lỗi thứ 2 check tuổi 
![trigerr1.png](https://tiloid.com/uploads/images/june2024/trigerr1.png)
 - kiểm tra xem có bố mẹ không rồi khai báo biến lưu trữ 
 - lấy ngày sinh cha mẹ ở biến lưu rồi so sánh ngày sinh với 
   con của họ 
 - giả sử nếu tuổi con lớn hơn tuổi chame
 - IF @NgaySinh <= @NgaySinhChaMe
            BEGIN
                RAISERROR ('Tuổi của con không thể lớn hơn hoặc bằng tuổi của bố/mẹ', 16, 1);
                ROLLBACK TRANSACTION; -- Quay lại trạng thái trước đó nếu có lỗi
                RETURN; -- Kết thúc trigger
            END
        END
  - nếu ko lỗi thì update lên 
### lỗi tuổi 
 ![loiqktuoi.png](https://tiloid.com/uploads/images/june2024/loiqktuoi.png)
## đến phần sp ( thủ tục lưu trữ ) 
 - tạo thủ tục 
 - tạo ACTION 
 - tạo các biến cho sp
 ### ACTION thứ nhất
 - giả sử như có nhóc con mới đẻ cho vào @ACTION 'INSERT'
 - acction này đơn giản là câu truy vấn select from whe insert into values, nhưng thay vì gọi như thế thì sẽ gọi là ![kqthutucINSERT.png](https://tiloid.com/uploads/images/june2024/kqthutucINSERT.png)
 - tìm thử kq 
 - ![3.png](https://tiloid.com/uploads/images/june2024/3.png)
 ### ACTION thứ 2 SEARCH 
 ![spSEARCHthongtin.png](https://tiloid.com/uploads/images/june2024/spsearchthongtin1.png)
 - ![spSEARCHthongtin1.png](https://tiloid.com/uploads/images/june2024/spSEARCHthongtin1.png)
 -  muốn tìm full thông tin của thành viên thông qua ACCTION này thêm các mô tả thành viên để biết kỹ hơn 
 - chọn thành viên gia đình , thêm mô tả , as vào bảng Mota 
 - từ bảng Giaphahopham ở đây tôi thêm điều kiện hoặc 
 - ví dụ như tìm theo mã , hoặc tên , mã cha mẹ vv 
 - kết quả tìm theo ID
 -  ![4.png](https://tiloid.com/uploads/images/june2024/4.png)
### ACCTION Thứ 3 UPDATE 
 -thay vì UPdATE từ câu lệnh ngoài , mặc dù nó ngắn nhưng không phải cái nào cũng ngắn như thế nên phải cho vào thủ tục 
 -câu lệnh và kết quả 
![5.png](https://tiloid.com/uploads/images/june2024/5.png)
![6.png](https://tiloid.com/uploads/images/june2024/6.png)
###ACTION Thứ 4 Delete 
 - để xóa 1 thành viên trong gia phả 
 - sử dụng DELETE + Gia phả họ phạm 
 - điều kiện xóa theo ID 
 - ví dụ tôi vừa tạo thành viên có ID 75
 - bây giờ xóa đi 
 - ![DElete.png](https://tiloid.com/uploads/images/june2024/DElete.png)
 - ![7.png](https://tiloid.com/uploads/images/june2024/7.png)
   
 ### ACTION Thứ 5 REPORT
   - thống kê thế hệ 
   - ![spREPORT.png](https://tiloid.com/uploads/images/june2024/spREPORT.png)
   - ![8.png](https://tiloid.com/uploads/images/june2024/8.png)
