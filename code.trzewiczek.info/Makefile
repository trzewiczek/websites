SITE     = _site/*
CSS_IN   = _site/css/style.css _site/css/highlight.css
CSS_OUT  = _site/css/style.css
GITHUB   = ../../trzewiczek.github.com/
MAKEFILE = _site/Makefile

post: $(GITHUB) $(SITE)
	jekyll --no-auto
	yui-compressor $(CSS_IN) -o $(CSS_OUT)
	rm $(MAKEFILE)
	@echo ">>> pushing into websites repo"
	git add .
	git commit -am "New post"
	git push
	@echo ">>> pushing into github pages"
	cp -r $(SITE) $(GITHUB)
	cd $(GITHUB); ls; git add .; git commit -m "New post"; git push;
