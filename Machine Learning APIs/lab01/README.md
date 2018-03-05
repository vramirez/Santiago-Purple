# Lab 01 
## Extract, Analyze, and Translate Text from Images with the Cloud ML API's


```bash
STR=$(jq .responses[0].textAnnotations[0].description ocr-response.json) && STR="${STR//\"}" 
&& sed -i "s|your_text_here|$STR|g" translation-request.json
```

```
git status
git add
git commit
```
