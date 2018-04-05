# Arduino-board

# 參考資料


[Arduino Ethernet Shield W5100 乙太網路擴充板，使用 DHCP 取得 IP 位址](
https://blog.gtwang.org/iot/arduino-ethernet-shield-w5100-dhcp-ip-address/)



這裡介紹如何使用 Arduino Ethernet Shield W5100 乙太網路擴充板，透過 DHCP 自動取得 IP 位址。

這張是副廠的 Arduino Ethernet Shield W5100 乙太網路擴充板，只要兩百多塊。
![baidu](https://blog.gtwang.org/wp-content/uploads/2015/03/arduino-ethernet-shield-w5100-2-816x459.jpg "arduino-ethernet-shield-w5100")

擴充板在使用時就直接插在 Arduino 即可，這裡我是拿一張 UNO 的相容板來示範。

這張擴充板在插上 UNO 上面時，RJ45 的插座下方的針腳很容易頂到 UNO 的 USB 插座，如果怕短路的話，在上面貼個膠帶會比較好。

接上 USB 線與網路線，就可以來開發程式了。

要使用這張乙太網路擴充板需要一些函式庫，而 Arduino 的開發環境中有內建基本函式庫可以使用，以下是從 DHCP 取得 IP 位址，讓 Arduino 連上網路的範例。

```cpp
#include <SPI.h>
#include <Ethernet.h>

// 指定網路卡 MAC 卡號
byte mac[] = {  0x00, 0xAA, 0xBB, 0xCC, 0xDE, 0x02 };

// 初始化 Ethernet client library
EthernetClient client;

void setup() {
  // 初始化序列埠
  Serial.begin(115200);
  // 啟用 Ethernet 連線，預設會以 DHCP 取得 IP 位址
  if (Ethernet.begin(mac) == 0) {
    Serial.println("無法取得 IP 位址");
    // 無法取得 IP 位址，不做任何事情
    for(;;)
      ;
  }
  // 輸出 IP 位址
  Serial.print("IP 位址：");
  Serial.println(Ethernet.localIP());
}

void loop() {

}
```

這裡的 Ethernet.begin(mac) 是設定網路卡 MAC 卡號，並且以 DHCP 取得 IP 位址，如果要自行指定 IP 位址，可以這樣寫：
```cpp
// 網路卡 MAC 卡號
byte mac[] = { 0xDE, 0xAD, 0xBE, 0xEF, 0xFE, 0xED };

// 指定 IP 位址
byte ip[] = { 192, 168, 1, 200 };

void setup() {
  Ethernet.begin(mac, ip);
}
```
若要設定 DNS、預設閘道與子網域等，就將這些參數再加上去即可，詳細用法請參考 [Ethernet.begin()](https://www.arduino.cc/en/Reference/EthernetBegin)
的說明。

將寫好的程式編譯並上傳至 Arduino 之後，開啟序列埠監控視窗，就可以看到 Arduino 從 DHCP 伺服器所取得的動態 IP 位址了。

arduino-ethernet-shield-w5100-8
Arduino 輸出

這時候可以使用 ping 測試一下，看看 Arduino 所取得的 IP 位址是否可以正常使用。

ubuntu-terminal-ping
ping 輸出

如果您對於 Arduino 的相關應用有興趣，建議您可以繼續閱讀物聯網的文章。

#補充說明
----
[甚麼叫做電腦網路卡號碼(MAC address)?](http://sysadm.ntpu.edu.tw/mac.html)

```
很多人應該都聽過「MAC」（不是蘋果電腦 Mac）這個名詞，卻不太了解他的意義。其實 MAC 位置
（Media Access Control Address）是一組由國際組織認證後，由合法授權的網通產品製造公司
所生產的網路卡才能配發的位置代碼，一般為六組255進位（00~FF）的代碼組成，其中前三組碼代表
的是生產網卡的公司，後三組碼為網卡實體編號，世界上的任何一片網路卡都有一個獨一無二的MAC
位置碼，在生產時直接就燒入EEPROM中，是一組不可修改的唯一代碼（除非是非法的黑牌廠商或是
經過各種手段特意修改過），也代表了你在網路的真實位置。在網路世界ip位置可能因為每次撥接
而改變，或者你轉移到其他地方也會改變，唯一不變的就是MAC位置。
```
[如何查看與更改網路卡 Mac 卡號（Mac Address）？](https://blog.gtwang.org/useful-tools/change-mac-address/)
```
然後在乙太網路卡的部分會有一個「實體位址」，上面的十六進位數字就是 Mac 卡號。
```




