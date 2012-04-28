SITE     = _site/*
CSS      = _site/css/
CSS_IN   = _site/css/style.css _site/css/highlight.css
CSS_OUT  = _site/css/style.min.css
GITHUB   = ../../trzewiczek.github.com/
MAKEFILE = _site/Makefile

post: $(GITHUB) $(SITE)
	jekyll --no-auto
	yui-compressor $(CSS_IN) -o $(CSS_OUT)
	cd $(CSS); ls | grep -v 'min' | xargs rm; cd -
	rm $(MAKEFILE)

	@echo ">>> pushing into websites repo"
	git add .
	git commit -am "New post"
	git push

	@echo ">>> pushing into github pages"
	cp -r $(SITE) $(GITHUB)
	cd $(GITHUB); ls; git add .; git commit -m "New post"; git push;
