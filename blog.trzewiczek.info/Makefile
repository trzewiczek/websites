CSS_PATH=_site/css/style.css
SOURCE=_site/*

deploy: $(CSS_PATH) 
	jekyll --no-auto
	yui-compressor -o $(CSS_PATH) $(CSS_PATH)
	rsync -v -r -e ssh $(SOURCE) beet@trzewiczek.info:~/public_html/blog.trzewiczek.info/
