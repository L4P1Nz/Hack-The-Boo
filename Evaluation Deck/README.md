# **Hack The Boo CTF 2022**
## **Evaluation Deck** 
___

Khi vào trang thì ta có giao diện một trò chơi rút thẻ gì đó (????)

![1.png](https://github.com/L4P1Nz/Hack-The-Boo/blob/main/Evaluation%20Deck/Img/1.png)

Sau khi đọc source thì mình phát hiện có thể command injection ở đây

![2.png](https://github.com/L4P1Nz/Hack-The-Boo/blob/main/Evaluation%20Deck/Img/2.png)

Mình test thử :
```
"current_health":"1",
"operator":"+2;3+",
"attack_power":"4"
```
thì nhận được response {"message":3}.

Như vậy: mình có thể command injection nhưng chỉ có thể thấy được out của phép tính đầu, `current_health` và `attack_power` bị ép kiểu sang int nên ta chỉ có thể tận dụng `operator`

Ta có thể chạy reverse shell với sh shell như bài [Spookifier](https://github.com/L4P1Nz/Hack-The-Boo/tree/main/Spookifier)

Vì host này có wget nên mình sẽ dùng requestbin xD

![3.png](https://github.com/L4P1Nz/Hack-The-Boo/blob/main/Evaluation%20Deck/Img/3.png)

![4.png](https://github.com/L4P1Nz/Hack-The-Boo/blob/main/Evaluation%20Deck/Img/4.png)