| Resource Type | Permissions |
| ------------- | ----------- |
{{ $product_code := "cc" -}}
{{- with .Site.Data.product_resource_types_permissions -}}{{- range . -}}{{- if eq .ProductCode $product_code -}}{{ range $rtp := .ResourceTypesPermissions }}
{{- println "| " $rtp.ResourceType " | " (delimit $rtp.Permissions ",<br/>") " |" -}}
{{ end }}{{- end -}}{{- end -}}{{- end -}}

