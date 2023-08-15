# Tổng hợp thuật toán
## Sắp xếp chèn
Dãy a1,a2,...,an
- **Ý tưởng:**
	- Chia thành dãy đích (a1,...,a_i-1) và dãy nguồn (ai,...,an)
	- Ban đầu i=2, từng bước lấy phần tử i của dãy nguồn ra và chen vào vị trí thích hợp của dãy nguồn sao cho dãy đích tăng dần. Sau đó i tăng lên 1 và lặp lại
- **Ví dụ:**
6 1 10 7 4 => 1 6 10 7 4 => 1 6 7 10 4 => 1 6 7 4 10 => 1 6 4 7 10

- **Code:**
```C
void insertionSort(int a[], int array_size) {
int i, j, last;
for (i=1; i < array_size; i++) {
last = a[i];
j = i;
while ((j > 0) && (a[j-1] > last)) {
a[j] = a[j-1];
j = j - 1; }
a[j] = last;
} // end for
} // end of isort
```
Best case: 7(n-1); Worst case: 5(n-1)+2n(n-1)+n-1

## Sắp xếp nổi bọt
- **Ý tưởng:**
	- Đổi chỗ các phần tử liền kề nhau theo thứ tự tăng dần, số nhỏ nhất của dãy được đẩy lên
	- Số nhỏ nhất của dãy 2 được đẩy lên
	- Tiếp tục cho đến khi dãy chỉ còn 1 phần tử

- **Code**
```C
for(i = 0; i<n-1; i++)
	for(j=i+1; j<n; j++)
		if(a[i]>a[j])
		{
		tg=a[i];
		a[i]=a[j];
		a[j]=tg;
		}
```

- **Ví dụ:**
![[Pasted image 20230725002734.png]]

## Sắp xếp chọn
- **Ý tưởng:**
	- Chọn phần tử nhỏ nhất đưa vào vị trí 1
	- Chọn phần tử nhỏ nhất tiếp theo đưa vào vị trí 2
	- ... cho đến khi dãy chỉ còn 1 phần tử

- **Ví dụ:**
6 10 1 7 4 => 1 10 7 4 6 => 1 4 7 10 6 => 1 4 6 10 7 => 1 4 6 7 10

- **Code:**
```C
void bubbleSort(int a[], int n){
int i, j;
for (i = (n-1); i >= 0; i--) {
	for (j = 1; j <= i; j++){
		if (a[j-1] > a[j])
			swap(a[j-1],a[j]);
		}
	}
}
```

## Tìm kiếm tuần tự
- **Pseudo Code:**
sequence_search(a,n,k)
1, Khởi tạo: i:=1
2, Tìm kiếm trong dãy khoá: while a[i] != k do i = i+1
3, Ko thấy: If i = n+1 then return 0
			else return i;

Best: 1; Worst: n

- **Code:**
```C
int sequence_search(int a; int n; int x)
	int i = 0
	while ((i<n) & (a[i]!=x)) i++
	if (i==n)
	return 0
return i+1; 
```

## Tìm kiếm nhị phân
- **Ý tưởng:**
	- Dãy khoá k_L,..., k_R
	- Khoá ở giữa k_i với i = (L+R)\2
	- Search end if x=k_i
	- x<k_i => search continue trên k_L,..., k_i-1
	- x>k_i => search continue trên k_i+1,..., k_R

- **Code:**
```C
int Binary_search (int *a; int n; int x)
{// khởi tạo
int L=0; R=n-1; m;
//search
while (L<=R){
	m = (L+R)\2
	if(x<a[m])
		L=m+1;
	else m+1;
}
//ko thấy
return 0
}
```

## Cây nhị phân tìm kiếm
- **Ý tưởng:**
	- x == khoá của gốc
	- x < khoá của gốc: continue = cách xét cây con trái của gốc
	- x > khoá của gốc: continue = cách xét cây con phải của gốc

- **Giải thuật:**
	- Khởi tạo con trỏ: P:=rỗng, a:=t
	- Tìm kiếm trên cây: while q!=rỗng do
								case: x < infor(q): p:=q;q:=left(q)
										x = infor(q): return q
										x > infor(q): p:=q; q:=right(q)
								end case
	- Bổ sung: q <== AVAIL;
				  infor(q):=x;
				  left(q):=right(q):=rỗng;
				  case 
					  T:=rỗng; T:=q;
					  x<infor(p):left(p):=q
					  else: right(p):=q
					end case
					return q;

==> O(log2(n))
BST: khoá của cây con trái < khoá của gốc; khoá của cây con phải < khoá của gốc

## Quick sort
- **Phát biểu bài toán tìm kiếm:**
Cho một tập gồm n bản ghi F1, F2, In, với i = [1, n], mỗi một bản ghi tương ứng với một khóa k
Yêu cầu: Tìm bản ghi có giá trị khóa tương ứng bằng x cho trước.
Gọi x là khóa tìm kiếm hay giá trị tìm kiếm. Khi đó bài toán có 2 trường hợp:
TH1: Tìm được bản ghi có giá trị khóa tương ứng bằng x. Kết quả của bài toán là "Đã tìm được bản ghi cần tìm”.
TH2: Không tìm được bản ghi có giá trị khóa bằng x. Kết quả của bài toán là "Không tìm được bản ghi cần tìm”.
Ngoài ra, với trường hợp không tìm được khóa có giá trị bằng x, ta còn có thể bổ sung bản ghi mới có khóa x vào tập cho trước → Tìm kiếm có bổ sung.

-**Code:**
```C
void QuickSort(int* &a, int L, int R)
if(LR)
{
return;
}
int i=L, j=R, k=(L+R)/2, x=a[k], tg;
do
{
// Duyet tu ben trai sang va duyet tu ben phai while(a[i]<x)
{
itt;
}
while(a[j]>x)
j--;
if(i<j)
{
tg=a[i];
a[i]=a[j];
a[j]=tg;
}
while(i<j);
QuickSort(a, L, j-1); QuickSort(a, j+1, R);
}
```

