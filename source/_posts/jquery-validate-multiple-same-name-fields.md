title: "jQuery validation 多个相同name解决方案"
date: 2015-03-31
tags: [Web Front, jQuery]
categories: 技术
---

jQuery validation 是非常优秀的前端验证插件，能够帮我们很方便的对前端的 Form 组件进行验证，在使用的过程中也发现了一些问题，比如，当存在多个 name 相同的表单域时，不能正确的进行验证，针对这个问题，在网上搜索了两段代码来使 jQuery validate 支持多个name相同的表单域。

<!-- more -->

## 代码片段1

``` javascript
if ($.validator) {
    //fix: when several input elements shares the same name, but has different id-ies....
    $.validator.prototype.elements = function () {

        var validator = this,
            rulesCache = {};

        // select all valid inputs inside the form (no submit or reset buttons)
        // workaround $Query([]).add until http://dev.jquery.com/ticket/2114 is solved
        return $([]).add(this.currentForm.elements)
        .filter(":input")
        .not(":submit, :reset, :image, [disabled]")
        .not(this.settings.ignore)
        .filter(function () {
            var elementIdentification = this.id || this.name;

            !elementIdentification && validator.settings.debug && window.console && console.error("%o has no id nor name assigned", this);

            // select only the first element for each name, and only those with rules specified
            if (elementIdentification in rulesCache || !validator.objectLength($(this).rules()))
                return false;

            rulesCache[elementIdentification] = true;
            return true;
        });
    };
}
```

## 代码片段2

``` javascript
if ($.validator) {
	$.validator.prototype.elements = function() {
		var validator = this,
			rulesCache = {};

		// select all valid inputs inside the form (no submit or reset buttons)
		return $(this.currentForm)
		.find("input, select, textarea")
		.not(":submit, :reset, :image, [disabled]")
		.not( this.settings.ignore )
		.filter(function() {
			if ( !this.name && validator.settings.debug && window.console ) {
				console.error( "%o has no name assigned", this);
			}

			// select this element if this has the same name as one in cache (may be was dynamically added)
			if ( this.name in rulesCache ) {
				if ( !validator.objectLength($(this).rules()) ) {
					$(this).rules('add', rulesCache[this.name]);
				}
				return true;
			}

			// select only the element with rules specified
			if ( !validator.objectLength($(this).rules()) ) {
				return false;
			}

			// Add rules to the cache
			rulesCache[this.name] = $(this).rules();
			return true;
		});
	};
}
```
