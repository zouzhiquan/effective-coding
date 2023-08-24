# effective-coding

html to md
ls | grep ".html" | xargs -I {} pandoc -s {}  -t markdown -o {}.md && sed -i "" "s/{.*\"}/ /g" *.md && sed -i "" "s/width=\".*\"}/ /g" *.md && sed -i "" "s/{height=\".*\"/ /g" *.md && sed -i "" "s/^%.*/ /g" *.md && rm *.html
