# wp-ip-query
一键查询wordpress访客IP归属地

WordPress后台评论功能会记录每一位访客IP地址，但是如果我们希望获取访客IP的具体归属地，虽然可以通过第三方IP查询工具手动输入IP查询，但是这样略显不便，于是小z博客利用淘宝IP接口对WordPress后台进行了略微的改动，使其可以一键查询访客IP的归属地。

### 引入Jquery库，并加载javascript函数
修改<code>/wp-admin/admin-footer.php</code>将下面的代码添加到<code>body</code>之前。
``` javascript
<script src = "https://lib.sinaapp.com/js/jquery/1.7.2/jquery.min.js"></script>
<script>
	function ipquery(ip,num) {
	var ip = ip;
 
	$.get('./ip-query.php',{ip:ip},function(data,status){
			var address = new Function('return' + data)();
			var guojia = address['data']['country'];
			var sheng = address['data']['region'];
			var shi = address['data']['city'];
			var isp = address['data']['isp'];
			var ip_add = guojia + ' ' + sheng + ' ' + shi + ' ' + isp;
			num = '#' + num;
			$(num).html(ip_add);
		});
	}
</script>
```

### 添加ip-query接口查询文件
将下面的代码另存为:<code>ip-query.php</code>并上传到<code>/wp-admin/</code>目录下。
``` php
<?php
	$ip = $_GET['ip'];			//获取IP
	//通过淘宝IP接口查询IP归属地
	$get_ipquery = "http://ip.taobao.com//service/getIpInfo.php?ip=$ip";
 
	//CURL
	$ch = curl_init();
	curl_setopt($ch,CURLOPT_URL,$get_ipquery);
	curl_setopt($ch,CURLOPT_RETURNTRANSFER,1);
	curl_setopt($ch,CURLOPT_HEADER,0);
 
	$content = curl_exec($ch);
 
	curl_close($ch);
 
 
	echo $content;
?>
```

### 修改wordpress后台功能
找到<code>wp-admin/includes/class-wp-comments-list-table.php</code>这个文件，搜索关键词<code>get_comment_author_IP</code>在这句代码：<code>printf( '<a href="%s">%s</a>', esc_url( $author_ip_url ), $author_ip );</code>之后添加如下代码：
``` php
$num = 'abc'.rand(1000,9999);
				echo " | <a href = 'javascript:;' onclick = 'return ipquery(\"$author_ip\",\"$num\");'>查询</a>";
				echo "<div id ='$num'></div>";
```

### 最后的效果
![](https://nzzv57zo9.qnssl.com/wp-content/uploads/2016/05/ip_query.jpg)
