# **Hack The Boo CTF 2022**
## **Juggling Facts** 
___
### **Description:**

An organization seems to possess knowledge of the true nature of pumpkins. Can you find out what they honestly know and uncover this centuries-long secret once and for all?

___
### **TL;DR**

Bài này khá dễ nhưng mình nghĩ lung tung quá nên không làm ra trong thời gian của giải. Bài viết vì mục đích học tập và rút kinh nghiệm xD
___

Giao diện đầu của web, ta có thể click button để view các facts:

![1.png](https://github.com/L4P1Nz/Hack-The-Boo/blob/main/Juggling%20Facts/img/1.png)

Flag được khởi tạo trong database:

![2.png](https://github.com/L4P1Nz/Hack-The-Boo/blob/main/Juggling%20Facts/img/2.png)

Ta có thể lấy flag bằng cách truy cập ``/api/getfacts`` với ``type=secrets``:

![3.png](https://github.com/L4P1Nz/Hack-The-Boo/blob/main/Juggling%20Facts/img/3.png)

Nhưng nó yêu cầu request phải đến từ localhost:

![4.png](https://github.com/L4P1Nz/Hack-The-Boo/blob/main/Juggling%20Facts/img/4.png)

Đoạn này làm mình nghĩ nhiều đến SSRF, nhưng vuln của bài này lại nằm ở logic code phía sau :

![5.png](https://github.com/L4P1Nz/Hack-The-Boo/blob/main/Juggling%20Facts/img/5.png)

Trong PHP có trick kinh điển là [Type Juggling](https://owasp.org/www-pdf-archive/PHPMagicTricks-TypeJuggling.pdf). Vì vậy ta không nên [compare](https://www.php.net/manual/en/types.comparisons.php) với ``==`` . Vậy Loose Comparison nằm ở đâu trong bài này ? Câu trả lời là [SWITCH](https://www.php.net/manual/en/control-structures.switch.php) ! Nghĩa là cái switch case của chúng ta sẽ trông như thế này :

```
if ($jsondata['type'] == 'secrets') { 
    ...
}
elseif ($jsondata['type'] == 'spooky') { 
    ... 
}...
```

![6.png](https://github.com/L4P1Nz/Hack-The-Boo/blob/main/Juggling%20Facts/img/6.png)

```
public function getfacts($router)
    {
        $jsondata = json_decode(file_get_contents('php://input'), true);

        if ( empty($jsondata) || !array_key_exists('type', $jsondata))                          #[1]
        {
            return $router->jsonify(['message' => 'Insufficient parameters!']);
        }

        if ($jsondata['type'] === 'secrets' && $_SERVER['REMOTE_ADDR'] !== '127.0.0.1')         #[2]
        {
            return $router->jsonify(['message' => 'Currently this type can be only accessed through localhost!'.$_SERVER['REMOTE_ADDR']]);
        }

        switch ($jsondata['type'])
        {
            case 'secrets':                                                                     #[3]
                return $router->jsonify([
                    'facts' => $this->facts->get_facts('secrets')
                ]);

            case 'spooky':
                return $router->jsonify([
                    'facts' => $this->facts->get_facts('spooky')
                ]);
            
            case 'not_spooky':
                return $router->jsonify([
                    'facts' => $this->facts->get_facts('not_spooky')
                ]);
            
            default:
                return $router->jsonify([
                    'message' => 'Invalid type!'
                ]);
        }
    }
```

Như vậy, nếu sử dụng Loose Comparison đúng cách, ta có thể bypass check empty của condition [1], ignore condition [2], và nhảy vào condition [3] để lấy secrets chứa flag.

Ta có thể sử dụng true(boolean) để loose compare với string "secrets":

![7.png](https://github.com/L4P1Nz/Hack-The-Boo/blob/main/Juggling%20Facts/img/7.png)

Get flag thôi !

![8.png](https://github.com/L4P1Nz/Hack-The-Boo/blob/main/Juggling%20Facts/img/8.png)