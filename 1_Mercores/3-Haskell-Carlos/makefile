slides.html:	slides.md reveal.js
	pandoc $< -o $@ -t revealjs -s --css slides.css --highlight-style=espresso

reveal.js:
	git clone https://github.com/hakimel/reveal.js.git

clean:	slides.html
	rm slides.html
