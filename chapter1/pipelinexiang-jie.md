### Pipeline出现的背景

redis客户端执行一条命令分4个过程

      发送命令-》命令排队-》命令执行-》返回结果

这个过程称为Round trip time（RTT，往返时间），mget mset 有效节约了RTT，但大部分命令（如hgetall，并没有mhgetall），不支持批量操作，需要消耗N次RTT，这个时候就需要pipeline来解决这个问题



