  
> [!Cite]  
{{bibliography}}  
  
  
> [!md]  
{% for type, creators in creators | groupby("creatorType") -%}  
{%- for creator in creators -%}  
**{{"First" if loop.first}}{{type | capitalize}}**::  
{%- if creator.name %} {{creator.name}}  
{%- else %} {{creator.lastName}}, {{creator.firstName}}  
{%- endif %}  
{% endfor %}  
{%- endfor %}  
**Title**:: {{title}}  
**Year**:: {{date | format("YYYY")}}  
**Citekey**:: {{citekey}}  
{%- if itemType %}  
**ItemType**:: {{itemType}}  
{%- endif %}  
{%- if itemType == "journalArticle" %}  
**Journal**:: *{{publicationTitle}}*  
{%- endif %}  

  
> [!LINK]  
{%- for attachment in attachments | filterby("path", "endswith", ".pdf") %}  
[{{attachment.title}}](file://{{attachment.path | replace(" ", "%20")}})  
{%- endfor -%}  
  
> [!Abstract]  
{%- if abstractNote %}  
{{abstractNote}}  
{%- endif -%}  
  
# Notes  
{%- if markdownNotes %}  
{{markdownNotes}}  
{%- endif -%}  
  