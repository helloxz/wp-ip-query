# wp-ip-query
一键查询wordpress访客IP归属地

WordPress后台评论功能会记录每一位访客IP地址，但是如果我们希望获取访客IP的具体归属地，虽然可以通过第三方IP查询工具手动输入IP查询，但是这样略显不便，于是小z博客利用淘宝IP接口对WordPress后台进行了略微的改动，使其可以一键查询访客IP的归属地。

### 引入Jquery库，并加载javascript函数
修改<code>/wp-admin/admin-footer.php</code>将下面的代码添加到<code></body></code>之前。
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


