# Lab 01 - Extract, Analyze, and Translate Text from Images with the Cloud ML API's


## Create API Key

Go to APIs & services > Credentials: Create Credentials > API Key 

```bash
export API_KEY=<YOUR_API_KEY>
```

## Upload an image to a cloud storage bucket
### Creating a Cloud Storage bucket

Storage > Create bucket > unique_name

### Upload an image to your bucket

Upload to the bucket the image ```resources/sign.png```

```bash
STR=$(jq .responses[0].textAnnotations[0].description ocr-response.json) && STR="${STR//\"}" && sed -i "s|your_text_here|$STR|g" translation-request.json
```

Next, click the checkbox under **"Share publicly"** This will make the image accessible to the Vision API from Cloud Shell

## Create your Vision API request

Create the file ```ocr-request.json``` with the content 

```json
{
  "requests": [
      {
        "image": {
          "source": {
              "gcsImageUri": "gs://my-bucket-name/sign.png"
          } 
        },
        "features": [
          {
            "type": "TEXT_DETECTION",
            "maxResults": 10
          }
        ]
      }
  ]
}
```


## Call the Vision API's text detection method

```bash
curl -s -X POST -H "Content-Type: application/json" --data-binary @ocr-request.json  https://vision.googleapis.com/v1/images:annotate?key=${API_KEY}
```

Same but saving output in ```ocr-responde.json```

```bash
curl -s -X POST -H "Content-Type: application/json" --data-binary @ocr-request.json  https://vision.googleapis.com/v1/images:annotate?key=${API_KEY} -o ocr-response.json
```

## Sending text from the image to the Translation API

First, create a translation-request.json file and add the following to it:

```json
{
  "q": "your_text_here",
  "target": "en"
}
```

```q``` is where you'll pass the string to translate

```bash
STR=$(jq .responses[0].textAnnotations[0].description ocr-response.json) && STR="${STR//\"}" && sed -i "s|your_text_here|$STR|g" translation-request.json
```

```bash
curl -s -X POST -H "Content-Type: application/json" --data-binary @translation-request.json https://translation.googleapis.com/language/translate/v2?key=${API_KEY} -o translation-response.json
```

## Analyzing our image's text with the Natural Language API

To set up the API request, create a nl-request.json file with the following:

```json
{
  "document":{
    "type":"PLAIN_TEXT",
    "content":"your_text_here"
  },
  "encodingType":"UTF8"
}
```

Replace text

```bash
STR=$(jq .data.translations[0].translatedText  translation-response.json) && STR="${STR//\"}" && sed -i "s|your_text_here|$STR|g" nl-request.json
```

Call the analyzeEntities endpoint of the Natural Language API with this ```curl``` request:

```bash
curl "https://language.googleapis.com/v1/documents:analyzeEntities?key=${API_KEY}" \
  -s -X POST -H "Content-Type: application/json" --data-binary @nl-request.json

```

##Conclusion

You've combined 3 different machine learning APIs: 

1. Vision API's OCR method extracted text from an image
2. Translation API translated that text to English 
3. Natural Language API to found entities in that text.

###WHAT WE'VE COVERED
* Use cases for combining multiple machine learning APIs
* Creating a Vision API OCR request and calling the API with curl
* Translating text with the Translation API
* Extract entities from text with the Natural Language API
