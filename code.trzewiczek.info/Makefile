SITE=_site
CSS_IN=$(SITE)/css/style.css $(SITE)/css/highlight.css
CSS_OUT=$(SITE)/css/style.css

tech: $(CSS_IN) $(CSS_OUT) 
	jekyll --no-auto
	yui-compressor -o $(CSS_IN) $(CSS_OUT)
	git add .
	git commit -m "Technical change"
	git push

post: $(CSS_IN) $(CSS_OUT) 
	jekyll --no-auto
	yui-compressor -o $(CSS_IN) $(CSS_OUT)
	git add .
	git commit -m "New post"
	git push
