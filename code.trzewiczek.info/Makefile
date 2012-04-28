SITE    = _site/*
CSS_IN  = _site/css/style.css _site/css/highlight.css
CSS_OUT = _site/css/style.css
GITHUB  = ../../trzewiczek.github.com/

post: $(CSS_IN) $(CSS_OUT) $(GITHUB) $(SITE)
	jekyll --no-auto
	yui-compressor -o $(CSS_IN) $(CSS_OUT)
	cp -r $(SITE) $(GITHUB)
	cd $(GITHUB)
	git add .
	git commit -m "New post"
	git push
