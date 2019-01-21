1、现已知有八瓶药水，其中有一瓶有毒，服毒后24时毒性发作。现在要用喂小白鼠药水的方法来找出有毒的那瓶。

请问至少需要几只小白鼠才能在24小时内把有毒的那瓶药找出来？

用3位2进制代表8瓶药水

第一瓶000

第二瓶001

第三瓶010

第四瓶011

第五瓶100

第六瓶101

第七瓶 110

第八瓶111

第一只喝下最低位为1对应的药水，第二只喝下中间位为1对应的药水，第三只喝下最高位为1对应的药水

如果是三只老鼠都死了，那么就是第八瓶

如果是三只老鼠都没死，那么就是第一瓶

如果是第一只死了就是第二瓶，第二只死了就是第三瓶，第三只死了就是第五瓶

如果是1,2死了，就是第四瓶，如果是1,3死了就是第六瓶

如果是2,3死了，就是第7瓶

2、100颗药1颗毒药两天发作5只小白鼠如何3天检查出毒药（接上题的条件）？

如果依然考虑二进制的哈，因为这里限制了5只

那么 2^7=128 那么需要7只老鼠

可以考虑一下3进制，3^5=243

我大约是这么个思路：

0表示不吃，1表示第1天吃，2表示第二天吃，那么100粒药用3进制编码，编5位，药的编号如下：

第0粒：00000

第1粒：00001

第2粒：00002

第3粒：00010

第4粒：00011

第5粒：00012

..........

5只老鼠进行编号1,2,3，4,5，比方说有一粒药的编号是02011，则这粒药给第一只老鼠不喂，给第二只老鼠第二天喂，第三只老鼠不喂，给第四第五只老鼠第一天喂。

还是看老鼠怎么死的：比方说第一第二只没死，第三只老鼠第二天死，第四第五只老鼠在第三天死了，那么记为00122。换算过来就是第45粒药有毒。
