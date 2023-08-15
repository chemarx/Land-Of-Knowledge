up:: [[Financial Services Database MOC]]
tags:: #on/database 

# Code for Financial Services Database MOC
## Truy vấn dữ liệu 
- Câu lệnh 1: 
- **Source code:**
```sql
SELECT loan_id, account_id, amount, duration,
timestampdiff(month, date, '2000-01-01') AS 'Total past payments',
amount - payments*timestampdiff(month, date, '2000-01-01') AS 'Loan balance', contract_status, date
FROM bankloan.loan
WHERE timestampdiff(month, date, '2000-01-01')<duration
ORDER BY loan_id ASC;
```

- Đại số quan hệ:

- Kết quả:
![[Pasted image 20230524000022.png]]

## Cập nhật dữ liệu 
>[!NOTE]- Vì MySQL đặt ở Safe Mode nên muốn gọi lệnh update hay delete phải chạy đoạn lệnh sau: `SET SQL_SAFE_UPDATES = 0;`

#### Procedure Insert
##### Procedure insert account
- Tạo procedure insert account
```sql
delimiter //
create procedure insertaccount( account_id int, in district_id int, in statement_freq text, in date text)
begin
insert into account(account_id, district_id, statement_freq, date)
values(account_id, district_id, statement_freq, date);
end //
delimiter ;
```

- Gọi procedure updateaccount để thêm account có account_id là 12081:
```sql
call insertaccount('12081', '81', 'Monthly', '1998-08-31');
select * from account
order by account_id desc;
```

- Kết quả sau khi gọi procedure insert account: account có account_id là 12081 đã xuất hiện ngay dòng đầu tiên.

![[Pasted image 20230524135558.png]]

#### Procedure update
##### Procedure update date account 
- Tạo procedure update_date_account: update lại ngày (date) cho account 
```sql
delimiter //
create procedure updateaccount( account_id int, in date text)
begin
update account
set date = date where account_id = account_id;
end //
delimiter ;
```

- Gọi procedure update_date_account để thay date '1993-05-26' của account có account_id '113200' thành '1993-05-05'.
>[!NOTE]- Vì MySQL đặt ở chế độ Safe Update nên muốn gọi lệnh update phải chạy đoạn lệnh sau: `SET SQL_SAFE_UPDATES = 0;`

```sql
CALL updateaccount('113200','1993-05-05');
SELECT * FROM account
```

- Kết quả sau khi gọi procedure update_date_account 

![[Pasted image 20230526202831.png]]

##### Procedure update duration loan
- Tạo procedure update_duration_loan: update lại thời gian đáo hạn (duration) cho loan_id
```sql
delimiter //
create procedure update_duration_loan( loan_id int, in duration int)
begin
update loan
set duration = duration where loan_id = loan_id;
end //
delimiter ;
```

- Gọi procedure update_duration_loan để update thời gian đáo hạn(duration) cho account có loan_id là '7308' từ '24' thành '36'
```sql
call update_duration_loan('7308','36');
select * from loan
```

- Kết quả sau khi gọi procedure update_duration_loan
![[Pasted image 20230526211710.png]]

##### Procedure update balance trans
- Tạo procedure update_balance_trans: update lại số dư tài khoản ngân hàng (balance) cho trans_id
```sql
delimiter //
create procedure update_balance_trans( trans_id int, in balance int)
begin
update trans
set balance = balance where trans_id = trans_id;
end //
delimiter ;
```

- Gọi procedure update_balance_trans để update lại số dư tài khoản ngân hàng (balance) cho trans_id '3673340' từ '29016' thành '29023'
```sql
call update_balance_trans('3673340','29023');
select * from trans
```

- Kết quả sau khi gọi procedure update_balance_trans 
![[Pasted image 20230526213318.png]]

#### Procedure delete
##### Procedure delete account
- Tạo procedure delete_account: xoá account 
```sql
delimiter //
create procedure delete_account(in account_id_deleted int)
begin 
delete from account where account_id = account_id_deleted;
end //
delimiter ; 
```

- Gọi procedure delete_account để xoá account có account_id là '11320'
```sql
call delete_account(11320);
select * from account
```

- Kết quả sau khi gọi procedure delete_account
![[Pasted image 20230526223642.png]]

##### Procedure delete loan
- Tạo procedure delete_loan: xoá các khoản vay (loan)
```sql
delimiter //
create procedure delete_loan(in loan_id_deleted int)
begin 
delete from loan where loan_id = loan_id_deleted;
end //
delimiter ; 
```

- Gọi procedure delete_loan để xoá khoản vay có loan_id là '7308'
```sql
call delete_loan(7308);
select * from loan
```

- Kết quả sau khi gọi delete_loan:
![[Pasted image 20230527203026.png]]

##### Procedure delete trans
- Tạo procedure delete_trans: xoá các giao dịch (trans)
```sql
delimiter //
create procedure delete_trans(in trans_id_deleted int)
begin 
delete from trans where trans_id = trans_id_deleted;
end //
delimiter ; 
```

-  Gọi procedure delete_trans để xoá giao dịch có trans_id là '3673340'
```sql
call delete_trans(3673340);
select * from trans
```

- Kết quả sau khi gọi procedure delete_trans:
![[Pasted image 20230527203904.png]]

#### Transaction
##### Transaction xoá một account
- Khi một khách hàng yêu cầu huỷ tài khoản ngân hàng, thì cần xoá tài khoản và các thông tin liên quan phụ thuộc vào tài khoản đó
- Mã nguồn
```sql
start transaction;

delete from disp
where account_id = '11359';
delete from loan
where account_id = '11359';
delete from bankloan.order
where account_id = '11359';
delete from trans
where account_id = '11359';
delete from account
where account_id = '11359';
-- do bảng card ko có account_id nên ta lấy disp_id tương ứng vs account_id đó từ bảng disp
delete from card
where disp_id = '13660';
-- do cx ko có account_id nên ta lấy cilent_id tương ứng từ bảng disp
delete from client
where client_id = '13968';

commit;
```

- Kết quả sau khi thực hiện transaction xoá tài khoản (lấy ví dụ ở bảng account)
    - Hình ảnh trước khi tài khoản bị xoá:
    ![[Pasted image 20230528233801.png]]
    - Kết quả sau khi xoá: 
    ![[Pasted image 20230528234036.png]]
    

##### Transaction đăng ký thêm 1 account 
- Khi có 1 khách hàng mới đăng ký tài khoản ngân hàng, thì cần thêm tài khoản và các thông tin liên quan vào cơ sở dữ liệu
- Mã nguồn
```sql
start transaction;

insert into account(account_id, district_id, statement_freq, date)
values (14050, 77, 'Monthly', '2001-09-11');
insert into client(client_id, district_id, gender, DateOfBirth)
values (14030, 77, 'Male', '2001-09-11');
insert into disp(disp_id, client_id, account_id, type)
values (14010, 14030, 14050, 'OWNER');
insert into card(disp_id)
values (14010); 
-- do chưa đky làm thẻ cứng nên các cột còn lại để trống
insert into bankloan.order(account_id)
values (14050);
-- do chưa có đơn nào nên cũng thế
insert into trans(account_id)
values(14050);
-- do chưa có giao dịch nào nên cx thế
insert into loan(account_id)
values(14050);
-- do chưa có khoản nợ nào nên cx vậy

commit;
```

- Kết quả sau khi đăng ký tài khoản (lấy ví dụ ở bảng account)
![[Pasted image 20230528232041.png]]


## Fix
*Không tồn tại limit trong đại số quan hệ*

About the `limit` in relational algebra. Traditional relational algebra does not support anything like the `limit` in SQL. This problem has been recognized and studied by Li, Chang, Ilyas and Song in _RankSQL: query algebra and optimization for relational top-k queries_ (SIGMOD 2005). They have proposed a monotonic scoring function _F_ that ranks the results by the sorting operator _tauF_.
[sql - relational algebra for Limit Operator - Stack Overflow](https://stackoverflow.com/questions/10229535/relational-algebra-for-limit-operator)

### Câu 5
```sql
SELECT d.Dname, COUNT(c.Dnum) AS total_clients, AVG(c.Dnum) AS avg_clients_per_district
FROM DEPARTMENT AS d
LEFT JOIN PROJECT AS c ON d.Dnumber = c.Pnumber
GROUP BY d.Dname
HAVING total_clients > 10
ORDER BY avg_clients_per_district DESC;
```

--> Relational Algebra
```relational Algebra
τ avg_clients_per_district desc π d.Dname, total_clients, avg_clients_per_district σ total_clients > 10 γ d.Dname; COUNT(c.Dnum)→total_clients, AVG(c.Dnum)→avg_clients_per_district ( ρ d DEPARTMENT ⟕ d.Dnumber = c.Pnumber ρ c PROJECT )
```

```sql
SELECT d.dis_name, COUNT(c.client_id) AS total_clients,

       AVG(COUNT(c.client_id)) OVER () AS avg_clients_per_district

FROM district AS d

LEFT JOIN client AS c ON d.dis_code = c.district_id

GROUP BY d.dis_name

HAVING total_clients > 10

ORDER BY avg_clients_per_district DESC;
```

### Câu 8
[This calculator](https://dbis-uibk.github.io/relax/help.htm) has an example (see Value Expressions at the very bottom) -- it's essentially the same as in MySQL, but as the author notes there isn't really a standard for relational algebra like there is for ANSI SQL, so there isn't necessarily a correct answer.

I'd have to look in the original Database Systems to see if they offer any mathematical notation for it, but since it seems you just use the SQL variant my guess is that this was bolted on later.
=> *có khả năng case when ko có ĐSQH tương ứng với nó*
=> có thể bỏ đại số quan hệ câu lệnh này

### Câu 7
*Limit* cũng ko có ĐSQH tương ứng với nó nên có thể bỏ cái limit này trong ĐSQH
Có thể viết thành: KQ <-- trả về n kết quả đầu tiên

Kiểm tra tình trạng nợ của các tài khoản ngân hàng cho đến ngày 1/1/2000
Kiểm tra số dư của tài khoản sau giao dịch cuối cùng
Danh sách 10 quận có số lượng tài khoản lớn hơn 10, được sắp xếp theo tuổi trung bình của khách hàng từ cao đến thấp
Số lượng khách hàng nam/ nữ theo từng khu vực
Các khu vực có tổng số khách hàng lớn hơn 10 và số lượng khách hàng trung bình trên mỗi khu vực
Thống kê số lượng đơn đặt hàng của các ngân hàng với số lượng nhỏ nhất lớn hơn 3 (Đã sử dụng hàm Sum, count, max, min và group by, having
Kiểm tra các thông tin 5 khoản vay trên 15 năm với trạng thái hợp đồng là đã đóng, được sắp xếp tăng dần theo mã khoản vay (đã sử dụng left join, sort và limit)
Kiểm tra thời hạn vay (sử dụng case)
Các tài khoản kèm theo tên khu vực và tổng số tiền giao dịch trên mỗi tài khoản
Các khách hàng có tổng số tiền giao dịch vượt quá giá trị trung bình của tổng số tiền giao dịch của tất cả các khách hàng.

