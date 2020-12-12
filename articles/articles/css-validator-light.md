===
theme="pillar"
mode="light"
===

#### Theme: {{ .FrontMatter.Theme }}. See this page in [dark mode](css-validator.html) or [go back](../index-light.html)
##

# A Command-line CSS validator for "modern" CSS

I am working on a project with hundreds of CSS files to maintain.
Using the web-based W3C [CSS Validation Service](https://jigsaw.w3.org/css-validator/) stopped being an option when I started to use CSS custom properties, which look like this:

```
:root
{
  /* Default foreground color */
  --fg:white;
}
```

They're applied using `var` like this:

```
article {color:var(--fg);}
```


While custom properties have been in CSS for well-nigh a decade, the interactive W3C validator online doesn't support them. Further, running checks at the command line is faster and more convenient for me. 

## Nu HTML checker to the rescue, almost

To make a long story short, there appear to be no easily available CSS checkers that operate from the command line and also support custom properties. But the [Nu HTML checker](https://validator.github.io/validator/) does work from the command line, and it supports custom properties even though its web-based counterpart doesn't.

### Downloading a compiled version

You don't have to compile from source. You can get a compiled version of the Nu HTML checker from the [validator releases](https://github.com/validator/validator/releases) page.

As a Mac user specifically, I used the version at [https://github.com/validator/validator/releases/tag/20.6.30](https://github.com/validator/validator/releases/tag/20.6.30) but of course versions change. Your mileage may vary.

Nu HTML checker did the trick pretty well except for line numbers. It expects an HTML file, which means the CSS errors are displaced. Plus I just wanted to check pure CSS files, not an HTML file. The solution was obviously something along the lines of "create a temporary HTML file, insert the CSS, and do the validation."

## Creating a one-line HTML file 

In order to get the error messages to yield correct line numbers, the answer was to create the temp HTML in a single line, insert the CSS, and run the validation. I created a script that works for Bash or zsh:

##### file: v.sh
```
OUTFILE="VNU_CHECK.HTML"
# Read the file named on the command line, which is $1, 
# into the variable $INFILE.
INFILE=$(<$1)
# Create the variable $CONTENTS from this HTML, plus
# insert the contents of $1 inside a style tag. 
# Keep it all on the same line 
# so error messages are accurate
read -r -d '' CONTENTS << EOM
<!DOCTYPE html><html lang=""><head><title>$1</title><style>$INFILE</style></head><body></body></html>
EOM
## Copy all of this to the output file
echo "$CONTENTS" > $OUTFILE
echo $1:
vnu $OUTFILE
```

* You save that as `v.sh` or whatever and make it executable:

```
$ chmod +x v.sh
```

* Then put it on the path if you wish; on my system I placed it in `/usr/local/bin`:

```
$ mv v.sh /usr/local/bin
```

So if your file is named `layout.css` you'd invoke:

```
$ v.sh layout.css
```

The script generates a file with the name hardcoded to `VNU_CHECK.HTML` because that name is unlikely to collide with any other filename, so you can put it in `.gitignore` or otherwise delete it at will. The inline HTML contains the CSS file so error messages give you the right line number.

The Nu HMTL Checker error messages are that rarest kind, because they are detailed and relevant. I've run it hundreds of times and so far it hasn't been wrong or misleading. Mad props to whoever wrote it.


```
ERROR:html5validator.validator:[u'"file:/Users/tom/code/tom/VNU_CHECK.HTML":26.32-26.32: error: CSS: "margin-block-start": Property "margin-block-start" doesn\'t exist.'
```


When you're finished delete `VNU_CHECK.HTML`.

{{ inc "common|mdemo.md" }}


