# 2022 Space Heroes CTF
###### tags: `CTF`
## Reverse
### Cape Kennedy
- 打開 py 檔可以看到就是要輸入長度為 8 的 input，且其 ord() 加總要是 713，另外有判斷哪幾個字要相等。
- 一開始試了 YYYYYYYZ，ord() 加總 713 沒錯，但提交 flag 不是這個，他有提示這是一個主題 flag，只有一個答案。
- 查了一下 Cape Kennedy，好像有個 nasa 航空總署，看到了關鍵字 Apollo 11， 好像明白了什麼 XDD，符合自串比對的所有條件，ord() 也是 713 ~

    ![](https://i.imgur.com/FReIWrp.png)

### Dreadful Battle
- 這題是個 rpg 遊戲，要輸入正確指令讓 Samus 打敗敵人。
    
    ![](https://i.imgur.com/6xQ5400.png)

- ida 開啟來看一下，循序進去就會找到 TurnAction() 的 function，可以看到 case 就是每回合的動作。
- 可以看到有 one two three 三種 obstacle，分別就是對輸入指令的字串做一些位移或 XOR 的操作，然後跟設定好的混淆後的指令做比對，若相同敵人就會扣血並印出一些東西。

    ![](https://i.imgur.com/cKfmpJ4.png)

- 總之就是拿混淆後指令按照各 obstavle function 逆著做回去，就能知道該輸入指令是甚麼了。
```python=
def revObstacleOne(a1):
    result = ""
    for i in range(len(a1)):
        # print(ord(a1[1]))
        result += chr(ord(a1[i])-i)
    return result
def revObstacleTwo(a1):
    result = ""
    for i in range(len(a1)):
        # print(ord(a1[1]))
        result += chr(ord(a1[i])^i)
    return result
def revObstacleThree(a1):
    result = ""
    for i in range(len(a1)):
        # print(ord(a1[1]))
        result += chr(ord(a1[i])+i+7)
    return result
s = "Fkvk1"
s = revObstacleOne(s)
s = revObstacleTwo(s)
s = revObstacleOne(s)
s = revObstacleOne(s)
print(s)
```
## web
### R2D2
- 恩...蠻直白的提示? (robots.txt)

    ![](https://i.imgur.com/v2rZOFP.png)

### Space Buds
- 這題進去就是一個都次按鈕，輸入框被隱藏起來了，但也不重要。
- 題目說有隻星際狗狗(這是部電影)跑進去網站，看可不可以猜到是誰。

    ![](https://i.imgur.com/gYLlOQ7.png)

- 用 burp 攔截後可以看到有一個 cookie。
    
    ![](https://i.imgur.com/fIFC0LC.png)

- 用電影了狗狗的名字都試了一次，`mudbud` 是正解，進去就看到 flag 了。

    ![](https://i.imgur.com/J7qxtRY.png)
### Flag in Space
- 連進網頁後可以看到很多黑色區塊，都是 div 但裡面都沒東西。
- 
    ![](https://i.imgur.com/UEiYzKU.png)
    
- 從 url 上可以看到有 `flag=` 的參數傳遞，本來想說會不會是 `flag=True` 或 `flag=1` 之類的就出來了，稍微試了一下，看起來應該不是。
- 試一試突然想說反正 flag 形式是 `shctf{}`，不然就丟進去看看好了，結果...冒出了一些東西?
    
    ![](https://i.imgur.com/0b5gcKi.png)

- 看來就是要把 flag "猜"出來了，當然一個一個猜也太勇了點，所以寫了個腳本，來做 brute force。
```python=
import requests
from bs4 import BeautifulSoup
from tqdm import tqdm

flag=""
for i in tqdm(range(25)):
    for j in range(32, 127):
        r = requests.get(f"http://172.105.154.14/?flag={flag}{chr(j)}")
        soup = BeautifulSoup(r.text, "html.parser")
        result = soup.find("div", {"id": "grid"})
        result = result.find_all("div")[i].text.strip()
        if result:
            print(chr(j))
            flag += chr(j)
            break
print(flag)
```
- flag 就出來啦！

  ![](https://i.imgur.com/ZsuG2iO.png)
  
### Space Traveler
- 這題蠻簡單的，要猜 flag。
    
    ![](https://i.imgur.com/tywMNqA.png)
    
- F12 可以看到有個寫 script 的區塊，其實就是在做輸入的判斷，只是一對轉成 16 進位的東東，稍微看一下其實就可以看得懂了。

    ![](https://i.imgur.com/P4Qb2aa.png)

- 把那個陣列轉回 ascii，其實就看到 flag 了。

    ![](https://i.imgur.com/6VpaKTW.png)
