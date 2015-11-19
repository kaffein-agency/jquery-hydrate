# jQuery simple input auto datasync on change
Very simple jquery plugin for handling automatic input data saving on change. A "value" data key will be sent along with the other html data attributes.

Exemple usage : 
##HTML
```
<input type="text" value="42" data-id_employee="11" data_id_price="3">
```
##JS
```
$(input).AutoDataSync({
    // The data-attributes array will be mapped as key/value data for the request
    dataAttributes : ["id_employee","id_price"],
    post : "/myProcessingCode.php",
    success : function (response) {
        console.log("yay data saved !");
        $(this).css({border:"1px solid green"});
    }
    // Any other $.ajax() options can be used
});
```

##The plugin code :
```
$.fn.AutoDataSync = function (options) {
	$(this).each(function () {
		var $input = $(this);
		var o = { data: { value: $input.val() } };
	
		$.each(options,function (p,v) {
			if(p.match(/post|get/i))
				o.method = p, o.url = v;
			else if(p.match(/dataattributes/i))
				$.map(v,function (d) { o.data[d]=$input.data(d) });
			else if(p.match(/success|error/i) && typeof v == "function" )
				o[p]=$.proxy(v,$input);
			else 
				o[p] = v;
		})

		$input.bind({
			keyup :function(e) { e.keyCode === 13 && $input.blur(); },
			blur : function () { $.ajax(o); }
		})
	})
}
```
