{{- /*  Automatically name first item in header    
        based on company name, author name name
        if no company was specified, or just 
        the name of the theme if neither of those
        was specified.
        
*/ -}}
{{- if .Site.Company.Name -}}
{{- $name := .Site.Company.Name -}}
* [{{ $name -}}](/)
{{- else if .FrontMatter.Theme -}}
{{- $name := .FrontMatter.Theme -}}
* [{{ $name -}}](/)
{{- else }}
* [{{.FrontMatter.Theme}}](/)
{{- end }} 
* [Create](/)
* [Pricing](/)
* [Try it Free](/)



