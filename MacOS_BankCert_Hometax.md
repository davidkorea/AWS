
요새는 공부를 많이 하다 보니까 전자 노트를 필요하다는 생각을 듭니다. 그래서 아이페드 프로를 보고 있는데요. 

구매하려면 어떤 지불수단으로 할지 고민하는 순간 일단 올해 연말정산에 따라 결정하자 라는 소리를 분명히 듣거든요.

자,, 이제 macbook pro 어떤게 진행할지에 대해 간단하게 설명 ㄲ

## 1. 은행 공인인증서 

![](https://tvax3.sinaimg.cn/large/006gDTsUgy1g9nyoplxhlj311u0pq7fj.jpg)

다운 받은 후, 저장위치가 직접 알 수가 없기 때문에, 아래 내용을 참조

- 맥OS에서는 

`/Users/홈 폴더/Library/Preferences/NPKI/yessign/USER/cn=MY_NAME()001101234567890111001326,ou=NACF,ou=personal4IB,o=yessign,c=kr/
 `폴더에 `signCert.der`,	`signPri.key` 있습니다

- 윈도우에서는

`내 PC > C드라이브 > 사용자 > 사용자이름폴더 > AppData > LocalLow > NPKI` 폴더에 있습니다

## 2. Hometax 연말정산 미리보기

### 2.1 [열기] 클릭
![](https://tvax3.sinaimg.cn/large/006gDTsUgy1g9nyx3tt4nj31ia10m7j8.jpg)

### 2.2 [가쟈오기] - [열기]
![](https://tvax4.sinaimg.cn/large/006gDTsUgy1g9nz1hj4mgj31ge0yc7kq.jpg)
![](https://tvax2.sinaimg.cn/large/006gDTsUgy1g9nz4bhmd2j30m20nu78k.jpg)

근데, [열기] 클릭하면 위에 있는 인증서 저장위치가 안 나외요. 그래서 아래 참조해서 인증서를 desktop르로 복사

### 2.3 인증서 복사 
1. go to cert path in `Terminal`
```
$ cd /Users/yong/Library/Preferences/NPKI/yessign/USER/cn=MY_NAME()001101234567890111001326,ou=NACF,ou=personal4IB,o=yessign,c=kr/
```
```
$ ls
signCert.der	signPri.key
```
