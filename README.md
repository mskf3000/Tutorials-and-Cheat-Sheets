# Cheat-Sheets

A repository for some of my technical cheatsheets. All in Markdown format. 

If you stumble across this repository of my cheat sheets while checking out my code repo and wonder, “Why is this here?” My answer is basically: Why not?

If you find any of this useful, you’re welcome!

***

***NOTE:** This is a nice Awk one-liner to do basic Markdown link formatting of everything in the directory.*

    ls -1 *.md | awk '{ split($0,split_name,"."); printf "* [%s](%s)\n", split_name[1], split_name[1] }'